---
title: Aggiornare un'offerta esistente-Marketplace commerciale Microsoft
description: Come apportare aggiornamenti a un'offerta o a un piano commerciale esistente, sincronizzare i destinatari privati e rimuovere un'offerta.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 10/27/2020
ms.custom: contperfq2
ms.openlocfilehash: 376852c214d503867cd938507760e963b9b75e93
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129984"
---
# <a name="update-existing-offers-in-the-commercial-marketplace"></a>Aggiornare le offerte esistenti nel Marketplace commerciale

Questo articolo illustra come apportare aggiornamenti alle offerte e ai piani esistenti e come rimuovere un'offerta dal Marketplace commerciale. È possibile visualizzare le offerte nella scheda **Panoramica** del portale del [Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) nel centro per i partner.

## <a name="update-a-published-offer"></a>Aggiornare un'offerta pubblicata

Usare questi passaggi per aggiornare un'offerta pubblicata correttamente in anteprima o in stato attivo.

1. Selezionare il nome dell'offerta che si desidera aggiornare. Lo stato dell'offerta può essere elencato come **Anteprima** , in **tempo reale** , **pubblicazione in corso** , **bozza** , **attenzione necessaria** o **non disponibile** (se si è scelto in precedenza di interrompere la vendita dell'offerta). Una volta selezionata, viene visualizzata la pagina di **Panoramica dell'offerta** per l'offerta.
1. Selezionare la pagina offerta che si desidera aggiornare, ad esempio **Proprietà** , **elenco offerte** o **Anteprima** (oppure selezionare **Aggiorna** dalla scheda applicabile nella pagina Panoramica dell' **offerta** ).
1. Apportare le modifiche e selezionare **Salva bozza** . Ripetere questo processo fino a quando non vengono completate tutte le modifiche.
1. Esaminare le modifiche nella pagina **[Confronta](#compare-changes-to-your-offer)** .
1. Quando si è pronti per pubblicare l'offerta aggiornata, selezionare **revisione e pubblicazione** da qualsiasi pagina. Viene visualizzata la pagina **revisione e pubblicazione** . In questa pagina verrà visualizzato lo stato di completamento per le sezioni dell'offerta aggiornata: 
    - **Modifiche non pubblicate** : la sezione è stata aggiornata ed è stata completata. Sono stati forniti tutti i dati necessari e non sono stati introdotti errori negli aggiornamenti.
    - **Incompleto** : gli aggiornamenti apportati alla sezione hanno introdotto errori che devono essere corretti o sono necessarie altre informazioni.
2. Selezionare **pubblica** per inviare l'offerta aggiornata per la pubblicazione. L'offerta passerà quindi attraverso i [passaggi di convalida e pubblicazione](../review-publish-offer.md#validation-and-publishing-steps)standard.

> [!IMPORTANT]
> È necessario rivedere l'anteprima dell'offerta una volta disponibile e selezionare **Go-Live** per pubblicare l'offerta aggiornata per i destinatari (pubblici o privati).

## <a name="add-a-plan-to-an-existing-offer"></a>Aggiungi un piano a un'offerta esistente

Completare questi passaggi per aggiungere un nuovo piano a un'offerta che è già stata pubblicata.

1. Con la pagina della **Panoramica** dell'offerta esistente aperta, passare alla pagina **Panoramica del piano** e quindi selezionare **Crea nuovo piano** .
1. Creare un nuovo piano in base alle [linee guida](../plans-pricing.md) utilizzando il **modello di prezzi dei piani esistenti** .
1. Selezionare **Salva bozza** dopo aver modificato il nome del piano.
1. Selezionare **pubblica** quando si è pronti per pubblicare gli aggiornamenti. Viene visualizzata la pagina **[revisione e pubblicazione](../review-publish-offer.md)** in cui è disponibile lo stato di completamento degli aggiornamenti.

## <a name="update-a-plan-for-an-existing-offer"></a>Aggiornare un piano per un'offerta esistente

Completare questi passaggi per apportare modifiche a un piano per un'offerta già pubblicata.

1. Con la pagina **Panoramica** dell'offerta aperta, scegliere il piano che si desidera modificare. Se il piano non è accessibile dall'elenco **panoramica piano** , selezionare **Visualizza tutti i piani** .
1. Selezionare il **nome** del piano, il **modello di determinazione dei prezzi** o la **disponibilità** . *Attualmente i piani sono disponibili solo in inglese (Stati Uniti)* .
1. Selezionare **Salva bozza** dopo avere apportato modifiche al nome, alla descrizione o alla disponibilità dei destinatari del piano.
1. Selezionare **Verifica e pubblica** quando si è pronti per pubblicare gli aggiornamenti. Viene visualizzata la pagina **[revisione e pubblicazione](../review-publish-offer.md)** in cui è disponibile lo stato di completamento degli aggiornamenti.
1. Selezionare **pubblica** per inviare il piano aggiornato per la pubblicazione. Ti invieremo un messaggio di posta elettronica quando sarà disponibile una versione di anteprima dell'offerta aggiornata che potrai esaminare e approvare.

## <a name="offer-a-virtual-machine-plan-at-a-new-price"></a>Offerta di un piano di macchina virtuale a un nuovo prezzo

Dopo la pubblicazione di un piano di macchina virtuale, il relativo prezzo non può essere modificato. Per offrire lo stesso piano a un prezzo diverso, è necessario nascondere il piano e crearne uno nuovo con il prezzo aggiornato. Prima di tutto, Nascondi il piano con il prezzo che vuoi modificare:

1. Con la pagina **Panoramica** dell'offerta aperta, scegliere il piano che si desidera modificare. Se il piano non è accessibile dall'elenco **panoramica piano** , selezionare **Visualizza tutti i piani** .
1. Selezionare la casella di controllo **Nascondi piano** . Salvare la bozza prima di continuare.

Ora che è stato nascosto il piano con il prezzo precedente, creare una copia del piano con il prezzo aggiornato:

1. Nel centro per i partner tornare alla **Panoramica del piano** .
2. Selezionare **Crea nuovo piano** . Immettere un **ID piano** e un **nome piano** , quindi selezionare **Crea** .
1. Per riutilizzare la configurazione tecnica dal piano nascosto, selezionare la casella di controllo **Riutilizza configurazione tecnica** . Per altre informazioni, vedere [creare piani per un'offerta di macchina virtuale](../azure-vm-create-plans.md) .
    > [!IMPORTANT]
    > Se si seleziona **questo piano riutilizza la configurazione tecnica da un altro piano** , non sarà più possibile smettere di vendere il piano padre in un secondo momento. Non usare questa opzione se si vuole interrompere la vendita del piano padre.
3. Completare tutte le sezioni necessarie per il nuovo piano, incluso il nuovo prezzo.
1. Selezionare **Salva bozza** .
1. Dopo aver completato tutte le sezioni necessarie per il nuovo piano, selezionare **revisione e pubblicazione** . Questa operazione invierà l'offerta per la revisione e la pubblicazione. Per informazioni dettagliate, leggere [la recensione e pubblicare un'offerta nel Marketplace commerciale](../review-publish-offer.md) .

## <a name="sync-private-plan-audiences"></a>Sincronizzare i destinatari del piano privato

Se l'offerta include uno o più piani configurati per essere disponibili solo per destinatari privati, è possibile aggiornare solo i destinatari che possono accedere a tale piano privato senza pubblicare altre modifiche nell'offerta. 

Per aggiornare e sincronizzare i destinatari privati per i piani:

- Modificare i destinatari in uno o più piani privati usando il pulsante + **Aggiungi ID** o **Importa clienti (CSV)** e quindi salvare le modifiche.
- Selezionare **Sincronizza i destinatari privati** nella pagina **Panoramica del piano** .

I **destinatari privati della sincronizzazione** pubblicheranno solo le modifiche apportate ai destinatari privati, senza pubblicare altri aggiornamenti che potrebbero essere stati apportati all'offerta bozza.

## <a name="compare-changes-to-your-offer"></a>Confrontare le modifiche all'offerta

Prima di pubblicare gli aggiornamenti nell'offerta Live o in [Anteprima](#compare-changes-to-a-preview-offer) , è possibile controllare le modifiche salvate nella pagina di **confronto** . È possibile accedere alla pagina di **confronto** nell'angolo superiore destro di qualsiasi pagina dell'offerta, ad esempio la pagina **Proprietà** o **elenco offerte** . La pagina di **confronto** Mostra le versioni affiancate delle modifiche salvate dell'offerta e dell'offerta del Marketplace pubblicata.

- È possibile utilizzare **Confronta** in qualsiasi momento durante il processo di modifica.
- Selezionare un campo nella pagina **Confronta** per passare al valore che si desidera modificare.
- Per visualizzare i valori di tutti i campi, anche i campi non aggiornati, selezionare il filtro **tutti i campi** . È possibile modificare i filtri in questi campi selezionando i **campi modificati** e quindi selezionando uno dei filtri seguenti:
    - Filtro **valori rimossi Visualizza i** campi pubblicati e a questo punto si sta rimuovendo completamente.
    - Filtro **valori aggiunti Visualizza i** campi che non sono stati pubblicati originariamente e che ora aggiungono.
    - Filtro **valori modificati** Visualizza i campi pubblicati ma a questo punto il contenuto è stato aggiornato.

      >[!NOTE]
      > Se uno di questi filtri non è disponibile, significa che non è stato creato un aggiornamento di quel tipo.

- Per visualizzare solo i valori che non sono stati aggiornati, selezionare il filtro **campi non modificati** . I valori dei campi visualizzati per la versione pubblicata e bozza saranno uguali.

  ![Filtri per il confronto degli aggiornamenti per l'offerta pubblicata o in anteprima](./media/compare-changes-marketplace.png)

>[!NOTE]
> Le pagine seguenti attualmente non supportano il **confronto** :
>- Destinatari del rivenditore CSP
>- Configurazione tecnica test drive
>- Elenco del Marketplace di test drive
>- Co-selling
>- File supplementari

Ricordarsi di ripubblicare l'offerta dopo avere apportato aggiornamenti per rendere effettive le modifiche.

### <a name="compare-changes-to-a-preview-offer"></a>Confrontare le modifiche di un'offerta di anteprima

Se sono presenti modifiche in anteprima che non sono attive, è possibile confrontare le nuove modifiche con l'offerta di anteprima del Marketplace.

1. Selezionare **Confronta** nella barra dei comandi della pagina.
2. Selezionare l'elenco **a discesa con** e modificare l'offerta dall' **offerta Live** all' **Anteprima** . Se l'offerta non è ancora attiva, non verrà visualizzata l'opzione **offerta in tempo reale** .
3. La pagina **Confronta** fornisce versioni affiancate che mostrano le modifiche.

Ricordarsi di ripubblicare l'offerta dopo avere apportato aggiornamenti per rendere effettive le modifiche.

## <a name="stop-selling-an-offer-or-plan"></a>Interrompi la vendita di un'offerta o di un piano

È possibile rimuovere gli elenchi di offerte e i piani da Microsoft Commercial Marketplace, in modo da impedire ai nuovi clienti di trovarli e acquistarli. Tutti i clienti che hanno acquisito l'offerta o il piano possono comunque usarlo e possono scaricarlo di nuovo, se necessario. Tuttavia, non riceveranno gli aggiornamenti se si decide di ripubblicare l'offerta o il piano in un secondo momento.

- Per interrompere la vendita di un'offerta dopo la pubblicazione, selezionare **Interrompi la vendita** dalla pagina **Panoramica dell'offerta** . Entro poche ore dalla conferma, l'offerta non sarà più visibile nel Marketplace commerciale.

- Per interrompere la vendita di un piano, selezionare **Interrompi la vendita** dalla pagina **Panoramica del piano** . L'opzione per interrompere la vendita di un piano è disponibile solo se nell'offerta è presente più di un piano. È possibile scegliere di interrompere la vendita di un piano senza influito sugli altri piani nell'offerta.
     >[!NOTE]
     > Dopo aver verificato che si desidera interrompere la vendita del piano, è necessario ripubblicare l'offerta per rendere effettive le modifiche.

Dopo aver interrotto la vendita di un'offerta o di un piano, sarà ancora possibile visualizzarlo nel centro per i partner con uno stato **non disponibile** . Se si decide di elencare o vendere questa offerta o pianificare nuovamente, seguire le istruzioni per [aggiornare un'offerta pubblicata](#update-a-published-offer). Non dimenticare che è necessario **pubblicare** di nuovo l'offerta o il piano dopo avere apportato modifiche.

## <a name="remove-offers-from-existing-customers"></a>Rimuovi offerte da clienti esistenti

Per rimuovere le offerte da clienti esistenti, [registrare una richiesta di supporto](https://aka.ms/marketplacepublishersupport). Nell'elenco argomento del supporto selezionare offerta del **Marketplace commerciale**  >  **o annullamento dell'elenco, rimozione o chiusura dell'app** e inviare la richiesta. Il team di supporto guiderà l'utente durante il processo di rimozione dell'offerta.

## <a name="next-steps"></a>Passaggi successivi

- [Verificare lo stato di pubblicazione dell'offerta del Marketplace commerciale](../review-publish-offer.md)
