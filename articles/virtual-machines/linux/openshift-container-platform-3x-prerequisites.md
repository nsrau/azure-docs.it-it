---
title: OpenShift container Platform 3,11 in Prerequisiti di Azure | Microsoft Docs
description: Prerequisiti per la distribuzione della piattaforma OpenShift container 3,11 in Azure.
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
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 591cc7a4b84f75536446abbcbe32a69a122ddf5a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392670"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>Prerequisiti comuni per la distribuzione di OpenShift container Platform 3,11 in Azure

Questo articolo descrive i prerequisiti comuni per distribuire OpenShift Container Platform o OKD in Azure.

L'installazione di OpenShift usa i playbook Ansible. Ansible usa Secure Shell (SSH) per la connessione a tutti gli host del cluster al fine di completare i passaggi di installazione.

Quando Ansible esegue la connessione SSH agli host remoti, non è possibile immettere una password. Per questo motivo, la chiave privata non può avere una password (passphrase) associata, altrimenti la distribuzione avrà esito negativo.

Poiché tutte le macchine virtuali vengono distribuite tramite modelli di Azure Resource Manager, viene usata la stessa chiave pubblica per l'accesso a tutte le macchine virtuali. La chiave privata corrispondente deve trovarsi anche nella macchina virtuale che esegue tutti i PlayBook. Per eseguire questa azione in modo sicuro, viene usato un insieme di credenziali delle chiavi di Azure per passare la chiave privata alla macchina virtuale.

Se è necessario un archivio permanente per i contenitori, sono necessari volumi permanenti. OpenShift supporta i dischi rigidi virtuali (VHD) di Azure per i volumi permanenti, ma Azure deve prima essere configurato come provider di servizi cloud.

In questo modello, OpenShift provvederà a:

- Consente di creare un oggetto VHD in un account di archiviazione di Azure o in un disco gestito.
- Montare il disco rigido virtuale in una macchina virtuale e formattare il volume.
- Montare il volume nel pod.

A tale scopo, OpenShift necessita delle autorizzazioni per eseguire le attività precedenti in Azure. A questo scopo, viene usata un'entità servizio. L'entità servizio è un account di sicurezza in Azure Active Directory a cui vengono concesse le autorizzazioni per le risorse.

L'entità servizio deve avere accesso agli account di archiviazione e alle macchine virtuali che costituiscono il cluster. Se tutte le risorse del cluster OpenShift vengono distribuite in un singolo gruppo di risorse, è possibile concedere all'entità servizio le autorizzazioni per tale gruppo di risorse.

Questa guida descrive come creare gli artefatti associati ai prerequisiti.

> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi per gestire le chiavi SSH per il cluster OpenShift.
> * Creare un'entità servizio per l'uso da parte del provider di servizi cloud Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere a Azure 
Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/reference-index) e seguire le istruzioni visualizzate oppure fare clic su **Prova** per usare Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. È necessario usare un gruppo di risorse dedicato per ospitare l'insieme di credenziali delle chiavi. Questo gruppo è separato dal gruppo di risorse in cui vengono distribuite le risorse del cluster OpenShift.

L'esempio seguente crea un gruppo di risorse denominato *keyvaultrg* nella località *eastus*:

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi
Creare un insieme di credenziali delle chiavi per archiviare le chiavi SSH per il cluster con il comando [az keyvault create](/cli/azure/keyvault). Il nome dell'insieme di credenziali delle chiavi deve essere univoco a livello globale e deve essere abilitato per la distribuzione del modello, altrimenti la distribuzione avrà esito negativo con l'errore "KeyVaultParameterReferenceSecretRetrieveFailed".

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
OpenShift comunica con Azure usando un nome utente e una password o un'entità servizio. Un'entità servizio di Azure è un'identità di sicurezza che è possibile usare con le app, con i servizi e con strumenti di automazione come OpenShift. Le autorizzazioni per le operazioni che l'entità servizio può eseguire in Azure vengono controllate e definite dall'utente. È consigliabile definire come ambito le autorizzazioni dell'entità servizio per gruppi di risorse specifici anziché per l'intera sottoscrizione.

Creare un'entità servizio con [az ad sp create-for-rbac](/cli/azure/ad/sp) e generare l'output delle credenziali necessarie per OpenShift.

Nell'esempio seguente viene creata un'entità servizio a cui vengono assegnate le autorizzazioni di Collaboratore per un gruppo di risorse denominato openshiftrg.

Si crea prima un gruppo di risorse chiamato openshiftrg:

```azurecli
az group create -l eastus -n openshiftrg
```

Creare un'entità servizio:

```azurecli
scope=`az group show --name openshiftrg --query id`
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --password {Strong Password} \
      --scopes $scope \
```
Se si usa Windows, eseguire ```az group show --name openshiftrg --query id``` e usare l'output al posto di $scope.

Prendere nota della proprietà appId restituita dal comando:
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
 > Creare una password sicura. Attenersi alle istruzioni relative a [regole e limitazioni sulle password di Azure AD](/azure/active-directory/active-directory-passwords-policy).

Per altre informazioni sulle entità servizio, vedere [Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Prerequisiti applicabili solo al modello di Gestione risorse

È necessario creare i segreti per la chiave privata SSH (**sshPrivateKey**), il segreto client Azure ad (**aadClientSecret**), la password amministratore OpenShift (**openshiftPassword**) e la password o la chiave di attivazione di Red Hat Subscription Manager ( **rhsmPasswordOrActivationKey**).  Inoltre, se si usano certificati SSL personalizzati, sarà necessario creare sei segreti aggiuntivi, ovvero **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile**e  **masterkeyfile**.  Questi parametri verranno illustrati in modo più dettagliato.

Il modello fa riferimento a nomi di segreti specifici, quindi è **necessario** usare i nomi in grassetto elencati sopra (maiuscole/minuscole).

### <a name="custom-certificates"></a>Certificati personalizzati

Per impostazione predefinita, il modello consente di distribuire un cluster OpenShift usando certificati autofirmati per la console Web OpenShift e il dominio di routing. Se si desidera utilizzare certificati SSL personalizzati, impostare ' routingCertType ' su' Custom ' è masterCertType ' su' Custom '.  Per i certificati sono necessari i file di CA, CERT e Key nel formato PEM.  È possibile utilizzare certificati personalizzati per uno ma non per l'altro.

È necessario archiviare questi file in Key Vault segreti.  Usare lo stesso Key Vault di quello usato per la chiave privata.  Anziché richiedere 6 input aggiuntivi per i nomi dei segreti, il modello è hardcoded per l'uso di nomi di segreti specifici per ogni file di certificato SSL.  Archiviare i dati del certificato usando le informazioni riportate nella tabella seguente.

| Nome del segreto      | File del certificato   |
|------------------|--------------------|
| mastercafile     | file CA Master     |
| mastercertfile   | file certificato Master   |
| masterkeyfile    | file di chiave master    |
| routingcafile    | file CA di routing    |
| routingcertfile  | file CERT di routing  |
| routingkeyfile   | file della chiave di routing   |

Creare i segreti usando l'interfaccia della riga di comando di Azure. Di seguito è riportato un esempio.

```bash
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato gli argomenti seguenti:
> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi per gestire le chiavi SSH per il cluster OpenShift.
> * Creare un'entità servizio per l'uso da parte del provider di soluzioni cloud Azure.

Distribuire ora un cluster OpenShift:

- [Distribuire OpenShift Container Platform](./openshift-container-platform-3x.md)
- [Distribuisci l'offerta del Marketplace self-Managed OpenShift container Platform](./openshift-container-platform-3x-marketplace-self-managed.md)
