---
title: Pubblicare un modello di soluzione | Microsoft Docs
description: Pubblicare un modello di soluzione in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: ded952ac6418ae3d9916b3ae8b8dbacb0c9c5a84
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807767"
---
# <a name="publish-a-solution-template-to-azure-marketplace"></a>Pubblicare un modello di soluzione in Azure Marketplace

Questo articolo illustra la procedura di pubblicazione di un'offerta del modello di soluzione in Azure Marketplace.

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti tecnici e non tecnici di seguito si applicano all'elencazione di un modello di soluzione in Azure Marketplace.

### <a name="technical"></a>Tecnici

- [Comprendere modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

- Modelli di Guida introduttiva di Azure:

    - [Documentazione dei modelli di guida introduttiva di Azure](https://azure.microsoft.com/documentation/templates/)

    - [Documentazione di guida introduttiva di Azure su GitHub](https://github.com/azure/azure-quickstart-templates)

 - [Creare un file di interfaccia utente del portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

### <a name="non-technical-business-requirements"></a>Non tecnici (requisiti aziendali)

-   La società (o la filiale) deve avere sede in uno dei paesi di origine della vendita supportati da Azure Marketplace.

-   Il prodotto deve essere concesso in licenza in modo da essere compatibile con i modelli di fatturazione supportati da Azure Marketplace.

-   L'utente ha la responsabilità di mettere a disposizione dei clienti il team di supporto in modo ragionevole da un punto di vista commerciale, sia esso gratuito, a pagamento o attraverso il supporto della community.

-   Il partner ha la responsabilità di concedere in licenza il software e le dipendenze da software di terze parti.

-   Offrire contenuti che soddisfino i criteri affinché l'offerta sia inserita su Azure Marketplace e nel portale di gestione di Azure.

-   Accettare le condizioni delle Politiche di partecipazione a Microsoft Azure Marketplace e del Contratto per gli editori.

-   Accettare le Condizioni per l'utilizzo del sito Web di Microsoft Azure, l'Informativa sulla privacy di Microsoft e il Contratto del Programma Microsoft Azure Certified.

## <a name="before-you-begin"></a>Prima di iniziare

Una volta soddisfatti tutti i prerequisiti, è possibile iniziare a creare l'offerta per il modello di soluzione. Prima di iniziare, rivedere le informazioni sulle offerte e gli SKU riportate di seguito.

**Offerta**

Un'offerta dell'applicazione Azure corrisponde a una classe di offerta di prodotti da parte di un editore. Per rendere disponibile su Azure Marketplace un nuovo tipo di soluzione/applicazione, l'approccio migliore consiste nel creare una nuova offerta. Un'offerta è una raccolta di SKU. Ogni offerta viene visualizzata come entità a sé stante in Azure Marketplace.

**SKU**

Uno SKU è la più piccola unità acquistabile di un'offerta. All'interno della stessa classe di prodotti (offerta), gli SKU consentono di distinguere tra diverse funzionalità supportate. Ad esempio, l'offerta può essere gestita o non gestita e sono supportati modelli di fatturazione diversi.

Aggiungere più SKU negli scenari seguenti:
- Si vogliono supportare modelli di fatturazione diversi, come Bring Your Own License (BYOL) o Pagamento in base al consumo (PAYG).
- Ogni SKU supporta un set di funzionalità diverso e ogni set di funzionalità ha un prezzo diverso.

Uno SKU viene visualizzato nella sezione offerta padre di Azure Marketplace e come entità acquistabile a sé stante nel portale di Azure.

## <a name="to-create-a-new-offer"></a>Per creare una nuova offerta

1.  Accedere al [Portale per Cloud Partner](http://cloudpartner.azure.com/).

2.  Nella barra di spostamento a sinistra, selezionare **+ Nuova offerta**, quindi **Applicazioni Azure**.

    ![Creare una nuova offerta](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  In **Nuova offerta**, selezionare **Editor**.

    ![Editor di una nuova offerta](./media/cloud-partner-portal-publish-managed-app/newOffer_OfferSettings.png)

4.  In **Editor** specificare le informazioni nelle viste seguenti:
    - Impostazioni dell'offerta
    - SKU
    - Marketplace
    - Supporto. Ogni vista contiene un set di campi da compilare. I campi obbligatori sono contrassegnati con un asterisco rosso (\*)

## <a name="to-configure-offer-settings"></a>Per configurare le Impostazioni dell'offerta

1. In Impostazioni dell'offerta configurare i campi **Identità dell'offerta** seguenti.

    **ID offerta**

     Un identificatore univoco dell'offerta in un profilo di pubblicazione. Questo ID è visibile negli URL dei prodotti, nei modelli ARM e nei report di fatturazione. È possibile usare solo caratteri alfanumerici minuscoli o trattini (-). L'ID non può terminare con un trattino e può contenere al massimo 50 caratteri. 
    >[!Note]
    >Questo campo viene bloccato dopo la pubblicazione dell'offerta.

    **ID editore**

    Un elenco a discesa per il profilo di pubblicazione. Scegliere il profilo con cui si vuole pubblicare l'offerta. 
    >[!Note]
    >Questo campo viene bloccato dopo la pubblicazione dell'offerta.

    **Nome**

    Il nome visualizzato dell'offerta. Questo nome viene visualizzato su Azure Marketplace e nel portale di Azure. Può contenere massimo 50 caratteri. Seguire le istruzioni seguenti al momento di scegliere il nome dell'offerta:
    -  Includere un nome di marchio riconoscibile per il prodotto. 
    - Non includere il nome della società, a meno che non corrisponda al nome con cui l'offerta viene commercializzata.
    - Se l'offerta viene commercializzata sul proprio sito Web, assicurarsi che il nome sia identico al nome del sito Web.

2. Selezionare **Salva** per completare le Impostazioni dell'offerta.
per l'offerta.

## <a name="to-create-skus"></a>Per creare gli SKU
------------------

1. Selezionare **SKU**. 

    ![Nuovo SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

    L'ID SKU è l'identificatore univoco dello SKU in un'offerta. Questo ID è visibile negli URL dei prodotti, nei modelli ARM e nei report di fatturazione. L'ID SKU:
    - Può essere composto da massimo 50 caratteri.
    - Può essere composto solo da caratteri alfanumerici minuscoli o trattini (-).
    - L'ID non può terminare con un trattino

    >[!Note]
    >Dopo aver aggiunto uno SKU, questo viene visualizzato nell'elenco degli SKU nella vista SKU. Per visualizzare i dettagli dello SKU, selezionarne il nome. 

2. Selezionare **Nuovo SKU** per immettere le informazioni visualizzate nella schermata seguente. 

    ![Dettagli SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)


### <a name="sku-settings"></a>Impostazioni SKU

Specificare le impostazioni SKU seguenti.

- **Titolo**: il titolo dello SKU. Questo titolo viene visualizzato nella raccolta di questo elemento.

- **Riepilogo**: una breve descrizione di riepilogo dello SKU. (La lunghezza massima consentita è di 100 caratteri.)

- **Descrizione**: descrizione dettagliata dello SKU.

- **Tipo di SKU**: un elenco a discesa con i valori seguenti: "Applicazione gestita (anteprima)" e "Modello di soluzione". Per questo scenario, selezionare **Modello di soluzione**.

- **Disponibilità del Cloud**: la posizione dello SKU. Il valore predefinito è Azure pubblico.

### <a name="package-details"></a>Dettagli del pacchetto

Dopo aver completato le impostazioni dello SKU, fornire i dettagli del pacchetto seguenti.

![Dettagli del pacchetto](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_ST_package.png)

- **Versione corrente**: la versione del pacchetto che verrà caricato. Deve essere nel formato seguente:

- **File pacchetto**: pacchetto che contiene i file seguenti, salvati in un file ZIP.

    -   applianceMainTemplate.json: file del modello di distribuzione usato per distribuire la soluzione/applicazione e creare le risorse definite per la soluzione. Per altre informazioni, consultare [come creare i file del modello di distribuzione](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)

    -   createUIDefinition.json: questo file viene usato dal portale di Azure per generare l'interfaccia utente per il provisioning di questa soluzione/applicazione. Per altre informazioni, consultare [Creare l'interfaccia utente del portale di Azure per l'applicazione gestita](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

    >[!IMPORTANT]
    >Questo pacchetto deve contenere gli altri modelli annidati o gli script necessari per completare il provisioning di questa applicazione. Il mainTemplate.json e createUIDefinition.json devono trovarsi nella cartella radice.

## <a name="to-configure-the-marketplace"></a>Per configurare il Marketplace

Usare la vista Marketplace per configurare i campi visualizzati per l'offerta in [Azure Marketplace](https://azuremarketplace.microsoft.com) e nel [portale di Azure](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Preview Subscription Ids (ID sottoscrizione di anteprima)

Elenco di ID di sottoscrizione di Azure che devono avere accesso all'offerta dopo la sua pubblicazione. Queste sottoscrizioni consentite permettono di testare l'offerta in anteprima prima di pubblicarla. Il portale per i partner permette di inserire nell'elenco elementi consentiti fino a 100 sottoscrizioni.

### <a name="suggested-categories"></a>Suggested Categories (Categorie suggerite)

Nell'elenco fornito selezionare fino a 5 categorie con cui l'offerta può essere associata in modo ottimale. Le categorie selezionate saranno usate per il mapping dell'offerta alle categorie di prodotti disponibili in [Azure Marketplace](https://azuremarketplace.microsoft.com) e nel [portale di Azure](https://portal.azure.com/).

Gli esempi seguenti mostrano informazioni sul marketplace in Azure Marketplace e nel portale di Azure.

**Azure Marketplace**

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)


**Portale di Azure**


![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)


![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)


### <a name="logo-guidelines"></a>Linee guida per il logo

Seguire queste linee guida per qualsiasi logo caricato nel portale Cloud Partner:

-   La progettazione di Azure ha una tavolozza dei colori semplice. Nel logo usare un numero ridotto di colori primari e secondari.

-   I colori del tema del portale di Azure sono il bianco e il nero. Evitare di usare questi colori per lo sfondo del logo. Usare un colore che faccia risaltare il logo nel portale di Azure. Si consiglia di usare colori primari semplici.

    >[!Note] 
    >Se si usa uno sfondo trasparente, verificare che il logo e il testo non siano di colore bianco, nero o blu.

-   Non usare uno sfondo sfumato sul logo.

-   Evitare di inserire testo sul logo. Incluso il nome della società o del marchio. L'aspetto del logo deve essere *semplice* e senza sfumature.

-   Il logo non deve essere allungato.

#### <a name="hero-logo"></a>Logo alto

Il logo alto è facoltativo. L'autore può scegliere di non caricare un logo alto. Tuttavia, una volta caricato, il logo non può essere eliminato. Il partner deve seguire le istruzioni di Azure Marketplace per le icone del logo alto.

#### <a name="guidelines-for-the-hero-logo-icon"></a>Linee guida per l'icona del logo alto

-   Il nome visualizzato dell'editore, il titolo del piano e il riepilogo lungo dell'offerta sono visualizzati con il colore carattere bianco. Evitare di usare un colore chiaro per lo sfondo. Sfondi neri, bianchi e trasparenti non sono consentiti per le icone del logo alto.

-   Il nome visualizzato dell'editore, il titolo del piano, il riepilogo lungo dell'offerta e il pulsante Crea vengono incorporati in modo programmatico all'interno del logo alto quando l'offerta viene quotata. Non inserire alcun testo durante la progettazione del logo alto. Lasciare uno spazio vuoto a destra del logo. Questo spazio deve avere dimensioni di 415 x 100 pixel ed essere spostato con un offset di 370 px da sinistra.

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

## <a name="to-configure-support"></a>Per configurare Assistenza

Usare la vista Assistenza per specificare le informazioni seguenti:

- Contatti del servizio di assistenza dell'azienda, ad esempio il reparto progettazione.
- Contatti dell'assistenza clienti.

## <a name="to-publish-the-offer"></a>Per pubblicare l'offerta

Il passaggio finale consiste nella pubblicazione dell'offerta. Selezionare **Pubblica**.
