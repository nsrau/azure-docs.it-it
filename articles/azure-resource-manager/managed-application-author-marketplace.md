---
title: Applicazione gestita di Azure nel marketplace| Microsoft Docs
description: Descrive un'applicazione gestita di Azure disponibile tramite il marketplace.
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: bd7880bdbca8cbf1abcab2cbade050876e26aea1
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="azure-managed-applications-in-the-marketplace"></a>Applicazioni gestite di Azure nel marketplace

Come illustrato nell'articolo [Panoramica di Applicazione gestita di Azure](managed-application-overview.md), le applicazioni gestite in Microsoft Azure Marketplace consentono a provider di servizi gestiti, ISV e integratori di sistemi di offrire le proprie soluzioni a tutti i clienti di Azure. Tali soluzioni riducono l'overhead derivante da manutenzione e gestione per i clienti. Gli editori possono vendere l'infrastruttura e il software tramite il marketplace e collegarvi servizi e supporto operativo. 

Questo articolo illustra come un provider di servizi gestiti, un ISV o un integratore di sistemi possa pubblicare un'applicazione nel marketplace e renderla disponibile per i clienti su larga scala.  

## <a name="pre-requisites-for-publishing-a-managed-application"></a>Prerequisiti per la pubblicazione di un'applicazione gestita

Prerequisiti per l'inserimento nel marketplace

* Tecnici

    *  Per informazioni sulla struttura e la sintassi di base dei modelli di Resource Manager, vedere [Creare modelli di Azure Resource Manager](resource-group-authoring-templates.md).
    *  Per visualizzare soluzioni di modelli complete, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/en-us/documentation/templates/) o il [repository di modelli di avvio rapido](https://github.com/azure/azure-quickstart-templates).
    *  Per informazioni sulla creazione dell'interfaccia per i clienti che distribuiscono l'applicazione tramite il marketplace, vedere [Creare un file di definizione dell'interfaccia utente](managed-application-createuidefinition-overview.md).

* Non tecnici (requisiti aziendali)

    *   La società (o l'affiliata) deve avere sede in uno dei paesi di origine della vendita supportati dal marketplace.
    *   Il prodotto deve essere concesso in licenza in modo da essere compatibile con i modelli di fatturazione supportati dal Marketplace.
    *   L'utente è responsabile di rendere il supporto tecnico disponibile per i clienti in una modalità ragionevole dal punto di vista commerciale. Il supporto può essere gratuito, a pagamento o tramite il supporto della community.
    *   Il partner ha la responsabilità di concedere in licenza il software e le dipendenze da software di terze parti.
    *   È necessario offrire contenuti che soddisfino i criteri perché l'offerta sia inserita in Azure Marketplace e nel portale di Azure
    *   È necessario accettare le condizioni delle Politiche di partecipazione a Microsoft Azure Marketplace e del Contratto per gli editori.
    *   È necessario accettare le Condizioni per l'utilizzo del sito Web di Microsoft Azure, l'Informativa sulla privacy di Microsoft e il Contratto del Programma Microsoft Azure Certified.

## <a name="how-to-create-a-new-azure-application-offer"></a>Come creare una nuova offerta di applicazione Azure

Dopo avere soddisfatto i prerequisiti, è possibile iniziare a creare l'offerta di applicazione gestita. Di seguito è disponibile una breve panoramica di un'offerta e di uno SKU.

### <a name="offer"></a>Offerta

L'offerta per un'applicazione gestita corrisponde a una classe di offerta di prodotti di un editore. Per rendere disponibile nel marketplace un nuovo tipo di soluzione/applicazione, è possibile configurarlo come nuova offerta. Un'offerta è una raccolta di SKU. Ogni offerta viene visualizzata come entità in sé nel marketplace.

### <a name="sku"></a>SKU

Uno SKU è la più piccola unità acquistabile di un'offerta. All'interno della stessa classe di prodotti (offerta), gli SKU consentono di distinguere tra diverse funzionalità supportate, indipendentemente dal fatto che l'offerta sia gestita o non gestita e che i modelli di fatturazione siano supportati.

Uno SKU viene visualizzato sotto l'offerta padre nel marketplace e come entità acquistabile di per sé nel portale di Azure.

### <a name="set-up-offer"></a>Configurare l'offerta

1.  Accedere al [portale per Cloud Partner](https://cloudpartner.azure.com/).
2.  Nella barra di spostamento a sinistra scegliere **+ New Offer** (+ Nuova offerta) e selezionare **Azure Applications** (Applicazioni Azure).

    ![Nuova offerta](./media/managed-application-author-marketplace/newOffer.png)

3.  Viene ora aperta la vista **Editor** della nuova offerta in cui è possibile iniziare la creazione.

    ![Impostazioni dell'offerta](./media/managed-application-author-marketplace/newOffer_OfferSettings.png)

4.  I moduli da compilare sono a sinistra nella vista **Editor**. Ogni modulo è costituito da un set di campi da compilare. I campi obbligatori sono contrassegnati con un asterisco rosso (*).

 **I moduli principali per creare un'applicazione gestita sono quattro**

    *   Impostazioni dell'offerta
    *   SKU
    *   Marketplace
    *   Supporto

Questi moduli vengono descritti più dettagliatamente nelle sezioni seguenti.

## <a name="offer-settings-form"></a>Modulo delle impostazioni dell'offerta

Il modulo delle impostazioni dell'offerta è un modulo di base in cui specificare le impostazioni di offerta. I diversi campi sono:

* Offer ID (ID offerta): questo campo è un identificatore univoco dell'offerta in un profilo di pubblicazione. Questo ID è visibile negli URL dei prodotti, nei modelli di Resource Manager e nei report di fatturazione. Può essere composto solo da caratteri alfanumerici minuscoli o trattini (-). L'ID non può terminare con un trattino e può contenere massimo 50 caratteri. Questo campo è bloccato dopo la pubblicazione dell'offerta.
* Publisher ID (ID editore): questo elenco a discesa consente di scegliere il profilo di pubblicazione in cui si vuole pubblicare l'offerta. Questo campo è bloccato dopo la pubblicazione dell'offerta.
* Name (Nome): nome visualizzato dell'offerta. Questo nome viene visualizzato nel marketplace e nel portale. Può contenere massimo 50 caratteri. Le linee guida prevedono qui l'inclusione di un nome di marchio riconoscibile per il prodotto. Non includere il nome della società, a meno che non corrisponda al nome con cui viene commercializzato. Se si sta proponendo questa offerta sul proprio sito Web, assicurarsi che il nome corrisponda esattamente a quello con cui viene visualizzato nel sito Web.

Selezionare **Save** (Salva) per salvare le voci immesse. Il passaggio successivo è quello di aggiungere gli SKU dell'offerta.

## <a name="skus-form"></a>Modulo degli SKU

Selezionare il modulo **SKUs** (SKU) in cui è disponibile l'opzione **New SKU** (Nuovo SKU). Selezionando questa opzione, è possibile immettere un **ID SKU**.

![Selezionare il nuovo SKU](./media/managed-application-author-marketplace/newOffer_skus.png)

L'**ID SKU** è l'identificatore univoco dello SKU in un'offerta. Questo ID è visibile negli URL dei prodotti, nei modelli di Resource Manager e nei report di fatturazione. Può essere composto solo da caratteri alfanumerici minuscoli o trattini (-). L'ID non può terminare con un trattino e può contenere massimo 50 caratteri. Questo campo è bloccato dopo la pubblicazione dell'offerta. All'interno di un'offerta possono essere presenti più SKU. È necessario uno SKU per ogni immagine che si prevede di pubblicare.

Dopo avere selezionato **New SKU** (Nuovo SKU), sarà necessario compilare il modulo seguente:

![Fornire il nuovo SKU](./media/managed-application-author-marketplace/newOffer_newsku.png)

### <a name="how-to-fill-sku-details-section"></a>Come compilare la sezione dei dettagli dello SKU

* Title (Titolo): immettere un titolo per lo SKU, che viene visualizzato nella raccolta di questo elemento.
* Summary (Riepilogo): immettere un breve riepilogo per questo SKU, che viene visualizzato sotto il titolo.
* Description (Descrizione): immettere una descrizione dettagliata sullo SKU.
* SKU Type (Tipo di SKU): i valori consentiti sono **Managed Application** (Applicazione gestita) e **Solution Templates** (Modelli di soluzioni). In questo caso, selezionare **Managed Application** (Applicazione gestita).

### <a name="how-to-fill-package-details-section"></a>Come compilare la sezione dei dettagli del pacchetto

La sezione del pacchetto include i campi seguenti che è necessario compilare

![Pacchetto](./media/managed-application-author-marketplace/newOffer_newsku_package.png)

**Current version** (Versione corrente): immettere la versione per il pacchetto caricato. Deve essere nel formato `{number}.{number}.{number}{number}`

**Package File** (File pacchetto): questo pacchetto contiene i file seguenti compressi in un file ZIP.

* **applianceMainTemplate.json**: file modello di distribuzione usato per distribuire la soluzione/applicazione. Per altre informazioni su come creare i file modello di distribuzione, vedere [Creare il primo modello di Azure Resource Manager](resource-manager-create-first-template.md)
* **appliancecreateUIDefinition.json**: questo file viene usato dal portale di Azure per generare l'interfaccia utente per il provisioning di questa soluzione/applicazione. Per altre informazioni, vedere [Introduzione a CreateUiDefinition](managed-application-createuidefinition-overview.md).
* **mainTemplate.json**: file modello contenente solo le risorse Microsoft.Solution/appliances. Le proprietà chiave di questa risorsa da tenere presenti sono le seguenti:

mainTemplate include le proprietà seguenti:

*  kind: usare **Marketplace** per le applicazioni gestite nel marketplace
*  ManagedResourceGroupId: gruppo di risorse nella sottoscrizione del cliente in cui vengono distribuite tutte le risorse definite nel file applianceMainTemplate.json.
*  PublisherPackageId: stringa che identifica il pacchetto in modo univoco. Specificare il valore nel formato `{publisherId}.{OfferId}.{SKUID}.{PackageVersion}`.
  È possibile ottenere publisherId e OfferId dal portale di pubblicazione.

  ![ID offerta](./media/managed-application-author-marketplace/UniqueString_pubid_offerid.png)
        
  È possibile ottenere l'ID SKU come illustrato nella figura seguente:

  ![ID SKU](./media/managed-application-author-marketplace/UniqueString_skuid.png)
        
  È possibile ottenere la versione del pacchetto come illustrato nella figura seguente:

  ![Versione del pacchetto](./media/managed-application-author-marketplace/UniqueString_packageversion.png)
    
  Usando gli esempi precedenti, il valore di **PublisherPackageId** è quindi `azureappliance-test.ravmanagedapptest.ravpreviewmanagedsku.1.0.0`

  mainTemplate.json di esempio:

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "storageAccountNamePrefix": {
        "type": "string",
        "metadata": {
          "description": "Specify the name of the storage account"
        }
      },
      "storageAccountType": {
        "type": "string"
      }
    },
    "variables": {
      "managedResourceGroup": "[concat(resourceGroup().id,uniquestring(resourceGroup().id))]"
    },
    "resources": [{
      "type": "Microsoft.Solutions/appliances",
      "apiVersion": "2016-09-01-preview",
      "name": "[concat(parameters('storageAccountNamePrefix'), '-', 'managed')]",
      "location": "[resourceGroup().location]",
      "kind": "marketplace",
      "properties": {
        "managedResourceGroupId": "[variables('managedResourceGroup')]",
        "PublisherPackageId":"azureappliancetest.ravmanagedapptest.ravpreviewmanagedsku.1.0.0",
        "parameters": {
          "storageAccountName": {
            "value": "[parameters('storageAccountNamePrefix')]"
          },
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }],
    "outputs": {

    }
  }
  ```

Questo pacchetto deve contenere gli altri modelli annidati o gli script necessari per completare il provisioning di questa applicazione. mainTemplate.json, applianceMainTemplate.json e applianceCreateUIDefinition.json devono essere presenti nella cartella radice.

**Authorizations** (Autorizzazioni): questa proprietà definisce chi ottiene l'accesso e il livello di accesso alle risorse nelle sottoscrizioni dei clienti. Consente all'editore di gestire l'applicazione per conto del cliente.

* PrincipalId: questa proprietà è l'identificatore Azure Active Directory di un utente, un gruppo di utenti o un'applicazione a cui vengono concesse determinate autorizzazioni (come indicato dalla definizione del ruolo) per le risorse nella sottoscrizione del cliente.
* Role Definition (Definizione ruolo): questa proprietà è un elenco di tutti i ruoli Controllo degli accessi in base al ruolo forniti da Azure AD. È possibile selezionare il ruolo più appropriato per poter gestire le risorse per conto del cliente.

È possibile aggiungere più autorizzazioni. È tuttavia consigliabile creare un gruppo di utenti di Active Directory e specificarne l'ID in **PrincipalId**. Consente di aggiungere altri utenti al gruppo di utenti senza dover aggiornare lo SKU.

Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Introduzione al controllo degli accessi in base al ruolo nel portale di Azure](../active-directory/role-based-access-control-what-is.md).

## <a name="marketplace-form"></a>Modulo Marketplace

Il form Marketplace include i campi che vengono visualizzati in [Azure Marketplace](https://azuremarketplace.microsoft.com) e nel [portale di Azure](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Preview Subscription IDs (ID sottoscrizione di anteprima)

Immettere qui un elenco di ID di sottoscrizione di Azure che devono avere accesso all'offerta dopo la sua pubblicazione. Queste sottoscrizioni consentite permettono di testare l'offerta in anteprima prima di pubblicarla. Il portale per i partner permette fino a 100 sottoscrizioni consentite.

### <a name="suggested-categories"></a>Suggested Categories (Categorie suggerite)

Nell'elenco fornito selezionare fino a cinque categorie con cui l'offerta può essere associata in modo ottimale. Le categorie selezionate vengono usate per il mapping dell'offerta alle categorie di prodotti disponibili in [Azure Marketplace](https://azuremarketplace.microsoft.com) e nel [portale](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Azure Marketplace

Nel riepilogo dell'applicazione gestita vengono visualizzati i campi seguenti:

![Riepilogo del Marketplace](./media/managed-application-author-marketplace/publishvm10.png)

Nella panoramica dell'applicazione gestita vengono visualizzati i campi seguenti:

![Panoramica del marketplace](./media/managed-application-author-marketplace/publishvm11.png)

Nei piani e nei prezzi dell'applicazione gestita vengono visualizzati i campi seguenti:

![Piani del marketplace](./media/managed-application-author-marketplace/publishvm15.png)

#### <a name="azure-portal"></a>Portale di Azure

Nel riepilogo dell'applicazione gestita vengono visualizzati i campi seguenti:

![Riepilogo del portale](./media/managed-application-author-marketplace/publishvm12.png)

Nella panoramica dell'applicazione gestita vengono visualizzati i campi seguenti:

![Panoramica del portale](./media/managed-application-author-marketplace/publishvm13.png)

#### <a name="logo-guidelines"></a>Linee guida per il logo

Usare le linee guida seguenti per ogni logo caricato nel portale per Cloud Partner:

*   La progettazione di Azure ha una tavolozza dei colori semplice. Nel logo usare un numero ridotto di colori primari e secondari.
*   I colori del tema del portale sono il bianco e il nero. Pertanto evitare di usare questi colori per lo sfondo dei logo. Usare un colore che faccia risaltare i logo nel portale. Si consiglia di usare colori primari semplici. **Se si usa lo sfondo trasparente, verificare che i logo e il testo non siano bianchi, neri o blu.**
*   Non usare uno sfondo sfumato sul logo.
*   Evitare di inserire testo, anche il nome del marchio o della società, sul logo. L'aspetto del logo deve essere semplice e senza sfumature.
*   Verificare che il logo non venga adattato.

#### <a name="hero-logo"></a>Logo alto

Il logo alto è facoltativo. L'autore può scegliere di non caricare un logo alto. Tuttavia, una volta caricata, l'icona del logo alto non può essere eliminata. A quel punto il partner deve seguire le istruzioni del marketplace per le icone del logo alto.

##### <a name="guidelines-for-the-hero-logo-icon"></a>Linee guida per l'icona del logo alto

*   Il nome visualizzato dell'autore, il titolo del piano e il riepilogo lungo dell'offerta sono visualizzati con il testo bianco. Di conseguenza non è consigliabile usare un colore chiaro come sfondo dell'icona del logo alto. Lo sfondo nero, bianco o trasparente non è ammesso per le icone del logo alto.
*   Il nome visualizzato dell'autore, il titolo del piano, il riepilogo lungo dell'offerta e il pulsante Crea vengono incorporati a livello di codice all'interno del logo alto quando l'offerta viene elencata. Pertanto non è necessario inserire testo mentre si progetta il logo alto. Lasciare uno spazio vuoto sulla destra dove il testo (nome visualizzato dell'editore, titolo del piano e riepilogo lungo dell'offerta) viene automaticamente incluso a livello di codice. Lo spazio vuoto per il testo deve essere 415x100 a destra e viene spostato con un offset di 370 px da sinistra.

![publishvm14](./media/managed-application-author-marketplace/publishvm14.png)

## <a name="support-form"></a>Modulo di supporto

Il modulo successivo da compilare è quello del supporto. Questo modulo richiede di specificare i contatti del supporto fornito dalla società, ad esempio le informazioni di contatto del supporto tecnico e le informazioni di contatto dell'assistenza clienti.

## <a name="how-to-publish-an-offer"></a>Come pubblicare un'offerta

Dopo avere completato tutte le sezioni, selezionare **Publish** (Pubblica) per avviare il processo per rendere disponibile l'offerta per i clienti.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](managed-application-overview.md).
* Per informazioni sull'utilizzo di un'applicazione gestita dal marketplace, vedere [Consume Azure managed applications in the Marketplace (Utilizzare le applicazioni gestite di Azure nel marketplace)](managed-application-consume-marketplace.md).
* Per informazioni sulla pubblicazione di un'applicazione gestita del catalogo di servizi, vedere [Creare e pubblicare un'applicazione gestita del catalogo di servizi](managed-application-publishing.md).
* Per informazioni sull'utilizzo dell'applicazione gestita del catalogo di servizi, vedere [Utilizzare un'applicazione gestita del catalogo di servizi](managed-application-consumption.md).

