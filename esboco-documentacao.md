# Esboço de Documentação do Template-Pipeline

## Introdução
Este documento descreve o que foi construído no template-pipeline, de forma passo a passo e em linguagem natural, sem entrar em detalhes técnicos profundos.

## Passo 1: Criação da Pipeline Reutilizável
Foi criada uma pipeline no método reutilizável do GitHub Actions, chamada "docker-terraform-pipeline". Essa pipeline pode ser chamada de outros repositórios, permitindo a implantação automatizada de aplicações. Ela inclui uma opção para provisionar infraestrutura adicional, como EC2, através de um parâmetro de entrada chamado "infra".

## Passo 2: Estrutura Geral da Pipeline
A pipeline é dividida em dois jobs principais:
- **Job INFRA**: Executado apenas se a opção "infra" for ativada. Este job cuida da infraestrutura EC2, aplicando e destruindo recursos conforme necessário.
- **Job DEV**: Sempre executado. Este job constrói a aplicação, prepara o repositório de imagens, constrói e envia o contêiner Docker, e implanta a aplicação no serviço ECS.

## Passo 3: Configurações Iniciais
Para cada job, foram adicionados passos iniciais para:
- Fazer checkout do código do repositório principal.
- Fazer checkout dos repositórios auxiliares: "templates-pipeline" (que contém as ações reutilizáveis) e "iac" (que contém os códigos Terraform para infraestrutura).
- Configurar credenciais da AWS para permitir acesso aos serviços necessários.

## Passo 4: Criação das Ações Reutilizáveis
Foram desenvolvidas várias ações reutilizáveis, organizadas em pastas, para modularizar as tarefas:

### Ação para Construção do Node.js
Foi criada uma ação simples para preparar o projeto Node.js, executando a instalação de dependências via npm. Isso garante que a aplicação esteja pronta para ser empacotada em um contêiner.

### Ação para Construção e Envio do Docker
Foi criada uma ação para configurar o ambiente Docker, fazer login no Amazon ECR (repositório de imagens), construir a imagem Docker da aplicação e enviá-la para o ECR com uma tag baseada no commit atual.

### Ações para Infraestrutura EC2
Foram criadas duas ações para gerenciar a infraestrutura EC2 usando Terraform:
- Uma para aplicar (provisionar) os recursos EC2, incluindo configuração de backend remoto, importação automática de recursos existentes e aplicação das mudanças.
- Uma para destruir os recursos EC2, removendo a infraestrutura quando necessário.

### Ação para Repositório ECR
Foi criada uma ação para gerenciar o repositório de imagens no ECR usando Terraform. Ela verifica se o repositório já existe, importa no estado do Terraform se necessário, e aplica as configurações para garantir que o repositório esteja disponível.

### Ação para Implantação no ECS
Foi criada uma ação para implantar a aplicação no Amazon ECS usando Terraform. Inclui configuração de backend, importação automática de recursos existentes (como roles IAM, grupos de segurança, balanceador de carga, etc.), e aplicação das mudanças para colocar a aplicação em produção.

## Passo 5: Integração e Fluxo Final
O workflow principal integra todas essas ações em sequência lógica: primeiro constrói a aplicação, depois prepara o ECR, constrói e envia o Docker, e finalmente implanta no ECS. Se a infraestrutura EC2 for necessária, ela é provisionada no job separado. Tudo é executado em um ambiente Ubuntu, com permissões adequadas para acessar repositórios e AWS.

Este template permite uma implantação completa e automatizada de aplicações containerizadas na AWS, facilitando o processo para diferentes projetos.</content>
<parameter name="filePath">/home/guilherme/POCS/teddy/templates-pipeline/esboco-documentacao.md