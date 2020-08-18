---
title: Esercitazione per restituire Azure Data Box Disk | Microsoft Docs
description: Questa esercitazione illustra come restituire il dispositivo Azure Data Box Disk. Le istruzioni per il ritiro dipendono dalla località da cui viene restituito il dispositivo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/21/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 692ddbf1e17e316bdcee6c34062ffa51ef278bc7
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87923782"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk"></a>Esercitazione: Restituire Azure Data Box Disk

Questa esercitazione illustra come restituire il dispositivo Azure Data Box Disk. Le istruzioni per il ritiro dipendono dalla località da cui viene restituito il dispositivo.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Spedire i dischi di Data Box a Microsoft

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di aver completato l'[Esercitazione: copiare i dati in Azure Data Box Disk ed eseguire la verifica](data-box-disk-deploy-copy-data.md).

## <a name="ship-data-box-disk-back"></a>Restituire i dischi di Data Box

::: zone-end

::: zone target="chromeless"

## <a name="return-azure-data-box-disk"></a>Restituire Azure Data Box Disk

::: zone-end

1. Dopo aver completato la convalida dei dati, scollegare i dischi. Rimuovere i cavi di collegamento.
2. Avvolgere i dischi e i cavi di collegamento nel pluriball e inserirli nella scatola per la spedizione. In caso di accessori mancanti, potrebbero essere addebitate spese aggiuntive.
    - Riutilizzare l'imballaggio della spedizione iniziale.  
    - È consigliabile imballare i dischi avvolgendoli con cura in una pellicola a bolle d'aria.
    - Assicurarsi di riempire i vuoti per ridurre i movimenti all'interno della scatola.

I passaggi successivi dipendono dalla destinazione di restituzione del dispositivo. Le istruzioni sono diverse per Stati Uniti, Canada, Unione Europa (UE), Australia o paesi/aree geografiche in Asia.

### <a name="us-or-canada"></a>[Stati Uniti o Canada](#tab/in-us-or-canada)

Eseguire la procedura seguente se si restituisce il dispositivo negli Stati Uniti o in Canada.

1. Usare l'etichetta per la spedizione di ritorno con busta in plastica trasparente applicata sulla scatola. Se l'etichetta è danneggiata o è stata smarrita:
    - Passare a **Panoramica > Scarica etichetta di spedizione** e scaricare un'etichetta di spedizione per reso.
    - Applicare l'etichetta sul dispositivo.

2. Sigillare la scatola e assicurarsi che l'etichetta per la spedizione di ritorno sia visibile.
3. Pianificare un ritiro con UPS. Per pianificare un ritiro:

    - Chiamare il servizio UPS locale (numero verde specifico del paese o area).
    - Nella chiamata specificare il numero di monitoraggio della spedizione di reso indicato sull'etichetta stampata.
    - Se non si specifica il numero di monitoraggio, il servizio UPS addebiterà un costo aggiuntivo al momento del ritiro.
    - Anziché pianificare il ritiro, è anche possibile consegnare Data Box Disk al più vicino punto di consegna.

### <a name="europe"></a>[Europa](#tab/in-europe)

Eseguire la procedura seguente se si restituisce il dispositivo in Europa.

1. Usare l'etichetta per la spedizione di ritorno con busta in plastica trasparente applicata sulla scatola. Se l'etichetta è danneggiata o è stata smarrita:
    - Passare a **Panoramica > Scarica etichetta di spedizione** e scaricare un'etichetta di spedizione per reso.
    - Applicare l'etichetta sul dispositivo.

2. Sigillare la scatola e assicurarsi che l'etichetta per la spedizione di ritorno sia visibile.
3. Se i dischi vengono rispediti in Europa con DHL, richiedere un ritiro a DHL, visitando il sito Web e specificando il numero di lettera di vettura.
4. Passare al sito Web di DHL Express del paese o area e scegliere **Prenota il ritiro di un reso > Richiedere ritiro**.
5. Specificare il numero di lettera di vettura e fare clic su **Schedule Pickup** (Pianifica ritiro) per organizzare il ritiro.

### <a name="australia"></a>[Australia](#tab/in-australia)

I datacenter di Azure in Australia prevedono una notifica sulla sicurezza aggiuntiva. Tutte le spedizioni in ingresso devono prevedere una notifica anticipata. Seguire questa procedura per il ritiro in Australia.

1. Usare l'etichetta di spedizione per reso fornita e verificare che vi sia scritto il codice TAU (numero di riferimento). Se l'etichetta di spedizione fornita è mancante o per qualsiasi altro problema, inviare un messaggio di posta elettronica a [Data Box Asia Operations](mailto:adbo@microsoft.com). Specificare il nome dell'ordine nell'intestazione dell'oggetto e fornire i dettagli del problema riscontrato.
2. Applicare l'etichetta sulla scatola.
3. Prenotare un ritiro online facendo clic sul collegamento https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference.

### <a name="japan"></a>[Giappone](#tab/in-japan)

1. Sulla lettera di vettura, nelle informazioni sul mittente, indicare il nome dell'azienda e l'indirizzo.
2. Inviare un messaggio di posta elettronica a Quantium Solutions usando il modello di messaggio di posta elettronica seguente.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Microsoft Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00
        b. 13：00-15：00
        c. 15：00-17：00
        d. 17：00-19：00
    ```
    - **Se il ritiro avviene a Osaka**, modificare l'oggetto nel modello di messaggio di posta elettronica in: `Pickup request for Microsoft Azure OSA`.
    - Se la lettera di vettura di Japan Post Chakubarai non era inclusa o risulta mancante, indicarlo nel messaggio di posta elettronica. Quantium Solutions Japan richiederà a Japan Post di portare la lettera di vettura al momento del ritiro.
    - In caso di più ordini, inviare messaggi di posta elettronica per garantire prelievi singoli.

3. Si riceverà una conferma tramite posta elettronica da Quantium Solutions dopo la prenotazione di un prelievo. La conferma tramite posta elettronica include anche informazioni sulla lettera di vettura di Chakubarai.

Se necessario, è possibile contattare il supporto di Quantium Solutions (in lingua giapponese) come segue: 

- Indirizzo di posta elettronica：[Customerservice.JP@quantiumsolutions.com](mailto:Customerservice.JP@quantiumsolutions.com)
- Telefono：03-5755-0150 

### <a name="korea"></a>[Corea del Sud](#tab/in-korea)

1. Assicurarsi di includere la lettera di vettura per il reso.
2. Per richiedere il prelievo quando è presente la lettera di vettura:
    1. Chiamare l'assistenza clienti di *Quantium Solutions International* al numero 070-8231-1418 durante l'orario di ufficio (dalle 10.00 alle 17.00, dal lunedì al venerdì). Indicare *Ritiro Microsoft Azure* e il numero della richiesta di servizio per organizzare il ritiro.  
    2. Se il numero dell'assistenza clienti è occupato, inviare un messaggio di posta elettronica all'indirizzo `microsoft@rocketparcel.com` con l'oggetto *Ritiro Microsoft Azure* e il numero della richiesta di servizio come riferimento.
    3. Qualora il corriere non si presenti, chiamare *Quantium Solutions International* per accordarsi in merito a un ritiro alternativo.
    4. Si riceverà una conferma tramite posta elettronica con la pianificazione del prelievo.
3. Eseguire questo passaggio solo se la lettera di vettura non è presente. Per richiedere il ritiro:
    1. Chiamare l'assistenza clienti di *Quantium Solutions International* al numero 070-8231-1418 durante l'orario di ufficio (dalle 10.00 alle 17.00, dal lunedì al venerdì). Indicare *Ritiro Microsoft Azure* e il numero della richiesta di servizio per organizzare il ritiro. Specificare che è necessaria una nuova lettera di vettura per organizzare un prelievo. Specificare il mittente (cliente), le informazioni sul destinatario (data center di Azure) e il numero di riferimento (numero della richiesta di servizio). 
    2. Se il numero dell'assistenza clienti è occupato, inviare un messaggio di posta elettronica all'indirizzo `microsoft@rocketparcel.com` con l'oggetto *Ritiro Microsoft Azure* e il numero della richiesta di servizio come riferimento.
    3. Qualora il corriere non si presenti, chiamare *Quantium Solutions International* per accordarsi in merito a un ritiro alternativo.
    4. Se la richiesta viene eseguita tramite telefono, si riceverà una conferma verbale.

### <a name="singapore"></a>[Singapore](#tab/in-singapore)

1. Stampare l'etichetta indirizzo e applicarla alla scatola. Se l'etichetta è danneggiata o è stata smarrita:
    - Passare a **Panoramica > Scarica etichetta di spedizione** e ottenere un'etichetta di spedizione per reso.
    - Applicare l'etichetta sul dispositivo. Assicurarsi che l'etichetta sia visibile.

2. Per richiedere il ritiro, inviare un messaggio di posta elettronica al Servizio clienti SingPost usando il modello seguente con il numero tracciabilità (il numero tracciabilità si trova sull'etichetta di reso fornita nel pacco consegnato).

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pickup - XZ00001234567
    Body:
     a.    Requestor name
     b.    Requestor contact number
     c.    Requestor collection address
     d.    Preferred collection date
    ```

   > [!NOTE]
   > Per le richieste di prenotazione ricevute in un giorno lavorativo:
   >
   > * Prima delle 15:00 il ritiro verrà effettuato il giorno lavorativo successivo tra le 9:00 e le 13:00.
   > * Dopo le 15:00 il ritiro verrà effettuato il giorno lavorativo successivo tra le 14:00 e le 18:00.

   In caso di problemi, contattare Data Box Operations Asia all'indirizzo adbo@microsoft.com. Specificare il nome del processo nell'intestazione dell'oggetto e il problema riscontrato.

3. Consegnare la scatola al corriere.

### <a name="south-africa"></a>[Sud Africa](#tab/in-sa)

Eseguire questa procedura se si restituisce il dispositivo in Sudafrica.

1. Applicare l'etichetta di spedizione fornita sulla scatola. L'etichetta contiene il numero tracciabilità. Se l'etichetta indirizzo non è presente, è possibile scaricarne una nuova da **Panoramica > Scarica etichetta di spedizione**.

2. Sigillare la scatola e assicurarsi che l'etichetta per la spedizione di ritorno sia visibile.

3. Per prenotare un ritiro con DHL:

    * Chiamare il servizio clienti al numero +27(0) 11 9213600, selezionare l'opzione 1 e specificare il numero di lettera di vettura.
    * Per pianificare un ritiro, è necessario chiamare prima delle 14.00 (ora solare Sud Africa).  
    * Inviare un messaggio di posta elettronica all'indirizzo [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) per organizzare il ritiro usando il modello di messaggio di posta elettronica seguente:

    ```output
    To: Priority.Support@dhl.com
    Subject:Pickup request for Microsoft Azure
    Body:  Need pick up for the below shipment
    *  DHL tracking number (reference number/waybill number)
    *  Requested pickup date：yyyy/mm/dd; time: HH MM
    ```

    * In alternativa, è possibile consegnare il pacchetto al centro DHL più vicino.

4. Se si verificano problemi, inviare un messaggio di posta elettronica a [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) specificando i dettagli dei problemi riscontrati e indicando nell'oggetto il numero di lettera di vettura. È anche possibile chiamare il numero +27(0)119213902.

### <a name="china"></a>[Cina](#tab/in-china)

Eseguire questa procedura se si restituisce il dispositivo in Cina.

1. Allegare l'etichetta indirizzo fornita e incollarla sulla scatola. L'etichetta contiene il numero tracciabilità. Se l'etichetta indirizzo non è presente, è possibile scaricarne una nuova da **Panoramica > Scarica etichetta di spedizione**.

2. Inviare un messaggio di posta elettronica al servizio clienti FedEx Premier e specificare il numero tracciabilità (numero di riferimento della spedizione) per organizzare il prelievo usando il modello di messaggio di posta elettronica seguente:  

   ```output
   To: ying.bao@fedex.com;739951@fedex.com
   Subject: Pickup request for Microsoft Azure : Order Name
   Body: Need pick up for the below shipment
   * FedEx tracking number (reference number)
   * Requested pickup date：yyyy/mm/dd; time: HH MM
   ```

3. Ricevere una conferma tramite posta elettronica da FedEx dopo il completamento della prenotazione del prelievo.  

4. Se si verificano problemi, inviare un messaggio di posta elettronica a [DL-DC-SHA@oe.21vianet.com](mailto:DL-DC-SHA@oe.21vianet.com) specificando i dettagli del problema rilevato e indicando nell'oggetto il nome dell'ordine.

#### <a name="premier-customer-care-contact-information"></a>Informazioni di contatto per il servizio clienti Premier

<ins>Server/istanza primaria</ins>

| Informazioni contatto | Dettagli |
|---|---|
|Nome:       | Bao Ying|
|Designazione | Senior OneCall Representative |
|Telefono:      | 400.889.6066 ext. 3693 |
|Indirizzo di posta elettronica:     | [ying.bao@fedex.com](mailto:ying.bao@fedex.com) |

<ins>Backup</ins>

| Informazioni contatto | Dettagli |
|---|---|
|Nome:       | He Xun|
|Designazione | OneCall Representative |
|Telefono:      | 400.889.6066 ext. 3603 |
|Indirizzo di posta elettronica:     | [739951@fedex.com](mailto:739951@fedex.com) |

### <a name="self-managed"></a>[Modalità autogestita](#tab/in-selfmanaged)

Se si usa Data Box Disk in Giappone, Singapore, Corea ed Europa occidentale o India ed è stata selezionata l'opzione Spedizione autogestita durante la creazione dell'ordine, seguire queste istruzioni.

1. Passare al pannello **Panoramica** dell'ordine nel portale di Azure. Seguire le istruzioni visualizzate quando si seleziona **Pianifica ritiro**. Verrà visualizzato un codice di autorizzazione da usare per la consegna dell'ordine.

2. Quando si è pronti per restituire il dispositivo, inviare un messaggio di posta elettronica al team delle operazioni di Azure Data Box usando il modello seguente.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box Disk drop-off for order: 'orderName'
    Body:
     a. Order name
     b. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```
3. Il team delle operazioni di Azure Data Box collaborerà con l'utente per organizzare la consegna presso il data center di Azure.

---

::: zone target="docs"

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati illustrati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
>
> * Spedire i dischi di Data Box a Microsoft

Passare alla guida pratica successiva per informazioni su come verificare il caricamento dei dati da Data Box Disk all'account di archiviazione di Azure.

> [!div class="nextstepaction"]
> [Verificare il caricamento dei dati da Azure Data Box Disk](./data-box-disk-deploy-upload-verify.md)

::: zone-end
