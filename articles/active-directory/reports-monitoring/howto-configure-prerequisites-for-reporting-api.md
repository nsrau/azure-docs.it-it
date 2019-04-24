---
title: Prerequisiti di accesso all'API di creazione report di Azure AD | Microsoft Docs
description: Informazioni sui prerequisiti di accesso all'API di creazione report di Azure AD
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ab30dfeccc4ae1c7b09a8f48846ffb9e71cfc23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60439091"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Prerequisiti di accesso all'API di creazione report di Azure AD

Le [API di creazione report di Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) forniscono l'accesso ai dati dal codice tramite un set di API basate su REST. È possibile chiamare le API da numerosi linguaggi di programmazione e strumenti.

L'API di creazione report usa l'autenticazione [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) per autorizzare l'accesso alle API Web.

Per preparare l'accesso all'API di creazione report, è necessario:

1. [Assegnare ruoli](#assign-roles)
2. [Registrare un'applicazione](#register-an-application)
3. [Concedere le autorizzazioni](#grant-permissions)
4. [Ottenere le impostazioni di configurazione](#gather-configuration-settings)

## <a name="assign-roles"></a>Assegnare ruoli

Per accedere ai dati di creazione dei report tramite l'API, è necessario disporre di uno dei seguenti ruoli assegnati:

- Ruolo con autorizzazioni di lettura per la sicurezza

- Amministratore della sicurezza

- Amministratore globale


## <a name="register-an-application"></a>Registrare un'applicazione

È necessario registrare un'applicazione, anche se si accede all'API di creazione dei report tramite uno script. Questo consente di avere un **ID applicazione** necessario per le chiamate di autorizzazione e consente al codice di ricevere i token.

Per configurare la directory per l'accesso all'API di creazione report di Azure AD, è necessario accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore di Azure che è anche membro del ruolo di directory **Amministratore globale** nel tenant di Azure AD.

> [!IMPORTANT]
> Le applicazioni eseguite con credenziali con privilegi di amministratore possono essere molto potenti, quindi assicurarsi di conservare l'ID e il segreto dell'applicazione in un luogo sicuro.
> 

**Per registrare un'applicazione Azure AD:**

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.
   
    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Nella pagina **Azure Active Directory** selezionare **Registrazioni per l'app**.

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Nella pagina **Registrazioni per l'app** selezionare **Registrazione nuova applicazione**.

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Nella pagina **Crea** seguire la procedura seguente:

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. Nella casella di testo **Nome** digitare `Reporting API application`.

    b. Per **Tipo di applicazione** selezionare **App Web/API**.

    c. Nella casella di testo **URL di accesso** digitare `https://localhost`.

    d. Selezionare **Create**. 


## <a name="grant-permissions"></a>Concedere le autorizzazioni 

A seconda delle API a cui si desidera accedere, è necessario concedere all'app le autorizzazioni seguenti:  

| API | Autorizzazione |
| --- | --- |
| Microsoft Azure Active Directory | Leggi i dati della directory |
| Microsoft Graph | Leggere tutti i dati dei log di controllo |


![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/36.png)

Nella sezione seguente viene elencata la procedura per entrambe le API. Se non si desidera accedere a una delle API, è possibile ignorare le procedure relative.

**Per concedere l'autorizzazione dell'applicazione per l'uso delle API:**

1. Selezionare l'applicazione nella pagina **Registrazioni per l'app** e selezionare **impostazioni**. 

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Nella pagina **Impostazioni** selezionare **Autorizzazioni necessarie**. 

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Nella pagina **Autorizzazioni necessarie** fare clic su Windows **Azure Active Directory** nell'elenco delle **API**. 

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Nella pagina **Abilita l'accesso**, selezionare **Lettura dati directory** e deselezionare **Accedi e leggi il profilo di un altro utente**. 

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Nel barra degli strumenti in alto fare clic su **Salva**.

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/15.png)

6. Nella pagina **Autorizzazioni necessarie** fare clic su **Aggiungi** sulla barra degli strumenti in alto.

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/32.png)

7. Nella pagina **Aggiungi accesso all'API** fare clic su **Selezionare un'API**.

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/31.png)

8. Nella pagina **Selezionare un'API** fare clic su **Microsoft Graph** e quindi fare clic su **Seleziona**.

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/33.png)

9. Nella pagina **Abilita l'accesso**, selezionare **Leggere tutti i dati del log di controllo**, quindi fare clic su **Seleziona**.  

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/34.png)

10. Nella pagina **Aggiungi accesso all'API** fare clic su **Fatto**.  

11. Nella pagina **Autorizzazioni necessarie**, sulla barra degli strumenti in alto. fare clic su **Concedi autorizzazioni** e quindi su **Sì**.

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/17.png)


## <a name="gather-configuration-settings"></a>Ottenere le impostazioni di configurazione 

Questa sezione illustra come ottenere le impostazioni seguenti dalla directory:

- Nome di dominio
- ID client
- Segreto client

Questi valori sono necessari quando si configurano le chiamate all'API di creazione report. 

### <a name="get-your-domain-name"></a>Ottenere il nome di dominio

**Per ottenere il nome di dominio:**

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento a sinistra.
   
    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Nella pagina **Azure Active Directory** selezionare **Nomi di dominio personalizzati**.

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Copiare il nome del dominio dall'elenco dei domini.


### <a name="get-your-applications-client-id"></a>Ottenere l'ID client dell'applicazione

**Per ottenere l'ID client dell'applicazione:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro di spostamento sinistro.
   
    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Selezionare l'applicazione nella pagina **Registrazioni per l'app**.

3. Nella pagina dell'applicazione, passare a **ID applicazione** e selezionare **Fare clic per copiare**.

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Ottenere il segreto client dell'applicazione
Per ottenere il segreto client dell'applicazione, è necessario creare una nuova chiave e salvare il relativo valore durante il salvataggio della nuova chiave perché non è più possibile recuperare il valore in un secondo momento.

**Per ottenere il segreto client dell'applicazione:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro di spostamento sinistro.
   
    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Selezionare l'applicazione nella pagina **Registrazioni per l'app**.

3. Nella pagina dell'applicazione selezionare **Impostazioni** nella barra degli strumenti nella parte superiore. 

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/05.png)

4. Nella pagina **Impostazioni** fare clic su **Chiavi** nella sezione **Accesso all'API**. 

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Nel pagina **Chiavi** seguire questa procedura:

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/14.png)

    a. Nella casella di testo **Descrizione** digitare `Reporting API`.

    b. Per **Scadenza** selezionare **In 2 years** (In 2 anni).

    c. Fare clic su **Save**.

    d. Copiare il valore della chiave.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Risolvere gli errori relativi all'API di creazione report

Questo articolo elenca i comuni messaggi di errore che potrebbero verificarsi durante l'accesso ai report delle attività usando l'API Graph di Microsoft e i passaggi per la loro risoluzione.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>Errore interno 500 del server HTTP durante l'accesso all'endpoint Microsoft Graph versione 2

L'endpoint Microsoft Graph versione 2 non è attualmente supportato: assicurarsi di accedere ai log attività usando l'endpoint Microsoft Graph versione 1.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Errore: Impossibile ottenere i ruoli utente da AD Graph

È possibile ricevere questo messaggio di errore quando si tenta di accedere agli accessi tramite Graph Explorer. Assicurarsi di essere connessi a un account usando entrambi i pulsanti di accesso nell'interfaccia utente di Graph Explorer, come illustrato nell'immagine seguente. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Errore: Impossibile eseguire operazioni di controllo della licenza Premium da AD Graph 

Se compare questo messaggio di errore durante il tentativo di accedere agli accessi tramite Graph Explorer, scegliere **Autorizzazioni di modifica** sotto l'account sulla barra di spostamento a sinistra e selezionare **Tasks.ReadWrite** e **Directory.Read.All**. 

![Interfaccia utente di Autorizzazioni di modifica](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Errore: Nessun tenant è B2C o il tenant non ha una licenza Premium

L'accesso ai report sugli accessi richiede una licenza Azure Active Directory Premium 1 (P1). Se viene visualizzato questo messaggio di errore durante l'accesso agli accessi, assicurarsi che il tenant sia concesso in licenza con una licenza Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Errore: L'utente non è associato ai ruoli autorizzati 

Se viene visualizzato questo messaggio di errore durante il tentativo di accedere ai log di controllo o agli accessi usando l'API, assicurarsi che l'account faccia parte del **ruolo con autorizzazioni di lettura per la sicurezza** o del **ruolo lettore report** nel tenant di Azure Active Directory. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Errore: L'applicazione non possiede l'autorizzazione "Leggi i dati della directory" di AAD 

Seguire i passaggi descritti in [Prerequisiti di accesso all'API di creazione report di Azure AD](howto-configure-prerequisites-for-reporting-api.md) per assicurarsi che l'applicazione venga eseguita con il set di autorizzazioni corretto. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Errore: L'applicazione non possiede l'autorizzazione "Leggere tutti i dati dei log di controllo" dell'API Microsoft Graph

Seguire i passaggi descritti in [Prerequisiti di accesso all'API di creazione report di Azure AD](howto-configure-prerequisites-for-reporting-api.md) per assicurarsi che l'applicazione venga eseguita con il set di autorizzazioni corretto. 

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere dati con l'API di creazione report di Azure Active Directory con certificati](tutorial-access-api-with-certificates.md)
* [Informazioni di riferimento sulle API di controllo](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Informazioni di riferimento sulle API di report di attività di accesso](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
