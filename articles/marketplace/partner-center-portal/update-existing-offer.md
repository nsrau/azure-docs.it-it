---
title: Aggiornare un'offerta di Marketplace commerciale esistente
description: Come apportare aggiornamenti a un'offerta del Marketplace commerciale esistente, inclusa la modifica, l'eliminazione di una bozza, l'annullamento di una richiesta di pubblicazione, la vendita di un'offerta o un piano e la sincronizzazione di destinatari privati.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 01/16/2020
ms.openlocfilehash: ca106625acf28aded0009fd09022843f1016faf3
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056143"
---
# <a name="update-an-existing-offer-in-the-commercial-marketplace"></a>Aggiornare un'offerta esistente nel marketplace commerciale

È possibile visualizzare le offerte esistenti nella scheda **Panoramica** del portale del [Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) nel centro per i partner.

Per aggiornare un'offerta esistente attualmente disponibile nel Marketplace commerciale:

1. Selezionare il nome dell'offerta che si desidera aggiornare. Lo stato dell'offerta può essere elencato come **Anteprima**, in **tempo reale**, **pubblicazione in corso**, **bozza**, **attenzione necessaria**o **non disponibile** (se si è scelto in precedenza di interrompere la vendita dell'offerta). Una volta selezionata, viene visualizzata la pagina di **Panoramica dell'offerta** per l'offerta.
2. Selezionare **Aggiorna** dalla scheda nella pagina Panoramica dell'offerta oppure la voce di menu nel riquadro di spostamento a sinistra per l'area che si vuole aggiornare. Potrebbe essere necessario aggiornare la **configurazione dell'offerta**, le **Proprietà**, l' **elenco di offerte**, l' **Anteprima**, la **configurazione tecnica**, la **Panoramica del piano**o la **test drive**.
3. Apportare le modifiche e selezionare **Salva bozza**. Ripetere questo processo fino a quando non vengono completate tutte le modifiche.

## <a name="review-and-publish-an-updated-offer"></a>Esaminare e pubblicare un'offerta aggiornata

Quando si è pronti per pubblicare l'offerta aggiornata, selezionare **revisione e pubblicazione** da qualsiasi pagina. Viene visualizzata la pagina **revisione e pubblicazione** . In questa pagina è possibile:

- Vedere lo stato di completamento per le sezioni dell'offerta aggiornata: 
    - **Modifiche non pubblicate**: la sezione è stata aggiornata ed è stata completata. Sono stati forniti tutti i dati necessari e non sono stati introdotti errori negli aggiornamenti.
    - **Incompleto**: gli aggiornamenti apportati alla sezione hanno introdotto errori che devono essere corretti o sono necessarie altre informazioni.
- Fornire informazioni aggiuntive al team di test della certificazione per assicurarsi che il test venga eseguito senza problemi.
- Inviare l'offerta aggiornata per la pubblicazione selezionando **pubblica**.  Ti invieremo un messaggio di posta elettronica quando sarà disponibile una versione di anteprima dell'offerta aggiornata per la verifica e la disconnessione.

> [!IMPORTANT]
> È necessario rivedere l'anteprima dell'offerta una volta disponibile e selezionare **Go-Live** per pubblicare l'offerta aggiornata per i destinatari (pubblici o privati).

## <a name="add-a-plan-to-an-existing-offer"></a>Aggiungi un piano a un'offerta esistente

Per aggiungere un nuovo piano all'interno di un'offerta esistente già pubblicata:

1. Con la pagina della **Panoramica** dell'offerta esistente aperta, passare alla pagina **Panoramica del piano** e quindi selezionare **Crea nuovo piano**.
1. Creare un nuovo piano in base alle [linee guida](./create-new-saas-offer.md#plan-overview) utilizzando il **modello di prezzi dei piani esistenti**.
1. Selezionare **Salva bozza** dopo aver modificato il nome del piano.
1. Selezionare **pubblica** quando si è pronti per pubblicare gli aggiornamenti. Viene visualizzata la pagina **[revisione e pubblicazione](#review-and-publish-an-updated-offer)** in cui è disponibile lo stato di completamento degli aggiornamenti.

## <a name="update-a-plan-within-an-existing-offer"></a>Aggiornare un piano in un'offerta esistente

Per apportare modifiche a un piano all'interno di un'offerta esistente già pubblicata:

1. Con la pagina **Panoramica** dell'offerta aperta, scegliere il piano che si desidera modificare. Se il piano non è accessibile dall'elenco **panoramica piano** , selezionare **Visualizza tutti i piani**.
1. Selezionare il **nome**del piano, il **modello di determinazione dei prezzi**o la **disponibilità**. *Attualmente i piani sono disponibili solo in inglese (Stati Uniti)*.
1. Selezionare **Salva bozza** dopo avere apportato modifiche al nome, alla descrizione o alla disponibilità dei destinatari del piano.
1. Selezionare **Verifica e pubblica** quando si è pronti per pubblicare gli aggiornamenti. Viene visualizzata la pagina **[revisione e pubblicazione](#review-and-publish-an-updated-offer)** in cui è disponibile lo stato di completamento degli aggiornamenti.
1. Inviare il piano aggiornato per la pubblicazione selezionando **pubblica**. Si riceverà un messaggio di posta elettronica quando sarà disponibile una versione di anteprima dell'offerta aggiornata per la revisione e la disconnessione.

## <a name="offer-a-virtual-machine-plan-at-a-new-price"></a>Offerta di un piano di macchina virtuale a un nuovo prezzo

Dopo la pubblicazione di un piano di macchina virtuale, il relativo prezzo non può essere modificato. Per offrire lo stesso piano a un prezzo diverso, è necessario nascondere il piano e crearne uno nuovo con il prezzo aggiornato. Prima di tutto, Nascondi il piano con il prezzo che vuoi modificare:

1. Con la pagina **Panoramica** dell'offerta aperta, scegliere il piano che si desidera modificare. Se il piano non è accessibile dall'elenco **panoramica piano** , selezionare **Visualizza tutti i piani**.
1. Selezionare la casella di controllo **Nascondi piano** . Salvare la bozza prima di continuare.

Ora che è stato nascosto il piano con il prezzo precedente, creare una copia del piano con il prezzo aggiornato:

1. Nel centro per i partner tornare alla **Panoramica del piano**.
2. Selezionare **Crea nuovo piano**. Immettere un **ID piano** e un **nome piano**, quindi selezionare **Crea**.
1. Per riutilizzare la configurazione tecnica dal piano nascosto, selezionare la casella di controllo **Riutilizza configurazione tecnica** . Per altre informazioni, vedere [Panoramica del piano](azure-vm-create-offer.md#plan-overview) .
    > [!IMPORTANT]
    > Se si seleziona **questo piano riutilizza la configurazione tecnica da un altro piano**, non sarà più possibile smettere di vendere il piano padre in un secondo momento. Non usare questa opzione se si vuole interrompere la vendita del piano padre.
3. Completare tutte le sezioni necessarie per il nuovo piano, incluso il nuovo prezzo.
1. Selezionare **Salva bozza**.
1. Dopo aver completato tutte le sezioni necessarie per il nuovo piano, selezionare **revisione e pubblicazione**. Questa operazione invierà l'offerta per la revisione e la pubblicazione. Per informazioni dettagliate, leggere [la recensione e pubblicare un'offerta nel Marketplace commerciale](../review-publish-offer.md) .

## <a name="compare-changes-to-commercial-marketplace-offers"></a>Confrontare le modifiche alle offerte del Marketplace commerciale

È possibile controllare le modifiche apportate a un'offerta [pubblicata](#compare-changes-to-published-offer) o in [Anteprima](#compare-changes-to-a-preview-offer) prima di renderle attive usando il **confronto**.

> [!NOTE]
> Un'offerta pubblicata è un'offerta che è stata pubblicata correttamente per la visualizzazione in anteprima o in tempo reale.

Per informazioni generali sul controllo, vedere di seguito:

- È possibile utilizzare **Confronta** in qualsiasi momento durante il processo di modifica.
- Selezionare un campo nella pagina **Confronta** per passare al valore che si desidera modificare.
- Se si è pronti per pubblicare gli aggiornamenti, selezionare **revisione e pubblica**.
- Per visualizzare i valori di tutti i campi, anche i campi non aggiornati, selezionare il filtro **tutti i campi** . È possibile modificare i filtri in questi campi selezionando i **campi modificati**e quindi selezionando uno dei filtri seguenti:
    - Filtro **valori rimossi Visualizza i** campi pubblicati e a questo punto si sta rimuovendo completamente.
    - Filtro **valori aggiunti Visualizza i** campi che non sono stati pubblicati originariamente e che ora aggiungono.
    - Filtro **valori modificati** Visualizza i campi pubblicati ma a questo punto il contenuto è stato aggiornato.

      >[!NOTE]
      > Se uno di questi filtri non è disponibile, significa che non è stato creato un aggiornamento di quel tipo.

- Per visualizzare solo i valori che non sono stati aggiornati, selezionare il filtro **campi non modificati** . I valori dei campi visualizzati per la versione pubblicata e bozza saranno uguali.

  ![Filtri per il confronto degli aggiornamenti per l'offerta pubblicata o in anteprima](./media/compare-changes-marketplace.png)

>[!NOTE]
> Le pagine seguenti attualmente non supportano il **confronto**:
>- Destinatari del rivenditore CSP
>- Configurazione tecnica test drive
>- Elenco del Marketplace di test drive
>- Co-selling
>- File supplementari

### <a name="compare-changes-to-published-offer"></a>Confrontare le modifiche all'offerta pubblicata

Seguire le istruzioni seguenti per confrontare le modifiche apportate dall'offerta pubblicata:

1. Selezionare **Confronta** nella barra dei comandi della pagina. La pagina di **confronto** fornisce versioni affiancate delle modifiche salvate dell'offerta e dell'offerta del Marketplace pubblicata.
2. Quando si accede a **Confronta** da una pagina specifica dell'offerta, per impostazione predefinita vengono visualizzate solo le modifiche apportate a tale pagina.
    - Se si desidera confrontare le modifiche in tutte le pagine, modificare la pagina **selezionando una pagina da confrontare**.  
    - Se si desidera confrontare le modifiche apportate all'offerta in tutte le pagine, selezionare **tutte le pagine**.

Come impostazione predefinita, **Confronta** le nuove modifiche apportate all'offerta Marketplace LIVE.

Ricordarsi di ripubblicare l'offerta dopo avere apportato aggiornamenti per rendere effettive le modifiche.

### <a name="compare-changes-to-a-preview-offer"></a>Confrontare le modifiche di un'offerta di anteprima

Se sono presenti modifiche in anteprima che non sono attive, è possibile confrontare le nuove modifiche con l'offerta di anteprima del Marketplace.

Seguire le istruzioni seguenti per confrontare le nuove modifiche con l'offerta di anteprima del Marketplace:

1. Selezionare **Confronta** nella barra dei comandi della pagina.
2. Selezionare l'elenco **a discesa con** e modificare l'offerta dall' **offerta Live** all' **Anteprima**.
3. La pagina **Confronta** fornisce versioni affiancate che mostrano le modifiche.

>[!NOTE]
>Se l'offerta non è ancora disponibile ma è stata pubblicata in anteprima, non è possibile confrontarla con un'offerta Live.

Ricordarsi di ripubblicare l'offerta dopo avere apportato aggiornamenti per rendere effettive le modifiche.

## <a name="delete-a-draft-offer"></a>Eliminare un'offerta bozza

È possibile eliminare un'offerta bozza (una che non è stata pubblicata) selezionando **Elimina bozza** dalla pagina **Panoramica dell'offerta** . Questa opzione non sarà disponibile se in precedenza è stata pubblicata l'offerta.

Dopo aver confermato che si vuole eliminare l'offerta bozza, l'offerta non sarà più visibile o accessibile nel centro per i partner e verrà aperta la pagina **tutte le offerte** .

## <a name="delete-a-draft-plan"></a>Eliminare un piano bozza

Per eliminare un piano che non è stato pubblicato, selezionare **Elimina bozza** dalla pagina **panoramica piano** . Questa opzione non sarà disponibile se l'offerta è stata pubblicata in precedenza.

Dopo aver confermato che si desidera eliminare il piano bozza, il piano non sarà più visibile né accessibile nel centro per i partner.

## <a name="cancel-publishing"></a>Annulla pubblicazione

Per annullare un'offerta con lo stato **pubblicazione in corso** :

1. Selezionare il nome dell'offerta per aprire la pagina **Panoramica dell'offerta** .
1. Selezionare **Annulla pubblicazione** nell'angolo superiore destro della pagina.
1. Confermare che si desidera arrestare la pubblicazione dell'offerta.

Se si vuole pubblicare l'offerta in un secondo momento, è necessario avviare il processo di pubblicazione.

> [!NOTE]
> È possibile arrestare la pubblicazione di un'offerta solo se l'offerta non è ancora progredita nel passaggio di approvazione dell'editore. Dopo aver selezionato **Go Live** , non sarà più possibile annullare la pubblicazione.

## <a name="stop-selling-an-offer-or-plan"></a>Interrompi la vendita di un'offerta o di un piano

Per diversi motivi, è possibile decidere di rimuovere l'elenco di offerte da Microsoft Commercial Marketplace. La rimozione dell'offerta garantisce che i nuovi clienti non possano più acquistare o distribuire l'offerta, ma non hanno alcun effetto sui clienti esistenti.

Per interrompere la vendita di un'offerta dopo la pubblicazione, selezionare **Interrompi la vendita** dalla pagina **Panoramica dell'offerta** .

Dopo aver confermato che si desidera interrompere la vendita dell'offerta, entro poche ore non sarà più visibile nel Marketplace commerciale e nessun nuovo cliente sarà in grado di scaricarlo.

Per interrompere la vendita di un piano, selezionare **Interrompi la vendita** dalla pagina **Panoramica del piano** . L'opzione per interrompere la vendita di un piano è disponibile solo se nell'offerta è presente più di un piano. È possibile scegliere di interrompere la vendita di un piano senza influito sugli altri piani nell'offerta. Dopo aver verificato che si desidera interrompere la vendita del piano, è necessario ripubblicare l'offerta per rendere effettive le modifiche. Una volta ripubblicata l'offerta, il piano non sarà più visibile nel Marketplace commerciale e nessun nuovo cliente sarà in grado di scaricarlo.

Tutti i clienti che hanno precedentemente acquisito l'offerta o il piano possono comunque utilizzarlo. Possono scaricarlo di nuovo, ma non gli aggiornamenti se si aggiorna e si ripubblica l'offerta o il piano in un secondo momento.

Dopo che la richiesta di interruzione della vendita dell'offerta o del piano è stata completata, verrà comunque visualizzata nel portale del Marketplace commerciale nel centro per i partner con uno stato **non disponibile** .

Se si decide di elencare o vendere questa offerta o pianificare nuovamente, seguire le istruzioni per [aggiornare un'offerta esistente](#update-an-existing-offer-in-the-commercial-marketplace). Non dimenticare che è necessario **pubblicare** di nuovo l'offerta o il piano dopo avere apportato modifiche.

## <a name="remove-offers-from-existing-customers"></a>Rimuovi offerte da clienti esistenti

Per rimuovere le offerte da clienti esistenti, [registrare una richiesta di supporto](https://aka.ms/marketplacepublishersupport). Nell'elenco argomento del supporto selezionare offerta del **Marketplace commerciale**  >  **o annullamento dell'elenco, rimozione o chiusura dell'app** e inviare la richiesta. Il team di supporto guiderà l'utente durante il processo di rimozione dell'offerta.

## <a name="sync-private-plan-audiences"></a>Sincronizzare i destinatari del piano privato

Se l'offerta include uno o più piani configurati per essere disponibili solo per destinatari privati, è possibile aggiornare solo i destinatari che possono accedere a tale piano privato senza pubblicare altre modifiche nell'offerta. 

Per aggiornare e sincronizzare i destinatari privati per i piani:

- Modificare i destinatari in uno o più piani privati usando il pulsante + **Aggiungi ID** o **Importa clienti (CSV)** e quindi salvare le modifiche.
- Selezionare **Sincronizza i destinatari privati** nella pagina **Panoramica del piano** .

I **destinatari privati della sincronizzazione** pubblicheranno solo le modifiche apportate ai destinatari privati, senza pubblicare altri aggiornamenti che potrebbero essere stati apportati all'offerta bozza.

## <a name="next-steps"></a>Passaggi successivi

- [Verificare lo stato di pubblicazione dell'offerta del Marketplace commerciale](./publishing-status.md)
