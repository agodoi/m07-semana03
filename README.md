# EC2-RDS
Criação de RDS MySQL


# O que vamos fazer nessa aula?

Iremos apresentar os métodos para modelagem de banco de dados relacional, apresentando estudos de caso e realizando alguns exemplos de modelagem. 

Os alunos irão fazer a modelagem do banco de dados do projeto, desenvolver localmente no MySQL e implantar na nuvem no AWS RDS.

Este laboratório foi criado para reforçar o conceito de utilização de uma instância de banco de dados gerenciado pela AWS para atender as necessidades de banco de dados relacional.

O Amazon Relational Database Service (Amazon RDS) facilita configurar, operar e escalar um banco de dados relacional na nuvem. Ele oferece capacidade econômica e redimensionável enquanto gerencia tarefas demoradas de administração de banco de dados, permitindo que você se concentre nas suas aplicações e nos seus negócios. O Amazon RDS fornece seis opções de mecanismos de banco de dados familiares: Amazon Aurora, Oracle, Microsoft SQL Server, PostgreSQL, MySQL e MariaDB.

# Objetivos

Depois de concluir este laboratório, você será capaz de:

* Executar uma instância de banco de dados do Amazon RDS com alta disponibilidade.
* Configurar a instância de banco de dados para permitir conexões do seu servidor web.
* Abrir um aplicativo web e interagir com seu banco de dados.

# Arquitetura inicial

<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/EC2-RDS/blob/main/imgs/lab-5-starting-lab-architecture.png">
   <img alt="Região e Zonas AWS" src="[YOUR-DEFAULT-IMAGE](https://github.com/agodoi/EC2-RDS/blob/main/imgs/lab-5-starting-lab-architecture.png)">
</picture>

# Arquitetura final

<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/EC2-RDS/blob/main/imgs/lab-5-final-lab-architecture.png">
   <img alt="Região e Zonas AWS" src="[YOUR-DEFAULT-IMAGE](https://github.com/agodoi/EC2-RDS/blob/main/imgs/lab-5-final-lab-architecture.png)">
</picture>

# Passo-01: Criar um grupo de segurança no RDS

a) Dentro do console AWS já logado na sua conta de estudante, pesquise por **VPC** (Virtual Private Cloud) no campo **Pesquisar**.

b) Clique em **Grupo de Segurança**

   b.1) **Security group name** (Nome do grupo de segurança): **DB Security Group** (Grupo de segurança de banco de dados)

   b.2) **Description** (Descrição): **Permit access from Web Security Group** (Permitir acesso do grupo de segurança da Web)

   b.3) **VPC**: Lab VPC (VPC de laboratório)

Agora você adicionará uma regra ao grupo de segurança para permitir solicitações de entrada do banco de dados.

No painel **Inbound rules** (Regras de entrada), selecione **Add rule** (Adicionar regra).

No momento, o grupo de segurança não tem regras. Você adicionará uma regra para permitir acesso pelo Web Security Group (Grupo de segurança da Web).

c) Defina as seguintes configurações:

c.1) **Type** (Tipo): MySQL/Aurora (3306)

c.2) **CIDR**, **IP**, **Security Group or Prefix List** (CIDR, IP, grupo de segurança ou lista de prefixos): digite **sg** e selecione Web Security Group (Grupo de segurança da Web)

Isso configura o grupo de segurança de banco de dados para permitir tráfego de entrada na porta 3306 de qualquer instância do EC2 associada ao Web Security Group (Grupo de segurança da Web).

d) Selecione **Create security group** (Criar grupo de segurança).
