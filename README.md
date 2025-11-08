# ☁️ Projeto IaC: Provisionamento de VM Linux no Azure

Este projeto utiliza **Terraform** para provisionar uma Máquina Virtual (VM) Linux no Microsoft Azure e **Ansible** para automatizar a configuração da VM, incluindo a instalação do Docker e o deployment de uma aplicação containerizada.

## ⚙️ Configuração da Infraestrutura (Terraform)

O arquivo `main.tf` define a infraestrutura básica necessária:

* **Grupo de Recursos:** `trabalho-iac` (variável)
* **Localização:** `northcentralus` (variável)
* **Rede:** VNet (`vnet-pratica`) e Subnet (`subnet-pratica`).
* **IP Público:** `pip-pratica-vm` (IP Estático).
* **Segurança (NSG):** Regras liberando **SSH (22)** e **HTTP (80)**.
* **VM Linux:** Imagem Ubuntu 22.04 LTS, configurada com chave SSH.

## 🚀 Requisitos

Você precisará das seguintes ferramentas instaladas:

* **Terraform**
* **Ansible**
* **Azure CLI** (Para autenticação)

## 🔑 Credenciais e Variáveis

Os valores das variáveis são fornecidos no seu arquivo `terraform.tfvars`:

| Variável | Valor | Descrição |
| :--- | :--- | :--- |
| `resource_group_name` | `trabalho-iac` | Nome do Grupo de Recursos. |
| `location` | `northcentralus` | Região do Azure. |
| `ssh_public_key_path` | `~/.ssh/projeto_rsa.pub` | Caminho para a chave pública SSH. |
| *Outras Variáveis* | *(admin_username, vm_size)* | (Necessário definir no `variables.tf` e no `.tfvars`) |

## 📦 Passos de Execução

### 1. Provisionar Infraestrutura (Terraform)

Certifique-se de que está autenticado no Azure CLI (`az login`).

```bash
# 1. Inicializa o diretório de trabalho do Terraform
terraform init

# 2. Revisa as mudanças a serem aplicadas
terraform plan

# 3. Aplica o plano para criar a infraestrutura no Azure
terraform apply -auto-approve

2. Configurar a VM (Ansible)

Após a VM ser criada, execute o playbook. Ele instalará o Docker, configurará as permissões, construirá a imagem (meu-nginx-app:latest) e executará o container na porta 80.

⚠️ Nota: Você precisará do endereço IP público da VM (que pode ser obtido via terraform output) para configurar seu arquivo de inventário (hosts).
Bash

# Exemplo de execução (ajuste o caminho da chave privada e o nome de usuário)
ansible-playbook -i hosts playbook.yml -u azureuser --private-key ~/.ssh/projeto_rsa

3. Acessar a Aplicação

Acesse a sua aplicação no browser usando o IP Público da VM.

🗑️ Limpeza (Destroy)

Para destruir todos os recursos criados pelo Terraform no Azure:
Bash

terraform destroy -auto-approve

🔒 Segurança (Arquivos Ignorados)

Os seguintes arquivos são ignorados pelo controle de versão (Git) para proteger dados sensíveis e arquivos de estado, conforme o .gitignore:

    .terraform/

    terraform.tfstate

    terraform.tfstate.backup

    terraform.tfvars

    hosts

    azure_credentials.txt
