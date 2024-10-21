<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Desafio VExpenses | Estágio DevOps</title>
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

<p>
    Este projeto usa <strong>Terraform</strong> para criar uma infraestrutura na AWS. Abaixo estão as instruções detalhadas para reproduzir o ambiente configurado e as melhorias implementadas para otimizar segurança, automação e escalabilidade.
</p>

<h3>Melhorias Implementadas</h3>

<h4>1. Automação da Instalação do Nginx</h4>
<p>
    Adicionei um script no campo <code>user_data</code> da instância EC2, o qual instala e inicia automaticamente o servidor Nginx assim que a instância é criada.
</p>
<p><strong>Motivo:</strong> Essa automação garante que o servidor Nginx esteja pronto para uso logo após a criação da instância, economizando tempo e evitando a necessidade de configurações manuais.</p>

<h4>2. Medidas de Segurança</h4>
<p>
    Configurei um grupo de segurança que permite apenas o tráfego essencial: SSH liberado para qualquer IP (para acesso remoto) e saída irrestrita. Regras específicas de portas foram aplicadas para limitar o tráfego.
</p>
<p><strong>Motivo:</strong> Essa abordagem melhora a segurança da instância EC2, garantindo que ela esteja protegida contra acessos indevidos, mantendo apenas as portas estritamente necessárias abertas.</p>

<h4>3. Grupos de Dimensionamento Automático (Auto Scaling)</h4>
<p>
    Foi implementado um <strong>Auto Scaling Group (ASG)</strong> que ajusta automaticamente o número de instâncias EC2 de acordo com a demanda, baseado no uso da CPU.
</p>
<p><strong>Motivo:</strong> O uso de ASG permite que a infraestrutura responda a picos de uso, mantendo o sistema escalável e econômico ao reduzir as instâncias durante períodos de baixa demanda.</p>

<h4>4. Balanceador de Carga (Load Balancer)</h4>
<p>
    Um <strong>Load Balancer</strong> foi configurado para distribuir o tráfego entre as instâncias EC2, assegurando alta disponibilidade e balanceamento de carga eficiente.
</p>
<p><strong>Motivo:</strong> O Load Balancer garante que o tráfego seja distribuído de forma uniforme entre as instâncias, evitando sobrecarga em um único servidor e aumentando a resiliência do ambiente.</p>

<h2>Instruções de Uso</h2>

<h3>Pré-requisitos</h3>
<ul>
    <li>Conta AWS com permissões para criar instâncias EC2, VPCs, Auto Scaling e Load Balancer.</li>
    <li>Terraform instalado na sua máquina (siga <a href="https://learn.hashicorp.com/tutorials/terraform/install-cli" target="_blank">essas instruções</a> para instalação).</li>
    <li>Uma chave SSH gerada no seu sistema para acesso às instâncias EC2.</li>
</ul>

<h3>Passos para Configuração</h3>

<ol>
    <li><strong>Clone o repositório:</strong></li>
    <pre><code>git clone https://github.com/seu-usuario/seu-repositorio.git
cd seu-repositorio</code></pre>

    <li><strong>Inicialize o Terraform:</strong></li>
    <p>Primeiro, execute o comando de inicialização do Terraform:</p>
    <pre><code>terraform init</code></pre>

    <li><strong>Verifique o plano de execução:</strong></li>
    <p>Use o comando abaixo para visualizar o plano do que será criado:</p>
    <pre><code>terraform plan</code></pre>

    <li><strong>Aplique as configurações:</strong></li>
    <p>Para criar a infraestrutura na AWS, execute:</p>
    <pre><code>terraform apply</code></pre>
    <p>O Terraform pedirá uma confirmação antes de prosseguir. Digite <code>yes</code> para continuar.</p>

    <li><strong>Acesse a instância EC2:</strong></li>
    <p>Assim que a instância for criada, use o comando abaixo para acessá-la via SSH:</p>
    <pre><code>ssh -i path/to/your/key.pem ec2-user@<endereço-ip-público></code></pre>

    <li><strong>Verifique Auto Scaling e Load Balancer:</strong></li>
    <p>O Auto Scaling e o Load Balancer estarão configurados para ajustar e distribuir o tráfego automaticamente entre as instâncias EC2.</p>
</ol>

<h3>Comandos Úteis</h3>
<ul>
    <li><strong>Destruir a infraestrutura:</strong></li>
    <p>Se quiser remover todos os recursos criados, execute:</p>
    <pre><code>terraform destroy</code></pre>
</ul>

<h2>Resultados Esperados</h2>
<p>
    Após seguir as instruções, a infraestrutura provisionada incluirá:
</p>
<ul>
    <li>Instância EC2 com Nginx instalado e em funcionamento.</li>
    <li>Grupo de segurança com regras de tráfego apropriadas.</li>
    <li>Auto Scaling ajustando a quantidade de instâncias EC2 conforme a demanda.</li>
    <li>Balanceador de carga distribuindo tráfego de forma eficiente entre as instâncias.</li>
</ul>
<p>
    O objetivo dessas melhorias é garantir uma infraestrutura escalável, segura e eficiente, pronta para atender às demandas de produção.
</p>
</body>


