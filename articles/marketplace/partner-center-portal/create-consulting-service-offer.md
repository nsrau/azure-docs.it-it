---
title: Creare un'offerta di un servizio di consulenza - Marketplace commerciale Microsoft
description: Informazioni su come pubblicare un'offerta di un servizio di consulenza in Microsoft AppSource o Azure Marketplace usando il Centro per i partner.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: bd9d643708515855e81ac6729843388a690b2c45
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700942"
---
# <a name="create-a-consulting-service-offer"></a>Creare un'offerta di un servizio di consulenza

Questo articolo illustra come pubblicare un'offerta di un servizio di consulenza in [Microsoft AppSource](https://appsource.microsoft.com/) o [Azure Marketplace](https://azuremarketplace.microsoft.com/). Visualizzare l'elenco delle offerte di servizi di consulenza basati su Microsoft [Dynamics 365](https://dynamics.microsoft.com/) e Power Platform in AppSource. Visualizzare l'elenco delle offerte di servizi basati su Microsoft Azure in Azure Marketplace. Prima di iniziare, [creare un account per il marketplace commerciale nel Centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account), se non è già stato creato. Assicurarsi che l'account sia registrato nel programma del marketplace commerciale.

Prima di creare l'offerta, esaminare i prerequisiti in [Prerequisiti dei servizi di consulenza](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites).

## <a name="publishing-benefits"></a>Vantaggi della pubblicazione

Vantaggi della pubblicazione nel marketplace commerciale:

- Promozione della propria azienda usando il marchio Microsoft.
- Possibilità di raggiungere potenzialmente più di 100 milioni di utenti di Office 365 e Dynamics 365 in AppSource e più di 200.000 organizzazioni tramite Azure Marketplace.
- Acquisizione di lead di alta qualità dai marketplace.
- Promozione dei propri servizi da parte dei team sul campo e di televendita di Microsoft.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di spostamento di sinistra selezionare **Marketplace commerciale** > **Panoramica**.
3. Nella pagina Panoramica selezionare **+ Nuova offerta** > **Servizio di consulenza**.

    ![Immagine del menu di spostamento di sinistra.](./media/new-offer-consulting-service.png)

>[!NOTE]
>Dopo la pubblicazione di un'offerta, le modifiche apportate all'offerta nel Centro per i partner vengono visualizzate nelle vetrine solo dopo la ripubblicazione dell'offerta. Assicurarsi di ripubblicare sempre l'offerta dopo avere apportato modifiche.

## <a name="new-offer"></a>Nuova offerta

Immettere un ID in **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web dell'offerta del marketplace.
- Usare solo lettere minuscole e numeri. È possibile includere trattini e caratteri di sottolineatura, ma non spazi, per un massimo di 50 caratteri. Ad esempio, se si immette **test-offer-1**, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

Immettere un alias in **Alias offerta**. Si tratta del nome usato per l'offerta nel Centro per i partner.

- Questo nome non viene usato nel marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.
- L'alias dell'offerta non può essere modificato dopo aver selezionato **Crea**.

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-setup"></a>Configurazione dell'offerta

### <a name="connect-lead-management"></a>Connettere la gestione dei lead

Quando si pubblica l'offerta nel marketplace con il Centro per i partner, _è necessario_ connetterla a un sistema CRM (Customer Relationship Management) o a un sistema di automazione del marketing. In questo modo è possibile ricevere le informazioni di contatto del cliente non appena un utente esprime interesse o usa il prodotto.

1. Selezionare **Connetti** per specificare dove inviare i lead. Il Centro per i partner supporta i sistemi seguenti:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) for Customer Engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se il sistema CRM non è incluso nell'elenco, usare [Tabella di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) o [Endpoint HTTPS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) per archiviare i dati dei lead, quindi esportarli nel sistema CRM.

2. Quando si pubblica l'offerta nel Centro per i partner, connettere l'offerta alla destinazione del cliente potenziale.
3. Verificare che la connessione alla destinazione del lead sia configurata correttamente. Dopo la pubblicazione nel Centro per i partner, la connessione verrà convalidata e verrà inviato un lead di test. Quando si visualizza l'offerta in anteprima prima della pubblicazione, è anche possibile testare la connessione dei lead tentando di acquistare l'offerta nell'ambiente di anteprima.
4. Assicurarsi che la connessione alla destinazione dei lead rimanga aggiornata in modo da non perdere alcun lead.

Di seguito sono elencate alcune risorse aggiuntive per la gestione dei lead:

- [Panoramica della gestione dei lead](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Domande frequenti sulla gestione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Errori comuni di configurazione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#publishing-config-errors)
- PDF [Panoramica della gestione dei lead](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) (assicurarsi che il blocco dei popup sia disattivato)

Prima di continuare, selezionare **Salva bozza**.

### <a name="properties"></a>Proprietà

Questa pagina consente di impostare il prodotto primario offerto dal servizio di consulenza, impostare un tipo di servizio di consulenza e scegliere i prodotti applicabili.

1. Selezionare un **Prodotto primario** dall'elenco a discesa.
2. Selezionare un **Tipo di servizio di consulenza** dall'elenco a discesa:

    - **Valutazione:** valutazione dell'ambiente di un cliente per determinare l'applicabilità di una soluzione e fornire una stima dei costi e delle tempistiche.
    - **Briefing:** introduzione a una soluzione o a un servizio di consulenza per suscitare l'interesse del cliente con framework, demo ed esempi di clienti.
    - **Implementazione**: installazione completa che fornisce una soluzione perfettamente funzionante. Limitarla alle soluzioni che possono essere implementate in due settimane o meno.
    - **Modello di verifica:** implementazione di ambito limitato per determinare se una soluzione soddisfa i requisiti del cliente.
    - **Workshop:** engagement interattivo condotto presso la sede di un cliente. Può includere training, briefing, valutazioni o demo basate sui dati o sull'ambiente del cliente.

3. Se è stato selezionato un prodotto primario **Azure**, selezionare fino a tre **Aree della soluzione**. Le aree semplificano la ricerca dell'offerta da parte dei clienti in Azure Marketplace. Se non è stato scelto Azure, ignorare questo passaggio.
4. Se è stato selezionato un prodotto primario _non_ Azure, selezionare fino a tre **Prodotti applicabili:** . I prodotti applicabili semplificano la ricerca dell'offerta da parte dei clienti in AppSource. Per altre informazioni, vedere il file PDF delle [linee guida alla presentazione dei servizi di consulenza in Microsoft AppSource](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409).
5. Selezionare fino a sei **Settori** a cui si applica l'offerta. I settori semplificano la ricerca dell'offerta da parte dei clienti.
6. È possibile aggiungere fino a tre **Competenze** acquisite dall'azienda per la visualizzazione nell'elenco delle offerte di servizi di consulenza. È necessaria almeno una competenza, ad eccezione dei provider Azure Expert MSP e degli MSP di rete di Azure.

Prima di continuare, selezionare **Salva bozza**.

## <a name="offer-listing"></a>Presentazione dell'offerta

È possibile definire i dettagli dell'offerta visualizzati nel marketplace. È possibile specificare il nome dell'offerta, la descrizione, le immagini e così via. Durante la configurazione dell'offerta, assicurarsi di seguire i criteri descritti nella [pagina dei criteri di certificazione del marketplace commerciale](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services).

> [!NOTE]
> Non è necessario specificare i dettagli dell'offerta in inglese se la descrizione dell'offerta inizia con la frase &quot;Questa applicazione è disponibile solo in [lingua non inglese].&quot; È anche possibile inserire un collegamento utile ai contenuti dell'offerta in una lingua diversa da quella usata nei dettagli di presentazione dell'offerta.

### <a name="name"></a>Nome

Il nome immesso qui viene visualizzato come titolo dell'offerta. Questo campo viene precompilato con il testo immesso nella casella **Alias offerta** al momento della creazione dell'offerta. È possibile modificarlo in seguito.

Il nome:

- Può essere un marchio registrato (ed è possibile includere i simboli di marchio o copyright).
- Non può avere una lunghezza superiore a 50 caratteri.
- Non può includere emoji.

### <a name="search-results-summary"></a>Riepilogo dei risultati della ricerca

Specificare una breve descrizione dell'offerta. La descrizione può avere una lunghezza massima di 100 caratteri e viene usata nei risultati della ricerca nel marketplace.

### <a name="description"></a>Descrizione

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="keywords"></a>Parole chiave

Immettere fino a tre parole chiave di ricerca rilevanti per il servizio di consulenza e il prodotto primario. In questo modo sarà più semplice trovare l'offerta.

### <a name="duration"></a>Duration

Impostare la durata prevista dell'engagement del cliente.

### <a name="contact-information"></a>Informazioni sul contatto

È necessario specificare il nome, l'indirizzo e-mail e il numero di telefono di un **Contatto principale** e di un **Contatto secondario**. Queste informazioni non vengono visualizzate ai clienti. Sono disponibili per Microsoft e possono essere fornite ai partner CSP (Cloud Solution Provider).

### <a name="supporting-documents"></a>Documenti di supporto

Aggiungere almeno uno fino a un massimo di tre documenti PDF di supporto per l'offerta.

### <a name="marketplace-images"></a>Immagini del Marketplace

Inserire i logo e le immagini da usare per l'offerta. Tutte le immagini devono essere in formato PNG. Le immagini sfuocate verranno rifiutate.

>[!Note]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio https://upload.xboxlive.com usato dal Centro per i partner.

#### <a name="store-logos"></a>Logo dello store

Inserire i file PNG del logo dell'offerta in ognuna delle dimensioni in pixel seguenti:

- **Piccola (48 x 48)**
- **Grande (216 x 216)**

Tutti i logo sono obbligatori e sono usati in posizioni diverse nella presentazione del marketplace.

#### <a name="screenshots-optional"></a>Screenshot (facoltativi)

Aggiungere fino a cinque screenshot che illustrano il funzionamento dell'offerta. Ogni screenshot deve avere dimensioni pari a 1280 x 720 pixel ed essere in formato PNG.

#### <a name="videos-optional"></a>Video (facoltativi)

Aggiungere fino a quattro video che descrivono l'offerta. Immettere il nome del video, l'indirizzo Web (URL) e un'immagine PNG di anteprima del video con dimensioni pari a 1280 x 720 pixel.

Prima di continuare, selezionare **Salva bozza**.

## <a name="pricing-and-availability"></a>Prezzi e disponibilità

Definire elementi come prezzi, mercato e una chiave privata.

1. **Mercato**: impostare il mercato in cui sarà disponibile l'offerta. È possibile selezionare un solo mercato per ogni offerta.
    1. Per i mercati Stati Uniti o Canada, selezionare **Edit states** (or **provinces**) (Modifica stati (o province)) per specificare il luogo in cui sarà disponibile l'offerta.
2. **Visualizza in anteprima gruppo di destinatari**: configurare l'opzione **Nascondi la chiave** usata per impostare i destinatari privati per l'offerta.
3. **Pricing** (Prezzi): specificare se l'offerta è di tipo **Gratuito** o **A pagamento**.

    > [!NOTE]
    > Le offerte di servizi di consulenza sono disponibili solo per la presentazione. Tutte le transazioni vengono eseguite direttamente, all'esterno del marketplace commerciale.

4. Per un'offerta a pagamento, specificare **Prezzo e valuta** e se il prezzo è **Fisso** o **Stimato**. Se si seleziona Stimato, è necessario specificare nella descrizione i fattori che influenzeranno il prezzo.
5. Prima di continuare, selezionare **Salva bozza**.

## <a name="review-and-publish"></a>Rivedere e pubblicare

Dopo aver completato tutte le sezioni obbligatorie dell'offerta, è possibile inviare l'offerta per la revisione e la pubblicazione.

1. Quando si è pronti per pubblicare l'offerta del servizio di consulenza, fare clic su **Rivedi e pubblica**.
2. Esaminare i dettagli nella pagina di invio finale.
3. Se necessario, scrivere una nota per il team di certificazione se si ritiene che alcuni dettagli dell'offerta richiedano una spiegazione.
4. Al termine, selezionare **Invia**.
5. La pagina **Panoramica offerta** mostra la fase di pubblicazione dell'offerta.

Per altre informazioni sulla tempistica di ogni fase di pubblicazione dell'offerta, vedere [Verificare lo stato di pubblicazione dell'offerta del marketplace commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status).

## <a name="update-your-existing-consulting-service-offers"></a>Aggiornare le offerte dei servizi di consulenza esistenti

- [Aggiornare un'offerta esistente nel marketplace commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
