---
title: Esercitazione per restituire Azure Data Box Disk | Microsoft Docs
description: Usare questa esercitazione per scoprire come restituire i dischi di Azure Data Box a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: alkohli
ms.localizationpriority: high
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: b982e0bcd87a60e29b11c7123fbe5d675ab017b3
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390416"
---
::: zone target="chromeless"

## <a name="return-azure-data-box-disk"></a>Restituire Azure Data Box Disk 

::: zone-end

::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk"></a>Esercitazione: Restituire Azure Data Box Disk 

In questa esercitazione viene descritto come pianificare un ritiro per restituire il dispositivo Azure Data Box Disk. Le istruzioni per il ritiro dipendono dalla località da cui viene restituito il dispositivo. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Spedire i dischi di Data Box a Microsoft
> * Ritirare Data Box Disk in aree diverse

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di aver completato l'[Esercitazione: copiare i dati in Azure Data Box Disk ed eseguire la verifica](data-box-disk-deploy-copy-data.md).


## <a name="ship-data-box-disk-back"></a>Restituire i dischi di Data Box

::: zone-end

1. Dopo aver completato la convalida dei dati, scollegare i dischi. Rimuovere i cavi di collegamento.
2. Avvolgere i dischi e i cavi di collegamento nel pluriball e inserirli nella scatola per la spedizione. In caso di accessori mancanti, potrebbero essere addebitate spese aggiuntive.
    - Riutilizzare l'imballaggio della spedizione iniziale.  
    - È consigliabile imballare i dischi avvolgendoli con cura in una pellicola a bolle d'aria.
    - Assicurarsi di riempire i vuoti per ridurre i movimenti all'interno della scatola.

I passaggi successivi dipendono dalla destinazione di restituzione del dispositivo. Le istruzioni sono diverse per Stati Uniti, Canada, Unione Europea (UE), Australia o paesi asiatici.

### <a name="pick-up-in-us-canada"></a>Ritiro negli Stati Uniti e in Canada

Eseguire la procedura seguente se si restituisce il dispositivo negli Stati Uniti o in Canada.

1. Usare l'etichetta per la spedizione di ritorno nella busta in plastica trasparente applicata sulla scatola. Se l'etichetta è danneggiata o è stata smarrita:
    - Passare a **Panoramica > Scarica etichetta di spedizione** e scaricare un'etichetta di spedizione per reso.
    - Applicare l'etichetta sul dispositivo.

2. Sigillare la scatola e assicurarsi che l'etichetta per la spedizione di ritorno sia visibile.
3. Pianificare un ritiro con UPS. Per pianificare un ritiro:

    - Chiamare il servizio UPS locale (numero verde specifico del paese o area).
    - Nella chiamata specificare il numero di monitoraggio della spedizione di reso indicato sull'etichetta stampata.
    - Se non si specifica il numero di monitoraggio, il servizio UPS addebiterà un costo aggiuntivo al momento del ritiro.
    - Anziché pianificare il ritiro, è anche possibile consegnare Data Box Disk al più vicino punto di consegna.

### <a name="pick-up-in-europe"></a>Ritiro in Europa

Eseguire la procedura seguente se si restituisce il dispositivo in Europa.

1. Usare l'etichetta per la spedizione di ritorno nella busta in plastica trasparente applicata sulla scatola. Se l'etichetta è danneggiata o è stata smarrita:
    - Passare a **Panoramica > Scarica etichetta di spedizione** e scaricare un'etichetta di spedizione per reso.
    - Applicare l'etichetta sul dispositivo.

2. Sigillare la scatola e assicurarsi che l'etichetta per la spedizione di ritorno sia visibile.
3. Se i dischi vengono rispediti in Europa con DHL, richiedere un ritiro a DHL, visitando il sito Web e specificando il numero di lettera di vettura.
4. Passare al sito Web di DHL Express del paese o area e scegliere **Prenota il ritiro di un reso > Richiedere ritiro**.    
3. Specificare il numero di lettera di vettura e fare clic su **Schedule Pickup** (Pianifica ritiro) per organizzare il ritiro.

### <a name="pick-up-in-australia"></a>Ritiro in Australia

I datacenter di Azure in Australia prevedono una notifica sulla sicurezza aggiuntiva. Tutte le spedizioni in ingresso devono prevedere una notifica anticipata. Seguire questa procedura per il ritiro in Australia.

1. Usare l'etichetta di spedizione per reso fornita e verificare che vi sia scritto il codice TAU (numero di riferimento). Se l'etichetta di spedizione fornita è mancante o per qualsiasi altro problema, inviare un messaggio di posta elettronica a [Data Box Asia Operations](mailto:adbo@microsoft.com). Specificare il nome dell'ordine nell'intestazione dell'oggetto e fornire i dettagli del problema riscontrato.
3. Applicare l'etichetta sulla scatola. 
4. Prenotare un ritiro online al collegamento https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference. 

### <a name="pick-up-in-asia"></a>Ritiro in Asia

Le istruzioni di ritiro sono diverse per Giappone, Corea e Singapore.

#### <a name="pick-up-in-japan"></a>Ritiro in Giappone

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

- Posta elettronica：Customerservice.JP@quantiumsolutions.com 
- Telefono：03-5755-0150 

#### <a name="pick-up-in-korea"></a>Ritiro in Corea

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

#### <a name="pick-up-in-singapore"></a>Prelievo a Singapore

1. Stampare l'etichetta indirizzo e applicarla alla scatola. Se l'etichetta è danneggiata o è stata smarrita:
    - Passare a **Panoramica > Scarica etichetta di spedizione** e ottenere un'etichetta di spedizione per reso.
    - Applicare l'etichetta sul dispositivo. Assicurarsi che l'etichetta sia visibile.

2. Per richiedere il ritiro:
    - Chiamare l'assistenza clienti **SingPost** al numero **6845 6485** durante l'orario di ufficio (dalle 9.00 alle 17.00, da lunedì a venerdì).  
    - Indicare *Ritiro Microsoft Azure* e il numero della richiesta di servizio (numero di monitoraggio sull'etichetta indirizzo di reso) per organizzare il ritiro. 
    - Si riceverà una conferma verbale della pianificazione del prelievo. 
    - Qualora il corriere non si presenti, chiamare **SingPost** al numero **6845 6485** per accordarsi in merito a un ritiro alternativo. 
3. Consegnare la scatola al corriere. 


::: zone target="docs"

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati illustrati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Spedire i dischi di Data Box a Microsoft
> * Ritirare Data Box Disk in aree diverse

Passare alla guida pratica successiva per informazioni su come verificare il caricamento dei dati da Data Box Disk all'account di archiviazione di Azure.

> [!div class="nextstepaction"]
> [Verificare il caricamento dei dati da Azure Data Box Disk](./data-box-disk-deploy-picked-up.md)

::: zone-end




