---
title: Creare una macchina virtuale Windows nello Stack di Azure mediante Azure CLI | Documenti Microsoft
description: Informazioni su come creare una macchina virtuale Windows sullo Stack di Azure mediante Azure CLI
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: E26B246E-811D-44C9-9BA6-2B3CE5B62E83
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: ea972db9ce3488d9a46a7d059714c8bbe820d47d
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="create-a-windows-virtual-machine-on-azure-stack-using-azure-cli"></a>Creare una macchina virtuale Windows nello Stack di Azure mediante Azure CLI

CLI di Azure viene utilizzato per creare e gestire le risorse di Azure Stack dalla riga di comando. Questa guida descrive l'utilizzo di CLI di Azure per creare una macchina virtuale di Windows Server 2016 nello Stack di Azure. Una volta creata la macchina virtuale, verrà connettersi con Desktop remoto, installare IIS, quindi visualizzare il sito Web predefinito. 

## <a name="prerequisites"></a>Prerequisiti 

* Verificare che l'operatore di Azure Stack ha aggiunto l'immagine di "Windows Server 2016" nel Marketplace dello Stack di Azure.  

* Stack di Azure richiede una versione specifica di CLI di Azure per creare e gestire le risorse. Se non è configurato per Azure Stack CLI di Azure, seguire i passaggi per [installare e configurare Azure CLI](azure-stack-connect-cli.md).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui Stack di Azure le risorse vengono distribuite e gestite. Il kit di sviluppo o lo Stack di Azure integrato system, eseguire il [gruppo az creare](/cli/azure/group#az_group_create) comando per creare un gruppo di risorse. Sono stati assegnati valori per tutte le variabili in questo documento, è possibile utilizzarli così come sono oppure assegnare un valore diverso. Nell'esempio seguente viene creato un gruppo di risorse denominato myResourceGroup nel percorso locale.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una VM con il comando [az vm create](/cli/azure/vm#az_vm_create). L'esempio seguente crea una macchina virtuale denominata myVM. L'esempio Usa Demouser per un nome utente amministrativo e Demouser@123 come password. Aggiornare i valori in modo che siano appropriati all'ambiente. Questi valori sono necessari quando ci si connette alla macchina virtuale.

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

Quando viene creata la macchina virtuale, annotare il *PublicIPAddress* parametro che viene restituito, che verrà utilizzato per accedere alla macchina virtuale.
 
## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web

Per impostazione predefinita, sono consentite solo le connessioni RDP a una macchina virtuale Windows distribuita nello Stack di Azure. Se si intende usare questa macchina virtuale come un server Web, è necessario aprire la porta 80 da Internet. Usare il comando [az vm open-port](/cli/azure/vm#open-port) per aprire la porta.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale

Usare il comando seguente per creare una sessione desktop remoto con la macchina virtuale. Sostituire l'indirizzo IP con l'indirizzo IP pubblico della macchina virtuale. Quando richiesto, immettere le credenziali utilizzate durante la creazione della macchina virtuale.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Installare IIS tramite PowerShell

Dopo avere eseguito l'accesso alla macchina virtuale di Azure, è possibile usare una singola riga di codice di PowerShell per installare IIS e abilitare la regola del firewall locale per consentire il traffico Web. Aprire un prompt di PowerShell ed eseguire questo comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Visualizzare la pagina iniziale di IIS

Dopo l'installazione di IIS e l'apertura della porta 80 nella macchina virtuale da Internet, è possibile usare il Web browser preferito per visualizzare la pagina iniziale predefinita di IIS. Assicurarsi di usare l'indirizzo IP pubblico descritto in precedenza per passare alla pagina predefinita. 

![Sito IIS predefinito](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png) 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [az group delete](/cli/azure/group#az_group_delete) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida rapida, aver distribuito una semplice macchina virtuale di Windows. Per ulteriori informazioni sulle macchine virtuali di Azure Stack, continuare a [considerazioni per le macchine virtuali in Azure Stack](azure-stack-vm-considerations.md).
