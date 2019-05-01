---
title: Descrizione di Marketplace per un modulo Azure IoT Edge | Azure Marketplace
description: Creare la descrizione del marketplace per un modulo IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: aa0c908e18233926965d8dc3cd1b474f876b8dda
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942401"
---
# <a name="iot-edge-module-marketplace-tab"></a>Scheda Marketplace del modulo IoT Edge

La scheda **Marketplace** della pagina **Nuova offerta** consente di offrire ai clienti potenziali informazioni e contratti legali, di vendita e marketing e di gestire i clienti potenziali generati dal marketplace. Questa forma estesa è suddivisa in quattro sezioni: **Cenni preliminari sulla**, **gli elementi di Marketing**, **condurre una gestione**, e **legali**.


## <a name="overview"></a>Panoramica

In questa sezione immettere le informazioni generali sull'offerta di Azure Marketplace.  Un asterisco (*) accodato al nome del campo indica che si tratta di un campo obbligatorio.

![Sezione Overview (Panoramica) della scheda Marketplace nel modulo Nuova offerta per i moduli IoT Edge](./media/iot-edge-module-marketplace-tab-overview.png)

La tabella seguente descrive lo scopo e il contenuto di questi campi. I campi obbligatori sono indicati da un asterisco (*).

|  **Campo**                |     **Descrizione**                                                          |
|  ---------                |     ---------------                                                          |
| **Titolo\***                 | Titolo dell'offerta. Verrà visualizzato in posizione prominente nel marketplace.  La lunghezza massima consentita è di 50 caratteri. <!--ADD PICTURE IN ACTION-->|
| **Riepilogo\***               | Breve riepilogo dell'offerta. La lunghezza massima consentita è di 100 caratteri. <!--ADD PICTURE IN ACTION-->|
| **Riepilogo esteso\***          | Riepilogo più lungo dell'offerta (sebbene possa essere coincidere con il **riepilogo**).  La lunghezza massima consentita è di 256 caratteri. <!--ADD PICTURE IN ACTION-->|
| **Descrizione\***           | Descrizione dell'offerta.  La lunghezza massima consentita è di 3000 caratteri. Supporta la formattazione HTML semplice.<br/> Deve includere un paragrafo di *requisiti hardware minimi* nella parte inferiore, ad esempio: <br/> <p><u>Requisiti hardware minimi:</u> Linux x64 e arm32 sistema operativo, 1 GB di RAM, 500 Mb di spazio di archiviazione</p>
| **Identificatore di marketing\***  | URL univoco da associare a questa offerta. Include in genere il nome dell'organizzazione e della soluzione. La lunghezza massima consentita è di 50 caratteri.  Ad esempio:  <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleIoTEdgeModule`  |
| **Preview Subscription IDs** (ID sottoscrizione di anteprima) | Aggiungere da uno a 100 identificatori di sottoscrizione di visualizzatori anteprima. Queste sottoscrizioni consentite avranno accesso all'offerta dopo la pubblicazione, prima che venga attivata. |
| **Collegamenti utili**          | Selezione multipla di categorie commerciali e tecniche a cui associare l'offerta in modo ottimale.  È consentita una selezione massima di 10 elementi. Assicurarsi di aggiungere almeno un collegamento alla documentazione e un collegamento ai dispositivi IoT Edge compatibili dal [catalogo di dispositivi Azure IoT](https://catalog.azureiotsolutions.com/). |
| **Categorie suggerite\*** | Scegliere fino a cinque categorie. Verranno visualizzati nella pagina dei dettagli del prodotto. Nelle pagine del browser tutti i moduli IoT Edge vengono visualizzati nella categoria *Internet delle cose \> Modulo IoT Edge*.|
|  |  |


### <a name="offer-example"></a>Esempio di offerta

 Gli esempi seguenti illustrano in che modo vengono visualizzati i campi **Titolo**, **Riepilogo**, **Descrizione**, **Logos** (Logo) e **Screenshot** dell'offerta in visualizzazioni diverse.

 
#### <a name="on-the-azure-marketplace-website"></a>Nel sito Web di Azure Marketplace:

- Quando si esplorano le offerte:

    ![Modalità di visualizzazione delle offerte del marketplace nel sito Web di Azure Marketplace - Esplorazione](./media/iot-edge-module-ampdotcom-card.png)

- Quando si osservano i dettagli dell'offerta:

    ![Modalità di visualizzazione del modulo IoT Edge durante l'osservazione dei dettagli del prodotto nel sito Web](./media/iot-edge-module-ampdotcom-pdp.png)


#### <a name="on-the-azure-portal-website"></a>Nel sito Web del portale di Azure:

- Quando si esplorano le offerte:

    ![Modalità visualizzazione del modulo IoT Edge durante l'esplorazione del portale di Azure n. 1](./media/iot-edge-module-portal-browse.png)

    ![Modalità visualizzazione del modulo IoT Edge durante l'esplorazione del portale di Azure n. 2](./media/iot-edge-module-portal-product-picker.png)

- Quando si cerca un'offerta:

    ![Modalità visualizzazione del modulo IoT Edge durante le ricerche nel portale di Azure](./media/iot-edge-module-portal-search.png)

- Quando si osservano i dettagli dell'offerta:

    ![Modalità di visualizzazione del modulo IoT Edge durante l'osservazione dei dettagli del prodotto nel portale](./media/iot-edge-module-portal-pdp.png)


## <a name="marketing-artifacts"></a>Artefatti di marketing

Questa sezione include le sottosezioni seguenti: **Logo**, **Screenshot**, e **video**. 

>[!Note]
>I logo sono gli unici artefatti di marketing obbligatori, ma tutti sono vivamente consigliati per catturare meglio l'attenzione dei clienti.

![Sezione Marketing Artifacts (Artefatti di marketing) della scheda Marketplace nel modulo Nuova offerta per le macchine virtuali](./media/publishvm_009.png)

|  **Campo**                |     **Descrizione**                                                          |
|  ---------                |     ---------------                                                          |
| *Logos* (Logo)  | Vedere le schermate precedenti per scoprire come e dove verranno usati i logo.  |
| **Small\***                 | Formato PNG 40x40 pixel                                                     |
| **Medium\***                | Formato PNG 90x90 pixel                                                     |
| **Large\***                 | Formato PNG 115x115 pixel                                                  |
| **Wide\***                  | Formato PNG 255x115 pixel                                                   |
| **Hero** (Banner)                  | Formato PNG 815x290 pixel  Facoltativo. Una volta caricata, tuttavia, l'icona del banner non può essere eliminata. |
| *Screenshot*  | Gli screenshot vengono visualizzati nella pagina dei dettagli del prodotto. Si tratta di un ottimo metodo per comunicare in modo visivo ciò che fa il modulo IoT Edge e come funziona. Ad esempio, è possibile mostrare diagrammi di architettura o illustrazioni di casi d'uso. Facoltativi, ma non più di cinque per SKU. |
| **Nome**                  | Nome o titolo. La lunghezza massima consentita è di 100 caratteri.                             |
| **Immagine**                 | Acquisizione di schermata. Formato PNG 533x324                               |
| *Video*  | I video vengono visualizzati nella pagina dei dettagli del prodotto. Si tratta di un ottimo metodo per comunicare in modo visivo ciò che fa il modulo IoT Edge e come funziona. |
| **Nome**                  | Nome o titolo. La lunghezza massima consentita è di 100 caratteri.                             |
| **Collegamento**                  | URL del video, ospitato in YouTube o Vimeo                                        |
| **Anteprima**             | Formato PNG 533x324 pixel                                                     |
|  |  |


### <a name="logo-guidelines"></a>Linee guida per il logo

<!-- It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Ogni logo caricato nel portale Cloud Partner deve rispettare le linee guida seguenti:

*  La progettazione di Azure ha una tavolozza dei colori semplice. Nel logo usare un numero ridotto di colori primari e secondari.
*  I colori del tema del portale di Azure sono il bianco e il nero. Evitare di usare questi colori per lo sfondo del logo. Usare un colore che faccia risaltare il logo nel portale di Azure. Si consiglia di usare colori primari semplici. Se si usa uno sfondo trasparente, verificare che il logo e il testo non siano di colore bianco, nero o blu.
*  Non usare uno sfondo sfumato sul logo.
*  Non inserire testo nel logo, neanche il nome del marchio o della società. L'aspetto del logo deve essere semplice e senza sfumature.
*  Non estendere il logo.


#### <a name="hero-logo"></a>Logo alto

Il logo alto è facoltativo.

>[!Important]
>Una volta caricato, il logo del banner non può essere eliminato.

Usare le linee guida seguenti per il logo di un banner: 

*  Sfondi neri, bianchi e trasparenti non sono consentiti.
*  Evitare di usare un colore chiaro per lo sfondo del logo.  Il nome visualizzato dell'editore, il titolo del piano e il riepilogo lungo dell'offerta sono visualizzati con il testo bianco e devono risaltare sullo sfondo.
*  Evitare di usare troppo testo durante la progettazione del logo. Il nome dell'editore, il titolo del piano, il riepilogo lungo dell'offerta e un pulsante Crea vengono incorporati a livello di codice all'interno del logo quando l'offerta viene presentata. 
* Includere uno spazio rettangolare inutilizzato sul lato destro del logo del banner. Questo spazio vuoto è di 415x100 pixel, scostato di 370 pixel a sinistra.  

<!-- P2: woudl be nice to also have an picture int he context of iot edge of the hero image
As an example, the following hero icon is for the Azure Container Service.

![Example hero icon for Azure Container Service](./media/publishvm_010.png)
-->


## <a name="lead-management"></a>Lead management (Gestione di clienti potenziali)

Questa sezione consente di configurare le opzioni per la raccolta dei clienti potenziali generati dalle offerte di Azure Marketplace. È possibile selezionare le opzioni di archiviazione seguenti da un elenco a discesa.

* **Nessuno**: impostazione predefinita, non vengono raccolte informazioni sui clienti potenziali.
* Tabella di Azure: le informazioni vengono scritte nella tabella di Azure specificata da una stringa di connessione.
* Dynamics CRM Online: le informazioni vengono scritte nell'istanza di [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) specificata da un URL e da credenziali di autenticazione.
* HTTPS Endpoint (Endpoint HTTPS): le informazioni vengono scritte nell'endpoint HTTPS specificato come payload JSON.
* Marketo: le informazioni vengono scritte nell'istanza [Marketo](https://www.marketo.com/) specificata da ID server, ID Munchkin e ID modulo.
* Salesforce: le informazioni vengono scritte in un database [Salesforce](https://www.salesforce.com/) specificato da un identificatore di oggetto.

Dopo aver pubblicato l'offerta, verrà verificata la connessione al cliente potenziale e verrà automaticamente inviato un cliente potenziale di test alla destinazione configurata. 

>[!Note]
>Le informazioni sul cliente potenziale devono essere gestite in modo continuo e queste impostazioni devono essere aggiornate immediatamente quando si apportano modifiche all'architettura di gestione dei clienti.

<!-- there is missing some marketing imagess
1. inside azure portal the wide logo/regular logo
2. inside azure portal the top curation section
3. amp.com the pricing tab
4. amp.com the tile -->


## <a name="legal"></a>Note legali

In questa sezione consente di fornire i due documenti legali necessari per ogni offerta: informativa sulla privacy e condizioni per l'utilizzo.

|  **Campo**                    |     **Descrizione**                                                          |
|  ---------                    |     ---------------                                                          |
| **Privacy Policy URL\***      | URL dell'informativa sulla privacy pubblicata                                            |
| **Usare Constract Standard\***  | Indica se utilizzare il modello di contratto standard Microsoft.  Per altre informazioni, vedere [contratto Standard](https://docs.microsoft.com/azure/marketplace/standard-contract).   |
| **Condizioni per l'utilizzo\***            | *Le condizioni d'uso* come inline semplice codice HTML o un collegamento inviate alle condizioni per l'uso pagina     |
|  |  |


## <a name="next-steps"></a>Passaggi successivi

La scheda [Supporto](./cpp-support-tab.md) consente di specificare le risorse di supporto utente e tecniche della propria offerta.
