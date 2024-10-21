<!DOCTYPE html>
<html lang="pt-BR">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Desafio VExpenses| Estágio DevOps</title>
        <style>
            body {
                font-family: Arial, sans-serif;
                margin: 20px;
                line-height: 1.6;
                background-color: #f4f4f4;
            
            }
       
            h1 {
                color: #058eff;
                text-transform: uppercase;
             

            }
            h2 {
                color: #666;
            }

            code {
                background-color: #eaeaea;
                padding: 2px 4px;
                border-radius: 4px;
            }
            pre {
                background-color: #eaeaea;
                padding: 10px;
                border-radius: 4px;
                overflow-x: auto;
            }
        </style>
    </head>
<body>
    <h1 style="font-size: 36px; font-family: Arial, sans-serif; color: yellow; text-align: center; margin: 50px; padding: 20px; background-color: #007BFF; border-radius: 10px; box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.2);">
        <span style="color: rgb(255, 255, 255);">Desafio VExpenses |</span> <span style="color: yellow;">Estágio DevOps</span>
    </h1>

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

    <h2>Modificação e Melhoria do Código</h2>
    <p>Objetivo: implementar melhorias na infraestrutura de TI utilizando AWS e Terraform.</p>

    <h2>Melhorias Implementadas</h2>
    <ul>
        <li><strong>Segurança Aprimorada:</strong> Limitação do acesso SSH a um IP específic, reduzindo a exposição ao risco.</li>
        <li><strong>Automação do Nginx:</strong> Configuração automática do servidor Nginx na instância EC2 após sua criação, garantindo que o serviço esteja pronto para uso sem intervenção manual.</li>
        <li><strong>Grupos de Dimensionamento Automático:</strong> Implementação de um grupo de autoescalonamento que ajusta o número de instâncias EC2 entre 1 e 3, com base na demanda.</li>
        <li><strong>Balanceador de Carga:</strong> Integração de um balanceador de carga para distribuir o tráfego entre as instâncias, melhorando a disponibilidade e desempenho da aplicação.</li>
    </ul>

    <h2>Detalhes Técnicos das Alterações</h2>
    <h3>1. Segurança Aprimorada</h3>
    <p>Foi configurado o grupo de segurança para restringir o acesso SSH a um IP específico, garantindo maior proteção contra acessos indesejados.</p>
    
    <h3>2. Automação do Nginx</h3>
    <pre><code>
    user_data = <<-EOF
                #!/bin/bash
                apt-get update -y
                apt-get upgrade -y
                apt-get install nginx -y
                systemctl start nginx
                systemctl enable nginx
                EOF
    </code></pre>

    <h3>3. Grupos de Dimensionamento Automático</h3>
    <p>Um grupo de autoescalonamento foi criado para garantir que a infraestrutura se adapte automaticamente à demanda, ajustando o número de instâncias EC2 conforme necessário.</p>

    <h3>4. Balanceador de Carga</h3>
    <p>Um balanceador de carga foi configurado para gerenciar o tráfego, garantindo que as solicitações sejam distribuídas entre as instâncias em execução.</p>

    <h2>Resultados Esperados</h2>
    <ul>
        <li><strong>Segurança:</strong> A aplicação estará protegida contra acessos indesejados via SSH.</li>
        <li><strong>Escalabilidade:</strong> O grupo de dimensionamento automático permitirá que a aplicação escale conforme a demanda.</li>
        <li><strong>Automação:</strong> O Nginx será instalado e configurado automaticamente.</li>
        <li><strong>Distribuição de Carga:</strong> O balanceador de carga otimizará o desempenho da aplicação.</li>
    </ul>

    <h2>Instruções de Uso</h2>
    <p>Para inicializar e aplicar a configuração Terraform, siga os passos abaixo:</p>
    <ol>
        <li>Certifique-se de que o <strong>Terraform</strong> está instalado em sua máquina. Você pode baixar a versão mais recente do Terraform no <a href="https://www.terraform.io/downloads.html" target="_blank">site oficial do Terraform</a>.</li>
        <li>Configure suas credenciais AWS. Certifique-se de que você tem as permissões necessárias para criar recursos na AWS.</li>
        <li>Clone o repositório e navegue até o diretório do projeto.</li>
        <li>Execute <code>terraform init</code> para inicializar o diretório do Terraform e baixar os provedores necessários.</li>
        <li>Execute <code>terraform plan</code> para visualizar as mudanças que serão aplicadas na infraestrutura.</li>
        <li>Execute <code>terraform apply</code> para criar os recursos na AWS. Você será solicitado a confirmar a aplicação.</li>
    </ol>
    <p>Após a conclusão, você poderá acessar os recursos criados e verificar se a infraestrutura está funcionando conforme o esperado.</p>
</body>
</html>