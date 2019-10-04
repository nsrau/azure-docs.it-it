---
title: SKU e piani privati | Azure Marketplace
description: Come usare gli SKU privati per gestire la disponibilità dell'offerta.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: pabutler
ms.openlocfilehash: 940b50cf4a04abacd4d7be2104dd97fb8b3db736
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883127"
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

È possibile riutilizzare i dischi in un altro SKU e modificare il piano tariffario o la descrizione. Per riutilizzare i dischi, selezionare **Sì** come risposta alla richiesta "questo SKU riutilizza immagini da uno SKU pubblico".

Se lo SKU è contrassegnato come privato e l'offerta ha altri SKU con dischi riutilizzabili, è necessario indicare che lo SKU riutilizza i dischi di un altro SKU. Viene anche richiesto di specificare il pubblico target dello SKU privato.

>[!NOTE]
>Dopo la pubblicazione, uno SKU pubblico non può essere reso privato.

<a name="select-an-image"></a>Selezionare un'immagine
------------------

È possibile specificare nuovi dischi per lo SKU privato o riutilizzare gli stessi dischi già specificati in un altro SKU, modificando solo il piano tariffario o la descrizione. Per riutilizzare i dischi, selezionare **Sì** come risposta alla richiesta "questa immagine riutilizzo SKU da uno SKU pubblico".

![Indica il riutilizzo delle immagini](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Dopo aver confermato che lo SKU riutilizza le immagini, selezionare lo SKU di origine o di *base* per le immagini:

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

<a name="managing-private-audiences"></a>Gestione dei destinatari privati
-------------------------

**Per aggiornare i destinatari senza ripubblicare l'intera offerta, è necessario apportare le modifiche desiderate al pubblico (usando l'interfaccia utente o l'API), quindi avviare l'azione "Sincronizza i destinatari privati".**

Se il pubblico è costituito da un massimo di 10 sottoscrizioni, è possibile gestirlo completamente usando l'interfaccia utente di CPP.

Se il pubblico è costituito da più di 10 sottoscrizioni, è possibile gestirlo usando un file CSV che può essere caricato nell'interfaccia utente di CPP o usando l'API.

Se si usa l'API e non si vuole mantenere un file CSV, è possibile gestire direttamente i destinatari usando l'API in base alle istruzioni riportate di seguito.

> [!NOTE]
> Usare l'ID sottoscrizione di Azure (piani e SKU) o l'ID tenant (solo piani) per aggiungere un gruppo di destinatari all'offerta privata.

###  <a name="managing-subscriptions-with-the-api"></a>Gestione delle sottoscrizioni con l'API

È possibile usare l'API per caricare un volume CSV o gestire i destinatari direttamente (senza usare un volume CSV). In generale, è sufficiente recuperare l'offerta, aggiornare l' `restrictedAudience` oggetto, quindi inviare di nuovo le modifiche all'offerta per aggiungere o rimuovere i membri del gruppo di destinatari.

Di seguito viene illustrato come aggiornare l'elenco di destinatari a livello di codice:

1. [Recuperare i dati dell'offerta](cloud-partner-portal-api-retrieve-specific-offer.md) :

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. Trovare oggetti audience limitati in ogni SKU dell'offerta usando questa query JPath:

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. Aggiornare gli oggetti destinatari limitati per l'offerta.

    **Se l'elenco delle sottoscrizioni per l'offerta privata è stato caricato originariamente dal file CSV:**

    Gli oggetti *restrictedAudience* appariranno come segue.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    Per ogni oggetto audience con restrizioni:

    a. Scaricare il contenuto di `restrictedAudience.uploadedCsvUri`. Il contenuto è semplicemente un file CSV con intestazioni. Esempio:

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. Aggiungere o eliminare sottoscrizioni nel file CSV scaricato in base alle esigenze.

    c. Caricare il file CSV aggiornato in un percorso, ad esempio [archiviazione BLOB di Azure](../../storage/blobs/storage-blobs-overview.md) o [OneDrive](https://onedrive.live.com), e creare un collegamento di sola lettura al file. Questo sarà il nuovo *SasUrl*.

    d. Aggiornare la `restrictedAudience.uploadedCsvUri` chiave con il nuovo *SasUrl*.

    **Se è stato immesso manualmente l'elenco originale di sottoscrizioni per l'offerta privata dall'portale Cloud Partner:**

    L'aspetto degli oggetti *restrictedAudience* sarà simile al seguente:

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

    a. Per ogni oggetto audience con restrizioni, aggiungere o eliminare le voci `restrictedAudience.manualEntries` nell'elenco in base alle esigenze.

4. Al termine dell'aggiornamento di tutti gli oggetti *restrictedAudience* per ogni SKU dell'offerta privata, [aggiornare l'offerta](cloud-partner-portal-api-creating-offer.md):

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    Con questo, l'elenco dei destinatari aggiornati è ora attivo.

<a name="previewing-private-offers"></a>Anteprima delle offerte private
-------------------------

Nella fase di anteprima/processo di gestione temporanea, solo le sottoscrizioni di anteprima a livello di offerta saranno in grado di accedere allo SKU. In questa fase di test è possibile visualizzare l'anteprima dell'offerta come verrebbe visualizzata ai clienti di destinazione.

Sottoscrizioni di anteprima a livello di offerta per accedere alle offerte in gestione temporanea:

![Preview Subscription Ids (ID sottoscrizione di anteprima)](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Dopo che l'offerta è attiva, solo le sottoscrizioni con gruppo di destinatari limitato (inserite tramite immissione manuale o file CSV) saranno in grado di visualizzare e distribuire lo SKU privato. È consigliabile **includere sempre le proprie sottoscrizioni nel gruppo di destinatari limitato** per lo SKU privato ai fini della convalida.

>[!NOTE]
>A scopo di debug, anche il supporto tecnico Microsoft e i team di progettazione avranno accesso a queste offerte private.
