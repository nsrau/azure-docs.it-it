---
title: Creare un'offerta di un servizio di consulenza - Marketplace commerciale Microsoft
description: Informazioni su come pubblicare un'offerta di un servizio di consulenza in Microsoft AppSource o Azure Marketplace usando il Centro per i partner.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 06/17/2020
ms.openlocfilehash: 464e75e55bc67ce619134be01ba00f2606a271a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91709071"
---
# <a name="create-a-consulting-service-offer"></a>Creare un'offerta di un servizio di consulenza

Questo articolo illustra come pubblicare un'offerta di un servizio di consulenza in [Microsoft AppSource](https://appsource.microsoft.com/) o [Azure Marketplace](https://azuremarketplace.microsoft.com/). Visualizzare l'elenco delle offerte di servizi di consulenza basati su Microsoft [Dynamics 365](https://dynamics.microsoft.com/) e Power Platform in AppSource. Visualizzare l'elenco delle offerte di servizi basati su Microsoft Azure in Azure Marketplace. Prima di iniziare, [creare un account per il marketplace commerciale nel Centro per i partner](create-account.md), se non è già stato creato. Assicurarsi che l'account sia registrato nel programma del marketplace commerciale.

Prima di creare l'offerta, esaminare i prerequisiti in [Prerequisiti dei servizi di consulenza](consulting-service-prerequisites.md).

## <a name="publishing-benefits"></a>Vantaggi della pubblicazione

Vantaggi della pubblicazione nel marketplace commerciale:

- Promozione della propria azienda usando il marchio Microsoft.
- È potenzialmente raggiungibile da più di 100 milioni Microsoft 365 e dagli utenti di Dynamics 365 in AppSource e da più di 200.000 organizzazioni tramite Azure Marketplace.
- Acquisizione di lead di alta qualità dai marketplace.
- Promozione dei propri servizi da parte dei team sul campo e di televendita di Microsoft.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di spostamento di sinistra selezionare **Marketplace commerciale** > **Panoramica**.
3. Nella pagina Panoramica selezionare **+ Nuova offerta** > **Servizio di consulenza**.

    ![Immagine del menu di spostamento di sinistra.](./media/new-offer-consulting-service.png)

>[!NOTE]
>Dopo la pubblicazione di un'offerta, le modifiche apportate al centro per i partner vengono visualizzate solo negli archivi online dopo la ripubblicazione dell'offerta. Assicurarsi di ripubblicare sempre l'offerta dopo avere apportato modifiche.

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

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Se il sistema CRM non è incluso nell'elenco, usare [Tabella di Azure](commercial-marketplace-lead-management-instructions-azure-table.md) o [Endpoint HTTPS](commercial-marketplace-lead-management-instructions-https.md) per archiviare i dati dei lead, quindi esportarli nel sistema CRM.

2. Quando si pubblica l'offerta nel Centro per i partner, connettere l'offerta alla destinazione del cliente potenziale.
3. Verificare che la connessione alla destinazione del lead sia configurata correttamente. Dopo la pubblicazione nel Centro per i partner, la connessione verrà convalidata e verrà inviato un lead di test. Quando si visualizza l'offerta in anteprima prima della pubblicazione, è anche possibile testare la connessione dei lead tentando di acquistare l'offerta nell'ambiente di anteprima.
4. Assicurarsi che la connessione alla destinazione dei lead rimanga aggiornata in modo da non perdere alcun lead.

Di seguito sono elencate alcune risorse aggiuntive per la gestione dei lead:

- [Lead di clienti tramite l'offerta del marketplace commerciale](commercial-marketplace-get-customer-leads.md)
- [Domande frequenti sulla gestione dei lead](../lead-management-faq.md#common-questions-about-lead-management)
- [Risoluzione degli errori di configurazione del lead](../lead-management-faq.md#publishing-config-errors)
- PDF [Panoramica della gestione dei clienti potenziali](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) (assicurarsi che il blocco dei popup sia disattivato)

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

3. Se è stato selezionato **Azure** come prodotto primario, selezionare fino a tre **aree della soluzione**. Le aree semplificano la ricerca dell'offerta da parte dei clienti in Azure Marketplace. Se non è stato scelto Azure, ignorare questo passaggio.

    - Analisi
    - Modernizzazione delle app
    - Archiviazione
    - Intelligenza artificiale e Machine Learning
    - Backup
    - Big Data
    - Piattaforma dati
    - Gestione dei Data Center
    - DevOps
    - Ripristino di emergenza
    - Identità
    - Internet delle cose
    - Migrazione
    - Rete
    - Sicurezza
    - Archiviazione

1. Se è stato selezionato **Azure** come prodotto principale, è possibile selezionare fino a sei **settori**. Le aree semplificano la ricerca dell'offerta da parte dei clienti in Azure Marketplace. Vedere l'elenco completo dei settori delle [procedure consigliate](../gtm-offer-listing-best-practices.md)per l'elenco delle offerte. Se non è stato scelto Azure, ignorare questo passaggio.
1. Se è stato selezionato un prodotto primario *non* Azure, selezionare fino a tre **Prodotti applicabili:** . I prodotti applicabili semplificano la ricerca dell'offerta da parte dei clienti in AppSource. Per altre informazioni, vedere il file PDF delle [linee guida alla presentazione dei servizi di consulenza in Microsoft AppSource](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409).
1. Se è stato selezionato un prodotto primario *diverso* da Azure, è possibile selezionare un massimo di due **settori** e due **verticali** per ogni settore. I prodotti applicabili semplificano la ricerca dell'offerta da parte dei clienti in AppSource. Vedere l'elenco completo delle [procedure consigliate](../gtm-offer-listing-best-practices.md)per l'elenco di settori e verticali.
1. È possibile aggiungere fino a tre **Competenze** acquisite dall'azienda per la visualizzazione nell'elenco delle offerte di servizi di consulenza. È necessaria almeno una competenza, ad eccezione dei provider Azure Expert MSP e degli MSP di rete di Azure.

Prima di continuare, selezionare **Salva bozza**.

## <a name="offer-listing"></a>Presentazione dell'offerta

È possibile definire i dettagli dell'offerta visualizzati nel marketplace. È possibile specificare il nome dell'offerta, la descrizione, le immagini e così via. Durante la configurazione dell'offerta, assicurarsi di seguire i criteri descritti nella [pagina dei criteri di certificazione del marketplace commerciale](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services).

> [!NOTE]
> Non è necessario specificare i dettagli dell'offerta in inglese se la descrizione dell'offerta inizia con la frase &quot;Questa applicazione è disponibile solo in [lingua non inglese].&quot; È anche possibile inserire un collegamento utile ai contenuti dell'offerta in una lingua diversa da quella usata nei dettagli di presentazione dell'offerta.

Di seguito è riportato un esempio di come vengono visualizzate le informazioni sull'offerta in Azure Marketplace (i prezzi elencati sono solo a scopo esemplificativo e non sono destinati a riflettere i costi effettivi):

:::image type="content" source="media/example-consulting-service-offer.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Logo
2. Prezzo
3. Aree della soluzione
4. Settori
5. Nome offerta
6. Riepilogo
7. Description
8. Screenshot/video

<br>Di seguito è riportato un esempio di come vengono visualizzate le informazioni sull'offerta in Microsoft AppSource (i prezzi elencati sono solo a scopo esemplificativo e non sono destinati a riflettere i costi effettivi):

:::image type="content" source="media/example-consulting-service-offer-appsource.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Logo
2. Prezzo
3. Prodotti
4. Settori
5. Nome offerta
6. Riepilogo
7. Description
8. Screenshot/video
9. Documenti

### <a name="name"></a>Nome

Il nome immesso qui viene visualizzato come titolo dell'offerta. Questo campo viene precompilato con il testo immesso nella casella **Alias offerta** al momento della creazione dell'offerta. È possibile modificarlo in seguito.

Il nome:

- Può essere un marchio registrato (ed è possibile includere i simboli di marchio o copyright).
- Non può avere una lunghezza superiore a 50 caratteri.
- Non può includere emoji.

### <a name="search-results-summary"></a>Riepilogo dei risultati della ricerca

Specificare una breve descrizione dell'offerta. Questa può essere lunga 100 caratteri e viene usata nei risultati della ricerca nel marketplace.

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

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio https://upload.xboxlive.com usato dal Centro per i partner.

#### <a name="store-logos"></a>Logo dello store

Fornire un file PNG per il logo di **grandi** dimensioni. Il centro per i partner utilizzerà questo per creare un logo di **piccole dimensioni** . Facoltativamente, è possibile sostituire questo oggetto con un'immagine diversa in un secondo momento.

- **Grande** (da 216 x 216 a 350 x 350 px, obbligatorio)
- **Piccolo** (48 x 48 px, facoltativo)

Questi logo vengono usati in posizioni diverse nell'elenco.

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Screenshot (facoltativo)

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

1. Quando si è pronti per pubblicare l'offerta del servizio di consulenza, selezionare **revisione e pubblicazione**.
2. Esaminare i dettagli nella pagina di invio finale.
3. Se necessario, scrivere una nota per il team di certificazione se si ritiene che alcuni dettagli dell'offerta richiedano una spiegazione.
4. Al termine, selezionare **Invia**.
5. La pagina **Panoramica offerta** mostra la fase di pubblicazione dell'offerta.

Per altre informazioni sulla tempistica di ogni fase di pubblicazione dell'offerta, vedere [Verificare lo stato di pubblicazione dell'offerta del marketplace commerciale](publishing-status.md).

## <a name="update-your-existing-consulting-service-offers"></a>Aggiornare le offerte dei servizi di consulenza esistenti

- [Aggiornare un'offerta esistente nel marketplace commerciale](update-existing-offer.md)
