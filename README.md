Projeto: Deploy de Arquivos HTML para EC2 usando GitHub Actions

Descrição:
Este projeto tem como objetivo automatizar o processo de deploy de arquivos HTML para uma instância EC2 na AWS, utilizando GitHub Actions e SSH (SCP). O deploy será realizado de maneira contínua sempre que houver atualizações no repositório.

Objetivo:
- Configurar um fluxo de trabalho (workflow) no GitHub Actions.
- Fazer upload de arquivos HTML para uma instância EC2 na AWS usando SSH.
- Automatizar o processo para facilitar o deploy contínuo de arquivos.

Como Funciona:
1. O repositório GitHub contém arquivos HTML e o arquivo de configuração para o GitHub Actions.
2. Quando uma mudança é feita no repositório, o GitHub Actions é acionado.
3. O workflow copia o arquivo HTML para a instância EC2 usando SCP (Secure Copy).
4. Após o arquivo ser enviado, o site hospedado na instância EC2 estará acessível.

Tecnologias Usadas:
- **GitHub Actions**: Para automatizar o processo de deploy.
- **Amazon EC2**: Para hospedar o site.
- **SCP (Secure Copy Protocol)**: Para transferir arquivos entre o repositório GitHub e a EC2.
- **SSH**: Para autenticar a conexão entre o GitHub Actions e a instância EC2.

Requisitos:
1. Conta na AWS com uma instância EC2 configurada.
2. Chave SSH configurada para acessar a instância EC2.
3. GitHub Secrets configurado com a chave privada e o host da instância EC2.
4. Arquivo de configuração do GitHub Actions pronto para automatizar o processo de deploy.

Passos para Configuração:

1. **Criação da Instância EC2**:
   - Crie uma instância EC2 na AWS.
   - Certifique-se de que a porta 22 (SSH) esteja aberta no grupo de segurança.
   - Gere e faça o download da chave `.pem` para autenticação.

2. **Configuração do GitHub**:
   - Vá até **Settings > Secrets and Variables > Actions** no seu repositório.
   - Adicione os seguintes secrets:
     - **EC2_SSH_KEY**: Sua chave privada `.pem` (use uma ferramenta como `base64` para converter o arquivo para string).
     - **EC2_HOST**: O endereço IP público ou o DNS da instância EC2.

3. **GitHub Actions**:
   - Adicione um arquivo `.yml` na pasta `.github/workflows` do seu repositório.
   - Defina as etapas do workflow para usar o `scp-action` para enviar o arquivo para a instância EC2.

4. **Deploy**:
   - Faça o commit das mudanças no repositório.
   - O GitHub Actions será acionado e realizará o upload do arquivo HTML para a instância EC2.
   - Acesse a URL pública da instância EC2 para verificar o resultado.

Exemplo de Workflow GitHub Actions:

```yaml
name: Deploy to EC2

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checar repositório
        uses: actions/checkout@v3

      - name: Enviar arquivo via SCP
        uses: appleboy/scp-action@v0.1.4
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ec2-user  # Ou 'ubuntu', dependendo da imagem da instância EC2
          key: ${{ secrets.EC2_SSH_KEY }}
          port: 22
          source: "index.html"
          target: "/var/www/html/"
          timeout: 30s
          command_timeout: 10m
