# MANIPULANDO OS EVENTOS DO PLAYER DA ALEXA

Como citado nos posts anteriores da [série](https://www.eximiaco.tech/pt/serie/criando-uma-skill-para-a-amazon-alexa/), ao ativar o *AudioPlayer* da Alexa, ela fornece alguns *intents* específicos para a obtenção do estado da execução. De modo geral, na *skill* da EximiaCo, utilizamos estes *intents* para duas tarefas principais: atualizar o estado de reprodução do episódio atual e para gerenciar o *playback* contínuo de episódios.

```
import ask_sdk_core.utils as ask_utils

from .player import Player
from ask_sdk_core.dispatch_components import AbstractRequestHandler


class PlaybackStartedHandler(AbstractRequestHandler):
    def can_handle(self, handler_input):
        return ask_utils.is_request_type("AudioPlayer.PlaybackStarted")(handler_input)

    def handle(self, handler_input):
        player = Player(handler_input)
        player.handle_playback_started()

        return handler_input.response_builder.response


class PlaybackNearlyFinishedHandler(AbstractRequestHandler):
    def can_handle(self, handler_input):
        return ask_utils.is_request_type("AudioPlayer.PlaybackNearlyFinished")(handler_input)

    def handle(self, handler_input):
        player = Player(handler_input)
        player.handle_playback_nearly_finished()

        return handler_input.response_builder.response


class PlaybackStoppedHandler(AbstractRequestHandler):
    def can_handle(self, handler_input):
        return ask_utils.is_request_type("AudioPlayer.PlaybackStopped")(handler_input)

    def handle(self, handler_input):
        player = Player(handler_input)
        player.handle_playback_stopped()

        return handler_input.response_builder.response


class PlaybackFinishedHandler(AbstractRequestHandler):
    def can_handle(self, handler_input):
        return ask_utils.is_request_type("AudioPlayer.PlaybackFinished")(handler_input)

    def handle(self, handler_input):
        player = Player(handler_input)
        player.handle_playback_finished()

        return handler_input.response_builder.response
```

Com o *intent AudioPlayer.PlaybackStarted*, simplesmente armazenamos o episódio em execução atual, para que, posteriormente, ele possa ser recuperado durante as possíveis modificações no estado do *playback*.

```
def handle_playback_started(self):
    current_token = self.handler_input.request_envelope.request.token
    self.state.set_token(current_token)
    self.state.set_current_episode(current_token)
```

No *intent AudioPlayer.PlaybackNearlyFinished*, a Alexa está informando que o episódio está perto do final, dando a possiblidade de definir o próximo episódio a ser executado. **Importante reforçar a utilização do *token* ao enfileirar o episódio. Quando é iniciada a reprodução, é informado o *token* da execução atual e também o *token* esperado da execução anterior, para garantir a ordem da execução. Caso o *token* anterior informado não coincida com o existente no episódio anterior (ou o atual, caso não tenha finalizado a reprodução), a diretiva *PlayDirective* é ignorada.**

```
def handle_playback_nearly_finished(self):
     current_episode = self.state.get_current_episode()
     if current_episode is None:
         return

     previous_episode = current_episode

     if not self.state.get_repeat():
         previous_episode = self.episodes_provider.get_previous(current_episode)

     if previous_episode is None and self.state.get_loop():
         previous_episode = self.episodes_provider.get_latest()

     if previous_episode is None:
         return

     self.handler_input.response_builder.add_directive(
         PlayDirective(
             play_behavior=PlayBehavior.ENQUEUE,
             audio_item=AudioItem(
                 stream=Stream(
                     token=previous_episode["pub"],
                     url=previous_episode["address"],
                     offset_in_milliseconds=0,
                     expected_previous_token=current_episode["pub"]),
                     metadata=None))
         ).set_should_end_session(True)
```

Quando a execução do episódio é cancelada com um comando como “Pare”, ou “Cancelar”, é invocado o *intent AudioPlayer.PlaybackStopped*. Neste *intent*, armazenamos o *offset* do episódio que está sendo reproduzido. Isto possibilita que a Alexa retome a reprodução do episódio quando for solicitado que ela continue a reprodução.

```
def handle_playback_stopped(self):
    millis = self.handler_input.request_envelope.request.offset_in_milliseconds
    self.state.set_offset(millis)
```

Ao finalizar o episódio, a Alexa invoca o *intent AudioPlayer.PlaybackFinished*. Neste momento apenas zeramos o *offset* do *playback*. Isso faz com que, ao iniciar um novo episódio, a Alexa comece a reprodução do início, e não do *offset* supostamente armazenado no estado da execução.

```
def handle_playback_finished(self):
    self.reset()
```

Nesta etapa, a *skill* está pronta. No próximo *postU|* da série, falaremos sobre o seu processo de publicação.

Para quem acompanha os drops da EximiaCo, ou para aqueles que gostariam de acompanhar, a nossa skill está disponível no  *[Marketplace da Alexa](https://www.amazon.com.br/dp/B087QV63BF)*. Gostaríamos muito de conhecer a sua opinião.