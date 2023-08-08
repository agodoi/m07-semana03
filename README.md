# EC2-RDS
Criação de RDS MySQL


# O que vamos fazer nessa aula?

Iremos apresentar os métodos para modelagem de banco de dados relacional, e para isso, este laboratório foi criado para reforçar o conceito de utilização de uma instância de banco de dados gerenciado pela AWS para atender as necessidades de banco de dados relacional.

O Amazon Relational Database Service (Amazon RDS) facilita a configuração, a operação e o escalamento de um banco de dados relacional na nuvem. Ele oferece capacidade econômica e redimensionável enquanto gerencia tarefas demoradas de administração de banco de dados, permitindo que você se concentre nas suas aplicações e nos seus negócios. O Amazon RDS fornece seis opções de mecanismos de banco de dados familiares: Amazon Aurora, Oracle, Microsoft SQL Server, PostgreSQL, MySQL e MariaDB. Mas hoje, vamos focar no **MySQL**

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

**(a)** Dentro do console AWS já logado na sua conta de estudante, pesquise por **VPC** (Virtual Private Cloud) no campo **Pesquisar**.

**(b)** Clique em **Grupo de Segurança**

**- (b.1)** **Security group name** (Nome do grupo de segurança): **DB Security Group** (Grupo de segurança de banco de dados)

**- (b.2)** **Description** (Descrição): **Permit access from Web Security Group** (Permitir acesso do grupo de segurança da Web)

**- (b.3)** **VPC**: Lab VPC (VPC de laboratório)

Agora você adicionará uma regra ao grupo de segurança para permitir solicitações de entrada do banco de dados.

**(c)** No painel **Inbound rules** (Regras de entrada), selecione **Add rule** (Adicionar regra).

No momento, o grupo de segurança não tem regras. Você adicionará uma regra para permitir acesso pelo Web Security Group (Grupo de segurança da Web).

**(d)** Defina as seguintes configurações:

**- (d.1)** **Type**: MySQL/Aurora (3306)

**- (d.2)** **CIDR**, **IP**, **Security Group or Prefix List** (CIDR, IP, grupo de segurança ou lista de prefixos): digite **sg** e selecione Web Security Group (Grupo de segurança da Web)

Isso configura o grupo de segurança de banco de dados para permitir tráfego de entrada na porta 3306 de qualquer instância do EC2 associada ao Web Security Group (Grupo de segurança da Web).

**(e)** Selecione **Create security group**.

Você usará esse grupo de segurança ao executar o banco de dados do Amazon RDS.

# Passo 02 - Criar um grupo de sub-redes de banco de dados

Nesta tarefa, você criará um grupo de sub-redes de banco de dados, que é usado para informar ao RDS quais sub-redes podem ser usadas com o banco de dados. 

Cada grupo de sub-redes de banco de dados requer sub-redes em pelo menos duas zonas de disponibilidade.

**(a)** No menu **Services**, clique em **RDS**.

**(b)** No painel de navegação esquerdo, clique em **Grupos de sub-redes**.

**(c)** Clique em **Create DB Subnet Group** e configure:

**- (c.1)** Name: **DB-Subnet-Group**

**- (c.2)** Description: **Grupo de sub-redes de banco de dados**

**- (c.3)** VPC: **Lab VPC**

**(d)** Role para baixo até a seção Adicionar sub-redes.

**(e)** Expanda a lista de valores em **Zonas de disponibilidade** e selecione as duas primeiras zonas: **us-east-1a** e **us-east-1b**.

**(f)** Expanda a lista de valores em **Sub-redes** e selecione as sub-redes associadas aos intervalos de CIDR **10.0.1.0/24** e **10.0.3.0/24**. Essas sub-redes devem agora ser mostradas na tabela Sub-redes selecionadas.

**(g)** Clique em **Create**.

Você usará esse grupo de sub-redes de banco de dados ao criar o banco de dados na próxima tarefa.


# Passo 03: Criar uma instância de banco de dados do Amazon RDS

Nesta tarefa, você configurará e executará uma instância de banco de dados Multi-AZ do Amazon RDS for MySQL.

As implantações Multi-AZ do Amazon RDS proporcionam disponibilidade e durabilidade melhores para instâncias de banco de dados, o que as torna a solução ideal para cargas de trabalho de banco de dados de produção. Quando você provisiona uma instância de banco de dados Multi-AZ, o Amazon RDS cria automaticamente uma instância de banco de dados principal e replica os dados de maneira síncrona para uma instância de espera em uma zona de disponibilidade (AZ) diferente.

No painel de navegação esquerdo, clique em Databases (Bancos de dados).

**(a)** Clique em **Create database**.

Se aparecer **Switch to the new database creation flow* (alternar para o novo fluxo de criação de banco de dados) na parte superior da tela, clique nele.

**(b)** Selecione  **MySQL**.

**(c)** Em **Settings**, faça:

**- (c.1)** DB instance identifier: lab-db

**- (c.2)** Master username: main

**- (c.3)** Master password: lab-password

**- (c.4)** Confirm password: lab-password

**- (c.5)** Em DB instance size, configure:

**- (c.6)** Selecione **Burstable classes** (includes t classes) (Classes com capacidade de intermitência (incluem classes t)).

**- (c.7)** Selecione **db.t3.micro**

**- (c.8)** Em **Storage**, faça:

**- (c.9)** Storage type: General Purpose (SSD) (Uso geral (SSD))

**- (c.10)** Allocated storage: 20

**- (c.11)** Em Connectivity, configure:

**- (c.12)** Virtual Private Cloud (VPC): Lab VPC

**- (c.13)** Existing VPC security groups, no menu suspenso:

**- (c.14)** Selecione DB Security Group.

**- (c.15)** Desmarque a seleção default (padrão).

**- (c.16)** Expanda  **Additional configuration** e configure:

**- (c.17)** Initial database name: **lab**

**- (c.18)** Desmarque **Enable automatic backups** (Habilitar backups automáticos).

**- (c.19)** Desmarque **Enable Enhanced monitoring** (Habilitar monitoramento aprimorado).

Isso desativará os backups, o que normalmente não é recomendado, mas agilizará a implantação do banco de dados para este laboratório.

**(d)** Clique em **Create database**. Seu banco de dados agora será executado.

Se você receber um erro que menciona **"not authorized to perform: iam:CreateRole"** (não autorizado a executar: iam:CreateRole), desmarque **Enable Enhanced monitoring** (Habilitar monitoramento aprimorado) na etapa anterior.

**(e)** Clique em **lab-db** (clique no próprio link).

Agora você precisará aguardar aproximadamente 4 minutos para que o banco de dados esteja disponível. O processo está implantando um banco de dados em duas zonas de disponibilidade diferentes.

Aguarde até **Info** mudar para **Modifying** (Modificando) ou **Available** (Disponível).

**(d)** Role para baixo até a seção **Connectivity & security** e copie o campo **Endpoint**. Ele será semelhante a: **lab-db.cggq8lhnxvnv.us-west-2.rds.amazonaws.com**

Cole o valor do endpoint em um editor de texto. Você o usará mais tarde no laboratório.
 

# Passo-04: Interagir com seu banco de dados

Nesta tarefa, você abrirá uma aplicação Web em execução no servidor da Web e o configurará para usar o banco de dados.

**(a)** Para copiar o endereço IP de WebServer, clique no menu suspenso **Details** acima destas instruções e, em seguida, clique em **Show**.

**(b)** Abra uma nova guia do navegador Web, cole o endereço IP de WebServer [enter]. A aplicação Web será exibida com informações sobre a instância do EC2.

**(c)** Clique no link RDS na parte superior da página. Agora, você configurará a aplicação para se conectar ao banco de dados. Defina as seguintes configurações:

**- (c.1):** Endpoint: cole o endpoint que você copiou em um editor de texto anteriormente

**- (c.2):** Database: **lab**

**- (c.3):** Username: **main**

**- (c.4):** Password: **lab-password**

**- (c.5):** Clique em **Submit** (Enviar)

Uma mensagem será exibida explicando que a aplicação está executando um comando para copiar informações para o banco de dados. Após alguns segundos, a aplicação exibirá um [Address Book] (Catálogo de endereços).

A aplicação Address Book (Catálogo de endereços) está usando o banco de dados do RDS para armazenar informações.

**(d)** Adicione, edite e remova contatos para testar o aplicativo web.

Os dados estão sendo mantidos no banco de dados e são replicados automaticamente para a segunda zona de disponibilidade.


# Laboratório concluído
Parabéns! Você concluiu o laboratório.
