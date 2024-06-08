# HA Kubernetes Cluster with Keepalived and HAProxy

Projeto de implementação de um cluster Kubernetes HA usando Ansible.

### Cluster Architecture

![](https://kubesphere.io/images/docs/v3.x/installing-on-linux/high-availability-configurations/set-up-ha-cluster-using-keepalived-haproxy/architecture-ha-k8s-cluster.png)

## Estrutura do Projeto

```plaintext
k8s-ha-cluster/
├── .gitignore
├── ansible.cfg
├── inventory.ini
├── README.md
├── vars/
│   └── vars.yml
├── playbooks/
│   ├── 00-provision-vms.yml
│   ├── 01-setup.yml
│   ├── 02-install-packages.yml
│   ├── 03-configure-haproxy.yml
│   ├── 04-configure-keepalived.yml
│   ├── 05-configure-certificates.yml
│   ├── 06-bootstrap-kubernetes.yml
│   ├── 07-deploy-kubernetes.yml
├── roles/
│   ├── common/
│   │   └── tasks/
│   │       └── main.yml
│   ├── haproxy/
│   │   ├── files/
│   │   │   └── certs/
│   │   │       ├── cadeiaintermediaria.crt
│   │   │       ├── k8s.unilab.edu.br.crt
│   │   │       ├── k8s.unilab.edu.br.key
│   │   │       └── k8s_unilab_wildcard.pem
│   │   ├── tasks/
│   │   │   └── main.yml
│   │   └── templates/
│   │       └── haproxy.cfg.j2
│   ├── keepalived/
│   │   ├── tasks/
│   │   │   └── main.yml
│   │   └── templates/
│   │       └── keepalived.conf.j2
│   ├── kubernetes/
│   │   ├── files/
│   │   │   ├── ingress.yaml
│   │   │   └── kubeadm-init.sh
│   │   ├── tasks/
│   │   │   └── main.yml
│   │   └── templates/
│   │       └── kubeadm-config.yaml.j2
│   ├── proxmox/
│   │   ├── files/
│   │   │   ├── deploy_qcow2_vms.yml
│   │   │   └── install_base_packages.yml
│   │   ├── tasks/
│   │   │   └── main.yml
```

## Pré-requisitos

1. **Proxmox VE**: Certifique-se de que o Proxmox VE esteja configurado e acessível.
2. **Ansible**: Instale o Ansible na máquina de gerenciamento.
3. **Acesso SSH**: Configure o acesso SSH sem senha para todos os nós.
4. **Inventário e Variáveis**: Atualize `inventory.ini` e `vars/vars.yml` com os detalhes do seu ambiente.

## Passos para Configuração

```bash
mkdir -p k8s-ha-cluster/{vars,playbooks,roles/{common/tasks,haproxy/{tasks,templates,files/certs},keepalived/{tasks,templates},kubernetes/{tasks,templates,files},proxmox/{tasks,templates,files}}}
touch k8s-ha-cluster/ansible.cfg
touch k8s-ha-cluster/inventory.ini
touch k8s-ha-cluster/vars/vars.yml
touch k8s-ha-cluster/playbooks/{00-provision-vms.yml,01-setup.yml,02-install-packages.yml,03-configure-haproxy.yml,04-configure-keepalived.yml,05-configure-certificates.yml,06-bootstrap-kubernetes.yml,07-deploy-kubernetes.yml}
touch k8s-ha-cluster/roles/common/tasks/main.yml
touch k8s-ha-cluster/roles/haproxy/tasks/main.yml
touch k8s-ha-cluster/roles/haproxy/templates/haproxy.cfg.j2
touch k8s-ha-cluster/roles/haproxy/files/certs/{cadeiaintermediaria.crt,k8s.unilab.edu.br.crt,k8s.unilab.edu.br.key,k8s_unilab_wildcard.pem}
touch k8s-ha-cluster/roles/keepalived/tasks/main.yml
touch k8s-ha-cluster/roles/keepalived/templates/keepalived.conf.j2
touch k8s-ha-cluster/roles/kubernetes/tasks/main.yml
touch k8s-ha-cluster/roles/kubernetes/templates/kubeadm-config.yaml.j2
touch k8s-ha-cluster/roles/kubernetes/files/{ingress.yaml,kubeadm-init.sh}
touch k8s-ha-cluster/roles/proxmox/tasks/main.yml
touch k8s-ha-cluster/roles/proxmox/files/{deploy_qcow2_vms.yml,install_base_packages.yml}
```

## Configuração

1. Atualize o inventário `inventory.ini` com os IPs corretos dos hosts.
2. Configure as variáveis no arquivo `vars/vars.yml`.

## Como executar

1. Provisione as VMs:
   ```bash
   ansible-playbook playbooks/00-provision-vms.yml
   ```

2. Configure os servidores:
   ```bash
   ansible-playbook playbooks/01-setup.yml
   ```

3. Instale os pacotes necessários:
   ```bash
   ansible-playbook playbooks/02-install-packages.yml
   ```

4. Configure o HAProxy:
   ```bash
   ansible-playbook playbooks/03-configure-haproxy.yml
   ```

5. Configure o Keepalived:
   ```bash
   ansible-playbook playbooks/04-configure-keepalived.yml
   ```

6. Configure os certificados SSL:
   ```bash
   ansible-playbook playbooks/05-configure-certificates.yml
   ```

7. Bootstrap do Kubernetes:
   ```bash
   ansible-playbook playbooks/06-bootstrap-kubernetes.yml
   ```

8. Deploy dos componentes do Kubernetes:
   ```bash
   ansible-playbook playbooks/07-deploy-kubernetes.yml
   ```
