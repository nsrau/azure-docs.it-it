---
title: Creare un'offerta di app Power BI - Azure MarketplaceCreate a Power BI app offer - Azure Marketplace
description: Informazioni su come creare e pubblicare un'offerta di app Power BI in Microsoft AppSource.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 776311d6e6395cbe462f958bd8685fa0259e1fc2
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674189"
---
# <a name="create-a-power-bi-app"></a>Creare un'app di Power BI

> [!IMPORTANT]
> Stiamo spostando la gestione delle offerte dell'app Power BI dal portale per i partner cloud al Centro per i partner. Fino alla migrazione delle offerte, seguire le istruzioni in [Creare un'offerta](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-create-offer) di app Power BI per il portale per i partner cloud per gestire le offerte.

In questo articolo viene descritto come creare e pubblicare un'offerta di app Power BI in Microsoft [AppSource.](https://appsource.microsoft.com/)

Prima di poter creare un'offerta di app Power BI, è necessario disporre di un account marketplace commerciale nel Centro per i partner. Se non ne hai ancora creato uno, vedi [Creare un account Marketplace commerciale nell'account del Centro per i partner.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di spostamento a sinistra selezionare**Panoramica** **Marketplace** > commerciale .

    :::image type="content" source="media/power-bi-menu-overview.png" alt-text="panoramica del menu del mercato commerciale" border="false":::

3. Selezionare **Nuova offerta** > **Power BI App**. Viene visualizzata la finestra di dialogo **Nuova offerta.**

> [!IMPORTANT]
> Se l'opzione **Power BI App** non viene visualizzata&#39;non è visualizzata o non è abilitata&#39;, l'account non&#39;dispone dell'autorizzazione per creare questo tipo di offerta. Verifica di aver soddisfatto&#39;tutti i [requisiti](create-power-bi-app-overview.md) per questo tipo di offerta, inclusa la registrazione per un account sviluppatore

### <a name="offer-id-and-alias"></a>ID e alias dell'offerta

Immettere un **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nel tuo account.

- Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta del marketplace e nei modelli di Azure Resource Manager, se applicabile.
- Usare solo lettere minuscole e numeri. Può includere trattini e caratteri di sottolineatura, ma senza spazi, ed è limitato a 50 caratteri. Ad esempio, se si immette **test-offer-1** , `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`l'indirizzo Web dell'offerta sarà .

- L'ID offerta non può essere modificato dopo aver selezionato **Crea**.

Immettere un **alias di offerta**. Questo è il nome usato per fare riferimento all'offerta nel Centro per i partner.

- Questo nome non viene utilizzato nel marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.
- Questa operazione non può essere modificata dopo aver selezionato **Crea**.

Dopo aver immesso questi due valori, selezionare **Crea** per passare alla pagina Panoramica offerta.

## <a name="offer-overview"></a>Panoramica dell'offerta

La pagina **Panoramica dell'offerta** mostra una rappresentazione visiva dei passaggi necessari per pubblicare questa offerta (sia completata che successiva) e della durata del completamento di ogni passaggio.

Include collegamenti per eseguire operazioni su questa offerta in base alla selezione effettuata. Ad esempio:

- Se l'offerta è una bozza - [Elimina bozza di offerta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Se l'offerta è in diretta - [Smetti di vendere l'offerta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Se l'offerta è in anteprima - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Se non hai completato la disconnessione del publisher - [Annulla pubblicazione](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Configurazione dell'offerta

Segui questi passaggi per configurare la tua offerta.

### <a name="connect-lead-management"></a>Gestione dei lead di Connect

Quando pubblichi l'offerta nel marketplace con il Centro per i partner, devi connetterla al sistema CRM (Customer Relationship Management). Ciò consente di ricevere le informazioni di contatto del cliente non appena qualcuno esprime interesse o utilizza il prodotto.

1. Selezionare la destinazione a cui si desidera che vengano inviati i lead. Il Centro per i partner supporta i seguenti sistemi CRM:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) per coinvolgimento dei clienti
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se il sistema CRM non è elencato in precedenza, usare [Tabella di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) o Endpoint [https](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) per archiviare i dati dei lead dei clienti. Esportare quindi i dati nel sistema CRM.

2. Collegare l'offerta alla destinazione lead durante la pubblicazione nel Centro per i partner.
3. Verificare che la connessione alla destinazione del lead sia configurata correttamente. Dopo averla pubblicata nel Centro per i partner, convalideremo la connessione e ti invieremo un responsabile di test. Durante l'anteprima dell'offerta prima che venga messa in diretta, puoi anche testare la connessione lead provando ad acquistare l'offerta nell'ambiente di anteprima.
4. Assicurati che la connessione alla destinazione lead rimanga aggiornata in modo da non perdere alcun lead.

Di seguito sono riportate alcune risorse aggiuntive per la gestione dei lead:Here are some additional lead management resources:

- [Panoramica della gestione dei lead](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Domande frequenti sulla gestione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Errori comuni di configurazione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Panoramica della gestione dei lead](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (assicurarsi che il blocco popup sia disattivato)

Selezionare **Salva bozza** prima di continuare con la sezione successiva, Proprietà.

## <a name="properties"></a>Proprietà

Questa pagina ti consente di definire le categorie e i settori utilizzati per raggruppare la tua offerta sul marketplace, la versione dell'app e i contratti legali che supportano la tua offerta.

### <a name="category"></a>Category

Selezionare un minimo di una e un massimo di tre categorie. Queste categorie vengono utilizzate per inserire l'offerta nelle aree di ricerca del marketplace appropriate e vengono visualizzate nella pagina dei dettagli dell'offerta. Nella descrizione dell'offerta, spiega come la tua offerta supporta queste categorie.

### <a name="industry"></a>Settore

Facoltativamente, selezionare fino a due settori e due verticali in ogni settore. Mentre le categorie vengono utilizzate per la visualizzazione dell'offerta, il settore e i verticali vengono utilizzati nei filtri di ricerca e vengono applicati nello Storefront. Se la tua offerta è destinata a un settore e/o verticale specifico, utilizza la descrizione dell'offerta per spiegare in che modo la tua offerta supporta i settori o i verticali selezionati. Se l'offerta non è&#39;specifica del settore, lasciare vuota questa sezione.

> [!NOTE]
> Mentre lavoriamo per introdurre nuove industrie e verticali per migliorare l'esperienza di scoperta dell'offerta, alcuni settori o verticali potrebbero non essere ancora visibili sullo Storefront. Le industrie e i verticali contrassegnati con un simbolo di canceltura saranno disponibili in una data futura. Tutte le offerte pubblicate sono individuabili tramite la ricerca per parole chiave.
<p>&nbsp;

| **Settore** | **Settore secondario** |
| --- | --- |
| -Automotive | -Automotive |
| Agricoltura | - Altro - Non segmentato |
| Distribuzione | - Vendita all'ingrosso<br>Spedizione di pacchi e pacchi |
| Formazione | *Istruzione<br> superiore* Istruzione primaria e secondaria / K-12<br>Librerie e Musei |
| Servizi finanziari | *Mercati bancari<br> e dei capitali* Assicurazione |
| Government | *Difesa e intelligence (usato per essere<br> chiamato Sicurezza Nazionale e Pubblica)* Sicurezza pubblica e giustizia<br>- Governo civile |
| Sanità (usato per essere chiamato Salute) | *Pagatore<br> della salute* Fornitore di servizi sanitari<br>Prodotti farmaceutici |
| Produzione e Risorse (utilizzate per essere chiamate Produzione) | *Chimica e agrochimica<br> * Produzione discreta<br>Energia |
| Vendita al dettaglio e beni di consumo (utilizzato per essere chiamato vendita al dettaglio) | *Beni<br> di consumo* Rivenditori |
| Media e comunicazioni (usati per supporti e intrattenimento) | *Media e<br> intrattenimento* Telecomunicazioni |
| Servizi professionali | *Legale<br> * Servizi professionali per i partner |
| Architettura e costruzione (usato per essere chiamato Ingegneria dell'Architettura) | - Altro - Non segmentato |
| - Ospitalità e viaggi | *Alberghi e<br> tempo libero* Viaggi e trasporti<br>Ristoranti e servizi alimentari |
| Altre industrie del settore pubblico | *La silvicoltura e la pesca<br> * Nonprofits |
| - Immobili | - Altro - Non segmentato |

### <a name="legal"></a>Note legali

#### <a name="terms-and-conditions"></a>Termini e condizioni

Per fornire termini e condizioni personalizzati, inserisci fino a 10.000 caratteri nella casella **Termini e condizioni.** Se i termini e le condizioni richiedono una descrizione più lunga, inserisci un singolo link web a cui sono reperibili. Verrà visualizzato ai clienti come collegamento attivo.

I clienti devono accettare questi termini prima di poter provare la tua offerta.

Seleziona **Salva bozze** prima di continuare con la sezione successiva, Offerta.

## <a name="offer-listing"></a>Inserzione dell'offerta

Qui definirai i dettagli dell'offerta che vengono visualizzati nel marketplace. Sono inclusi il nome, la descrizione, le immagini e così via.

### <a name="language"></a>Linguaggio

Seleziona la lingua in cui verrà elencata la tua offerta. Attualmente, **English (Stati Uniti)** è l'unica opzione disponibile.

Definire i dettagli del marketplace (ad esempio il nome, la descrizione e le immagini dell'offerta) per ogni lingua/mercato. Selezionare la lingua/nome del mercato per fornire queste informazioni.

> [!NOTE]
> I dettagli dell'offerta non sono necessari per essere &quot;in inglese se la descrizione dell'offerta inizia con la frase, Questa applicazione è disponibile solo in [lingua non in lingua inglese]. &quot; E '&#39;anche bene fornire un link utile per offrire contenuti in una lingua che&#39;s diverso da quello utilizzato nella lista offerta.

### <a name="name"></a>Nome

Il nome che inserisci qui viene visualizzato come titolo dell'offerta. Questo campo è precompilato con il testo immesso nella casella **Alias offerta** al momento della creazione dell'offerta. Il nome può essere modificato successivamente.

Il nome:

- Può essere registrato (e l'utente può includere simboli di marchio o copyright).
- Non può essere lungo più di 50 caratteri.
- Non è possibile includere emoji.

### <a name="search-results-summary"></a>Riepilogo dei risultati della ricerca

Fornisci una breve descrizione della tua offerta. Può essere lungo fino a 100 caratteri e viene utilizzato nei risultati di ricerca del marketplace.

### <a name="description"></a>Descrizione

Fornisci una descrizione più lunga della tua offerta, fino a 3.000 caratteri. Viene visualizzato dai clienti nella panoramica dell'elenco dei marketplace.

Includere una o più delle seguenti opzioni nella descrizione:

- Il valore e i vantaggi principali offerti dalla tua offerta.
- Associazioni di categoria o di settore, o entrambi.
- Opportunità di acquisto in-app.
- Eventuali divulgazioni obbligatorie.

Ecco alcuni suggerimenti per scrivere la tua descrizione:

- Descrivi chiaramente il valore della tua offerta nelle prime frasi della tua descrizione. Includere i seguenti elementi:
  - Descrizione dell'offerta.
  - Il tipo di utente che beneficia dell'offerta.
  - Il cliente ha bisogno o emette gli indirizzi dell'offerta.
- Ricorda che le prime frasi potrebbero essere visualizzate nei risultati della ricerca.
- Non fare affidamento su caratteristiche e funzionalità per vendere il tuo prodotto. Concentrati invece sul valore offerto dalla tua offerta.
- Prova a usare il vocabolario specifico del settore o la formulazione basata sui vantaggi.

Per rendere **Description** la descrizione dell'offerta più coinvolgente, usa l'editor RTF per formattare la descrizione. L'editor RTF consente di aggiungere numeri, punti elenco, grassetto, corsivo e rientri per rendere la descrizione più leggibile.

:::image type="content" source="media/power-bi-rich-text-editor.png" alt-text="illustra l'editor di testo RTF" border="false":::

### <a name="search-keywords"></a>Search Keywords (Parole chiave di ricerca)

Inserisci fino a tre parole chiave di ricerca facoltative per aiutare i clienti a trovare la tua offerta nel marketplace. Per ottenere risultati ottimali, utilizza anche queste parole chiave nella descrizione.

### <a name="helpprivacy-web-addresses"></a>Indirizzo Web Guida/Privacy

Fornisci collegamenti per aiutare i clienti a comprendere meglio la tua offerta.

#### <a name="help-link"></a>Collegamento alla Guida

Inserisci l'indirizzo web dove i clienti possono saperne di più sulla tua offerta.

#### <a name="privacy-policy-url"></a>URL dell'informativa sulla privacy

Immettere l'indirizzo Web dell'informativa sulla privacy dell'organizzazione. Hai la responsabilità di garantire che la tua offerta sia conforme alle leggi e ai regolamenti sulla privacy. Sei anche responsabile per la pubblicazione di una politica sulla privacy valida sul tuo sito web.

### <a name="contact-information"></a>Informazioni sul contatto

È necessario fornire il nome, l'indirizzo e-mail e il numero di telefono per un **contatto di supporto** e un contatto **Engineering**. Queste informazioni non vengono&#39;mostrate ai clienti. È disponibile per Microsoft e può essere fornito ai partner Cloud Solution Provider (CSP).

- Contatto di supporto (obbligatorio): per domande generali sul supporto tecnico.
- Contatto tecnico (obbligatorio): per domande tecniche e problemi di certificazione.
- Contatto del programma CSP (facoltativo): per le domande dei rivenditori relative al programma CSP.

Nella sezione **Contatto supporto** specificare l'indirizzo Web del sito Web del **supporto tecnico** in cui i partner possono trovare supporto per l'offerta.

### <a name="supporting-documents"></a>Documenti di supporto

Fornisci almeno uno e fino a tre documenti di marketing correlati in formato PDF. Ad esempio, white paper, brochure, elenchi di controllo o presentazioni.

### <a name="marketplace-images"></a>Immagini del Marketplace

Fornisci loghi e immagini da utilizzare con la tua offerta. Tutte le immagini devono essere in formato PNG. Le immagini sfocate verranno rifiutate.

#### <a name="store-logos"></a>Loghi del Negozio

Fornisci i file .png dell'offerta&#39;logo s in due dimensioni: **Piccolo** (48 x 48 pixel) e **Grande** (216 x 216 pixel).

Entrambi i loghi sono obbligatori e vengono utilizzati in luoghi diversi nella presentazione del marketplace.

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungi almeno uno e fino a cinque screenshot che mostrano come funziona la tua offerta. Ciascuno deve avere dimensioni 1280 x 720 pixel e in formato PNG.

#### <a name="videos-optional"></a>Video (opzionale)

Aggiungi fino a cinque video che dimostrano la tua offerta. Inserisci il video&#39;s nome, il suo indirizzo web e l'immagine in miniatura .png del video a 1280 x 720 pixel.

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per la presentazione del marketplace

Per ulteriori informazioni sulla creazione di inserzioni di offerte, consulta Best practice per [l'inserzione di offerte.](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="technical-configuration"></a>Configurazione tecnica

Promuovere l'app nel servizio Power BI all'ambiente di produzione e fornire il collegamento di installazione dell'app Power BI che consente ai clienti di installare l'app. Per altre informazioni, vedere [Pubblicare app con dashboard e report in Power BI.](https://docs.microsoft.com/power-bi/service-create-distribute-apps)

## <a name="supplemental-content"></a>Contenuti supplementari

Fornisci ulteriori informazioni sulla tua offerta per aiutarci a convalidarla. Queste informazioni non vengono&#39;mostrate ai clienti o pubblicate nel marketplace.

### <a name="validation-assets"></a>Risorse di convalida

Facoltativamente, aggiungi istruzioni (fino a 3.000 caratteri) per consentire al team di convalida Microsoft di configurare, connettere e testare l'app. Includere impostazioni di configurazione, account, parametri o altre informazioni tipiche che possono essere utilizzate per testare l'opzione Connetti dati. Queste informazioni sono visibili solo al team di convalida e vengono utilizzate solo a scopo di convalida.

## <a name="review-and-publish"></a>Revisione e pubblicazione

Dopo aver completato&#39;tutte le sezioni obbligatorie dell'offerta, puoi inviare l'offerta per la revisione e la pubblicazione.

Nell'angolo superiore destro del portale selezionare **Revisione e pubblicazione**.

Nella pagina di revisione è possibile:

- Vedi lo stato di completamento per ogni sezione dell'offerta. Non è possibile&#39;pubblicare fino a quando tutte le sezioni dell'offerta non sono contrassegnate come complete.
  - **Non avviato:** la sezione non è stata avviata e deve essere completata.
  - **Incompleto:** la sezione contiene errori che devono essere corretti o richiede di fornire ulteriori informazioni. Per istruzioni, vedere le sezioni precedenti in questo documento.
  - **Completo** - La sezione contiene tutti i dati richiesti e non ci sono errori. Tutte le sezioni dell'offerta devono essere complete prima di poter inviare l'offerta.
- Fornisci istruzioni di test al team di certificazione per assicurarti che l'app venga testata correttamente. Inoltre, fornisci eventuali note supplementari utili per comprendere la tua offerta.

Per inviare l'offerta per la pubblicazione, selezionare **Pubblica**.

Ti invieremo un'email per comunicarti quando è disponibile una versione di anteprima dell'offerta per la revisione e l'approvazione. Per pubblicare l'offerta al pubblico (o se un'offerta privata, a un pubblico privato), vai al Centro per i partner e seleziona **Go-live**.
