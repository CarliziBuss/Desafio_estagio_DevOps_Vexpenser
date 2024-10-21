<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
       
<body>
    <h1>Desafio VExpenses |Estágio DevOps </h1>

<h1 align="center">Tarefa 1</h1>
<h2>Descrição Técnica do Código Recebido:</h2>
<p>O código recebido para análise em <strong>Terraform</strong> monta toda a infraestrutura básica na <strong>AWS</strong> para rodar uma instância EC2 com o sistema operacional Debian 12. Ele cuida de tudo, desde a criação da rede até as permissões de acesso.
  
<h3>1. Provedor AWS</h3>
<p>O bloco provider "aws" define que o provedor utilizado será a AWS, e a região selecionada é us-east-1.
  Primeiro, o código define que vamos usar a AWS como provedor de nuvem. A região escolhida é <strong>us-east-1</strong>.</p>

<h3>2. Variáveis</h3>
<ul>
    <li><code>variable "projeto"</code>: Define o nome do projeto (padrão: "VExpenses").</li>
    <li><code>variable "candidato"</code>: Define o nome do candidato (padrão: "SeuNome").</li>
</ul>
<p>Essas variáveis são usadas para dar nome aos recursos automaticamente.</p>

<h3>3. Par de Chaves (Key Pair)</h3>
<p>Essa parte gera um par de chaves para acesso à instância EC2:</p>
<ul>
    <li><code>resource "tls_private_key" "ec2_key"</code></strong>: Cria uma chave privada RSA de 2048 bits.</li>
    <li><code>resource "aws_key_pair" "ec2_key_pair"</code>: Cria um par de chaves na AWS com base na chave pública gerada.</li>
</ul>

<h3>4. VPC (Rede Privada Virtual)</h3>
<ul>
    <li><code>resource "aws_vpc" "main_vpc"</code>: Cria uma rede virtual isolada(VPC) com o bloco de IPs<code>10.0.0.0/16</code></li>.
</ul>

<h3>5. Sub-rede (Subnet)</h3>
<ul>
    <li><code>resource "aws_subnet" "main_subnet"</code>: Cria uma sub-rede com o bloco de IPs <code>10.0.1.0/24</code>, localizada na zona de disponibilidade <strong>us-east-1a</strong></li>
</ul>

<h3>6. Internet Gateway</h3>
<ul> 
    <li><code>resource "aws_internet_gateway" "main_igw"</code>: Cria um gateway de internet que permite que os recursos dentro da VPC se conectem à internet.</li>
</ul>

<h3>7. Tabela de Rotas</h3>
<ul>
    <li><code>resource "aws_route_table" "main_route_table"</code>: Define uma tabela de rotas que direciona o tráfego da rede para o internet gateway, permitindo que os recursos na sub-rede tenham acesso à internet.</li>
</ul>

<h3>8. Grupo de Segurança (Security Group)</h3>
<ul> 
    <li><code>resource "aws_security_group" "main_sg"</code>:Cria um grupo de segurança com as seguintes regras: </li> 
</ul>
  <ol>
    <li><strong>Ingress:</strong> Permite conexões SSH (porta 22) de qualquer origem.</li>
        <li><strong>Egress:</strong> Permite todo o tráfego de saída.</li>
  </ol>

<h3>9. AMI (Amazon Machine Image)</h3>
<ul>
    <li><code> data "aws_ami" "debian12"</code>:Encontra a imagem mais recente do Debian 12 na AWS, que será usada para criar a instância EC2. </li>
</ul>

<h3>10. Instância EC2</h3>
<ul>
    <li><code>resource "aws_instance" "debian_ec2"</code>: Cria uma instância EC2 do tipo <code>t2.micro</code>usando a imagem Debian 12.
    <li><code>user_data</code><strong>Script de inicialização</strong>: Atualiza o sistema operacional automaticamente logo após a criação da instância.</li>
    <li><code>associate_public_ip_address</code><strong>IP Público:</strong> A instância recebe um IP público para permitir o acesso via internet.</li>
</ul>

<h3>11. Saídas (Outputs)</h3>
<ul>
    <li><code>output "private_key"</code>:Exibe a chave privada que será utilizada para acessar a instância via SSH.</li>
    <li><code>output "ec2_public_ip"</code>: Exibe o IP público da instância EC2, facilitando o acesso remoto.</li>
</ul>


<h1 align="center">Tarefa 2</h1>


<h2>Documentação Completa</h2>
<p>
    Este projeto foi desenvolvido utilizando <strong>Terraform</strong> para criar e gerenciar uma infraestrutura automatizada na AWS. O objetivo principal foi provisionar uma instância EC2 com melhorias focadas em automação, segurança e escalabilidade. Abaixo, descrevo as melhorias implementadas e o raciocínio por trás de cada uma, além das instruções para que você possa reproduzir o ambiente.
</p>

<h3>Melhorias Implementadas</h3>

<h4>1. Automação da Instalação do Nginx</h4>
<p>
    Um dos primeiros ajustes foi adicionar um script no <code>user_data</code> da instância EC2 para que o Nginx seja instalado e iniciado automaticamente logo após a criação da instância. Isso foi feito diretamente no código Terraform, garantindo que o servidor web esteja pronto para uso sem que seja necessário fazer login na instância para configurá-lo manualmente.
</p>
<p><strong>Por que isso é útil?</strong> Automatizar esse processo ajuda a economizar tempo e evita a necessidade de intervenção manual logo após o provisionamento. Além disso, esse tipo de automação é essencial para ambientes de produção, onde a consistência e rapidez são importantes.</p>

<h4>2. Reforço na Segurança</h4>
<p>
    O grupo de segurança da EC2 foi configurado para permitir apenas tráfego de SSH (porta 22) e bloquear todas as outras portas para acessos de entrada, mantendo todas as saídas liberadas. Também criei um par de chaves SSH que será usado para acessar a instância com segurança.
</p>
<p><strong>Por que isso é importante?</strong> Limitar o tráfego de entrada a apenas o necessário (neste caso, SSH) minimiza as superfícies de ataque, aumentando a segurança do servidor. O uso de chaves SSH, em vez de senhas, garante uma camada extra de proteção contra acessos não autorizados.</p>

<h4>3. Grupos de Dimensionamento Automático (Auto Scaling)</h4>
<p>
    Para garantir que o ambiente possa se ajustar automaticamente conforme o uso, configurei um <strong>Auto Scaling Group (ASG)</strong>. Ele aumenta ou reduz a quantidade de instâncias EC2 com base no uso de CPU.
</p>
<p><strong>Vantagens:</strong> Essa configuração é essencial para ambientes com variações de demanda. Quando o tráfego ou a carga de trabalho aumenta, novas instâncias são automaticamente criadas. Quando a demanda diminui, as instâncias extras são removidas, ajudando a reduzir custos.</p>

<h4>4. Balanceamento de Carga</h4>
<p>
    Também configurei um <strong>Load Balancer</strong> para distribuir o tráfego entre as instâncias EC2. O Load Balancer assegura que, conforme mais instâncias EC2 são criadas ou removidas pelo Auto Scaling, o tráfego seja distribuído de maneira eficiente e uniforme.
</p>
<p><strong>Por que é essencial?</strong> O Load Balancer garante que o sistema continue respondendo adequadamente mesmo sob carga elevada. Isso aumenta a disponibilidade e confiabilidade da aplicação.</p>

<h2>Instruções de Uso</h2>

<h3>Pré-requisitos</h3>
<ul>
    <li>Uma conta AWS com permissões para criar recursos como EC2, VPC, Auto Scaling e Load Balancer.</li>
    <li>Terraform instalado em sua máquina (<a href="https://learn.hashicorp.com/tutorials/terraform/install-cli" target="_blank">guia de instalação</a>).</li>
    <li>Chave SSH gerada localmente para acessar a instância EC2.</li>
</ul>

<h3>Passos para Configuração</h3>

<ol>
    <li><strong>Clone o repositório:</strong></li>
    <pre><code>git clone https://github.com/seu-usuario/seu-repositorio.git
cd seu-repositorio</code></pre>

    <li><strong>Inicialize o Terraform:</strong></li>
    <p>Execute o comando a seguir para inicializar o ambiente Terraform:</p>
    <pre><code>terraform init</code></pre>

    <li><strong>Verifique o plano de execução:</strong></li>
    <p>Antes de aplicar as configurações, verifique o plano com o comando abaixo:</p>
    <pre><code>terraform plan</code></pre>

    <li><strong>Aplique as configurações:</strong></li>
    <p>Agora, crie a infraestrutura rodando o comando:</p>
    <pre><code>terraform apply</code></pre>
    <p>Ao ser solicitado, confirme digitando <code>yes</code>.</p>

    <li><strong>Acesse a instância EC2 via SSH:</strong></li>
    <p>Depois que a instância for criada, você pode acessá-la via SSH com o seguinte comando:</p>
    <pre><code>ssh -i path/to/your/key.pem ec2-user@<endereço-ip-público></code></pre>

    <li><strong>Verifique o Auto Scaling e Load Balancer:</strong></li>
    <p>O Auto Scaling e o Load Balancer já estarão configurados e operando, ajustando o número de instâncias EC2 e gerenciando o tráfego automaticamente.</p>
</ol>

<h3>Comandos Adicionais</h3>
<ul>
    <li><strong>Para destruir a infraestrutura:</strong></li>
    <p>Se você quiser remover todos os recursos criados, execute o seguinte comando:</p>
    <pre><code>terraform destroy</code></pre>
</ul>

<h2>Resultados Esperados</h2>
<p>
    Ao aplicar estas configurações, você terá a seguinte infraestrutura funcionando:
</p>
<ul>
    <li>Instância EC2 com o servidor Nginx rodando automaticamente.</li>
    <li>Grupo de segurança configurado para permitir acesso apenas via SSH.</li>
    <li>Auto Scaling ajustando o número de instâncias conforme a demanda de uso da CPU.</li>
    <li>Load Balancer gerenciando o tráfego e garantindo alta disponibilidade.</li>
</ul>
<p>
    Com essas configurações, esperamos um ambiente seguro, automatizado e escalável, pronto para ser usado em produção ou ambientes de teste com alta demanda.
</p>



