FROM jenkins/jenkins:lts-jdk17

USER root

# Instalar dependências básicas
RUN apt-get update && apt-get install -y lsb-release ca-certificates curl gnupg sudo

# Adicionar repositório oficial do Docker para instalar o CLI corretamente
RUN install -m 0755 -d /etc/apt/keyrings
RUN curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
RUN chmod a+r /etc/apt/keyrings/docker.gpg

RUN echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  tee /etc/apt/sources.list.d/docker.list > /dev/null

RUN apt-get update && apt-get install -y docker-ce-cli

# Instalar plugins modernos (versões atualizadas para evitar bugs de segurança/compatibilidade)
RUN jenkins-plugin-cli --plugins "blueocean docker-workflow git workflow-aggregator"

# Permitir que o usuário jenkins use sudo sem senha (útil para gerenciar o socket se necessário)
RUN echo "jenkins ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

# Voltar para o usuário jenkins, mas garantir que ele esteja no grupo do docker se o socket permitir
# No Docker Desktop Mac, o socket geralmente pertence ao root, por isso o Jenkins rodará como root ou via sudo
USER root
