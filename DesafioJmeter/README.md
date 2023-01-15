## JMETER
o [JMeter](https://jmeter.apache.org/) é um software de código aberto, um aplicativo em java que foi projetado para realizar testes de carga pesada,grupo de servidores, rede ou objeto para testar e analisar o desempenho geral.

###### Configuração NECESSÁRIA ######

## Apache JMeter
Para instalar o Apache JMeter será necessario baixar o instalador acessando o site oficial [nesse link](https://jmeter.apache.org/) no lado direito a opção "Download Releases".
1. Escolha a opção que deseja podendo ser Source ou Binaries.
2. Selecione os arquivos .zip e extensões tgz.
3. Clique em extrair arquivo.


## Java
Para instalar o Java será necessario baixar o instalador acessando o site oficial [nesse link](https://www.java.com/pt-BR/download/) e clique na opção "Fazer Download do Java".
1. Clique duas vezes no instalador para iniciar o assistente de instalação do Java.
2. Clique na opção "Instalar". 
3. Após o processo de instalação clicar em fechar.

## Abrindo o projeto
Para abrir o projeto será necessário seguir as seguintes orientações:
1. Dentro da pasta do jmeter entrar na pasta bin e executar o arquivo jmeter.bat
2. Clique na opção arquivo e selecione a opção abrir 
3. Selecione o arquivo TesteDeCarga.jmx
4. Selecionar a opção Options e clicar na opção choose language e selecionar a opção "Portuguese(brazilian)"

## Executando projeto:
Com o projeto já aberto basta apenas clicar no plano de teste e depois na primeira seta verde(botão iniciar) no cabeçalho.

## Exportação de resultados
Caso haja necessidade de rodar o projeto é necessário clicar no botão "Salvar dados da tabela" nas páginas "Relatório agregado" e "Relatório de sumário" e quanto aos resultados em tabela ele é gerado automaticamente quando roda o teste.
OBS: Surgirá um pop-up e pedindo para acrescentar ao documento existente ou sobrescrever o arquivo já existente e caso queira gerar um novo é só trocar o caminho de saída.

## Desafio- Teste de Carga com JMeter

Descrição da demanda: 
Foi verificado a necessidade de realizar testes na api no site accor-fab.herokuapp.com/swagger para que possa verificar a integridade da api como a sua resposta de requisição, degradação de respostas e numero de requests.

Solução proposta:
A ferramenta escolhida para realização do teste foi o JMeter onde será feito o teste de carga com grupos de usuários, para verificar a integridade da api, localizando possíveis erros, latência e vazão.

## Plano de teste
I- Para execução dos testes foi pensado na utilização de três endpoints da api que são:
GET e POST: /dev/users
PATCH: /dev/users/{userId}
A escolha foi feita pensando em um fluxo básico de usuários onde fariam o POST, GET e depois o PATCH alterando o usuário que ele criou no POST.
II-Dentro do plano de teste foram criadas três variáveis randômicas para que cada POST seja executado com um usuário diferente, essas variáveis serão colocadas como valores do JSON de POST e PATCH.
III-Para carga foi criado um grupo de 500 usuários para interagir com as apis de forma simultânea, simulando uma carga de usuários fazendo um fluxo de uso natural do sistema e realizar requisições GET, POST e PATCH nos endpoints no dev/users
IV-Para obter as respostas do teste de carga foi utilizado os seguintes ouvintes: 
1. Ver Árvore de Resultados: Neste é possível verificar os resultados da requisição e os dados da resposta. 
2. Ver Resultados em Tabela: É possível verificar o tempo da amostra,latência e o tempo de conexão.
3. Relatório de Sumário: Neste é possível verificar o tempo médio, máximo e mínimo, vazão(Throughput) e porcentagem de erro.
4. Relatório Agregado: É possível verificar o tempo médio,máximo e mínimo, faixa de usuário 90%, 95% e 99%, vazão(Throughput) e porcentagem de erro.


## Resultados
Através do relatório **"Ver Árvore de Resultados"** podemos verificar informações de cada requisição e suas respostas(responses), e ao analisar os dados do teste já foi possível ver algumas falhas nas requisições sendo elas:

POST: Erro 500 - Internal Server Error 
PATCH: Erro 405- Method Not Allowed

Ao conferir o PATCH foi notado que o erro Not Allowed foi devido ao erro 500 do POST, pois esta sendo feito um fluxo e esta sendo pego o Id da resposta do POST para atualizar no PATCH automaticamente se der erro no POST consequentemente o PATCH dará erro.
OBS: Na avaliação dos responses também foi possivel verificar que mesmo colocando os dados corretamente no PATCH ele retorna id=0, "null" nos valores firstName e lastName e no valor email "vcxvcxcxv@vccx.com".

<div align-center>
  <img  src="https://user-images.githubusercontent.com/105563616/212572310-d96230fc-5da7-4de2-b34e-50b48f7c1d0e.png"  width=700 />
</div>
<br>

O relatório **"Ver Resultados em Tabela"** podemos verificar o tempo das amostras individualmente de cada requisição, ao executar a carga, o número de amostragem a partir de 844(ms) alguns POST começaram apresentar erro 500, lembrando que os usuários obteve erro no POST consequentemente obteve erro no PATCH.

<div align-center>
  <img  src="https://user-images.githubusercontent.com/105563616/212572834-e539267a-a454-4d57-9b0c-165be4e58990.png" width=700 />
</div>
<br>
No **"relatório de Sumário"** podemos verificar o tempo mínimo POST(800 ms) e o máximo POST(1976 ms) com a porcentagem de erro de 23% e vazão de 204,0/sec,e ao comparar com o GET sabendo que para um cenário otimista o ideal seria uma vazão grande com o tempo de resposta baixo e não é isso que acontece com o GET e ainda assim ele performa melhor que POST. Como apresentado no relatório anterior o POST passa apresentar erro a partir 844(ms), sendo que ele inicia já com 800(ms) como o tempo de amostragem mais baixo. Nas primeiras requisições ele já começa apresentar a instabilidade em algumas requisições e começando a falhar.
OBS: Quanto aos dados do PATCH nenhum dos erros apresentados foram referentes a carga no endpoints, seus erros foram ocasionados devido ao fluxo de teste de quando um POST falha um PATCH também falha.Sendo assim, a % de erro apresentada do endpoints não corresponde a carga que foi direcionada ao endpoints de PATCH.

<div align-center>
  <img  src="https://user-images.githubusercontent.com/105563616/212573029-8435a8c3-10dc-4b7c-8a0b-61811e01dcc4.png" width=700 />
</div>
<br>
No **"relatório Agregado"** podemos verificar a faixa de usuário(percentil) de 99% line tiveram um tempo de resposta entre 800(ms) e 1527(ms), considerando que entre 100(ms) e 1000(ms) um tempo bom de tempo de resposta e de 1000(ms) a 10000(ms) que onde se encaixa o POST já é considerado problemático, notando também que o GET também se encaixa no último caso citado e o PATCH estando no melhor cenário. A média da porcentagem de erro foi de 15,33%, mas podemos considerar metade levando em consideração que os problemas causados no PATCH foram derivados do POST. 

<div align-center>
  <img  src="https://user-images.githubusercontent.com/105563616/212573190-d8b1ab51-f1ff-4886-a0d7-d80cf7be1895.png" width=700 />
</div>
<br>

## Conclusão
POST demonstrou ser a requisição mais frágil em relação ao teste de carga apresentando erro 500 quando está estressada, já o GET apesar de não apresentar erro 500 demonstrou ter uma lentidão muito grande nas requisições e o PATCH tendo a melhor performance, ignorando o erro do POST.
