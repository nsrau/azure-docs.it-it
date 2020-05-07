---
title: Creare un'offerta di servizio di consulenza-Microsoft Commercial Marketplace
description: Informazioni su come pubblicare un'offerta del servizio di consulenza in Microsoft AppSource o Azure Marketplace tramite il centro per i partner.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 7e85175cbee53bd5984a38a587a90b98eaf8dcad
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791902"
---
# <a name="create-a-consulting-service-offer"></a>Creare un'offerta di un servizio di consulenza

> [!IMPORTANT]
> Stiamo muovendo la gestione delle offerte del servizio di consulenza dal portale Cloud Partner al centro per i partner. Fino a quando non viene eseguita la migrazione delle offerte, segui le istruzioni riportate nell' [offerta del servizio di consulenza Azure e Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer) per portale cloud partner per gestire le tue offerte.

Questo articolo descrive come pubblicare un'offerta del servizio di consulenza in [Microsoft AppSource](https://appsource.microsoft.com/) o in [Azure Marketplace](https://azuremarketplace.microsoft.com/). Elencare le offerte del servizio di consulenza basate su Microsoft [Dynamics 365](https://dynamics.microsoft.com/) e Power Platform in AppSource. Elenca le offerte del servizio di consulenza basate su Microsoft Azure in Azure Marketplace. Prima di iniziare, [creare un account Marketplace commerciale nel centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , se non è ancora stato fatto. Assicurarsi che l'account sia registrato nel programma Commercial Marketplace.

Prima di creare l'offerta, esaminare i prerequisiti in consulto dei [prerequisiti del servizio](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites).

## <a name="publishing-benefits"></a>Vantaggi della pubblicazione

Vantaggi della pubblicazione nel Marketplace commerciale:

- Promuovere la propria azienda usando il marchio Microsoft.
- È potenzialmente raggiungibile da più di 100 milioni utenti di Office 365 e Dynamics 365 in AppSource e più di 200.000 organizzazioni tramite Azure Marketplace.
- Ricevi lead di alta qualità da questi Marketplace.
- Ottenere i servizi promossi dai team Microsoft Field e Telesales

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al centro per i [partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di navigazione a sinistra selezionare **Commercial Marketplace** > **Overview**.
3. Nella pagina Panoramica selezionare **+ nuovo offerta** > **servizio di consulenza**.

    ![Viene illustrato il menu di spostamento a sinistra.](./media/new-offer-consulting-service.png)

>[!NOTE]
>Dopo la pubblicazione di un'offerta, le modifiche apportate al centro per i partner vengono visualizzate solo nelle vetrine dopo la ripubblicazione dell'offerta. Assicurarsi di ripubblicare sempre dopo avere apportato modifiche.

## <a name="new-offer"></a>Nuova offerta

Immettere un **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web dell'offerta del Marketplace.
- Usare solo lettere minuscole e numeri. Può includere trattini e caratteri di sottolineatura, ma senza spazi e con un limite di 50 caratteri. Se ad esempio si immette **test-offer-1**, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

Immettere un **alias dell'offerta**. Questo è il nome usato per l'offerta nel centro per i partner.

- Questo nome non viene usato nel Marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati per i clienti.
- Non è possibile modificare l'alias dell'offerta dopo aver selezionato **Crea**.

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-setup"></a>Installazione dell'offerta

### <a name="connect-lead-management"></a>Gestione dei lead di connessione

Quando si pubblica l'offerta nel Marketplace con il centro per i partner, è _necessario_ connetterla a un sistema di gestione delle relazioni con i clienti (CRM) o di automazione del marketing. In questo modo è possibile ricevere le informazioni di contatto del cliente non appena un utente esprime l'interesse o usa il prodotto.

1. Selezionare **Connetti** per specificare dove si vuole inviare i lead dei clienti. Il centro per i partner supporta i sistemi seguenti:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) per il coinvolgimento dei clienti
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se il sistema CRM non è elencato sopra, usare la [tabella di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) o l' [endpoint HTTPS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) per archiviare i dati dei clienti potenziali, quindi esportare i dati nel sistema CRM.

2. Connetti l'offerta alla destinazione principale durante la pubblicazione nel centro per i partner.
3. Verificare che la connessione alla destinazione principale sia configurata correttamente. Dopo la pubblicazione nel centro per i partner, la connessione verrà convalidata e verrà inviato un lead di test. Quando si visualizza l'anteprima dell'offerta prima che venga attivata, è anche possibile testare la connessione del lead provando ad acquistare l'offerta nell'ambiente di anteprima.
4. Assicurarsi che la connessione alla destinazione principale rimanga aggiornata in modo da non perdere i lead.

Di seguito sono riportate alcune risorse di gestione lead aggiuntive:

- [Panoramica della gestione dei lead](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Domande frequenti sulla gestione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Errori comuni di configurazione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#publishing-config-errors)
- [Panoramica della gestione dei lead](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (assicurarsi che il blocco popup sia disattivato)

Selezionare **Salva bozza** prima di continuare.

### <a name="properties"></a>Proprietà

Questa pagina consente di impostare il prodotto principale offerto dal servizio di consulenza, impostare un tipo di servizio di consulenza e scegliere i prodotti applicabili.

1. Selezionare un **prodotto principale** dall'elenco a discesa.
2. Selezionare un **tipo di servizio di consulenza** dall'elenco a discesa:

    - **Valutazione**: valutazione dell'ambiente di un cliente per determinare l'applicabilità di una soluzione e fornire una stima dei costi e delle tempistiche.
    - **Briefing**: Introduzione a una soluzione o a un servizio di consulenza per attrarre gli interessi dei clienti usando Framework, demo ed esempi di clienti.
    - **Implementazione**: un'installazione completa che produce una soluzione completamente funzionante. Limitare le soluzioni che possono essere implementate in due settimane o meno.
    - **Modello di verifica:** implementazione di ambito limitato per determinare se una soluzione soddisfa i requisiti del cliente.
    - **Workshop**: impegno interattivo condotto sulle sedi dei clienti. Può comportare training, briefing, valutazioni o demo basate sui dati o sull'ambiente del cliente.

1. Se è stato selezionato un prodotto primario di **Azure**, selezionare fino a tre **aree della soluzione**. Che semplificano la ricerca dell'offerta da clienti in Azure Marketplace. Se non è stato scelto Azure, ignorare questo passaggio.
2. Se è stato selezionato un prodotto primario _diverso_ da Azure, selezionare un massimo di tre **prodotti applicabili**. Per consentire ai clienti di AppSource di trovare l'offerta in modo più semplice. Per informazioni dettagliate, vedere le [linee guida per l'elenco di Microsoft AppSource Consulting Service](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
3. Selezionare fino a sei **settori** a cui si applica l'offerta. In questo modo sarà più semplice per i clienti trovare l'offerta.
4. Aggiungere fino a tre **competenze** che l'azienda ha guadagnato per visualizzare nell'elenco delle offerte di servizi di consulenza. Almeno una competenza è obbligatoria ad eccezione di MSPs di Azure e di rete MSPs di Azure.

Selezionare **Salva bozza** prima di continuare.

## <a name="offer-listing"></a>Elenco offerte

Qui verranno definiti i dettagli dell'offerta visualizzati nel Marketplace. Sono inclusi il nome dell'offerta, la descrizione, le immagini e così via. Assicurarsi di seguire i criteri descritti in dettaglio nella [pagina Criteri di certificazione del Marketplace commerciale](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) durante la configurazione di questa offerta.

> [!NOTE]
> I dettagli dell'offerta non devono essere in inglese se la descrizione dell'offerta inizia con la &quot;frase, l'applicazione è disponibile solo in [lingua non inglese]. &quot; È anche possibile fornire un collegamento utile per offrire contenuti in una lingua diversa da quella usata nell'elenco dei dettagli dell'offerta.

### <a name="name"></a>Nome

Il nome immesso qui viene visualizzato come titolo dell'offerta. Questo campo è già compilato con il testo immesso nella casella **alias offerta** al momento della creazione dell'offerta. Il nome può essere modificato successivamente.

Nome:

- Può essere registrato (e possono essere inclusi marchi o simboli di copyright).
- Non può contenere più di 50 caratteri.
- Non può includere emoji.

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
- Non fare affidamento sulle funzionalità e sulle funzionalità per la vendita del prodotto. Concentrarsi invece sul valore fornito dall'offerta.
- Provare a usare il vocabolario specifico del settore o il wording basato sui vantaggi.

Per rendere più accattivante la descrizione dell'offerta, usare l'editor di testo RTF per applicare la formattazione.

![Uso dell'editor di testo RTF](./media/rich-text-editor.png)

| <center>Modificare il formato del testo | <center>Aggiungi elenchi puntati o numerati | <center>Aggiungere o rimuovere il rientro del testo |
| --- | --- | --- |
| <center>![Utilizzo dell'editor di testo RTF per modificare il formato del testo](./media/text-editor3.png) |  <center>![Uso dell'editor di testo RTF per aggiungere elenchi](./media/text-editor4.png) |  <center>![Utilizzo dell'editor di testo RTF per rientrare](./media/text-editor5.png) |

### <a name="keywords"></a>Parole chiave

Immettere fino a tre parole chiave di ricerca rilevanti per il servizio di consulenza e il prodotto principale. Che renderà più semplice trovare l'offerta.

### <a name="duration"></a>Duration

Impostare la durata prevista di questo Engagement con il cliente.

### <a name="contact-information"></a>Informazioni sul contatto

È necessario specificare il nome, l'indirizzo di posta elettronica e il numero di telefono per un contatto **primario** e **secondario**. Queste informazioni non vengono visualizzate ai clienti. È disponibile per Microsoft e può essere fornita ai partner Cloud Solution Provider (CSP).

### <a name="supporting-documents"></a>Documenti di supporto

Aggiungere fino a tre (ma almeno uno) per supportare i documenti PDF per l'offerta.

### <a name="marketplace-images"></a>Immagini del Marketplace

Fornire i logo e le immagini da usare con l'offerta. Tutte le immagini devono essere in formato png. Le immagini sfocate verranno rifiutate.

>[!Note]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il https://upload.xboxlive.com servizio usato dal centro per i partner.

#### <a name="store-logos"></a>Logo dello Store

Fornire i file PNG del logo dell'offerta in ognuna delle dimensioni dei pixel seguenti:

- **Piccolo (48 x 48)**
- **Grande (216 x 216)**

Tutti i logo sono obbligatori e vengono usati in posizioni diverse nell'inserzione del Marketplace.

#### <a name="screenshots-optional"></a>Schermate (facoltativo)

Aggiungere fino a cinque schermate che mostrano il funzionamento dell'offerta. Ogni deve avere una dimensione di 1280 x 720 pixel e in formato PNG.

#### <a name="videos-optional"></a>Video (facoltativo)

Aggiungere fino a quattro video che illustrano l'offerta. Immettere il nome del video, l'indirizzo Web (URL) e un'immagine PNG di anteprima del video con dimensioni di 1280 x 720 pixel.

Selezionare **Salva bozza** prima di continuare.

## <a name="pricing-and-availability"></a>Prezzi e disponibilità

Qui verranno definiti elementi quali prezzi, mercato e una chiave privata.

1. **Market**: impostare il mercato in cui sarà disponibile l'offerta. È possibile selezionare solo un mercato per ogni offerta.
    1. Per i mercati Stati Uniti o Canada, selezionare **modifica Stati** o **Province**per specificare dove sarà disponibile l'offerta.
2. **Anteprima destinatari**: configurare la **chiave Hide** usata per impostare i destinatari privati per l'offerta.
3. **Prezzi**: specificare se l'offerta è un'offerta **gratuita** o a **pagamento** .

    > [!NOTE]
    > Le offerte del servizio di consulenza sono disponibili solo per l'elenco. Tutte le transazioni vengono eseguite direttamente, all'esterno del Marketplace commerciale.

4. Per un'offerta a pagamento, specificare il **prezzo e la valuta** e se il prezzo è **fisso** o **stimato**. Se viene stimato, è necessario specificare nella descrizione i fattori che influiranno sul prezzo.
5. Selezionare **Salva bozza** prima di continuare.

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
