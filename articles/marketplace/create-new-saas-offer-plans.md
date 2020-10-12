---
title: Come creare piani per l'offerta SaaS nel centro per i partner Microsoft
description: Come creare piani per una nuova offerta SaaS (Software as a Service) usando il portale di Microsoft Commercial Marketplace nel centro per i partner.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 63629f32c97611fa8bc57ecdac968552375125f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89380993"
---
# <a name="how-to-create-plans-for-your-saas-offer"></a>Come creare piani per l'offerta SaaS

Le offerte vendute attraverso Microsoft Commercial Marketplace devono avere almeno un piano. È possibile creare un'ampia gamma di piani con diverse opzioni all'interno della stessa offerta. Questi piani, detti anche SKU, possono differire in termini di versione, monetizzazione o livelli di servizio. Per istruzioni dettagliate sui piani, vedere [piani e prezzi per le offerte per Marketplace commerciali](plans-pricing.md).

> [!NOTE]
> Se si sceglie di elaborare le transazioni in modo indipendente, questa opzione non verrà visualizzata. È invece possibile passare a [come commercializzare l'offerta SaaS](create-new-saas-offer-marketing.md).

## <a name="create-a-plan"></a>Creare un piano

1. Nella parte superiore della scheda **panoramica piano** selezionare **+ Crea nuovo piano**.

1. Nella finestra di dialogo visualizzata, nella casella **ID piano** , immettere un ID piano univoco. Usare un massimo di 50 caratteri alfanumerici minuscoli, trattini o caratteri di sottolineatura. Non è possibile modificare l'ID del piano dopo aver selezionato **Crea**.

1. Nella casella **nome piano** immettere un nome univoco per il piano. Usare un massimo di 50 caratteri.

1. Selezionare **Crea**.

## <a name="define-the-plan-listing"></a>Definire l'elenco dei piani

Nella scheda **elenco piani** è possibile definire il nome e la descrizione del piano come si desidera che vengano visualizzati nel Marketplace commerciale.

1. Nella casella **nome piano** il nome specificato in precedenza per questo piano viene visualizzato qui. È possibile modificarlo in qualsiasi momento. Questo nome verrà visualizzato nel Marketplace commerciale come titolo del piano software dell'offerta.

1. Nella casella **Descrizione piano** spiegare cosa rende univoco questo piano software e qualsiasi differenza rispetto ad altri piani all'interno dell'offerta. Questa descrizione può contenere un massimo di 500 caratteri.
1. Selezionare **Salva bozza** prima di continuare con la scheda successiva: **prezzi e disponibilità**.

## <a name="define-markets-pricing-and-availability"></a>Definire mercati, prezzi e disponibilità

Ogni piano deve essere disponibile in almeno un mercato. Nella scheda **prezzi e disponibilità** è possibile configurare i mercati in cui sarà disponibile questo piano, il modello di monetizzazione, il prezzo e le condizioni di fatturazione desiderati. Inoltre, è possibile indicare se rendere il piano visibile a tutti o solo a specifici clienti (detto anche piano privato).

1. In **mercati**selezionare il collegamento **modifica mercati** .
1. Nella finestra di dialogo visualizzata selezionare le località di mercato in cui si desidera rendere disponibile il piano. È necessario selezionare un minimo di uno e un massimo di 141 mercati.

   > [!NOTE]
   > Questa finestra di dialogo include una casella di ricerca e un'opzione per filtrare solo i paesi "fiscali rimittenti", in cui Microsoft imposta le vendite e usa le imposte per conto dell'utente.

1. Selezionare **Salva**per chiudere la finestra di dialogo.

## <a name="define-a-pricing-model"></a>Definire un modello di determinazione dei prezzi

È necessario associare un modello di determinazione dei prezzi a ogni piano, ovvero una _Tariffa_ fissa o _per utente_. Tutti i piani nella stessa offerta devono utilizzare lo stesso modello di determinazione prezzi. Un'offerta, ad esempio, non può avere un piano di tariffa fissa e un altro piano per ogni utente. Per altre informazioni, vedere [modelli tariffari Saas](plan-saas-offer.md#saas-pricing-models).

> [!IMPORTANT]
> Dopo la pubblicazione dell'offerta, non è possibile modificare il modello di determinazione dei prezzi. Inoltre, tutti i piani per la stessa offerta devono condividere lo stesso modello di determinazione prezzi.

### <a name="configure-flat-rate-pricing"></a>Configurare prezzi forfettari

1. Nella scheda **prezzi e disponibilità** , in **prezzi**, selezionare **tariffa flat**.
1. Selezionare la casella di controllo **mensile** o **annuale** oppure entrambi, quindi immettere il prezzo.

### <a name="add-a-custom-meter-dimension"></a>Aggiungere una dimensione del contatore personalizzata

Questa opzione è disponibile solo se è stato selezionato tariffa flat rate. Per altre informazioni, vedere [fatturazione a consumo per Saas usando il servizio di misurazione del Marketplace commerciale](./partner-center-portal/saas-metered-billing.md).

1. In **dimensioni del servizio di misurazione del Marketplace**selezionare il collegamento **Aggiungi una dimensione del contatore personalizzata (max 30)** .
1. Nella casella **ID** immettere il riferimento all'identificatore non modificabile durante la creazione degli eventi di utilizzo.
1. Nella casella **nome visualizzato** immettere il nome visualizzato associato alla dimensione. Ad esempio, "SMS inviati".
1. Nella casella **unità di misura** immettere la descrizione dell'unità di fatturazione. Ad esempio, "per messaggio di testo" o "per ogni 100 di messaggi di posta elettronica".
1. Nella casella **Prezzo per unità in USD** immettere il prezzo di un'unità della dimensione.
1. Nella casella **quantità mensile inclusa in base** immettere la quantità (come numero intero) della dimensione inclusa ogni mese per i clienti che pagano la tariffa mensile ricorrente. Per impostare una quantità illimitata, selezionare invece la casella di controllo.
1. Nella casella **quantità annua inclusa nella** casella di base immettere la quantità della dimensione (sotto forma di numero intero) inclusa ogni mese per i clienti che pagano la tariffa annuale ricorrente. Per impostare una quantità illimitata, selezionare invece la casella di controllo.
1. Per aggiungere un'altra dimensione di misurazione personalizzata, selezionare il collegamento **Aggiungi un'altra dimensione** , quindi ripetere i passaggi da 1 a 7.

### <a name="configure-per-user-pricing"></a>Configurare i prezzi per utente

1. Nella scheda **prezzi e disponibilità** , in **prezzi**, selezionare **per utente**.
2. Se applicabile, in **limiti utente**specificare il numero minimo e massimo di utenti per questo piano.
3. In **periodo di fatturazione**specificare un prezzo mensile, un prezzo annuale o entrambi.

### <a name="validate-custom-prices"></a>Convalida prezzi personalizzati

Per impostare prezzi personalizzati in un singolo mercato, esportare, modificare e quindi importare il foglio di calcolo dei prezzi. L'utente è responsabile della convalida dei prezzi e del possesso di queste impostazioni. Per informazioni dettagliate, vedere [prezzi personalizzati](plans-pricing.md#custom-prices).

1. Salvare le modifiche apportate ai prezzi per consentire l'esportazione dei dati relativi ai prezzi. Nella parte inferiore della scheda relativa a **prezzi e disponibilità** selezionare **Salva bozza**.
1. In **prezzi**selezionare il collegamento **Esporta dati sui prezzi** .
1. Aprire il file exportedPrice.xlsx in Microsoft Excel.
1. Nel foglio di calcolo, apportare gli aggiornamenti desiderati alle informazioni sui prezzi e quindi salvare il. File CSV.<br> Per poter aggiornare il file, potrebbe essere necessario abilitare la modifica in Excel.
2. Nella scheda **prezzi e disponibilità** , in **prezzi**, selezionare il collegamento **Importa dati prezzi** .
3. Nella finestra di dialogo visualizzata fare clic su **Sì**.
4. Selezionare il file exportedPrice.xlsx aggiornato e quindi fare clic su **Apri**.

### <a name="enable-a-free-trial"></a>Abilita una versione di valutazione gratuita

È possibile configurare una versione di valutazione gratuita per ogni piano nell'offerta. Selezionare la casella di controllo per consentire una versione di valutazione gratuita di un mese. Questa casella di controllo non è disponibile per i piani che usano il servizio di misurazione del Marketplace. Per altre informazioni, vedere [versioni di valutazione gratuite](plans-pricing.md#free-trials).

> [!IMPORTANT]
> Dopo che l'offerta transazionale è stata pubblicata con una versione di valutazione gratuita, non è possibile disabilitarla per quel piano. Assicurarsi che questa impostazione sia corretta prima di pubblicare l'offerta per evitare di dover ricreare il piano.

- In **versione di valutazione gratuita**selezionare la casella **di controllo Consenti una versione di valutazione gratuita di un mese** .

## <a name="choose-who-can-see-your-plan"></a>Scegliere chi può visualizzare il piano

È possibile configurare ogni piano in modo che sia visibile a tutti o solo a un destinatario specifico. Si concede l'accesso a un piano privato usando gli ID tenant con l'opzione per includere una descrizione di ogni ID tenant assegnato. È possibile aggiungere un massimo di 10 ID tenant manualmente o fino a 20.000 ID tenant usando. File CSV.

> [!NOTE]
> Se si pubblica un piano privato, è possibile modificarne la visibilità a Public in un secondo momento. Tuttavia, dopo aver pubblicato un piano pubblico, non è possibile modificarne la visibilità in privato.

### <a name="make-your-plan-public"></a>Rendere pubblico il piano

1. In **visibilità piano**selezionare la casella **pubblica** .
1. Selezionare **Salva bozza**, quindi nella parte superiore sinistra della scheda selezionare **piano Panoramica** per tornare alla scheda **panoramica piano** .
1. Per creare un altro piano per questa offerta, fare clic su **+ Crea nuovo piano**nella parte superiore della scheda **panoramica piano** . Ripetere quindi i passaggi nella sezione [creare un piano](#create-a-plan) . In caso contrario, vedere [visualizzare i piani](#view-your-plans).

### <a name="manually-add-tenant-ids-for-a-private-plan"></a>Aggiungere manualmente gli ID tenant per un piano privato 

1. In **visibilità piano**selezionare la casella **questo è un piano privato** .
1. Nella casella **ID tenant** visualizzata immettere il Azure ad ID tenant del gruppo di destinatari a cui si vuole concedere l'accesso a questo piano privato. È necessario almeno un ID tenant.
1. Opzionale Immettere una descrizione di questo gruppo di destinatari nella casella **Descrizione** .
1. Per aggiungere un altro ID tenant, ripetere i passaggi 2 e 3.
1. Al termine dell'aggiunta degli ID tenant, selezionare **Salva bozza**, quindi in alto a sinistra nella scheda selezionare **panoramica piano** per tornare alla scheda **panoramica piano** .
1. Per creare un altro piano per questa offerta, fare clic su **+ Crea nuovo piano**nella parte superiore della scheda **panoramica piano** . Ripetere quindi i passaggi nella sezione [creare un piano](#create-a-plan) . In caso contrario, vedere [visualizzare i piani](#view-your-plans).

### <a name="use-a-csv-file-for-a-private-plan"></a>Usare. File CSV per un piano privato

1. In **visibilità piano**selezionare la casella **questo è un piano privato** .
2. Selezionare il collegamento **Esporta destinatari (CSV)** .
3. Aprire. File CSV e aggiungere gli ID di Azure a cui si vuole concedere l'accesso all'offerta privata alla colonna **ID** .
4. Facoltativamente, immettere una descrizione per ogni gruppo di destinatari nella colonna **Descrizione** .
5. Aggiungere "TenantID" nella colonna **Type** per ogni riga con un ID Azure.
6. Salvare il. File CSV.
7. Nella scheda **prezzi e disponibilità** , in **visibilità piano**, selezionare il collegamento **Importa destinatari (CSV)** .
8. Nella finestra di dialogo visualizzata selezionare **Sì**.
9. Selezionare il. File CSV, quindi selezionare **Apri**.
10. Selezionare **Salva bozza**, quindi nella parte superiore sinistra della scheda selezionare **piano Panoramica** per tornare alla scheda **panoramica piano** .
11. Per creare un altro piano per questa offerta, nella parte superiore della scheda **panoramica piano** selezionare **+ Crea nuovo piano**. Ripetere quindi i passaggi nella sezione [creare un piano](#create-a-plan) . In caso contrario, se si è terminato di creare piani, passare alla sezione successiva: **visualizzare i piani**.

## <a name="view-your-plans"></a>Visualizzare i piani

Dopo aver creato uno o più piani, verranno visualizzati il nome del piano, l'ID del piano, il modello di determinazione dei prezzi, la disponibilità (pubblica o privata), lo stato di pubblicazione corrente e tutte le azioni disponibili nella scheda **Panoramica del piano** .

Le azioni disponibili nella colonna **azione** della scheda **panoramica piano** variano a seconda dello stato del piano e possono includere quanto segue:

- Se lo stato del piano è **bozza**, il collegamento nella colonna **azione** indicherà **Elimina bozza**.
- Se lo stato del piano è **Live**, il collegamento nella colonna **azione** sarà **Stop sell Plan** o **Sync private audience**. Il collegamento per i **destinatari privati della sincronizzazione** pubblicherà solo le modifiche apportate ai destinatari privati, senza pubblicare altri aggiornamenti che potrebbero essere stati apportati all'offerta.

## <a name="next-steps"></a>Passaggi successivi

- Scopri [come commercializzare le tue offerte SaaS](create-new-saas-offer-marketing.md) tramite la **co-selling con Microsoft** e la **rivendita tramite** i programmi CSP.
- [Come testare e pubblicare un'offerta SaaS nel Marketplace commerciale](test-publish-saas-offer.md).
