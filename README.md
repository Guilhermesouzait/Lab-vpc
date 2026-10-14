# Lab-vpc

# ☁️ Projeto AWS: Criação de VPC e Servidor Web EC2
<img width="1441" height="593" alt="Imagem do ChatGPT 14 de out  de 2026, 13_28_37" src="https://github.com/user-attachments/assets/8b66fdad-a7d5-43fd-95bc-abc8c653e004" />

## 📌 Sobre o projeto

Este projeto faz parte dos meus estudos em **Amazon Web Services (AWS)** e tem como objetivo criar uma infraestrutura de rede personalizada utilizando o Amazon VPC e hospedar um servidor web em uma instância Amazon EC2.

Durante o laboratório, foram configuradas sub-redes públicas e privadas, tabelas de rotas, regras de segurança e uma instância EC2 preparada para executar um servidor web Apache com PHP.

O projeto foi desenvolvido com foco no aprendizado prático de computação em nuvem, redes e infraestrutura como serviço (IaaS).

## 🎯 Objetivos

- Criar uma VPC personalizada na AWS.
- Configurar sub-redes públicas e privadas.
- Organizar a infraestrutura em duas Zonas de Disponibilidade.
- Configurar tabelas de rotas e um Internet Gateway.
- Compreender a função de um NAT Gateway.
- Criar um grupo de segurança para permitir acesso HTTP.
- Provisionar uma instância Amazon EC2.
- Instalar e configurar um servidor web Apache.
- Publicar uma aplicação web e acessá-la pelo navegador.

## 🛠️ Tecnologias e serviços utilizados

| Serviço ou tecnologia | Finalidade |
|---|---|
| Amazon VPC | Criação da rede virtual |
| Amazon EC2 | Hospedagem do servidor web |
| Sub-redes | Organização da rede pública e privada |
| Internet Gateway | Comunicação entre a VPC e a internet |
| NAT Gateway | Saída para a internet a partir de sub-redes privadas |
| Route Tables | Definição dos caminhos do tráfego de rede |
| Security Groups | Controle do tráfego de entrada e saída |
| Amazon Linux 2 | Sistema operacional da instância |
| Apache HTTP Server | Serviço responsável por disponibilizar o site |
| PHP | Suporte à execução de aplicações PHP |
| Bash | Automação da instalação e configuração do servidor |

## 🏗️ Arquitetura do projeto

A infraestrutura foi planejada com uma VPC personalizada, quatro sub-redes distribuídas em duas Zonas de Disponibilidade e uma instância EC2 hospedada em uma sub-rede pública.

**Configuração da rede:**

- **VPC:** `Lab VPC`
- **Bloco CIDR da VPC:** `10.0.0.0/16`
- **Sub-rede pública 1:** `10.0.0.0/24`
- **Sub-rede privada 1:** `10.0.1.0/24`
- **Sub-rede pública 2:** `10.0.2.0/24`
- **Sub-rede privada 2:** `10.0.3.0/24`
- **Instância EC2:** `Web Server 1`
- **Tipo de instância:** `t3.micro`
- **Sistema operacional:** Amazon Linux 2
- **Servidor web:** Apache HTTP Server
- **Grupo de segurança:** `Web Security Group`

A instância EC2 foi configurada na segunda sub-rede pública, com atribuição de IP público habilitada.

## 📋 Etapas de implementação

### 1. Criação da VPC

No console de gerenciamento da AWS, acessei o serviço Amazon VPC e criei uma rede virtual com o bloco CIDR `10.0.0.0/16`.

Também foram configurados um Internet Gateway, sub-redes públicas e privadas e tabelas de rotas.

### 2. Criação das sub-redes adicionais

Foram criadas duas sub-redes adicionais para ampliar a infraestrutura:

- `Public Subnet 2`: `10.0.2.0/24`
- `Private Subnet 2`: `10.0.3.0/24`

Essas sub-redes foram associadas às tabelas de rotas correspondentes, conforme a arquitetura planejada.

### 3. Configuração das tabelas de rotas

As tabelas de rotas foram configuradas para atender às sub-redes públicas e privadas.

A tabela pública utiliza uma rota para o Internet Gateway, permitindo conectividade com a internet quando as demais configurações necessárias estão presentes.

A tabela privada utiliza a configuração de roteamento destinada às sub-redes privadas, podendo utilizar um NAT Gateway para permitir conexões de saída com a internet.

### 4. Criação do grupo de segurança

Foi criado o grupo de segurança `Web Security Group`, responsável por controlar o tráfego da instância EC2.

A regra de entrada configurada foi:

| Tipo | Protocolo | Porta | Origem |
|---|---|---|---|
| HTTP | TCP | 80 | `0.0.0.0/0` |

Essa regra permite que navegadores na internet acessem o servidor web pela porta HTTP.

**Observação de segurança:** em ambientes de produção, as regras devem ser limitadas conforme a necessidade da aplicação. O acesso SSH, quando necessário, deve ser autorizado separadamente e restrito a origens confiáveis.

### 5. Criação da instância EC2

Foi criada a instância `Web Server 1` com as seguintes configurações:

- Imagem: Amazon Linux 2 AMI.
- Tipo: `t3.micro`.
- Par de chaves: `vockey`, disponibilizado pelo ambiente do laboratório.
- Rede: `Lab VPC`.
- Sub-rede: `Public Subnet 2`.
- IP público: habilitado.
- Grupo de segurança: `Web Security Group`.

### 6. Instalação e configuração do servidor web

Para automatizar a configuração inicial da instância, utilizei o campo **Dados do usuário (User Data)** do Amazon EC2.

O script Bash abaixo instala as dependências, baixa os arquivos da aplicação e inicia o Apache automaticamente.

```bash
#!/bin/bash
dnf install -y httpd
systemctl enable --now httpd

dnf install -y php php-mysqlnd wget unzip

cd /tmp
wget -O lab-app.zip https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-100-RESTRT-1/267-lab-NF-build-vpc-web-server/s3/lab-app.zip

unzip -o lab-app.zip -d /var/www/html/
chown -R apache:apache /var/www/html/

systemctl restart httpd

```

**Importante:** o script foi preparado para o Amazon Linux 2 utilizado no laboratório. A execução depende do acesso da instância ao repositório de pacotes e ao endereço de download. O comando `yum install` instala os pacotes; já `systemctl enable --now httpd` também poderia ser utilizado para habilitar e iniciar o serviço em um único comando.

### 7. Teste de acesso ao servidor web

Após iniciar a instância, aguardei a conclusão das verificações de status do EC2.

Em seguida:

1. Acessei os detalhes da instância.
2. Copiei o DNS IPv4 público.
3. Abri uma nova aba do navegador.
4. Acessei o endereço utilizando HTTP.

<img width="1275" height="560" alt="Imagem do ChatGPT 14 de out  de 2026, 13_28_31" src="https://github.com/user-attachments/assets/a16dcff0-2daa-4b7f-be80-6ba294a9c277" />

<img width="1357" height="672" alt="Captura de tela 2026-10-14 132559" src="https://github.com/user-attachments/assets/7a336244-ff44-45f6-b056-7e9734d8b3df" />


Exemplo de acesso:

```text
http://DNS-PUBLICO-DA-INSTANCIA
```

O teste permite verificar se o Apache está funcionando e se as regras de rede permitem o acesso à aplicação.

## 📚 Principais aprendizados

Com a execução deste laboratório, pratiquei conceitos importantes de infraestrutura em nuvem:

- Criação e organização de redes virtuais na AWS.
- Planejamento de endereçamento IPv4 com blocos CIDR.
- Diferenças entre sub-redes públicas e privadas.
- Configuração de tabelas de rotas e gateways.
- Aplicação de regras de segurança em instâncias EC2.
- Provisionamento de servidores Linux na nuvem.
- Automatização da configuração de servidores com Bash e User Data.
- Publicação e validação de aplicações web por meio de um endereço público.

## 🚀 Possíveis melhorias futuras

Como evolução deste projeto, pretendo aprofundar os estudos em:

- Configuração de acesso seguro por SSH.
- Implementação de HTTPS com certificado TLS.
- Uso de Application Load Balancer.
- Escalabilidade com Auto Scaling.
- Monitoramento com Amazon CloudWatch.
- Gerenciamento da infraestrutura com Terraform.
- Aplicação de boas práticas de segurança e redução de custos.

## 💰 Observação sobre custos

O laboratório pode criar recursos cobrados na AWS, especialmente o NAT Gateway, além de outros recursos que ultrapassem as condições gratuitas aplicáveis à conta.

Ao finalizar os estudos, é importante verificar os recursos criados e removê-los quando não forem mais necessários, seguindo as orientações do ambiente de laboratório.

## 👨‍💻 Autor

**Guilherme Alexandre**

Projeto desenvolvido para fins educacionais, como parte dos meus estudos em computação em nuvem e Amazon Web Services (AWS).

---

⭐ Este repositório registra minha evolução prática em AWS, redes, Linux e administração de infraestrutura em nuvem.
<img width="1441" height="593" alt="Imagem do ChatGPT 14 de out  de 2026, 13_28_37" src="https://github.com/user-attachments/assets/f48348cf-57cf-44f6-b266-c924b216cbc9" />
