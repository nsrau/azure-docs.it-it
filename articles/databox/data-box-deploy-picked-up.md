---
title: Esercitazione per restituire Azure Data Box | Microsoft Docs
description: Informazioni su come spedire Azure Data Box a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: alkohli
ms.openlocfilehash: 5b43241be4e161cd6051dce02a3574fbdb580f28
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606238"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Esercitazione: Restituire Azure Data Box e verificare il caricamento dei dati in Azure

Questa esercitazione illustra come restituire Azure Data Box e verificare che i dati siano stati caricati in Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Prerequisiti
> * Preparare per la spedizione
> * Spedire il Data Box a Microsoft
> * Verificare il caricamento dei dati in Azure
> * Cancellare i dati dal Data Box

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

- Sia stata completata l'[Esercitazione: Copiare dati in Azure Data Box Disk ed eseguire la verifica](data-box-deploy-copy-data.md). 
- I processi di copia siano stati completati. La preparazione per la spedizione non può essere eseguita se sono in corso processi di copia.

## <a name="prepare-to-ship"></a>Preparare per la spedizione

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="ship-data-box-back"></a>Rispedire il Data Box

1. Verificare che il dispositivo sia spento e i cavi scollegati. Avvolgere il cavo di alimentazione fornito con il dispositivo e assicurarlo al retro del dispositivo.
2. Assicurarsi che l'etichetta indirizzo sia visibile sul display E-ink e prenotare un corriere per il ritiro. Se l'etichetta viene danneggiata o smarrita oppure non viene visualizzata sul display E-ink, contattare il supporto tecnico Microsoft. Se consigliato dal supporto, è possibile passare a **Panoramica > Scarica etichetta di spedizione** nel portale di Azure. Scaricare l'etichetta di spedizione e applicarla sul dispositivo. 
3. Pianificare un ritiro con UPS per la restituzione del dispositivo. Per pianificare un ritiro:

    - Chiamare il servizio UPS locale (numero verde specifico del paese o area).
    - Nella chiamata specificare il numero di monitoraggio della spedizione di reso indicato sul display E-ink o sull'etichetta stampata.
    - Se non si specifica il numero di monitoraggio, il servizio UPS addebiterà un costo aggiuntivo al momento del ritiro.

    Anziché pianificare il ritiro, è anche possibile consegnare Data Box al più vicino punto di consegna.
4. Dopo che il Data Box è stato ritirato ed esaminato dal corriere, lo stato dell'ordine nel portale diventa **Ritirato**. Viene visualizzato anche un ID di traccia.

## <a name="verify-data-upload-to-azure"></a>Verificare il caricamento dei dati in Azure

Quando Microsoft riceve e analizza il dispositivo, lo stato dell'ordine diventa **Ricevuto**. Il dispositivo viene quindi sottoposto a una verifica fisica per rilevare eventuali danni o segni di manomissione.

Al termine della verifica, il Data Box viene connesso alla rete nel data center di Azure. La copia dei dati viene avviata automaticamente. A seconda delle dimensioni dei dati, l'operazione di copia potrebbe richiedere da alcune ore a giorni per il completamento. È possibile monitorare lo stato di avanzamento del processo di copia nel portale.

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

## <a name="erasure-of-data-from-data-box"></a>Cancellare i dati dal Data Box
 
Una volta completato il caricamento in Azure, i dati vengono cancellati dai dischi del Data Box in base alle [linee guida NIST SP 800-88 revisione 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Prerequisiti
> * Preparare per la spedizione
> * Spedire il Data Box a Microsoft
> * Verificare il caricamento dei dati in Azure
> * Cancellare i dati dal Data Box

Passare all'articolo successivo per informazioni su come gestire il Data Box tramite l'interfaccia utente Web locale.

> [!div class="nextstepaction"]
> [Usare l'interfaccia utente Web locale per amministrare Azure Data Box](./data-box-local-web-ui-admin.md)


