# eureka-bank-server

Service Discovery para registro dos serviços bancários

Este é o repositório principal referente aos microsserviços de simulação de transferência bancária.

O eureka-bank-server é uma implementação simplificada para execução do Eureka, serviço destinado ao registro
e descobrimento de instâncias de outros microsserviços. Em síntese, além do eureka-bank-server, tem-se os seguintes 
repositórios e serviços:

* cadastro-cliente-service (API para controle de registros de clientes)
  * Link de acesso: https://github.com/MatheusFerraz/cadastro-cliente-service/tree/main
* saldo-transferencia-service (API para controle de saldo e efetivação de transferências bancárias)
  * Link de acesso: https://github.com/MatheusFerraz/saldo-transferencia-service/tree/main
* bacen-service (API para simulação de recepção de transferências)
  * Link de acesso: https://github.com/MatheusFerraz/bacen-service/tree/main

Todos os microsserviços citados acima são inicializados e se registram no Servidor Eureka. Quanto à visão de arquitetura 
de software em cada microsserviço, optou-se pela concepção de uma arquitetura padrão, contendo os seguintes
pacotes:

* **Controller:** camada responsável por realizar exposição dos endpoints da API
* **Model:** camada responsável por abranger as entidades persistentes, bem como objetos de domínio de retorno
da integração com outros microsserviços
* **Service:** camada responsável por encapsular as chamadas à camada repository, bem como conter regras negociais
simples
* **Repository:** camada responsável por efetuar comunicação com o banco de dados (nos microsserviços foi utilizado 
o h2, um banco de utilização em memória, apenas para tornar a simulação mais real ao invés de apenas retornar mocks diretos;
é válido ressaltar que a solução mais real possui proposta de utilização do DynamoDB (conforme desenho de arquitetura) e assim
bastaria adequar a camada repository para utilizar um client de conexão e manipulação apropriado para o DynamoDB)
* **Exception:** camada responsável por conter as classes específicas para tratamento de exceção
* **Integration:** camada responsável por conter serviços de integração entre microsserviços e chamadas externas

É válido ressaltar que também poderia ter sido utilizado uma Arquitetura Hexagonal, bem como aplicação de outros
princípios do Clean Architecture. Contudo, para estes projetos, jugou-se pertinente utilizar uma arquitetura padrão
de visão Controller -> Service -> Repository.

Com relação à resiliência de clientes, tem-se o padrão Circuit Breaker com o Hystrix. Nos projetos em questão, adotou-se
apenas uma demonstração de exibição do funcionamento dos métodos de fallbacks, que possuem a finalidade de criar retornos padrões
quando o circuito se encontra aberto ou quando a chamada a algum outro método falha. É válido ressaltar que após um intervalo,
que pode ser parametrizado, o Hystrix pode fechar o circuito para que as requisições voltem a ser encaminhadas para o fluxo normal.

Outro aspecto que é importante considerar é que a utilização do Zuul Gateway pode ser viável a fim de se unificar as chamadas,
visto que o mesmo permite um caminho único para os microsserviços, facilitando até mesmo autenticação e configuração de rate limits de maneira
centralizada.

Com relação aos testes unitários, embora nem todos os microsserviços possuam implementações, é válido notar que pode
ser utilizado o Mockito para contrução de mocks, assertivas do Junit e o JsonPath para testar conteúdo de retorno dos
endpoints.