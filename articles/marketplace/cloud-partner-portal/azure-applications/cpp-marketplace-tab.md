---
title: Scheda Marketplace offerta applicazione Azure
description: Usare la scheda Marketplace per identificare gli asset di marketing per un'offerta di applicazione di Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: b5d54e4c6744675da0dea5b095590b71df6d2641
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962868"
---
# <a name="azure-application-marketplace-tab"></a>Scheda Marketplace per applicazioni di Azure

Usare la scheda Marketplace per descrivere l'applicazione di Azure e offrire asset di marketing. Questa scheda include i moduli seguenti: Panoramica, Artefatti di marketing, Gestione dei Lead e Note legali.

## <a name="overview-form"></a>Modulo Panoramica

Il modulo Panoramica presenta i campi obbligatori e facoltativi illustrati nella schermata successiva. I campi obbligatori sono indicati da un asterisco (*).

![Modulo Panoramica](./media/azureapp-marketplace-overview.png)

La tabella seguente descrive le impostazioni da usare per la creazione di una vetrina dell'offerta.   I campi accodati con un asterisco sono obbligatori.

|      Campo         |    Descrizione    |
|  ---------------   |  ---------------  |
| **Titolo\***        | Titolo dell'offerta. Verrà visualizzato in posizione prominente nel marketplace. La lunghezza massima consentita è di 50 caratteri. |
| **Riepilogo\***      | Breve riepilogo dell'offerta. La lunghezza massima consentita è di 100 caratteri.           |
| **Riepilogo lungo\*** | Riepilogo più lungo dell'offerta (sebbene possa coincidere con il riepilogo). La lunghezza massima consentita è di 256 caratteri.           |
| **Descrizione\***  | Descrizione dell'offerta. La lunghezza massima consentita è di 3000 caratteri. È consentito codice HTML semplice, inclusi i tag &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt; e header.  |
| **Identificatore marketing\*** | URL univoco da associare a questa offerta. Include in genere il nome dell'organizzazione e della soluzione. La lunghezza massima consentita è di 50 caratteri. Scegliere un identificatore di marketing breve e descrittivo per il servizio. Questo verrà usato negli URL di marketplace per l'offerta. Ad esempio, se l'ID editore è "contoso" e l'identificatore di marketing è "sampleApp", l'URL per l'offerta in Azure Marketplace sarà https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp  
| **Anteprima ID sottoscrizione\*** | Aggiungere da uno a 100 identificatori di sottoscrizione di visualizzatori anteprima. Queste sottoscrizioni consentite avranno accesso all'offerta disponibile in anteprima dopo la pubblicazione, prima che venga attivata.          |
| **Collegamenti utili**    | Facoltativamente, è possibile fornire collegamenti a diverse risorse per gli utenti dell'offerta, ad esempio supporto tecnico, documentazione, forum e così via.  È consigliabile aggiungere almeno un collegamento alla documentazione.            |
| **Categorie suggerite (max 5)\*** | Selezionare da una a cinque categorie. Le categorie selezionate sono usate per il mapping dell'offerta alle categorie di prodotti disponibili in Azure Marketplace e nel portale di Azure. Verranno visualizzate sulle pagine del browser e nella pagina dei dettagli del prodotto. |
|  |  |


## <a name="marketing-artifacts"></a>Artefatti di marketing

Il modulo Artefatti di marketing presenta i campi obbligatori e facoltativi illustrati nella schermata successiva. I campi obbligatori sono indicati da un asterisco (*).

![Modulo Artefatti di marketing](./media/azureapp-marketplace-artifacts.png)

La tabella seguente descrive gli artefatti di marketing.

|      Campo         |    Descrizione    |
|  ---------------   |  ---------------  |
| **Small\***        | Logo piccolo: Formato PNG 40x40 pixel     |
| **Medium\***       | Logo medio: 90x90 pixel in formato PNG    |
| **Large\***        | Logo grande: 115x115 pixel in formato PNG   |
| **Wide\***         | Logo Wide: 255x115 pixel in formato PNG    |
| **Hero** (Banner)           | Logo Hero facoltativo: 815x290 pixel in formato PNG. **Nota:** Dopo essere stata caricata, l'icona del logo alto non può essere eliminata. |
| **Screenshot (max 5)** |        Gli screenshot vengono visualizzati nella pagina dei dettagli del prodotto. Si tratta di un ottimo metodo per comunicare in modo visivo ciò che fa l'app e come funziona. Ad esempio, è possibile mostrare diagrammi di architettura o illustrazioni di casi d'uso. Gli screenshot sono facoltativi e sono limitati a 5 per ogni SKU. Per aggiungere uno screenshot:<ul><li>Selezionare **+ Aggiungi screenshot** per aprire la finestra Screenshot</li><li>**Nome**: immettere un nome/titolo (lunghezza massima di 100 caratteri).</li><li>**Upload**: caricare l'immagine. L'immagine deve essere in formato PNG, con una dimensione di 533 x 324 pixel.</li></ul>           |
| **Aggiungi video**      | Facoltativo, i video vengono visualizzati nella pagina dei dettagli del prodotto. Si tratta di un ottimo metodo per comunicare in modo visivo ciò che fa l'applicazione e come funziona. Per aggiungere un video: <ul><li>Selezionare **+ Aggiungi video** per aprire la finestra Video</li><li>**Nome**: immettere un nome/titolo (lunghezza massima di 100 caratteri).</li><li>**Collegamento**: immettere l'URL del sito che ospita il video (YouTube o Vimeo)</li><li>**Anteprima**: caricare un'immagine di anteprima. L'immagine deve essere in formato PNG, con una dimensione di 533 x 324 pixel.</li></ul>          |
|  |  |


### <a name="artifact-examples-in-azure-marketplace"></a>Esempi di artefatto in Azure Marketplace

La schermata successiva mostra un esempio di risultato di ricerca nel Marketplace.

![Risultati della ricerca dell'offerta nel Marketplace](./media/azureapp-marketplace-example-browse.png)

L'immagine seguente mostra come viene visualizzata l'offerta nel Marketplace dopo che l'utente ha fatto clic sul riquadro dell'offerta nei risultati della ricerca.

![Dettagli dei risultati della ricerca dell'offerta nel Marketplace](./media/azureapp-marketplace-example-details.png)


### <a name="artifact-examples-in-azure-portal"></a>Esempi di artefatto nel portale di Azure

La schermata seguente mostra come viene visualizzata un'offerta nel portale di Azure. L'offerta di applicazione in questo esempio viene trovata passando a **Marketplace > Tutto > Sviluppo e Test > Jenkins**. L'offerta Jenkins mostra un logo, un titolo e il nome dell'editore visualizzato.

![Sfogliare le offerte nel portale di Azure](./media/azureapp-portalbrowse-artifacts-jenkins.png)

La schermata successiva mostra informazioni dettagliate sull'applicazione quando un utente seleziona Jenkins.

![Dettagli dell'offerta nel portale di Azure](./media/azureapp-portal-artifacts-jenkins-details.png)


### <a name="logo-guidelines"></a>Linee guida per il logo

Ogni logo caricato nel portale Cloud Partner deve rispettare le linee guida seguenti:

- La progettazione di Azure ha una tavolozza dei colori semplice. Nel logo usare un numero ridotto di colori primari e secondari.
- I colori del tema del portale di Azure sono il bianco e il nero. Evitare di usare questi colori per lo sfondo del logo. Usare un colore che faccia risaltare il logo nel portale di Azure. Si consiglia di usare colori primari semplici. Se si usa uno sfondo trasparente, verificare che il logo e il testo non siano di colore bianco, nero o blu.
- Non usare uno sfondo sfumato sul logo.
- Evitare di inserire testo, anche il nome del marchio o della società, sul logo. L'aspetto del logo deve essere semplice e senza sfumature.
- Non estendere il logo.


#### <a name="hero-logo"></a>Logo alto

Il logo alto è facoltativo.

>[!IMPORTANT]
>Dopo il caricamento non è possibile eliminare il logo alto.

Usare le linee guida seguenti per il logo di un banner:

- Sfondi neri, bianchi e trasparenti non sono consentiti.
- Evitare di usare un colore chiaro per lo sfondo del logo. Il nome visualizzato dell'editore, il titolo del piano e il riepilogo lungo dell'offerta sono visualizzati con caratteri bianchi e devono risaltare sullo sfondo.
- Evitare di usare troppo testo durante la progettazione del logo. Il nome dell'editore, il titolo del piano, il riepilogo lungo dell'offerta e un pulsante Crea vengono incorporati a livello di codice all'interno del logo quando l'offerta viene presentata.
- Includere uno spazio rettangolare inutilizzato sul lato destro del logo alto. Questo spazio vuoto è di 415x100 pixel, scostato di 370 pixel a sinistra.


## <a name="lead-management"></a>Gestione dei lead

Il modulo Gestione dei lead presenta un campo facoltativo per configurare la gestione dei lead. Per configurare la gestione dei lead, selezionare la destinazione dei lead nell'elenco a discesa. La schermata successiva mostra le destinazioni disponibili.

![Selezionare la destinazione del lead](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>Selezionare l'icona informazioni per visualizzare questo messaggio: “Selezionare il sistema nel quale archiviare i clienti potenziali. Informazioni su come connettersi al sistema CRM sono disponibili [qui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)".

Per altre informazioni, vedere [Configurare lead relativi ai clienti](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads).


## <a name="legal"></a>Note legali

Usare il modulo Note legali per fornire la documentazione legale necessaria per ogni offerta.

Specificare le informazioni seguenti:

- **URL\* informativa sulla privacy** : immettere un collegamento all'informativa sulla privacy dell'app.
- **Condizioniperl'utilizzo\***  : immettere le condizioni per l'utilizzo per l'app. Per ottenere la versione di prova dell'app, i clienti devono accettare tali condizioni.

![Modulo Note legali](./media/azureapp-marketplace-legal.png)


## <a name="next-steps"></a>Passaggi successivi

[Scheda Supporto](./cpp-support-tab.md)
