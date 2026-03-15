

# 🧠 Life OS: Plataforma de Inteligência Pessoal e Financeira

Um ecossistema fullstack baseado em arquitetura de microsserviços, projetado para unificar a gestão de finanças pessoais, estudos, rotinas e saúde. O sistema vai além do registro básico, cruzando dados cotidianos com indicadores macroeconômicos (consumidos via APIs governamentais) para gerar *insights* inteligentes através de fluxos automatizados com IA.

## 🏗️ Stack Tecnológica e Decisões de Arquitetura

Este projeto foi construído priorizando escalabilidade, resiliência e a separação clara de domínios (Domain-Driven Design). Abaixo estão as tecnologias escolhidas e o raciocínio arquitetural por trás de cada uma.

### ⚙️ Backend & API Gateway
* **Java 21 + Quarkus:** Utilizado como o coração de todos os microsserviços e do API Gateway (gerenciado via Maven).
  * **Onde uso:** No desenvolvimento dos domínios isolados (`Finance`, `Routine`, `Economic`, `Health`, `User`) e no roteamento de entrada.
  * **Por que escolhi:** O Quarkus oferece inicialização ultrarrápida e baixo consumo de memória (tecnologia *container-first*), o que é vital para rodar múltiplos microsserviços simultaneamente sem esgotar os recursos da máquina. Ele também brilha na integração nativa com mensageria e OIDC.

### 🎨 Frontend
* **React:** Interface de usuário da aplicação.
  * **Onde uso:** Construção dos dashboards interativos, telas de Kanban para gestão de tarefas e gráficos comparativos de rentabilidade e inflação.
  * **Por que escolhi:** Seu modelo baseado em componentes reutilizáveis é perfeito para uma aplicação modular. Permite criar interfaces altamente reativas, essenciais para visualizar grandes volumes de dados de carteiras de investimentos e métricas de produtividade em tempo real.

### 🛡️ Segurança e Gestão de Identidade (IAM)
* **Keycloak:** Servidor de Autenticação e Autorização Open Source.
  * **Onde uso:** Na proteção das rotas do API Gateway e gestão do ciclo de vida do usuário (Login, Cadastro, Roles).
  * **Por que escolhi:** Para não reinventar a roda da segurança. O Keycloak implementa o padrão de mercado OIDC (OpenID Connect) e gera tokens JWT assinados digitalmente. Isso delega a complexidade da criptografia de senhas para um serviço especializado, mantendo os microsserviços *stateless* e seguros.

### 🗄️ Persistência de Dados (Poliglota)
A arquitetura utiliza o padrão *Database per Service* (um banco para cada microsserviço), escolhendo a melhor ferramenta para cada tipo de dado.

* **PostgreSQL (Banco Relacional):**
  * **Onde uso:** Nos microsserviços de `Finance` (transações e carteira de ativos como CDI, Tesouro IPCA+ e FIIs), `User` (dados cadastrais) e `Routine` (tarefas diárias).
  * **Por que escolhi:** Garante integridade absoluta através das propriedades ACID. Transações financeiras e relacionamentos rígidos exigem tabelas estruturadas e previsíveis.
* **MongoDB (Banco NoSQL / Orientado a Documentos):**
  * **Onde uso:** Nos microsserviços de `Health` (logs de treinos flexíveis) e `Economic` (armazenamento de séries temporais históricas de taxas e inflação).
  * **Por que escolhi:** A ausência de um esquema rígido (schema-less) permite que um exercício aeróbico tenha atributos completamente diferentes de um exercício de força no mesmo banco de dados, eliminando a necessidade de *JOINs* complexos e lentos.

### ⚡ Performance e Comunicação Assíncrona
* **Redis (Cache em Memória):**
  * **Onde uso:** No microsserviço `Economic` utilizando o padrão *Cache-Aside*.
  * **Por que escolhi:** Evita gargalos ao consumir a API externa do Banco Central. A taxa do dia é buscada uma única vez, armazenada no Redis e servida em milissegundos para os dashboards, garantindo que o sistema não falhe se a API do governo ficar instável.
* **RabbitMQ (Message Broker):**
  * **Onde uso:** Como um "Event Bus" para comunicação assíncrona entre os microsserviços.
  * **Por que escolhi:** Promove o baixo acoplamento. Se um usuário altera um dado ou exclui a conta, o serviço `User` publica um evento no RabbitMQ. Os outros serviços consomem essa mensagem e se atualizam em *background*, garantindo Consistência Eventual sem travar a experiência do usuário (requisições REST síncronas).

### 🤖 Automação e IA
* **n8n:** Ferramenta de automação de *workflows*.
  * **Onde uso:** Integrado ao microsserviço `Economic`.
  * **Por que escolhi:** Atua como o "cérebro" assíncrono do sistema. Ele consome os dados de preços pessoais cadastrados pelo usuário, cruza com o IPCA oficial via API do BCB e utiliza Inteligência Artificial para gerar recomendações textuais sobre perda de poder de compra ou estratégias de investimento.