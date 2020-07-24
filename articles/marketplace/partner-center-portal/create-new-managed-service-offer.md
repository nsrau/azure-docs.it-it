---
title: Creare un'offerta di servizio gestito nel marketplace commerciale Microsoft
description: Come creare una nuova offerta di servizio gestito per l'inserimento in Azure Marketplace usando il portale del marketplace commerciale nel Centro per i partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: JnHs
ms.author: jenhayes
ms.date: 07/07/2020
ms.openlocfilehash: 3bb663dcdf007bf77d1b9f2b46af532774af22e1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086388"
---
# <a name="create-a-managed-service-offer"></a>Creare un'offerta di servizi gestiti

Il servizio gestito offre assistenza per abilitare scenari di [Azure Lighthouse](../../lighthouse/overview.md). Quando un cliente accetta un'offerta di servizio gestito, potrà eseguire l'onboarding delle risorse per la [gestione delle risorse delegata di Azure](../../lighthouse/concepts/azure-delegated-resource-management.md). Prima di iniziare, [creare un account per il marketplace commerciale nel Centro per i partner](create-account.md), se non è ancora stato fatto. Assicurarsi che l'account sia registrato nel programma del marketplace commerciale.

Per pubblicare un'offerta di servizio gestito, è necessario avere un [livello di competenza Silver o Gold Cloud Platform](https://partner.microsoft.com/membership/cloud-platform-competency) o essere un [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp).

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di spostamento di sinistra selezionare **Marketplace commerciale** > **Panoramica**.
3. Nella pagina Panoramica selezionare **+ Nuova offerta** > **Servizio gestito**.

    ![Immagine del menu di spostamento di sinistra.](./media/new-offer-managed-service.png)

>[!NOTE]
>Dopo la pubblicazione di un'offerta, le modifiche apportate all'offerta nel Centro per i partner vengono visualizzate nelle vetrine solo dopo la ripubblicazione dell'offerta. Assicurarsi di ripubblicare sempre l'offerta dopo avere apportato modifiche.

## <a name="new-offer"></a>Nuova offerta

Immettere un **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

* Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta del marketplace e nei modelli di Azure Resource Manager, se applicabile.
* Usare solo lettere minuscole e numeri. È possibile includere trattini e caratteri di sottolineatura, ma non spazi, per un massimo di 50 caratteri. Ad esempio, se si immette **test-offer-1**, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
* Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

Immettere un **Alias offerta**. Si tratta del nome usato per l'offerta nel Centro per i partner.

* Questo nome non viene usato nel marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.
* L'alias dell'offerta non può essere modificato dopo aver selezionato **Crea**.

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-setup"></a>Configurazione dell'offerta

### <a name="customer-leads"></a>Lead clienti

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

In base ai [criteri di certificazione dei servizi gestiti](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services), è necessaria una **destinazione del lead**. Verrà creato un record nel sistema CRM ogni volta che un cliente distribuisce l'offerta.

Per altre informazioni, vedere [Panoramica della gestione dei lead](./commercial-marketplace-get-customer-leads.md).

Prima di continuare, selezionare **Salva bozza**.

## <a name="properties"></a>Proprietà

Questa pagina consente di definire le categorie usate per raggruppare l'offerta nel marketplace e i contratti legali che supportano l'offerta.

### <a name="category"></a>Category

Selezionare almeno una e un massimo di cinque categorie che verranno usate per inserire l'offerta nelle aree di ricerca appropriate del marketplace. Nella descrizione sottolineare come l'offerta rientri in queste categorie.

### <a name="terms-and-conditions"></a>Termini e condizioni

Specificare i propri termini e condizioni legali nel campo **Termini e condizioni**. È anche possibile specificare l'URL in cui sono disponibili i termini e le condizioni. Ai clienti verrà chiesto di accettare questi termini e condizioni prima di poter provare l'offerta.

Prima di continuare, selezionare **Salva bozza**.

## <a name="offer-listing"></a>Presentazione dell'offerta

Questa pagina consente di definire i dettagli del marketplace, ad esempio il nome dell'offerta, la descrizione e le immagini, per l'offerta.

> [!NOTE]
> Non è necessario usare l'inglese per i contenuti di presentazione dell'offerta (ad esempio descrizione, documenti, screenshot e condizioni per l'utilizzo), purché la descrizione dell'offerta inizi con la frase "Questa applicazione è disponibile solo in [lingua non inglese]". È anche possibile specificare un *URL a collegamenti utili* per offrire contenuti in una lingua diversa da quella usata nei contenuti di presentazione dell'offerta.

Di seguito è riportato un esempio di come vengono visualizzate le informazioni sull'offerta nell'portale di Azure:

:::image type="content" source="media/example-managed-services.png" alt-text="Viene illustrato il modo in cui questa offerta viene visualizzata nel portale di Azure.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Titolo
2. Descrizione
3. Collegamenti utili
4. Screenshots (Schermate)

### <a name="name"></a>Nome

Il nome immesso qui verrà visualizzato ai clienti come titolo della presentazione dell'offerta. Questo campo viene precompilato con il testo immesso per **Alias offerta** quando è stata creata l'offerta, ma è possibile modificarne il valore. Questo nome può essere un marchio registrato (ed è possibile includere i simboli di marchio o copyright). Il nome non può essere costituito da più di 50 caratteri e non può includere emoji.

### <a name="search-results-summary"></a>Riepilogo risultati di ricerca

Specificare una descrizione breve dell'offerta (fino a un massimo di 100 caratteri) da usare nei risultati di ricerca del marketplace.

### <a name="long-summary"></a>Riepilogo lungo

Fornire una descrizione più lunga dell'offerta (fino a 256 caratteri). Questo riepilogo lungo può anche essere usato nei risultati di ricerca del marketplace.

### <a name="description"></a>Descrizione

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="privacy-policy-link"></a>Collegamento dell'informativa sulla privacy

Immettere l'URL dell'informativa sulla privacy dell'organizzazione (presente nel sito). Si è tenuti a verificare che l'app sia conforme alle leggi e alle normative sulla privacy e a fornire un'informativa sulla privacy valida.

### <a name="useful-links"></a>Collegamenti utili

Specificare altri documenti online facoltativi sulla soluzione. Aggiungere altri collegamenti utili facendo clic su **+ Aggiungi un collegamento**.

### <a name="contact-information"></a>Informazioni sul contatto

In questa sezione è necessario specificare il nome, l'indirizzo di posta elettronica e il numero di telefono per **Contatto di supporto** e **Contatto tecnico**. Queste informazioni non vengono visualizzate ai clienti, ma saranno disponibili per Microsoft e possono essere fornite ai partner CSP.

### <a name="support-urls"></a>URL di supporto

Se si hanno siti Web di supporto per i **clienti globali di Azure** e/o i **clienti di Azure per enti pubblici**, specificare gli URL qui.

### <a name="marketplace-images"></a>Immagini del Marketplace

In questa sezione è possibile specificare logo e immagini che verranno usati quando si mostra l'offerta al cliente. Tutte le immagini devono essere in formato PNG.

>[!NOTE]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio https://upload.xboxlive.com usato dal Centro per i partner.

#### <a name="marketplace-logos"></a>Logo del marketplace

Specificare il logo dell'offerta in quattro dimensioni in pixel:

- **Piccola** (48 x 48)
- **Media** (90 x 90)
- **Grande** (216 x 216)
- **Molto grande** (255 X 115)

Seguire queste linee guida per i logo:

- La progettazione di Azure ha una tavolozza dei colori semplice. Limitare il numero di colori primari e secondari nel logo.
- I colori del tema del portale sono il bianco e il nero. Non usare questi colori per lo sfondo del logo. Usare un colore che faccia risaltare il logo nel portale. Si consiglia di usare colori primari semplici.
- Se si usa uno sfondo trasparente, verificare che i logo e il testo non siano bianchi, neri o blu.
- L'aspetto del logo deve essere semplice e senza sfumature. Non usare uno sfondo sfumato sul logo.
- Non inserire testo, nemmeno il nome del marchio o della società, sul logo.
- Verificare che il logo non venga adattato.

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere fino a cinque screenshot che illustrano il funzionamento dell'offerta. Tutti gli screenshot devono avere una dimensione di 1280 x 720 pixel.

#### <a name="videos"></a>Video

Facoltativamente, è possibile aggiungere fino a cinque video che descrivono l'offerta. Questi video devono essere ospitati in YouTube e/o Vimeo. Per ogni video, immettere il nome, l'URL e un'immagine di anteprima (1280 x 720 pixel).

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per la presentazione nel marketplace

- [Procedure consigliate per la presentazione di offerte nel marketplace](../gtm-offer-listing-best-practices.md)

Prima di continuare, selezionare **Salva bozza**.

## <a name="preview"></a>Anteprima

Prima di pubblicare l'offerta live all'interno della più ampia offerta del marketplace, è necessario renderla disponibile in anteprima a un numero di destinatari limitato. Ciò consente di verificare come appare l'offerta in Azure Marketplace prima di renderla disponibile ai clienti. Anche i team di supporto tecnico e di progettazione Microsoft potranno visualizzare l'offerta durante questo periodo di anteprima.

È possibile definire il gruppo di destinatari dell'anteprima immettendo gli ID sottoscrizione di Azure nella sezione **Preview Audience** (Gruppo di destinatari dell'anteprima). È possibile immettere un massimo di 10 ID sottoscrizione manualmente oppure caricare un file con estensione csv con un massimo di 100 ID sottoscrizione.

Tutti i clienti associati a queste sottoscrizioni potranno visualizzare l'offerta in Azure Marketplace prima che diventi disponibile. Assicurarsi di includere qui le sottoscrizioni per poter visualizzare l'offerta in anteprima.

Prima di continuare, selezionare **Salva bozza**.

## <a name="plan-overview"></a>Panoramica del piano

Ogni offerta deve avere uno o più piani, chiamati in precedenza SKU. È possibile aggiungere più piani per supportare set di funzionalità diversi a prezzi diversi o per personalizzare un piano specifico per un numero limitato di clienti specifici. I clienti possono visualizzare i piani disponibili nell'offerta padre.

Nella pagina **Panoramica del piano** selezionare **+ Crea nuovo piano**. Immettere quindi un **ID piano** e un **Nome del piano**. Entrambi questi valori possono contenere solo caratteri alfanumerici minuscoli, trattini e caratteri di sottolineatura, fino a un massimo di 50 caratteri. Questi valori possono essere visibili ai clienti e non possono essere modificati dopo la pubblicazione dell'offerta.

Selezionare **Crea** dopo aver immesso questi valori per continuare a creare il piano. È necessario compilare tre sezioni: **Elenco piani**, **Prezzi e disponibilità** e **Configurazione tecnica**.

### <a name="plan-listing"></a>Elenco piani

Per prima cosa, specificare un **Riepilogo risultati di ricerca** per il piano. Si tratta di una descrizione breve del piano (di un massimo di 100 caratteri) che può essere usata nei risultati di ricerca del marketplace.

Quindi immettere una **Descrizione** che offre una spiegazione più dettagliata del piano.

### <a name="pricing-and-availability"></a>Prezzi e disponibilità

Attualmente è disponibile un solo modello di determinazione dei prezzi che può essere usato per l'offerta di servizio gestito: **Bring Your Own License (BYOL)** . Ciò significa che i costi relativi a questa offerta verranno fatturati direttamente ai clienti e non si riceverà alcun addebito da parte di Microsoft.

La sezione **Plan visibility** (Visibilità del piano) consente di indicare se il piano deve essere [privato](../../marketplace/private-offers.md). Se si lascia la casella di controllo **Questo è un piano privato** deselezionata, il piano non verrà limitato a clienti specifici (o a un determinato numero di clienti).

Per rendere questo piano disponibile solo a clienti specifici, selezionare **Sì**. Quando si esegue questa operazione, è necessario identificare i clienti fornendo gli ID sottoscrizione. Questi possono essere immessi uno alla volta (per un massimo di 10 sottoscrizioni) o caricando un file con estensione csv (per un massimo di 10.000 sottoscrizioni in tutti i piani). Assicurarsi di includere qui le sottoscrizioni per poter testare e convalidare l'offerta.

> [!IMPORTANT]
> Quando un piano viene pubblicato come pubblico non è possibile modificarlo in un piano privato. Per controllare i clienti che possono accettare l'offerta e delegare le risorse, usare un piano privato. Con un piano pubblico non è possibile limitare la disponibilità a determinati clienti o a un determinato numero di clienti, ma è possibile interrompere completamente la vendita del piano, se necessario. È possibile [rimuovere l'accesso a una delega](../../lighthouse/how-to/remove-delegation.md) dopo che un cliente accetta un'offerta solo se è stata inclusa un'**Autorizzazione** con **Definizione ruolo** impostata su [Ruolo con autorizzazioni di eliminazione assegnazioni di registrazione dei servizi gestiti](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) quando è stata pubblicata l'offerta. È anche possibile contattare il cliente e chiedere di [rimuovere l'accesso](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

### <a name="technical-configuration"></a>Configurazione tecnica

Questa sezione del piano crea un manifesto con le informazioni di autorizzazione per la gestione delle risorse dei clienti. Queste informazioni sono necessarie per abilitare la [gestione delle risorse delegata di Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).

Assicurarsi di esaminare [tenant, ruoli e utenti negli scenari di Azure Lighthouse](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) per individuare i ruoli supportati e le procedure consigliate per la definizione delle autorizzazioni.

> [!NOTE]
> Come indicato in precedenza, gli utenti e i ruoli nelle voci di **Autorizzazione** vengono applicati a ogni cliente che acquista il piano. Se si vuole limitare l'accesso a un cliente specifico, è necessario pubblicare un piano privato per l'uso esclusivo.

#### <a name="manifest"></a>Manifesto

Specificare prima di tutto una **versione** per il manifesto. Usare il formato *n.n.n* (ad esempio, 1.2.5).

Immettere quindi l'**ID tenant**. Si tratta di un GUID associato all'ID tenant di Azure Active Directory (Azure AD) dell'organizzazione, ovvero il tenant di gestione da cui verrà eseguito l'accesso alle risorse dei clienti. Se non lo si ha disposizione, è possibile trovarlo passando il puntatore sul nome dell'account nell'angolo in alto a destra del portale di Azure o selezionando **Cambia directory**.

Se si pubblica una nuova versione dell'offerta ed è necessario creare un manifesto aggiornato, selezionare **+ Nuovo manifesto**. Assicurarsi di incrementare il numero di versione rispetto al numero di versione precedente del manifesto.

#### <a name="authorization"></a>Autorizzazione

Le autorizzazioni definiscono le entità nel tenant di gestione che possono accedere alle risorse e alle sottoscrizioni per i clienti che acquistano il piano. A ognuna di queste entità viene assegnato un ruolo predefinito che concede livelli di accesso specifici.

È possibile creare fino a 20 autorizzazioni per ogni piano.

> [!TIP]
> Nella maggior parte dei casi, è consigliabile assegnare i ruoli a un gruppo utenti o a un'entità servizio di Azure AD anziché a una serie di singoli account utente. In questo modo è possibile aggiungere o rimuovere l'accesso per i singoli utenti senza dover aggiornare e ripubblicare il piano quando cambiano i requisiti di accesso. Quando si assegnano ruoli a gruppi di Azure AD [assicurarsi che il **Tipo di gruppo** sia impostato su **Sicurezza** e non su **Office 365**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Per altri consigli, vedere [Tenant, ruoli e utenti negli scenari di Azure Lighthouse](../../lighthouse/concepts/tenants-users-roles.md).

Per ogni valore di **Autorizzazione**, è necessario specificare gli elementi seguenti. È possibile selezionare **+ Aggiungi autorizzazione** ogni volta che è necessario per aggiungere altri utenti o definizioni del ruolo.

- **ID oggetto Azure AD**: identificatore Azure AD di un utente, un gruppo utenti o un'applicazione a cui vengono concesse determinate autorizzazioni (come indicato dalla definizione del ruolo) per le risorse dei clienti.
- **Azure AD Object Display Name** (Nome visualizzato dell'oggetto Azure AD): nome descrittivo per aiutare il cliente a comprendere lo scopo di questa autorizzazione. Questo nome verrà visualizzato dal cliente durante la delega delle risorse.
- **Role Definition** (Definizione ruolo): selezionare uno dei ruoli predefiniti di Azure AD disponibili nell'elenco. Questo ruolo determinerà le autorizzazioni che l'utente indicato nel campo **ID oggetto Azure AD** avrà per le risorse dei clienti. Per le descrizioni di questi ruoli, vedere [ruoli predefiniti](../../role-based-access-control/built-in-roles.md) e supporto dei ruoli [per il faro di Azure](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse).
  > [!NOTE]
  > Poiché i nuovi ruoli predefiniti applicabili vengono aggiunti ad Azure, diventeranno disponibili qui, anche se potrebbe verificarsi un ritardo prima che vengano visualizzati.
- **Assignable Roles** (Ruoli assegnabili): questa opzione viene visualizzata solo se per questa autorizzazione è stato selezionato Amministratore Accesso utenti in **Definizione ruolo**. In tal caso, è necessario aggiungere qui uno o più ruoli assegnabili. L'utente nel campo **ID oggetto Azure AD** sarà in grado di assegnare i ruoli a [entità gestite](../../active-directory/managed-identities-azure-resources/overview.md). Questa operazione è necessaria per [distribuire criteri che possono essere corretti](../../lighthouse/how-to/deploy-policy-remediation.md). Si noti che nessun'altra autorizzazione normalmente associata al ruolo Amministratore Accesso utenti verrà applicata a questo utente.

> [!TIP]
> Per assicurarsi che sia possibile [rimuovere l'accesso a una delega](../../lighthouse/how-to/remove-delegation.md) se necessario, includere un'**Autorizzazione** con **Definizione ruolo** impostata su [Ruolo con autorizzazioni di eliminazione assegnazioni di registrazione dei servizi gestiti](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Se questo ruolo non viene assegnato, le risorse delegate possono essere rimosse solo da un utente che si trova nel tenant del cliente.

Dopo aver compilato tutte le sezioni per il piano, è possibile selezionare **+ Crea nuovo piano** il numero di volte necessario per creare piani aggiuntivi. Al termine, selezionare **Salva**.

Prima di continuare, selezionare **Salva bozza**.

## <a name="publish"></a>Pubblica

### <a name="submit-offer-to-preview"></a>Inviare l'offerta per l'anteprima

Dopo aver completato tutte le sezioni obbligatorie dell'offerta, selezionare **Rivedi e pubblica** nell'angolo superiore destro del portale.

Se è la prima volta che si pubblica questa offerta, è possibile:

- Visualizzare lo stato di completamento di ogni sezione dell'offerta.
  - **Non avviata**: la sezione è stata lasciata inalterata e deve essere completata.
  - **Incompleta**: la sezione contiene errori che devono essere corretti o è necessario specificare altre informazioni. Tornare alla sezione e aggiornarla.
  - **Completa**: la sezione è completa, sono stati forniti tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere in stato completato prima di poter inviare l'offerta.
- Nella sezione **Note per la certificazione** specificare le istruzioni per il test per il team di certificazione per assicurarsi che l'app venga testata correttamente, oltre a eventuali note supplementari utili per conoscere l'app.
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Si riceverà un messaggio di posta elettronica quando una versione di anteprima dell'offerta è disponibile per la revisione e l'approvazione. Tornare al Centro per i partner e selezionare **Passa allo stato Live** per l'offerta per pubblicare l'offerta per il pubblico oppure, se l'offerta è privata, per destinatari privati.

### <a name="customer-experience-and-offer-management"></a>Gestione dell'esperienza dei clienti e dell'offerta

Quando un cliente distribuisce l'offerta, sarà in grado di delegare le sottoscrizioni o i gruppi di risorse per la [gestione delle risorse delegata di Azure](../../lighthouse/concepts/azure-delegated-resource-management.md). Per altre informazioni su questo processo, vedere [Il processo di onboarding del cliente](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process).

È possibile [pubblicare una versione aggiornata dell'offerta](update-existing-offer.md) in qualsiasi momento. Ad esempio, potrebbe essere necessario aggiungere una nuova definizione di ruolo a un'offerta pubblicata in precedenza. Quando si esegue questa operazione, i clienti che hanno già aggiunto l'offerta vedranno un'icona nella pagina [**Provider di servizi**](../../lighthouse/how-to/view-manage-service-providers.md) del portale di Azure che consente di sapere che è disponibile un aggiornamento. Ogni cliente potrà rivedere le modifiche e decidere se eseguire l'aggiornamento alla nuova versione.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
- [Informazioni su Azure Lighthouse](../../lighthouse/overview.md)
