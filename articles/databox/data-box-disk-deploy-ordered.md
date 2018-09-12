---
title: Esercitazione per ordinare dischi di Microsoft Azure Data Box | Microsoft Docs
description: Usare questa esercitazione per scoprire come iscriversi e ordinare dischi di Azure Data Box per importare dati in Azure.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 37130e946aad00ef4eca14b7ce7942a8e435e6cd
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782329"
---
# <a name="tutorial-order-an-azure-data-box-disk-preview"></a>Esercitazione: Ordinare un disco di Azure Data Box (anteprima)

Azure Data Box Disk è una soluzione cloud ibrida che consente di importare i dati in locale in Azure in modo rapido, semplice e affidabile. È possibile trasferire i dati su dischi SSD (Solid State Disk) forniti da Microsoft e rispedire i dischi. Questi dati vengono poi caricati in Azure. 

Questa esercitazione descrive come è possibile ordinare un disco di Azure Data Box. Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
> * Iscriversi per Data Box Disk
> * Ordinare un disco di Data Box
> * Monitorare l'ordine
> * Annullare l'ordine

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!IMPORTANT]
> - Data Box Disk è disponibile in anteprima. Rivedere le [condizioni del servizio per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) prima di ordinare e distribuire la soluzione. 
> - Durante l'anteprima, i dischi di Data Box possono essere spediti ai clienti negli Stati Uniti, in Europa settentrionale e occidentale, in Canada e in Australia. Per altre informazioni, vedere [Disponibilità a livello di area](data-box-disk-overview.md#region-availability).

## <a name="sign-up"></a>Iscrizione 

Data Box Disk è disponibile in anteprima ed è necessario effettuare l'iscrizione per il servizio. Seguire questa procedura per iscriversi per il servizio Data Box:

1. Accedere al portale di Azure all'indirizzo: [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).
2. Selezionare la sottoscrizione che si vuole abilitare per l'anteprima. Rispondere alle domande relative alle dimensioni dei dati, al paese di residenza dei dati, all'intervallo di tempo e alla frequenza di trasferimento dei dati. Fare clic su **Iscrivimi**.
3. Dopo aver effettuato l'iscrizione e abilitato la sottoscrizione per l'anteprima, è possibile ordinare un disco di Data Box.

## <a name="order-data-box-disk"></a>Ordinare un disco di Data Box

Seguire questa procedura nel [portale di Azure](https://aka.ms/azuredataboxfromdiskdocs) per ordinare un disco di Data Box.

1. Nell'angolo superiore sinistro del portale fare clic su **+ Crea una risorsa** e cercare *Azure Data Box*. Fare clic su **Azure Data Box**.
    
   ![Cercare Azure Data Box 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Fare clic su **Create**(Crea).

3. Controllare se il servizio Data Box è disponibile nella propria area. Immettere o selezionare le informazioni seguenti e quindi fare clic su **Applica**.

    ![Selezionare l'opzione Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Impostazione|Valore|
    |---|---|
    |Sottoscrizione|Selezionare la sottoscrizione per cui è abilitato il servizio Data Box.<br> La sottoscrizione viene collegata all'account di fatturazione. |
    |Tipo di trasferimento| Importa in Azure|
    |Paese di origine | Selezionare il paese in cui si trovano attualmente i dati.|
    |Area di Azure di destinazione|Selezionare l'area di Azure in cui si vogliono trasferire i dati.|

  
5.  Selezionare **Data Box Disk**. La capacità massima della soluzione per un singolo ordine di 5 dischi è di 35 TB. È possibile creare più ordini per volumi di dati maggiori. 

     ![Selezionare l'opzione Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

6.  In **Ordine** specificare i **Dettagli ordine**. Immettere o selezionare le informazioni seguenti.

    |Impostazione|Valore|
    |---|---|
    |NOME|Specificare un nome descrittivo per tenere traccia dell'ordine.<br> Il nome può contenere da 3 a 24 caratteri che possono essere lettere, numeri e trattini. <br> Il nome deve iniziare e terminare con una lettera o un numero. |
    |Gruppo di risorse| Usare un gruppo esistente o crearne uno nuovo. <br> Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme. |
    |Area di Azure di destinazione| Selezionare l'area per l'account di archiviazione.<br> Attualmente sono supportati gli account di archiviazione in tutte le aree di Stati Uniti, Europa occidentale e settentrionale, Canada e Australia. |
    |Account di archiviazione|In base all'area di Azure specificata, selezionare un account di archiviazione esistente nell'elenco filtrato. <br>È anche possibile creare un nuovo account Utilizzo generico v1 o Utilizzo generico v2. |
    |Dimensioni dei dati stimate in TB| Immettere una stima in TB. <br>In base alle dimensioni dei dati, Microsoft invia un numero appropriato di dischi SSD da 8 TB (con capacità utilizzabile di 7 TB). <br>La capacità massima utilizzabile nei 5 dischi è di 35 TB. |

13. Fare clic su **Avanti**. 

    ![Specificare i dettagli dell'ordine](media/data-box-disk-deploy-ordered/data-box-order-details.png)

14. Nella scheda **Indirizzo di spedizione** specificare nome e cognome, nome e indirizzo postale della società e un numero di telefono valido. Fare clic su **Convalida indirizzo**. Il servizio convalida l'indirizzo di spedizione per la disponibilità del servizio. Se il servizio è disponibile per l'indirizzo di spedizione specificato, si riceve una notifica in tal senso. 

    ![Specificare l'indirizzo di spedizione](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
15. In **Dettagli notifica** specificare gli indirizzi di posta elettronica. Il servizio invia notifiche tramite posta elettronica per qualsiasi aggiornamento dello stato dell'ordine agli indirizzi di posta elettronica specificati. 

    È consigliabile usare un indirizzo di posta elettronica di gruppo in modo da continuare a ricevere le notifiche anche se un amministratore del gruppo non è disponibile.

16. Verificare il **riepilogo** delle informazioni relative a ordine, contatti, notifiche e informativa sulla privacy. Selezionare la casella corrispondente per accettare le condizioni per la privacy.

17. Fare clic su **Ordina**. Per la creazione dell'ordine sono richiesti pochi minuti.

 
## <a name="track-the-order"></a>Monitorare l'ordine

Dopo aver inserito l'ordine, è possibile monitorare lo stato dell'ordine dal portale dell'anteprima di Azure. Passare all'ordine e quindi a **Panoramica** per visualizzare lo stato. Il portale mostra il processo con stato **Ordinato**. 

![Stato del disco di Data Box ordinato](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png) 

Se i dischi non sono disponibili, si riceverà una notifica. Se i dischi sono disponibili, Microsoft identifica i dischi per la spedizione e prepara il pacchetto dei dischi. Durante la preparazione dei dischi vengono eseguite le azioni seguenti:

- I dischi vengono crittografati con crittografia BitLocker AES-128.  
- I dischi vengono bloccati per impedire l'accesso non autorizzato.
- Durante questo processo viene generata la passkey che sblocca i dischi.

Una volta completata la preparazione dei dischi, lo stato dell'ordine nel portale diventa **Elaborato**.

Microsoft prepara e spedisce quindi i dischi tramite un vettore dell'area. Verrà comunicato un numero di tracciabilità dopo la spedizione dei dischi. Il portale mostra l'ordine con lo stato **Spedito**.



## <a name="cancel-the-order"></a>Annullare l'ordine

Per annullare questo ordine, nel portale dell'anteprima di Azure passare a **Panoramica** e fare clic su **Annulla** dalla barra dei comandi. 

È possibile annullare solo quando i dischi sono stati ordinati ed è in corso l'elaborazione dell'ordine per la spedizione. Dopo l'elaborazione dell'ordine, non è più possibile annullarlo. 

![Annullare l'ordine](media/data-box-disk-deploy-ordered/cancel-order1.png)

Per eliminare un ordine annullato, passare a **Panoramica** e fare clic su **Elimina** dalla barra dei comandi. 


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Iscriversi per Data Box Disk
> * Ordinare un disco di Data Box
> * Monitorare l'ordine
> * Annullare l'ordine

Passare all'esercitazione successiva per informazioni su come configurare Data Box Disk.

> [!div class="nextstepaction"]
> [Configurare Azure Data Box Disk](./data-box-disk-deploy-set-up.md)


