# Devemos saber diferenciar aplicações IO-bound e CPU-bound para trabalhar de maneira eficiente com Python

**Em que casos a utilização das bibliotecas para desenvolvimento de aplicações concorrentes é apropriada e pode resultar em um aumento de performance, utilizando Python?**

Para responder essa pergunta, primeiro precisamos discutir sobre como o interpretador do Python funciona. No decorrer do post, ao detalhar o funcionamento do interpretador, estaremos nos referindo ao CPython, que traz a implementação de referência da linguagem Python e também é o interpretador padrão e mais utilizado entre desenvolvedores.

>## Interpretadores Python
>CPython é o interpretador de referência, criado por Guido van Rossum, criador da linguagem Python. Com a popularização da linguagem, outros interpretadores foram criados pela comunidade, como o IronPython (.NET), Jython (JVM), PyPy, Stackless, etc.

O interpretador, ao executar o código, utiliza um mecanismo de segurança chamado GIL, ou Global Interpreter Lock. Essa estrutura de lock foi implementada originalmente para eliminar problemas relacionados ao gerenciamento de memória em cenários de aplicações concorrentes. Para realizar as execuções das instruções pelo interpretador, é necessária a obtenção do controle do GIL por parte da *thread*. **Isso significa que em qualquer momento durante a execução de uma aplicação Python apenas uma thread estará em execução por processo.**

>## O que é lock?
>Lock é uma técnica de sincronização que é utilizada por meio de uma abstração. Ele que no máximo uma *thread* possua seu controle por vez. Obter um lock é a maneira que uma *thread* tem para dizer as outras “eu estou controlando esse estado, não mexa nele agora”. - [MIT - Software Construction](https://web.mit.edu/6.005/www/fa15/classes/23-locks/)


O GIL é reconhecido por grande parte da comunidade Python como o principal fator responsável pela popularização da linguagem. Devido o GIL, problemas clássicos da área da programação concorrente são evitados e a interoperabilidade com bibliotecas em C é realizada de maneira simples, fator que foi determinante para a adoção do Python pelos desenvolvedores de outras linguagens. **[Entender o funcionamento do Global Interpreter Lock é essencial para desenvolvermos aplicações eficientes em Python](https://twitter.com/intent/tweet?text=Entender%20o%20funcionamento%20do%20Global%20Interpreter%20Lock%20%C3%A9%20essencial%20para%20desenvolvermos%20aplica%C3%A7%C3%B5es%20eficientes%20em%20Python&url=https://www.eximiaco.tech/?p=2642&via=eximiaco)**.

**Uma thread, ao obter o controle do Global Interpreter Lock, continuará com o controle dele enquanto estiver realizando processamento das instruções.** Ao terminar, a thread irá liberar o controle obtido. Durante a execução da aplicação outras threads realizarão o mesmo procedimento de obtenção e liberação do GIL.

Uma exceção ocorre em aplicações que realizam chamadas a um processos externos, como a processos de IO. Neste caso a thread em execução libera o controle do GIL ao realizar a chamada de sistema para realizar IO, e obtém ele novamente apenas após a conclusão da operação de IO.

**Esse tipo de aplicação é denominada de *IO-bound*, por ter sua performance relacionada diretamente ao desempenho de operações de IO.** Aplicações *IO-bound* não sofrem nenhum tipo de penalização de performance devido ao uso do GIL pelo interpretador.

Como exemplo de *IO-bound*, vamos utilizar uma aplicação que obtém as informações de alguns álbuns selecionados utilizando a API do [Cover Art Archive](https://musicbrainz.org/doc/Cover_Art_Archive/API). O seguinte trecho de código define as funções que iremos utilizar para realizar o benchmark entre as formas de execução sequencial e paralela, por meio do uso de threads.

```
import requests 

def get_allan_holdsworth_cd_list(): 
    return [ 
        '1d9e7f7f-ab75-3f96-a1a3-9977f75c3570', 
        '40901f7e-f7dc-33b5-9380-7b3ea9e3094e', 
        '9caacf47-99e5-3193-ba29-5302e225cac4', 
        '146495af-a0bd-3774-b677-c57f8e2369d2', 
        '57b6813d-d0b7-3c70-8d0a-4001cee7b5c2' 
    ] 

def make_http_album_archive_request(album_id): 
    url = f'https://coverartarchive.org/release-group/{album_id}' 
    r = requests.get(url)
```

A execução sequencial é realizada pelo seguinte código:

```
import time 
from io_bound import get_allan_holdsworth_cd_list,make_http_album_archive_request

if __name__ == '__main__': 
    start = time.time() 
    cds = get_allan_holdsworth_cd_list() 

    for cd in cds: 
        make_http_album_archive_request(cd) 
    
    duration = time.time() - start 
    print(f'Duração total de {duration} segundos.')
```

A execução paralela, utilizando cinco threads, é feita da seguinte maneira:

```
import time 
from concurrent import futures 
from io_bound import get_allan_holdsworth_cd_list, make_http_album_archive_request 

if __name__ == '__main__': 
    start = time.time() 
    cd_list = (id for id in get_allan_holdsworth_cd_list()) 
    threads = 5 

    with futures.ThreadPoolExecutor(threads) as executor: 
        future = executor.map(make_http_album_archive_request, cd_list) 
    
    duration = time.time() - start 
    print(f'Duração total de {duration} segundos.')
```

Ao comparar a duração total de cada execução, **foi possível notar uma melhoria de performance de até 5 vezes utilizando a abordagem com threads.** Por ser um processo *IO-bound*, o uso de threads proporciona ganhos significativos de performance.

**Por outro lado temos as aplicações *CPU-bound*. Essas aplicações possuem sua performance relacionada diretamente com a velocidade que o processamento de instruções é realizado.** Além de possuírem sua performance severamente penalizada pela velocidade de execução das instruções pelo interpretador Python, não podem ser otimizadas por meio de uma estratégia de paralelização via *threads*, devido o GIL.

Como exemplo de aplicação *CPU-bound*, utilizaremos uma função que realiza a soma de números de 0 até n, dez vezes. Esse procedimento é definido pelo seguinte trecho de código:

```
def __sum_from_zero_to_num(num): 
    return sum(i for i in range(num))

def cpu_bound_processing(start, end): 
    large_number = 10_000_000 
    for i in range(start, end): 
        increment = i ** 2 
        __sum_from_zero_to_num(large_number + increment)
```

A execução sequencial é realizada da seguinte maneira:

```
import time 
from cpu_bound import cpu_bound_processing 

if __name__ == '__main__': 
    start = time.time() 
    cpu_bound_processing(0, 10) 
    duration = time.time() - start 
    print(f'Duração total de {duration} segundos.')
```

A execução paralela, também utilizando cinco *threads*, é realizada da seguinte forma:

```
import time 
from concurrent import futures
from cpu_bound import cpu_bound_processing 

if __name__ == '__main__': 
    start = time.time() 
    threads = 5 
    parameters = [(0, 2), (2, 4), (4, 6), (6, 8), (8, 10)]

    with futures.ThreadPoolExecutor(threads) as executor: 
        future = executor.map(cpu_bound_processing, *zip(*parameters)) 
    
    duration = time.time() - start
    print(f'Duração total de {duration} segundos.')
```

Ao realizar a comparação final, **não foi possível notar nenhuma melhoria entre as implementações.** Por se tratar de uma aplicação *CPU-bound*, o uso de threads não resulta em melhoria de performance, devido o *Global Interpreter Lock*.

Neste caso, recomendamos que outras opções sejam consideradas, desde a utilização de múltiplos processos, o uso de outro interpretador Python mais adequado, ou, se possível, **a utilização de outra linguagem mais apropriada para tarefas *CPU-bound*, a fim de maximizar a performance de sua aplicação.**

O processo de categorização da aplicação entre as categorias *CPU-bound* e *IO-bound* é determinante para a definição da arquitetura de qualquer aplicação. **[Com Python, devemos entender o funcionamento do interpretador de código para garantirmos que os requisitos de performance sejam atendidos pelo sistema](https://twitter.com/intent/tweet?text=%20Com%20Python,%20devemos%20entender%20o%20funcionamento%20do%20interpretador%20de%20c%C3%B3digo%20para%20garantirmos%20que%20os%20requisitos%20de%20performance%20sejam%20atendidos%20pelo%20sistema&url=https://www.eximiaco.tech/?p=2642&via=eximiaco)**.

## EM RESUMO

## O problema
O desenvolvimento de uma aplicação concorrente em Python pode se transformar em um processo desafiador caso não se tenha domínio sobre o funcionamento do interpretador e de suas estruturas internas, e em específico sobre o Global Interpreter Lock.

## O insight
Aplicações Python que utilizam o interpretador padrão, ou seja, a esmagadora maioria delas, possuem a limitação de executar código de maneira single-threaded, salvo em operações de IO. Na maioria dos casos, em específico nas aplicações IO-bound, essa limitação não traz nenhum impacto negativo, porém em aplicações categorizadas como CPU-bound, não existirá ganho de performance caso seja empregada uma estratégia de execução multi-thread.

## Os benefícios
A partir da compreensão do funcionamento do interpretador Python e como ele se comporta em ambientes paralelos, é possível estruturar a aplicação de forma que se tenha um impacto significativo na performance de aplicações CPU-bound, utilizando os recursos disponíveis.
