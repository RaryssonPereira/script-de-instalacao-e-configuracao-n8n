# Guia Completo: Instalação, Backup e Atualização de uma Instância n8n com Docker

Este documento serve como uma base de conhecimento detalhada, registrando o processo de diagnóstico e os passos necessários para realizar um backup seguro, criar um ambiente de teste funcional e atualizar uma instância do n8n rodando em Docker, gerenciada por um Nginx como proxy reverso.

---

## 1. Análise do Ambiente de Exemplo

- **Diretório da Aplicação:** `/var/www/n8n`
- **Configuração:** `docker-compose.yml`
- **Domínio:** `n8n.seudominio.com`
- **Banco de Dados:** SQLite (padrão do n8n)

📌 **Ponto de Atenção:** Em alguns sistemas, pode ser necessária a instalação manual de dependências dentro do container (ex: sqlite). Este guia cobre como diagnosticar e resolver isso.

---

## 2. Objetivo

Atualizar uma instância do n8n de uma versão mais antiga para a mais recente, garantindo a integridade dos dados (workflows, credenciais, etc.) através da criação de um ambiente de teste idêntico ao de produção.

---

## 3. O Processo de Atualização Segura

A estratégia consiste em clonar o ambiente de produção, testar a atualização em um clone seguro e, somente após o sucesso, aplicar a atualização no ambiente real.

### Passo A: Backup Verificado do Ambiente de Produção

A primeira tentativa de backup utilizando `docker run ... tar` a partir do host pode falhar silenciosamente. O método mais confiável é criar o backup de dentro do próprio container.

```bash
# Acessar o shell do container de produção
docker exec -it n8n sh

# Criar backup compactado do diretório .n8n
tar -czvf /tmp/n8n-backup-verificado.tar.gz -C /home/node/ .n8n

# Sair do container
exit

# Copiar o arquivo do container para o host
docker cp n8n:/tmp/n8n-backup-verificado.tar.gz .
