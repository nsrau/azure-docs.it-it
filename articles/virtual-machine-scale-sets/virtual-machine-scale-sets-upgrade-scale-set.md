---
title: "Aggiornare un set di scalabilità di macchine virtuali di Azure | Microsoft Docs"
description: "Aggiornare un set di scalabilità di macchine virtuali di Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: guybo
ms.openlocfilehash: c7093e221ff8fe69ded1cfbce4f3ddeb1a195666
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="upgrade-a-virtual-machine-scale-set"></a>Aggiornare un set di scalabilità di macchine virtuali
Questo articolo descrive come eseguire un aggiornamento del sistema operativo a un set di scalabilità di macchine virtuali di Azure senza tempi di inattività. In questo contesto, un aggiornamento del sistema operativo riguarda la modifica della versione/SKU del sistema operativo oppure la modifica dell'URI di un'immagine personalizzata. L'aggiornamento senza tempi di inattività implica l'aggiornamento di una macchina virtuale alla volta o in gruppi, ad esempio un dominio di errore alla volta, anziché contemporaneamente. In questo modo, è possibile mantenere in esecuzione tutte le macchine virtuali non in fase di aggiornamento.

Per evitare ambiguità, si distinguono quattro tipi di aggiornamento del sistema operativo che è possibile eseguire:

* La modifica della versione o della SKU di un'immagine della piattaforma. Ad esempio la modifica della versione Ubuntu 14.04.2-LTS da 14.04.201506100 a 14.04.201507060 o la modifica della SKU Ubuntu 15.10/più recente a 16.04.0-LTS/più recente. Questo scenario è illustrato in questo articolo.
* La modifica dell'URI che punta a una nuova versione di un'immagine personalizzata creata (**properties** > **virtualMachineProfile** > **storageProfile** > **osDisk** > **image** > **uri**). Questo scenario è illustrato in questo articolo.
* Modifica del riferimento all'immagine di un set di scalabilità creato con i dischi gestiti di Azure.
* L'applicazione di patch del sistema operativo da una macchina virtuale (esempi di questo tipo includono l'installazione di una patch di sicurezza e l'esecuzione di Windows Update). Questo scenario è supportato ma non è trattato in questo articolo.

I set di scalabilità delle macchine virtuali che vengono distribuiti come parte di un cluster di [Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) non sono trattati qui. Per altre informazioni sulle patch di Service Fabric vedere [Applicare patch al sistema operativo Windows nel cluster di Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-patch-orchestration-application).

La sequenza di base per la modifica della versione/SKU del sistema operativo di un'immagine della piattaforma o dell'URI di un'immagine personalizzata è simile alla seguente:

1. Ottenere il modello del set di scalabilità di macchine virtuali.
2. Modificare la versione, la SKU, il riferimento all'immagine o il valore dell'URI del modello.
3. Aggiornare il modello.
4. Eseguire una chiamata *manualUpgrade* sulle macchine virtuali nel set di scalabilità. Questo passaggio è applicabile solo se la proprietà *upgradePolicy* è impostata su **Manuale** nel set di scalabilità. Se è impostata su **Automatico**, tutte le macchine virtuali vengono aggiornate contemporaneamente, con conseguente tempo di inattività.

Tenendo conto di queste informazioni, si noti come è possibile aggiornare la versione di un set di scalabilità in PowerShell e tramite l'API REST. Questi esempi riguardano il caso di un'immagine di piattaforma, ma le informazioni fornite in questo articolo saranno sufficienti a adattare il processo per un'immagine personalizzata.

## <a name="powershell"></a>PowerShell
Questo esempio aggiorna un set di scalabilità di una macchina virtuale Windows creando una nuova versione 4.0.20160229. Dopo l'aggiornamento del modello viene eseguito un aggiornamento di un'istanza di macchina virtuale alla volta.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the virtual machine scale set model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

Nel caso di un aggiornamento dell'URI per un'immagine personalizzata anziché della modifica di una versione di immagine della piattaforma, sostituire la riga "set the new version" con un comando che aggiornerà l'URI dell'immagine di origine. Ad esempio, se il set di scalabilità è stato creato senza usare i dischi gestiti di Azure, l'aggiornamento sarebbe simile al seguente:

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```

Se un set di scalabilità basato sull'immagine è stato creato usando i dischi gestiti di Azure, il riferimento all'immagine verrà aggiornato. ad esempio:

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.id = $newImageReference
```

## <a name="the-rest-api"></a>API REST
Di seguito sono riportati due esempi di Python che usano l'API REST di Azure per implementare un aggiornamento di versione del sistema operativo. Entrambi usano la libreria [azurerm](https://pypi.python.org/pypi/azurerm) semplificata della funzione wrapper dell'API REST di Azure per eseguire un'operazione GET sul modello del set di scalabilità, seguiti da un'operazione PUT con un modello aggiornato. Gli esempi esaminano le viste delle istanze della macchina virtuale per l'individuazione delle macchine virtuali tramite il dominio di aggiornamento.

### <a name="vmssupgrade"></a>Vmssupgrade
 [Vmssupgrade](https://github.com/gbowerman/vmsstools) è uno script Python utile per implementare un aggiornamento del sistema operativo per un set di scalabilità di macchine virtuali in esecuzione, un dominio di aggiornamento alla volta.

![Script Vmssupgrade per la scelta delle macchine virtuali o di un dominio di aggiornamento](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

Questo script consente di scegliere le macchine virtuali specifiche per aggiornare o specificare un dominio di aggiornamento. Supporta la modifica di una versione dell'immagine della piattaforma o la modifica dell'URI di un'immagine personalizzata.

### <a name="vmsseditor"></a>Vmsseditor
[Vmsseditor](https://github.com/gbowerman/vmssdashboard) è un editor generico per i set di scalabilità di macchine virtuali che mostra lo stato della macchina virtuale come heatmap in cui una riga rappresenta un dominio di aggiornamento. Tra le altre cose, è possibile aggiornare il modello per un set di scalabilità con una nuova versione, SKU o URI dell'immagine personalizzata e quindi selezionare i domini di errore da aggiornare. A questo scopo, tutte le macchine virtuali in questo dominio di aggiornamento vengono aggiornate al nuovo modello. In alternativa, è possibile eseguire un aggiornamento in sequenza in base alla dimensione di batch scelta.  

La schermata seguente illustra un modello di un set di scalabilità per Ubuntu 14.04-2LTS versione 14.04.201507060. Da quando è stata acquisita questa schermata, sono state aggiunte a questo strumento molte opzioni.

![Modello Vmsseditor di un set di scalabilità per Ubuntu 14.04-2LTS](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

Dopo aver selezionato **Aggiorna** e **Dettagli**, le macchine virtuali nel dominio di aggiornamento 0 avviano l'aggiornamento.

![Vmsseditor che illustra un aggiornamento in corso](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)

