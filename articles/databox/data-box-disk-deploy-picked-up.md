---
title: Restituire i dischi di Microsoft Azure Data Box | Microsoft Docs
description: Usare questa esercitazione per scoprire come restituire i dischi di Azure Data Box a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 7c62ec722e5bb909c13b21a7b98d86dd72d9e665
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59425951"
---
# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>Esercitazione: Restituire Azure Data Box Disk e verificare il caricamento dei dati in Azure

Questa è l'ultima esercitazione della serie: Distribuire Azure Data Box Disk. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Spedire i dischi di Data Box a Microsoft
> * Verificare il caricamento dei dati in Azure
> * Cancellazione dei dati dai dischi di Data Box

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di aver completato l'[Esercitazione: copiare i dati in Azure Data Box Disk ed eseguire la verifica](data-box-disk-deploy-copy-data.md).

## <a name="ship-data-box-disk-back"></a>Restituire i dischi di Data Box

1. Dopo aver completato la convalida dei dati, scollegare i dischi. Rimuovere i cavi di collegamento.
2. Avvolgere i dischi e i cavi di collegamento nel pluriball e inserirli nella scatola per la spedizione. In caso di accessori mancanti, potrebbero essere addebitate spese aggiuntive.
    - Riutilizzare l'imballaggio della spedizione iniziale.  
    - È consigliabile imballare i dischi avvolgendoli con cura in una pellicola a bolle d'aria.
    - Assicurarsi di riempire i vuoti per ridurre i movimenti all'interno della scatola.

I passaggi successivi dipendono dalla destinazione di restituzione del dispositivo.

### <a name="pick-up-in-us-canada"></a>Ritiro negli Stati Uniti e in Canada

Eseguire la procedura seguente se si restituisce il dispositivo negli Stati Uniti o in Canada.

1. Usare l'etichetta per la spedizione di ritorno nella busta in plastica trasparente applicata sulla scatola. Se l'etichetta è danneggiata o è stata smarrita:
    - Passare a **Panoramica > Scarica etichetta di spedizione**.

        ![Scaricare l'etichetta di spedizione](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Questa azione scarica un'etichetta per la spedizione di ritorno, come illustrato di seguito.

        ![Esempio di etichetta di spedizione](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Applicare l'etichetta sul dispositivo.

2. Sigillare la scatola e assicurarsi che l'etichetta per la spedizione di ritorno sia visibile.
3. Pianificare un ritiro con UPS.


### <a name="pick-up-in-europe"></a>Ritiro in Europa

Eseguire la procedura seguente se si restituisce il dispositivo in Europa.

1. Usare l'etichetta per la spedizione di ritorno nella busta in plastica trasparente applicata sulla scatola. Se l'etichetta è danneggiata o è stata smarrita:
    - Passare a **Panoramica > Scarica etichetta di spedizione**.

        ![Scaricare l'etichetta di spedizione](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Questa azione scarica un'etichetta per la spedizione di ritorno, come illustrato di seguito.

        ![Esempio di etichetta di spedizione](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Applicare l'etichetta sul dispositivo.

2. Sigillare la scatola e assicurarsi che l'etichetta per la spedizione di ritorno sia visibile.
3. Se i dischi vengono rispediti in Europa con DHL, richiedere un ritiro a DHL, visitando il sito Web e specificando il numero di lettera di vettura.
4. Passare al sito Web di DHL Express del paese e scegliere **Prenota il ritiro di un reso > Richiedere ritiro**.

    ![Spedizione di ritorno DHL](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. Specificare il numero di lettera di vettura e fare clic su **Schedule Pickup** (Pianifica ritiro) per organizzare il ritiro.

      ![Pianificare il ritiro](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>Ritiro nell'area Asia Pacifico

Quest'area include le istruzioni per il ritiro in Giappone, Corea e Australia.

#### <a name="pick-up-in-australia"></a>Ritiro in Australia

I datacenter di Azure in Australia prevedono una notifica sulla sicurezza aggiuntiva. Tutte le spedizioni in ingresso devono prevedere una notifica anticipata. Seguire questa procedura per il ritiro in Australia.

1. Inviare un messaggio all'indirizzo `adbops@microsoft.com` per richiedere un'etichetta di spedizione con ID di ingresso univoco o il codice TAU. Inviare la richiesta almeno 3 giorni prima della data di spedizione pianificata per ricevere l'etichetta in tempo.
2. L'oggetto del messaggio di posta elettronica deve essere: *Richiesta di etichetta per la spedizione inversa con codice TAU*. Assicurarsi di includere i seguenti dettagli nel messaggio di posta elettronica: 

    - Nome ordine
    - Indirizzo
    - Nome del contatto

#### <a name="pick-up-in-japan"></a>Ritiro in Giappone

1. Includere la lettera di vettura (connote) per il reso di Japan Post Chakubarai.
2. Sulla lettera di vettura, nelle informazioni sul mittente indicare il nome dell'azienda e l'indirizzo.
3. Chiamare il numero 0800-0800-111 (numero verde) di Japan Post per la richiesta di ritiro. Digitare il codice postale a 7 cifre dell'indirizzo per il ritiro e l'inoltro all'ufficio postale più vicino.
    - Gli orari disponibili per la richiesta di ritiro dipendono dai singoli uffici postali.
    - Indicare che il vettore della spedizione è Japan Post Chakubarai Yu-Pack.
    - Usare la lettera di vettura di Chakubarai inclusa.
4. Se la lettera di vettura di Japan Post Chakubarai non è stata inclusa, inviare un messaggio di posta elettronica a *Quantium Solutions* all'indirizzo `Customerservice.JP@quantiumsolutions.com`. *Quantium Solutions* richiederà a Japan Post di fornire la lettera di vettura al momento del ritiro.
    - Indicare il numero di riferimento nella colonna dei commenti della lettera di vettura di Chakubarai che verrà fornita da Japan Post.
    - Specificare l'indirizzo di spedizione come indicato sotto:   
        ```
        3F N7 Prologis Park Tokyo Ohta, 1-3-6 Tokai Ohta-ku, Tokyo 143-0001
        Microsoft Service Center c/o Quantium Solutions Japan
        TEL: 03-5755-0150
        ```

Se la lettera di vettura di Chakubarai non è presente, è possibile richiedere il ritiro tramite posta elettronica. Usare il modello di messaggio di posta elettronica seguente per richiedere il ritiro.

```
To: Customerservice.JP@quantiumsolutions.com
Subject: Pickup request for Azure Data Box Disk｜Job Name： 
Body: 
- Azure Data Box Disk job name：
- Reference number:  
- Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
```

#### <a name="pick-up-in-korea"></a>Ritiro in Corea

1. Assicurarsi di includere la lettera di vettura (connote) per il reso.
2. Per richiedere il ritiro:
    1. Chiamare l'assistenza clienti di *Quantium Solutions International* al numero 070-8231-1418 durante l'orario di ufficio (dalle 10.00 alle 17.00, dal lunedì al venerdì). Indicare *Ritiro Microsoft* e il numero della lettera di vettura per organizzare il ritiro.  
    2. Se il numero dell'assistenza clienti è occupato, inviare un messaggio di posta elettronica all'indirizzo `microsoft@rocketparcel.com`, con l'oggetto *Ritiro Microsoft* e il numero di lettera di vettura come riferimento.
    3. Qualora il corriere non si presenti, chiamare *Quantium Solutions International* per accordarsi in merito a un ritiro alternativo. 

## <a name="verify-data-upload-to-azure"></a>Verificare il caricamento dei dati in Azure

Dopo il ritiro dei dischi da parte del vettore, lo stato dell'ordine nel portale diventa **Ritirato**. Viene visualizzato anche un ID di traccia.

![Dischi prelevati](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Quando Microsoft riceve e analizza i dischi, lo stato del processo diventa **Ricevuto**. 

![Dischi ricevuti](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

I dati vengono copiati automaticamente quando i dischi vengono collegati a un server nel data center di Azure. A seconda delle dimensioni dei dati, l'operazione di copia potrebbe richiedere da alcune ore a giorni per il completamento. È possibile monitorare lo stato di avanzamento del processo di copia nel portale.

Al termine della copia, lo stato dell'ordine diventa **Completato**.

![Copia dei dati completata](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Verificare la presenza dei dati negli account di archiviazione prima di eliminarli dall'origine. I dati possono trovarsi in:

- Account di archiviazione di Azure. Quando si copiano i dati in Data Box, a seconda del tipo, i dati vengono caricati in uno dei percorsi seguenti nell'account di archiviazione di Azure.

  - Per i BLOB in blocchi e i BLOB di pagine: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Per File di Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    In alternativa, è possibile passare all'account di archiviazione di Azure nel portale di Azure e procedere da questa posizione.

- Gruppi di risorse di dischi gestiti. Quando si creano dischi gestiti, i dischi rigidi virtuali vengono caricati come BLOB di pagine e quindi convertiti in dischi gestiti. I dischi gestiti vengono associati ai gruppi di risorse specificati al momento della creazione dell'ordine.

  - Se la copia nei dischi gestiti in Azure ha avuto esito positivo, è possibile passare a **Dettagli ordine** nel portale di Azure e prendere nota del gruppo di risorse specificato per i dischi gestiti.

      ![Visualizzare i dettagli dell'ordine](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Passare al gruppo di risorse annotato e individuare i dischi gestiti.

      ![Gruppo di risorse per i dischi gestiti](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Se è stato copiato un file VHDX oppure un disco rigido virtuale differenze o dinamico, questo verrà caricato nell'account di archiviazione di staging come BLOB in blocchi. Passare all'**account di archiviazione di staging > BLOB** e quindi selezionare il contenitore appropriato (StandardSSD, StandardHDD o PremiumSSD). I file VHDX o i dischi rigidi virtuali verranno visualizzati come BLOB in blocchi nell'account di archiviazione di staging.

Per verificare che i dati siano stati caricati in Azure, seguire questa procedura:

1. Passare all'account di archiviazione associato all'ordine del disco.
2. Passare a **Servizio Blob > Esplora BLOB**. Viene presentato l'elenco di contenitori. In modo corrispondente alla sottocartella creata nelle cartelle *BlockBlob* e *PageBlob*, contenitori con lo stesso nome vengono creati nell'account di archiviazione.
    Se i nomi delle cartelle non sono conformi alle convenzioni di denominazione di Azure, il caricamento dei dati in Azure avrà esito negativo.

4. Per verificare che sia stato caricato l'intero set di dati, usare Microsoft Azure Storage Explorer. Collegare l'account di archiviazione corrispondente all'ordine di noleggio del disco e quindi esaminare l'elenco dei contenitori BLOB. Selezionare un contenitore, fare clic su **Altro** e quindi fare clic su **Statistiche cartella**. Nel riquadro **Attività** vengono visualizzate le statistiche per la cartella, inclusi il numero di BLOB e le dimensioni totali dei BLOB. Le dimensioni totali dei BLOB devono corrispondere alle dimensioni del set di dati.

    ![Statistiche delle cartelle in Storage Explorer](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Cancellazione dei dati dai dischi di Data Box

Dopo aver completato la copia e aver verificato che i dati siano disponibili nell'account di archiviazione di Azure, i dischi vengono cancellati in modo sicuro come da standard NIST.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati illustrati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Spedire i dischi di Data Box a Microsoft
> * Verificare il caricamento dei dati in Azure
> * Cancellazione dei dati dai dischi di Data Box


Passare alla guida pratica successiva per scoprire come gestire i dischi di Data Box tramite il portale di Azure.

> [!div class="nextstepaction"]
> [Usare il portale di Azure per amministrare Azure Data Box Disk](./data-box-portal-ui-admin.md)


