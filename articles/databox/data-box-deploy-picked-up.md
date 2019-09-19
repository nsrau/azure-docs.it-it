---
title: Esercitazione per restituire Azure Data Box | Microsoft Docs
description: Informazioni su come spedire Azure Data Box a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 194b90ab27d02c1fa3eb05bb3ddd78395d351599
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898184"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Esercitazione: Restituire Azure Data Box e verificare il caricamento dei dati in Azure

::: zone-end

::: zone target="chromeless"

# <a name="return-data-box-and-verify-data-upload-to-azure"></a>Restituire Data Box e verificare il caricamento dei dati in Azure

::: zone-end

::: zone target="docs"

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

::: zone-end

::: zone target="chromeless"

Dopo aver completato la copia dei dati, vengono eseguite le operazioni di preparazione e spedizione del dispositivo. Quando il dispositivo viene recapitato presso il data center di Azure, i dati vengono caricati automaticamente in Azure.

## <a name="prepare-to-ship"></a>Preparare per la spedizione

Prima di eseguire le operazioni di preparazione per la spedizione, assicurarsi che i processi di copia siano stati completati.

1. Passare alla pagina **Prepara per la spedizione** nell'interfaccia utente Web locale e avviare la preparazione della spedizione. 
2. Disattivare il dispositivo dall'interfaccia utente Web locale. Rimuovere i cavi dal dispositivo. 

I passaggi successivi dipendono dalla destinazione di restituzione del dispositivo.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Rispedire il Data Box

Assicurarsi che la copia dei dati sul dispositivo sia completa e che la **preparazione per la spedizione** sia stata eseguita correttamente. In base all'area in cui si spedisce il dispositivo, la procedura è diversa.

::: zone-end

## <a name="in-us-canada-europetabin-us-canada-europe"></a>[Stati Uniti, Canada o Europa](#tab/in-us-canada-europe)

Eseguire la procedura seguente se si restituisce il dispositivo negli Stati Uniti, in Canada on in Europa.

1. Verificare che il dispositivo sia spento e i cavi scollegati. 
2. Avvolgere il cavo di alimentazione fornito con il dispositivo e assicurarlo al retro del dispositivo.
3. Assicurarsi che l'etichetta indirizzo sia visibile sul display E-ink e prenotare un corriere per il ritiro. Se l'etichetta viene danneggiata o smarrita oppure non viene visualizzata sul display E-ink, contattare il supporto tecnico Microsoft. Se consigliato dal supporto, è possibile passare a **Panoramica > Scarica etichetta di spedizione** nel portale di Azure. Scaricare l'etichetta di spedizione e applicarla sul dispositivo. 
4. Pianificare un ritiro con UPS per la restituzione del dispositivo. Per pianificare un ritiro:

    - Chiamare il servizio UPS locale (numero verde specifico del paese o area).
    - Nella chiamata specificare il numero di monitoraggio della spedizione di reso indicato sul display E-ink o sull'etichetta stampata.
    - Se non si specifica il numero di monitoraggio, il servizio UPS addebiterà un costo aggiuntivo al momento del ritiro.

    Anziché pianificare il ritiro, è anche possibile consegnare Data Box al più vicino punto di consegna.
4. Dopo che il Data Box è stato ritirato ed esaminato dal corriere, lo stato dell'ordine nel portale diventa **Ritirato**. Viene visualizzato anche un ID di traccia.


## <a name="in-australiatabin-australia"></a>[Australia](#tab/in-australia)

I datacenter di Azure in Australia prevedono una notifica sulla sicurezza aggiuntiva. Tutte le spedizioni in ingresso devono prevedere una notifica anticipata. Seguire questa procedura per la spedizione in Australia.


1. Conservare la scatola originale usata per spedire il dispositivo per la restituzione.
2. Assicurarsi che la copia dei dati sul dispositivo sia completa e che la **preparazione per la spedizione** sia stata eseguita correttamente.
3. Spegnere il dispositivo e rimuovere i cavi.
4. Avvolgere il cavo di alimentazione fornito con il dispositivo e assicurarlo al retro del dispositivo.
5. Scrivere un messaggio di posta elettronica a Quantium Solutions per richiedere un ritiro. Vedere il numero di riferimento del servizio specificato nel portale di Azure. Usare il modello di messaggio e-mail seguente: *Richiesta di etichetta per la spedizione inversa con codice TAU*. Assicurarsi di includere i seguenti dettagli nel messaggio di posta elettronica: 

    ```
    To: Azure@quantiumsolutions.com
    Subject: Pickup request for Azure｜Reference number：XXX XXX XXX
    Body: 
    - Company name：
    - Address:
    - Contact name:
    - Contact number:
    - Requested pickup date: mm/dd
    ```
6. Quantium Solutions Australia invierà un'etichetta per la spedizione di ritorno.
7. Stampare l'etichetta di restituzione e applicarla sulla scatola per la spedizione.
8. Consegnare il pacco al corriere.

Se necessario, è possibile inviare un messaggio e-mail al supporto di Quantium Solutions all'indirizzo Azure@quantiumsolutions.com o telefonare.

Per richiedere informazioni riguardanti l'ordine al telefono:

- Prima di tutto, inviare un messaggio di posta elettronica per il ritiro.
- Fornire il nome dell'ordine al telefono.

## <a name="in-japantabin-japan"></a>[Giappone](#tab/in-japan) 

1. Conservare la scatola originale usata per spedire il dispositivo per la restituzione.
2. Spegnere il dispositivo e rimuovere i cavi.
3. Avvolgere il cavo di alimentazione fornito con il dispositivo e assicurarlo al retro del dispositivo.
4. Sulla lettera di vettura, nelle informazioni sul mittente, indicare il nome dell'azienda e l'indirizzo.
5. Inviare un messaggio di posta elettronica a Quantium Solutions usando il modello di messaggio di posta elettronica seguente.

    - Se la lettera di vettura di Japan Post Chakubarai non era inclusa o risulta mancante, indicarlo nel messaggio di posta elettronica. Quantium Solutions Japan richiederà a Japan Post di portare la lettera di vettura al momento del ritiro.
    - In caso di più ordini, inviare messaggi di posta elettronica per garantire prelievi singoli.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

3. Si riceverà una conferma tramite posta elettronica da Quantium Solutions dopo la prenotazione di un prelievo. La conferma tramite posta elettronica include anche informazioni sulla lettera di vettura di Chakubarai.

Se necessario, è possibile contattare il supporto di Quantium Solutions (in lingua giapponese) come segue: 

- Posta elettronica：Customerservice.JP@quantiumsolutions.com 
- Telefono：03-5755-0150 

::: zone target="docs"

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

::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificare il caricamento dei dati in Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

::: zone-end

## <a name="erasure-of-data-from-data-box"></a>Cancellare i dati dal Data Box
 
Una volta completato il caricamento in Azure, i dati vengono cancellati dai dischi del Data Box in base alle [linee guida NIST SP 800-88 revisione 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone target="docs"

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

::: zone-end


