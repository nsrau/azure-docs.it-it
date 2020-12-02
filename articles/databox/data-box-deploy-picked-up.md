---
title: Esercitazione per restituire Azure Data Box | Microsoft Docs
description: Questa esercitazione illustra le operazioni da eseguire per restituire il dispositivo Azure Data Box Heavy, tra cui la preparazione per la spedizione, la spedizione del dispositivo, la verifica del caricamento dei dati e la cancellazione dei dati dal dispositivo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 11/19/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 28637f35294e9c70cbf0e96fb2cdcb1853d2ba39
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992727"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Esercitazione: Restituire Azure Data Box e verificare il caricamento dei dati in Azure

::: zone-end

::: zone target="chromeless"

## <a name="return-data-box-and-verify-data-upload-to-azure"></a>Restituire Data Box e verificare il caricamento dei dati in Azure

::: zone-end

::: zone target="docs"

Questa esercitazione illustra come restituire Azure Data Box e verificare che i dati siano stati caricati in Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> * Prerequisiti
> * Preparare per la spedizione
> * Spedire il Data Box a Microsoft
> * Verificare il caricamento dei dati in Azure
> * Cancellare i dati dal Data Box

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

* Sia stata completata l'[Esercitazione: Copiare dati in Azure Data Box Disk ed eseguire la verifica](data-box-deploy-copy-data.md).
* I processi di copia sono stati completati e non sono presenti errori nella pagina **Connetti e copia**. Se i processi di copia sono in corso o se sono presenti errori nella pagina **Connetti e copia**, non è possibile procedere con la preparazione per la spedizione.

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

## <a name="us-canada-europe"></a>[Stati Uniti, Canada o Europa](#tab/in-us-canada-europe)

Eseguire la procedura seguente se si restituisce il dispositivo negli Stati Uniti, in Canada on in Europa.

1. Verificare che il dispositivo sia spento e i cavi scollegati. 
2. Avvolgere il cavo di alimentazione fornito con il dispositivo e assicurarlo al retro del dispositivo.
3. Assicurarsi che l'etichetta indirizzo sia visibile sul display E-ink e prenotare un corriere per il ritiro. Se l'etichetta viene danneggiata o smarrita oppure non viene visualizzata sul display E-ink, contattare il supporto tecnico Microsoft. Se consigliato dal supporto, è possibile passare a **Panoramica > Scarica etichetta di spedizione** nel portale di Azure. Scaricare l'etichetta di spedizione e applicarla sul dispositivo. 
4. Pianificare un ritiro con UPS per la restituzione del dispositivo. Per pianificare un ritiro:

    * Chiamare il servizio UPS locale (numero verde specifico del paese o area).
    * Nella chiamata specificare il numero di monitoraggio della spedizione di reso indicato sul display E-ink o sull'etichetta stampata.
    * Se non si specifica il numero di monitoraggio, il servizio UPS addebiterà un costo aggiuntivo al momento del ritiro.

    Anziché pianificare il ritiro, è anche possibile consegnare Data Box al più vicino punto di consegna.
4. Dopo che il Data Box è stato ritirato ed esaminato dal corriere, lo stato dell'ordine nel portale diventa **Ritirato**. Viene visualizzato anche un ID di traccia.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificare il caricamento dei dati in Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Cancellare i dati dal Data Box

Una volta completato il caricamento in Azure, i dati vengono cancellati dai dischi del Data Box in base alle [linee guida NIST SP 800-88 revisione 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="australia"></a>[Australia](#tab/in-australia)

I datacenter di Azure in Australia prevedono una notifica sulla sicurezza aggiuntiva. Tutte le spedizioni in ingresso devono prevedere una notifica anticipata. Seguire questa procedura per la spedizione in Australia.

1. Conservare la scatola originale usata per spedire il dispositivo per la restituzione.
2. Assicurarsi che la copia dei dati sul dispositivo sia completa e che la **preparazione per la spedizione** sia stata eseguita correttamente.
3. Spegnere il dispositivo e rimuovere i cavi.
4. Avvolgere il cavo di alimentazione fornito con il dispositivo e assicurarlo al retro del dispositivo.
5. Prenotare un ritiro online seguendo il [collegamento a DHL](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference).

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificare il caricamento dei dati in Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Cancellare i dati dal Data Box

Una volta completato il caricamento in Azure, i dati vengono cancellati dai dischi del Data Box in base alle [linee guida NIST SP 800-88 revisione 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

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

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificare il caricamento dei dati in Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Cancellare i dati dal Data Box
 
Una volta completato il caricamento in Azure, i dati vengono cancellati dai dischi del Data Box in base alle [linee guida NIST SP 800-88 revisione 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

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
   > * Prima delle 15:00 il ritiro verrà effettuato il giorno lavorativo successivo tra le 9:00 e le 13:00.
   > * Dopo le 15:00 il ritiro verrà effettuato il giorno lavorativo successivo tra le 14:00 e le 18:00.  

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificare il caricamento dei dati in Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Cancellare i dati dal Data Box

Una volta completato il caricamento in Azure, i dati vengono cancellati dai dischi del Data Box in base alle [linee guida NIST SP 800-88 revisione 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="south-africa"></a>[Sud Africa](#tab/in-sa)

1. Imballare il dispositivo da spedire per la restituzione nella scatola originale.
2. Avvolgere il cavo di alimentazione fornito con il dispositivo e assicurarlo al retro del dispositivo.
3. Prendere nota del numero di tracciabilità (mostrato come numero di riferimento nella pagina **Prepara per la spedizione** dell'interfaccia utente Web locale di Data Box). Il numero sarà disponibile dopo il completamento del passaggio "Prepara per la spedizione". Scaricare l'etichetta indirizzo da questa pagina e incollarla sulla scatola.
4. Richiedere un codice di reso al team delle operazioni di Azure Data Box. Un codice di reso è necessario per restituire il pacchetto al data center. Inviare un messaggio di posta elettronica all'indirizzo [adbops@microsoft.com](mailto:adbops@microsoft.com). Annotare questo codice sull'etichetta indirizzo, accanto all'indirizzo del mittente in modo che sia chiaramente visibile.
5. Prenotare un ritiro con DHL usando uno dei metodi seguenti:
 
   * Prenotare un ritiro online sul sito [DHL Express South Africa, **Schedule a Pickup**](https://mydhl.express.dhl/za/en/schedule-pickup.html#/schedule-pickup#label-reference).
   * Inviare un messaggio di posta elettronica a [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) usando il modello seguente:

     ```output
     To: Priority.Support@dhl.com
     Subject: Pickup request for Microsoft Azure
     Body: Need pick up for the below shipment
       *  DHL tracking number: (reference number/waybill number)
       *  Requested pickup date: yyyy/mm/dd;time:HH MM
       *  Shipper contact: (company name)
       *  Contact person: 
       *  Phone number: 
       *  Full physical address: 
       *  Item to be collected: Azure Dt
     ```

    * In alternativa, consegnare il pacchetto al punto di ritiro DHL più vicino.

6. Se si verificano problemi, inviare un messaggio di posta elettronica a [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) specificando i dettagli dei problemi riscontrati e indicando nell'oggetto il numero di lettera di vettura. È anche possibile chiamare il numero +27(0)119213902.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificare il caricamento dei dati in Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Cancellare i dati dal Data Box

Una volta completato il caricamento in Azure, i dati vengono cancellati dai dischi del Data Box in base alle [linee guida NIST SP 800-88 revisione 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="hong-kong"></a>[RAS di Hong Kong](#tab/in-hk)

1. Imballare il dispositivo da spedire per la restituzione nella scatola originale.
2. Avvolgere il cavo di alimentazione fornito con il dispositivo e assicurarlo al retro del dispositivo.
3. Chiamare l'assistenza clienti di **Quantium Solutions** al numero **(852) 2318 1213** durante l'orario di ufficio (dalle 9.00 alle 18.00, da lunedì a venerdì).  
4. Aggiungere la dicitura Ritiro Microsoft Azure, nonché il numero di riferimento e il numero di tracciabilità (sopra il codice a barre) sull'etichetta dell'indirizzo di reso per predisporre un ritiro.
5. Si riceverà una conferma verbale della pianificazione del prelievo. Qualora il corriere non si presenti, chiamare l'assistenza clienti di Quantium Solutions per ulteriori accordi.
6. Quando si prenota un ritiro con Quantium, inviare la conferma a [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com) usando il modello seguente:

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

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificare il caricamento dei dati in Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Cancellare i dati dal Data Box
 
Una volta completato il caricamento in Azure, i dati vengono cancellati dai dischi del Data Box in base alle [linee guida NIST SP 800-88 revisione 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

<!--## [In Korea](#tab/in-korea) 

1. Retain the original box used to ship the device for return shipment.
2. Note down the tracking number (shown as reference number on the Prepare to Ship page of the Data Box local web UI). This is available after the prepare to ship step successfully completes. Download the shipping label from this page and paste on the packing box. 
3. Power off the device and remove the cables.
4. Spool and securely place the power cord that was provided with the device in the back of the device. 

Request pickup  
If consignment note is present:  

1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number to arrange for a collection.
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.  
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.  
4. You will receive an email confirmation for the pickup schedule.  

Exception process
If the consignment note is not present:
1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number. Specify that you need a new consignment note to arrange for a collection. Provide sender (customer), receiver information (Azure datacenter), and reference number (service request number).
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.
4. You get a verbal confirmation if request is made via telephone.  
::: zone target="chromeless"

## Verify data upload to Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## Erasure of data from Data Box
 
Once the upload to Azure is complete, the Data Box erases the data on its disks as per the [NIST SP 800-88 Revision 1 guidelines](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end
-->

## <a name="self-managed"></a>[Modalità autogestita](#tab/in-selfmanaged)

Se si usa il dispositivo Data Box nelle aree Governo degli Stati Uniti, Giappone, Singapore, Corea, India, Sudafrica o Europa occidentale ed è stata selezionata l'opzione Spedizione autogestita durante la creazione dell'ordine, seguire queste istruzioni. 

1. Annotare il codice di autorizzazione visualizzato nella pagina Prepara per la spedizione dell'interfaccia utente Web locale di Data Box una volta completato questo passaggio.
2. Spegnere il dispositivo e rimuovere i cavi. Avvolgere il cavo di alimentazione fornito con il dispositivo e assicurarlo al retro del dispositivo.
3. Quando si è pronti per restituire il dispositivo, inviare un messaggio di posta elettronica al team delle operazioni di Azure Data Box usando il modello seguente.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box drop-off for order: 'orderName'
    Body:
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificare il caricamento dei dati in Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Cancellare i dati dal Data Box
 
Una volta completato il caricamento in Azure, i dati vengono cancellati dai dischi del Data Box in base alle [linee guida NIST SP 800-88 revisione 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

---

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Verificare il caricamento dei dati in Azure

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

