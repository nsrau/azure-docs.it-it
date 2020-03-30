---
title: OpenShift Container Platform 3.11 nei prerequisiti di AzureOpenShift Container Platform 3.11 in Azure prerequisites
description: Prerequisiti per distribuire OpenShift Container Platform 3.11 in Azure.Prerequisites to deploy OpenShift Container Platform 3.11 in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: b2b34a6fdf96613c5bc372e585598fabbe43d53d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066618"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>Prerequisiti comuni per la distribuzione di OpenShift Container Platform 3.11 in AzureCommon prerequisites for deploying OpenShift Container Platform 3.11 in Azure

Questo articolo descrive i prerequisiti comuni per distribuire OpenShift Container Platform o OKD in Azure.

L'installazione di OpenShift usa i playbook Ansible. Ansible usa Secure Shell (SSH) per la connessione a tutti gli host del cluster al fine di completare i passaggi di installazione.

Quando ansible rende la connessione SSH agli host remoti, non può immettere una password. Per questo motivo, la chiave privata non può avere una password (passphrase) associata, altrimenti la distribuzione avrà esito negativo.

Poiché tutte le macchine virtuali vengono distribuite tramite modelli di Azure Resource Manager, viene usata la stessa chiave pubblica per l'accesso a tutte le macchine virtuali. La chiave privata corrispondente deve trovarsi nella macchina virtuale che esegue anche tutti i playbook. Per eseguire questa azione in modo sicuro, viene usato un insieme di credenziali delle chiavi di Azure per passare la chiave privata nella macchina virtuale.

Se è necessario un archivio permanente per i contenitori, sono necessari volumi permanenti. OpenShift supporta i dischi rigidi virtuali di Azure per i volumi persistenti, ma Azure deve prima essere configurato come provider di cloud.

In questo modello, OpenShift provvederà a:

- Crea un oggetto VHD in un account di archiviazione di Azure o in un disco gestito.
- Montare il disco rigido virtuale in una macchina virtuale e formattare il volume.
- Montare il volume nel pod.

A tale scopo, OpenShift necessita delle autorizzazioni per eseguire le attività precedenti in Azure. A questo scopo viene utilizzata un'entità servizio. L'entità servizio è un account di sicurezza in Azure Active Directory a cui vengono concesse le autorizzazioni per le risorse.

L'entità servizio deve avere accesso agli account di archiviazione e alle macchine virtuali che costituiscono il cluster. Se tutte le risorse del cluster OpenShift vengono distribuite in un singolo gruppo di risorse, è possibile concedere all'entità servizio le autorizzazioni per tale gruppo di risorse.

Questa guida descrive come creare gli artefatti associati ai prerequisiti.

> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi per gestire le chiavi SSH per il cluster OpenShift.
> * Creare un'entità servizio per l'uso da parte del provider di servizi cloud Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure 
Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/reference-index) e seguire le istruzioni visualizzate oppure fare clic su **Prova** per usare Cloud Shell.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. È consigliabile usare un gruppo di risorse dedicato per ospitare l'insieme di credenziali delle chiavi. Questo gruppo è separato dal gruppo di risorse in cui vengono distribuite le risorse del cluster OpenShift.

Nell'esempio seguente viene creato un gruppo di risorse denominato keyvaultrg nella posizione *eastus:The* following example creates a resource group named *keyvaultrg* in the eastus location:

```azurecli
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi
Creare un insieme di credenziali delle chiavi per archiviare le chiavi SSH per il cluster con il comando [az keyvault create](/cli/azure/keyvault). Il nome dell'insieme di credenziali delle chiavi deve essere univoco a livello globale e deve essere abilitato per la distribuzione del modello, altrimenti la distribuzione avrà esito negativo con errore "KeyVaultParameterReferenceSecretRetrieveFailed".

L'esempio seguente crea un insieme di credenziali delle chiavi denominato *keyvault* nel gruppo di risorse *keyvaultrg*:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Creare una chiave SSH 
La chiave SSH è necessaria per proteggere l'accesso al cluster di OpenShift. Creare una coppia di chiavi SSH usando il comando `ssh-keygen` (in Linux o macOS):
 
```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> La coppia di chiavi SSH non deve avere una password / passphrase.

Per altre informazioni sulle chiavi SSH in Windows, vedere [Come usare le chiavi SSH con Windows in Azure](/azure/virtual-machines/linux/ssh-from-windows). Assicurarsi di esportare la chiave privata nel formato OpenSSH.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Archiviare la chiave privata SSH in Azure Key Vault
La distribuzione di OpenShift usa la chiave SSH che è stata creata per proteggere l'accesso al master di OpenShift. Per consentire alla distribuzione di recuperare in modo sicuro la chiave SSH, archiviare la chiave nell'insieme di credenziali delle chiavi usando il comando seguente:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Creare un'entità servizio 
OpenShift comunica con Azure usando un nome utente e una password o un'entità servizio. Un'entità servizio di Azure è un'identità di sicurezza che è possibile usare con le app, con i servizi e con strumenti di automazione come OpenShift. Le autorizzazioni per le operazioni che l'entità servizio può eseguire in Azure vengono controllate e definite dall'utente. È consigliabile definire l'ambito delle autorizzazioni dell'entità servizio per gruppi di risorse specifici anziché per l'intera sottoscrizione.

Creare un'entità servizio con [az ad sp create-for-rbac](/cli/azure/ad/sp) e generare l'output delle credenziali necessarie per OpenShift.

Nell'esempio seguente viene creata un'entità servizio che le assegna le autorizzazioni di collaboratore a un gruppo di risorse denominato *openshiftrg*.

Creare innanzitutto il gruppo di risorse denominato *openshiftrg*:

```azurecli
az group create -l eastus -n openshiftrg
```

Creare un'entità servizio:

```azurecli
az group show --name openshiftrg --query id
```

Salvare l'output del comando e utilizzarlo al posto di $scope nel comando successivo

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

Prendere nota della proprietà appId e della password restituite dal comando:

```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```

 > [!WARNING] 
 > Assicurarsi di annotare la password sicura in quanto non sarà possibile recuperare nuovamente la password.

Per altre informazioni sulle entità servizio, vedere [Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Prerequisiti applicabili solo al modello di Resource ManagerPrerequisites applicable only to Resource Manager template

Sarà necessario creare segreti per la chiave privata SSH (**sshPrivateKey**), il segreto client di Azure AD (**aadClientSecret**), la password di amministratore OpenShift (**openshiftPassword**) e la password o la chiave di attivazione di Red Hat Subscription Manager (**rhsmPasswordOrActivationKey**).  Inoltre, se vengono utilizzati certificati SSL personalizzati, sarà necessario creare sei segreti aggiuntivi: **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile**e **masterkeyfile**.  Questi parametri saranno spiegati in modo più dettagliato.

Il modello fa riferimento a nomi di segreti specifici, pertanto è **necessario** utilizzare i nomi in grassetto elencati in precedenza (con distinzione tra maiuscole e minuscole).

### <a name="custom-certificates"></a>Certificati personalizzati

Per impostazione predefinita, il modello distribuirà un cluster OpenShift utilizzando certificati autofirmati per la console Web OpenShift e il dominio di routing. Se si desidera utilizzare certificati SSL personalizzati, impostare 'routingCertType' su 'custom' e 'masterCertType' su 'custom'.  Per i certificati sono necessari i file CA, Cert e Key in formato .pem.  È possibile utilizzare certificati personalizzati per uno ma non per l'altro.

È necessario archiviare questi file in segreti key Vault.  Utilizzare lo stesso insieme di credenziali delle chiavi utilizzato per la chiave privata.  Anziché richiedere 6 input aggiuntivi per i nomi dei segreti, il modello è hardcoded per utilizzare nomi segreti specifici per ognuno dei file di certificato SSL.  Archiviare i dati del certificato utilizzando le informazioni della tabella seguente.

| Nome segreto      | File del certificato   |
|------------------|--------------------|
| mastercafile     | file CA master     |
| mastercertfile   | file CERT master   |
| masterkeyfile    | file di chiave master    |
| routingcafile    | file CA di routing    |
| routingcertfile  | file CERT di routing  |
| routingkeyfile   | file di chiave di instradamento   |

Creare i segreti usando l'interfaccia della riga di comando di Azure.Create the secrets using the Azure CLI. Di seguito è riportato un esempio.

```azurecli
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato gli argomenti seguenti:
> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi per gestire le chiavi SSH per il cluster OpenShift.
> * Creare un'entità servizio per l'uso da parte del provider di soluzioni cloud Azure.

Distribuire ora un cluster OpenShift:

- [Distribuire OpenShift Container Platform](./openshift-container-platform-3x.md)
- [Distribuire l'offerta OpenShift Container Platform Self-Managed Marketplace](./openshift-container-platform-3x-marketplace-self-managed.md)
