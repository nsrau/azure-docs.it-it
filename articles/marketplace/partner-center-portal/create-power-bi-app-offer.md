---
title: Creare un'offerta per un'app Power BI nel marketplace commerciale Microsoft
description: Informazioni su come creare e pubblicare un'offerta per un'app Power BI in Microsoft AppSource.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 16b44b3a4496e98f8b6b643bf76aa5aa7ce593c6
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849094"
---
# <a name="create-a-power-bi-app-for-microsoft-appsource"></a>Creare un'app Power BI per Microsoft AppSource

Questo articolo descrive come creare e pubblicare un'offerta per un'app Power BI in Microsoft [AppSource](https://appsource.microsoft.com/).

Prima di iniziare, [creare un account per il marketplace commerciale nel Centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) se non è ancora stato fatto. Assicurarsi che l'account sia registrato nel programma del marketplace commerciale.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di spostamento a sinistra selezionare **Marketplace commerciale** > **Panoramica**.
3. Nella pagina Panoramica selezionare **+ Nuova offerta** > **App del servizio Power BI**.

   ![Immagine del menu di spostamento a sinistra.](./media/new-offer-pbi-app.png)

> [!NOTE]
> Dopo la pubblicazione di un'offerta, le modifiche apportate all'offerta nel Centro per i partner vengono visualizzate nelle vetrine solo dopo la ripubblicazione dell'offerta. Assicurarsi di ripubblicare sempre l'offerta dopo avere apportato modifiche.

> [!IMPORTANT]
> Se l'opzione **App del servizio Power BI** non è visualizzata o abilitata, l'account non è autorizzato a creare questo tipo di offerta. Verificare di aver soddisfatto tutti i [requisiti](create-power-bi-app-overview.md) per questo tipo di offerta, tra cui la registrazione per un account sviluppatore.

## <a name="new-offer"></a>Nuova offerta

Immettere un ID in **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta nel marketplace e nei modelli di Azure Resource Manager, se applicabile.
- Usare solo lettere minuscole e numeri. Può includere trattini e caratteri di sottolineatura, ma senza spazi e con un limite di 50 caratteri. Se ad esempio si immette **test-offer-1**, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

Immettere un alias in **Alias offerta**. Si tratta del nome usato per l'offerta nel Centro per i partner.

- Questo nome non viene usato nel marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.
- L'alias dell'offerta non può essere modificato dopo aver selezionato **Crea**.

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-overview"></a>Panoramica dell'offerta

Questa pagina mostra una rappresentazione visiva dei passaggi per pubblicare l'offerta (completa o imminente) e il tempo necessario per completare ogni passaggio.

Sono inclusi i collegamenti per eseguire operazioni su questa offerta in base alla selezione effettuata. Ad esempio:

- Se l'offerta è una bozza, [Elimina bozza](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Se l'offerta è stata pubblicata, [Interrompi vendita](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Se l'offerta è in anteprima, [Passa allo stato Live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Se non è stata completata la disconnessione dell'editore, [Annulla pubblicazione](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Configurazione dell'offerta

### <a name="customer-leads"></a>Lead clienti

Quando si pubblica l'offerta nel marketplace con il Centro per i partner, è necessario connetterla al sistema Customer Relationship Management (CRM). In questo modo, è possibile ricevere le informazioni di contatto del cliente non appena un utente esprime interesse o usa il prodotto.

1. Selezionare la destinazione a cui si desidera che vengano inviati i lead. Il Centro per i partner supporta i sistemi CRM seguenti:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) for Customer Engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se il sistema CRM non è incluso nell'elenco, usare [Tabella di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) o [Endpoint HTTPS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) per archiviare i dati dei lead clienti. Esportare quindi i dati nel sistema CRM.

2. Quando si pubblica l'offerta nel Centro per i partner, connettere l'offerta alla destinazione dei lead.
3. Verificare che la connessione alla destinazione dei lead sia configurata correttamente. Dopo la pubblicazione nel Centro per i partner, la connessione verrà convalidata e verrà inviato un lead di test. Quando si visualizza l'offerta in anteprima prima della pubblicazione, è anche possibile testare la connessione dei lead provando ad acquistare personalmente l'offerta nell'ambiente di anteprima.
4. Assicurarsi che la connessione alla destinazione dei lead resti aggiornata in modo da non perdere alcun lead.

Di seguito sono elencate alcune risorse aggiuntive per la gestione dei lead:

- [Panoramica della gestione dei lead](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Domande frequenti sulla gestione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Errori comuni di configurazione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- PDF [Panoramica della gestione dei lead](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) (assicurarsi che il blocco dei popup sia disattivato)

Prima di continuare, selezionare **Salva bozza**.

## <a name="properties"></a>Proprietà

Questa pagina consente di definire le categorie e i settori usati per raggruppare l'offerta nel marketplace, la versione dell'app e i contratti legali che supportano l'offerta.

### <a name="category"></a>Categoria

Selezionare almeno una e fino a tre categorie. Queste categorie vengono usate per inserire l'offerta nelle aree di ricerca del marketplace appropriate e vengono visualizzate nella pagina dei dettagli dell'offerta. Nella descrizione dell'offerta spiegare in che modo l'offerta supporta queste categorie.

### <a name="industry"></a>Settore

È possibile selezionare fino a due settori e due segmenti verticali all'interno di ogni settore. Mentre le categorie vengono usate per la visualizzazione dell'offerta, il settore e i segmenti verticali vengono usati nei filtri di ricerca e vengono applicati nella vetrina. Se l'offerta è destinata a un settore e/o segmento verticale specifico, usare la descrizione dell'offerta per spiegare in che modo l'offerta supporta i settori o segmenti verticali selezionati. Se l'offerta non è specifica di un settore, lasciare vuota questa sezione.

> [!NOTE]
> Benché sia forte l'impegno a introdurre nuovi settori e segmenti verticali per migliorare l'esperienza di individuazione dell'offerta, alcuni settori o segmenti verticali potrebbero non essere ancora visibili nella vetrina. I settori e i segmenti verticali contrassegnati con (*) saranno disponibili in una data futura. Tutte le offerte pubblicate sono individuabili tramite la ricerca di parole chiave.
<p>&nbsp;

| **Settore** | **Settore secondario** |
| --- | --- |
| *Automobili | *Automobili |
| Agricoltura | *Altro, non segmentato |
| Distribuzione | *All'ingrosso<br>Spedizione pacchi e pacchetti |
| Formazione | *Istruzione superiore<br>* Istruzione primaria e secondaria/K-12<br>*Biblioteche e musei |
| Servizi finanziari | *Servizi bancari e mercati finanziari<br>* Assicurazioni |
| Government | *Difesa e servizi segreti (in precedenza Sicurezza pubblica e nazionale)<br>* Giustizia e pubblica sicurezza<br>*Governo civile |
| Servizi sanitari (in precedenza Sanità) | *Ente assistenziale sanitario<br>* Operatore sanitario<br>*Prodotti farmaceutici |
| Produzione e risorse (in precedenza Produzione) | *Chimica e agrochimica<br>* Produzione discreta<br>*Energia |
| Vendita al dettaglio e beni di consumo (in precedenza Vendita al dettaglio) | *Beni di consumo<br>* Rivenditori |
| *Media e comunicazioni (in precedenza Intrattenimento e contenuti multimediali) | *Media e intrattenimento<br>* Telecomunicazioni |
| Servizi professionali | *Legale<br>* Servizi professionali per i partner |
| \* Architettura ed edilizia (in precedenza Ingegneria dell'architettura) | *Altro, non segmentato |
| *Ospitalità e viaggi | *Hotel e tempo libero<br>* Viaggi e trasporti<br>*Ristoranti e servizi alimentari |
| *Altre aree del settore pubblico | *Silvicoltura e pesca<br>* No profit |
| *Immobiliare | *Altro, non segmentato |

### <a name="legal"></a>Note legali

#### <a name="terms-and-conditions"></a>Termini e condizioni

Per specificare termini e condizioni personalizzati, immettere fino a 10.000 caratteri nella casella **Termini e condizioni**. Se i termini e le condizioni richiedono una descrizione più lunga, immettere un unico collegamento Web alla pagina in cui possono essere consultati. Verrà visualizzato ai clienti come collegamento attivo.

I clienti devono accettare queste condizioni prima di poter provare l'offerta.

Selezionare **Salva bozza** prima di passare alla sezione successiva, Presentazione offerta.

## <a name="offer-listing"></a>Presentazione offerta

Qui è possibile definire i dettagli dell'offerta visualizzati nel marketplace. Sono inclusi il nome dell'offerta, la descrizione, le immagini e così via.

### <a name="language"></a>Lingua

Selezionare la lingua in cui verrà presentata l'offerta. Attualmente, **Inglese (Stati Uniti)** è l'unica opzione disponibile.

Definire i dettagli del marketplace per ogni lingua/mercato, ad esempio il nome dell'offerta, la descrizione e le immagini. Selezionare il nome della lingua e del mercato in cui fornire queste informazioni.

> [!NOTE]
> I dettagli dell'offerta non devono essere necessariamente in inglese se la descrizione dell'offerta inizia con la frase "Questa applicazione è disponibile solo in [lingua non inglese]". È anche possibile fornire un collegamento utile per offrire contenuti in una lingua diversa da quella usata nella presentazione dell'offerta.

### <a name="name"></a>Nome

Il nome immesso qui viene visualizzato come titolo dell'offerta. Questo campo è precompilato con il testo immesso nella casella **Alias offerta** quando è stata creata l'offerta. È possibile modificarlo in seguito.

Il nome:

- Può essere un marchio registrato (ed è possibile includere i simboli di marchio o copyright).
- Non può essere più lungo di 50 caratteri.
- Non può includere emoji.

### <a name="search-results-summary"></a>Riepilogo risultati di ricerca

Fornire una breve descrizione dell'offerta. Questa può essere lunga 100 caratteri e viene usata nei risultati della ricerca nel marketplace.

### <a name="description"></a>Descrizione

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="search-keywords"></a>Search Keywords (Parole chiave di ricerca)

Immettere fino a tre parole chiave di ricerca facoltative per aiutare i clienti a trovare l'offerta nel marketplace. Per ottenere risultati ottimali, usare queste parole chiave anche nella descrizione.

### <a name="helpprivacy-web-addresses"></a>Indirizzi Web della Guida/informativa sulla privacy

Fornire i collegamenti per aiutare i clienti a comprendere meglio l'offerta.

#### <a name="help-link"></a>Collegamento alla Guida

Immettere l'indirizzo Web in cui i clienti possono ottenere altre informazioni sull'offerta.

#### <a name="privacy-policy-url"></a>URL dell'informativa sulla privacy

Immettere l'indirizzo Web dell'informativa sulla privacy dell'organizzazione. L'editore è tenuto a verificare che l'offerta sia conforme alle leggi e normative sulla privacy. È anche responsabile della pubblicazione di un'informativa sulla privacy valida sul proprio sito Web.

### <a name="contact-information"></a>Informazioni sul contatto

È necessario specificare il nome, l'indirizzo di posta elettronica e il numero di telefono per **Contatto di supporto** e **Contatto tecnico**. Queste informazioni non vengono visualizzate ai clienti. Sono disponibili per Microsoft e possono essere fornite ai partner CSP (Cloud Solution Provider).

- Contatto di supporto (obbligatorio): per domande di supporto generali.
- Contatto tecnico (obbligatorio): per domande tecniche e problemi di certificazione.
- Contatto programma CSP (facoltativo): per domande destinate al rivenditore relative al programma CSP.

Nella sezione **Contatto di supporto** specificare l'indirizzo Web per **Sito Web supporto** in cui i partner possono trovare supporto per l'offerta.

### <a name="supporting-documents"></a>Documenti di supporto

Fornire almeno uno e fino a tre documenti di marketing correlati in formato PDF. Ad esempio, white paper, brochure, elenchi di controllo o presentazioni.

### <a name="marketplace-images"></a>Immagini del Marketplace

Fornire i logo e le immagini da usare con l'offerta. Tutte le immagini devono essere in formato PNG. Le immagini sfocate verranno rifiutate.

>[!NOTE]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio https://upload.xboxlive.com usato dal Centro per i partner.

#### <a name="store-logos"></a>Logo per lo Store

Fornire file PNG del logo dell'offerta in due dimensioni di pixel:
- **Piccola** (48 x 48)
- **Grande** (216 x 216)

Entrambi i logo sono obbligatori e vengono usati in posizioni diverse nella presentazione nel marketplace.

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere almeno uno e fino a cinque screenshot che mostrano il funzionamento dell'offerta. Ogni screenshot deve avere dimensioni pari a 1280 x 720 pixel ed essere in formato PNG.

#### <a name="videos-optional"></a>Video (facoltativo)

Aggiungere fino a cinque video che descrivono l'offerta. Immettere il nome del video, l'indirizzo Web e un'immagine PNG di anteprima del video con dimensioni 1280 x 720 pixel.

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per la presentazione nel marketplace

Per altre informazioni sulla creazione di presentazioni di offerte, vedere [Procedure consigliate per la presentazione di offerte](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

## <a name="technical-configuration"></a>Configurazione tecnica

Promuovere l'app nel servizio Power BI all'ambiente di produzione e fornire il collegamento al programma di installazione dell'app Power BI che consente ai clienti di installare l'app. Per altre informazioni, vedere [Pubblicare app con dashboard e report in Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).

## <a name="supplemental-content"></a>Contenuto supplementare

Fornire informazioni aggiuntive sull'offerta per semplificarne la convalida. Queste informazioni non vengono visualizzate ai clienti o pubblicate nel marketplace.

### <a name="validation-assets"></a>Risorse di convalida

È possibile aggiungere istruzioni (fino a 3.000 caratteri) per consentire al team di convalida Microsoft di configurare, connettere e testare l'app. Queste includono le impostazioni di configurazione, gli account, i parametri o altre informazioni tipiche che è possibile usare per testare l'opzione Connetti dati. Queste informazioni sono visibili solo al team di convalida e vengono usate solo per scopi di convalida.

## <a name="review-and-publish"></a>Rivedi e pubblica

Dopo aver completato tutte le sezioni obbligatorie dell'offerta, è possibile inviare l'offerta per la revisione e la pubblicazione.

Nell'angolo in alto a destra del portale selezionare **Rivedi e pubblica**.

Nella pagina di revisione è possibile:

- Visualizzare lo stato di completamento di ogni sezione dell'offerta. Non è possibile pubblicare l'offerta fino a quando tutte le sezioni non vengono contrassegnate come complete.
  - **Non avviata**: la sezione non è stata avviata e deve essere completata.
  - **Incompleta**: la sezione contiene errori che devono essere corretti o è necessario inserire altre informazioni. Per indicazioni, vedere le sezioni precedenti di questo documento.
  - **Completa**: la sezione contiene tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere complete prima di poter inviare l'offerta.
- Fornire al team di certificazione istruzioni di test per assicurarsi che l'app venga testata correttamente. Specificare anche eventuali note supplementari utili per comprendere l'offerta.

Per inviare l'offerta per la pubblicazione, selezionare **Pubblica**.

Si riceverà un messaggio di posta elettronica che indica che una versione di anteprima dell'offerta è disponibile per la revisione e l'approvazione. Per pubblicare l'offerta al pubblico o, se si tratta di un'offerta privata, a destinatari privati, passare al Centro per i partner e selezionare **Passa allo stato Live**.
