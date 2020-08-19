---
title: Esercitazione per restituire Azure Data Box nell'ordine di esportazione| Microsoft Docs
description: Informazioni su come spedire Azure Data Box a Microsoft al termine dell'ordine di esportazione
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/21/2020
ms.author: alkohli
ms.openlocfilehash: 04e4394e6a439c923558ef90e13c14c0adaa4020
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142112"
---
# <a name="tutorial-return-azure-data-box-preview"></a>Esercitazione: Restituire Azure Data Box (anteprima)

Questa esercitazione descrive come restituire Azure Data Box e come i dati vengono cancellati dopo la ricezione del dispositivo nel data center di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> * Prerequisiti
> * Preparare per la spedizione
> * Spedire il Data Box a Microsoft
> * Cancellare i dati dal Data Box

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

* Sia stata completata l'[Esercitazione: Copiare i dati da Azure Data Box](data-box-deploy-export-copy-data.md).
* I processi di copia siano stati completati. La preparazione per la spedizione non può essere eseguita se sono in corso processi di copia.

## <a name="prepare-to-ship"></a>Preparare per la spedizione

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

I passaggi successivi dipendono dalla destinazione di restituzione del dispositivo.

## <a name="ship-data-box-back"></a>Rispedire il Data Box

Assicurarsi che la copia dei dati dal dispositivo sia completa e che la **preparazione per la spedizione** sia stata eseguita correttamente. In base all'area in cui si spedisce il dispositivo, la procedura è diversa.

## <a name="us-canada-europe"></a>[Stati Uniti, Canada o Europa](#tab/in-us-canada-europe)

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

## <a name="australia"></a>[Australia](#tab/in-australia)

I datacenter di Azure in Australia prevedono una notifica sulla sicurezza aggiuntiva. Tutte le spedizioni in ingresso devono prevedere una notifica anticipata. Seguire questa procedura per la spedizione in Australia.

1. Conservare la scatola originale usata per spedire il dispositivo per la restituzione.
2. Assicurarsi che la copia dei dati sul dispositivo sia completa e che la **preparazione per la spedizione** sia stata eseguita correttamente.
3. Spegnere il dispositivo e rimuovere i cavi.
4. Avvolgere il cavo di alimentazione fornito con il dispositivo e assicurarlo al retro del dispositivo.
5. Prenotare un ritiro online seguendo il [collegamento a DHL](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference).

## <a name="japan"></a>[Giappone](#tab/in-japan)

1. Conservare la scatola originale usata per spedire il dispositivo per la restituzione.
2. Spegnere il dispositivo e rimuovere i cavi.
3. Avvolgere il cavo di alimentazione fornito con il dispositivo e assicurarlo al retro del dispositivo.
4. Sulla lettera di vettura, nelle informazioni sul mittente, indicare il nome dell'azienda e l'indirizzo.
5. Inviare un messaggio di posta elettronica a Quantium Solutions usando il modello di messaggio di posta elettronica seguente.

    * Se la lettera di vettura di Japan Post Chakubarai non era inclusa o risulta mancante, indicarlo nel messaggio di posta elettronica. Quantium Solutions Japan richiederà a Japan Post di portare la lettera di vettura al momento del ritiro.
    * In caso di più ordini, inviare messaggi di posta elettronica per garantire prelievi singoli.

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

6. Si riceverà una conferma tramite posta elettronica da Quantium Solutions dopo la prenotazione di un prelievo. La conferma tramite posta elettronica include anche informazioni sulla lettera di vettura di Chakubarai.

Se necessario, è possibile contattare il supporto di Quantium Solutions (in lingua giapponese) come segue: 

* Posta elettronica：Customerservice.JP@quantiumsolutions.com
* Telefono：03-5755-0150

## <a name="singapore"></a>[Singapore](#tab/in-singapore)

1. Conservare la scatola originale usata per spedire il dispositivo per la restituzione.
2. Annotare il numero di tracciabilità (visualizzato come numero di riferimento nella pagina Prepara per la spedizione dell'interfaccia utente Web locale di Data Box). Il numero sarà disponibile dopo il completamento del passaggio Prepara per la spedizione. Scaricare l'etichetta indirizzo da questa pagina e incollarla sulla scatola.
3. Spegnere il dispositivo e rimuovere i cavi.
4. Avvolgere il cavo di alimentazione fornito con il dispositivo e assicurarlo al retro del dispositivo. 
5. Inviare un messaggio di posta elettronica al Servizio clienti SingPost usando il modello seguente con il numero tracciabilità.

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pickup - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > Per le richieste di prenotazione ricevute in un giorno lavorativo:
   >
   > * Prima delle 15:00 il ritiro verrà effettuato il giorno lavorativo successivo tra le 9:00 e le 13:00.
   > * Dopo le 15:00 il ritiro verrà effettuato il giorno lavorativo successivo tra le 14:00 e le 18:00.  

## <a name="south-africa"></a>[Sud Africa](#tab/in-sa)

1. Conservare la scatola originale usata per imballare il dispositivo da spedire per la restituzione.
2. Prendere nota del numero di riferimento (numero di lettera di vettura) indicato nell'interfaccia utente Web locale del dispositivo. Questo numero viene visualizzato al termine della procedura **Prepara per la spedizione**.
3. Scaricare e stampare l'etichetta di spedizione disponibile nell'interfaccia utente Web locale del dispositivo e incollarla sul pacchetto da spedire.
4. Per prenotare un ritiro con DHL, scegliere una delle opzioni seguenti:

    * Chiamare prima delle 14.00 il servizio clienti al numero **+27(0) 11 9213600**, selezionare l'opzione 1 e quindi specificare il numero di lettera di vettura.
    * Inviare un messaggio di posta elettronica a [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) usando il modello seguente:

    ```output
    To: Priority.Support@dhl.com
    Subject: Pickup request for Microsoft Azure
    Body: Need pick up for the below shipment
      *  DHL tracking number (reference number/waybill number)
      *  Requested pickup date: yyyy/mm/dd;time:HH MM
    ```

    * In alternativa, è possibile consegnare il pacchetto al centro DHL più vicino.

5. Se si verificano problemi, inviare un messaggio di posta elettronica a [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) specificando i dettagli dei problemi riscontrati e indicando nell'oggetto il numero di lettera di vettura. È anche possibile chiamare il numero +27(0)119213902.

## <a name="hong-kong"></a>[RAS di Hong Kong](#tab/in-hk)

1. Imballare il dispositivo da spedire per la restituzione nella scatola originale.
2. Prendere nota del numero di riferimento (numero di tracciabilità per la spedizione inversa) indicato nell'interfaccia utente Web locale del dispositivo. Questo numero viene visualizzato al termine della procedura **Prepara per la spedizione**.
3. Scaricare e stampare l'etichetta di spedizione disponibile nell'interfaccia utente Web locale del dispositivo e incollarla sul pacchetto da spedire.
4. Avvolgere il cavo di alimentazione fornito con il dispositivo e assicurarlo al retro del dispositivo.
5. Chiamare l'assistenza clienti di **Quantium Solutions** al numero **(852) 2318 1213** durante l'orario di ufficio (dalle 9.00 alle 18.00, da lunedì a venerdì).  
6. Aggiungere la dicitura Ritiro Microsoft Azure, nonché il numero di riferimento e il numero di tracciabilità (sopra il codice a barre) sull'etichetta dell'indirizzo di reso per predisporre un ritiro.
7. Si riceverà una conferma verbale della pianificazione del prelievo. Qualora il corriere non si presenti, chiamare l'assistenza clienti di Quantium Solutions per ulteriori accordi.
8. Quando si prenota un ritiro con Quantium, inviare la conferma a [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com) usando il modello seguente:

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

Se si verificano problemi, inviare un messaggio di posta elettronica a Data Box Operations Asia [adbo@microsoft.com](mailto:adbo@microsoft.com) indicando il nome del processo nell'oggetto e specificando il problema riscontrato.

## <a name="self-managed"></a>[Modalità autogestita](#tab/in-selfmanaged)

Se si usa il dispositivo Data Box in Giappone, Singapore, Corea, India, Sudafrica o Europa occidentale ed è stata selezionata l'opzione Spedizione autogestita durante la creazione dell'ordine, seguire queste istruzioni.

1. Annotare il codice di autorizzazione visualizzato nella pagina Prepara per la spedizione dell'interfaccia utente Web locale di Data Box una volta completato questo passaggio.
2. Spegnere il dispositivo e rimuovere i cavi. Avvolgere il cavo di alimentazione fornito con il dispositivo e assicurarlo al retro del dispositivo.
3. Quando si è pronti per restituire il dispositivo, inviare un messaggio di posta elettronica al team delle operazioni di Azure Data Box usando il modello seguente.
    
    ```
    To: adbops@microsoft.com 
    Subject: Request for Azure Data Box drop-off for order: ‘orderName’ 
    Body: 
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

---

## <a name="erasure-of-data-from-data-box"></a>Cancellare i dati dal Data Box
 
Quando il dispositivo raggiunge il data center di Azure, i dati vengono cancellati dai dischi del Data Box in base alle [linee guida NIST SP 800-88 revisione 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati descritti gli argomenti seguenti:

> [!div class="checklist"]
> * Prerequisiti
> * Preparare per la spedizione
> * Spedire il Data Box a Microsoft
> * Cancellare i dati dal Data Box

Per informazioni su come gestire il Data Box, passare all'articolo successivo.

> [!div class="nextstepaction"]
> [Gestire Data Box tramite il portale di Azure](./data-box-portal-admin.md)


