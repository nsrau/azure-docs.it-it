---
title: Configurare un'applicazione OpenID/OAuth dalla raccolta di app di Azure AD | Microsoft Docs
description: Passaggi per configurare un'applicazione OpenID/OAuth dalla raccolta di app di Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 713e4e7874b2ca650ab669d52f9d3026b5e80899
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780984"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Configurare un'applicazione OpenID/OAuth dalla raccolta di app di Azure AD

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>Processo per aggiungere un'applicazione OpenID dalla raccolta

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory](common/select-azuread.png))

2. Passare ad **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare il nome dell'applicazione. Selezionare l'applicazione desiderata dal pannello dei risultati e iscriversi all'applicazione.

    ![OpenID nell'elenco dei risultati](common/search-new-app.png)

    > [!NOTE]
    > Per le app OpenID Connect e OAuth il pulsante **Aggiungi** è disabilitato per impostazione predefinita. In questo caso l'amministratore del tenant deve selezionare il pulsante di iscrizione e fornire il consenso all'applicazione. L'applicazione viene quindi aggiunta al tenant del cliente, in cui è possibile eseguire le configurazioni. Non è necessario aggiungere l'applicazione in modo esplicito.

    ![Pulsante Aggiungi](./media/openidoauth-tutorial/addbutton.png)

5. Quando si seleziona il collegamento di iscrizione, si viene reindirizzati alla pagina di Azure Active Directory (Azure AD) per le credenziali di accesso.

6. Una volta eseguita l'autenticazione, l'utente accetta il consenso dalla relativa pagina. Viene quindi visualizzata la home page dell'applicazione.

    > [!NOTE]
    > È possibile aggiungere solo un'istanza dell'applicazione. Se ne è già stata aggiunta una e si è tentato di fornire nuovamente il consenso, questa non verrà aggiunta una seconda volta nel tenant. È quindi possibile usare logicamente solo un'istanza di app nel tenant.

## <a name="authentication-flow-using-openid-connect"></a>Flusso di autenticazione con OpenID Connect

Il flusso di accesso di base include i passaggi seguenti:

![Flusso di autenticazione con OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Applicazione multi-tenant 
Un'applicazione multi-tenant è destinata all'uso in molte organizzazioni, non solo in una. Si tratta generalmente di applicazioni SaaS (Software-as-a-Service) scritte da un fornitore di software indipendente (ISV). 

Le applicazioni multi-tenant devono essere sottoposte a provisioning in ogni directory in cui verranno usate. Per la registrazione, richiedono il consenso dell'utente o dell'amministratore. Questo processo di consenso inizia quando un'applicazione viene registrata nella directory e può accedere all'API Graph o magari a un'altra API Web. Quando un utente o un amministratore di un'altra organizzazione esegue l'accesso per usare l'applicazione, viene visualizzata una finestra di dialogo in cui sono indicate le autorizzazioni richieste dall'applicazione. 

L'utente o l'amministratore può a questo punto fornire il consenso all'applicazione. Il consenso conferisce all'applicazione l'accesso ai dati indicati e la registra nella directory.

> [!NOTE]
> Se l'applicazione viene resa disponibile agli utenti in più directory, è necessario un meccanismo per determinare il tenant attivo. Un'applicazione a tenant singolo deve cercare un utente solo nella propria directory. Un'applicazione multi-tenant deve identificare un utente specifico in tute le directory in Azure AD.
> 
> A questo scopo, Azure AD offre un endpoint di autenticazione comune in cui qualsiasi applicazione multi-tenant può indirizzare le richieste di accesso, invece di un endpoint specifico di un tenant. Questo endpoint è [https://login.microsoftonline.com/common](https://login.microsoftonline.com/common) per tutte le directory di Azure AD. Un endpoint specifico del tenant può essere ad esempio [https://login.microsoftonline.com/contoso.onmicrosoft.com](https://login.microsoftonline.com/contoso.onmicrosoft.com). 
>
> L'endpoint comune è importante da considerare quando si sviluppa l'applicazione. È necessaria la logica richiesta per gestire più tenant durante l'accesso, la disconnessione e la convalida del token.

Per impostazione predefinita, Azure AD promuove le applicazioni multi-tenant. È facile accedervi tra organizzazioni diverse e sono facili da usare dopo aver accettato il consenso.

## <a name="consent-framework"></a>Framework di consenso

È possibile usare il framework di consenso di Azure AD per sviluppare applicazioni client Web multi-tenant e native. Queste applicazioni consentono l'accesso da parte degli account utente da un tenant di Azure AD diverso da quello in cui l'applicazione è registrata. Potrebbe dover accedere anche ad API Web, ad esempio:
- L'API Graph di Microsoft per accedere ad Azure AD, Intune e ai servizi di Office 365. 
- Altre API di servizi Microsoft.
- API Web personalizzate. 

Il framework è basato sulla possibilità per un utente o un amministratore di fornire il consenso alla richiesta di registrazione di un'applicazione nella propria directory, che può comportare l'accesso a dati di directory. Una volta ottenuto il consenso, l'applicazione client può chiamare l'API Graph di Microsoft per conto dell'utente e usare le informazioni nel modo necessario.

L'[API Graph di Microsoft](https://developer.microsoft.com/graph/) garantisce l'accesso ai dati in Office 365, ad esempio:

- Calendari e messaggi di Exchange.
- Siti ed elenchi di SharePoint.
- Documenti di OneDrive.
- Blocchi appunti di OneNote.
- Attività di Planner.
- Cartelle di lavoro di Excel.

L'API Graph conferisce inoltre l'accesso a utenti e gruppi da Azure AD e ad altri oggetti dati da più servizi cloud Microsoft.

I passaggi seguenti illustrano il funzionamento dell'esperienza di consenso per lo sviluppatore e l'utente dell'applicazione:

1. Si supponga di avere un'applicazione client Web che deve richiedere autorizzazioni specifiche per accedere a una risorsa o un'API. Il portale di Azure viene usato per dichiarare le richieste di autorizzazione al momento della configurazione. Come altre impostazioni di configurazione, diventano parte della registrazione dell'applicazione in Azure Active Directory:

    ![API Graph](./media/openidoauth-tutorial/graphapi.png)

2. Considerare che le autorizzazioni dell'applicazione siano state aggiornate. L'applicazione è in esecuzione e un utente sta per usarla per la prima volta. L'applicazione deve prima ottenere un codice di autorizzazione dall'endpoint di Azure AD/dell'autorizzazione. Il codice di autorizzazione può quindi essere usato per acquisire un nuovo un token di accesso e aggiornamento.

3. Se l'utente non è già autenticato, l'endpoint di Azure AD/dell'autorizzazione richiede l'accesso.

    ![Authentication](./media/openidoauth-tutorial/authentication.png)

4. Dopo che l'utente ha effettuato l'accesso, Azure AD determinerà se l'utente deve essere reindirizzato a una pagina di consenso. Questa decisione dipende dal fatto che l'utente o l'amministratore dell'organizzazione abbia o meno già concesso il consenso dell'applicazione.

   Se il consenso non è stato concesso, Azure AD lo richiede all'utente e visualizza le autorizzazioni necessarie per il funzionamento. Le autorizzazioni visualizzate nella finestra di dialogo di consenso corrispondono a quelle selezionate nelle autorizzazioni delegate nel portale di Azure.

    ![Pagina di consenso](./media/openidoauth-tutorial/consentpage.png)

Un utente normale può offrire il consenso per alcune autorizzazioni. Altre autorizzazioni richiedono il consenso dell'amministratore del tenant.

## <a name="difference-between-admin-consent-and-user-consent"></a>Differenza tra consenso dell'amministratore e consenso dell'utente

In qualità di amministratore, è possibile inoltre consentire le autorizzazioni delegate di un'applicazione per conto di tutti gli utenti nel proprio tenant. Il consenso dell'amministratore impedisce che la finestra di dialogo di consenso sia visualizzata per ogni utente nel tenant. Gli utenti con ruolo di amministratore possono concedere il consenso nel portale di Azure. Nella pagina **Impostazioni** dell'applicazione selezionare **Autorizzazioni necessarie** > **Concedi autorizzazioni**.

![Pulsante Concedi autorizzazioni](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> La concessione esplicita del consenso usando il pulsante **Concedi autorizzazioni** è ora richiesta per le applicazioni a pagina singola (SPA) che usano ADAL.js. In caso contrario, l'applicazione non funziona quando viene richiesto il token di accesso.

Le autorizzazioni solo app richiedono il consenso dell'amministratore tenant. Se l'applicazione richiede un'autorizzazione solo per app e un utente tenta di accedere all'applicazione, viene visualizzato un messaggio di errore che informa che l'utente non è in grado di fornire il consenso.

Se l'applicazione usa autorizzazioni che richiedono il consenso dell'amministratore, è necessario che sia presente un movimento, ad esempio un pulsante o un collegamento, con cui l'amministratore può avviare l'azione. La richiesta inviata dall'applicazione per questa azione è la richiesta di autorizzazione OAuth2/OpenID Connect standard, che include anche il parametro *prompt=admin_consent* della stringa di query. 

Dopo che l'amministratore ha fornito il consenso e l'entità servizio è stata creata nel tenant del cliente, le successive richieste di accesso non richiedono il parametro *prompt=admin_consent*. Poiché l'amministratore ha deciso che le autorizzazioni richieste sono accettabili, a nessun altro utente nel tenant viene richiesto il consenso da questo punto in poi.

Un amministratore tenant può disabilitare la possibilità che gli utenti normali possano il consenso alle applicazioni. Se questa funzionalità è disabilitata, è necessario usare il consenso dell'amministratore come obbligatorio sempre per l'applicazione nel tenant. Per testare l'applicazione con il consenso dell'utente finale disabilitato, è possibile trovare l'opzione di configurazione nel [portale di Azure](https://portal.azure.com/), nella sezione [Impostazioni utente](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) in **Applicazioni aziendali**.

Il parametro *prompt=admin_consent* può essere usato anche dalle applicazioni richiedenti autorizzazioni che non necessitano del consenso dell'amministratore. Un esempio è rappresentato da un'applicazione che richiede un'esperienza in cui l'amministratore del tenant "si iscrive" una volta e a nessun altro utente viene richiesto il consenso da tale punto in poi.

Si immagini che un'applicazione richieda il consenso dell'amministratore e un amministratore esegua l'accesso senza il parametro *prompt=admin_consent* inviato. Se l'amministratore concede il consenso all'applicazione, viene applicato solo per l'account utente. Gli utenti normali non possono comunque eseguire l'accesso o fornire il consenso all'applicazione. Questa funzionalità è utile se si vuole assegnare all'amministratore del tenant la possibilità di esplorare l'applicazione prima di consentire l'accesso ad altri utenti.
