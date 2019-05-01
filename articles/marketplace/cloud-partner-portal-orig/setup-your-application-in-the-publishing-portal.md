---
title: Configurare l'applicazione nel portale di pubblicazione
description: Istruzioni su come configurare la propria applicazione nel portale di pubblicazione Cloud.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6da80bd3b5fe487a44400f4f3c21e8fa49da75d4
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943571"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Configurare l'applicazione nel portale di pubblicazione

A questo punto si è pronti a configurare l'applicazione nel portale di pubblicazione.

## <a name="login-and-create-a-new-offer"></a>Accedere e creare una nuova offerta

1. Accedere al [Portale per Cloud Partner](https://cloudpartner.azure.com/).
2. Nella barra di spostamento a sinistra scegliere "+ Nuova offerta" e selezionare "Dynamics 365 for Customer Engagement".

![Selezione di una nuova offerta](./media/CRMScreenShot14.png)

1. Viene ora aperto l'"Editor" della nuova offerta in cui è possibile iniziare la creazione.

![Schermata Nuova offerta](./media/CRMScreenShot15.png)

1. I "moduli" da compilare sono visibili a sinistra nell'"Editor". Ogni "modulo" è costituito da un set di campi da compilare. I campi obbligatori sono contrassegnati con un asterisco rosso (\*).

I moduli principali per creare un'offerta Dynamics 365 for Customer Engagement sono quattro.

* Impostazioni dell'offerta
* Informazioni tecniche
* Dati da visualizzare nella vetrina
* Contatti

## <a name="fill-out-the-offer-settings-form"></a>Compilare il modulo delle impostazioni dell'offerta

Il modulo delle impostazioni dell'offerta è un modulo di base in cui specificare le impostazioni di offerta. I diversi campi sono descritti di seguito.

### <a name="offer-id"></a>Offer ID (ID offerta)

Identificatore univoco dell'offerta in un profilo di pubblicazione. Questo ID sarà visibile negli URL dei prodotti. Può essere composto solo da caratteri alfanumerici minuscoli o trattini (-). L'ID non può terminare con un trattino e può contenere massimo 50 caratteri. Questo campo è bloccato dopo la pubblicazione dell'offerta.

ad esempio, se un server di pubblicazione **"contoso"** server di pubblicazione viene creata un'offerta con ID offerta **"esempio-WebApp"**, verrà visualizzata in AppSource come "https:\//appsource.microsoft.com/marketplace/apps/contoso.sample-WebApp?tab=Overview"

### <a name="publisher-id"></a>Publisher ID (ID editore)

Questo elenco a discesa consente di scegliere il profilo di pubblicazione in cui si desidera pubblicare l'offerta. Questo campo è bloccato dopo la pubblicazione dell'offerta.

### <a name="name"></a>NOME

Nome visualizzato dell'offerta. È il nome che verrà visualizzato in [AppSource](https://appsource.microsoft.com/). Può contenere massimo 50 caratteri.

Fare clic su "Salva" per salvare le voci immesse. Il passaggio successivo consiste nell'aggiungere le informazioni tecniche dell'offerta.

## <a name="fill-out-the-technical-info-form"></a>Compilare il modulo delle informazioni tecniche


Nel modulo delle informazioni tecniche è possibile compilare le informazioni specifiche della propria soluzione Dynamics 365 for Customer Engagement. Al passaggio del mouse verranno presentate altre informazioni. Vedere l'esempio seguente.

![Schermata delle informazioni tecniche](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Informazioni sull'applicazione

In questi campi la maggior parte degli editori lascerà i valori predefiniti, User (Utente), No, No e non immetterà valori nel campo Application Configuration URL (URL configurazione applicazione), come illustrato nella schermata precedente.

### <a name="crm-package"></a>Pacchetto CRM

![Informazioni sul pacchetto CRM](./media/CRMScreenShot17.png)

Di seguito è riportata una spiegazione di questi campi:

* File name of your package (Nome file del pacchetto): nome del file creato nel passaggio precedente in cui è stato creato il file con estensione zip, ovvero il pacchetto CRM per AppSource. Nell'esempio precedente è "Microsoft\_SamplePackage.zip".
* URL of your package location (URL del percorso pacchetto): URL per l'account Archiviazione di Azure che contiene il nome file del pacchetto specificato sopra. È l'URL creato nel passaggio 9 della sezione precedente.
* Is there more than one crm package in your package file (Sono presenti più pacchetti CRM nel file del pacchetto): selezionare Yes (Sì) **SOLO** se si supportano più versioni di CRM con pacchetti diversi. Per la maggior parte dei partner questo valore è "No". Se si seleziona Yes (Sì), è necessario creare pacchetti AppSource per ogni versione della soluzione. _Nota: NON viene chiesto se si hanno più file con estensione **zip**. Se si dispone di più file solution.zip ma di un'unica versione, selezionare "No". Lo strumento per la creazione di pacchetti li raggrupperà automaticamente._

### <a name="crm-package-availability"></a>Disponibilità del pacchetto CRM

In questa sezione selezionare le aree geografiche di CRM in cui il pacchetto sarà reso disponibile. Per informazioni su quali CRM aree servono quali paesi/aree geografiche, vedere il collegamento: [https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Note: la distribuzione nei "cloud sovrani e US Gov" in Germania RICHIEDE UN'AUTORIZZAZIONE SPECIALE e LA CONVALIDA DURANTE LA CERTIFICAZIONE.

## <a name="storefront-details"></a>Dati da visualizzare nella vetrina

### <a name="offer-summary"></a>Riepilogo offerta

Il riepilogo della proposta di valore dell'offerta, che verrà visualizzato nella pagina di ricerca dell'offerta. La lunghezza massima è 100 caratteri.

### <a name="offer-description"></a>Descrizione dell'offerta

La descrizione che verrà visualizzata nella pagina dei dettagli dell'app. La lunghezza massima consentita è 1.300 caratteri.

### <a name="industries"></a>Settori

Selezionare il settore che corrisponde meglio all'app. Se l'app rientra in più settori, è possibile lasciare vuoto il campo.

### <a name="categories"></a>Categories

Selezionare le categorie rilevanti per l'app. Selezionare al massimo tre categorie.

### <a name="app-type"></a>Tipo di app

Selezionare il tipo di versione di valutazione che verrà supportata dall'app in AppSource. "Gratuito" significa che l'app è gratuita. "Versione di valutazione" significa che i clienti possono provare l'app per un breve periodo in AppSource. Il tipo "Richiesta per la versione di valutazione" non è supportato per le app Dynamics 365 for Customer Engagement. Non selezionare questa opzione.

### <a name="help-link-for-your-app"></a>Help link for your app (Collegamento alla Guida per l'app)

Immettere l'URL a una pagina contenente le informazioni della Guida correlate per l'app.

### <a name="supported-countriesregions"></a>Supported countries/regions (Paesi supportati)

Questo campo specifica i paesi o le aree in cui sarà disponibile la versione di valutazione dell'offerta.

### <a name="supported-languages"></a>Lingue supportate

Selezionare le lingue supportate dall'app. Se l'app supporta lingue aggiuntive non incluse in questo elenco, continuare a pubblicare l'offerta e segnalare questo aspetto scrivendo all'indirizzo [appsource@microsoft.com](mailto:appsource@microsoft.com).

### <a name="app-version"></a>Versione dell'app

Immettere il numero di versione per l'app.

### <a name="app-release-date"></a>Data di rilascio dell'app

Immettere la data di rilascio per l'app

### <a name="products-your-app-works-with-max-3"></a>Prodotti interrelati con l'app (al massimo 3)

Elencare i prodotti specifici interrelati con l'app. È possibile elencare fino a tre prodotti. Per elencare un prodotto, fare clic sul segno più (accanto a Nuovo). Viene creato un nuovo campo di testo aperto in cui è possibile immettere il nome di un prodotto interrelato con l'app.

### <a name="search-keywords-max-3"></a>Parole chiave di ricerca (al massimo 3)

AppSource consente al cliente di eseguire la ricerca in base a parole chiave. È possibile immettere il set di parole chiave con cui l'applicazione verrà visualizzata ai clienti.

Ad esempio se l'applicazione è un "servizio di invio e-mail personale" alcune parole chiave potrebbero essere e-mail, posta elettronica, servizio di posta elettronica. Scegliere parole che gli utenti useranno probabilmente per cercare l'app nella casella di ricerca di AppSource.

### <a name="hide-key"></a>Hide key (Chiave nascosta)

È una chiave che verrà combinata con l'URL di anteprima dell'offerta che impedisce la visualizzazione pubblica dell'offerta. Non è una password. È possibile immettere qualsiasi stringa.

### <a name="offer-logo-png-format-48x48"></a>Logo dell'offerta (formato PNG, 48x48)

Verrà visualizzato nella pagina di ricerca dell'app. **È consentito solo il formato PNG.** Caricare un'immagine PNG con una risoluzione di 48\*48 pixel

### <a name="offer-logo-png-format-216x216"></a>Logo dell'offerta (formato PNG 216x216)

Verrà visualizzato nella pagina dei dettagli dell'app. **È consentito solo il formato PNG.** Caricare un'immagine PNG con una risoluzione di 216\*216 pixel

### <a name="videos"></a>Video

È possibile caricare al massimo quattro video. Per ogni video da caricare è necessario immettere il nome del video, l'URL (solo YouTube o Vimeo) e l'anteprima da associare al video. L'anteprima deve essere nel formato PNG e con una risoluzione di 1.280\*720 pixel. Per aggiungere nuovi video, fare clic sul segno più. Le anteprime dei video verranno visualizzate nella pagina dei dettagli dell'app.

### <a name="documents"></a>Documenti

È possibile caricare al massimo tre documenti in formato PDF. Per ogni documento da caricare, è necessario immettere il relativo nome e caricare il documento. Il documento deve essere in formato PDF.

Per aggiungere nuovi documenti, fare clic sul segno più.

### <a name="screenshots"></a>Screenshots (Schermate)

Sono gli screenshot che verranno visualizzati nella pagina dei dettagli per l'app di AppSource.

### <a name="privacy-policy"></a>Informativa sulla privacy

Immettere l'URL dell'informativa sulla privacy dell'app

### <a name="terms-of-use"></a>Condizioni per l'utilizzo

Immettere le condizioni per l'uso dell'app. Per ricevere la versione di valutazione dell'app, i clienti di AppSource sono tenuti ad accettare queste condizioni.

### <a name="support-url"></a>URL del supporto

Immettere l'URL del supporto per l'app.

### <a name="lead-destination"></a>Clienti potenziali destinatari

Selezionare un sistema CRM nel quale verranno archiviati i clienti potenziali. Selezionare "Azure Table" (Tabella di Azure) se si usa uno di questi sistemi CRM: Salesforce, Marketo, Microsoft Dynamics CRM. Nel sistema CRM selezionato verranno scritti i dettagli degli utenti finali che provano l'app specifica in AppSource (clienti potenziali). In base al sistema CRM selezionato fare clic sull'URL corrispondente di seguito per informazioni su come completare il set di campi successivo.

* [Tabella di Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Dati da visualizzare nella vetrina

I dettagli di contatto vengono usati solo per le comunicazioni interne tra il partner e Microsoft. Note: in questi campi è importante usare un indirizzo di posta elettronica che viene monitorato. Questo indirizzo verrà usato per comunicare con l'utente sullo stato di avanzamento della pubblicazione in AppSource. Solo l'URL del supporto sarà visibile ai clienti.
