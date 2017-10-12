---
title: Applicazioni gestite di Azure nel Marketplace| Microsoft Docs
description: Descrive le applicazioni gestite di Azure disponibili tramite il Marketplace.
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.openlocfilehash: 58ac7665abf7e75a43bb0b92bdf6f41005c3efe8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-managed-applications-in-the-marketplace"></a>Applicazioni gestite di Azure nel marketplace

 Provider di servizi gestiti, fornitori di software indipendente e integratori di sistemi possono usare le applicazioni gestite per offrire le proprie soluzioni a tutti i clienti di Azure Marketplace. Tali soluzioni riducono l'overhead derivante da manutenzione e gestione per i clienti. Gli editori possono vendere l'infrastruttura e il software tramite il Marketplace. Possono collegare servizi e supporto operativo alle applicazioni gestite. Per altre informazioni, vedere [Panoramica delle applicazioni gestite di Azure](managed-application-overview.md).

Questo articolo illustra come un provider di servizi gestiti, un fornitore di software indipendente o un integratore di sistemi possa pubblicare un'applicazione nel Marketplace e renderla disponibile per i clienti su larga scala.

## <a name="prerequisites-for-publishing-a-managed-application"></a>Prerequisiti per la pubblicazione di un'applicazione gestita

Prerequisiti per l'inserimento nel Marketplace:

* Tecnici

    *  Per informazioni sulla struttura e la sintassi di base dei modelli di Azure Resource Manager, vedere [Modelli di Azure Resource Manager](resource-group-authoring-templates.md).
    *  Per visualizzare soluzioni di modelli complete, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/en-us/documentation/templates/) o il [repository di modelli di avvio rapido](https://github.com/azure/azure-quickstart-templates).
    *  Per informazioni sulla creazione dell'interfaccia per i clienti che distribuiscono l'applicazione tramite il Marketplace, vedere [Creare un file di definizione dell'interfaccia utente](managed-application-createuidefinition-overview.md).

* Non tecnici (requisiti aziendali)

    *   La società (o la sua affiliata) deve avere sede in un paese in cui le vendite sono supportate dal Marketplace.
    *   Il prodotto deve essere concesso in licenza in modo da essere compatibile con i modelli di fatturazione supportati dal Marketplace.
    *   Il partner è responsabile di rendere il supporto tecnico disponibile per i clienti in una modalità ragionevole dal punto di vista commerciale. Il supporto può essere gratuito, a pagamento o tramite il supporto della community.
    *   Il partner ha la responsabilità di concedere in licenza il software e le dipendenze da software di terze parti.
    *   È necessario offrire contenuti che soddisfino i criteri perché l'offerta sia inserita nel Marketplace e nel portale di Azure.
    *   È necessario accettare le condizioni delle Politiche di partecipazione a Microsoft Azure Marketplace e del Contratto per gli editori.
    *   È necessario accettare le Condizioni per l'utilizzo del sito Web di Microsoft Azure, l'Informativa sulla privacy di Microsoft e il Contratto del Programma Microsoft Azure Certified.

## <a name="create-a-new-azure-application-offer"></a>Creare una nuova offerta di applicazione Azure

Una volta soddisfatti i prerequisiti, si è pronti per creare l'offerta di applicazione gestita. Di seguito è disponibile una breve panoramica di un'offerta e di uno SKU.

### <a name="offer"></a>Offerta

L'offerta per un'applicazione gestita corrisponde a una classe di offerta di prodotti di un editore. Per rendere disponibile nel Marketplace un nuovo tipo di soluzione/applicazione, è possibile configurare una nuova offerta. Un'offerta è una raccolta di SKU. Ogni offerta viene visualizzata come entità in sé nel marketplace.

### <a name="sku"></a>SKU

Uno SKU è la più piccola unità acquistabile di un'offerta. All'interno della stessa classe di prodotti (offerta), gli SKU consentono di distinguere tra:

* le diverse funzionalità supportate
* il fatto che l'offerta sia gestita o non gestita
* i modelli di fatturazione supportati

Uno SKU viene visualizzato sotto l'offerta padre nel Marketplace e come entità acquistabile di per sé nel portale di Azure.

### <a name="set-up-an-offer"></a>Configurare un'offerta

1. Accedere al [Portale per Cloud Partner](https://cloudpartner.azure.com/).

2. Nella barra di spostamento a sinistra selezionare **+ New Offer**  (+ Nuova offerta)  > **Azure Applications** (Applicazioni Azure).

    ![Nuova offerta](./media/managed-application-author-marketplace/newOffer.png)

3. Compilare i moduli a sinistra nella vista **Editor**. I campi obbligatori sono contrassegnati con un asterisco rosso (*).

    ![Impostazioni dell'offerta](./media/managed-application-author-marketplace/newOffer_OfferSettings.png)

    Per creare un'applicazione gestita, vengono usati quattro moduli principali:

    a. Impostazioni dell'offerta

    b. SKU

    c. Marketplace

    d. Supporto

Questi moduli vengono descritti più dettagliatamente nelle sezioni seguenti.

## <a name="offer-settings-form"></a>Modulo delle impostazioni dell'offerta
Usare questo modulo di base per specificare le impostazioni dell'offerta.

1. Compilare il modulo delle **impostazioni dell'offerta**. I diversi campi sono:

    a. **Offer ID** (ID offerta): questo campo è un identificatore univoco dell'offerta in un profilo di pubblicazione. Questo ID è visibile negli URL dei prodotti, nei modelli di Resource Manager e nei report di fatturazione. Può essere composto solo da caratteri alfanumerici minuscoli o trattini (-). L'ID non può terminare con un trattino e può contenere massimo 50 caratteri. Questo campo è bloccato dopo la pubblicazione dell'offerta.

    b. **Publisher ID** (ID editore): usare questo elenco a discesa per scegliere il profilo di pubblicazione in cui si vuole pubblicare l'offerta. Questo campo è bloccato dopo la pubblicazione dell'offerta.

    c. **Name** (Nome): nome visualizzato dell'offerta nel Marketplace e nel portale. Può contenere massimo 50 caratteri. Includere un nome di marchio riconoscibile per il prodotto. Non includere il nome della società, a meno che non corrisponda al nome con cui viene commercializzato. Se si sta proponendo questa offerta sul proprio sito Web, assicurarsi che il nome corrisponda esattamente a quello con cui viene visualizzato nel sito Web.

2. Selezionare **Save** (Salva) per salvare le voci immesse. 

## <a name="skus-form"></a>Modulo degli SKU
Il passaggio successivo è quello di aggiungere gli SKU dell'offerta.

1. Selezionare **SKU** > **New SKU** (Nuovo SKU). 

    ![Selezionare il nuovo SKU](./media/managed-application-author-marketplace/newOffer_skus.png)

2. Immettere un **ID SKU**. L'ID SKU è l'identificatore univoco dello SKU in un'offerta. Questo ID è visibile negli URL dei prodotti, nei modelli di Resource Manager e nei report di fatturazione. Può essere composto solo da caratteri alfanumerici minuscoli o trattini (-). L'ID non può terminare con un trattino e può contenere massimo 50 caratteri. Questo campo è bloccato dopo la pubblicazione dell'offerta. All'interno di un'offerta possono essere presenti più SKU. È necessario uno SKU per ogni immagine che si prevede di pubblicare.

3. Compilare la sezione dei **dettagli dello SKU** nel formato seguente:

    ![Fornire il nuovo SKU](./media/managed-application-author-marketplace/newOffer_newsku.png)

    Compilare i seguenti campi:
    
    a. **Title** (Titolo): immettere un titolo per lo SKU, che viene visualizzato nella raccolta di questo elemento.

    b. **Summary (Riepilogo)**: immettere un breve riepilogo per questo SKU, che viene visualizzato sotto il titolo.

    c. **Description (Descrizione)**: immettere una descrizione dettagliata sullo SKU.

    d. **SKU Type** (Tipo di SKU): i valori consentiti sono **Managed Application** (Applicazione gestita) e **Solution Templates** (Modelli di soluzioni). In questo caso, selezionare **Managed Application** (Applicazione gestita).

4. Compilare la sezione dei **dettagli del pacchetto** nel formato seguente:

    ![Pacchetto](./media/managed-application-author-marketplace/newOffer_newsku_package.png)

    Compilare i seguenti campi:

    a. **Current Version** (Versione corrente): immettere la versione per il pacchetto caricato. Deve essere nel formato `{number}.{number}.{number}{number}`.

    b. **Select a package file** (Seleziona un file pacchetto): questo pacchetto contiene i file seguenti compressi in un file ZIP:
    * **applianceMainTemplate.json**: file modello di distribuzione usato per distribuire la soluzione/applicazione. Per altre informazioni su come creare i file modello di distribuzione, vedere [Creare il primo modello di Azure Resource Manager](resource-manager-create-first-template.md).
    * **appliancecreateUIDefinition.json**: questo file viene usato dal portale di Azure per generare l'interfaccia utente usata per il provisioning di questa soluzione/applicazione. Per altre informazioni, vedere [Introduzione a CreateUiDefinition](managed-application-createuidefinition-overview.md).
    * **mainTemplate.json**: file modello contenente solo le risorse Microsoft.Solution/appliances. Il file mainTemplate include le proprietà seguenti:

        *  **kind**: usare **Marketplace** per le applicazioni gestite nel Marketplace.
        *  **ManagedResourceGroupId**: gruppo di risorse nella sottoscrizione del cliente in cui vengono distribuite tutte le risorse definite nel file applianceMainTemplate.json.
        *  **PublisherPackageId**: stringa che identifica il pacchetto in modo univoco. Specificare il valore nel formato `{publisherId}.{OfferId}.{SKUID}.{PackageVersion}`.

Ottenere l'**ID offerta** e l'**ID editore** dal portale di pubblicazione, come illustrato nella figura seguente:

![Offer ID (ID offerta)](./media/managed-application-author-marketplace/UniqueString_pubid_offerid.png)
        
Ottenere l'**ID SKU** come illustrato nella figura seguente:

![ID SKU](./media/managed-application-author-marketplace/UniqueString_skuid.png)
        
Ottenere la **versione** del pacchetto come illustrato nella figura seguente:

![Versione del pacchetto](./media/managed-application-author-marketplace/UniqueString_packageversion.png)
    
  In base agli esempi precedenti, il valore di **PublisherPackageId** è `azureappliance-test.ravmanagedapptest.ravpreviewmanagedsku.1.0.0`.

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

Questo pacchetto deve contenere gli altri modelli annidati o gli script necessari per completare il provisioning di questa applicazione. I file mainTemplate.json, applianceMainTemplate.json e applianceCreateUIDefinition.json devono essere presenti nella cartella radice.

* **Authorizations** (Autorizzazioni): questa proprietà definisce chi ottiene l'accesso e il livello di accesso alle risorse nelle sottoscrizioni dei clienti. L'editore può gestire l'applicazione per conto del cliente.
* **PrincipalId**: questa proprietà è l'identificatore Azure Active Directory (Azure AD) di un utente, un gruppo di utenti o un'applicazione a cui vengono concesse determinate autorizzazioni per le risorse nella sottoscrizione del cliente. La definizione del ruolo descrive le autorizzazioni. 
* **Role Definition** (Definizione ruolo): questa proprietà è un elenco di tutti i ruoli di controllo degli accessi in base al ruolo forniti da Azure AD. È possibile selezionare il ruolo più appropriato da usare per gestire le risorse per conto del cliente.

È possibile aggiungere più autorizzazioni. È consigliabile creare un gruppo di utenti di AD e specificare il relativo ID in **PrincipalId**. In questo modo è possibile aggiungere più utenti al gruppo di utenti senza che sia necessario aggiornare lo SKU.

Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Introduzione al controllo degli accessi in base al ruolo nel portale di Azure](../active-directory/role-based-access-control-what-is.md).

## <a name="marketplace-form"></a>Modulo Marketplace

Il form Marketplace include i campi che vengono visualizzati in [Azure Marketplace](https://azuremarketplace.microsoft.com) e nel [portale di Azure](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Preview Subscription IDs (ID sottoscrizione di anteprima)

Immettere un elenco di ID di sottoscrizione di Azure che possono accedere all'offerta dopo la pubblicazione. È possibile usare queste sottoscrizioni consentite per testare l'offerta in anteprima prima di pubblicarla. È possibile compilare un elenco con un massimo di 100 sottoscrizioni consentite nel portale per i partner.

### <a name="suggested-categories"></a>Suggested Categories (Categorie suggerite)

Nell'elenco selezionare fino a cinque categorie a cui l'offerta può essere associata in modo ottimale. Queste categorie vengono usate per il mapping dell'offerta alle categorie di prodotti disponibili in [Azure Marketplace](https://azuremarketplace.microsoft.com) e nel [portale di Azure](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Azure Marketplace

Nel riepilogo dell'applicazione gestita vengono visualizzati i campi seguenti:

![Riepilogo del Marketplace](./media/managed-application-author-marketplace/publishvm10.png)

Nella scheda **Anteprima** per l'applicazione gestita vengono visualizzati i campi seguenti:

![Panoramica del Marketplace](./media/managed-application-author-marketplace/publishvm11.png)

Nella scheda **Piani + prezzi** per l'applicazione gestita vengono visualizzati i campi seguenti:

![Piani del Marketplace](./media/managed-application-author-marketplace/publishvm15.png)

#### <a name="azure-portal"></a>Portale di Azure

Nel riepilogo dell'applicazione gestita vengono visualizzati i campi seguenti:

![Riepilogo del portale](./media/managed-application-author-marketplace/publishvm12.png)

Nell'anteprima per l'applicazione gestita vengono visualizzati i campi seguenti:

![Panoramica del portale](./media/managed-application-author-marketplace/publishvm13.png)

#### <a name="logo-guidelines"></a>Linee guida per il logo

Seguire queste linee guida per qualsiasi logo caricato nel portale per Cloud Partner:

*   La progettazione di Azure ha una tavolozza dei colori semplice. Limitare il numero di colori primari e secondari nel logo.
*   I colori del tema del portale sono il bianco e il nero. Non usare questi colori per lo sfondo del logo. Usare un colore che faccia risaltare il logo nel portale. Si consiglia di usare colori primari semplici. *Se si usa uno sfondo trasparente, verificare che i logo e il testo non siano bianchi, neri o blu.*
*   Non usare uno sfondo sfumato sul logo.
*   Non inserire testo, nemmeno il nome del marchio o della società, sul logo. L'aspetto del logo deve essere semplice e senza sfumature.
*   Verificare che il logo non venga adattato.

#### <a name="hero-logo"></a>Logo alto

Il logo alto è facoltativo. L'editore può scegliere di non caricare un logo alto. Una volta caricata, l'icona del logo alto non può essere eliminata. A quel punto il partner deve seguire le istruzioni del Marketplace per le icone del logo alto.

Seguire queste linee guida per l'icona del logo alto:

*   Il nome visualizzato dell'editore, il titolo del piano e il riepilogo lungo dell'offerta sono visualizzati con il colore bianco. Non usare quindi un colore chiaro come sfondo dell'icona del logo alto. Lo sfondo nero, bianco o trasparente non è ammesso per le icone del logo alto.
*   Quando l'offerta viene elencata, il nome visualizzato dell'editore, il titolo del piano, il riepilogo lungo dell'offerta e il pulsante **Crea** vengono incorporati a livello di codice all'interno del logo alto. Non inserire quindi testo mentre si progetta il logo alto. Lasciare uno spazio vuoto sulla destra dove il testo viene automaticamente incluso a livello di codice. Lo spazio vuoto per il testo deve essere 415x100 pixel a destra e viene spostato con un offset di 370 pixel da sinistra.

    ![Esempio di logo alto](./media/managed-application-author-marketplace/publishvm14.png)

## <a name="support-form"></a>Modulo di supporto

Compilare il modulo **Supporto** con i contatti del supporto forniti dalla società, ad esempio le informazioni di contatto del supporto tecnico e dell'assistenza clienti.

## <a name="publish-an-offer"></a>Pubblicare un'offerta

Dopo avere completato tutte le sezioni, selezionare **Publish** (Pubblica) per avviare il processo per rendere disponibile l'offerta per i clienti.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica delle applicazioni gestite di Azure](managed-application-overview.md).
* Per informazioni sull'uso di un'applicazione gestita dal Marketplace, vedere [Consume Azure managed applications in the Marketplace](managed-application-consume-marketplace.md) (Uso delle applicazioni gestite di Azure nel Marketplace).
* Per informazioni sulla pubblicazione di un'applicazione gestita del catalogo di servizi, vedere [Creare e pubblicare un'applicazione gestita del catalogo di servizi](managed-application-publishing.md).
* Per informazioni sull'uso delle applicazioni gestite del catalogo di servizi, vedere [Utilizzare un'applicazione gestita di Azure](managed-application-consumption.md).
