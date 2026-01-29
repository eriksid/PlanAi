# ## PRD: Mini-Ambev Logistics Hub

### ## 1. Objetivo do Produto

Criar um sistema de backend resiliente para gerenciar o ciclo de vida de pedidos e entregas, utilizando arquitetura de microsserviços para garantir escalabilidade e isolamento de domínio.

---

### ## 2. Público-Alvo

* **Operadores de Logística:** Para criar e monitorar pedidos.
* **Motoristas:** Para atualizar o status da entrega em tempo real.

---

### ## 3. Requisitos Funcionais (Escopo do MVP)

| ID | Funcionalidade | Descrição |
| --- | --- | --- |
| **RF01** | **Criação de Pedidos** | O sistema deve permitir criar um pedido com cliente, itens e endereço. |
| **RF02** | **Atribuição de Entrega** | Ao criar um pedido, o sistema deve disparar um evento para o serviço de logística. |
| **RF03** | **Atualização de Status** | O motorista deve conseguir alterar o status para "Em Rota" ou "Entregue". |
| **RF04** | **Dashboard Real-time** | Uma interface web para visualizar o status consolidado de todos os pedidos. |

---

### ## 4. Requisitos Não Funcionais (A "Cereja do Bolo" para a Vaga)

* **Arquitetura Hexagonal:** O núcleo de negócio não deve depender de frameworks externos.
* **Mensageria:** A comunicação entre o serviço de Pedidos e o de Logística deve ser assíncrona (simulando Kafka/RabbitMQ).
* **Observabilidade:** Logs estruturados e métricas que poderiam ser lidas pelo DataDog.
* **Cloud Native:** O projeto deve estar pronto para rodar em containers Docker e ser orquestrado via Kubernetes.

---

### ## 5. Arquitetura Técnica e Stack

#### **Backend (Java 17 + Spring Boot)**

* **Microsserviço A (Order-Service):** Gerencia a intenção de compra.
* **Microsserviço B (Delivery-Service):** Gerencia a frota e o status da entrega.
* **Banco de Dados:** PostgreSQL (Relacional) para persistência de pedidos.
* **Cache:** Redis (Não-Relacional) para armazenar o status rápido das últimas entregas.

#### **Frontend (React ou Angular)**

* Single Page Application (SPA) consumindo as APIs REST de ambos os serviços.

#### **DevOps & CI/CD (Azure DevOps)**

* Pipeline automatizado para rodar testes unitários e de integração em cada push.

---

### ## 6. Plano de Implementação (Roadmap)

1. **Semana 1: Core & Hexagonal Architecture**
* Setup do projeto com Java 17.
* Criação das entidades de domínio e ports (interfaces).
* Implementação de testes unitários com JUnit e Mockito.


2. **Semana 2: Persistência e Mensageria**
* Implementação dos adapters de banco de dados (Spring Data JPA).
* Configuração de um Broker (Docker) para comunicação assíncrona entre serviços.


3. **Semana 3: Frontend e Monitoramento**
* Desenvolvimento da interface em React/Angular.
* Configuração de métricas e logs estruturados.


4. **Semana 4: Infraestrutura e Deploy**
* Escrita de Dockerfiles e manifestos de Kubernetes.
* Configuração do YAML do Azure Pipeline.



---

### ## 7. Critérios de Aceite para a Entrevista

* O código do domínio não possui `@Autowired` ou outras anotações do Spring.
* Existe cobertura de testes para as regras de negócio.
* É possível subir todo o ambiente (bancos, mensageria e apps) com um único `docker-compose up`.
