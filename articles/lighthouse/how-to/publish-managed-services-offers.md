---
title: Pubblicare un'offerta di servizio gestito in Azure Marketplace
description: Informazioni su come pubblicare un'offerta di servizio gestito che carica i clienti nella gestione delle risorse delegate di Azure.
ms.date: 04/24/2020
ms.topic: conceptual
ms.openlocfilehash: 19c4d1a4bd0ffd7c0162cbf7f20c49a5b219b9bc
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146732"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Pubblicare un'offerta di servizio gestito in Azure Marketplace

> [!IMPORTANT]
> A partire dal 13 aprile 2020, inizieremo a trasferire la gestione delle offerte del servizio gestito al centro per i partner. Dopo la migrazione, sarà possibile creare e gestire le offerte nel centro per i partner. Per gestire le offerte migrate, seguire le istruzioni riportate in [creare una nuova offerta di servizio gestito](../../marketplace/partner-center-portal/create-new-managed-service-offer.md) .

In questo articolo si apprenderà come pubblicare un'offerta di servizio gestito pubblica o privata in [Azure Marketplace](https://azuremarketplace.microsoft.com) usando [portale cloud partner](https://cloudpartner.azure.com/). I clienti che acquistano l'offerta possono quindi eseguire l'onboarding di sottoscrizioni e gruppi di risorse per la [gestione delle risorse delegate di Azure](../concepts/azure-delegated-resource-management.md).

## <a name="publishing-requirements"></a>Requisiti per la pubblicazione

Per creare e pubblicare le offerte, è necessario disporre di un [account valido nel centro](../../marketplace/partner-center-portal/create-account.md) per i partner. Se non si dispone già di un account, il [processo di iscrizione](https://aka.ms/joinmarketplace) consentirà di creare un account nel centro per i partner e di iscriversi al programma Commercial Marketplace.

In base ai [requisiti di certificazione dell'offerta del servizio gestito](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements), è necessario disporre di un livello di competenza della [piattaforma Cloud Silver o Gold](https://docs.microsoft.com/partner-center/learn-about-competencies) oppure essere un [msp esperto di Azure](https://partner.microsoft.com/membership/azure-expert-msp) per pubblicare un'offerta di servizio gestito.

L'ID MPN (Microsoft Partner Network) verrà [associato automaticamente](../../billing/billing-partner-admin-link-started.md) alle offerte pubblicate per tenere traccia dell'impatto sugli engagement dei clienti.

> [!NOTE]
> Se non si vuole pubblicare un'offerta in Azure Marketplace, è possibile eseguire l'onboarding dei clienti manualmente usando i modelli di Azure Resource Manager. Per altre informazioni, vedere [Eseguire l'onboarding di un cliente nella gestione risorse delegate di Azure](onboard-customer.md).

La pubblicazione di un'offerta di servizio gestita è simile alla pubblicazione di qualsiasi altro tipo di offerta in Azure Marketplace. Per informazioni sul processo di pubblicazione generale, vedere la [Guida alla pubblicazione su Azure Marketplace e AppSource](../../marketplace/marketplace-publishers-guide.md). È anche necessario esaminare i [criteri di certificazione del Marketplace commerciale](https://docs.microsoft.com/legal/marketplace/certification-policies), in particolare la sezione [Servizi gestiti](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services).

Una volta che un cliente ha aggiunto l'offerta, sarà in grado di delegare una o più sottoscrizioni o gruppi di risorse, che verranno quindi caricati [per la gestione delle risorse delegate di Azure](#the-customer-onboarding-process).

> [!IMPORTANT]
> Ogni piano in un'offerta di servizio gestito include una sezione dei **Dettagli del manifesto** , in cui si definiscono le entità Azure Active Directory (Azure ad) nel tenant che avranno accesso ai gruppi di risorse e/o alle sottoscrizioni delegate per i clienti che acquistano il piano. È importante tenere presente che qualsiasi gruppo, o utente o entità servizio, incluso avrà le stesse autorizzazioni per ogni cliente che acquista il piano. Per assegnare gruppi diversi per lavorare con ogni cliente, è necessario pubblicare un [piano privato](../../marketplace/private-offers.md) separato che è esclusivo per ogni cliente.

## <a name="create-your-offer"></a>Creare l'offerta

1. Accedere al [portale cloud partner](https://cloudpartner.azure.com/).
2. Dal menu di spostamento a sinistra selezionare **Nuova offerta**, quindi selezionare **Servizi gestiti**.
3. Verrà visualizzata una sezione dell' **Editor** per l'offerta con quattro parti da compilare: **impostazioni offerta**, **piani**, **Marketplace**e **supporto**. Continuare a leggere per istruzioni su come completare queste sezioni.

### <a name="enter-offer-settings"></a>Immettere le impostazioni dell'offerta

Nella sezione **Impostazioni dell'offerta** immettere le informazioni seguenti:

|Campo  |Descrizione  |
|---------|---------|
|**Offer ID (ID offerta)**     | Un identificatore univoco dell'offerta (nel profilo di pubblicazione). Questo ID può contenere solo caratteri alfanumerici minuscoli, trattini e caratteri di sottolineatura, fino a un massimo di 50 caratteri. Tenere presente che l'ID dell'offerta può essere visibile ai clienti ad esempio negli URL dei prodotti e nei report di fatturazione. Dopo aver pubblicato l'offerta, non è possibile modificare questo valore.        |
|**ID editore**     | ID dell'editore che verrà associato all'offerta. Se sono presenti più ID editore, è possibile selezionare quello che si vuole usare per questa offerta.       |
|**Nome**     | Nome (fino a 50 caratteri) dell'offerta visualizzato dai clienti in Azure Marketplace e nel portale di Azure. Usare un nome di marchio riconoscibile che i clienti potranno comprendere. Se si promuove questa offerta tramite il proprio sito Web, assicurarsi di usare esattamente lo stesso nome.        |

Al termine, fare clic su **Salva**. A questo punto è possibile passare alla sezione **Piani**.

### <a name="create-plans"></a>Creare piani

Ogni offerta deve avere uno o più piani, detti anche SKU. È possibile aggiungere più piani per supportare set di funzionalità diversi a prezzi diversi o per personalizzare un piano specifico per un numero limitato di clienti specifici. I clienti possono visualizzare i piani disponibili nell'offerta padre.

Nella sezione Piani selezionare **Nuovo piano**. Immettere quindi un **ID piano**. Questo ID può contenere solo caratteri alfanumerici minuscoli, trattini e caratteri di sottolineatura, fino a un massimo di 50 caratteri. L'ID del piano può essere visibile ai clienti ad esempio negli URL dei prodotti e nei report di fatturazione. Dopo aver pubblicato l'offerta, non è possibile modificare questo valore.

#### <a name="plan-details"></a>Dettagli piano

Completare le sezioni seguenti nella sezione **Dettagli piano**:

|Campo  |Descrizione  |
|---------|---------|
|**Titolo**     | Nome descrittivo del piano da visualizzare. La lunghezza massima consentita è di 50 caratteri.        |
|**Riepilogo**     | Breve descrizione del piano da visualizzare sotto il titolo. La lunghezza massima consentita è di 100 caratteri.        |
|**Descrizione**     | Testo della descrizione con una spiegazione più dettagliata del piano.         |
|**Modello di fatturazione**     | Sono visualizzati 2 modelli di fatturazione, ma è necessario scegliere **Bring Your Own License** per le offerte di servizi gestiti. Ciò significa che i costi relativi a questa offerta verranno fatturati direttamente ai clienti e non si riceverà alcun addebito da parte di Microsoft.   |
|**Si tratta di un piano privato?**     | Indica se lo SKU è pubblico o privato. Il valore predefinito è **No** (pubblico). Se si lascia questa selezione, il piano non sarà limitato a clienti specifici (o a un certo numero di clienti). Dopo aver pubblicato un piano pubblico, non sarà possibile impostarlo in un secondo momento come privato. Per rendere questo piano disponibile solo a clienti specifici, selezionare **Sì**. Quando si esegue questa operazione, è necessario identificare i clienti fornendo gli ID sottoscrizione. Questi possono essere immessi uno alla volta (per un massimo di 10 sottoscrizioni) o caricando un file CSV (per un massimo di 20.000 sottoscrizioni). Assicurarsi di includere qui le sottoscrizioni per poter testare e convalidare l'offerta. Per altre informazioni, vedere [SKU e piani privati](../../marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  |

> [!IMPORTANT]
> Quando un piano è stato pubblicato come pubblico, non è possibile modificarlo in privato. Per controllare quali clienti possono accettare l'offerta e delegare le risorse, usare un piano privato. Con un piano pubblico, non è possibile limitare la disponibilità a determinati clienti o anche a un certo numero di clienti (sebbene sia possibile smettere completamente di vendere il piano se si sceglie di farlo). È possibile [rimuovere l'accesso a una delega](remove-delegation.md) dopo che un cliente accetta un'offerta solo se è stata inclusa un' **autorizzazione** con la **definizione del ruolo** impostata sul [ruolo eliminazione di assegnazione di registrazione dei servizi gestiti](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) quando è stata pubblicata l'offerta. È anche possibile contattare il cliente e chiedere di [rimuovere l'accesso](view-manage-service-providers.md#add-or-remove-service-provider-offers).

#### <a name="manifest-details"></a>Dettagli del manifesto

Completare la sezione **Manifest Details** (Dettagli manifesto) per il proprio piano. Verrà creato un manifesto con le informazioni di autorizzazione per la gestione delle risorse dei clienti. Queste informazioni sono necessarie per abilitare la gestione delle risorse delegate di Azure.

> [!NOTE]
> Come indicato in precedenza, gli utenti e i ruoli nelle voci di **Autorizzazione** vengono applicati a ogni cliente che acquista il piano. Se si vuole limitare l'accesso a un cliente specifico, è necessario pubblicare un piano privato per l'uso esclusivo.

Specificare prima di tutto una **versione** per il manifesto. Usare il formato *n.n.n* (ad esempio, 1.2.5).

Immettere quindi l'**ID tenant**. Si tratta di un GUID associato all'ID tenant di Azure Active Directory dell'organizzazione, ad esempio il tenant che si userà per gestire le risorse dei clienti. Se non lo si ha disposizione, è possibile trovarlo passando il puntatore sul nome dell'account nell'angolo in alto a destra del portale di Azure o selezionando **Cambia directory**.

Aggiungere infine una o più voci **Autorizzazione** al piano. Le autorizzazioni definiscono le entità che possono accedere alle risorse e alle sottoscrizioni per i clienti che acquistano il piano e assegnano ruoli che concedono livelli di accesso specifici.

> [!TIP]
> Nella maggior parte dei casi, è consigliabile assegnare le autorizzazioni a un gruppo di utenti o a un'entità servizio di Azure AD, invece che a una serie di singoli account utente. In questo modo è possibile aggiungere o rimuovere l'accesso per i singoli utenti senza dover aggiornare e ripubblicare il piano quando cambiano i requisiti di accesso. Per altri consigli, vedere [Tenant, ruoli e utenti negli scenari di Azure Lighthouse](../concepts/tenants-users-roles.md).

Per ogni valore di **Autorizzazione**, è necessario specificare gli elementi seguenti. È possibile selezionare **New authorization** (Nuova autorizzazione) ogni volta che è necessario per aggiungere altri utenti o definizioni del ruolo.

- **ID oggetto Azure ad**: identificatore Azure ad di un utente, un gruppo di utenti o un'applicazione a cui verranno concesse determinate autorizzazioni (come descritto nella definizione del ruolo) per le risorse dei clienti.
- **Azure ad nome visualizzato dell'oggetto**: un nome descrittivo per aiutare il cliente a comprendere lo scopo di questa autorizzazione. Questo nome verrà visualizzato dal cliente durante la delega delle risorse.
- **Definizione del ruolo**: selezionare uno dei ruoli predefiniti Azure ad disponibili nell'elenco. Questo ruolo determinerà le autorizzazioni che l'utente indicato nel campo **ID oggetto Azure AD** avrà per le risorse dei clienti. Per le descrizioni di questi ruoli, vedere [ruoli predefiniti](../../role-based-access-control/built-in-roles.md) e supporto dei ruoli [per la gestione delle risorse delegate di Azure](../concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management).
  > [!NOTE]
  > Poiché i nuovi ruoli predefiniti applicabili vengono aggiunti ad Azure, diventeranno disponibili qui, anche se potrebbe verificarsi un certo ritardo prima che vengano visualizzati.
- **Ruoli assegnabili**: questa operazione è necessaria solo se è stato selezionato amministratore accesso utenti nella **definizione del ruolo** per l'autorizzazione. In tal caso, è necessario aggiungere qui uno o più ruoli assegnabili. L'utente nel campo **ID oggetto Azure AD** sarà in grado di assegnare i **ruoli assegnabili** a [entità gestite](../../active-directory/managed-identities-azure-resources/overview.md). Questa operazione è necessaria per [distribuire criteri che possono essere corretti](deploy-policy-remediation.md). Si noti che nessun'altra autorizzazione normalmente associata al ruolo Amministratore Accesso utenti verrà applicata a questo utente. Se non si selezionano uno o più ruoli, l'invio non supererà la certificazione. Se non è stato selezionato amministratore accesso utenti per la definizione di ruolo di questo utente, questo campo non ha alcun effetto.

> [!TIP]
> Per assicurarsi che sia possibile [rimuovere l'accesso a una delega](remove-delegation.md) , se necessario, includere un' **autorizzazione** con la **definizione del ruolo** impostata sul [ruolo eliminazione di assegnazione di registrazione dei servizi gestiti](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Se questo ruolo non viene assegnato, le risorse delegate possono essere rimosse solo da un utente che si trova nel tenant del cliente.

Dopo aver completato le informazioni, è possibile selezionare **Nuovo piano** il numero di volte necessario per creare piani aggiuntivi. Al termine, selezionare **Salva**, quindi passare alla sezione **Marketplace**.

### <a name="provide-marketplace-text-and-images"></a>Fornire testo e immagini del Marketplace

Nella sezione **Marketplace** si forniscono il testo e le immagini che i clienti visualizzeranno in Azure Marketplace e nel portale di Azure.

Completare i campi seguenti nella sezione **Panoramica**:

|Campo  |Descrizione  |
|---------|---------|
|**Titolo**     |  Titolo dell'offerta, spesso corrispondente alla forma estesa e formale del nome. Questo titolo verrà visualizzato in una posizione di estremo rilievo nel marketplace. La lunghezza massima consentita è di 50 caratteri. Nella maggior parte dei casi, corrisponderà al **nome** immesso nella sezione **Impostazioni dell'offerta**.       |
|**Riepilogo**     | Descrizione breve dello scopo o della funzione dell'offerta. Viene in genere visualizzata sotto il titolo. La lunghezza massima consentita è di 100 caratteri.        |
|**Long Summary** (Riepilogo lungo)     | Riepilogo più lungo dello scopo o della funzione dell'offerta. La lunghezza massima consentita è di 256 caratteri.        |
|**Descrizione**     | Altre informazioni sull'offerta. Questo campo ha una lunghezza massima consentita di 3000 caratteri e supporta la formattazione HTML semplice. È necessario includere le parole "servizio gestito" o "servizi gestiti" in un punto qualsiasi della descrizione.       |
|**Marketing Identifier** (Identificatore di marketing)     | Identificatore univoco descrittivo per gli URL. Questo identificatore può contenere solo caratteri alfanumerici minuscoli e trattini. Verrà usato negli URL del Marketplace per questa offerta. Se ad esempio l'ID editore è *contoso* e l'identificatore di marketing è *sampleApp*, l'URL per l'offerta in Azure Marketplace sarà `https://azuremarketplace.microsoft.com/marketplace/apps/contoso-sampleApp`.       |
|**Preview Subscription IDs** (ID sottoscrizione di anteprima)     | Aggiungere da uno a 100 identificatori di sottoscrizione. I clienti associati a queste sottoscrizioni potranno visualizzare l'offerta in Azure Marketplace prima che diventi disponibile. È consigliabile includere qui le sottoscrizioni per poter visualizzare in anteprima come viene visualizzata l'offerta in Azure Marketplace prima di renderla disponibile per i clienti.  Anche i team di supporto tecnico e di progettazione Microsoft potranno visualizzare l'offerta durante questo periodo di anteprima.   |
|**Collegamenti utili**     | URL correlati all'offerta, ad esempio la documentazione, le note sulla versione, le domande frequenti e così via.        |
|**Suggested Categories (Max 5)** (Categorie suggerite - Max 5)     | Una o più categorie (fino a cinque) applicabili all'offerta. Queste categorie aiutano i clienti a individuare l'offerta in Azure Marketplace e nel portale di Azure.        |

Nella sezione **Marketing Artifacts** (Artefatti di marketing) è possibile caricare logo e altri asset da visualizzare con l'offerta. È facoltativamente possibile caricare screenshot o collegamenti a video che consentono ai clienti di comprendere l'offerta.

Sono necessarie quattro dimensioni del logo: **Small (40x40)**, **Medium (90x90)**, **Large (115x115)** e **Wide (255x115)**. Seguire queste linee guida per i logo:

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

Specificare infine l'**URL dell'Informativa sulla privacy** e le **Condizioni per l'utilizzo** nella sezione **Note legali**. È anche possibile specificare qui se usare o meno il [contratto standard](../../marketplace/standard-contract.md) per questa offerta.

Assicurarsi di salvare le modifiche prima di passare alla sezione **Supporto**.

### <a name="add-support-info"></a>Aggiungere informazioni di supporto

Nella sezione **Supporto** specificare il nome, l'indirizzo di posta elettronica e il numero di telefono di un contatto tecnico e di un contatto di supporto clienti. Sarà anche necessario fornire gli URL del supporto tecnico. Microsoft può usare queste informazioni quando è necessario contattare l'utente in merito a problemi aziendali e di supporto.

Dopo aver aggiunto queste informazioni, selezionare **Salva**.

## <a name="publish-your-offer"></a>Pubblicare l'offerta

Dopo aver completato tutte le sezioni, il passaggio successivo consiste nel pubblicare l'offerta in Azure Marketplace. Selezionare il pulsante **Pubblica** per attivare l'offerta. Per altre informazioni su questo processo, vedere [Pubblicare offerte di Azure Marketplace e AppSource](../../marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer.md).

È possibile [pubblicare una versione aggiornata dell'offerta](../../marketplace/cloud-partner-portal/manage-offers/cpp-update-offer.md) in qualsiasi momento. Ad esempio, potrebbe essere necessario aggiungere una nuova definizione di ruolo a un'offerta pubblicata in precedenza. Quando si esegue questa operazione, i clienti che hanno già aggiunto l'offerta vedranno un'icona nella pagina [**Provider di servizi**](view-manage-service-providers.md) del portale di Azure che consente di sapere che è disponibile un aggiornamento. Ogni cliente sarà in grado di [rivedere le modifiche](view-manage-service-providers.md#update-service-provider-offers) e decidere se eseguire l'aggiornamento alla nuova versione. 

## <a name="the-customer-onboarding-process"></a>Il processo di onboarding del cliente

Dopo aver aggiunto l'offerta, un cliente potrà [delegare una o più sottoscrizioni o gruppi di risorse specifici](view-manage-service-providers.md#delegate-resources), di cui verrà quindi eseguito l'onboarding per la gestione risorse delegate di Azure. Se un cliente ha accettato un'offerta, ma non ha ancora delegato alcuna risorsa, vedrà una nota nella parte superiore della sezione **Provider offers** (Offerte di provider) della pagina [**Provider di servizi**](view-manage-service-providers.md) del portale di Azure.

> [!IMPORTANT]
> La delega deve essere eseguita da un account non Guest nel tenant del cliente che ha il [ruolo predefinito proprietario](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) per la sottoscrizione da caricare (o che contiene i gruppi di risorse sottoposto a onboarding). Per visualizzare tutti gli utenti che possono delegare la sottoscrizione, un utente nel tenant del cliente può selezionare la sottoscrizione nel portale di Azure, aprire **Controllo di accesso (IAM)** e [visualizzare tutti gli utenti con il ruolo Proprietario](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Quando il cliente delega una sottoscrizione (o uno o più gruppi di risorse all'interno di una sottoscrizione), il provider di risorse **Microsoft. ManagedServices** verrà registrato per tale sottoscrizione e gli utenti nel tenant potranno accedere alle risorse delegate in base alle autorizzazioni dell'offerta.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [Marketplace commerciale](../../marketplace/partner-center-portal/commercial-marketplace-overview.md).
- Informazioni sulle [esperienze di gestione tra tenant](../concepts/cross-tenant-management-experience.md).
- [Visualizzare e gestire i clienti](view-manage-customers.md) passando a **Clienti personali** nel portale di Azure.
