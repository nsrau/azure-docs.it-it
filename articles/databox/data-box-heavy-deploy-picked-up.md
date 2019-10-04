---
title: Esercitazione per restituire Azure Data Box Heavy | Microsoft Docs
description: Informazioni su come spedire Azure Data Box Heavy a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: e438fb38afb649f6f4c7f595059ef64800977242
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240340"
---
::: zone target = "docs"

# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Esercitazione: Restituire Azure Data Box Heavy e verificare il caricamento dei dati in Azure

::: zone-end

::: zone target = "chromeless"

# <a name="return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Restituire Azure Data Box Heavy e verificare il caricamento dei dati in Azure

::: zone-end

::: zone target = "docs"

Questa esercitazione illustra come restituire Azure Data Box Heavy e verificare che i dati siano stati caricati in Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Prerequisiti
> * Preparare per la spedizione
> * Spedire Data Box Heavy a Microsoft
> * Verificare il caricamento dei dati in Azure
> * Cancellare i dati da Data Box Heavy

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

- Aver completato l'esercitazione descritta in [Esercitazione: Copiare dati in Azure Data Box Disk ed eseguire la verifica](data-box-heavy-deploy-copy-data.md).
- I processi di copia siano stati completati. La preparazione per la spedizione non può essere eseguita se sono in corso processi di copia.


## <a name="prepare-to-ship"></a>Preparare per la spedizione

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

::: zone-end

::: zone target = "chromeless"

## <a name="prepare-to-ship"></a>Preparare per la spedizione

Prima di eseguire le operazioni di preparazione per la spedizione, assicurarsi che i processi di copia siano stati completati.

1. Passare alla pagina Prepara per la spedizione nell'interfaccia utente Web locale e avviare la preparazione della spedizione.
2. Disattivare il dispositivo dall'interfaccia utente Web locale. Rimuovere i cavi dal dispositivo.

A questo punto è possibile rispedire il dispositivo.

::: zone-end

## <a name="ship-data-box-heavy-back"></a>Restituire Data Box Heavy

1. Verificare che il dispositivo sia spento e che tutti i cavi siano stati rimossi. Avvolgere i 4 cavi di alimentazione e collocarli nel vano accessibile dal retro del dispositivo.
2. Il dispositivo spedisce il carico LTL tramite FedEx negli Stati Uniti e DHL nell'Unione Europea

    1. Contattare il team [Data Box Operations](mailto:DataBoxOps@microsoft.com) per informarli sul ritiro e ottenere l'etichetta per la spedizione di reso.
    2. Chiamare il corriere al numero locale per prenotare il ritiro.
    3. Assicurarsi che l'etichetta indirizzo sia chiaramente visibile all'esterno della spedizione.
    4. Verificare che le etichette della spedizione precedente siano state rimosse dal dispositivo.
3. Quando Data Box Heavy viene ritirato ed esaminato dal corriere, lo stato dell'ordine nel portale diventa **Ritirato**. Viene visualizzato anche un ID di traccia.

::: zone target = "docs"

## <a name="verify-data-upload-to-azure"></a>Verificare il caricamento dei dati in Azure

Quando Microsoft riceve e analizza il dispositivo, lo stato dell'ordine diventa **Ricevuto**. Il dispositivo viene quindi sottoposto a una verifica fisica per rilevare eventuali danni o segni di manomissione.

Al termine della verifica, Data Box Heavy viene connesso alla rete nel data center di Azure. La copia dei dati viene avviata automaticamente. A seconda delle dimensioni dei dati, l'operazione di copia potrebbe richiedere da alcune ore a giorni per il completamento. È possibile monitorare lo stato di avanzamento del processo di copia nel portale.

Al termine della copia, lo stato dell'ordine diventa **Completato**.

Verificare che i dati siano stati caricati in Azure prima di eliminarli dall'origine. I dati possono trovarsi in:

- Account di archiviazione di Azure. Quando si copiano i dati in Data Box, a seconda del tipo, i dati vengono caricati in uno dei percorsi seguenti nell'account di archiviazione di Azure.

  - Per i BLOB in blocchi e i BLOB di pagine: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Per File di Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    In alternativa, è possibile passare all'account di archiviazione di Azure nel portale di Azure e procedere da questa posizione.

- Gruppi di risorse di dischi gestiti. Quando si creano dischi gestiti, i dischi rigidi virtuali vengono caricati come BLOB di pagine e quindi convertiti in dischi gestiti. I dischi gestiti vengono associati ai gruppi di risorse specificati al momento della creazione dell'ordine. 

    - Se la copia nei dischi gestiti in Azure ha avuto esito positivo, è possibile passare a **Dettagli ordine** nel portale di Azure e prendere nota dei gruppi di risorse specificati per i dischi gestiti.

        ![Identificare i gruppi di risorse dei dischi gestiti](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Passare al gruppo di risorse annotato e individuare i dischi gestiti.

        ![Dischi gestiti associati ai gruppi di risorse](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Se è stato copiato un VHDX oppure un disco rigido virtuale differenziale o dinamico, il caricamento verrà effettuato nell'account di archiviazione di staging come BLOB di pagine, ma la conversione del disco rigido virtuale in disco gestito avrà esito negativo. Passare ad **Account di archiviazione > BLOB** di staging e quindi selezionare il contenitore appropriato, ovvero Unità SSD Standard, HDD Standard o Unità SSD Premium. I dischi rigidi virtuali verranno caricati come BLOB di pagine nell'account di archiviazione di staging.
    
::: zone-end

::: zone target = "chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificare il caricamento dei dati in Azure

Quando il dispositivo Data Box Heavy viene connesso alla rete del data center di Azure, il caricamento dei dati in Azure si avvia automaticamente. Il servizio Data Box invia notifica del completamento della copia dei dati tramite il portale di Azure.

- Controllare i log degli errori per rilevare eventuali errori e intraprendere le azioni appropriate.
- Verificare la presenza dei dati negli account di archiviazione prima di eliminarli dall'origine.

::: zone-end

## <a name="erasure-of-data-from-data-box-heavy"></a>Cancellare i dati da Data Box Heavy
 
Una volta completato il caricamento in Azure, i dati vengono cancellati dai dischi del Data Box in base alle [linee guida NIST SP 800-88 revisione 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). Al termine della cancellazione, è possibile [scaricare la cronologia degli ordini](data-box-portal-admin.md#download-order-history).

::: zone target = "docs"

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Prerequisiti
> * Preparare per la spedizione
> * Spedire Data Box Heavy a Microsoft
> * Verificare il caricamento dei dati in Azure
> * Cancellare i dati da Data Box Heavy

Passare all'articolo successivo per informazioni su come gestire Data Box Heavy tramite l'interfaccia utente Web locale.

> [!div class="nextstepaction"]
> [Usare l'interfaccia utente Web locale per amministrare Azure Data Box](./data-box-local-web-ui-admin.md)

::: zone-end


