---
title: Prerequisiti di OpenShift in Azure | Microsoft Docs
description: Prerequisiti per la distribuzione di OpenShift in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 467428462260596f21ba59f49e3c48b5fc2526b6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Prerequisiti comuni per la distribuzione di OpenShift in Azure

Questo articolo descrive i prerequisiti comuni per OpenShift Origin o OpenShift Container Platform in Azure.

L'installazione di OpenShift usa i playbook Ansible. Ansible usa Secure Shell (SSH) per la connessione a tutti gli host del cluster al fine di completare i passaggi di installazione.

Quando viene avviata la connessione SSH agli host remoti, non è possibile immettere una password. Per questo motivo, la chiave privata non può avere una password associata, altrimenti la distribuzione avrà esito negativo.

Poiché tutte le macchine virtuali vengono distribuite tramite modelli di Azure Resource Manager, viene usata la stessa chiave pubblica per l'accesso a tutte le macchine virtuali. È necessario inserire la chiave privata corrispondente anche nella macchina virtuale che esegue tutti i playbook. Per eseguire questa operazione in modo sicuro, usare Azure Key Vault per passare la chiave privata alla macchina virtuale.

Se è necessario un archivio permanente per i contenitori, sono necessari volumi permanenti. Per offrire questa funzionalità, OpenShift supporta i dischi rigidi virtuali di Azure, ma è prima necessario configurare Azure come provider di servizi cloud. 

In questo modello, OpenShift provvederà a:

- Creare un oggetto disco rigido virtuale in un account di archiviazione di Azure.
- Montare il disco rigido virtuale in una macchina virtuale e formattare il volume.
- Montare il volume nel pod.

A tale scopo, OpenShift necessita delle autorizzazioni per eseguire le attività precedenti in Azure. È quindi necessaria un'entità servizio. L'entità servizio è un account di sicurezza in Azure Active Directory a cui vengono concesse le autorizzazioni per le risorse.

L'entità servizio deve avere accesso agli account di archiviazione e alle macchine virtuali che costituiscono il cluster. Se tutte le risorse del cluster OpenShift vengono distribuite in un singolo gruppo di risorse, è possibile concedere all'entità servizio le autorizzazioni per tale gruppo di risorse.

Questa guida descrive come creare gli artefatti associati ai prerequisiti.

> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi per gestire le chiavi SSH per il cluster OpenShift.
> * Creare un'entità servizio per l'uso da parte del provider di soluzioni cloud Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure 
Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#az_login) e seguire le istruzioni visualizzate oppure fare clic su **Prova** per usare Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Verrà usato un gruppo di risorse dedicato per ospitare l'insieme di credenziali delle chiavi. Questo gruppo è separato dal gruppo di risorse in cui vengono distribuite le risorse del cluster OpenShift. 

L'esempio seguente crea un gruppo di risorse denominato *keyvaultrg* nella località *eastus*:

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi
Creare un insieme di credenziali delle chiavi per archiviare le chiavi SSH per il cluster con il comando [az keyvault create](/cli/azure/keyvault#az_keyvault_create). Il nome dell'insieme di credenziali delle chiavi deve essere univoco a livello globale.

L'esempio seguente crea un insieme di credenziali delle chiavi denominato *keyvault* nel gruppo di risorse *keyvaultrg*:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Creare una chiave SSH 
La chiave SSH è necessaria per proteggere l'accesso al cluster di OpenShift Origin. Creare una coppia di chiavi SSH usando il comando `ssh-keygen` (in Linux o macOS):
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> La coppia di chiavi SSH non deve avere una password.

Per altre informazioni sulle chiavi SSH in Windows, vedere [Come usare le chiavi SSH con Windows in Azure](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Archiviare la chiave privata SSH in Azure Key Vault
La distribuzione di OpenShift usa la chiave SSH che è stata creata per proteggere l'accesso al master di OpenShift. Per consentire alla distribuzione di recuperare in modo sicuro la chiave SSH, archiviare la chiave nell'insieme di credenziali delle chiavi usando il comando seguente:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Creare un'entità servizio 
OpenShift comunica con Azure usando un nome utente e una password o un'entità servizio. Un'entità servizio di Azure è un'identità di sicurezza che è possibile usare con le app, con i servizi e con strumenti di automazione come OpenShift. Le autorizzazioni per le operazioni che l'entità servizio può eseguire in Azure vengono controllate e definite dall'utente. Per migliorare la sicurezza rispetto alla semplice immissione di nome utente e password, questo esempio crea un'entità servizio di base.

Creare un'entità servizio con [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) e generare l'output delle credenziali necessarie per OpenShift.

L'esempio seguente crea un'entità servizio e le assegna le autorizzazioni di Collaboratore per un gruppo di risorse denominato myResourceGroup. Se si usa Windows, eseguire ```az group show --name myResourceGroup --query id``` separatamente e usare l'output da inserire nell'opzione --scopes.

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

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

Per altre informazioni sulle entità servizio, vedere [Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato gli argomenti seguenti:
> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi per gestire le chiavi SSH per il cluster OpenShift.
> * Creare un'entità servizio per l'uso da parte del provider di soluzioni cloud Azure.

Distribuire ora un cluster OpenShift:

- [Distribuire OpenShift Origin](./openshift-origin.md)
- [Distribuire OpenShift Container Platform](./openshift-container-platform.md)

