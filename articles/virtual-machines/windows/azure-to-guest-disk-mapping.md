---
title: Come eseguire il mapping di dischi di Azure a dischi Guest di macchine virtuali Windows
description: Come determinare i dischi di Azure che hanno sottoposto a dischi Guest di una macchina virtuale Windows.
author: timbasham
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.openlocfilehash: f16e34f372016f284d4af79443e84d9d5cdea957
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523585"
---
# <a name="how-to-map-azure-disks-to-windows-vm-guest-disks"></a>Come eseguire il mapping di dischi di Azure a dischi Guest di macchine virtuali Windows

Potrebbe essere necessario determinare i dischi di Azure che eseguono il backup dei dischi Guest di una macchina virtuale. In alcuni scenari è possibile confrontare le dimensioni del disco o del volume con le dimensioni dei dischi di Azure collegati. Negli scenari in cui sono presenti più dischi di Azure della stessa dimensione collegati alla macchina virtuale, è necessario usare il numero di unità logica (LUN) dei dischi dati. 

## <a name="what-is-a-lun"></a>Che cos'è un LUN?

Un numero di unità logica (LUN) è un numero usato per identificare un dispositivo di archiviazione specifico. A ogni dispositivo di archiviazione viene assegnato un identificatore numerico univoco, a partire da zero. Il percorso completo di un dispositivo è rappresentato dal numero di bus, dal numero di ID di destinazione e dal numero di unità logica (LUN). 

Ad esempio: ***numero bus 0, ID destinazione 0, lun 3***

Per questo esercizio, è sufficiente usare il LUN.

## <a name="finding-the-lun"></a>Ricerca del LUN

Esistono due metodi per trovare il LUN, che è possibile scegliere dipende da se si usa o meno [spazi di archiviazione](https://docs.microsoft.com/windows-server/storage/storage-spaces/overview) .

### <a name="disk-management"></a>Gestione disco

Se non si usano i pool di archiviazione, è possibile usare [Gestione disco](https://docs.microsoft.com/windows-server/storage/disk-management/overview-of-disk-management) per trovare il lun.

1. Connettersi alla macchina virtuale e aprire Gestione disco a. Fare clic con il pulsante destro del mouse sul pulsante Start e scegliere "Gestione disco" a. È anche possibile digitare `diskmgmt.msc` nella casella Avvia ricerca
1. Nel riquadro inferiore fare clic con il pulsante destro del mouse su uno dei dischi e scegliere "proprietà".
1. Il LUN verrà elencato nella proprietà "location" nella scheda "General" (generale)

### <a name="storage-pools"></a>Pool di archiviazione

1. Connettersi alla macchina virtuale e aprire Server Manager
1. Selezionare "Servizi file e archiviazione", "volumi", "pool di archiviazione"
1. Nell'angolo in basso a destra di Server Manager sarà presente una sezione "dischi fisici". I dischi che compongono il pool di archiviazione sono elencati qui, oltre al LUN per ogni disco.

## <a name="finding-the-lun-for-the-azure-disks"></a>Ricerca del LUN per i dischi di Azure

È possibile individuare il LUN per un disco di Azure usando il portale di Azure, l'interfaccia della riga di comando di Azure o Azure PowerShell

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Ricerca di un LUN del disco di Azure nell'portale di Azure

1. Nella portale di Azure selezionare "macchine virtuali" per visualizzare un elenco delle macchine virtuali
1. Selezionare la macchina virtuale.
1. Selezionare "dischi"
1. Selezionare un disco dati dall'elenco dei dischi collegati.
1. Il LUN del disco verrà visualizzato nel riquadro dei dettagli del disco. Il LUN visualizzato qui è correlato ai Lun cercati nel guest usando Device Manager o Server Manager.

### <a name="finding-an-azure-disks-lun-using-azure-cli-or-azure-powershell"></a>Trovare un LUN del disco di Azure usando l'interfaccia della riga di comando di Azure o Azure PowerShell

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
$vm.StorageProfile.DataDisks | ft
```
---
