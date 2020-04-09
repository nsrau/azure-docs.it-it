---
title: Creare una nuova offerta di managed service nel Marketplace commerciale
description: Come creare una nuova offerta del servizio gestito per la presentazione in Azure Marketplace usando il portale del Marketplace commerciale nel Centro per i partner.
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: de6e08534722deccb8be11248dd8af3ca2e66739
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983213"
---
# <a name="create-a-new-managed-service-offer"></a>Creare una nuova offerta del servizio gestitoCreate a new Managed Service offer

> [!IMPORTANT]
> Stiamo spostando la gestione delle offerte di servizi gestiti dal portale Cloud Partner al Centro per i partner. Fino a quando non viene eseguita la migrazione delle offerte, seguire le istruzioni in [Pubblicare un'offerta](../../lighthouse/how-to/publish-managed-services-offers.md) di servizio gestito in Azure Marketplace per gestire le offerte.

Il servizio gestito offre assistenza per abilitare gli scenari [Faro di Azure.Managed Service](../../lighthouse/overview.md) offers help to enable Azure Lighthouse scenarios. Quando un cliente accetta un'offerta del servizio gestito, è quindi in grado di onboard delle risorse per la gestione delle risorse delegate di [Azure.](../../lighthouse/concepts/azure-delegated-resource-management.md)

Per iniziare a creare offerte di Managed Service, assicurarsi di creare prima [un account del Centro per](./create-account.md) i partner e aprire il dashboard Commercial [Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)con la pagina **Panoramica** selezionata. È necessario disporre di un livello di [competenza Silver o Gold Cloud Platform](https://partner.microsoft.com/membership/cloud-platform-competency) o essere un MSP di Azure [Expert](https://partner.microsoft.com/membership/azure-expert-msp) per pubblicare un'offerta del servizio gestito.

![Dashboard Marketplace commerciale nel Centro per i partner](./media/new-offer-overview.png)

>[!Note]
> Una volta pubblicata un'offerta, le modifiche apportate all'offerta effettuata nel Centro per i partner verranno aggiornate nel sistema e archiviate solo dopo la ripubblicazione. Assicurati di inviare l'offerta per la pubblicazione dopo aver apportato le modifiche.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

Seleziona il pulsante **" Nuova offerta",** quindi seleziona la voce di menu **Servizio gestito.** Verrà visualizzata la finestra di dialogo **Nuova offerta.**

### <a name="offer-id-and-alias"></a>ID e alias dell'offerta

- **ID offerta**: Identificatore univoco per ogni offerta nel tuo account. Questo ID sarà visibile ai clienti nell'indirizzo URL dell'offerta del marketplace. Questo ID può contenere solo caratteri alfanumerici minuscoli (inclusi trattini e caratteri di sottolineatura, ma senza spazi vuoti), limitati a 50 caratteri e non può essere modificato dopo aver selezionato **Crea**.  Ad esempio, se si immette *test-offer-1* in `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`questo campo, l'URL dell'offerta sarà .

- **Alias dell'offerta:** nome utilizzato per fare riferimento all'offerta all'interno del Centro per i partner. Questo nome non verrà utilizzato nel marketplace ed è diverso dal nome dell'offerta e da altri valori che verranno visualizzati ai clienti. Questo valore non può essere modificato dopo aver selezionato **Crea**.

Una volta inseriti **l'ID offerta** e **l'alias dell'offerta,** selezionare **Crea**. Potrai quindi lavorare su tutte le diverse parti della tua offerta.

## <a name="offer-setup"></a>Configurazione dell'offerta

Nella pagina **Impostazione offerta** vengono richieste le seguenti informazioni. Assicurarsi di selezionare **Salva** dopo aver completato questi campi.

## <a name="connect-lead-management"></a>Gestione dei lead di Connect

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Si noti che in base ai [criteri di certificazione dei servizi gestiti](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services), è necessaria una **destinazione del lead**. Verrà creato un record nel sistema CRM ogni volta che un cliente distribuisce l'offerta.

Per ulteriori informazioni, vedere [Panoramica](./commercial-marketplace-get-customer-leads.md)della gestione dei lead .

Ricordarsi di **salvare** la pagina prima di passare alla sezione successiva.

## <a name="properties"></a>Proprietà

La pagina **Proprietà** consente di definire le categorie utilizzate per raggruppare l'offerta sul marketplace e i contratti legali a supporto dell'offerta. Seleziona **Salva** dopo aver completato questa pagina.

### <a name="category"></a>Category

Seleziona un minimo di una e un massimo di cinque categorie che verranno utilizzate per inserire l'offerta nelle aree di ricerca del marketplace appropriate. Assicurati di sapere come la tua offerta supporta queste categorie nella descrizione dell'offerta.

### <a name="terms-and-conditions"></a>Termini e condizioni

Fornire i propri termini e condizioni legali nel campo **Termini e condizioni.** Puoi anche fornire l'URL in cui trovare i termini e le condizioni. I clienti saranno tenuti ad accettare questi termini prima di poter provare la tua offerta.

## <a name="offer-listing"></a>Inserzione dell'offerta

La pagina **Inserzione dell'offerta** ti consente di definire i dettagli del marketplace (nome dell'offerta, descrizione, immagini, ecc.) per la tua offerta.

> [!NOTE]
> Il contenuto dell'elenco dell'offerta (come la descrizione, i documenti, le schermate, le condizioni per l'utilizzo, ecc.) non è necessario per essere in inglese, purché la descrizione dell'offerta inizi con la frase "Questa applicazione è disponibile solo in [lingua non in lingua inglese]". È inoltre accettabile fornire un *URL di collegamento utile* per offrire contenuti in una lingua diversa da quella utilizzata nel contenuto della scheda Offerta.

### <a name="name"></a>Nome

Il nome che inserisci qui verrà mostrato ai clienti come titolo della tua offerta. Questo campo viene prepopolato con il testo immesso per **l'alias dell'offerta** al momento della creazione dell'offerta, ma è possibile modificare questo valore. Questo nome può essere registrato (e l'utente può includere simboli di marchio o copyright). Il nome non può contenere più di 50 caratteri e non può includere emoji.

### <a name="search-results-summary"></a>Riepilogo dei risultati della ricerca

Fornisci una breve descrizione dell'offerta (fino a 100 caratteri), che può essere utilizzata nei risultati di ricerca del marketplace.

### <a name="long-summary"></a>Riassunto lungo

Fornisci una descrizione più lunga della tua offerta (fino a 256 caratteri). Questo lungo riepilogo può essere utilizzato anche nei risultati di ricerca del marketplace.

### <a name="description"></a>Descrizione

Fornisci una descrizione più lunga della tua offerta (fino a 3.000 caratteri). Questa descrizione verrà visualizzata ai clienti nella panoramica dell'elenco dei marketplace. Includi la proposta di valore della tua offerta, i vantaggi principali, le associazioni di categoria e/o di settore, le opportunità di acquisto in-app e le eventuali informazioni richieste.

Alcuni suggerimenti per scrivere la descrizione:  

- Descrivere chiaramente la proposta di valore dell'offerta subito nelle prime frasi della descrizione. Includi i seguenti elementi nella tua proposta di valore:
  - Descrizione dell'offerta
  - Il tipo di utente che beneficia dell'offerta
  - Esigenze o dolore del cliente affrontate dall'offerta
- Tenere presente che le prime frasi possono essere visualizzate nei risultati dei motori di ricerca.  
- Per vendere un prodotto, non basarsi sulle funzionalità, ma concentrarsi sul valore che può offrire.  
- Usare il più possibile la terminologia specifica del settore o formule che mettano in evidenza i vantaggi offerti.

Per rendere più coinvolgente la descrizione dell'offerta, usa l'editor RTF per formattare la descrizione.

![Utilizzo dell'editor DI testo RTF](./media/text-editor2.png)

Utilizzare le istruzioni seguenti per utilizzare l'editor RTF:

- Per modificare il formato del contenuto, evidenziare il testo da formattare e selezionare uno stile di testo, come illustrato di seguito:

     ![Utilizzo dell'editor RTF per modificare il formato del testo](./media/text-editor3.png)

- Per aggiungere un elenco puntato o numerato al testo, utilizzare le opzioni seguenti:

     ![Utilizzo dell'editor RTF per aggiungere elenchi](./media/text-editor4.png)

- Per aggiungere o rimuovere il rientro del testo, utilizzare le opzioni seguenti:

     ![Utilizzo dell'editor RTF per il rientro](./media/text-editor5.png)

### <a name="privacy-policy-link"></a>Collegamento all'informativa sulla privacy

Immettere l'URL dell'informativa sulla privacy dell'organizzazione (ospitato nel sito). L'utente è responsabile di garantire che l'app sia conforme alle leggi e ai regolamenti sulla privacy e di fornire una valida informativa sulla privacy.

### <a name="useful-links"></a>Collegamenti utili

Fornire documenti online supplementari facoltativi sulla soluzione. Aggiungere ulteriori collegamenti utili facendo clic su **Aggiungi un collegamento**.

### <a name="contact-information"></a>Informazioni sul contatto

In questa sezione è necessario specificare il nome, l'indirizzo di posta elettronica e il numero di telefono di un **contatto di supporto** e di un contatto **Engineering**. Queste informazioni non vengono mostrate ai clienti, ma saranno disponibili per Microsoft e possono essere fornite ai partner CSP.

### <a name="support-urls"></a>URL di supporto

Se si dispone di siti Web di supporto per **i clienti globali** di Azure e/o per enti pubblici di **Azure,** fornire tali URL qui.

### <a name="marketplace-images"></a>Immagini del Marketplace

In questa sezione, è possibile fornire loghi e immagini che verranno utilizzati quando si mostra l'offerta al cliente. Tutte le immagini devono essere in formato PNG.

#### <a name="marketplace-logos"></a>Loghi del Marketplace

Sono necessarie quattro formati di logo: **Piccolo (40x40),** **Medio (90x90)**, **Grande (115x115)** e **Wide (255x115)**. Seguire queste linee guida per i logo:

- La progettazione di Azure ha una tavolozza dei colori semplice. Limitare il numero di colori primari e secondari nel logo.
- I colori del tema del portale sono il bianco e il nero. Non usare questi colori per lo sfondo del logo. Usare un colore che faccia risaltare il logo nel portale. Si consiglia di usare colori primari semplici.
- Se si usa uno sfondo trasparente, verificare che i logo e il testo non siano bianchi, neri o blu.
- L'aspetto del logo deve essere semplice e senza sfumature. Non usare uno sfondo sfumato sul logo.
- Non inserire testo, nemmeno il nome del marchio o della società, sul logo.
- Verificare che il logo non venga adattato.

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungi fino a cinque screenshot che mostrano come funziona la tua offerta. Tutte le schermate devono essere 1280 x 720 pixel.

#### <a name="videos"></a>Video

Facoltativamente, puoi aggiungere fino a cinque video che dimostrano la tua offerta. Questi video devono essere ospitati su YouTube e/o Vimeo. Per ognuno di essi, inserisci il nome del video, il suo URL e un'immagine di anteprima del video (1280 x 720 pixel).

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per la presentazione del marketplace

- [Procedure consigliate per le inserzioni con offerte di marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Anteprima

Prima di pubblicare la tua offerta in diretta nell'offerta più ampia del marketplace, devi prima renderla disponibile a un pubblico di anteprima limitato. Ciò consente di verificare la modalità di visualizzazione dell'offerta in Azure Marketplace prima di renderla disponibile ai clienti. I team di supporto e progettazione Microsoft saranno inoltre in grado di visualizzare l'offerta durante questo periodo di anteprima.

È possibile definire il gruppo di destinatari di anteprima immettendo gli ID sottoscrizione di Azure nella sezione Anteprima gruppo di **destinatari.** È possibile immettere manualmente fino a 10 ID sottoscrizione oppure caricare un file CSV con un massimo di 100 ID sottoscrizione.

Tutti i clienti associati a queste sottoscrizioni potranno visualizzare l'offerta in Azure Marketplace prima che venga disponibile. Assicurati di includere i tuoi abbonamenti qui in modo da poter visualizzare in anteprima la tua offerta.

## <a name="plan-overview"></a>Panoramica del piano

Ogni offerta deve avere uno o più piani, detti anche SKU. È possibile aggiungere più piani per supportare set di funzionalità diversi a prezzi diversi o per personalizzare un piano specifico per un numero limitato di clienti specifici. I clienti possono visualizzare i piani disponibili nell'offerta padre.

Nella pagina **Panoramica piano** selezionare **Crea nuovo piano**. Immettere quindi un **ID piano** e un nome **di piano**. Entrambi questi valori possono contenere solo caratteri alfanumerici minuscoli, trattini e caratteri di sottolineatura, con un massimo di 50 caratteri. Questi valori possono essere visibili ai clienti e non possono essere modificati dopo la pubblicazione dell'offerta.

Selezionare **Crea** dopo aver immesso questi valori per continuare a lavorare sul piano. Ci sono tre sezioni da completare: **Plan listing**, Pricing **and availability**e Technical **configuration**.

### <a name="plan-listing"></a>Pianificare l'inserzione

In primo luogo, fornire un **riepilogo dei risultati** di ricerca per il piano. Questa è una breve descrizione del piano (fino a 100 caratteri), che può essere utilizzata nei risultati di ricerca del marketplace.

Immettere quindi una **Descrizione** che fornisca una spiegazione più dettagliata del piano.

### <a name="pricing-and-availability"></a>Prezzi e disponibilità

Attualmente, esiste un solo modello di determinazione dei prezzi che può essere utilizzato per l'offerta del servizio gestito: **Portare la propria licenza (BYOL)**. Ciò significa che i costi relativi a questa offerta verranno fatturati direttamente ai clienti e non si riceverà alcun addebito da parte di Microsoft.

La sezione **Visibilità piano** consente di indicare se il piano deve essere [privato.](../../marketplace/private-offers.md) Se si esce dalla casella **Questo è un piano privato** non è selezionata, il piano non sarà limitato a clienti specifici (o a un determinato numero di clienti).

Per rendere questo piano disponibile solo a clienti specifici, selezionare **Sì**. Quando si esegue questa operazione, è necessario identificare i clienti fornendo gli ID sottoscrizione. Questi possono essere immessi uno per uno (per un massimo di 10 abbonamenti) o caricando un file CSV (per un massimo di 10.000 abbonamenti in tutti i piani). Assicurarsi di includere qui le sottoscrizioni per poter testare e convalidare l'offerta.

> [!IMPORTANT]
> Una volta che un piano è stato pubblicato come pubblico, non è possibile modificarlo in privato. Per controllare quali clienti possono accettare l'offerta e delegare le risorse, utilizzare un piano privato. Con un piano pubblico, non puoi limitare la disponibilità a determinati clienti o anche a un certo numero di clienti (anche se puoi smettere di vendere completamente il piano se scegli di farlo). È possibile [rimuovere l'accesso a una delega](../../lighthouse/how-to/onboard-customer.md#remove-access-to-a-delegation) dopo che un cliente ha accettato un'offerta solo se è stata inclusa un'autorizzazione con la **definizione** di ruolo impostata su Ruolo di eliminazione assegnazione registrazione [servizi gestiti](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) al momento della pubblicazione dell'offerta. **Authorization** Puoi anche contattare il cliente e chiedere loro di [rimuovere il tuo accesso.](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)

### <a name="technical-configuration"></a>Configurazione tecnica

Questa sezione del piano crea un manifesto con informazioni di autorizzazione per la gestione delle risorse dei clienti. Queste informazioni sono necessarie per abilitare la gestione delle risorse delegate di [Azure.](../../lighthouse/concepts/azure-delegated-resource-management.md)

Assicurarsi di esaminare [tenant, ruoli e utenti negli scenari Lighthouse](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) di Azure per comprendere quali ruoli sono supportati e le procedure consigliate per la definizione delle autorizzazioni.

> [!NOTE]
> Come indicato in precedenza, gli utenti e i ruoli nelle voci di **Autorizzazione** vengono applicati a ogni cliente che acquista il piano. Se si vuole limitare l'accesso a un cliente specifico, è necessario pubblicare un piano privato per l'uso esclusivo.

#### <a name="manifest"></a>manifesto

Specificare prima di tutto una **versione** per il manifesto. Usare il formato *n.n.n* (ad esempio, 1.2.5).

Immettere quindi l'**ID tenant**. Si tratta di un GUID associato all'ID tenant di Azure Active Directory (Azure AD) dell'organizzazione. vale a dire, il tenant di gestione da cui si accederà alle risorse dei clienti. Se non lo si ha disposizione, è possibile trovarlo passando il puntatore sul nome dell'account nell'angolo in alto a destra del portale di Azure o selezionando **Cambia directory**.

Se si pubblica una nuova versione dell'offerta ed è necessario creare un manifesto aggiornato, selezionare **Nuovo manifesto**. Assicurarsi di aumentare il numero di versione della versione precedente del manifesto.

#### <a name="authorization"></a>Autorizzazione

Le autorizzazioni definiscono le entità nel tenant di gestione che possono accedere alle risorse e alle sottoscrizioni per i clienti che acquistano il piano. A ognuna di queste entità viene assegnato un ruolo predefinito che concede livelli di accesso specifici.

È possibile creare fino a venti autorizzazioni per ogni piano.

> [!TIP]
> Nella maggior parte dei casi, è consigliabile assegnare ruoli a un gruppo di utenti o a un'entità servizio di Azure AD, anziché a una serie di singoli account utente. In questo modo è possibile aggiungere o rimuovere l'accesso per i singoli utenti senza dover aggiornare e ripubblicare il piano quando cambiano i requisiti di accesso. Quando si assegnano ruoli ai gruppi di Azure AD, assicurarsi che il tipo di [ **gruppo** sia **Sicurezza** e non **Office 365.**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) Per altri consigli, vedere [Tenant, ruoli e utenti negli scenari di Azure Lighthouse](../../lighthouse/concepts/tenants-users-roles.md).

Per ogni valore di **Autorizzazione**, è necessario specificare gli elementi seguenti. È quindi possibile selezionare **Aggiungi autorizzazione** tutte le volte che è necessario per aggiungere altri utenti e definizioni di ruolo.

- **ID oggetto di Azure AD:** identificatore di Azure AD di un utente, un gruppo di utenti o un'applicazione a cui verranno concesse determinate autorizzazioni (definite dalla definizione del ruolo) alle risorse dei clienti.
- **Nome visualizzato oggetto Azure AD:** un nome descrittivo per consentire al cliente di comprendere lo scopo di questa autorizzazione. Questo nome verrà visualizzato dal cliente durante la delega delle risorse.
- **Definizione ruolo**: selezionare uno dei ruoli predefiniti di Azure AD disponibili dall'elenco. Questo ruolo determinerà le autorizzazioni che l'utente indicato nel campo **ID oggetto Azure AD** avrà per le risorse dei clienti. Per le descrizioni di questi ruoli, vedere [Ruoli predefiniti](../../role-based-access-control/built-in-roles.md) e Supporto dei ruoli per la gestione delle risorse delegate di [Azure.](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management)
  > [!NOTE]
  > Man mano che i nuovi ruoli predefiniti applicabili vengono aggiunti ad Azure, questi saranno disponibili qui, anche se potrebbe verificarsi un ritardo prima che vengano visualizzati.
- **Ruoli assegnabili**: Questa opzione viene visualizzata solo se è stato selezionato Amministratore accesso utenti nella **definizione** del ruolo per questa autorizzazione. In tal caso, è necessario aggiungere qui uno o più ruoli assegnabili. L'utente nel campo **ID oggetto** di Azure AD sarà in grado di assegnare questi ruoli alle [identità gestite,](../../active-directory/managed-identities-azure-resources/overview.md)operazione necessaria per [distribuire criteri che possono essere corretti.](../../lighthouse/how-to/deploy-policy-remediation.md) Si noti che nessun'altra autorizzazione normalmente associata al ruolo Amministratore Accesso utenti verrà applicata a questo utente.

> [!TIP]
> Per assicurarsi di poter [rimuovere l'accesso a una delega,](../../lighthouse/how-to/onboard-customer.md#remove-access-to-a-delegation) se necessario, includere un'autorizzazione con la **definizione** del ruolo impostata su Ruolo eliminazione [assegnazione registrazione servizi gestiti](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). **Authorization** Se questo ruolo non viene assegnato, le risorse delegate possono essere rimosse solo da un utente che si trova nel tenant del cliente.

Dopo aver completato tutte le sezioni per il piano, è possibile selezionare **Crea nuovo piano** tutte le volte che è necessario creare piani aggiuntivi. Al termine, selezionare **Salva**.

## <a name="publish"></a>Pubblica

### <a name="submit-offer-to-preview"></a>Invia l'offerta all'anteprima

Dopo aver completato tutte le sezioni richieste dell'offerta, selezionare **Pubblica** nell'angolo superiore destro del portale. Si verrà reindirizzati alla pagina **Revisione e pubblicazione.**

Se è la prima volta che pubblichi questa offerta, puoi:

- Vedi lo stato di completamento per ogni sezione dell'offerta.
  - *Non avviato* - significa che la sezione non è stata toccata e deve essere completata.
  - *Incompleto* - significa che la sezione contiene errori che devono essere corretti o che richiedono ulteriori informazioni. Tornare alle sezioni e aggiornarle.
  - *Completo* - significa che la sezione è completa, tutti i dati richiesti sono stati forniti e non ci sono errori. Tutte le sezioni dell'offerta devono essere in uno stato completo prima di poter inviare l'offerta.
- Nella sezione Note per la **certificazione** fornire istruzioni di test al team di certificazione per assicurarsi che l'app sia testata correttamente, oltre a eventuali note aggiuntive utili per comprendere l'app.
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Ti invieremo un'email quando è disponibile una versione di anteprima dell'offerta da esaminare e approvare. Torna al Centro per i partner e seleziona **Go-live** per l'offerta di pubblicare la tua offerta al pubblico (o se un'offerta privata, al pubblico privato).

### <a name="customer-experience-and-offer-management"></a>Esperienza del cliente e gestione delle offerte

Quando un cliente distribuisce l'offerta, sarà in grado di delegare sottoscrizioni o gruppi di risorse per la gestione delle risorse delegate di [Azure.](../../lighthouse/concepts/azure-delegated-resource-management.md) Per ulteriori informazioni su questo processo, vedere Processo di [onboarding del cliente](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process).

È possibile [pubblicare una versione aggiornata dell'offerta](update-existing-offer.md) in qualsiasi momento. Ad esempio, potrebbe essere necessario aggiungere una nuova definizione di ruolo a un'offerta pubblicata in precedenza. Quando si esegue questa operazione, i clienti che hanno già aggiunto l'offerta vedranno un'icona nella pagina [**Provider di servizi**](../../lighthouse/how-to/view-manage-service-providers.md) del portale di Azure che consente di sapere che è disponibile un aggiornamento. Ogni cliente sarà in grado di rivedere le modifiche e decidere se eseguire l'aggiornamento alla nuova versione.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
- [Scopri di più sul faro di Azure](../../lighthouse/overview.md)