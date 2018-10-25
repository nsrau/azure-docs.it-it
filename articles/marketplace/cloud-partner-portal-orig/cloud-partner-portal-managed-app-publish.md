---
title: Pubblicare un'applicazione gestita di Azure in Azure Marketplace
description: Pubblicare un'applicazione gestita di Azure in Azure Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: bc044c8b59c939163336ecab01546fc26a7a2643
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808027"
---
<a name="publish-an-azure-managed-application-to-azure-marketplace"></a>Pubblicare un'applicazione gestita di Azure in Azure Marketplace 
========================================================

Questo articolo elenca i vari passaggi necessari per la pubblicazione di un'offerta di applicazione gestita in Azure Marketplace.

<a name="pre-requisites-for-publishing-a-managed-application"></a>Prerequisiti per la pubblicazione di un'applicazione gestita 
---------------------------------------------------

Prerequisiti per l'inserimento in Azure Marketplace

1.  Tecnici

    -   [Creazione di modelli di Gestione risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

    -   Modelli di Guida introduttiva di Azure:

        -   <https://azure.microsoft.com/documentation/templates/>

        -   <https://github.com/azure/azure-quickstart-templates>

    -   Creare la definizione dell'interfaccia utente

        -   [Creare il file di definizione dell'interfaccia utente](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

2.  Non tecnici (requisiti aziendali)

    -   La società (o la sua filiale) deve avere sede in uno dei paesi di origine della vendita supportati da Azure Marketplace

    -   Il prodotto deve essere concesso in licenza in modo da essere compatibile con i modelli di fatturazione supportati da Azure Marketplace

    -   La società ha la responsabilità di offrire supporto tecnico ai clienti: gratuito, a pagamento o tramite la community

    -   La società ha la responsabilità di concedere in licenza il software e le dipendenze da software di terze parti

    -   È necessario offrire contenuti che soddisfino i criteri affinché l'offerta sia inserita in Azure Marketplace e nel portale di gestione di Azure

    -   È necessario accettare le condizioni delle Politiche di partecipazione a Microsoft Azure Marketplace e del Contratto per gli editori

    -   È necessario accettare le Condizioni per l'utilizzo, l'Informativa sulla privacy di Microsoft e il Contratto del Programma Microsoft Azure Certified

<a name="how-to-create-a-new-azure-application-offer"></a>Come creare una nuova offerta di applicazione Azure 
-------------------------------------------

Dopo avere soddisfatto i prerequisiti, è possibile iniziare a creare l'offerta dell'applicazione gestita. Prima di iniziare, ecco una breve panoramica di un'offerta e di uno SKU

**Offerta**

Un'offerta dell'applicazione Azure corrisponde a una classe di offerta di prodotti da parte di un editore. Se si vuole pubblicare una soluzione/applicazione nuova in Azure Marketplace, una nuova offerta è la scelta giusta. Un'offerta è una raccolta di SKU. Ogni offerta viene visualizzata come entità a sé stante in Azure Marketplace.

**SKU**

Uno SKU è un'offerta con la più piccola unità acquistabile. All'interno della stessa classe di prodotti (offerta), gli SKU consentono di distinguere le diverse funzionalità supportate, se l'offerta è gestita o non è gestita e i modelli di fatturazione.

È possibile aggiungere più SKU se si vuole supportare modelli di fatturazione diversi, ad esempio Bring Your Own License (BYOL), Pagamento in base al consumo, e così via. 

È possibile aggiungere più SKU quando ogni SKU supporta un set di funzionalità diverso e per ognuno di essi il prezzo cambia.

Uno SKU viene visualizzato sotto l'offerta padre in Azure Marketplace, mentre viene visualizzato come entità acquistabile a sé stante nel sito azure.com.

1.  Accedere al [Portale per Cloud Partner](http://cloudpartner.azure.com).

2.  Nella barra di spostamento a sinistra scegliere \"+ New Offer\" (+ Nuova offerta) e selezionare \"Azure Applications\" (Applicazioni Azure).

    ![Nuova offerta](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  Si aprirà la vista \"Editor\" della nuova offerta in cui è possibile iniziare la creazione.

4.  I \"moduli\" da compilare sono a sinistra nella vista all'interno della vista \"Editor\". Ogni \"modulo\" è costituito da un set di campi da compilare. I campi obbligatori sono contrassegnati con un asterisco rosso (\*).

    > I moduli principali per creare un'applicazione gestita sono quattro

    -   Impostazioni dell'offerta

    -   SKU

    -   Marketplace

    -   Supporto

<a name="how-to-fill-out-the-offer-settings-form"></a>Come compilare il modulo delle impostazioni dell'offerta 
---------------------------------------

Il modulo delle impostazioni dell'offerta è un modulo di base in cui specificare le impostazioni di offerta.
I diversi campi sono descritti di seguito.

**ID offerta**

Un identificatore univoco dell'offerta in un profilo di pubblicazione.
Questo ID sarà visibile negli URL dei prodotti, nei modelli di Resource Manager e nei report di fatturazione. Sono consentiti solo caratteri alfanumerici minuscoli o trattini (-). L'ID non può terminare con un trattino e può contenere massimo 50 caratteri. Questo campo è bloccato dopo la pubblicazione dell'offerta.

**ID editore**

Questo elenco a discesa consente di scegliere il profilo di pubblicazione in cui si desidera pubblicare l'offerta. Questo campo è bloccato dopo la pubblicazione dell'offerta.

**Nome**

Il nome visualizzato dell'offerta. Il nome che verrà visualizzato in Azure Marketplace e nel portale di Azure. Può contenere massimo 50 caratteri. Le linee guida prevedono qui l'inclusione di un nome di marchio riconoscibile per il prodotto. Non includere il nome della società, a meno che non corrisponda al nome con cui viene commercializzato. Se si sta proponendo questa offerta sul proprio sito Web, assicurarsi che il nome corrisponda esattamente a quello con cui viene visualizzato nel sito Web.

Fare clic su \"Save\" (Salva) per salvare le voci immesse. Il passaggio successivo sarà quello di aggiungere gli SKU dell'offerta.

<a name="how-to-create-skus"></a>Come creare gli SKU 
------------------

Fare clic sul modulo \"SKUs\" (SKU). Qui è disponibile un'opzione \"Add a SKU\" (Aggiungi uno SKU) che, se selezionata, consente di immettere l'\"ID dello SKU\".

![SKU della nuova offerta](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

L'\"ID SKU\" è l'identificatore univoco dello SKU in un'offerta. Questo ID sarà visibile negli URL dei prodotti, nei modelli ARM e nei report di fatturazione. Può essere composto solo da caratteri alfanumerici minuscoli o trattini (-).
Non può terminare con un trattino e può contenere massimo 50 caratteri. Questo campo è bloccato dopo la pubblicazione dell'offerta. All'interno di un'offerta possono essere presenti più SKU. È necessario uno SKU per ogni immagine che si prevede di pubblicare.

Dopo essere stato aggiunto, lo SKU verrà visualizzato nell'elenco di SKU all'interno del modulo \"SKUs\" (SKU). Fare clic sul nome dello SKU per i dettagli del determinato SKU. Ecco i dettagli relativi ad alcuni campi.

Dopo avere selezionato \"New SKU\" (Nuovo SKU), sarà necessario compilare il modulo seguente:

![Nuova offerta: nuovo SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)

### <a name="how-to-fill-sku-details-section"></a>Come compilare la sezione dei dettagli dello SKU 

**Title** (Titolo): immettere un titolo per lo SKU, che viene visualizzato nella raccolta di questo elemento.

**Summary** (Riepilogo): immettere un breve riepilogo per questo SKU, che viene visualizzato sotto il titolo.

**Description** (Descrizione): immettere una descrizione dettagliata sullo SKU.

**Sku Type** (Tipo di SKU): i valori consentiti sono \"Managed Application\" (Applicazione gestita) e \"Solution Templates\" (Modelli di soluzioni). In questo caso, selezionare \"Managed Application\" (Applicazione gestita).

### <a name="how-to-fill-package-details-section"></a>Come compilare la sezione dei dettagli del pacchetto 

La sezione del pacchetto include i campi seguenti che è necessario compilare

![Nuova offerta: nuovo pacchetto SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_package.png)

**Current version** (Versione corrente): immettere la versione del pacchetto che sarà caricato,
che deve avere un formato specifico.

**Package File** (File pacchetto): il pacchetto contiene i file seguenti compressi in un file ZIP.

applianceMainTemplate.json: il file modello di distribuzione usato per distribuire la soluzione/applicazione e creare le risorse definite nella soluzione. Per altri dettagli sulla creazione dei file modello di distribuzione, vedere <https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template>

applianceCreateUIDefinition.json: questo file viene usato dal portale (azure.com) per generare l'interfaccia utente per il provisioning di questa soluzione/applicazione. Per altri dettagli sulla creazione di questo file, vedere <https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview>.

mainTemplate.json: il file modello contenente solo le risorse Microsoft.Solution/appliances. Le proprietà chiave di questa risorsa da tenere presenti sono le seguenti:

-   \"kind\": il valore deve essere \"Marketplace\" in caso di scenario di applicazione gestita di Marketplace
-   \"ManagedResourceGroupId\": il gruppo di risorse nella sottoscrizione del cliente\' in cui vengono distribuite tutte le risorse definite nel file applianceMainTemplate.json.
-   \"PublisherPackageId\": la stringa che identifica il pacchetto in modo univoco. 

Il valore deve essere costruito nel modo seguente. Si tratta di una concatenazione di \[publisherId\].\[OfferId\]-preview\[SKUID\].\[PackageVersion\].
Ogni valore deve essere ottenuto come illustrato di seguito.

Questo pacchetto deve contenere tutti gli altri modelli annidati o gli script necessari per completare il provisioning di questa applicazione. I file mainTemplate.json, applianceMainTemplate.json e applianceCreateUIDefinition.json devono essere presenti nella cartella radice.

**Autorizzazioni**: questa proprietà definisce chi ottiene l'accesso e il livello di accesso alle risorse nelle sottoscrizioni dei clienti. Consente all'editore di gestire l'applicazione per conto del cliente.

-   PrincipalId: l'identificatore Azure Active Directory di un utente, un gruppo utenti o un'applicazione a cui vengono concesse determinate autorizzazioni (come indicato dalla definizione del ruolo) per le risorse nella sottoscrizione del cliente.

-   Role Definition: un elenco di tutti i ruoli Controllo degli accessi in base al ruolo fornito da Azure AD. È possibile selezionare il ruolo più appropriato con il quale sarà possibile gestire le risorse per conto del cliente.

È possibile aggiungere più autorizzazioni. È tuttavia consigliabile creare un gruppo utenti di Active Directory e specificarne l'ID in \"PrincipalId\..  Consentirà di aggiungere altri utenti al gruppo utenti senza dover aggiornare lo SKU.

Per altri dettagli sui ruoli Controllo degli accessi in base al ruolo, vedere <https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is>

<a name="marketplace-form"></a>Modulo Marketplace
----------------

Il modulo Marketplace all'interno di un'offerta di applicazione Azure richiede la compilazione di campi che verranno visualizzati in [Azure Marketplace](https://azuremarketplace.microsoft.com) e nel [portale di Azure](https://portal.azure.com/). Ecco i dettagli relativi ad alcuni campi.

#### <a name="preview-subscription-ids"></a>Preview Subscription IDs (ID sottoscrizione di anteprima)

Immettere qui un elenco di ID di sottoscrizione di Azure che devono avere accesso all'offerta dopo la sua pubblicazione. Queste sottoscrizioni consentite permetteranno di testare l'offerta in anteprima prima di pubblicarla. Il portale Cloud Partner consente di inserire nell'elenco degli elementi consentiti fino a 100 sottoscrizioni.

#### <a name="suggested-categories"></a>Suggested Categories (Categorie suggerite)

Nell'elenco fornito selezionare fino a cinque categorie con cui l'offerta può essere associata in modo ottimale. Le categorie selezionate saranno usate per il mapping dell'offerta alle categorie di prodotti disponibili in [Azure Marketplace](https://azuremarketplace.microsoft.com) e nel [portale di Azure](https://portal.azure.com/).

Ecco alcune delle posizioni in cui vengono visualizzati i dati forniti in questo modulo.

##### <a name="azure-marketplace"></a>Azure Marketplace

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)

![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)

![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)

##### <a name="portal-at-azurecom"></a>Portale (azure.com)

![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)

![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)

##### <a name="logo-guidelines"></a>Linee guida per il logo

Ogni logo caricato nel portale per Cloud Partner deve rispettare le seguenti linee guida:

-   La progettazione di Azure ha una tavolozza dei colori semplice. Nel logo usare un numero ridotto di colori primari e secondari.

-   I colori del tema del portale (azure.com) sono il bianco e il nero. Evitare di usare questi colori per lo sfondo del logo. Usare un colore che faccia risaltare i logo nel portale (azure.com).
    Si consiglia di usare colori primari semplici. **Se si usa lo sfondo trasparente, verificare che i logo e il testo non siano bianchi, neri o blu.**

-   Non usare uno sfondo sfumato sul logo.

-   Non inserire testo, neppure il nome del marchio o della società, nel logo.
    L'aspetto del logo deve essere \'semplice\' e senza sfumature.

-   Evitare di adattare il logo modificandone le dimensioni.

##### <a name="hero-logo"></a>Logo alto

Il logo alto è facoltativo. L'autore può scegliere di non caricare un logo alto. Tuttavia, una volta caricata, l'icona del logo alto non può essere eliminata. In tal caso, il partner deve seguire le istruzioni di Azure Marketplace per le icone banner.

###### <a name="guidelines-for-the-hero-logo-icon"></a>Linee guida per l'icona del logo alto

-   Il nome visualizzato dell'autore, il titolo del piano e il riepilogo lungo dell'offerta sono visualizzati con il testo bianco. Non è consigliabile usare un colore chiaro come sfondo dell'icona banner. Sfondi neri, bianchi e trasparenti non sono consentiti per le icone banner.

-   Quando l'offerta si trova nell'elenco, il nome visualizzato dell'editore, il titolo del piano, il riepilogo lungo dell'offerta e il pulsante di creazione vengono incorporati a livello di codice all'interno del logo banner. Non è necessario inserire testo mentre si progetta il logo banner. Lasciare spazi vuoti a destra per il nome visualizzato dell'editore, il titolo del piano, il riepilogo lungo dell'offerta e così via. Vengono inclusi a livello di codice.
    Gli spazi vuoti per il testo devono essere 415x100 a destra e vengono spostati con un offset di 370 px da sinistra.

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

<a name="support-form"></a>Modulo del supporto
------------

Il modulo successivo da compilare è quello del supporto. In questo modulo devono essere inseriti i contatti aziendali per il supporto,  ad esempio i contatti del supporto tecnico e i contatti dell'assistenza clienti.

<a name="how-to-publish-an-offer"></a>Come pubblicare un'offerta
-----------------------

Ora che la bozza dell'offerta è pronta, il passaggio successivo sarà pubblicarla in Azure Marketplace. Fare clic sul pulsante \"Publish\" (Pubblica) per attivare l'offerta.
