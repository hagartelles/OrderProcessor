O Projeto: Microsserviço de Processamento de Pedidos (E-commerce)
O objetivo é criar um microsserviço assíncrono que receba pedidos, processe a baixa de estoque e registre logs detalhados para auditoria e troubleshooting.

Requisitos Técnicos a serem aplicados:

Framework: .NET Core / .NET 6 ou superior.
Banco de Dados: SQL Server (modelagem e manutenção).
Mensageria: RabbitMQ, Kafka ou Azure Service Bus.
Observabilidade: Logs estruturados utilizando Serilog ou Application Insights.
Qualidade: Testes unitários (obrigatórios) e testes de integração (diferencial).
Versionamento: Prática do Gitflow (uso de branches feature, release, hotfix).

Passo a Passo do Desafio

1. Modelagem e API REST 
Crie uma API para o cadastro e consulta de Produtos (com Id, Nome, Preço e Quantidade em Estoque).
Crie um endpoint para o recebimento de Pedidos. Esse endpoint não deve processar o pedido na hora. Ele deve apenas validar o payload, salvar um status inicial de "Pendente" no SQL Server e retornar um 202 Accepted para o cliente.

2. Integração com Filas (Mensageria) 
Após o endpoint de Pedidos aceitar a requisição, ele deve publicar uma mensagem em uma fila (ex: pedido-criado-event) utilizando RabbitMQ ou Kafka (você pode subir as imagens facilmente usando Docker).
Crie um Worker Service (ou um consumidor em background) que fique escutando essa fila. Quando a mensagem chegar, ele deve:
Verificar se há estoque disponível no SQL Server.
Se houver, debitar o estoque e mudar o status do pedido para "Aprovado".
Se não houver, mudar o status para "Rejeitado por falta de estoque".

3. Observabilidade e Tratamento de Erros 
Integre o Serilog no projeto.
Crie logs estruturados em todas as etapas críticas: quando o pedido é recebido na API, quando entra na fila, quando o worker inicia o processamento e o resultado final.
Propositalmente, crie um cenário onde o banco de dados falhe (ou simule uma exceção). O log deve capturar a stack trace e os dados do pedido de forma estruturada, provando que você consegue fazer troubleshooting com autonomia em um ambiente que simula a produção.

4. Testes Automatizados 
Escreva Testes Unitários utilizando xUnit ou NUnit para as regras de negócio principais (ex: o serviço que valida e deduz o estoque).
Se quiser um diferencial, crie pelo menos um Teste de Integração simulando a chamada na API de criação de pedido ou verificando a persistência no SQL Server. O código precisa se manter legível e sustentável.

5. Fluxo de Versionamento 
Não faça tudo na branch main.
Inicie criando uma branch feature/setup-api.
Crie outras para a mensageria: feature/rabbitmq-integration.