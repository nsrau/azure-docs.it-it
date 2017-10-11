---
title: Distribuire OpenShift Origin in Azure| Microsoft Docs
description: Informazioni su come distribuire OpenShift Origin in macchine virtuali di Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: jbinder
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: jbinder
ms.openlocfilehash: e03da05625e440eab29ccc28a2343d3433fc7607
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-openshift-origin-to-azure-virtual-machines"></a>Distribuire OpenShift Origin in macchine virtuali di Azure 

[ OpenShift Origin](https://www.openshift.org/) è una piattaforma contenitore open source basata su [Kubernetes](https://kubernetes.io/). Semplifica distribuzione, ridimensionamento e uso delle applicazioni multi-tenant. 

Questa guida descrive come distribuire OpenShift Origin in macchine virtuali di Azure tramite la CLI di Azure e i modelli di Azure Resource Manager. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi per gestire le chiavi SSH per il cluster OpenShift.
> * Distribuire un cluster OpenShift in macchine virtuali di Azure. 
> * Installare e configurare la [CLI di OpenShift](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index) per gestire il cluster.
> * Personalizzare la distribuzione di OpenShift.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Questa guida introduttiva richiede l'interfaccia della riga di comando di Azure 2.0.8 o versioni successive. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="log-in-to-azure"></a>Accedere ad Azure 
Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#login) e seguire le istruzioni visualizzate oppure fare clic su **Prova** per usare Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi
Creare un insieme di credenziali delle chiavi per archiviare le chiavi SSH per il cluster con il comando [az keyvault create](/cli/azure/keyvault#create).  

```azurecli 
az keyvault create --resource-group myResourceGroup --name myKeyVault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Creare una chiave SSH 
La chiave SSH è necessaria per proteggere l'accesso al cluster di OpenShift Origin. Creare una coppia di chiavi SSH usando il comando `ssh-keygen`. 
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> La coppia di chiavi SSH che viene creata non deve avere una passphrase.

Per altre informazioni sulle chiavi SSH in Windows, vedere [Come usare le chiavi SSH con Windows in Azure](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-ssh-private-key-in-key-vault"></a>Archiviare la chiave privata SSH nell'insieme di credenziali per le chiavi
La distribuzione di OpenShift usa la chiave SSH che è stata creata per proteggere l'accesso al master di OpenShift. Per abilitare la distribuzione per recuperare in modo sicuro la chiave SSH, archiviare la chiave nell'insieme di credenziali per le chiavi usando il comando seguente.

# <a name="enabled-for-template-deployment"></a>Distribuzione abilitata per modello
```azurecli
az keyvault secret set --vault-name KeyVaultName --name OpenShiftKey --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>Creare un'entità servizio 
OpenShift comunica con Azure usando un nome utente e una password o un'entità servizio. Un'entità servizio di Azure è un'identità di sicurezza che è possibile usare con le app, con i servizi e con strumenti di automazione come OpenShift. Le autorizzazioni per le operazioni che l'entità servizio può eseguire in Azure vengono controllate e definite dall'utente. Per migliorare la sicurezza rispetto alla semplice immissione di nome utente e password, questo esempio crea un'entità servizio di base.

Creare un'entità servizio con [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) e generare l'output delle credenziali necessarie per OpenShift:

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {strong password} \
          --scopes $(az group show --name myResourceGroup --query id)
```
Prendere nota della proprietà appId restituita dal comando.
```json
{
  "appId": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {strong password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > Non creare una password non sicura.  Attenersi alle istruzioni relative a [regole e limitazioni sulle password di Azure AD](/azure/active-directory/active-directory-passwords-policy).

Per altre informazioni sulle entità servizio, vedere [Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

## <a name="deploy-the-openshift-origin-template"></a>Distribuire il modello di OpenShift Origin
A questo punto distribuire OpenShift Origin usando un modello di Azure Resource Manager. 

> [!NOTE] 
> Il comando seguente richiede l'interfaccia della riga di comando di Azure 2.0.8 o versione successiva. È possibile verificare la versione della CLI di Azure con il comando `az --version`. Per aggiornare la versione della CLI, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

Usare il valore `appId` dall'entità servizio creata in precedenza per il parametro `aadClientId`.

```azurecli 
az group deployment create --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --params \ 
        openshiftMasterPublicIpDnsLabel=myopenshiftmaster \
        infraLbPublicIpDnsLabel=myopenshiftlb \
        openshiftPassword=Pass@word!
        sshPublicKey=~/.ssh/openshift_rsa.pub \
        keyVaultResourceGroup=myResourceGroup \
        keyVaultName=myKeyVault \
        keyVaultSecret=OpenShiftKey \
        aadClientId={appId} \
        aadClientSecret={strong password} 
```
Il completamento della distribuzione richiede fino a 20 minuti. Al termine della distribuzione, l'URL della console di OpenShift e il nome DNS del master di OpenShift vengono visualizzati sul terminale.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ocpadmin@myopenshiftmaster.cloudapp.azure.com"
}
```
## <a name="connect-to-the-openshift-cluster"></a>Eseguire la connessione al cluster OpenShift
Al termine della distribuzione, eseguire la connessione alla console di OpenShift tramite il browser immettendo `OpenShift Console Uri`. In alternativa, è possibile connettersi al master di OpenShift usando il comando seguente.

```bash
$ ssh ocpadmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non servono più, è possibile rimuovere il gruppo di risorse, il cluster OpenShift e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group#delete).

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come:
> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi per gestire le chiavi SSH per il cluster OpenShift.
> * Distribuire un cluster OpenShift in macchine virtuali di Azure. 
> * Installare e configurare la [CLI di OpenShift](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index) per gestire il cluster.

A questo punto il cluster OpenShift Origin è stato distribuito. È possibile seguire le esercitazioni di OpenShift per imparare a distribuire la prima applicazione e a usare gli strumenti di OpenShift. Per una panoramica iniziale, vedere [Getting Started with OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html) (Introduzione a OpenShift Origin). 
