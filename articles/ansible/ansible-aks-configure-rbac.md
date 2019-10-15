---
title: Esercitazione - Configurare ruoli Controllo degli accessi in base al ruolo nel servizio Azure Kubernetes con Ansible
description: Informazioni su come usare Ansible per configurare Controllo degli accessi in base al ruolo nel cluster del servizio Azure Kubernetes
keywords: ansible, azure, devops, bash, cloudshell, playbook, servizio Azure Kubernetes, contenitore, aks, kubernetes, azure active directory, controllo degli accessi in base al ruolo
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 36a6f5ade7a60a989d2e80f2405aaa2d1d50b756
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242345"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Esercitazione: Configurare ruoli Controllo degli accessi in base al ruolo nel servizio Azure Kubernetes con Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

È possibile configurare il servizio Azure Kubernetes in modo da usare [Azure Active Directory (AD)](/azure/active-directory/) per l'autenticazione utente. Dopo la configurazione si usa il token di autenticazione di Azure AD per accedere al cluster del servizio Azure Kubernetes. Il ruolo Controllo degli accessi in base al ruolo può essere basato sull'identità di un utente o sull'appartenenza a gruppi di Active Directory.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Creare un cluster del servizio Azure Kubernetes abilitato per Azure AD
> * Configurare un ruolo Controllo degli accessi in base al ruolo nel cluster

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Installare la libreria OpenShift di RedHat** - `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>Configurare Azure AD per l'autenticazione del servizio Azure Kubernetes

Quando si configura Azure AD per l'autenticazione del servizio Azure Kubernetes, vengono configurate due applicazioni di Azure AD. Questa operazione deve essere completata da un amministratore tenant di Azure. Per altre informazioni, vedere [Integrare Azure Active Directory con il servizio Azure Kubernetes](/azure/aks/aad-integration#create-the-server-application). 

Richiedere i valori seguenti all'amministratore del tenant di Azure:

- Segreto dell'app server
- ID app server
- ID app client 
- ID tenant

Questi valori sono necessari quando si esegue il playbook di esempio.  

## <a name="create-an-aks-cluster"></a>Creare un cluster del servizio Azure Container

In questa sezione viene creato un servizio Azure Kubernetes con l'[applicazione Azure AD](#configure-azure-ad-for-aks-authentication).

Quando si usa il playbook di esempio, è necessario tenere conto di alcuni concetti fondamentali:

- Il playbook carica `ssh_key` da `~/.ssh/id_rsa.pub`. Per modificare, usare il formato a riga singola che inizia con "ssh-rsa" (senza virgolette).
- I valori `client_id` e `client_secret` vengono caricati da `~/.azure/credentials`, cioè il file credenziali predefinito. È possibile impostare questi valori sull'entità servizio o caricarli dalle variabili di ambiente:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

Salvare il playbook seguente come `aks-create.yml`:

```yml
- name: Create resource group
  azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

- name: List supported kubernetes version from Azure
  azure_rm_aksversion_facts:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with RBAC enabled
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      enable_rbac: yes
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      aad_profile:
          client_app_id: "{{ client_app_id }}"
          server_app_id: "{{ server_app_id }}"
          server_app_secret: "{{ server_app_secret }}"
          tenant_id: "{{ app_tenant_id }}"
  register: aks

- name: Save cluster user config
  copy:
      content: "{{ aks.kube_config }}"
      dest: "aks-{{ name }}-kubeconfig-user"

- name: Get admin config of AKS
  azure_rm_aks_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      show_kubeconfig: admin
  register: aks

- name: Save the kubeconfig
  copy:
      content: "{{ aks.aks[0].kube_config }}"
      dest: "aks-{{ name }}-kubeconfig"
```

## <a name="get-the-azure-ad-object-id"></a>Ottenere l'ID oggetto di Azure AD

Per creare un binding Controllo degli accessi in base al ruolo, è prima necessario ottenere l'ID oggetto di Azure AD. 

1. Accedere al [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Nel campo di ricerca nella parte superiore della pagina immettere `Azure Active Directory`. 

1. Fare clic su `Enter`.

1. Nel menu **Gestione** selezionare **Utenti**.

1. Nel campo del nome cercare l'account.

1. Nella colonna **Nome** selezionare il collegamento dell'account.

1. Nella sezione **Identità** copiare l'**ID oggetto**.

    ![Copiare l'ID oggetto di Azure AD.](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>Creare l'associazione RBAC

In questa sezione viene creato un binding del ruolo o un binding del ruolo cluster nel servizio Azure Kubernetes. 

Salvare il playbook seguente come `kube-role.yml`:

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: <your-aad-account>
```

Sostituire il segnaposto `&lt;your-aad-account>` con l'[ID oggetto](#get-the-azure-ad-object-id) del tenant di Azure AD.

Il playbook seguente distribuisce il nuovo ruolo nel servizio Azure Kubernetes. Salvarlo come `aks-kube-deploy.yml`:

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>Eseguire il playbook di esempio

Questa sezione riporta il playbook di esempio completo che chiama le attività create in questo articolo. 

Salvare il playbook seguente come `aks-rbac.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create AKS
       vars:
           client_app_id: <client id>
           server_app_id: <server id>
           server_app_secret: <server secret>
           app_tenant_id: <tenant id>
       include_tasks: aks-create.yml

     - name: Enable RBAC
       include_tasks: aks-kube-deploy.yml
```

Nella sezione `vars` sostituire i segnaposto seguenti con le informazioni di Azure AD:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Per eseguire il playbook completo, usare il comando `ansible-playbook`:

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Verificare i risultati

In questa sezione si usa kubectl per elencare i nodi creati in questo articolo.

Immettere il comando seguente a un prompt del terminale:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

Il comando consentirà di accedere direttamente a una pagina di autenticazione. Accedere con l'account Azure.

Dopo l'autenticazione, kubectl elenca i nodi in modo analogo ai risultati seguenti:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eliminare le risorse create in questo articolo. 

Aggiungere il codice seguente come `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      name: aksansibletest
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
      - name: Remove kubeconfig
        file:
            state: absent
            path: "aks-{{ name }}-kubeconfig"
```

Eseguire il playbook usando il comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ansible in Azure](/azure/ansible/)
