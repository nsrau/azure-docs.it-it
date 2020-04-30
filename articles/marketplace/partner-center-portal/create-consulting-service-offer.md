---
title: Creare un'offerta di servizio di consulenza nel centro per i partner-Azure Marketplace
description: Informazioni su come pubblicare un'offerta del servizio di consulenza in Azure Marketplace o AppSource usando il centro per i partner.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: eff37750f0580a28c9644ee1ffb7fe4e95038709
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869788"
---
# <a name="create-a-consulting-service-offer"></a>Creare un'offerta di servizio di consulenza

> [!IMPORTANT]
> Stiamo muovendo la gestione delle offerte del servizio di consulenza dal portale Cloud Partner al centro per i partner. Fino a quando non viene eseguita la migrazione delle offerte, segui le istruzioni riportate nell' [offerta del servizio di consulenza Azure e Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer) per portale cloud partner per gestire le tue offerte.

Questo articolo descrive come pubblicare un'offerta del servizio di consulenza in [Azure Marketplace](https://azuremarketplace.microsoft.com/) o in [AppSource](https://appsource.microsoft.com/). Elencare le offerte del servizio di consulenza basate su Microsoft [Dynamics 365](https://dynamics.microsoft.com/) e Power Platform in AppSource. Elenca le offerte del servizio di consulenza basate su Microsoft Azure in Azure Marketplace.

Per creare un'offerta di servizio di consulenza in Azure Marketplace o in AppSource Consulting Services, è necessario prima di tutto [avere un account editore nel centro](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)per i partner e l'account deve essere registrato nel programma Commercial Marketplace. Prima di creare l'offerta, esaminare i prerequisiti in consulto dei [prerequisiti del servizio](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites).

## <a name="publishing-benefits"></a>Vantaggi della pubblicazione

Vantaggi della pubblicazione nel Marketplace commerciale:

- Promuovere la propria azienda usando il marchio Microsoft.
- È potenzialmente raggiungibile da più di 100 milioni utenti di Office 365 e Dynamics 365 in AppSource e più di 200.000 organizzazioni tramite Azure Marketplace.
- Ricevi lead di alta qualità da questi Marketplace.
- Ottenere i servizi promossi dai team Microsoft Field e Telesales

## <a name="create-a-new-offer"></a>Creare una nuova offerta

Dopo aver soddisfatto i requisiti descritti in precedenza, attenersi alla procedura seguente per creare un'offerta di servizio di consulenza.

1. Accedere al centro per i [partner](https://partner.microsoft.com)e quindi selezionare **Dashboard** dal menu in alto.
2. Nella barra di spostamento a sinistra selezionare **Marketplace commerciale**, quindi fare clic su **Panoramica**.

    :::image type="content" source="media/cs-menu-overview.png" alt-text="Viene illustrato il menu per il Marketplace commerciale":::

3. Selezionare **+ nuova offerta**, quindi selezionare **servizio di consulenza**.

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="Viene illustrato il pulsante per creare una nuova offerta.":::

4. Immettere un **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

    - Questo ID è visibile ai clienti nell'indirizzo Web dell'offerta del Marketplace.
    - Usare solo lettere minuscole, numeri, trattini e caratteri di sottolineatura, ma senza spazi. La lunghezza è limitata a 50 caratteri. Se ad esempio si immette **test-offer-1**, l'URL dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
    - L'ID offerta può&#39;t deve essere modificato dopo aver selezionato **Crea**.

5. Immettere un **alias dell'offerta**. Questo è il nome usato per fare riferimento all'offerta nel centro per i partner.

    - Questo nome non è&#39;t usato nel Marketplace. &#39;s è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti. È possibile utilizzare questo campo per assegnare un nome all'offerta più utile per identificare l'offerta internamente; non viene visualizzato ai clienti.
    - L'alias dell'offerta può&#39;t deve essere modificato dopo aver selezionato **Crea**.

Dopo aver immesso questi due valori, selezionare **Crea** per passare alla pagina **installazione offerta** .

## <a name="offer-setup"></a>Installazione dell'offerta

Dopo aver immesso un ID offerta e un alias offerta, il centro per i partner crea un'offerta bozza e visualizza la pagina **installazione offerta** . Per configurare l'offerta, seguire questa procedura.

### <a name="connect-lead-management"></a>Gestione dei lead di connessione

Quando si pubblica l'offerta nel Marketplace con il centro per i partner, è _necessario_ connetterla a un sistema di gestione delle relazioni con i clienti (CRM) o di automazione del marketing. In questo modo è possibile ricevere le informazioni di contatto del cliente non appena un utente esprime l'interesse o usa il prodotto.

1. Selezionare **Connetti** per specificare dove si vuole inviare i lead dei clienti. Il centro per i partner supporta i sistemi seguenti:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) per il coinvolgimento dei clienti
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se il sistema CRM è&#39;t elencato sopra, usare la [tabella di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) o l' [endpoint HTTPS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) per archiviare i dati dei clienti potenziali, quindi esportare i dati nel sistema CRM.

2. Connetti l'offerta alla destinazione principale durante la pubblicazione nel centro per i partner.
3. Verificare che la connessione alla destinazione principale sia configurata correttamente. Dopo la pubblicazione nel centro per i partner, è&#39;convalidare la connessione e inviare un lead di test. Quando si visualizza l'anteprima dell'offerta prima che venga attivata, è anche possibile testare la connessione del lead provando ad acquistare l'offerta nell'ambiente di anteprima.
4. Assicurarsi che la connessione alla destinazione lead rimanga aggiornata, in modo da non perdere alcun lead&#39;t.

Di seguito sono riportate alcune risorse di gestione lead aggiuntive:

- [Panoramica della gestione dei lead](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Domande frequenti sulla gestione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Errori comuni di configurazione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Panoramica della gestione dei lead](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (assicurarsi che il blocco popup sia disattivato)

Selezionare **Save Draft** prima di continuare con la sezione successiva, Properties.

### <a name="properties"></a>Proprietà

Questa pagina consente di impostare il prodotto principale offerto dal servizio di consulenza, impostare un tipo di servizio di consulenza e scegliere i prodotti applicabili.

1. Selezionare un **prodotto principale** dall'elenco a discesa.
2. Selezionare un **tipo di servizio di consulenza** dall'elenco a discesa:

    - **Valutazione** : valutazione dell'ambiente di un cliente&#39;s per determinare l'applicabilità di una soluzione e fornire una stima dei costi e delle tempistiche.
    - **Briefing** : Introduzione a una soluzione o a un servizio di consulenza per attrarre gli interessi dei clienti usando Framework, demo ed esempi di clienti.
    - **Implementazione** : un'installazione completa che produce una soluzione completamente funzionante. Limitare le soluzioni che possono essere implementate in due settimane o meno.
    - Modello **di prova** : implementazione con ambito limitato per determinare se una soluzione soddisfa i requisiti del cliente.
    - **Workshop** : un impegno interattivo condotto su un cliente&#39;s locali. Può comportare training, briefing, valutazioni o demo basate sui dati o sull'ambiente del cliente&#39;s.

1. Se è stato selezionato un prodotto primario di **Azure**, selezionare fino a tre **aree della soluzione**. Che semplificano la ricerca dell'offerta da clienti in Azure Marketplace. Se non si è&#39;t scegliere Azure, ignorare questo passaggio.
2. Se è stato selezionato un prodotto primario _diverso_ da Azure, selezionare un massimo di tre **prodotti applicabili**. Per consentire ai clienti di AppSource di trovare l'offerta in modo più semplice. Per informazioni dettagliate, vedere le [linee guida per l'elenco di Microsoft AppSource Consulting Service](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
3. Selezionare fino a sei **settori** a cui si applica l'offerta. In questo modo sarà più semplice per i clienti trovare l'offerta.
4. Aggiungere fino a tre **competenze** che l'azienda ha guadagnato per visualizzare nell'elenco delle offerte di servizi di consulenza. È necessaria almeno una competenza, ad eccezione di Azure Expert MSP&#39;s e di Azure networking MSP&#39;s.

Selezionare **Salva bozza** prima di continuare con la sezione successiva, ad elenco offerte.

## <a name="offer-listing"></a>Elenco offerte

Qui è&#39;definire i dettagli dell'offerta visualizzati nel Marketplace. Sono inclusi il nome dell'offerta, la descrizione, le immagini e così via. Assicurarsi di seguire i criteri descritti in dettaglio nella [pagina dei criteri di Microsoft&#39;](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) durante la configurazione di questa offerta.

> [!NOTE]
> I dettagli dell'offerta aren&#39;t devono essere in inglese se la descrizione dell'offerta inizia con la &quot;frase, l'applicazione è disponibile solo in [lingua non inglese]. &quot;&#39;s è anche possibile fornire un collegamento utile per offrire contenuti in una lingua diversa da quella di&#39;, da quella usata nei dettagli dell'offerta.

### <a name="name"></a>Name

Il nome immesso qui viene visualizzato come titolo dell'offerta. Questo campo è già compilato con il testo immesso nella casella **alias offerta** al momento della creazione dell'offerta. Il nome può essere modificato successivamente.

Nome:

- Può essere registrato (e possono essere inclusi marchi o simboli di copyright).
- Il numero di caratteri con&#39;sentiti non può essere più lungo di 50 caratteri.
- Può&#39;t includere emoji.

### <a name="search-results-summary"></a>Riepilogo risultati ricerca

Fornire una breve descrizione dell'offerta. Questo può avere una lunghezza di 100 caratteri e viene usato nei risultati della ricerca nel Marketplace.

### <a name="description"></a>Descrizione

Fornire una descrizione più lunga dell'offerta, fino a 3.000 caratteri. Viene visualizzato ai clienti nella panoramica dell'elenco del Marketplace.

Includere uno o più dei seguenti elementi nella descrizione:

- Il valore e i vantaggi principali offerti dall'offerta
- Categoria o associazioni di settore o entrambi
- Opportunità di acquisto in-app
- Eventuali divulgazioni richieste

Ecco alcuni suggerimenti per scrivere la descrizione:

- Descrivere chiaramente il valore dell'offerta nelle prime frasi della descrizione. Includere gli elementi seguenti:
  - Descrizione dell'offerta.
  - Tipo di utente che trae vantaggio dall'offerta.
  - Esigenze dei clienti o problemi relativi agli indirizzi dell'offerta.
- Tenere presente che le prime frasi potrebbero essere visualizzate nei risultati della ricerca.
- Don&#39;t si basa sulle funzionalità e sulle funzionalità per la vendita del prodotto. Concentrarsi invece sul valore fornito dall'offerta.
- Provare a usare il vocabolario specifico del settore o il wording basato sui vantaggi.

Per rendere più accattivante la descrizione, usare l'editor di testo RTF per formattare la descrizione. L'editor di testo RTF consente di aggiungere numeri, elenchi puntati, grassetto, corsivo e rientri per rendere la descrizione più leggibile.

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="Viene illustrato l'editor di testo RTF per scrivere la descrizione dell'offerta." border="false":::

### <a name="keywords"></a>Parole chiave

Immettere fino a tre parole chiave di ricerca rilevanti per il servizio di consulenza e il prodotto principale. Che renderà più semplice trovare l'offerta.

### <a name="duration"></a>Duration

Impostare la durata prevista di questo Engagement con il cliente.

### <a name="contact-information"></a>Informazioni sul contatto

È necessario specificare il nome, l'indirizzo di posta elettronica e il numero di telefono per un contatto **primario** e **secondario**. Queste informazioni non sono&#39;visualizzate ai clienti. È disponibile per Microsoft e può essere fornita ai partner Cloud Solution Provider (CSP).

### <a name="supporting-documents"></a>Documenti di supporto

Aggiungere fino a tre (ma almeno uno) per supportare i documenti PDF per l'offerta.

### <a name="marketplace-images"></a>Immagini del Marketplace

Fornire i logo e le immagini da usare con l'offerta. Tutte le immagini devono essere in formato png. Le immagini sfocate verranno rifiutate.

>[!Note]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il https://upload.xboxlive.com servizio usato dal centro per i partner.

#### <a name="store-logos"></a>Logo dello Store

Specificare i file con estensione png del logo dell'offerta&#39;s in ognuna delle dimensioni in pixel seguenti:

- **Piccolo (48 x 48)**
- **Grande (216 x 216)**

Tutti i logo sono obbligatori e vengono usati in posizioni diverse nell'inserzione del Marketplace.

#### <a name="screenshots-optional"></a>Schermate (facoltativo)

Aggiungere fino a cinque schermate che mostrano il funzionamento dell'offerta. Ogni deve avere una dimensione di 1280 x 720 pixel e in formato png.

#### <a name="videos-optional"></a>Video (facoltativo)

Aggiungere fino a quattro video che illustrano l'offerta. Immettere il nome del video&#39;, l'indirizzo Web (URL) e un'immagine thumbnail. png del video con dimensioni di 1280 x 720 pixel.

Selezionare **Salva bozza** prima di continuare con la sezione successiva, prezzi e disponibilità.

## <a name="pricing-and-availability"></a>Prezzi e disponibilità

Qui verranno definiti elementi quali prezzi, mercato e una chiave privata.

1. **Market**: impostare il mercato in cui sarà disponibile l'offerta. È possibile selezionare solo un mercato per ogni offerta.
    1. Per i mercati Stati Uniti o Canada, selezionare **modifica Stati** o **Province**per specificare dove sarà disponibile l'offerta.
2. **Anteprima destinatari**: configurare la **chiave Hide** usata per impostare i destinatari privati per l'offerta.
3. **Prezzi**: specificare se l'offerta è un'offerta **gratuita** o a **pagamento** .

    > [!NOTE]
    > Le offerte del servizio di consulenza sono disponibili solo per l'elenco. Tutte le transazioni vengono eseguite direttamente, all'esterno del Marketplace commerciale.

4. Per un'offerta a pagamento, specificare il **prezzo e la valuta** e se il prezzo è **fisso** o **stimato**. Se viene stimato, è necessario specificare nella descrizione i fattori che influiranno sul prezzo.
5. Selezionare **Salva bozza**.

## <a name="review-and-publish"></a>Esaminare e pubblicare

Una volta completate tutte le sezioni obbligatorie dell'offerta, è possibile inviare l'offerta per la revisione e la pubblicazione.

1. Quando si è pronti per pubblicare l'offerta del servizio di consulenza, fare clic su **Verifica e pubblica**.
2. Esaminare i dettagli nella pagina di invio finale.
3. Se necessario, scrivere una nota del team di certificazione qualora si ritenga che i dettagli dell'offerta siano necessari per la spiegazione.
4. Quando si è pronti, selezionare **Invia**.
5. La pagina di **Panoramica dell'offerta** Mostra la fase di pubblicazione in cui si trova l'offerta.

Per ulteriori informazioni su quanto tempo è possibile prevedere che l'offerta sia in ogni fase di pubblicazione, vedere [controllare lo stato di pubblicazione dell'offerta per il Marketplace commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status).

## <a name="update-your-existing-consulting-service-offers"></a>Aggiornare le offerte del servizio di consulenza esistente

- [Aggiornare un'offerta esistente nel Marketplace commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
