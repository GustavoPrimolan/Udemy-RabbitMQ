

<h1>Se��o 01 - Introdu��o ao RabbitMQ</h1>

<h2>O que � RabbitMQ</h2>

* O message broker mais utilizado.
* C�digo aberto
* Leve e f�cil de fazer o deploy
* Suporta diferentes protocolos de mensagem
* Pode ser feito o deploy em clusters para alta escalabilidade e disponibilidade. Necess�rio em solutions empresarias.
* Usado pelo Google, VMWare e Mozilla.

<h2>Principais caracter�sticas</h2>

* Level de confiabilidade vari�vel, geralmente configurando uma maior confiabilidade ir� reduzir a performance. Isso pode ser gerenciado como preferido.

* Capacidade complexa em encaminhar mensagens.

* Diferentes configura��es para propositos de mensagens.

<h2>Conceitos do RabbitMQ</h2>

* Primeiro � o producer, o intuito dele � enviar uma mensagem na fila, qualquer tipo de mensagem.
* A fila � onde as mensagens ficarao armazenadas, vinda do producer.
* Consumer serve para consumir, ele retira a mensagem da fila. Pega a informa��o da fila.

<img src="imgs/01.PNG"/>


* Publisher envia a mensagem para o Exchange, depois envia para as filas depois o consumidor recebe das filas a mensagem.
* O Exchange � um aterfato do Rabbit para rotear essa mensagem.
* Ele escolhe para qual lugar a mensagem dever� ir com uma chave de liga��o (binding key).

<img src="imgs/02.PNG"/>

<img src="imgs/03.PNG"/>


* Existe a mensagem, onde existe o corpo dela
* O Routing Key serve como label e indica onde a mensagem tem que passar.
* Routing Key vai para a dire��o do binding Key e vai para o consumir

<img src="imgs/04.PNG"/>

* Entre os Binding Keys temos 4 exemplos
<h2>Fanout</h2>
* O primeiro � o Fanout - Basicamente o RabbitMQ que n�o tem nenhum Binding Key, n�o especifica onde a mensagem tem que passar, aleatoriamente. Todas ser�o enviadas para consumer

<h2>Direct</h2>
* Ela envia uma mensagem por apenas uma fila, ex: No Rounting Key A e o Binding Key A vai para a dire��o onde a fila est�.

<img src="imgs/05.PNG"/>

<h2>Topic</h2>
* Resume a mensagem, por exemplo: Voc� envia um indicador red.* ele ir� procurar entre as binding keys a palavra que comece com red.
<img src="imgs/06.PNG"/>

<h2>Header</h2>
* Ele n�o usa Binding Key, apenas envia a mensagem. RabbitMQ normal, manda a mensagem A e envia para qualquer fila a mensagem A at� o consumer.

<h2>Default (nameless) Exchange</h2>
* O Routing Key vai levar o nome da fila. N�o existe Binding Key, apenas o nome da fila.
* Ao inv�s de utilizar o binding key voc� indica pela fila.
<img src="imgs/07.PNG"/>



-------------------------------------------------------------------
<h1>Se��o 02 - Instalando e configurando o RabbitMQ</h1>

* Primeiro passo: Instalar o Erlang

* Download e instalando o RabbitMQ de acordo com o seu sistema operacional. Isso se encontra no site do RabbitMQ.

<h2> Habilitando o Plugin de Gerenciamento do RabbitMQ</h2>
* Abrir o prompt do RabbitMQ
* O Plugin j� est� incluso por padr�o na instala��o do RabbitMQ, s� precisa ser habilitado.
* rabbitmq-plugins enable rabbitmq_management (Comando para ser executado) 

<h2>Checkar que tudo est� funcionando</h2>
* Apenas necess�rio acessar o http://localhost:15672 para carregar a o gerenciador em modo interface de usu�rio.
* Login e senha padr�o s�o:
* Login: guest
* Senha: guest


----------------------------------------------------------------------
<h1>Se��o 03 - Mensagens, Queues (Filas) e Exchanges (Trocas)</h1>

<h2>Mensagens ou Messages</h2>
* Uma mensagem � um blob bin�rio de um dado que ser� gerenciado pelo RabbitMQ e pode ser distribuido pelas filas (Queues) e Exchanges.

* Producer � um termo gen�rico que utilizamos para dizer que produz a mensagem e envia para a fila/troca do RabbitoMQ.

* Consumer � o termo gen�rico que utilizamos para dizer que recebemos as mensagens do RabbitMQ.

<h2>Queues ou Filas</h2>

* � o local onde a mensagem seguir� e ser� armazenada no RabbitMQ.

* Parece com um grande Buffer Message

* Existe um limite de tamanho de mensagem.

<h2>Exchange</h2>

* Recebe as mensagens do Producer e empurra elas para as filas.

* Ele pode enviar para uma ou v�rias filas dependendo das configura��es da rota.

<h2>Confirmador de mensagens</h2>

* Se a conex�o falhar entre o servidor Rabbit e o client (producer ou consumer), as mensagens n�o ser�o processadas corretamente e ser� necess�rio um reenvio.

* Se o enviador n�o receber uma mensagem positiva (acknowledgement ou ack) antes da conex�o falhar ele ter� que recolocar a mensagem na fila.

* Existem diferentes configura��es para confirmar mensagens, por exemplo ativar a o mode de confirma��o automatica (enabling automatic acknowledgement mode) a mensagem ser� considerada confirmada logo que for enviada. Ativando o modo atira e esque�a (fire and forget mode).

* Ir� diminuir a seguran�a das entregas das mensagens mas aumentar a taxa de transfer�ncia (throughput)

* Os consumer tamb�m podem enviar uma confirma��o negativa para a mensagem, e instruir para que o message broker recoloque na fila.

* Tanto a confirma��o negativa ou positiva podem ser enviadas em massa, setando a multiple flag do acknowledgement command para true.

* Protocol methods para acknowledgements:
	* basic.ack usado para confirma��es positivas (positives acknowledgements).
	* basic.nack usado para confirma��es negativas (negatives acknowledgements).
	* basic.reject tamb�m � usado para confirma��es negativas mas � o �nico capaz de rejeitar uma mensagem por vez.
* O nome do comando exato ir� depender das libs do client e das diferentes linguagens de programa��o.

<h2>Queues - Filas</h2>

* Por padr�o, a ordem das mensagens em queues � First In First Out (FIFO).

* Entretanto, as filas podem ser configuradas com prioridades, onde cada mensagem ser� ordenada dependendo da sua prioridade de executar, que � setada no sender.

<h3>Durability ou Durabilidade</h3>

* Queues duraveis s�o persistidas para o disco e ent�o sobrevivem quando reinicia o broker. As Queues que n�o s�o dur�veis s�o chamadas de transient.

* Configurando uma fila como duravel n�o faz com que a mensagem a ser enviada seja duravel.

* O message sender pode configurar o delivery_mode property para a mensagem como persistent, onde ir� persistir no disco e logo ser� recebida por uma fila duravel.

* Em casos de non-persistent messages tamb�m s�o escritos quando a mem�ria � baixa. Entretanto, isso n�o os faz duravel.

<h3>Filas temporarias ou Temporary Queues</h3>

* Podem ser configuradas para serem excluidas de tr�s formas:
	* Exclusive queues pode apenas ser usadas pelas suas conex�es decladas, e ser� automaticamente deletada quando a conex�o for fechada.
	* Tempo expirado (Expiry Time) ou tamb�m conhecida como tempo para viver (time to live) pode ser configurada para uma fila. Se a fila n�o for usadas nesse tempo configurado a fila ser� apagada.
	* Filas auto-deletaveis (Auto-delete queues) ser�o automaticamente deletadas uma vez que o ultimo consumer tiver cancelado o basic.cancel protocol or gone. (Exemplo: fechar conex�o).

<h2>Demonstra��o</h2> 

* Na aba Queues poder� criar filas

<img src="imgs/08.PNG"/>

* Escolher nome, durability, Auto delete e os arguments
<img src="imgs/09.PNG"/>

* Voc� criar uma fila duravel e outra transient, voc� ver� que quando o servi�o do RabbitMQ for reiniciado, apenas a fila duravel ir� permanecer.

--------------------------------------------------------------------------------------------------------
<h1>Se��o 04 - Exchanges and Complex Routing (Rotas Complexas)</h1>

<h3>Exchange Types</h3>

* Exchanges recebem as mensagens do producer e empurram elas para as filas

* Pode ser configurado para enviar as mensagens para 0 ou mais queues.

* Existem quatro tipos de Exchanges
	* Fanout
	* Direct
	* Topic
	* Headers

<h3>Fanout Exchange</h3>

* O tipo Fanout roteia uma mensagem para todas as filas.

* Ignora qualquer routing key enviada com a mensagem.

<img src="imgs/10.PNG"/>

<h3>Direct Exchange</h3>

* O tipo Direct Exchange roteia a mensagem com um routing key particular para as  filas que tem os exatos routing key.

<img src="imgs/11.PNG"/>

<h3>Topic Exchange</h3>

* O tipo Topic Exchange voc� monta um padr�o para routing key.

* As mensagens s�o publicadas com routing keys que contem uma ou mais palavras separadas por ponto (.). Ex: multi.word.test

* O padr�o �:
	*  ('*') Significa que combina qual palavra em uma posi��o espec�fica da routing key
	*  ('#') combina zero ou mais palavras.

* Exemplo: Uma mensagem publicada com routing key 'multi.word.test'
	* Ira combinar com filas com routing key 'multi.#', '*.word.*', 'multi.word.test', '#'
	* Mas n�o ira combinar com filas com routing key 'multi.*', 'single.#' ou 'multi.foo.test'

<img src="imgs/12.PNG"/>

<h2>Headers Exchange</h2>

* Parecido com o Topic Exchange, mas o topic exchange combina com a routing key e o header exchange combina com o cabe�alho da mensagem (message header).

<img src="imgs/13.PNG"/>

------------------------------------------------------------------------------------
<h2>Se��o 5 - Plugins</h2>

<h2>Consistent-Hash Exchange Plugin</h2>

<h3>O que o Plugin faz?</h3>

* Adiciona novos tipos de exchange: the consistent-hash exchange.

* O consistent-hash exchange mede as mensagens enviadas para que um exchange � consistente e euqalmente distribuida para os limites das filas dependendo dos routing key das mensagens

* Isso significado que se duas mensagens possuem a mesma routing key, a consistent-hash exchange ir� rotear elas para a mesma fila a menos que a liga��o das filas tenha mudado o exchange.

* Se o n�mero de filas do exchange mudar, o consistent-hash exchange ir� distribuir rounting keys entre essas filas.

<h3>Configura��es do Consistent-Hash</h3>

* Por padr�o o consistent-hash exchange realiza o hashing no routing key message, mas tamb�m pode ser feito em:

* Message Properties: Declarando o exchange com o argumento "hash-property" e o valor do argumento como message_id, correlation_id ou timestamp.

* Message Headers: Declarando o exchange com o argumento "hash-header" e o valor do argumento como header name para ser usado pelo hashing.

* A rela��o entre o routing keys distribuidos entre as filas pode ser controlada mudando o valor do binding key da fila do exchange.

* Entretando, se voc� quiser que a fila A recebe duas vezes as muitas routing keys roteadas que s�o roteadas para a fila B, ent�o voc� a fila A com o binding key de duas vezes o n�mero.

<img src="imgs/14.PNG"/>

<h2>Enabling the plugin</h2>

* Execute o comando
* rabbitmq-plugins enable rabbitmq_consistent_hash_exchange

-----------------------------------------------------------------------------
<h1>Se��o 06 - Software Architecture Applications</h1>

<h2>Para que o RabbitMQ pode ser usado?</h2>

* Processamento Assyncrono

* Trabalho de filas, distribuindo o trabalho entre multiplos workers

* Publishing data para ser capturada em multiplos consumers inscritos para um exchange

* Handling Remote Procedure Calls de uma forma escal�vel.


<h2>Para que o RabbitMQ N�O DEVE ser usado</h2>

<img src="imgs/15.PNG"/>

* � muito mais simples e mais leve usar uma chamada de APi, ao inves de envolver o RabbitMQ para entrega de mensagem.


<img src="imgs/16.PNG"/>

<h2>Exemplo de um cen�rio</h2>

* Um sistema onde um usuario faz o upload de um arquivo para o site, o site scaneia o arquivo por virus e retorna a mensagem para o usu�rio indicando se o arquivo est� limpo. Um email ser� enviado para outro usu�rio comoa vers�o compressa do arquivo, e pode ser feito de forma assincrona.

* Arquitetura do sistema seria mais ou menos assim:


<img src="imgs/17.PNG"/>














