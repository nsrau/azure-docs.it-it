---
title: SKU e piani privati Azure Marketplace
description: Come usare gli SKU privati per gestire la disponibilità dell'offerta.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: dsindona
ms.openlocfilehash: ee3ab7be4d15b13a3c0bb014a3ca4d4096299b4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280389"
---
<a name="private-skus-and-plans"></a>SKU e piani privati
============

Gli SKU privati consentono di limitare la disponibilità degli SKU a clienti specifici. Quando uno SKU è contrassegnato come privato, non è disponibile in nessun catalogo pubblico, inclusi [Azure Marketplace](https://azuremarketplace.microsoft.com) e il [portale di Azure](https://portal.azure.com). Nel portale di Azure, solo i clienti con accesso allo SKU possono visualizzarlo. Verranno anche informati di avere accesso a offerte private.

>[!NOTE]
>Gli SKU privati devono avere nuovi ID piano/SKU univoci per evitare ogni eventuale conflitto con gli SKU pubblici.

È possibile usare gli SKU privati per gestire gli scenari seguenti:

1.  Pubblicare il software che si vuole rendere disponibile pubblicamente a clienti specifici e non a livello generale.
2.  Pubblicare le variazioni di software pubblico a un prezzo personalizzato per clienti specifici.
3.  Pubblicare le variazioni di software pubblico con una descrizione e termini personalizzati (tramite una nuova offerta).

Se si vuole solo modificare il prezzo, è possibile riutilizzare i dischi di un altro SKU nella stessa offerta. Con gli SKU privati non è necessario inviare nuovamente i dischi negli SKU.

<a name="mark-a-sku-private"></a>Contrassegnare uno SKU come privato
---------------------

Per contrassegnare uno SKU come privato, impostare l'opzione che chiede se lo SKU è privato:

![Contrassegnare uno SKU come privato](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

È possibile riutilizzare i dischi in un altro SKU e modificare il piano tariffario o la descrizione. Per riutilizzare i dischi, selezionare **Sì** come risposta al prompt "Questo SKU riutilizza le immagini da uno SKU pubblico".

Se lo SKU è contrassegnato come privato e l'offerta ha altri SKU con dischi riutilizzabili, è necessario indicare che lo SKU riutilizza i dischi di un altro SKU. Viene anche richiesto di specificare il pubblico target dello SKU privato.

>[!NOTE]
>Dopo la pubblicazione, uno SKU pubblico non può essere reso privato.

<a name="select-an-image"></a>Selezionare un'immagine
------------------

È possibile specificare nuovi dischi per lo SKU privato o riutilizzare gli stessi dischi già specificati in un altro SKU, modificando solo il piano tariffario o la descrizione. Per riutilizzare i dischi, selezionare **Sì** come risposta al prompt "Riutilizza questo SKU da uno SKU pubblico".

![Indicare il riutilizzo dell'immagine](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Dopo aver verificato che lo SKU riutilizza le immagini, selezionare lo SKU di origine o *di base* per le immagini:

![Selezionare un'immagine](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Quando si pubblica l'offerta, le immagini dello SKU selezionato vengono rese disponibili sotto l'ID SKU privato con le tariffe/termini personalizzati. Lo SKU privato è visibile solo al pubblico di destinazione.

Per gli aggiornamenti delle immagini, viene richiesto solo di aggiornare l'immagine dello SKU sottostante. Dietro le quinte, anche l'immagine dello SKU privato verrà aggiornata automaticamente. Allo stesso modo, se si elimina l'immagine dallo SKU sottostante, essa verrà rimossa anche dello SKU privato.

<a name="restricting-the-audience"></a>Limitazione del numero di destinatari
------------------------

Le offerte private possono essere trovate e distribuite solo dagli utenti di destinazione.
Al momento gli utenti di destinazione sono supportati tramite gli ID sottoscrizione.

Queste sottoscrizioni possono essere immesse tramite un modulo di immissione manuale **per un massimo di 10 sottoscrizioni**, oppure caricando un file CSV che consente **fino a 20.000 sottoscrizioni**.

Immissione manuale per un gruppo di destinatari limitato:

![Limitare manualmente destinatari](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

Caricamento CSV per un gruppo di destinatari limitato:

![Usare un file CSV per limitare i destinatari](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Contenuto del file CSV di esempio:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Quando si passa dall'immissione manuale alla vista di caricamento di un file CSV o dal file CSV all'immissione manuale, l'elenco precedente di ID sottoscrizione con accesso allo SKU non viene mantenuto. Viene visualizzato un avviso e l'elenco viene semplicemente sovrascritto al momento del salvataggio dell'offerta.

<a name="managing-private-audiences"></a>Gestione dei gruppi di destinatari privati
-------------------------

**Per aggiornare il pubblico senza ripubblicare l'intera offerta, apporti le modifiche desiderate al gruppo di destinatari (utilizzando l'interfaccia utente o l'API) e quindi avvii l'azione "Sincronizza gruppi di destinatari privati".**

Se il pubblico è pari o inferiore a 10 sottoscrizioni, è possibile gestirlo interamente utilizzando l'interfaccia utente CPP.

Se il pubblico è più di 10 sottoscrizioni, puoi gestirlo usando un file CSV che puoi caricare nell'interfaccia utente CPP o usando l'API.

Se si utilizza l'API e non si desidera mantenere un file CSV, è possibile gestire il gruppo di destinatari direttamente utilizzando l'API in base alle istruzioni riportate di seguito.

> [!NOTE]
> Usare l'ID sottoscrizione di Azure (Piani e SKU) o l'ID tenant (solo piani) per aggiungere un gruppo di destinatari all'offerta privata.

###  <a name="managing-subscriptions-with-the-api"></a>Gestione delle sottoscrizioni con l'APIManaging subscriptions with the API

Puoi utilizzare l'API per caricare un file CSV o gestire direttamente il tuo pubblico (senza utilizzare un file CSV). In generale, devi semplicemente recuperare la `restrictedAudience` tua offerta, aggiornare l'oggetto, quindi inviare le modifiche alla tua offerta per aggiungere o rimuovere i membri del gruppo di destinatari.

Ecco come aggiornare a livello di codice l'elenco dei segmenti di pubblico:

1. Recupera i dati [dell'offerta:](cloud-partner-portal-api-retrieve-specific-offer.md)

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. Trova oggetti gruppo di destinatari con restrizioni in ogni SKU dell'offerta usando questa query JPath:

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. Aggiorna gli oggetti del pubblico con restrizioni per la tua offerta.

    **Se hai originariamente caricato l'elenco delle sottoscrizioni per la tua offerta privata dal file CSV:**

    Gli oggetti *restrictedAudience* avranno questo aspetto.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    Per ogni oggetto pubblico con restrizioni:

    a. Scaricare il `restrictedAudience.uploadedCsvUri`contenuto di . Il contenuto è semplicemente un file CSV con intestazioni. Ad esempio:

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. Aggiungere o eliminare sottoscrizioni nel file CSV scaricato in base alle esigenze.

    c. Caricare il file CSV aggiornato in un percorso, ad esempio [Archiviazione BLOB](../../storage/blobs/storage-blobs-overview.md) di Azure o [OneDrive,](https://onedrive.live.com)e creare un collegamento di sola lettura al file. Questo sarà il tuo nuovo *SasUrl*.

    d. Aggiornare `restrictedAudience.uploadedCsvUri` la chiave con il nuovo *SasUrl*.

    **Se hai inserito manualmente l'elenco originale degli abbonamenti per la tua offerta privata dal portale Cloud Partner:**

    Gli oggetti *restrictedAudience* avranno un aspetto simile al seguente:

    ```
    "restrictedAudience": {
        "manualEntries": [{
            "type": "subscriptionId",
            "id": "541a269f-3df2-486e-8fe3-c8f9dcf28205",
            "description": "sub1"
            }, {
            "type": "subscriptionId",
            "id": "c0da499c-25ec-4e4b-a42a-6e75635253b9",
            "description": "sub2"
            }
        ]}
    ```

    a. Per ogni oggetto gruppo di destinatari con `restrictedAudience.manualEntries` restrizioni, aggiungere o eliminare voci nell'elenco in base alle esigenze.

4. Al termine dell'aggiornamento di tutti gli oggetti *restrictedAudience* per ogni SKU dell'offerta privata, [aggiornare l'offerta:](cloud-partner-portal-api-creating-offer.md)

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    Con questo, l'elenco dei segmenti di pubblico aggiornato è ora attivo.

<a name="previewing-private-offers"></a>Anteprima delle offerte private
-------------------------

Nella fase di anteprima/processo di gestione temporanea, solo le sottoscrizioni di anteprima a livello di offerta saranno in grado di accedere allo SKU. In questa fase di test è possibile visualizzare in anteprima l'offerta come apparirebbe ai clienti di destinazione.

Sottoscrizioni di anteprima a livello di offerta per accedere alle offerte in gestione temporanea:

![Preview Subscription Ids (ID sottoscrizione di anteprima)](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Dopo che l'offerta è attiva, solo le sottoscrizioni con gruppo di destinatari limitato (inserite tramite immissione manuale o file CSV) saranno in grado di visualizzare e distribuire lo SKU privato. È consigliabile **includere sempre le proprie sottoscrizioni nel gruppo di destinatari limitato** per lo SKU privato ai fini della convalida.

>[!NOTE]
>A scopo di debug, anche il supporto tecnico Microsoft e i team di progettazione avranno accesso a queste offerte private.
