---
title: Creare una nuova offerta di servizio gestito nel Marketplace commerciale
description: Come creare una nuova offerta di servizio gestito per l'inserimento in Azure Marketplace usando il portale di Marketplace commerciale nel centro per i partner.
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: c82c3e0b6e6d11ccc33bf8556b06aeb04a6d37b5
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147918"
---
# <a name="create-a-new-managed-service-offer"></a>Creare una nuova offerta di servizio gestito

> [!IMPORTANT]
> Stiamo muovendo la gestione delle offerte del servizio gestito dal portale Cloud Partner al centro per i partner. Fino a quando non viene eseguita la migrazione delle offerte, segui le istruzioni in [pubblicare un'offerta di servizio gestito in Azure Marketplace](../../lighthouse/how-to/publish-managed-services-offers.md) per gestire le tue offerte in Portale cloud partner.

Il servizio gestito offre assistenza per abilitare gli scenari di [Azure Lighthouse](../../lighthouse/overview.md) . Quando un cliente accetta un'offerta di servizio gestito, potrà eseguire l'onboarding delle risorse per la [gestione delle risorse delegate di Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).

Per iniziare a creare le offerte del servizio gestito, assicurarsi di [creare prima un account del centro](./create-account.md) per i partner e aprire il dashboard del [Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), con la pagina **Panoramica** selezionata. Per pubblicare un'offerta di servizio gestito, è necessario avere un [livello di competenza Silver o Gold per la piattaforma cloud](https://partner.microsoft.com/membership/cloud-platform-competency) o essere un [msp esperto di Azure](https://partner.microsoft.com/membership/azure-expert-msp) .

![Dashboard del Marketplace commerciale nel centro per i partner](./media/new-offer-overview.png)

>[!Note]
> Dopo la pubblicazione di un'offerta, le modifiche apportate all'offerta nel centro per i partner verranno aggiornate solo nel sistema e archiviano i front-end dopo la nuova pubblicazione. Assicurarsi di inviare l'offerta per la pubblicazione dopo avere apportato le modifiche.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

Selezionare il pulsante **+ nuova offerta** , quindi selezionare la voce di menu **servizio gestito** . Verrà visualizzata la finestra di dialogo **nuova offerta** .

### <a name="offer-id-and-alias"></a>ID offerta e alias

- **ID offerta**: identificatore univoco per ogni offerta nell'account. Questo ID sarà visibile ai clienti nell'indirizzo URL dell'offerta del Marketplace. Questo ID può contenere solo caratteri alfanumerici minuscoli (inclusi trattini e caratteri di sottolineatura, ma senza spazi vuoti), limitati a 50 caratteri e non può essere modificato dopo aver selezionato **Crea**.  Se ad esempio si immette *test-offer-1* qui, l'URL dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

- **Alias offerta**: nome usato per fare riferimento all'offerta all'interno del centro per i partner. Questo nome non verrà usato nel Marketplace ed è diverso dal nome dell'offerta e da altri valori che verranno visualizzati ai clienti. Questo valore non può essere modificato dopo aver selezionato **Crea**.

Dopo aver immesso l' **ID offerta** e l' **alias offerta**, selezionare **Crea**. Sarà quindi possibile lavorare su tutte le diverse parti dell'offerta.

## <a name="offer-setup"></a>Installazione dell'offerta

La pagina **installazione offerta** richiede le informazioni seguenti. Assicurarsi di selezionare **Salva** dopo aver completato questi campi.

## <a name="connect-lead-management"></a>Gestione dei lead di connessione

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Si noti che in base ai [criteri di certificazione dei servizi gestiti](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services), è necessaria una **destinazione del lead**. Verrà creato un record nel sistema CRM ogni volta che un cliente distribuisce l'offerta.

Per altre informazioni, vedere [Cenni preliminari sulla gestione dei lead](./commercial-marketplace-get-customer-leads.md).

Ricordarsi di **salvare** la pagina prima di procedere alla sezione successiva.

## <a name="properties"></a>Proprietà

La pagina **Proprietà** consente di definire le categorie usate per raggruppare l'offerta nel Marketplace e i contratti legali che supportano l'offerta. Selezionare **Salva** dopo aver completato questa pagina.

### <a name="category"></a>Category

Selezionare almeno una e un massimo di cinque categorie che verranno usate per inserire l'offerta nelle aree di ricerca del Marketplace appropriate. Assicurarsi di richiamare il modo in cui l'offerta supporta queste categorie nella descrizione dell'offerta.

### <a name="terms-and-conditions"></a>Termini e condizioni

Fornire i termini e le condizioni legali nel campo **termini e condizioni** . È anche possibile specificare l'URL in cui è possibile trovare i termini e le condizioni. Ai clienti verrà richiesto di accettare le presenti condizioni per poter provare l'offerta.

## <a name="offer-listing"></a>Elenco offerte

La pagina di **inserzione dell'offerta** consente di definire i dettagli del Marketplace (nome dell'offerta, descrizione, immagini e così via) per l'offerta.

> [!NOTE]
> Il contenuto dell'offerta (ad esempio la descrizione, i documenti, le schermate, le condizioni per l'utilizzo e così via) non deve essere in inglese, purché la descrizione dell'offerta inizi con la frase "questa applicazione è disponibile solo in [lingua non inglese]". È anche accettabile fornire un URL di *collegamento utile* per offrire contenuto in una lingua diversa da quella usata nell'offerta di visualizzazione del contenuto.

### <a name="name"></a>Name

Il nome immesso qui verrà visualizzato ai clienti come titolo dell'elenco di offerte. Questo campo viene prepopolato con il testo immesso per l' **alias offerta** al momento della creazione dell'offerta, ma è possibile modificare questo valore. Questo nome può essere registrato (e possono essere inclusi marchi o simboli di copyright). Il nome non può contenere più di 50 caratteri e non può includere emoji.

### <a name="search-results-summary"></a>Riepilogo risultati ricerca

Fornire una breve descrizione dell'offerta (fino a 100 caratteri), che può essere usata nei risultati della ricerca nel Marketplace.

### <a name="long-summary"></a>Riepilogo lungo

Fornire una descrizione più lunga dell'offerta (fino a 256 caratteri). Questo riepilogo lungo può anche essere usato nei risultati della ricerca nel Marketplace.

### <a name="description"></a>Descrizione

Fornire una descrizione più lunga dell'offerta (fino a 3.000 caratteri). Questa descrizione verrà visualizzata ai clienti nella panoramica dell'inserzione sul Marketplace. Includere la proposta di valore dell'offerta, i vantaggi principali, le associazioni di categoria e/o di settore, le opportunità di acquisto in-app ed eventuali divulgazioni necessarie.

Alcuni suggerimenti per scrivere la descrizione:  

- Descrivere chiaramente la proposta di valore dell'offerta subito nelle prime frasi della descrizione. Includere gli elementi seguenti nella proposta di valore:
  - Descrizione dell'offerta
  - Il tipo di utente che trae vantaggio dall'offerta
  - Esigenze dei clienti o dolore che l'offerta indirizzi
- Tenere presente che le prime frasi possono essere visualizzate nei risultati dei motori di ricerca.  
- Per vendere un prodotto, non basarsi sulle funzionalità, ma concentrarsi sul valore che può offrire.  
- Usare il più possibile la terminologia specifica del settore o formule che mettano in evidenza i vantaggi offerti.

Per rendere più accattivante la descrizione dell'offerta, usare l'editor di testo RTF per formattare la descrizione.

![Uso dell'editor di testo RTF](./media/text-editor2.png)

Utilizzare le istruzioni seguenti per utilizzare l'editor di testo RTF:

- Per modificare il formato del contenuto, evidenziare il testo che si desidera formattare e selezionare uno stile di testo, come illustrato di seguito:

     ![Utilizzo dell'editor di testo RTF per modificare il formato del testo](./media/text-editor3.png)

- Per aggiungere un elenco puntato o numerato al testo, usare le opzioni seguenti:

     ![Uso dell'editor di testo RTF per aggiungere elenchi](./media/text-editor4.png)

- Per aggiungere o rimuovere il rientro nel testo, usare le opzioni seguenti:

     ![Utilizzo dell'editor di testo RTF per rientrare](./media/text-editor5.png)

### <a name="privacy-policy-link"></a>Collegamento all'informativa sulla privacy

Immettere l'URL dell'informativa sulla privacy dell'organizzazione, ospitata nel sito. L'utente è responsabile di garantire che l'applicazione sia conforme alle leggi e alle normative sulla privacy e per fornire un'informativa sulla privacy valida.

### <a name="useful-links"></a>Collegamenti utili

Fornire documenti online supplementari facoltativi sulla soluzione. Aggiungere altri collegamenti utili facendo clic su **+ Aggiungi un collegamento**.

### <a name="contact-information"></a>Informazioni sul contatto

In questa sezione è necessario fornire il nome, l'indirizzo di posta elettronica e il numero di telefono per un **contatto di supporto** e un **contatto tecnico**. Queste informazioni non vengono visualizzate ai clienti, ma saranno disponibili per Microsoft e potranno essere fornite ai partner CSP.

### <a name="support-urls"></a>URL di supporto

Se si hanno siti Web di supporto per **i clienti globali di Azure** e/o **i clienti di Azure per enti pubblici**, fornire tali URL qui.

### <a name="marketplace-images"></a>Immagini del Marketplace

In questa sezione è possibile fornire logo e immagini che verranno usati quando si mostra l'offerta al cliente. Tutte le immagini devono essere in formato png.

>[!Note]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il https://upload.xboxlive.com servizio usato dal centro per i partner.

#### <a name="marketplace-logos"></a>Logo del Marketplace

Sono necessarie quattro dimensioni del logo: **Small (40x40)**, **Medium (90x90)**, **Large (115x115)** e **Wide (255x115)**. Seguire queste linee guida per i logo:

- La progettazione di Azure ha una tavolozza dei colori semplice. Limitare il numero di colori primari e secondari nel logo.
- I colori del tema del portale sono il bianco e il nero. Non usare questi colori per lo sfondo del logo. Usare un colore che faccia risaltare il logo nel portale. Si consiglia di usare colori primari semplici.
- Se si usa uno sfondo trasparente, verificare che i logo e il testo non siano bianchi, neri o blu.
- L'aspetto del logo deve essere semplice e senza sfumature. Non usare uno sfondo sfumato sul logo.
- Non inserire testo, nemmeno il nome del marchio o della società, sul logo.
- Verificare che il logo non venga adattato.

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere fino a cinque schermate che mostrano il funzionamento dell'offerta. Tutte le schermate devono avere 1280 x 720 pixel.

#### <a name="videos"></a>Video

Facoltativamente, è possibile aggiungere fino a cinque video che illustrano l'offerta. Questi video devono essere ospitati in YouTube e/o Vimeo. Per ciascuna di esse, immettere il nome del video, il relativo URL e un'immagine di anteprima del video (1280 x 720 pixel).

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per l'elenco del Marketplace

- [Procedure consigliate per gli elenchi di offerte del Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Anteprima

Prima di pubblicare l'offerta in tempo reale nell'offerta del Marketplace più ampia, sarà prima di tutto necessario renderla disponibile per un gruppo di destinatari di anteprima limitato. Questo consente di confermare la modalità di visualizzazione dell'offerta in Azure Marketplace prima di renderla disponibile ai clienti. I team di supporto e di progettazione Microsoft potranno anche visualizzare l'offerta durante questo periodo di anteprima.

È possibile definire il gruppo di destinatari di anteprima immettendo ID sottoscrizione di Azure nella sezione **Anteprima destinatari** . È possibile immettere un massimo di 10 ID sottoscrizione manualmente oppure caricare un file CSV con un massimo di 100 ID sottoscrizione.

Tutti i clienti associati a queste sottoscrizioni saranno in grado di visualizzare l'offerta in Azure Marketplace prima che diventi disponibile. Assicurati di includere qui le tue sottoscrizioni per poter visualizzare in anteprima l'offerta.

## <a name="plan-overview"></a>Panoramica del piano

Ogni offerta deve avere uno o più piani, detti anche SKU. È possibile aggiungere più piani per supportare set di funzionalità diversi a prezzi diversi o per personalizzare un piano specifico per un numero limitato di clienti specifici. I clienti possono visualizzare i piani disponibili nell'offerta padre.

Nella pagina **panoramica piano** selezionare **+ Crea nuovo piano**. Immettere quindi un **ID piano** e un **nome del piano**. Entrambi questi valori possono contenere solo caratteri alfanumerici minuscoli, trattini e caratteri di sottolineatura, con un massimo di 50 caratteri. Questi valori possono essere visibili ai clienti e non possono essere modificati dopo la pubblicazione dell'offerta.

Selezionare **Crea** dopo aver immesso questi valori per continuare a utilizzare il piano. Ci sono tre sezioni da completare: **elenco di piani**, **prezzi e disponibilità**e **configurazione tecnica**.

### <a name="plan-listing"></a>Elenco di piani

Per prima cosa, fornire un **Riepilogo dei risultati della ricerca** per il piano. Si tratta di una breve descrizione del piano (fino a 100 caratteri), che può essere usata nei risultati della ricerca nel Marketplace.

Immettere quindi una **Descrizione** che fornisca una spiegazione più dettagliata del piano.

### <a name="pricing-and-availability"></a>Prezzi e disponibilità

Attualmente è disponibile un solo modello di determinazione dei prezzi che può essere usato per l'offerta di servizio gestito: **Bring your own License (BYOL)**. Ciò significa che i costi relativi a questa offerta verranno fatturati direttamente ai clienti e non si riceverà alcun addebito da parte di Microsoft.

La sezione **visibilità del piano** consente di indicare se il piano deve essere [privato](../../marketplace/private-offers.md). Se si lascia deselezionata questa casella di **piano privata** , il piano non verrà limitato a specifici clienti o a un certo numero di clienti.

Per rendere questo piano disponibile solo a clienti specifici, selezionare **Sì**. Quando si esegue questa operazione, è necessario identificare i clienti fornendo gli ID sottoscrizione. Questi possono essere immessi uno alla volta (per un massimo di 10 sottoscrizioni) o caricando un file CSV (per un massimo di 10.000 sottoscrizioni in tutti i piani). Assicurarsi di includere qui le sottoscrizioni per poter testare e convalidare l'offerta.

> [!IMPORTANT]
> Quando un piano è stato pubblicato come pubblico, non è possibile modificarlo in privato. Per controllare quali clienti possono accettare l'offerta e delegare le risorse, usare un piano privato. Con un piano pubblico, non è possibile limitare la disponibilità a determinati clienti o anche a un certo numero di clienti (sebbene sia possibile smettere completamente di vendere il piano se si sceglie di farlo). È possibile [rimuovere l'accesso a una delega](../../lighthouse/how-to/remove-delegation.md) dopo che un cliente accetta un'offerta solo se è stata inclusa un' **autorizzazione** con la **definizione del ruolo** impostata sul [ruolo eliminazione di assegnazione di registrazione dei servizi gestiti](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) quando è stata pubblicata l'offerta. È anche possibile contattare il cliente e chiedere di [rimuovere l'accesso](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

### <a name="technical-configuration"></a>Configurazione tecnica

In questa sezione del piano viene creato un manifesto con le informazioni di autorizzazione per la gestione delle risorse dei clienti. Queste informazioni sono necessarie per abilitare la [gestione delle risorse delegate di Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).

Assicurarsi di esaminare i [tenant, i ruoli e gli utenti negli scenari di Azure Lighthouse](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) per comprendere quali ruoli sono supportati e le procedure consigliate per la definizione delle autorizzazioni.

> [!NOTE]
> Come indicato in precedenza, gli utenti e i ruoli nelle voci di **Autorizzazione** vengono applicati a ogni cliente che acquista il piano. Se si vuole limitare l'accesso a un cliente specifico, è necessario pubblicare un piano privato per l'uso esclusivo.

#### <a name="manifest"></a>manifesto

Specificare prima di tutto una **versione** per il manifesto. Usare il formato *n.n.n* (ad esempio, 1.2.5).

Immettere quindi l'**ID tenant**. GUID associato all'ID tenant Azure Active Directory (Azure AD) dell'organizzazione; ovvero il tenant di gestione da cui si accederà alle risorse dei clienti. Se non lo si ha disposizione, è possibile trovarlo passando il puntatore sul nome dell'account nell'angolo in alto a destra del portale di Azure o selezionando **Cambia directory**.

Se si pubblica una nuova versione dell'offerta ed è necessario creare un manifesto aggiornato, selezionare **+ nuovo manifesto**. Assicurarsi di aumentare il numero di versione dalla versione precedente del manifesto.

#### <a name="authorization"></a>Autorizzazione

Le autorizzazioni definiscono le entità nel tenant di gestione che possono accedere alle risorse e alle sottoscrizioni per i clienti che acquistano il piano. A ognuna di queste entità viene assegnato un ruolo predefinito che concede livelli di accesso specifici.

È possibile creare fino a venti autorizzazioni per ogni piano.

> [!TIP]
> Nella maggior parte dei casi, è consigliabile assegnare i ruoli a un gruppo di utenti o un'entità servizio di Azure AD, anziché a una serie di singoli account utente. In questo modo è possibile aggiungere o rimuovere l'accesso per i singoli utenti senza dover aggiornare e ripubblicare il piano quando cambiano i requisiti di accesso. Quando si assegnano ruoli a gruppi di Azure AD, [assicurarsi che il **tipo di gruppo** sia **sicurezza** e non **Office 365**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Per altri consigli, vedere [Tenant, ruoli e utenti negli scenari di Azure Lighthouse](../../lighthouse/concepts/tenants-users-roles.md).

Per ogni valore di **Autorizzazione**, è necessario specificare gli elementi seguenti. È quindi possibile selezionare **+ Aggiungi autorizzazione** il numero di volte necessario per aggiungere altri utenti e definizioni di ruolo.

- **ID oggetto Azure ad**: identificatore Azure ad di un utente, di un gruppo di utenti o di un'applicazione a cui verranno concesse determinate autorizzazioni (definite dalla definizione di ruolo) per le risorse dei clienti.
- **Azure ad nome visualizzato dell'oggetto**: un nome descrittivo per aiutare il cliente a comprendere lo scopo di questa autorizzazione. Questo nome verrà visualizzato dal cliente durante la delega delle risorse.
- **Definizione del ruolo**: selezionare uno dei ruoli predefiniti Azure ad disponibili nell'elenco. Questo ruolo determinerà le autorizzazioni che l'utente indicato nel campo **ID oggetto Azure AD** avrà per le risorse dei clienti. Per le descrizioni di questi ruoli, vedere [ruoli predefiniti](../../role-based-access-control/built-in-roles.md) e supporto dei ruoli [per la gestione delle risorse delegate di Azure](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management).
  > [!NOTE]
  > Poiché i nuovi ruoli predefiniti applicabili vengono aggiunti ad Azure, diventeranno disponibili qui, anche se potrebbe verificarsi un certo ritardo prima che vengano visualizzati.
- **Ruoli assegnabili**: questa opzione viene visualizzata solo se è stato selezionato amministratore accesso utenti nella **definizione del ruolo** per l'autorizzazione. In tal caso, è necessario aggiungere qui uno o più ruoli assegnabili. L'utente nel campo **Azure ad ID oggetto** sarà in grado di assegnare questi ruoli alle [identità gestite](../../active-directory/managed-identities-azure-resources/overview.md), operazione necessaria per [distribuire i criteri che possono essere corretti](../../lighthouse/how-to/deploy-policy-remediation.md). Si noti che nessun'altra autorizzazione normalmente associata al ruolo Amministratore Accesso utenti verrà applicata a questo utente.

> [!TIP]
> Per assicurarsi che sia possibile [rimuovere l'accesso a una delega](../../lighthouse/how-to/remove-delegation.md) , se necessario, includere un' **autorizzazione** con la **definizione del ruolo** impostata sul [ruolo eliminazione di assegnazione di registrazione dei servizi gestiti](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Se questo ruolo non viene assegnato, le risorse delegate possono essere rimosse solo da un utente che si trova nel tenant del cliente.

Una volta completate tutte le sezioni del piano, è possibile selezionare **+ Crea nuovo piano** tutte le volte che è necessario creare piani aggiuntivi. Al termine, selezionare **Salva**.

## <a name="publish"></a>Pubblica

### <a name="submit-offer-to-preview"></a>Invia l'offerta all'anteprima

Una volta completate tutte le sezioni obbligatorie dell'offerta, selezionare **pubblica** nell'angolo superiore destro del portale. Si verrà reindirizzati alla pagina di **revisione e pubblicazione** .

Se è la prima volta che si pubblica questa offerta, è possibile:

- Vedere lo stato di completamento di ogni sezione dell'offerta.
  - *Non avviato* : indica che la sezione non è stata toccata e deve essere completata.
  - *Incompleto* : indica che la sezione contiene errori che devono essere corretti o che sono necessarie altre informazioni. Tornare alla sezione o aggiornarla.
  - *Complete* : indica che la sezione è completa, sono stati forniti tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere in uno stato completo prima di poter inviare l'offerta.
- Nella sezione **Note per la certificazione** , fornire istruzioni di test al team di certificazione per assicurarsi che l'app venga testata correttamente, oltre a eventuali note aggiuntive utili per comprendere l'app.
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Ti invieremo un messaggio di posta elettronica quando sarà disponibile una versione di anteprima dell'offerta da rivedere e approvare. Tornare al centro per i partner e selezionare **Go-Live** per l'offerta per pubblicare l'offerta al pubblico (o se un'offerta privata, per i destinatari privati).

### <a name="customer-experience-and-offer-management"></a>Gestione dell'esperienza utente e dell'offerta

Quando un cliente distribuisce l'offerta, sarà in grado di delegare le sottoscrizioni o i gruppi di risorse per la [gestione delle risorse delegate di Azure](../../lighthouse/concepts/azure-delegated-resource-management.md). Per ulteriori informazioni su questo processo, vedere [il processo di onboarding del cliente](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process).

È possibile [pubblicare una versione aggiornata dell'offerta](update-existing-offer.md) in qualsiasi momento. Ad esempio, potrebbe essere necessario aggiungere una nuova definizione di ruolo a un'offerta pubblicata in precedenza. Quando si esegue questa operazione, i clienti che hanno già aggiunto l'offerta vedranno un'icona nella pagina [**Provider di servizi**](../../lighthouse/how-to/view-manage-service-providers.md) del portale di Azure che consente di sapere che è disponibile un aggiornamento. Ogni cliente sarà in grado di rivedere le modifiche e decidere se eseguire l'aggiornamento alla nuova versione.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
- [Scopri di più su Azure Lighthouse](../../lighthouse/overview.md)