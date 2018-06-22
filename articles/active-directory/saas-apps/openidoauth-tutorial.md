---
title: Passaggi per configurare un'applicazione OAuth/OpenID dalla raccolta di App Azure AD | Microsoft Docs
description: Passaggi per configurare un'applicazione OAuth/OpenID dalla raccolta di App Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 176af41197810059a17daf5ab09d29e0169e9640
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225008"
---
# <a name="steps-to-configure-an-openidoauth-application-from-azure-ad-app-gallery"></a>Passaggi per configurare un'applicazione OAuth/OpenID dalla raccolta di App Azure AD

## <a name="process-of-open-id-application-addition-from-gallery"></a>Processo di aggiunta dell'applicazione Open ID dalla raccolta

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory](./media/openidoauth-tutorial/tutorial_general_01.png)

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](./media/openidoauth-tutorial/tutorial_general_02.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](./media/openidoauth-tutorial/tutorial_general_03.png)

4. Nella casella di ricerca, digitare il **nome dell'applicazione**, selezionare l'**applicazione desiderata** dal pannello dei risultati e accedere all'applicazione.

    ![Aggiunta dell'applicazione](./media/openidoauth-tutorial/addfromgallery.png)

    > [!NOTE]
    > Per le app Open ID Connect e OAuth il pulsante Aggiungi è disabilitato per impostazione predefinita. In questo caso l'amministratore del tenant deve fare clic sul pulsante di **iscrizione** e fornire il consenso all'applicazione. Con ciò che l'applicazione aggiunge al tenant del cliente, non è necessario aggiungere in modo esplicito ed eseguire le configurazioni.

    ![Pulsante Aggiungi](./media/openidoauth-tutorial/addbutton.png)

5. Quando si fa clic sul collegamento di iscrizione, si viene reindirizzati alla pagina di Azure AD per le credenziali di accesso.

6. Al termine dell'autenticazione, l'utente deve accettare il consenso dalla pagina corrispondente e, successivamente, verrà visualizzata la home page dell'applicazione.

    > [!NOTE]
    > I clienti possono aggiungere solo un'istanza dell'applicazione. Se ne è già stata aggiunta una e si è tentato di fornire nuovamente il consenso, questa non verrà aggiunta ulteriormente nel tenant. Pertanto, logicamente è possibile usare solo un'istanza di app nel tenant.

## <a name="authentication-flow-using-openid-connect"></a>Flusso di autenticazione con OpenID Connect

Il flusso di accesso di base include i passaggi seguenti: ognuno di essi è descritto in dettaglio di seguito.

![Flusso di autenticazione con OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

* **Applicazione multi-tenant**: un'applicazione multi-tenant è destinata all'uso in molte organizzazioni, non solo in una. Si tratta generalmente di applicazioni SaaS (Software-as-a-Service) scritte da un fornitore di software indipendente (ISV). È necessario eseguire il provisioning delle applicazioni multi-tenant in ogni directory in cui verranno usate ed è richiesto il consenso dell'utente o dell'amministratore per registrarle. Questo processo di consenso inizia quando un'applicazione viene registrata nella directory e può accedere all'API Graph o magari a un'altra API Web. Quando un utente o amministratore di un'altra organizzazione esegue l'accesso per usare l'applicazione, viene visualizzata una finestra di dialogo in cui sono indicate le autorizzazioni richieste dall'applicazione. L'utente o amministratore può quindi concedere il consenso all'applicazione, in modo da fornirle l'accesso ai dati indicati e infine di registrarla nella propria directory.

    > [!NOTE]
    > Se l'applicazione viene resa disponibile agli utenti in più directory, è necessario un meccanismo per determinare il tenant attivo. Un'applicazione con un singolo tenant deve solo cercare un utente nella propria directory, mentre un'applicazione multi-tenant deve identificare un utente specifico in tutte le directory in Azure AD. A questo scopo, Azure AD fornisce un endpoint di autenticazione comune in cui qualsiasi applicazione multi-tenant può indirizzare le richieste di accesso, invece di un endpoint specifico di un tenant. Questo endpoint è [https://login.microsoftonline.com/common](https://login.microsoftonline.com/common) per tutte le directory in Azure AD, mentre un endpoint specifico del tenant può essere [https://login.microsoftonline.com/contoso.onmicrosoft.com](https://login.microsoftonline.com/contoso.onmicrosoft.com). L'endpoint comune deve essere valutato con particolare attenzione durante lo sviluppo di un'applicazione, perché occorrerà la logica necessaria per gestire più tenant durante l'accesso, la disconnessione e la convalida dei token.

Il team di Azure Active Directory, per impostazione predefinita, promuove le applicazioni multi-tenant alle quali è possibile accedere facilmente a partire da diverse organizzazioni e sono facili da usare dopo averne accettato il consenso.

## <a name="what-is-consent-framework"></a>Che cos'è il framework di consenso?

Il framework di consenso di Azure AD semplifica lo sviluppo di applicazioni client Web multi-tenant e native. Queste applicazioni consentono l'accesso da parte degli account utente da un tenant di Azure AD diverso da quello in cui l'applicazione è registrata. Esse possono anche dover accedere ad API Web come l'API Microsoft Graph (per l'accesso ad Azure Active Directory, Intune e ai servizi di Office 365) e le API di altri servizi Microsoft, oltre alle API Web personalizzate. Il framework è basato sulla possibilità per un utente o un amministratore di fornire il consenso alla richiesta di registrazione di un'applicazione nella propria directory, che può comportare l'accesso a dati di directory. Una volta ottenuto il consenso, l'applicazione client potrà chiamare l'API di Microsoft Graph per conto dell'utente e usare le informazioni nel modo necessario.

L'[API Microsoft Graph](https://graph.microsoft.io/) fornisce l'accesso ai dati in Office 365 (come calendari e messaggi di Exchange, siti ed elenchi di SharePoint, documenti di OneDrive, blocchi appunti di OneNote, attività di Planner, cartelle di lavoro di Excel e così via), nonché a utenti e gruppi di Azure AD e ad altri oggetti dati di più servizi cloud Microsoft.

I passaggi seguenti illustrano il funzionamento dell'esperienza di consenso per lo sviluppatore e l'utente dell'applicazione.

1. Si supponga di avere un'applicazione client Web che deve richiedere autorizzazioni specifiche per accedere a una risorsa/API. Il portale di Azure viene usato per dichiarare le richieste di autorizzazione al momento della configurazione. Come altre impostazioni di configurazione, diventano parte della registrazione dell'applicazione in Azure Active Directory:

    ![API Graph](./media/openidoauth-tutorial/graphapi.png)

2. Considerare che sono state aggiornate le autorizzazioni dell'applicazione, che l'applicazione è in esecuzione e che un utente sta per usarla per la prima volta. L'applicazione deve prima ottenere un codice di autorizzazione dall'endpoint di Azure AD/dall'autorizzazione. Il codice di autorizzazione può quindi essere usato per acquisire un nuovo un token di accesso e aggiornamento.

3. Se l'utente non è già autenticato, l'endpoint di Azure AD/dell'autorizzazione richiede l'accesso.

    ![Authentication](./media/openidoauth-tutorial/authentication.png)

4. Dopo che l'utente ha effettuato l'accesso, Azure AD determinerà se l'utente deve essere reindirizzato a una pagina di consenso. Questa decisione dipende dal fatto che l'utente o l'amministratore dell'organizzazione abbia o meno già concesso il consenso dell'applicazione. Se il consenso non è già stato concesso, Azure AD lo richiede all'utente e visualizza le autorizzazioni necessarie per il funzionamento. Il set di autorizzazioni visualizzato nella finestra di dialogo di consenso corrisponde a quello selezionato in Autorizzazioni delegate nel portale di Azure.

    ![Pagina di consenso](./media/openidoauth-tutorial/consentpage.png)

Alcune autorizzazioni possono essere concesse da un utente normale, mentre altre richiedono il consenso dell'amministratore tenant.

## <a name="whats-the-difference-between-admin-consent-and-user-consent"></a>Qual è la differenza tra consenso dell'amministratore e il consenso dell'utente?

In qualità di amministratore, è possibile inoltre consentire le autorizzazioni delegate di un'applicazione per conto di tutti gli utenti nel proprio tenant. Il consenso amministrativo evita la visualizzazione della finestra di dialogo di consenso per ogni utente del tenant e può essere eseguito nel portale di Azure da utenti con ruolo di amministratore. Dalla pagina Impostazioni dell'applicazione selezionare Autorizzazioni necessarie e fare clic sul pulsante Concedi autorizzazioni.

![Concessione dell'autorizzazione](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> La concessione esplicita del consenso usando il pulsante Concedi autorizzazioni è attualmente richiesta per le applicazioni a pagina singola (SPA) che usano ADAL.js. In caso contrario, l'applicazione non funziona quando viene richiesto il token di accesso.

Le autorizzazioni solo app richiedono il consenso dell'amministratore tenant. Se l'applicazione richiede un'autorizzazione solo per app e un utente tenta di accedere all'applicazione, viene visualizzato un messaggio di errore che informa che l'utente non è in grado di fornire il consenso.

Se l'applicazione usa autorizzazioni che richiedono il consenso dell'amministratore, è necessario che sia presente un movimento, ad esempio un pulsante o un collegamento, con cui l'amministratore può avviare l'azione. La richiesta inviata dall'applicazione per questa azione è la richiesta di autorizzazione OAuth2/OpenID Connect standard, che include anche il parametro prompt=admin_consent della stringa di query. Dopo che l'amministratore ha fornito il consenso e l'entità servizio è stata creata nel tenant del cliente, le successive richieste di accesso non richiedono il parametro prompt=admin_consent. Poiché l'amministratore ha deciso che le autorizzazioni richieste sono accettabili, a nessun altro utente nel tenant viene richiesto il consenso da questo punto in poi. Un amministratore tenant può disabilitare la possibilità che gli utenti normali possano il consenso alle applicazioni. Se questa funzionalità è disabilitata, è necessario usare il consenso dell'amministratore come obbligatorio sempre per l'applicazione nel tenant. Per testare l'applicazione con il consenso dell'utente finale disabilitato, è possibile trovare l'opzione di configurazione nel [Portale di Azure](https://portal.azure.com/) nella sezione [Impostazioni utente](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) in **Applicazioni aziendali**

Il parametro prompt=admin_consent può essere usato anche dalle applicazioni che richiedono autorizzazioni che non necessitano del consenso dell'amministratore. Questo parametro viene usato, ad esempio, quando l'applicazione richiede un'esperienza in cui il tenant amministratore "si iscrive" una volta e a nessun altro utente viene richiesto il consenso da tale punto in poi.

Se un'applicazione richiede il consenso dell'amministratore e un amministratore accede senza che il parametro prompt=admin_consent venga inviato, quando l'amministratore concede correttamente il consenso all'applicazione, il consenso sarà valido solo per l'account utente. Gli utenti normali non potranno ancora eseguire l'accesso o fornire il consenso all'applicazione. Questa funzionalità è utile se si vuole assegnare all'amministratore tenant la possibilità di esplorare l'applicazione prima di consentire l'accesso ad altri utenti.