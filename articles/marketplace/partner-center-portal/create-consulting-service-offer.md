---
title: Creare un'offerta di servizio di consulenza nel Centro per i partner - Azure MarketplaceCreate a Consulting service offer in Partner Center - Azure Marketplace
description: Informazioni su come pubblicare un'offerta di servizio Consulting in Azure Marketplace o AppSource tramite il Centro per i partner.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: eff37750f0580a28c9644ee1ffb7fe4e95038709
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869788"
---
# <a name="create-a-consulting-service-offer"></a>Crea un'offerta di servizio di consulenza

> [!IMPORTANT]
> Stiamo spostando la gestione delle offerte di servizi di consulenza dal portale per i partner cloud al Centro per i partner. Fino alla migrazione delle offerte, seguire le istruzioni nell'offerta di servizio di [consulenza di Azure e Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer) per il portale Cloud Partner per gestire le offerte.

In questo articolo viene descritto come pubblicare un'offerta del servizio Consulting in [Azure Marketplace](https://azuremarketplace.microsoft.com/) o [AppSource.](https://appsource.microsoft.com/) Elencare le offerte del servizio di consulenza basate su Microsoft [Dynamics 365](https://dynamics.microsoft.com/) e Power Platform in AppSource. Elencare le offerte del servizio di consulenza basate su Microsoft Azure in Azure Marketplace.

Per creare un'offerta di servizio di consulenza nei servizi Azure Marketplace o AppSource Consulting, è necessario innanzitutto disporre di [un account editore nel Centro per](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)i partner e l'account deve essere registrato nel programma del marketplace commerciale. Prima di creare l'offerta, esaminare i prerequisiti in Prerequisiti del [servizio di consulenza](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites).

## <a name="publishing-benefits"></a>Vantaggi della pubblicazione

Vantaggi della pubblicazione sul mercato commerciale:

- Promuovi la tua azienda utilizzando il marchio Microsoft.
- Raggiungi potenzialmente più di 100 milioni di utenti di Office 365 e Dynamics 365 su AppSource e più di 200.000 organizzazioni tramite Azure Marketplace.
- Ricevi lead di alta qualità da questi mercati.
- Fai in modo che i tuoi servizi vengano promossi dai team di settore e di televendita Microsoft

## <a name="create-a-new-offer"></a>Creare una nuova offerta

Dopo aver rispettato i requisiti descritti in precedenza, attenersi alla seguente procedura per creare un'offerta di servizio di consulenza.

1. Accedi al [Centro per i partner](https://partner.microsoft.com), quindi seleziona **Dashboard** dal menu in alto.
2. Nella barra di spostamento sinistra selezionare **Mercato commerciale**, quindi **Panoramica**.

    :::image type="content" source="media/cs-menu-overview.png" alt-text="Illustra il menu per il mercato commerciale":::

3. Selezionare **Nuova offerta**, quindi Servizio **di consulenza**.

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="Illustra il pulsante per creare una nuova offerta.":::

4. Immettere un **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nel tuo account.

    - Questo ID è visibile ai clienti nell'indirizzo Web dell'offerta del marketplace.
    - Utilizzare solo lettere minuscole, numeri, trattini e caratteri di sottolineatura, ma senza spazi. La lunghezza è limitata a 50 caratteri. Ad esempio, se si immette **test-offer-1** `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`, l'URL dell'offerta sarà .
    - L'ID offerta può&#39;t non essere modificato dopo aver selezionato **Crea**.

5. Immettere un **alias di offerta**. Questo è il nome usato per fare riferimento all'offerta nel Centro per i partner.

    - Questo nome non viene&#39;utilizzato nel marketplace. È&#39;è diverso dal nome dell'offerta e da altri valori che vengono mostrati ai clienti. È possibile utilizzare questo campo per assegnare all'offerta un nome più utile per identificare l'offerta internamente; non viene mostrato ai clienti.
    - L'alias dell'offerta può&#39;t dopo aver selezionato **Crea**.

Dopo aver immesso questi due valori, selezionare **Crea** per continuare con la pagina **Impostazione offerta.**

## <a name="offer-setup"></a>Configurazione dell'offerta

Dopo aver immesso un ID offerta e un alias offerta, il Centro per i partner crea una bozza di offerta e visualizza la pagina **Impostazione offerta.** Segui questi passaggi per configurare la tua offerta.

### <a name="connect-lead-management"></a>Gestione dei lead di Connect

Quando pubblichi l'offerta nel marketplace con il Centro per i partner, _devi_ connetterla a un sistema CRM (Customer Relationship Management) o di automazione del marketing. Ciò consente di ricevere le informazioni di contatto del cliente non appena qualcuno esprime interesse o utilizza il prodotto.

1. Selezionare **Connetti** per specificare dove inviare i lead dei clienti. Il Centro per i partner supporta i sistemi seguenti:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) per coinvolgimento dei clienti
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se il sistema CRM non&#39;è elencato in precedenza, usare [Tabella di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) o Endpoint [https](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) per archiviare i dati dei lead dei clienti, quindi esportare i dati nel sistema CRM.

2. Collegare l'offerta alla destinazione lead durante la pubblicazione nel Centro per i partner.
3. Verificare che la connessione alla destinazione del lead sia configurata correttamente. Dopo averla pubblicata nel Centro per i partner,&#39;convalideremo la connessione e ti invieremo un responsabile di test. Durante l'anteprima dell'offerta prima che venga messa in diretta, puoi anche testare la connessione lead provando ad acquistare l'offerta nell'ambiente di anteprima.
4. Assicurarsi che la connessione alla destinazione lead rimanga aggiornata in modo da non&#39;perdere alcun lead.

Di seguito sono riportate alcune risorse aggiuntive per la gestione dei lead:Here are some additional lead management resources:

- [Panoramica della gestione dei lead](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Domande frequenti sulla gestione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Errori comuni di configurazione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Panoramica della gestione dei lead](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Assicurarsi che il blocco popup sia disattivato)

Selezionare **Salva bozza** prima di continuare con la sezione successiva, Proprietà.

### <a name="properties"></a>Proprietà

Questa pagina consente di impostare il prodotto principale che il servizio di consulenza offre le migliori copertine, impostare un tipo di servizio di consulenza e scegliere i prodotti applicabili.

1. Selezionare un **prodotto primario** dall'elenco a discesa.
2. Selezionare un **tipo di servizio di consulenza** dall'elenco a discesa:

    - **Valutazione** : Una valutazione di un ambiente di&#39;cliente per determinare l'applicabilità di una soluzione e fornire una stima dei costi e dei tempi.
    - **Briefing** : Introduzione a una soluzione o a un servizio di consulenza per attirare l'interesse dei clienti utilizzando framework, demo ed esempi di clienti.
    - **Implementazione** : Un'installazione completa che si traduce in una soluzione completamente funzionante. Limite alle soluzioni che possono essere implementate in due settimane o meno.
    - **Proof of concept** : Un'implementazione con ambito limitato per determinare se una soluzione soddisfa i requisiti del cliente.
    - **Workshop** : Un coinvolgimento interattivo condotto sui&#39;clienti. Può coinvolgere la formazione, briefing, valutazioni o demo costruite sui dati o sull'ambiente del&#39;del cliente.

1. Se è stato selezionato un prodotto principale di **Azure,** selezionare fino a tre **aree di soluzione**. Questi semplificano l'individuazione dell'offerta da parte dei clienti in Azure Marketplace. Se non si è&#39;scegliere Azure, ignorare questo passaggio.
2. Se è stato selezionato un prodotto principale _diverso_ da Azure, selezionare fino a tre **prodotti applicabili.** Questi rendono più facile per i clienti in AppSource trovare la tua offerta. Per informazioni dettagliate, vedere Linee guida per l'elenco dei servizi Microsoft [AppSource Consulting](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
3. Seleziona fino a sei **Settori** a cui si applica la tua offerta. Questo renderà più facile per i clienti trovare la tua offerta.
4. Aggiungi fino a tre **Competenze** che la tua azienda ha guadagnato per essere visualizzata nella tua scheda dell'offerta di servizi di consulenza. È necessaria almeno una competenza, ad eccezione di Azure Expert MSP&#39;s e Azure Networking MSP&#39;s.

Seleziona **Salva bozze** prima di continuare con la sezione successiva, Offerta.

## <a name="offer-listing"></a>Inserzione dell'offerta

Qui è&#39;definire i dettagli dell'offerta visualizzati nel marketplace. Sono inclusi il nome, la descrizione, le immagini e così via. Assicurarsi di seguire i criteri descritti in [Microsoft&#39;s pagina dei criteri](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) durante la configurazione di questa offerta.

> [!NOTE]
> I dettagli dell'offerta sono&#39;non è richiesto di &quot;essere in inglese se la descrizione dell'offerta inizia con la frase, Questa applicazione è disponibile solo in [lingua non in lingua inglese]. &quot; E '&#39;va bene anche fornire un Link utile per offrire contenuti in una lingua che&#39;s diverso da quello utilizzato nei dettagli dell'inserzione Offerta.

### <a name="name"></a>Nome

Il nome che inserisci qui viene visualizzato come titolo dell'offerta. Questo campo è precompilato con il testo immesso nella casella **Alias offerta** al momento della creazione dell'offerta. Il nome può essere modificato successivamente.

Il nome:

- Può essere registrato (e l'utente può includere simboli di marchio o copyright).
- &#39;può avere una lunghezza superiore a 50 caratteri.
- Può&#39;t includere emoji.

### <a name="search-results-summary"></a>Riepilogo dei risultati della ricerca

Fornisci una breve descrizione della tua offerta. Può essere lungo fino a 100 caratteri e viene utilizzato nei risultati di ricerca del marketplace.

### <a name="description"></a>Descrizione

Fornisci una descrizione più lunga della tua offerta, fino a 3.000 caratteri. Viene visualizzato dai clienti nella panoramica dell'elenco dei marketplace.

Includere una o più delle seguenti opzioni nella descrizione:

- Il valore e i vantaggi principali che la tua offerta offre
- Associazioni di categoria o di settore, o entrambe
- Opportunità di acquisto in-app
- Eventuali divulgazioni obbligatorie

Ecco alcuni suggerimenti per scrivere la tua descrizione:

- Descrivi chiaramente il valore della tua offerta nelle prime frasi della tua descrizione. Includere i seguenti elementi:
  - Descrizione dell'offerta.
  - Il tipo di utente che beneficia dell'offerta.
  - Il cliente ha bisogno o emette gli indirizzi dell'offerta.
- Ricorda che le prime frasi potrebbero essere visualizzate nei risultati della ricerca.
- Non&#39;fare affidamento su caratteristiche e funzionalità per vendere il tuo prodotto. Concentrati invece sul valore offerto dalla tua offerta.
- Prova a usare il vocabolario specifico del settore o la formulazione basata sui vantaggi.

Per rendere la descrizione più coinvolgente, usa l'editor RTF per formattare la descrizione. L'editor RTF consente di aggiungere numeri, punti elenco, grassetto, corsivo e rientri per rendere la descrizione più leggibile.

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="Illustra l'editor RTF per scrivere la descrizione dell'offerta." border="false":::

### <a name="keywords"></a>Parole chiave

Immettere fino a tre parole chiave di ricerca rilevanti per il prodotto principale e il servizio di consulenza. Questi renderanno più facile trovare la tua offerta.

### <a name="duration"></a>Durata

Impostare la durata prevista di questo impegno con il cliente.

### <a name="contact-information"></a>Informazioni sul contatto

È necessario fornire il nome, l'indirizzo di posta elettronica e il numero di telefono per un contatto **primario** e **secondario.** Queste informazioni non vengono&#39;mostrate ai clienti. È disponibile per Microsoft e può essere fornito ai partner Cloud Solution Provider (CSP).

### <a name="supporting-documents"></a>Documenti di supporto

Aggiungi fino a tre (ma almeno uno) documenti PDF a supporto per la tua offerta.

### <a name="marketplace-images"></a>Immagini del Marketplace

Fornisci loghi e immagini da utilizzare con la tua offerta. Tutte le immagini devono essere in formato PNG. Le immagini sfocate verranno rifiutate.

>[!Note]
>Se si verifica un problema durante il caricamento dei https://upload.xboxlive.com file, assicurarsi che la rete locale non blocchi il servizio utilizzato dal Centro per i partner.

#### <a name="store-logos"></a>Loghi del Negozio

Fornisci i file .png dell'offerta&#39;il logo s in ognuna delle seguenti dimensioni in pixel:

- **Piccolo (48 x 48)**
- **Grande (216 x 216)**

Tutti i loghi sono obbligatori e vengono utilizzati in luoghi diversi nella presentazione del marketplace.

#### <a name="screenshots-optional"></a>Screenshot (facoltativo)

Aggiungi fino a cinque screenshot che mostrano come funziona la tua offerta. Ciascuno deve avere dimensioni 1280 x 720 pixel e in formato PNG.

#### <a name="videos-optional"></a>Video (opzionale)

Aggiungi fino a quattro video che dimostrano la tua offerta. Inserisci il nome del&#39;video, il suo indirizzo web (URL) e un'immagine .png in miniatura del video con dimensioni di 1280 x 720 pixel.

Seleziona **Salva bozza** prima di continuare con la sezione successiva, Prezzi e disponibilità.

## <a name="pricing-and-availability"></a>Prezzi e disponibilità

Qui definirai elementi come prezzi, mercato e una chiave privata.

1. **Mercato**: Imposta il mercato in cui la tua offerta sarà disponibile. È possibile selezionare un solo mercato per offerta.
    1. Per i mercati degli Stati Uniti o del Canada, selezionare **Modifica stati** (o **province)** per specificare dove sarà disponibile l'offerta.
2. **Anteprima pubblico**: Configurare la **chiave Nascondi** utilizzata per impostare il pubblico privato per l'offerta.
3. **Prezzi**: Specificare se l'offerta è **gratuita** o **a pagamento.**

    > [!NOTE]
    > Le offerte di servizi di consulenza sono solo per l'inserzione. Tutte le transazioni avverranno direttamente, al di fuori del mercato commerciale.

4. Per un'offerta a pagamento, specificare il **prezzo e** la valuta e se il prezzo è **Fisso** o **Stimato**. Se Stimato, è necessario specificare nella descrizione quali fattori influenzeranno il prezzo.
5. Selezionare **Salva bozza**.

## <a name="review-and-publish"></a>Revisione e pubblicazione

Dopo aver completato tutte le sezioni obbligatorie dell'offerta, puoi inviare l'offerta per la revisione e la pubblicazione.

1. Quando si è pronti a pubblicare l'offerta di servizio Consulting, fare clic su **Rivedi e pubblica**.
2. Rivedere i dettagli nella pagina di invio finale.
3. Se necessario, scrivi una nota al team di certificazione se ritieni che uno dei dettagli della tua offerta richieda una spiegazione.
4. Quando si è pronti, selezionare **Invia**.
5. La pagina **Panoramica dell'offerta** mostra in quale fase di pubblicazione si trova l'offerta.

Per ulteriori informazioni su quanto tempo ci si può aspettare l'offerta in ogni fase di pubblicazione, vedere [Controllare lo stato di pubblicazione dell'offerta Marketplace commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status).

## <a name="update-your-existing-consulting-service-offers"></a>Aggiornare le offerte di servizi di consulenza esistenti

- [Aggiornare un'offerta esistente nel mercato commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
