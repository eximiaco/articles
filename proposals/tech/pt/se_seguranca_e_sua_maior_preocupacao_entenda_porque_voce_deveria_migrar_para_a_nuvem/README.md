# Se segurança é sua maior preocupação, entenda porque você deveria migrar para a nuvem

Ainda é comum encontrar executivos de TI que evitam migrar as cargas de trabalho da empresa para a nuvem, por sentirem-se inseguros ao delegar o armazenamento dos seus dados para um terceiro. Essa desconfiança geralmente é fundamentada pela percepção de que, se os dados estão dentro de casa (*on-premise*), é mais difícil que os mesmos sejam acessados de forma indevida. Uma analogia é guardar dinheiro sob o colchão, por não confiar no sistema bancário.

Entretanto, na maioria das vezes, os argumentos que sustentam esta afirmação são bastante superficiais, deixando de considerar alguns aspectos fundamentais para uma conclusão mais precisa.

***

**Neste sentido, um aspecto muito importante ao avaliar a migração, é que a responsabilidade na nuvem é compartilhada entre o provedor, que fornece a segurança "da" nuvem, e o cliente, que é responsável pela segurança "na" nuvem. Na prática, o provedor garante a segurança dos produtos e serviços que ele oferece e o cliente é responsável pela segurança dos recursos que ele utiliza do provedor.** Graças a este modelo, o cliente estará recebendo, de forma indireta, todos os cuidados que o provedor tem ao manter seus data centers, através de mão de obra especializada e procedimentos rigorosos. Uma das formas de validar a aplicação destas práticas de segurança é avaliar as certificações dos provedores:

![AWS Compliance](./aws.png "AWS Compliance")\
  *Algumas certificações da AWS*

***

![Azure Compliance](./azure.png "Azure Compliance")\
*Compliance do Microsoft Azure*

***

![Google Cloud Compliance](./google.png "Google Cloud Compliance")\
*Algumas certificações do Google Cloud*

Certificações como a *[PCI-DSS](https://pt.pcisecuritystandards.org/)*, exigem processos bastante rigorosos para serem conquistadas. O custo e o esforço para adequar a organização aos requisitos para atender estas certificações, em um ambiente *on-premise*, é bastante elevado e muitas vezes inviável. Na nuvem, este escopo é reduzido através das certificações que o provedor já possui, e isso acaba tornando-se transparente para os clientes. Neste ponto, é importante reforçar também que, assim como o cliente, o provedor precisa garantir a segurança de suas instalações, afim de manter a confiabilidade e a saúde do seu negócio.

***

**Outro aspecto a ser considerado é que o provedor fornece serviços especialistas para auxiliar a manter o ambiente de seus clientes seguro.** Na *[AWS](https://aws.amazon.com)*, por exemplo, o *[Amazon GuardDuty](https://aws.amazon.com/pt/guardduty/)* é um serviço que analisa logs de diversas fontes, e aplica algoritmos de inteligência artificial para identificar qualquer anomalia, possibilitando a mitigação da ameaça de forma proativa e autonoma. No *[Microsoft Azure](https://azure.microsoft.com)*, o *[Azure Sentinel](https://azure.microsoft.com/pt-br/services/azure-sentinel/)* é um *[SIEM](https://pt.wikipedia.org/wiki/Gerenciamento_e_Correla%C3%A7%C3%A3o_de_Eventos_de_Seguran%C3%A7a)*, que analisa logs e eventos utilizando *Machine Learning*, permitindo a identificação e o tratamento de ameaças em tempo real. Já no *[Google Cloud](https://cloud.google.com/)*, o *[Cloud Security Command Center](https://cloud.google.com/security-command-center)*, auxilia na detecção e mitigação de ameaças. É possível encontrar soluções com propósitos similares para ambientes *on-premise*, entretanto, geralmente são comercializadas em um modelo de licenciamento, tornando-as mais caras, e demandando um esforço maior de implementação.

***

**É importante lembrar também, que segurança, de forma mais ampla, não trata apenas de acesso indevido aos dados, mas também da sua durabilidade. Da mesma forma como "vazar" informações confidenciais, perder informações importantes também pode comprometer todo o negócio.** Em um cenário *on-premises*, soluções de *backup* são implementadas afim de reduzir este risco, contudo, dependendo do *[RPO](https://www.ibm.com/services/business-continuity/rpo)*, ainda existe a possibilidade de objetos importantes serem perdidos durante a janela entre a execução do backup e o momento em que uma falha acontece (como um defeito em um dispositivo de armazenamento). Somado a isso, dependendo da solução de *backup* adotada, também existe a probabilidade do processo falhar, seja por um problema no *backup/restore* ou no armazenamento.

Na nuvem, os provedores fornecem serviços com alta durabilidade "*by design*". O *[Amazon S3](https://aws.amazon.com/pt/s3/)*, o [Google Cloud Storage](https://cloud.google.com/storage) e o *[Azure Storage](https://azure.microsoft.com/pt-br/services/storage/)*, por exemplo, oferecem "99,999999999%" (11 9's) de durabilidade, que, por sinal, são excelentes soluções para armazenar *backups*. De forma estatística, para um conjunto de 10 milhões de objetos armazenados, a probabilidade destes serviços perderem objetos, é de, no máximo, 1 a cada 10.000 anos. Está durabilidade é atingida através da replicação destes objetos em múltiplas cópias, em diversos *datacenters*, com um controle automático e transparente de recuperação.


Dependendo do modelo de negócio, segurança é uma necessidade. Provedores de nuvem proporcionam recursos e infraestrutura em seus datacenters que seriam dificeis de estabelecer e manter em um ambiente *on-premise*. Por este motivo, a adoção da nuvem é um fator adicional para garantir a segurança do ambiente.
