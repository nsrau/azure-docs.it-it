---
title: Pubblicare un'offerta di servizi gestiti in Azure Marketplace
description: Informazioni su come pubblicare un'offerta di servizio gestito che esegue l'onboarding dei clienti nella gestione risorse delegate di Azure.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 09/19/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 4781126bc4fcfb6391db42a75553a13e0e4cc4f9
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155139"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>Pubblicare un'offerta di servizi gestiti in Azure Marketplace

Questo articolo illustra come pubblicare un'offerta di servizi gestiti pubblici o privati in [Azure Marketplace](https://azuremarketplace.microsoft.com) tramite il [portale Cloud Partner](https://cloudpartner.azure.com/), consentendo ai clienti che la acquistano di eseguire l'onboarding delle risorse per la gestione di risorse delegate di Azure.

> [!NOTE]
> È necessario avere un [account valido nel Centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) per creare e pubblicare queste offerte. Se non si ha già un account, il [processo di iscrizione](https://aka.ms/joinmarketplace) avvierà la procedura per creare un account nel Centro per i partner ed eseguire la registrazione nel programma Commercial Marketplace. L'ID MPN (Microsoft Partner Network) verrà [associato automaticamente](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started) alle offerte pubblicate per tenere traccia dell'impatto sugli engagement dei clienti.
>
> Se non si vuole pubblicare un'offerta in Azure Marketplace, è possibile eseguire l'onboarding dei clienti manualmente usando i modelli di Azure Resource Manager. Per altre informazioni, vedere [Eseguire l'onboarding di un cliente nella gestione risorse delegate di Azure](onboard-customer.md).

La pubblicazione di un'offerta di servizi gestiti è simile alla pubblicazione di qualsiasi altro tipo di offerta in Azure Marketplace. Per informazioni su tale processo, vedere [Guida alla pubblicazione per Azure Marketplace e AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) e [Gestire le offerte di Azure Marketplace e del marketplace di AppSource](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers). È anche necessario esaminare i [criteri di certificazione del Marketplace commerciale](https://docs.microsoft.com/legal/marketplace/certification-policies), in particolare la sezione [Servizi gestiti](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services).

Dopo aver aggiunto l'offerta, un cliente potrà delegare una o più sottoscrizioni o gruppi di risorse specifici, di cui verrà quindi eseguito l'[onboarding per la gestione risorse delegate di Azure](#the-customer-onboarding-process). Si noti che per poter eseguire l'onboarding di una sottoscrizione (o dei gruppi di risorse in essa contenuti), è prima di tutto necessario autorizzare la sottoscrizione per l'onboarding registrando manualmente il provider di risorse **Microsoft.ManagedServices**.

> [!IMPORTANT]
> Ogni piano in un'offerta di servizi gestiti include una sezione **Manifest Details** (Dettagli del manifesto), in cui si definiscono le entità di Azure Active Directory (Azure AD) del tenant che avranno accesso ai gruppi di risorse e/o alle sottoscrizioni delegate per i clienti che acquistano il piano. È importante tenere presente che qualsiasi gruppo (o utente o entità servizio) incluso qui avrà le stesse autorizzazioni per ogni cliente che acquista il piano. Per assegnare gruppi diversi per lavorare con ogni cliente, è necessario pubblicare un piano privato separato che sia esclusivo per ogni cliente.

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>Creare l'offerta nel portale Cloud Partner

1. Accedere al [Portale per Cloud Partner](https://cloudpartner.azure.com/).
2. Dal menu di spostamento a sinistra selezionare **Nuova offerta**, quindi selezionare **Servizi gestiti**.
3. Verrà visualizzata una sezione **Editor** per l'offerta con quattro parti da compilare: **Impostazioni dell'offerta**, **Piani**, **Marketplace** e **Supporto**. Continuare a leggere per istruzioni su come completare queste sezioni.

## <a name="enter-offer-settings"></a>Immettere le impostazioni dell'offerta

Nella sezione **Impostazioni dell'offerta** immettere le informazioni seguenti:

|Campo  |DESCRIZIONE  |
|---------|---------|
|**ID offerta**     | Un identificatore univoco dell'offerta (nel profilo di pubblicazione). Questo ID può contenere solo caratteri alfanumerici minuscoli, trattini e caratteri di sottolineatura, fino a un massimo di 50 caratteri. Tenere presente che l'ID dell'offerta può essere visibile ai clienti ad esempio negli URL dei prodotti e nei report di fatturazione. Dopo aver pubblicato l'offerta, non è possibile modificare questo valore.        |
|**ID editore**     | ID dell'editore che verrà associato all'offerta. Se sono presenti più ID editore, è possibile selezionare quello che si vuole usare per questa offerta.       |
|**Nome**     | Nome (fino a 50 caratteri) dell'offerta visualizzato dai clienti in Azure Marketplace e nel portale di Azure. Usare un nome di marchio riconoscibile che i clienti potranno comprendere. Se si promuove questa offerta tramite il proprio sito Web, assicurarsi di usare esattamente lo stesso nome.        |

Al termine, fare clic su **Salva**. A questo punto è possibile passare alla sezione **Piani**.

## <a name="create-plans"></a>Creare piani

Ogni offerta deve avere uno o più piani, detti anche SKU. È possibile aggiungere più piani per supportare set di funzionalità diversi a prezzi diversi o per personalizzare un piano specifico per un numero limitato di clienti specifici. I clienti possono visualizzare i piani disponibili nell'offerta padre.

Nella sezione Piani selezionare **Nuovo piano** per ogni piano che si vuole creare. Immettere quindi un **ID piano**. Questo ID può contenere solo caratteri alfanumerici minuscoli, trattini e caratteri di sottolineatura, fino a un massimo di 50 caratteri. L'ID del piano può essere visibile ai clienti ad esempio negli URL dei prodotti e nei report di fatturazione. Dopo aver pubblicato l'offerta, non è possibile modificare questo valore.

Completare ora le sezioni seguenti nella sezione **Dettagli piano**:

|Campo  |DESCRIZIONE  |
|---------|---------|
|**Titolo**     | Nome descrittivo del piano da visualizzare. La lunghezza massima consentita è di 50 caratteri.        |
|**Summary**     | Breve descrizione del piano da visualizzare sotto il titolo. La lunghezza massima consentita è di 100 caratteri.        |
|**Descrizione**     | Testo della descrizione con una spiegazione più dettagliata del piano.         |
|**Modello di fatturazione**     | Sono visualizzati 2 modelli di fatturazione, ma è necessario scegliere **Bring Your Own License** per le offerte di servizi gestiti. Ciò significa che i costi relativi a questa offerta verranno fatturati direttamente ai clienti e non si riceverà alcun addebito da parte di Microsoft.   |
|**Is this a private plan?** (Piano privato?)     | Indica se lo SKU è pubblico o privato. Il valore predefinito è **No** (pubblico). Se si lascia questa selezione, il piano non sarà limitato a clienti specifici (o a un certo numero di clienti). Dopo aver pubblicato un piano pubblico, non sarà possibile impostarlo in un secondo momento come privato. Per rendere questo piano disponibile solo a clienti specifici, selezionare **Sì**. Quando si esegue questa operazione, è necessario identificare i clienti fornendo gli ID sottoscrizione. Questi possono essere immessi uno alla volta (per un massimo di 10 sottoscrizioni) o caricando un file CSV (per un massimo di 20.000 sottoscrizioni). Assicurarsi di includere qui le sottoscrizioni per poter testare e convalidare l'offerta. Per altre informazioni, vedere [SKU e piani privati](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  |

Completare infine la sezione **Manifest Details** (Dettagli del manifesto). Verrà creato un manifesto con le informazioni di autorizzazione per la gestione delle risorse dei clienti. Le informazioni fornite qui sono necessarie per l'onboarding dei clienti per la gestione risorse delegate di Azure. Come indicato in precedenza, queste autorizzazioni verranno applicate a ogni cliente che acquista il piano. Se quindi si vuole limitare l'accesso a un cliente specifico, è necessario pubblicare un piano privato per uso esclusivo.

- Specificare prima di tutto una **versione** per il manifesto. Usare il formato *n.n.n* (ad esempio, 1.2.5).
- Immettere quindi l'**ID tenant**. Si tratta di un GUID associato all'ID tenant di Azure Active Directory dell'organizzazione, ad esempio il tenant che si userà per gestire le risorse dei clienti. Se non lo si ha disposizione, è possibile trovarlo passando il puntatore sul nome dell'account nell'angolo in alto a destra del portale di Azure o selezionando **Cambia directory**. 
- Aggiungere infine una o più voci **Autorizzazione** al piano. Le autorizzazioni definiscono le entità che possono accedere alle risorse e alle sottoscrizioni per i clienti che acquistano il piano. È necessario fornire queste informazioni per accedere alle risorse per conto del cliente usando la gestione risorse delegate di Azure.
  Per ogni autorizzazione, specificare quanto segue. È quindi possibile selezionare **New authorization** (Nuova autorizzazione) ogni volta che è necessario per aggiungere altri utenti o definizioni del ruolo.
  - **ID oggetto Azure AD**: identificatore Azure AD di un utente, un gruppo utenti o un'applicazione a cui vengono concesse determinate autorizzazioni (come indicato dalla definizione del ruolo) per le risorse dei clienti.
  - **Azure AD Object Display Name** (Nome visualizzato dell'oggetto Azure AD): nome descrittivo per aiutare il cliente a comprendere lo scopo di questa autorizzazione. Questo nome verrà visualizzato dal cliente durante la delega delle risorse.
  - **Role Definition** (Definizione ruolo): selezionare uno dei ruoli predefiniti di Azure AD disponibili nell'elenco. Questo ruolo determinerà le autorizzazioni che l'utente indicato nel campo **ID oggetto Azure AD** avrà per le risorse dei clienti. Per informazioni su questi ruoli, vedere [Ruoli predefiniti](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  - **Assignable Roles** (Ruoli assegnabili): questa impostazione è obbligatoria solo se per questa autorizzazione si è selezionato Amministratore Accesso utenti in **Definizione ruolo**. In tal caso, è necessario aggiungere qui uno o più ruoli assegnabili. L'utente indicato nel campo **ID oggetto Azure AD** potrà assegnare questi **ruoli assegnabili** alle [identità gestite](https://docs.microsoft.com/azure/managed-applications/publish-managed-identity). Si noti che nessun'altra autorizzazione normalmente associata al ruolo Amministratore Accesso utenti verrà applicata a questo utente. Se non si selezionano uno o più ruoli, l'invio non supererà la certificazione. Se non è stato selezionato Amministratore Accesso utenti per la definizione del ruolo di questo utente, questo campo non ha alcun effetto.

> [!TIP]
> Nella maggior parte dei casi, è consigliabile assegnare le autorizzazioni a un gruppo di utenti o a un'entità servizio di Azure AD, invece che a una serie di singoli account utente. In questo modo è possibile aggiungere o rimuovere l'accesso per i singoli utenti senza dover aggiornare e ripubblicare il piano quando cambiano i requisiti di accesso.

Dopo aver terminato di aggiungere i piani, selezionare **Salva**, quindi continuare con la sezione **Marketplace**.

## <a name="provide-marketplace-text-and-images"></a>Fornire testo e immagini del Marketplace

Nella sezione **Marketplace** si forniscono il testo e le immagini che i clienti visualizzeranno in Azure Marketplace e nel portale di Azure.

Fornire informazioni per i campi seguenti nella sezione **Panoramica**:

|Campo  |DESCRIZIONE  |
|---------|---------|
|**Titolo**     |  Titolo dell'offerta, spesso corrispondente alla forma estesa e formale del nome. Questo titolo verrà visualizzato in una posizione di estremo rilievo nel marketplace. La lunghezza massima consentita è di 50 caratteri. Nella maggior parte dei casi, corrisponderà al **nome** immesso nella sezione **Impostazioni dell'offerta**.       |
|**Summary**     | Descrizione breve dello scopo o della funzione dell'offerta. Viene in genere visualizzata sotto il titolo. La lunghezza massima consentita è di 100 caratteri.        |
|**Long Summary** (Riepilogo lungo)     | Riepilogo più lungo dello scopo o della funzione dell'offerta. La lunghezza massima consentita è di 256 caratteri.        |
|**Descrizione**     | Altre informazioni sull'offerta. Questo campo ha una lunghezza massima consentita di 3000 caratteri e supporta la formattazione HTML semplice. È necessario includere le parole "servizio gestito" o "servizi gestiti" in un punto qualsiasi della descrizione.       |
|**Marketing Identifier** (Identificatore di marketing)     | Identificatore univoco descrittivo per gli URL. Verrà usato negli URL del marketplace per l'offerta. Se ad esempio l'ID editore è *contoso* e l'identificatore di marketing è *sampleApp*, l'URL per l'offerta in Azure Marketplace sarà *https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp* .        |
|**Preview Subscription IDs** (ID sottoscrizione di anteprima)     | Aggiungere da uno a 100 identificatori di sottoscrizione. I clienti associati a queste sottoscrizioni potranno visualizzare l'offerta in Azure Marketplace prima che diventi disponibile. È consigliabile includere qui le sottoscrizioni per poter visualizzare in anteprima come viene visualizzata l'offerta in Azure Marketplace prima di renderla disponibile per i clienti.  Anche i team di supporto tecnico e di progettazione Microsoft potranno visualizzare l'offerta durante questo periodo di anteprima.   |
|**Collegamenti utili**     | URL correlati all'offerta, ad esempio la documentazione, le note sulla versione, le domande frequenti e così via.        |
|**Suggested Categories (Max 5)** (Categorie suggerite - Max 5)     | Una o più categorie (fino a cinque) applicabili all'offerta. Queste categorie aiutano i clienti a individuare l'offerta in Azure Marketplace e nel portale di Azure.        |

Nella sezione **Marketing Artifacts** (Artefatti di marketing) è possibile caricare logo e altri asset da visualizzare con l'offerta. È facoltativamente possibile caricare screenshot o collegamenti a video che consentono ai clienti di comprendere l'offerta.

Sono richieste quattro dimensioni per i logo: **Piccola (40x40)** , **Media (90x90)** , **Grande (115x115)** e **Larga (255x155)** . Seguire queste linee guida per i logo:

- La progettazione di Azure ha una tavolozza dei colori semplice. Limitare il numero di colori primari e secondari nel logo.
- I colori del tema del portale sono il bianco e il nero. Non usare questi colori per lo sfondo del logo. Usare un colore che faccia risaltare il logo nel portale. Si consiglia di usare colori primari semplici.
- Se si usa uno sfondo trasparente, verificare che i logo e il testo non siano bianchi, neri o blu.
- L'aspetto del logo deve essere semplice e senza sfumature. Non usare uno sfondo sfumato sul logo.
- Non inserire testo, nemmeno il nome del marchio o della società, sul logo.
- Verificare che il logo non venga adattato.

Il logo **Banner (815x290)** è facoltativo, ma consigliato. Se si include un logo banner, seguire queste linee guida:

- Non includere testo nel logo banner e assicurarsi di lasciare 415 pixel di spazio vuoto sul lato destro del logo. Questo spazio è necessario per gli elementi di testo che verranno incorporati a livello di codice: il nome visualizzato dell'editore, il titolo del piano, il riepilogo lungo dell'offerta.
- Lo sfondo del logo banner non può essere nero, bianco o trasparente. Assicurarsi che il colore di sfondo non sia troppo chiaro, perché il testo incorporato verrà visualizzato in bianco.
- Dopo aver pubblicato l'offerta con un'icona banner, non è possibile rimuoverla (anche se è possibile aggiornarla con una versione diversa, se necessario).

Nella sezione **Gestione dei lead** è possibile selezionare il sistema CRM in cui verranno archiviati i lead. Si noti che in base ai [criteri di certificazione dei servizi gestiti](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services), è necessaria una **destinazione del lead**.

Specificare infine l'**URL dell'Informativa sulla privacy** e le **Condizioni per l'utilizzo** nella sezione **Note legali**. È anche possibile specificare qui se usare o meno il [contratto standard](https://docs.microsoft.com/azure/marketplace/standard-contract) per questa offerta.

Assicurarsi di salvare le modifiche prima di passare alla sezione **Supporto**.

## <a name="add-support-info"></a>Aggiungere informazioni di supporto

Nella sezione **Supporto** specificare il nome, l'indirizzo di posta elettronica e il numero di telefono di un contatto tecnico e di un contatto di supporto clienti. Sarà anche necessario fornire gli URL del supporto tecnico. Microsoft può usare queste informazioni quando è necessario contattare l'utente in merito a problemi aziendali e di supporto.

Dopo aver aggiunto queste informazioni, selezionare **Salva**.

## <a name="publish-your-offer"></a>Pubblicare l'offerta

Quando si è soddisfatti di tutte le informazioni immesse, il passaggio successivo consiste nel pubblicare l'offerta in Azure Marketplace. Selezionare il pulsante **Pubblica** per attivare l'offerta. Per altre informazioni su questo processo, vedere [Pubblicare offerte di Azure Marketplace e AppSource](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer).

## <a name="the-customer-onboarding-process"></a>Il processo di onboarding del cliente

Quando un cliente aggiunge l'offerta, sarà in grado di [delegare una o più sottoscrizioni o gruppi di risorse specifici](view-manage-service-providers.md#delegate-resources) di cui verrà quindi eseguito l'onboarding per la gestione di risorse delegate di Azure. Se un cliente ha accettato un'offerta, ma non ha ancora delegato alcuna risorsa, vedrà una nota nella parte superiore della sezione **Provider offers** (Offerte di provider) della pagina [**Provider di servizi**](view-manage-service-providers.md) del portale di Azure.

Prima di eseguire l'onboarding di una sottoscrizione (o dei gruppi di risorse al suo interno), è necessario autorizzare la sottoscrizione registrando manualmente il provider di risorse **Microsoft.ManagedServices**. Un utente nel tenant del cliente con il ruolo Collaboratore o Proprietario può eseguire questa operazione seguendo i passaggi descritti in [Provider e tipi di risorse di Azure](../../azure-resource-manager/resource-manager-supported-services.md).

Il cliente può quindi verificare che la sottoscrizione sia pronta per l'onboarding in uno dei modi seguenti.

### <a name="azure-portal"></a>Portale di Azure

1. Nel portale di Azure selezionare la sottoscrizione.
1. Selezionare **Provider di risorse**.
1. Verificare che **Microsoft.ManagedServices** sia contrassegnato come **Registrato**.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Set-AzContext -Subscription <subscriptionId>
Get-AzResourceProvider -ProviderNameSpace 'Microsoft.ManagedServices'
```

Dovrebbe restituire risultati simili ai seguenti:

```output
ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationDefinitions}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationAssignments}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {operations}
Locations         : {}
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set –subscription <subscriptionId>
az provider show --namespace "Microsoft.ManagedServices" --output table
```

Dovrebbe restituire risultati simili ai seguenti:

```output
Namespace                  RegistrationState
-------------------------  -------------------
Microsoft.ManagedServices  Registered
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [esperienze di gestione tra tenant](../concepts/cross-tenant-management-experience.md).
- [Visualizzare e gestire i clienti](view-manage-customers.md) passando a **Clienti personali** nel portale di Azure.
