---
title: Prerequisiti di accesso all'API di creazione report di Azure AD | Microsoft Docs
description: Informazioni sui prerequisiti di accesso all'API di creazione report di Azure AD
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/30/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7b6fab4a4a36691bbdeb11975c7a93b97ab86cb
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241649"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Prerequisiti di accesso all'API di creazione report di Azure AD

Le [API di creazione report di Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) forniscono l'accesso ai dati dal codice tramite un set di API basate su REST. È possibile chiamare queste API da strumenti e linguaggi di programmazione.

L'API di creazione report usa l'autenticazione [OAuth](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) per autorizzare l'accesso alle API Web.

Per preparare l'accesso all'API di creazione report, è necessario:

1. [Assegnare ruoli](#assign-roles)
2. [Registrare un'applicazione](#register-an-application)
3. [Concedere le autorizzazioni](#grant-permissions)
4. [Ottenere le impostazioni di configurazione](#gather-configuration-settings)

## <a name="assign-roles"></a>Assegnare ruoli

Per accedere ai dati di creazione dei report tramite l'API, è necessario disporre di uno dei seguenti ruoli assegnati:

- Ruolo con autorizzazioni di lettura per la sicurezza

- Amministratore della protezione

- Amministratore globale


## <a name="register-an-application"></a>Registra un'applicazione

La registrazione è necessaria anche se si accede all'API di creazione report usando uno script. La registrazione fornisce un **ID applicazione**, necessario per le chiamate di autorizzazione e consente al codice di ricevere token.

Per configurare la directory per l'accesso all'API di creazione report di Azure AD, è necessario accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore di Azure che è anche membro del ruolo di directory **Amministratore globale** nel tenant di Azure AD.

> [!IMPORTANT]
> Le applicazioni eseguite con credenziali con privilegi di amministratore possono essere molto potenti, quindi assicurarsi di conservare l'ID e il segreto dell'applicazione in un luogo sicuro.
> 

**Per registrare un'applicazione Azure AD:**

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.
   
    ![Registra applicazione](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Nella pagina **Azure Active Directory** selezionare **Registrazioni per l'app**.

    ![Registra applicazione](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Nella pagina **registrazioni app** selezionare **nuova registrazione**.

    ![Registra applicazione](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Pagina **di registrazione di un'applicazione** :

    ![Registra applicazione](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. Nella casella di testo **Nome** digitare `Reporting API application`.

    b. Per **tipo di account supportati**, selezionare **account solo in questa organizzazione**.

    c. Nella casella di testo **URL di reindirizzamento** selezionare **Web** digitare `https://localhost`.

    d. Selezionare **Registra**. 


## <a name="grant-permissions"></a>Concedere le autorizzazioni 

A seconda delle API a cui si desidera accedere, è necessario concedere all'app le autorizzazioni seguenti:  

| API | Autorizzazioni |
| --- | --- |
| Microsoft Azure Active Directory | Leggi i dati della directory |
| Microsoft Graph | Lettura di tutti i dati del log di controllo |


![Registra applicazione](./media/howto-configure-prerequisites-for-reporting-api/36.png)

Nella sezione seguente viene elencata la procedura per entrambe le API. Se non si desidera accedere a una delle API, è possibile ignorare le procedure relative.

**Per concedere l'autorizzazione dell'applicazione per l'uso delle API:**


1. Selezionare **autorizzazioni API** e quindi **Aggiungi un'autorizzazione**. 

    ![Registra applicazione](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Nella **pagina autorizzazioni API richiesta**individuare supporto dell' **API legacy** **Azure Active Directory Graph**. 

    ![Registra applicazione](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Nella pagina **autorizzazioni necessarie** selezionare **Autorizzazioni applicazione**, quindi **directory** CheckBox **Directory. ReadAll**.  Selezionare **Aggiungi autorizzazioni**.

    ![Registra applicazione](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Nella pagina **API per la creazione di report-autorizzazioni API** selezionare **concedi il consenso dell'amministratore**. 

    ![Registra applicazione](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Nota: **Microsoft Graph** viene aggiunto per impostazione predefinita durante la registrazione dell'API.

    ![Registra applicazione](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Ottenere le impostazioni di configurazione 

Questa sezione illustra come ottenere le impostazioni seguenti dalla directory:

- Nome di dominio
- ID client
- Segreto client

Questi valori sono necessari quando si configurano le chiamate all'API di creazione report. 

### <a name="get-your-domain-name"></a>Ottenere il nome di dominio

**Per ottenere il nome di dominio:**

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento a sinistra.
   
    ![Registra applicazione](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Nella pagina **Azure Active Directory** selezionare **Nomi di dominio personalizzati**.

    ![Registra applicazione](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Copiare il nome del dominio dall'elenco dei domini.


### <a name="get-your-applications-client-id"></a>Ottenere l'ID client dell'applicazione

**Per ottenere l'ID client dell'applicazione:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro di spostamento sinistro.
   
    ![Registra applicazione](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Selezionare l'applicazione nella pagina **Registrazioni per l'app**.

3. Nella pagina dell'applicazione, passare a **ID applicazione** e selezionare **Fare clic per copiare**.

    ![Registra applicazione](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Ottenere il segreto client dell'applicazione
 Evitare errori durante il tentativo di accedere ai log di controllo o di accedere usando l'API.

**Per ottenere il segreto client dell'applicazione:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro di spostamento sinistro.
   
    ![Registra applicazione](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Selezionare l'applicazione nella pagina **Registrazioni per l'app**.

3.  Selezionare **certificati e segreti** nella pagina **applicazione API** , nella sezione **segreti client** fare clic su **+ nuovo segreto client**. 

    ![Registra applicazione](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Nella pagina **Aggiungi un segreto client** aggiungere:

    a. Nella casella di testo **Descrizione** digitare `Reporting API`.

    b. Per **Scadenza** selezionare **In 2 years** (In 2 anni).

    c. Fare clic su **Save**.

    d. Copiare il valore della chiave.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Risolvere gli errori relativi all'API di creazione report

Questo articolo elenca i comuni messaggi di errore che potrebbero verificarsi durante l'accesso ai report delle attività usando l'API Graph di Microsoft e i passaggi per la loro risoluzione.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>Errore interno 500 del server HTTP durante l'accesso all'endpoint Microsoft Graph versione 2

L'endpoint Microsoft Graph versione 2 non è attualmente supportato: assicurarsi di accedere ai log attività usando l'endpoint Microsoft Graph versione 1.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Errore: Impossibile ottenere i ruoli utente da AD Graph

 Accedere all'account usando entrambi i pulsanti di accesso nell'interfaccia utente di Graph Explorer per evitare di ricevere un errore durante il tentativo di accesso con Graph Explorer. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Errore: Impossibile eseguire operazioni di controllo della licenza Premium da AD Graph 

Se compare questo messaggio di errore durante il tentativo di accedere agli accessi tramite Graph Explorer, scegliere **Autorizzazioni di modifica** sotto l'account sulla barra di spostamento a sinistra e selezionare **Tasks.ReadWrite** e **Directory.Read.All**. 

![Interfaccia utente di Autorizzazioni di modifica](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Errore: Il tenant non è B2C o il tenant non ha una licenza Premium

L'accesso ai report sugli accessi richiede una licenza Azure Active Directory Premium 1 (P1). Se viene visualizzato questo messaggio di errore durante l'accesso agli accessi, assicurarsi che il tenant sia concesso in licenza con una licenza Azure AD P1.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Errore: I ruoli consentiti non includono User. 

 Evitare errori durante il tentativo di accedere ai log di controllo o di accedere usando l'API. Assicurarsi che l'account faccia parte del ruolo **lettore di sicurezza** o **lettore report** nel tenant del Azure Active Directory.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Errore: L'applicazione non possiede l'autorizzazione "Leggi i dati della directory" di AAD 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Errore: L'applicazione non possiede l'autorizzazione "Leggere tutti i dati dei log di controllo" dell'API Microsoft Graph

Seguire i passaggi descritti in [prerequisiti per accedere all'API di creazione report Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) per assicurarsi che l'applicazione sia in esecuzione con il set di autorizzazioni corretto. 

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere dati con l'API di creazione report di Azure Active Directory con certificati](tutorial-access-api-with-certificates.md)
* [Informazioni di riferimento sulle API di controllo](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Informazioni di riferimento sulle API di report di attività di accesso](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
