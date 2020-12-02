---
title: Configurare un piano di applicazione gestita
description: Informazioni su come configurare un piano di applicazione gestita per l'offerta di applicazione Azure nel centro per i partner.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 61ee0b769664d1f574177e27b5b1fb10e9845035
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463109"
---
# <a name="configure-a-managed-application-plan"></a>Configurare un piano di applicazione gestita

Questo articolo si applica solo ai piani di applicazioni gestite per un'offerta di applicazione Azure. Se si sta configurando un piano di modello di soluzione, vedere [configurare un piano del modello di soluzione](create-new-azure-apps-offer-solution.md).

## <a name="define-markets-pricing-and-availability"></a>Definire mercati, prezzi e disponibilità

Ogni piano deve essere disponibile in almeno un mercato. Nella scheda **prezzi e disponibilità** è possibile configurare i mercati in cui sarà disponibile il piano, il prezzo e se rendere il piano visibile a tutti o solo a specifici clienti (detto anche piano privato).

1. In **mercati** selezionare il collegamento **modifica mercati** .
1. Nella finestra di dialogo visualizzata selezionare le località di mercato in cui si desidera rendere disponibile il piano. È necessario selezionare un minimo di uno e un massimo di 141 mercati.

    > [!NOTE]
    > Questa finestra di dialogo include una casella di ricerca e un'opzione per filtrare solo i paesi "fiscali rimittenti", in cui Microsoft imposta le vendite e usa le imposte per conto dell'utente.

1. Selezionare **Salva** per chiudere la finestra di dialogo.

## <a name="define-pricing"></a>Definire i prezzi

Nella casella **Price (prezzo** ) specificare il prezzo per mese del piano. Questo prezzo è in aggiunta a qualsiasi infrastruttura di Azure o a costi basati sull'utilizzo sostenuti dalle risorse distribuite da questa soluzione.

Oltre al prezzo mensile, è anche possibile impostare i prezzi per l'utilizzo di unità non standard tramite la [fatturazione a consumo](partner-center-portal/azure-app-metered-billing.md). È anche possibile impostare il prezzo al mese su zero e addebitare esclusivamente la fatturazione a consumo.

I prezzi sono impostati in USD (USD = Stati Uniti dollaro) vengono convertiti nella valuta locale di tutti i mercati selezionati usando i tassi di cambio correnti quando vengono salvati. Convalidare questi prezzi prima della pubblicazione esportando il foglio di calcolo dei prezzi e controllando il prezzo in ogni mercato. Per impostare prezzi personalizzati in un singolo mercato, modificare e importare il foglio di calcolo dei prezzi.

### <a name="add-a-custom-meter-dimension-optional"></a>Aggiungere una dimensione del contatore personalizzata (facoltativo)

1. In **dimensioni del servizio di misurazione del Marketplace** selezionare il collegamento **Aggiungi una dimensione del contatore personalizzata (Max 18)** .
1. Nella casella **ID** immettere il riferimento all'identificatore non modificabile durante la creazione degli eventi di utilizzo.
1. Nella casella **nome visualizzato** immettere il nome visualizzato associato alla dimensione. Ad esempio, "SMS inviati".
1. Nella casella **unità di misura** immettere la descrizione dell'unità di fatturazione. Ad esempio, "per messaggio di testo" o "per ogni 100 di messaggi di posta elettronica".
1. Nella casella **Prezzo per unità in USD** immettere il prezzo di un'unità della dimensione.
1. Nella casella **quantità mensile inclusa in base** immettere la quantità (come numero intero) della dimensione inclusa ogni mese per i clienti che pagano la tariffa mensile ricorrente. Per impostare una quantità illimitata, selezionare invece la casella di controllo.
1. Per aggiungere un'altra dimensione del contatore personalizzata, ripetere i passaggi da 1 a 7.

### <a name="set-custom-prices-optional"></a>Imposta prezzi personalizzati (facoltativo)

I prezzi impostati in USD (USD = dollaro statunitense) vengono convertiti nella valuta locale di tutti i mercati selezionati usando i tassi di cambio correnti quando vengono salvati. Convalidare questi prezzi prima della pubblicazione esportando il foglio di calcolo dei prezzi e controllando il prezzo in ogni mercato. Per impostare prezzi personalizzati in un singolo mercato, modificare e importare il foglio di calcolo dei prezzi.

Esaminare attentamente i prezzi prima della pubblicazione, in quanto esistono alcune limitazioni relative alle modifiche consentite dopo la pubblicazione di un piano.

> [!NOTE]
> In seguito alla pubblicazione, non sarà più possibile modificare il prezzo per un mercato del piano.

Per impostare prezzi personalizzati in un singolo mercato, esportare, modificare e quindi importare il foglio di calcolo dei prezzi. L'utente è responsabile della convalida dei prezzi e del possesso di queste impostazioni. Per informazioni dettagliate, vedere [prezzi personalizzati](plans-pricing.md#custom-prices).

1. Salvare le modifiche apportate ai prezzi per consentire l'esportazione dei dati relativi ai prezzi. Nella parte inferiore della scheda relativa a **prezzi e disponibilità** selezionare **Salva bozza**.
1. In **prezzi** selezionare il collegamento **Esporta dati sui prezzi** .
1. Aprire il file exportedPrice.xlsx in Microsoft Excel.
1. Nel foglio di calcolo, apportare gli aggiornamenti desiderati alle informazioni sui prezzi e quindi salvare il file.

   Per poter aggiornare il file, potrebbe essere necessario abilitare la modifica in Excel.

1. Nella scheda **prezzi e disponibilità** , in **prezzi**, selezionare il collegamento **Importa dati prezzi** .
1. Nella finestra di dialogo visualizzata fare clic su **Sì**.
1. Selezionare il file exportedPrice.xlsx aggiornato e quindi fare clic su **Apri**.

## <a name="choose-who-can-see-your-plan"></a>Scegliere chi può visualizzare il piano

È possibile configurare ogni piano in modo che sia visibile a tutti o solo a un destinatario specifico. Si concede l'accesso a un pubblico privato usando gli ID sottoscrizione di Azure con l'opzione per includere una descrizione di ogni ID sottoscrizione assegnato. È possibile aggiungere un massimo di 10 ID sottoscrizione manualmente o fino a 10.000 ID sottoscrizione usando. File CSV. Gli ID sottoscrizione di Azure vengono rappresentati come GUID e le lettere devono essere minuscole.

> [!NOTE]
> Se si pubblica un piano privato, è possibile modificarne la visibilità a Public in un secondo momento. Tuttavia, dopo aver pubblicato un piano pubblico, non è possibile modificarne la visibilità in privato.

In **visibilità piano** eseguire una delle operazioni seguenti:

- Per rendere pubblico il piano, selezionare il pulsante di opzione **pubblico** (noto anche come _pulsante_ di opzione).
- Per rendere privato il piano, selezionare il pulsante di opzione **privato** e quindi aggiungere gli ID sottoscrizione di Azure manualmente o con un file CSV.

> [!NOTE]
> Un gruppo di destinatari privato o limitato è diverso dai destinatari per l'anteprima definiti nella scheda **Anteprima**. Un gruppo di destinatari per l'anteprima può accedere all'offerta prima che l'offerta venga pubblicata nel marketplace. Mentre la scelta di un gruppo di destinatari privato viene applicata solo a un piano specifico, i destinatari per l'anteprima possono visualizzare tutti i piani (privati o meno) per scopi di convalida.

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Aggiungere manualmente gli ID sottoscrizione di Azure per un piano privato

1. In **visibilità piano** selezionare il pulsante di opzione **privato** .
1. Nella casella **ID sottoscrizione di Azure** visualizzata immettere l'ID sottoscrizione di Azure del gruppo di destinatari a cui si vuole concedere l'accesso a questo piano privato. È necessario almeno un ID sottoscrizione.
1. Opzionale Immettere una descrizione di questo gruppo di destinatari nella casella **Descrizione** .
1. Per aggiungere un altro ID sottoscrizione, selezionare il collegamento **Aggiungi ID (max 10)** e ripetere i passaggi 2 e 3.

### <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Usare. File CSV per aggiungere ID sottoscrizione di Azure per un piano privato

1. In **visibilità piano** selezionare il pulsante di opzione **privato** .
1. Selezionare il collegamento **Esporta destinatari (CSV)** .
1. Aprire. File CSV e aggiungere gli ID sottoscrizione di Azure a cui si vuole concedere l'accesso all'offerta privata alla colonna **ID** .
1. Facoltativamente, immettere una descrizione per ogni gruppo di destinatari nella colonna **Descrizione** .
1. Aggiungere "SubscriptionId" nella colonna **Type** per ogni riga con un ID sottoscrizione.
1. Salvare il. File CSV.
1. Nella scheda **disponibilità** , in **visibilità piano**, selezionare il collegamento **Importa destinatari (CSV)** .
1. Nella finestra di dialogo visualizzata selezionare **Sì**.
1. Selezionare il. File CSV, quindi selezionare **Apri**. Viene visualizzato un messaggio che indica che il. Importazione del file CSV completata.

## <a name="define-the-technical-configuration"></a>Definire la configurazione tecnica

Nella scheda **configurazione tecnica** verrà caricato il pacchetto di distribuzione che consente ai clienti di distribuire il piano e fornire un numero di versione per il pacchetto. Verranno fornite anche altre informazioni tecniche.

> [!NOTE]
> Questa scheda non sarà visibile se si è scelto di riutilizzare i pacchetti di un altro piano nella scheda **Imposta piano** . In tal caso, vedere [visualizzare i piani](#view-your-plans).

### <a name="assign-a-version-number-for-the-package"></a>Assegnare un numero di versione per il pacchetto

Nella casella **versione** specificare la versione corrente della configurazione tecnica. Incrementare questa versione ogni volta che si pubblica una modifica in questa pagina. Il numero di versione deve essere nel formato: integer. Integer. Integer. Ad esempio: `1.0.2`.

### <a name="upload-a-package-file"></a>Carica un file di pacchetto

In **file di pacchetto (con estensione zip)** trascinare il file del pacchetto nella casella grigia oppure selezionare il collegamento **Cerca file** .

> [!NOTE]
> Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio `https://upload.xboxlive.com` usato dal Centro per i partner.

#### <a name="previously-published-packages"></a>Pacchetti pubblicati in precedenza

La sottoscheda **Pacchetti pubblicati in precedenza** consente di visualizzare tutte le versioni pubblicate della configurazione tecnica.

### <a name="enable-just-in-time-jit-access-optional"></a>Abilita accesso JIT (just-in-Time) (facoltativo)

Per abilitare l'accesso JIT per questo piano, selezionare la casella di controllo **Abilita accesso just-in-time (JIT)** . Perché i consumer dell'applicazione gestita concedano all'account l'accesso permanente, lasciare deselezionata questa opzione. Per altre informazioni su questa opzione, vedere [accesso just-in-time (JIT)](plan-azure-app-managed-app.md#just-in-time-jit-access).

### <a name="select-a-deployment-mode"></a>Selezionare una modalità di distribuzione

Selezionare la modalità di distribuzione **completa** o **incrementale** .

- In modalità **completa** , una ridistribuzione dell'applicazione da parte del cliente comporterà la rimozione delle risorse nel gruppo di risorse gestite se le risorse non sono definite nel [mainTemplate.js](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template).
- In modalità **incrementale** , una ridistribuzione dell'applicazione lascia invariate le risorse esistenti.

Per altre informazioni sulle modalità di distribuzione, vedere [Modalità di distribuzione di Azure Resource Manager](../azure-resource-manager/templates/deployment-modes.md).

### <a name="provide-a-notification-endpoint-url"></a>Fornire un URL dell'endpoint di notifica

Nella casella **URL endpoint di notifica** fornire un endpoint del webhook HTTPS per ricevere le notifiche relative a tutte le operazioni CRUD nelle istanze di applicazioni gestite di questa versione del piano.

### <a name="customize-allowed-customer-actions-optional"></a>Personalizzare le azioni dei clienti consentite (facoltativo)

1. Per specificare le azioni che i clienti possono eseguire sulle risorse gestite, oltre alle `*/read` azioni "" che sono disponibili per impostazione predefinita, selezionare la casella **personalizzare le azioni del cliente consentito** .
1. Nelle caselle visualizzate, fornire le azioni di controllo aggiuntive e le azioni dati consentite che si desidera consentire al cliente di eseguire, separate da punti e virgola. Per consentire ai consumer di riavviare le macchine virtuali, ad esempio, aggiungere `Microsoft.Compute/virtualMachines/restart/action` alla casella **azioni di controllo consentite** .

### <a name="choose-who-can-manage-the-application"></a>Scegliere chi può gestire l'applicazione

Indicare chi deve avere l'accesso di gestione a questa applicazione gestita in ogni area di Azure selezionata: _Azure globale_ e il _cloud di Azure per enti pubblici_. Si utilizzeranno Azure AD identità per identificare gli utenti, i gruppi o le applicazioni a cui si desidera concedere l'autorizzazione per il gruppo di risorse gestite. Per altre informazioni, vedere [pianificare un'applicazione gestita di Azure per un'offerta applicazione Azure](plan-azure-application-offer.md).

Completare la procedura seguente per Azure globale e il cloud di Azure per enti pubblici, come applicabile.

1. Nella casella **Azure Active Directory ID tenant** immettere l'id tenant Azure ad (noto anche come ID directory) contenente le identità degli utenti, dei gruppi o delle applicazioni a cui si desidera concedere le autorizzazioni.
1. Nella casella **ID entità** specificare il Azure ad ID oggetto dell'utente, del gruppo o dell'applicazione a cui si desidera concedere l'autorizzazione per il gruppo di risorse gestite. Identificare l'utente in base al relativo ID principale, disponibile nel pannello [Azure Active Directory utenti](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) del portale di Azure.
1. Dall'elenco **definizione ruolo** selezionare un Azure ad ruolo predefinito. Il ruolo selezionato descrive le autorizzazioni che l'entità avrà sulle risorse nella sottoscrizione del cliente.
1. Per aggiungere un'altra autorizzazione, selezionare il collegamento **Aggiungi autorizzazione (max 100)** e ripetere i passaggi da 1 a 3.

### <a name="policy-settings-optional"></a>Impostazioni criterio (facoltativo)

È possibile configurare un massimo di cinque criteri e una sola istanza di ogni opzione dei criteri. Alcuni criteri richiedono parametri aggiuntivi.

1. In **Impostazioni criteri** selezionare il collegamento **+ Aggiungi criterio (max 5)** .
1. Nella casella **nome** immettere il nome dell'assegnazione dei criteri (limitato a 50 caratteri).
1. Nella casella di riepilogo **criteri** selezionare i criteri di Azure che verranno applicati alle risorse create dall'applicazione gestita nella sottoscrizione del cliente.
1. Nella casella **parametri criteri** specificare il parametro su cui applicare i criteri di controllo e impostazioni di diagnostica.
1. Nella casella di riepilogo **SKU criteri** selezionare il tipo di SKU dei criteri.

    > [!NOTE]
    > Lo SKU dei _criteri standard_ è obbligatorio per i criteri di controllo.

## <a name="view-your-plans"></a>Visualizzare i piani

- Selezionare **Salva bozza**, quindi in alto a sinistra nella pagina selezionare **panoramica piano** per tornare alla pagina **panoramica piano** .

Dopo aver creato uno o più piani, verranno visualizzati il nome del piano, l'ID del piano, il tipo di piano, la disponibilità (pubblica o privata), lo stato di pubblicazione corrente e tutte le azioni disponibili nella scheda **panoramica piano** .

Le azioni disponibili nella colonna **azione** della scheda **panoramica piano** variano a seconda dello stato del piano e possono includere quanto segue:

- Se lo stato del piano è **bozza**, il collegamento nella colonna **azione** indicherà **Elimina bozza**.
- Se lo stato del piano è **Live**, il collegamento nella colonna **azione** verrà interrotto a **vendere piano** o **sincronizzare i destinatari privati**. Il collegamento per i **destinatari privati della sincronizzazione** pubblicherà solo le modifiche apportate ai destinatari privati, senza pubblicare altri aggiornamenti che potrebbero essere stati apportati all'offerta.
- Per creare un altro piano per questa offerta, nella parte superiore della scheda **panoramica piano** selezionare **+ Crea nuovo piano**. Ripetere quindi i passaggi in [come creare piani per l'offerta di applicazione Azure](create-new-azure-apps-offer-plans.md). In caso contrario, se si è terminato di creare i piani, passare alla sezione successiva: passaggi successivi.

## <a name="next-steps"></a>Passaggi successivi

- [Come testare e pubblicare l'offerta applicazione Azure](create-new-azure-apps-offer-test-publish.md).
- Scopri [come commercializzare il tuo applicazione Azure offerta](create-new-azure-apps-offer-marketing.md) attraverso la co-selling con Microsoft e la rivendita tramite i programmi CSP.