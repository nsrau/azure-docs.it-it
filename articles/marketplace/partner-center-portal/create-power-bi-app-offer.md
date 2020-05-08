---
title: Creare un'offerta di app Power BI in Microsoft Commercial Marketplace
description: Informazioni su come creare e pubblicare un'offerta di app Power BI per Microsoft AppSource.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 17a6b7e46341ee0231ac88b849495cfc7e46ad88
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82839148"
---
# <a name="create-a-power-bi-app-for-microsoft-appsource"></a>Creare un'app Power BI per Microsoft AppSource

Questo articolo descrive come creare e pubblicare un'offerta di app Power BI a Microsoft [AppSource](https://appsource.microsoft.com/).

Prima di iniziare, [creare un account Marketplace commerciale nel centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , se non è ancora stato fatto. Assicurarsi che l'account sia registrato nel programma Commercial Marketplace.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al centro per i [partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di navigazione a sinistra selezionare **Commercial Marketplace** > **Overview**.
3. Nella pagina Panoramica selezionare **+ nuova offerta** > **Power bi servizio app**.

   ![Viene illustrato il menu di spostamento a sinistra.](./media/new-offer-pbi-app.png)

> [!NOTE]
> Dopo la pubblicazione di un'offerta, le modifiche apportate al centro per i partner vengono visualizzate solo nelle vetrine dopo la ripubblicazione dell'offerta. Assicurarsi di ripubblicare sempre dopo avere apportato modifiche.

> [!IMPORTANT]
> Se **Power bi app del servizio** non viene visualizzata o abilitata, l'account non è autorizzato a creare questo tipo di offerta. Verificare di aver soddisfatto tutti i [requisiti](create-power-bi-app-overview.md) per questo tipo di offerta, inclusa la registrazione per un account per sviluppatore.

## <a name="new-offer"></a>Nuova offerta

Immettere un **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta Marketplace e Azure Resource Manager modelli, se applicabile.
- Usare solo lettere minuscole e numeri. Può includere trattini e caratteri di sottolineatura, ma senza spazi e con un limite di 50 caratteri. Se ad esempio si immette **test-offer-1** qui, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

Immettere un **alias dell'offerta**. Questo è il nome usato per l'offerta nel centro per i partner.

- Questo nome non viene usato nel Marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati per i clienti.
- Non è possibile modificare l'alias dell'offerta dopo aver selezionato **Crea**.

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-overview"></a>Panoramica dell'offerta

Questa pagina mostra una rappresentazione visiva dei passaggi necessari per pubblicare l'offerta (completa e imminente) e il tempo necessario per completare ogni passaggio.

Sono inclusi i collegamenti per eseguire operazioni su questa offerta in base alla selezione effettuata. Ad esempio:

- Se l'offerta è un'offerta bozza di [eliminazione](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Se l'offerta è Live- [stop selling the offer](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Se l'offerta è in anteprima- [Go-Live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Se il server di pubblicazione non è stato completato, [annullare la pubblicazione](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Installazione dell'offerta

### <a name="connect-lead-management"></a>Gestione dei lead di connessione

Quando si pubblica l'offerta nel Marketplace con il centro per i partner, è necessario connetterla al sistema CRM (Customer Relationship Management). In questo modo è possibile ricevere le informazioni di contatto del cliente non appena un utente esprime l'interesse o usa il prodotto.

1. Selezionare la destinazione a cui si desidera che vengano inviati i lead. Il centro per i partner supporta i sistemi CRM seguenti:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) per il coinvolgimento dei clienti
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se il sistema CRM non è elencato sopra, usare la [tabella di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) o l' [endpoint HTTPS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) per archiviare i dati dei clienti. Esportare quindi i dati nel sistema CRM.

2. Connetti l'offerta alla destinazione principale durante la pubblicazione nel centro per i partner.
3. Verificare che la connessione alla destinazione principale sia configurata correttamente. Dopo la pubblicazione nel centro per i partner, la connessione verrà convalidata e verrà inviato un lead di test. Quando si visualizza l'anteprima dell'offerta prima che venga attivata, è anche possibile testare la connessione del lead provando ad acquistare l'offerta nell'ambiente di anteprima.
4. Assicurarsi che la connessione alla destinazione principale rimanga aggiornata in modo da non perdere i lead.

Di seguito sono riportate alcune risorse di gestione lead aggiuntive:

- [Panoramica della gestione dei lead](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Domande frequenti sulla gestione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Errori comuni di configurazione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Panoramica della gestione dei lead](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (assicurarsi che il blocco popup sia disattivato)

Selezionare **Salva bozza** prima di continuare.

## <a name="properties"></a>Proprietà

Questa pagina consente di definire le categorie e i settori usati per raggruppare l'offerta nel Marketplace, la versione dell'app e i contratti legali che supportano l'offerta.

### <a name="category"></a>Categoria

Selezionare un minimo di uno e un massimo di tre categorie. Queste categorie vengono usate per inserire l'offerta nelle aree di ricerca del Marketplace appropriate e vengono visualizzate nella pagina dei dettagli dell'offerta. Nella descrizione dell'offerta, spiegare in che modo l'offerta supporta queste categorie.

### <a name="industry"></a>Settore

Facoltativamente, selezionare fino a due settori e due verticali in ogni settore. Mentre le categorie vengono usate per la visualizzazione dell'offerta, il settore e le verticali vengono usati nei filtri di ricerca e vengono applicati nella vetrina. Se l'offerta è destinata a un settore specifico e/o verticale, usare la descrizione dell'offerta per spiegare in che modo l'offerta supporta i settori selezionati o verticali. Se l'offerta non è specifica del settore, lasciare vuota questa sezione.

> [!NOTE]
> Quando lavoriamo per introdurre nuovi settori e verticali per migliorare l'esperienza di individuazione dell'offerta, alcuni settori o verticali potrebbero non essere ancora visibili nella vetrina. I settori e i verticali contrassegnati con (*) saranno disponibili in una data futura. Tutte le offerte pubblicate sono individuabili tramite la ricerca di parole chiave.
<p>&nbsp;

| **Settore** | **Sottosettore** |
| --- | --- |
| * Automotive | * Automotive |
| Agricoltura | * Altro-non segmentato |
| Distribuzione | * Ingrosso<br>Spedizione pacchetti e pacchetti |
| Education | *Istruzione<br> superiore* Educazione primaria e secondaria/K-12<br>* Librerie e musei |
| Servizi finanziari | *Servizi bancari e mercati<br> finanziari* Insurance |
| Government | *Difesa e Intelligence (per la sicurezza nazionale e pubblica)<br> * Sicurezza pubblica e giustizia<br>* Governo civile |
| Healthcare (chiamato Health) | *Pagante<br> di integrità* Provider di integrità<br>* Farmaci |
| Produzione e risorse (chiamate produzione) | *Chemical e agrochimica<br> * Produzione discreta<br>* Energia |
| Vendita al dettaglio e beni di consumo (da chiamare al dettaglio) | *Beni<br> di consumo* Rivenditori |
| * Supporti e comunicazioni (chiamati supporti e intrattenimento) | *Media e intrattenimento<br> * Telecomunicazioni |
| Servizi professionali | *Note<br> legali* Servizi professionali per i partner |
| * Architettura e costruzione (denominata progettazione dell'architettura) | * Altro-non segmentato |
| * Ospitalità e viaggi | *Alberghi e svago<br> * Viaggi e trasporti<br>* Ristoranti e servizi alimentari |
| * Altri settori pubblici | *Silvicoltura e pesca<br> * Organizzazioni no profit |
| * Immobiliare | * Altro-non segmentato |

### <a name="legal"></a>Note legali

#### <a name="terms-and-conditions"></a>Termini e condizioni

Per specificare i termini e le condizioni personalizzati, immettere fino a 10.000 caratteri nella casella **termini e condizioni** . Se i termini e le condizioni richiedono una descrizione più lunga, immettere un solo collegamento Web per la posizione in cui possono essere trovati. Verrà visualizzato come collegamento attivo ai clienti.

I clienti devono accettare questi termini prima di poter provare l'offerta.

Selezionare **Salva bozza** prima di continuare con la sezione successiva, ad elenco offerte.

## <a name="offer-listing"></a>Elenco offerte

Qui verranno definiti i dettagli dell'offerta visualizzati nel Marketplace. Sono inclusi il nome dell'offerta, la descrizione, le immagini e così via.

### <a name="language"></a>Lingua

Selezionare la lingua in cui verrà elencata l'offerta. Attualmente, l' **inglese (Stati Uniti)** è l'unica opzione disponibile.

Definire i dettagli del Marketplace, ad esempio il nome, la descrizione e le immagini dell'offerta, per ogni lingua/mercato. Selezionare il nome del mercato/lingua per fornire queste informazioni.

> [!NOTE]
> I dettagli dell'offerta non devono essere in inglese se la descrizione dell'offerta inizia con la frase "questa applicazione è disponibile solo in [lingua non inglese]". È anche possibile fornire un collegamento utile per offrire contenuti in una lingua diversa da quella usata nell'elenco di offerte.

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

- Il valore e i vantaggi principali offerti dall'offerta.
- Categoria o associazioni di settore o entrambi.
- Opportunità di acquisto in-app.
- Eventuali divulgazioni obbligatorie.

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

### <a name="search-keywords"></a>Search Keywords (Parole chiave di ricerca)

Immettere fino a tre parole chiave di ricerca facoltative per aiutare i clienti a trovare l'offerta nel Marketplace. Per ottenere risultati ottimali, usare anche queste parole chiave nella descrizione.

### <a name="helpprivacy-web-addresses"></a>Indirizzi Web della guida/privacy

Fornire i collegamenti per aiutare i clienti a comprendere meglio l'offerta.

#### <a name="help-link"></a>Collegamento alla guida

Immettere l'indirizzo Web in cui i clienti possono ottenere ulteriori informazioni sull'offerta.

#### <a name="privacy-policy-url"></a>URL informativa sulla privacy

Immettere l'indirizzo Web dell'informativa sulla privacy dell'organizzazione. L'utente è responsabile di garantire che l'offerta sia conforme alle leggi e alle normative sulla privacy. L'utente è anche responsabile dell'invio di un criterio di privacy valido nel sito Web.

### <a name="contact-information"></a>Informazioni sul contatto

È necessario specificare il nome, l'indirizzo di posta elettronica e il numero di telefono per un **contatto di supporto** e un **contatto tecnico**. Queste informazioni non vengono visualizzate ai clienti. È disponibile per Microsoft e può essere fornita ai partner Cloud Solution Provider (CSP).

- Contatto per il supporto tecnico (obbligatorio): per domande di supporto generale.
- Contatto tecnico (obbligatorio): per domande tecniche e problemi di certificazione.
- Contatto del programma CSP (facoltativo): per domande sul rivenditore correlate al programma CSP.

Nella sezione **contatto del supporto tecnico** , fornire l'indirizzo Web del **sito Web di supporto** in cui i partner possono trovare il supporto per l'offerta.

### <a name="supporting-documents"></a>Documenti di supporto

Fornire almeno uno e tre documenti di marketing correlati in formato PDF. Ad esempio, white paper, brochure, elenchi di controllo o presentazioni.

### <a name="marketplace-images"></a>Immagini del Marketplace

Fornire i logo e le immagini da usare con l'offerta. Tutte le immagini devono essere in formato png. Le immagini sfocate verranno rifiutate.

>[!NOTE]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il https://upload.xboxlive.com servizio usato dal centro per i partner.

#### <a name="store-logos"></a>Logo dello Store

Fornire file con estensione png del logo dell'offerta in due dimensioni in pixel:
- **Piccolo** (48 x 48)
- **Grande** (216 x 216)

Entrambi i logo sono obbligatori e vengono usati in posizioni diverse nell'inserzione del Marketplace.

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere almeno una e fino a cinque schermate che mostrano il funzionamento dell'offerta. Ogni deve avere una dimensione di 1280 x 720 pixel e in formato png.

#### <a name="videos-optional"></a>Video (facoltativo)

È possibile aggiungere fino a cinque video che illustrano l'offerta. Immettere il nome del video, il relativo indirizzo Web e l'immagine thumbnail. png del video con dimensioni di 1280 x 720 pixel.

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per l'elenco del Marketplace

Per ulteriori informazioni sulla creazione di elenchi di offerte, vedere l'articolo relativo alle [procedure consigliate](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)per l'elenco.

## <a name="technical-configuration"></a>Configurazione tecnica

Alzare di livello l'app nel servizio Power BI alla produzione e fornire il collegamento Power BI App Installer che consente ai clienti di installare l'app. Per altre informazioni, vedere [pubblicare app con dashboard e report in Power bi](https://docs.microsoft.com/power-bi/service-create-distribute-apps).

## <a name="supplemental-content"></a>Contenuto supplementare

Fornire informazioni aggiuntive sull'offerta per consentirne la convalida. Queste informazioni non vengono visualizzate ai clienti o pubblicate sul Marketplace.

### <a name="validation-assets"></a>Asset di convalida

Facoltativamente, aggiungere istruzioni (fino a 3.000 caratteri) per consentire al team di convalida Microsoft di configurare, connettere e testare l'app. Includere le impostazioni di configurazione, gli account, i parametri o altre informazioni tipiche che è possibile utilizzare per testare l'opzione Connetti dati. Queste informazioni sono visibili solo al team di convalida e vengono utilizzate solo a scopo di convalida.

## <a name="review-and-publish"></a>Esaminare e pubblicare

Una volta completate tutte le sezioni obbligatorie dell'offerta, è possibile inviare l'offerta per la revisione e la pubblicazione.

Nell'angolo superiore destro del portale selezionare **revisione e pubblicazione**.

Nella pagina verifica è possibile:

- Vedere lo stato di completamento di ogni sezione dell'offerta. Non è possibile pubblicare fino a quando tutte le sezioni dell'offerta non vengono contrassegnate come complete.
  - **Non avviato** : la sezione non è stata avviata e deve essere completata.
  - **Incompleto** . nella sezione sono presenti errori che devono essere corretti oppure è necessario fornire altre informazioni. Per informazioni aggiuntive, vedere le sezioni precedenti di questo documento.
  - **Completato** : la sezione contiene tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere completate prima di poter inviare l'offerta.
- Fornire istruzioni di test al team di certificazione per assicurarsi che l'app venga testata correttamente. Inoltre, fornire eventuali note supplementari utili per comprendere l'offerta.

Per inviare l'offerta per la pubblicazione, selezionare **pubblica**.

Ti invieremo un messaggio di posta elettronica per informare l'utente quando sarà disponibile una versione di anteprima dell'offerta da rivedere e approvare. Per pubblicare l'offerta al pubblico (o, se un'offerta privata, per un pubblico privato), accedere al centro per i partner e selezionare **Go-Live**.
