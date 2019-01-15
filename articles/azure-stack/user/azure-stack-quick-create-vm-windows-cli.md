---
title: Creare una macchina virtuale Windows in Azure Stack tramite Azure CLI | Microsoft Docs
description: Informazioni su come creare una VM Windows in Azure Stack tramite Azure CLI
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 1/14/2019
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 8838172313ec40298ccd564a6a3f84314461a9b1
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2019
ms.locfileid: "54301978"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Guida introduttiva: creare una macchina virtuale Windows Server usando Azure CLI in Azure Stack

‎*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile creare una macchina virtuale Windows Server 2016 tramite la CLI di Azure. Seguire i passaggi descritti in questo articolo per creare e usare una macchina virtuale. Questo articolo offre anche la procedura seguente:

* Connettersi alla macchina virtuale con un client remoto.
* Installare il server web IIS e visualizzare la home page predefinita.
* Pulire le risorse.

## <a name="prerequisites"></a>Prerequisiti

* Assicurarsi che l'operatore di Azure Stack è stato aggiunto il **Windows Server 2016** immagini nel Marketplace di Azure Stack.

* Azure Stack richiede una versione specifica di comando di Azure per creare e gestire le risorse. Se non si dispone della riga di comando di Azure configurato per Azure Stack, seguire i passaggi necessari per [installare e configurare Azure CLI](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui è possibile distribuire e gestire le risorse di Azure Stack. Dall'ambiente Azure Stack, eseguire la [creare il gruppo di az](/cli/azure/group#az-group-create) comando per creare un gruppo di risorse.

>[!NOTE]
 I valori vengono assegnati per tutte le variabili negli esempi di codice. Tuttavia, è possibile assegnare nuovi valori, se si desidera.

L'esempio seguente crea un gruppo di risorse denominato myResourceGroup nella località locale.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una macchina virtuale (VM) tramite il [az vm create](/cli/azure/vm#az-vm-create) comando. L'esempio seguente crea una macchina virtuale denominata myVM. Questo esempio Usa Demouser per un nome utente amministrativo e Demouser@123 come la password dell'utente. Modificare questi valori su un valore appropriato per l'ambiente.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Quando viene creata la macchina virtuale, il **PublicIPAddress** parametri nell'output contiene l'indirizzo IP pubblico per la macchina virtuale. Annotare questo indirizzo perché necessaria per accedere alla macchina virtuale.

## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web

Perché questa macchina virtuale verrà eseguito il server web IIS, è necessario aprire la porta 80 al traffico Internet.

Usare la [az vm open-port](/cli/azure/vm#open-port) comando per aprire la porta 80.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale

Usare il comando successivo per creare una connessione Desktop remoto alla macchina virtuale. Sostituire "Indirizzo IP pubblico" con l'indirizzo IP della macchina virtuale. Quando richiesto, immettere il nome utente e password usati per la macchina virtuale.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Installare IIS tramite PowerShell

Ora che si è connessi alla macchina virtuale, è possibile usare PowerShell per installare IIS. Avviare PowerShell nella macchina virtuale ed eseguire il comando seguente:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Visualizzare la pagina iniziale di IIS

È possibile usare un web browser preferito per visualizzare la pagina iniziale di IIS predefinita. Usare l'indirizzo IP pubblico descritto nella sezione precedente per visitare la pagina predefinita.

![Sito IIS predefinito](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Pulire le risorse che non sono più necessari. Usare la [eliminazione gruppo az](/cli/azure/group#az-group-delete) comando per rimuovere il gruppo di risorse, la macchina virtuale, e tutte le risorse correlate.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata distribuita una macchina virtuale di Windows Server base. Per altre informazioni sulle macchine virtuali di Azure Stack, continuare [considerazioni sulle macchine virtuali in Azure Stack](azure-stack-vm-considerations.md).
