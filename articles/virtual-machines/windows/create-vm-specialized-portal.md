---
title: Creare una macchina virtuale Windows da un disco rigido virtuale specializzato nel portale di Azure | Microsoft Docs
description: Creare una nuova macchina virtuale Windows da un disco rigido virtuale nel portale di Azure.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: cynthn
ms.openlocfilehash: 33e94777047ea8a116ae6f393439521356a53509
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2018
---
# <a name="create-a-vm-from-a-vhd-using-the-azure-portal"></a>Creare una macchina virtuale da un disco rigido virtuale con il portale di Azure


Per creare macchine virtuali in Azure, è possibile scegliere tra varie alternative. Se si ha già un disco rigido virtuale da usare o si vuole copiare il disco rigido virtuale da una macchina virtuale esistente, è possibile creare una nuova macchina virtuale collegando il disco rigido virtuale come disco del sistema operativo. Con questo processo il disco rigido virtuale viene *collegato* a una nuova macchina virtuale come disco del sistema operativo.

È anche possibile creare una nuova macchina virtuale dal disco rigido virtuale di una macchina virtuale che è stata eliminata. Ad esempio, è presente una macchina virtuale di Azure che non funziona correttamente, è possibile eliminarla e usare il disco rigido virtuale per crearne una nuova. È possibile riutilizzare lo stesso disco rigido virtuale o crearne una copia creando uno snapshot e quindi creando un nuovo disco gestito dallo snapshot. Questa procedura richiede qualche passaggio in più, ma consente di conservare il disco rigido virtuale originale e di avere a disposizione uno snapshot per eseguire il fallback in caso di necessità.

È disponibile una macchina virtuale locale che si vuole usare per creare una macchina virtuale in Azure. È possibile caricare il disco rigido virtuale e collegarlo a una nuova macchina virtuale. Per caricare un disco rigido virtuale, è necessario usare PowerShell o un altro strumento per caricarlo in un account di archiviazione e quindi creare un disco gestito dal disco rigido virtuale. Per altre informazioni, vedere [Caricare un disco rigido virtuale specializzato](create-vm-specialized.md#option-2-upload-a-specialized-vhd)

Non usare questo metodo se si vuole usare una macchina virtuale o un disco rigido virtuale per creare più macchine virtuali. Per distribuzioni di maggiori dimensioni è consigliabile [creare un'immagine](capture-image-resource.md) e quindi [usare tale immagine per creare più macchine virtuali](create-vm-generalized-managed.md).


## <a name="copy-a-disk"></a>Copiare un disco

Creare uno snapshot e quindi creare un disco dallo snapshot. In questo modo si potrà conservare il disco rigido virtuale originale come fallback.

1. Nel menu a sinistra fare clic su **Tutte le risorse**.
2. Nell'elenco a discesa **Tutti i tipi** deselezionare **Seleziona tutto** e quindi scorrere verso il basso e selezionare **Dischi** per trovare i dischi disponibili.
3. Fare clic sul disco che si vuole usare. Viene visualizzata la pagina **Panoramica** relativa al disco.
4. Nel menu in alto nella pagina Panoramica fare clic su **+ Crea snapshot**. 
5. Digitare un nome per lo snapshot.
6. Scegliere un **gruppo di risorse** per lo snapshot. È possibile usare un gruppo di risorse esistente o crearne uno nuovo.
7. Scegliere se usare l'archiviazione Standard (HDD) o Premium (SDD).
8. Al termine fare clic su **Crea** per creare lo snapshot.
9. Dopo aver creato lo snapshot, fare clic su **+ Crea una risorsa** nel menu a sinistra.
10. Nella barra di ricerca digitare **disco gestito** e selezionare **Dischi gestiti** nell'elenco.
11. Nella pagina **Dischi gestiti** fare clic su **Crea**.
12. Digitare un nome per il disco.
13. Scegliere un **gruppo di risorse** per il disco. È possibile usare un gruppo di risorse esistente o crearne uno nuovo. Sarà anche il gruppo di risorse in cui creare la macchina virtuale dal disco.
14. Scegliere se usare l'archiviazione Standard (HDD) o Premium (SDD).
15. In **Tipo di origine** assicurarsi che l'opzione **Snapshot** sia selezionata.
16. Nell'elenco a discesa **Snapshot di origine** selezionare lo snapshot da usare.
17. Apportare eventuali altre modifiche necessarie e quindi fare clic su **Crea** per creare il disco.

## <a name="create-a-vm-from-a-disk"></a>Creare una macchina virtuale da un disco

Dopo aver creato il disco rigido virtuale gestito da usare, è possibile creare la macchina virtuale nel portale.

1. Nel menu a sinistra fare clic su **Tutte le risorse**.
2. Nell'elenco a discesa **Tutti i tipi** deselezionare **Seleziona tutto** e quindi scorrere verso il basso e selezionare **Dischi** per trovare i dischi disponibili.
3. Fare clic sul disco che si vuole usare. Viene visualizzata la pagina **Panoramica** relativa al disco.
Nella pagina Panoramica assicurarsi che il valore di **STATO DISCO** sia **Scollegato**. In caso contrario, potrebbe essere necessario scollegare il disco dalla macchina virtuale o eliminare la macchina virtuale per liberare il disco.
4. Nel menu nella parte superiore del riquadro fare clic su **+ Crea macchina virtuale**.
5. Nella pagina **Informazioni di base** per la nuova macchina virtuale digitare un nome e selezionare un gruppo di risorse esistente oppure crearne uno nuovo.
6. Nella pagina **Dimensioni** scegliere le dimensioni per la macchina virtuale e quindi fare clic su **Seleziona**.
7. Nella pagina **Impostazioni** è possibile scegliere di consentire al portale di creare tutte le nuove risorse oppure selezionare una **rete virtuale** e un **gruppo di sicurezza di rete** esistenti. Il portale crea sempre una nuova scheda di interfaccia di rete e un nuovo indirizzo IP pubblico per la nuova macchina virtuale. 
8. Apportare eventuali modifiche alle opzioni di monitoraggio e, se necessario, aggiungere le estensioni.
9. Al termine, fare clic su **OK**. 
10. Se la configurazione della macchina virtuale supera la convalida, fare clic su **OK** per avviare la distribuzione.

## <a name="next-steps"></a>Passaggi successivi

È anche possibile usare PowerShell per [caricare un disco rigido virtuale in Azure e creare una macchina virtuale specializzata](create-vm-specialized.md).


