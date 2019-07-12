---
title: Creare una macchina virtuale Windows da un disco rigido virtuale specializzato nel portale di Azure | Microsoft Docs
description: Creare una nuova macchina virtuale Windows da un disco rigido virtuale nel portale di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: cadd4b16ab111f46e49429c6d99e0e692325b3b1
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718945"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Creare una macchina virtuale da un disco rigido virtuale usando il portale di Azure

Esistono diversi modi per creare una macchina virtuale in Azure: 

- Se si ha già un disco rigido virtuale da usare o si vuole copiare il disco rigido virtuale da una macchina virtuale esistente, è possibile creare una nuova macchina virtuale *collegando* il disco rigido virtuale alla nuova macchina virtuale come disco del sistema operativo. 

- È possibile creare una nuova macchina virtuale dal disco rigido virtuale di una macchina virtuale che è stata eliminata. Se ad esempio è presente una macchina virtuale di Azure che non funziona correttamente, è possibile eliminarla e usare il disco rigido virtuale per crearne una nuova. È possibile riutilizzare lo stesso disco rigido virtuale o crearne una copia creando uno snapshot e quindi creando un nuovo disco gestito dallo snapshot. La creazione di uno snapshot, anche se richiede qualche passaggio in più, mantiene il disco rigido virtuale originale e fornisce un fallback.

- Scegliere una macchina virtuale classica e usare il disco rigido virtuale per creare una nuova macchina virtuale che usa il modello di distribuzione di Resource Manager e i dischi gestiti. Per ottenere risultati ottimali, **arrestare** la macchina virtuale classica nel portale di Azure prima di creare lo snapshot.
 
- È possibile creare una macchina virtuale di Azure da un disco rigido virtuale locale caricandolo e collegandolo a una nuova macchina virtuale. Per caricare il disco rigido virtuale in un account di archiviazione, si usa PowerShell o un altro strumento e quindi si crea un disco gestito dal disco rigido virtuale. Per altre informazioni, vedere [Caricare un disco rigido virtuale specializzato](create-vm-specialized.md#option-2-upload-a-specialized-vhd). 

Non usare un disco specializzato se si vogliono creare più macchine virtuali. Per distribuzioni di maggiori dimensioni, [creare invece un'immagine](capture-image-resource.md) e quindi [usare tale immagine per creare più macchine virtuali](create-vm-generalized-managed.md).


## <a name="copy-a-disk"></a>Copiare un disco

Creare uno snapshot e quindi creare un disco dallo snapshot. Questa strategia consente di conservare il disco rigido virtuale originale come fallback:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Tutti i servizi** nel menu a sinistra.
2. Nella casella di ricerca **Tutti i servizi** immettere **dischi** e quindi selezionare **Dischi** per visualizzare l'elenco di dischi disponibili.
3. Selezionare il disco che si vuole usare. Verrà visualizzata la relativa pagina **Disco**.
4. Dal menu nella parte superiore selezionare **Crea snapshot**. 
5. Immettere un **Nome** per lo snapshot.
6. Scegliere un **gruppo di risorse** per lo snapshot. È possibile usare un gruppo di risorse esistente o crearne uno nuovo.
7. Per **Tipo di account** scegliere l'archiviazione **Standard (unità HDD)** o **Premium (unità SSD)** .
8. Al termine selezionare **Crea** per creare lo snapshot.
9. Dopo aver creato lo snapshot, selezionare **Crea una risorsa** nel menu a sinistra.
10. Nella casella di ricerca immettere **disco gestito** e quindi selezionare **Dischi gestiti** nell'elenco.
11. Nella pagina **Dischi gestiti** selezionare **Crea**.
12. Immettere un **nome** per il disco.
13. Scegliere un **gruppo di risorse** per il disco. È possibile usare un gruppo di risorse esistente o crearne uno nuovo. Questa selezione verrà anche usata come gruppo di risorse in cui creare la macchina virtuale dal disco.
14. Per **Tipo di account** scegliere l'archiviazione **Standard (unità HDD)** o **Premium (unità SSD)** .
15. In **Tipo di origine** assicurarsi che l'opzione **Snapshot** sia selezionata.
16. Nell'elenco a discesa **Snapshot di origine** selezionare lo snapshot da usare.
17. Apportare eventuali altre modifiche necessarie e quindi selezionare **Crea** per creare il disco.

## <a name="create-a-vm-from-a-disk"></a>Creare una macchina virtuale da un disco

Dopo aver creato il disco rigido virtuale gestito da usare, è possibile creare la macchina virtuale nel portale:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Tutti i servizi** nel menu a sinistra.
2. Nella casella di ricerca **Tutti i servizi** immettere **dischi** e quindi selezionare **Dischi** per visualizzare l'elenco di dischi disponibili.
3. Selezionare il disco che si vuole usare. Verrà aperta la relativa pagina **Disco**.
4. Nella pagina **Panoramica** assicurarsi che il valore di **STATO DISCO** sia **Scollegato**. In caso contrario, potrebbe essere necessario scollegare il disco dalla macchina virtuale o eliminare la macchina virtuale per liberare il disco.
4. Dal menu nella parte superiore della pagina selezionare **Crea macchina virtuale**.
5. Nella pagina **Informazioni di base** per la nuova macchina virtuale immettere un **nome di macchina virtuale** e selezionare un **gruppo di risorse esistente** oppure crearne uno nuovo.
6. Per **Dimensioni** selezionare **Modifica dimensioni** per accedere alla pagina **Dimensioni**.
7. Selezionare una riga di dimensioni della macchina virtuale e quindi scegliere **Seleziona**.
8. Nella pagina **Rete** è possibile scegliere di consentire al portale di creare tutte le nuove risorse oppure selezionare una **rete virtuale** e un **gruppo di sicurezza di rete** esistenti. Il portale crea sempre una nuova interfaccia di rete e un nuovo indirizzo IP pubblico per la nuova macchina virtuale. 
9. Nella pagina **Gestione** apportare eventuali modifiche alle opzioni di monitoraggio.
10. Nella pagina **Guest config** (Configurazione guest) aggiungere le estensioni necessarie.
11. Al termine, selezionare **Rivedi e crea**. 
12. Se la configurazione della macchina virtuale supera la convalida, selezionare **Crea** per avviare la distribuzione.

## <a name="next-steps"></a>Passaggi successivi

È anche possibile usare PowerShell per [caricare un disco rigido virtuale in Azure e creare una macchina virtuale specializzata](create-vm-specialized.md).


