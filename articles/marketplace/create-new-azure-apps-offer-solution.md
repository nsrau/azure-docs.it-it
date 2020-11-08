---
title: Configurare un piano di modello di soluzione
description: Informazioni su come configurare un piano di modelli di soluzione per l'offerta di applicazione Azure nel centro per i partner.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 8a8b04501bf81f41fb87de8fc0ce42eb4e7fda93
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370240"
---
# <a name="configure-a-solution-template-plan"></a>Configurare un piano di modello di soluzione

Questo articolo si applica solo ai piani di modelli di soluzione per un'offerta di applicazione Azure. Se si sta configurando un piano di applicazione gestito, vedere [configurare un piano di applicazione gestita](create-new-azure-apps-offer-managed.md).

## <a name="choose-who-can-see-your-plan"></a>Scegliere chi può visualizzare il piano

È possibile configurare ogni piano in modo che sia visibile a tutti o solo a un destinatario specifico. Si concede l'accesso a un pubblico privato usando gli ID sottoscrizione di Azure con l'opzione per includere una descrizione di ogni ID sottoscrizione assegnato. È possibile aggiungere un massimo di 10 ID sottoscrizione manualmente o fino a 10.000 ID sottoscrizione usando. File CSV. Gli ID sottoscrizione di Azure vengono rappresentati come GUID e le lettere devono essere minuscole.

> [!NOTE]
> Se si pubblica un piano privato, è possibile modificarne la visibilità a Public in un secondo momento. Tuttavia, dopo aver pubblicato un piano pubblico, non è possibile modificarne la visibilità in privato.

Nella scheda **disponibilità** , in **visibilità piano** , effettuare una delle operazioni seguenti:

- Per rendere pubblico il piano, selezionare il pulsante di opzione **pubblico** (noto anche come _pulsante_ di opzione).
- Per rendere privato il piano, selezionare il pulsante di opzione **privato** e quindi aggiungere gli ID sottoscrizione di Azure manualmente o con un file CSV.

    > [!NOTE]
    > Un gruppo di destinatari privato o limitato è diverso dai destinatari per l'anteprima definiti nella scheda **Anteprima**. Un gruppo di destinatari per l'anteprima può accedere all'offerta prima che l'offerta venga pubblicata nel marketplace. Mentre la scelta di un gruppo di destinatari privato viene applicata solo a un piano specifico, i destinatari per l'anteprima possono visualizzare tutti i piani (privati o meno) per scopi di convalida.

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Aggiungere manualmente gli ID sottoscrizione di Azure per un piano privato

1. In **visibilità piano** selezionare il pulsante di opzione **privato** .
1. Nella casella **ID sottoscrizione di Azure** visualizzata immettere l'ID sottoscrizione di Azure del gruppo di destinatari a cui si vuole concedere l'accesso a questo piano privato. È necessario almeno un ID sottoscrizione.
1. Opzionale Immettere una descrizione di questo gruppo di destinatari nella casella **Descrizione** .
1. Per aggiungere un altro ID sottoscrizione, selezionare il collegamento **Aggiungi ID (max 10)** e ripetere i passaggi 2 e 3.

## <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Usare. File CSV per aggiungere ID sottoscrizione di Azure per un piano privato

1. In **visibilità piano** selezionare il pulsante di opzione **privato** .
1. Selezionare il collegamento **Esporta destinatari (CSV)** .
1. Aprire. File CSV e aggiungere gli ID sottoscrizione di Azure a cui si vuole concedere l'accesso all'offerta privata alla colonna **ID** .
1. Facoltativamente, immettere una descrizione per ogni gruppo di destinatari nella colonna **Descrizione** .
1. Aggiungere "SubscriptionId" nella colonna **Type** per ogni riga con un ID sottoscrizione.
1. Salvare il. File CSV.
1. Nella scheda **disponibilità** , in **visibilità piano** , selezionare il collegamento **Importa destinatari (CSV)** .
1. Nella finestra di dialogo visualizzata selezionare **Sì**.
1. Selezionare il. File CSV, quindi selezionare **Apri**. Viene visualizzato un messaggio che indica che il. Importazione del file CSV completata.

### <a name="hide-your-plan"></a>Nascondi il piano

Se il modello di soluzione deve essere distribuito solo indirettamente se si fa riferimento a un altro modello di soluzione o a un'applicazione gestita, selezionare la casella di controllo in **Nascondi piano** per pubblicare il modello di soluzione ma nasconderlo dai clienti che eseguono ricerche e ricerche direttamente nel modello.

Selezionare **Salva bozza** prima di continuare con la sezione successiva: definire la configurazione tecnica.

## <a name="define-the-technical-configuration"></a>Definire la configurazione tecnica

Nella scheda **configurazione tecnica** verrà caricato il pacchetto di distribuzione che consente ai clienti di distribuire il piano e fornire un numero di versione per il pacchetto.

> [!NOTE]
> Questa scheda non sarà visibile se si è scelto di riutilizzare i pacchetti di un altro piano nella scheda **Imposta piano** . In tal caso, vedere [visualizzare i piani](#view-your-plans).

### <a name="assign-a-version-number-for-the-package"></a>Assegnare un numero di versione per il pacchetto

Nella casella **versione** specificare la versione corrente della configurazione tecnica. Incrementare questa versione ogni volta che si pubblica una modifica in questa pagina. Il numero di versione deve essere nel formato: integer. Integer. Integer. Ad esempio, `1.0.2`

### <a name="upload-a-package-file"></a>Carica un file di pacchetto

In **file di pacchetto (con estensione zip)** trascinare il file del pacchetto nella casella grigia oppure selezionare il collegamento **Cerca file** .

> [!NOTE]
> Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio `https://upload.xboxlive.com` usato dal Centro per i partner.

### <a name="previously-published-packages"></a>Pacchetti pubblicati in precedenza

Dopo la pubblicazione dell'offerta in tempo reale, la sottoscheda **pacchetti pubblicati in precedenza** viene visualizzata nella pagina **configurazione tecnica** . Questa scheda elenca tutte le versioni precedentemente pubblicate della configurazione tecnica.

## <a name="view-your-plans"></a>Visualizzare i piani

- Selezionare **Salva bozza** , quindi in alto a sinistra nella pagina selezionare **panoramica piano** per tornare alla pagina **panoramica piano** .

Dopo aver creato uno o più piani, verranno visualizzati il nome del piano, l'ID del piano, il tipo di piano, la disponibilità (pubblica o privata), lo stato di pubblicazione corrente e tutte le azioni disponibili nella scheda **panoramica piano** .

Le azioni disponibili nella colonna **azione** della scheda **panoramica piano** variano a seconda dello stato del piano e possono includere quanto segue:

- Se lo stato del piano è **bozza** , il collegamento nella colonna **azione** indicherà **Elimina bozza**.
- Se lo stato del piano è **Live** , il collegamento nella colonna **azione** verrà interrotto a **vendere piano** o **sincronizzare i destinatari privati**. Il collegamento per i **destinatari privati della sincronizzazione** pubblicherà solo le modifiche apportate ai destinatari privati, senza pubblicare altri aggiornamenti che potrebbero essere stati apportati all'offerta.
- Per creare un altro piano per questa offerta, nella parte superiore della scheda **panoramica piano** selezionare **+ Crea nuovo piano**. Ripetere quindi i passaggi in [come creare piani per l'offerta di applicazione Azure](create-new-azure-apps-offer-plans.md). In caso contrario, se si è terminato di creare i piani, passare alla sezione successiva: passaggi successivi.

## <a name="next-steps"></a>Passaggi successivi

- [Come testare e pubblicare l'offerta applicazione Azure](create-new-azure-apps-offer-test-publish.md).
- Scopri [come commercializzare il tuo applicazione Azure offerta](create-new-azure-apps-offer-marketing.md) attraverso la co-selling con Microsoft e la rivendita tramite i programmi CSP.
