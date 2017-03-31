---
title: Gestire i nodi di calcolo del cluster HPC Pack | Microsoft Docs
description: Informazioni sugli strumenti di script di PowerShell per aggiungere, rimuovere, avviare e arrestare i nodi di calcolo del cluster HPC Pack 2012 R2 in Azure
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 4193f03b-94e9-4704-a7ad-379abde063a9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: dc9f354191b9e80ff6a01bd401a874c6998bda79
ms.lasthandoff: 03/27/2017


---
# <a name="manage-the-number-and-availability-of-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Gestire il numero e la disponibilità dei nodi di calcolo in un cluster HPC Pack in Azure
Se è stato creato un cluster HPC Pack 2012 R2 nelle macchine virtuali di Azure, potrebbe essere utile conoscere il modo in cui aggiungere, rimuovere, avviare (provisioning) o arrestare (deprovisioning) facilmente alcune macchine virtuali dei nodi di calcolo nel cluster. Per eseguire queste attività, eseguire gli script di Azure PowerShell installati nella VM del nodo head. Questi script consentono di controllare il numero e la disponibilità delle risorse del cluster HPC Pack in modo da poter controllare i costi.

> [!IMPORTANT] 
> Questo articolo si applica solo ai cluster HPC Pack 2012 R2 in Azure creati con il modello di distribuzione classico. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.
> Gli script di PowerShell descritti in questo articolo non sono disponibili in HPC Pack 2016.

## <a name="prerequisites"></a>Prerequisiti
* **Cluster HPC Pack 2012 R2 in macchine virtuali di Azure** - Creare un cluster HPC Pack 2012 R2 nel modello di distribuzione classico. È possibile automatizzare ad esempio la distribuzione usando l'immagine di macchina virtuale di HPC Pack 2012 R2 in Azure Marketplace e uno script di Azure PowerShell. Per informazioni e indicazioni sui prerequisiti, vedere [Creare un cluster HPC con lo script di distribuzione IaaS di HPC Pack](hpcpack-cluster-powershell-script.md).
  
    Dopo la distribuzione, trovare gli script di gestione dei nodi nella cartella %CCP\_HOME%bin nel nodo head. Eseguire ogni script come amministratore.
* **File delle impostazioni di pubblicazione o certificato di gestione di Azure** - È necessario eseguire una di queste operazioni sul nodo head:
  
  * **Importare il file delle impostazioni di pubblicazione di Azure**. A questo scopo, eseguire i cmdlet di Azure PowerShell seguenti nel nodo head:
    
    ```PowerShell
    Get-AzurePublishSettingsFile
    
    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```
  * **Configurare il certificato di gestione di Azure nel nodo head**. Se si dispone del file con estensione cer, importarlo nell'archivio certificati CurrentUser\My certificate e quindi eseguire il seguente cmdlet di Azure PowerShell per l'ambiente Azure (AzureCloud o AzureChinaCloud):
    
    ```PowerShell
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## <a name="add-compute-node-vms"></a>Aggiungere le macchine virtuali dei nodi di calcolo
Aggiungere nodi di calcolo con lo script **Add-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Sintassi
```PowerShell
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### <a name="parameters"></a>Parametri
* **ServiceName** - Nome del servizio cloud a cui vengono aggiunte le nuove macchine virtuali dei nodi di calcolo.
* **ImageName** - Nome dell'immagine della macchina virtuale di Azure, che si può ottenere nel portale di Azure classico o con il cmdlet di Azure PowerShell **Get-AzureVMImage**. L'immagine deve soddisfare i seguenti requisiti:
  
  1. Deve essere installato un sistema operativo Windows.
  2. HPC Pack deve essere installato nel ruolo del nodo di calcolo.
  3. L'immagine deve essere un'immagine privata nella categoria utente, non un'immagine di macchina virtuale di Azure pubblica.
* **Quantity** - Numero delle macchine virtuali dei nodi di calcolo da aggiungere.
* **InstanceSize** - Dimensioni delle macchine virtuali dei nodi di calcolo.
* **DomainUserName** - Nome utente di dominio, da usare per aggiungere le nuove macchine virtuali al dominio.
* **DomainUserPassword** - Password dell'utente di dominio.
* **NodeNameSeries** (facoltativo) - Modello di denominazione per i nodi di calcolo. Il formato deve essere &lt;*Nome\_Radice*&gt;&lt;*Numero\_Iniziale*&gt;%. Ad esempio, MyCN%10% significa una serie di nomi dei nodi di calcolo a partire da MyCN11. Se non specificato, lo script usa la serie di nomi dei nodi di calcolo configurata nel cluster HPC.

### <a name="example"></a>Esempio
L'esempio seguente aggiunge 20 VM dei nodi di calcolo di grandi dimensioni nel servizio cloud *hpcservice1* in base all'immagine di VM *hpccnimage1*.

```PowerShell
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## <a name="remove-compute-node-vms"></a>Rimuovere le macchine virtuali dei nodi di calcolo
Rimuovere i nodi di calcolo con lo script **Remove-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Sintassi
```PowerShell
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### <a name="parameters"></a>Parametri
* **Name** - Nomi dei nodi del cluster da rimuovere. Sono supportati caratteri jolly. Il nome del set di parametri è Name. Non è possibile specificare entrambi i parametri **Name** e **Node**.
* **Node** - Oggetto HpcNode relativo ai nodi da rimuovere, che si può ottenere tramite il cmdlet di PowerShell per HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Il nome del set di parametri è Node. Non è possibile specificare entrambi i parametri **Name** e **Node**.
* **DeleteVHD** (facoltativo) - Impostazione per eliminare i dischi associati per le macchine virtuali rimosse.
* **Force** (facoltativo) - Impostazione per impostare offline i nodi HPC prima di rimuoverli.
* **Confirm** (facoltativo) - Richiesta di conferma prima dell'esecuzione di un comando.
* **WhatIf** - Impostazione per descrivere le conseguenze dell'esecuzione di un comando senza eseguirlo effettivamente.

### <a name="example"></a>Esempio
L'esempio seguente porta forzatamente offline i nodi con nomi che iniziano con *HPCNode-CN-* e quindi rimuove i nodi e i dischi associati corrispondenti.

```PowerShell
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## <a name="start-compute-node-vms"></a>Avviare le macchine virtuali dei nodi di calcolo
Avviare i nodi di calcolo con lo script **Start-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Sintassi
```PowerShell
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### <a name="parameters"></a>Parametri
* **Name** - Nomi dei nodi del cluster da avviare. Sono supportati caratteri jolly. Il nome del set di parametri è Name. Non è possibile specificare entrambi i parametri **Name** e **Node**.
* **Node**- Oggetto HpcNode relativo ai nodi da avviare, che può essere ottenuto tramite il cmdlet di PowerShell per HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Il nome del set di parametri è Node. Non è possibile specificare entrambi i parametri **Name** e **Node**.

### <a name="example"></a>Esempio
L'esempio seguente avvia i nodi i cui nomi iniziano con *HPCNode-CN-*.

```PowerShell
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## <a name="stop-compute-node-vms"></a>Arrestare le macchine virtuali dei nodi di calcolo
Arrestare i nodi di calcolo con lo script **Stop-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Sintassi
```PowerShell
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### <a name="parameters"></a>Parametri
* **Name**- Nomi dei nodi del cluster da arrestare. Sono supportati caratteri jolly. Il nome del set di parametri è Name. Non è possibile specificare entrambi i parametri **Name** e **Node**.
* **Node** - Oggetto HpcNode relativo ai nodi da arrestare, che si può ottenere tramite il cmdlet di PowerShell per HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Il nome del set di parametri è Node. Non è possibile specificare entrambi i parametri **Name** e **Node**.
* **Force** (facoltativo) - Impostazione per impostare offline i nodi HPC prima di arrestarli.

### <a name="example"></a>Esempio
L'esempio seguente imposta offline i nodi i cui nomi iniziano con *HPCNode-CN-* e li arresta.

```PowerShell
Stop-HPCIaaSNode.ps1 –Name HPCNodeCN-* -Force
```

## <a name="next-steps"></a>Passaggi successivi
* Per aumentare o ridurre automaticamente i nodi del cluster in base al carico di lavoro corrente dei processi e delle attività del cluster, vedere l'articolo [Aumentare e ridurre automaticamente le risorse del cluster HPC Pack in Azure in base al carico di lavoro del cluster](hpcpack-cluster-node-autogrowshrink.md).


