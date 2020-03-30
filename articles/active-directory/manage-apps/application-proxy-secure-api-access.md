---
title: Accedere alle API locali con il proxy di applicazione di Azure ADAccess on-premises APIs with Azure AD Application Proxy
description: Il proxy di applicazione di Azure Active Directory consente alle app native di accedere in modo sicuro alle API e alla logica aziendale ospitata in locale o nelle macchine virtuali cloud.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: ecd5d8bae22d67f8d9f5b99d5c94eecf54a4a1f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165986"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Accesso sicuro alle API locali con il proxy di applicazione di Azure ADSecure access to on-premises APIs with Azure AD Application Proxy

È possibile avere API con logica di business in esecuzione in locale o ospitate in macchine virtuali nel cloud. Le app native Android, iOS, Mac o Windows devono interagire con gli endpoint API per usare i dati o fornire l'interazione dell'utente. Il proxy di applicazione di Azure AD e le librerie di autenticazione di [Azure Active Directory (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries) consentono alle app native di accedere in modo sicuro alle API locali. Il proxy di applicazione di Azure Active Directory è una soluzione più veloce e sicura rispetto all'apertura delle porte del firewall e al controllo dell'autenticazione e dell'autorizzazione a livello di app. 

Questo articolo illustra come configurare una soluzione proxy di applicazione di Azure AD per l'hosting di un servizio API Web a cui possono accedere le app native. 

## <a name="overview"></a>Panoramica

Il diagramma seguente illustra un modo tradizionale per pubblicare le API locali. Questo approccio richiede l'apertura delle porte in ingresso 80 e 443.This approach requires opening incoming ports 80 and 443.

![Accesso API tradizionale](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

Il diagramma seguente mostra come usare il proxy di applicazione di Azure AD per pubblicare in modo sicuro le API senza aprire le porte in ingresso:The following diagram shows how you can use Azure AD Application Proxy to securely publish APIs without opening any incoming ports:

![Accesso all'API del proxy di applicazione di Azure ADAzure AD Application Proxy API access](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Il proxy di applicazione di Azure AD costituisce la spina dorsale della soluzione, fungendo da endpoint pubblico per l'accesso alle API e fornendo autenticazione e autorizzazione. È possibile accedere alle API da una vasta gamma di piattaforme usando le librerie [ADAL.](/azure/active-directory/develop/active-directory-authentication-libraries) 

Poiché l'autenticazione e l'autorizzazione del proxy di applicazione di Azure AD si basano su Azure AD, è possibile usare l'accesso condizionale di Azure AD per garantire che solo i dispositivi attendibili possano accedere alle API pubblicate tramite il proxy di applicazione. Usare l'aggiunta ad Azure AD o l'aggiunta ibrida di Azure AD per i desktop e Intune gestito per i dispositivi. È anche possibile sfruttare le funzionalità di Azure Active Directory Premium come Azure Multi-Factor Authentication e la sicurezza supportata dall'apprendimento automatico di [Azure Identity Protection.](/azure/active-directory/active-directory-identityprotection)

## <a name="prerequisites"></a>Prerequisiti

Per seguire questa procedura dettagliata, è necessario:To follow this walkthrough, you need:

- Accesso amministratore a una directory di Azure con un account che può creare e registrare appAdmin access to an Azure directory, with an account that can create and register apps
- L'API Web di esempio e le app client native da[https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>Pubblicare l'API tramite il proxy di applicazionePublish the API through Application Proxy

Per pubblicare un'API all'esterno della rete Intranet tramite il proxy di applicazione, seguire lo stesso modello per la pubblicazione di app Web. Per altre informazioni, vedere [Esercitazione: Aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory.For more information, see Tutorial: Add an on-premises application for remote access through Application Proxy in Azure Active Directory.](application-proxy-add-on-premises-application.md)

Per pubblicare l'API Web SecretAPI tramite il proxy di applicazione:

1. Compilare e pubblicare il progetto SecretAPI di esempio come ASP.NET'app Web nel computer locale o nella rete Intranet. Assicurati di poter accedere all'app Web in locale. 
   
1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory**. Selezionare quindi **Applicazioni aziendali**.
   
1. Nella parte superiore della pagina **Applicazioni aziendali - Tutte le applicazioni** selezionare Nuova **applicazione**.
   
1. Nella pagina **Aggiungi un'applicazione** selezionare **Applicazioni locali**. Viene visualizzata la pagina **Aggiungi applicazione locale.**
   
1. Se non è installato un connettore proxy di applicazione, verrà richiesto di installarlo. Selezionare **Scarica connettore proxy applicazione** per scaricare e installare il connettore. 
   
1. Dopo aver installato Application Proxy Connector, nella pagina Aggiungere un'applicazione locale è stata installata nella pagina **Aggiungere un'applicazione locale:**
   
   1. Accanto a **Nome**, immettere *SecretAPI*.
      
   1. Accanto a **URL interno**immettere l'URL utilizzato per accedere all'API dall'interno della rete Intranet.
      
   1. Assicurarsi che **la preautenticazione** sia impostata su **Azure Active Directory**. 
      
   1. Seleziona **Aggiungi** nella parte superiore della pagina e attendi la creazione dell'app.
   
   ![Aggiungi app per le API](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. Nella pagina **Applicazioni aziendali - Tutte le applicazioni** selezionare l'app **SecretAPI.** 
   
1. Nella pagina **SecretAPI - Panoramica** selezionare **Proprietà** nel riquadro di spostamento sinistro.
   
1. Non si desidera che le API siano disponibili per gli utenti finali nel pannello **MyApps,** quindi impostare **Visibile per gli utenti** su **No** nella parte inferiore della pagina **Proprietà** e quindi selezionare **Salva**.
   
   ![Non visibile agli utenti](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
L'API Web è stata pubblicata tramite il proxy di applicazione di Azure AD. A questo punto, aggiungere gli utenti che possono accedere all'app. 

1. Nella pagina **SecretAPI - Panoramica** selezionare **Utenti e gruppi** nel riquadro di spostamento sinistro.
   
1. Nella pagina **Utenti e gruppi** selezionare Aggiungi **utente**.  
   
1. Nella pagina **Aggiungi assegnazione** selezionare **Utenti e gruppi.** 
   
1. Nella pagina **Utenti e gruppi** cercare e selezionare gli utenti che possono accedere all'app, incluso almeno se stessi. Dopo aver selezionato tutti gli utenti, selezionare **Seleziona**. 
   
   ![Selezionare e assegnare l'utente](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. Nella pagina Aggiungi assegnazione selezionare **Assegna**nella pagina **Aggiungi assegnazione** . 

> [!NOTE]
> Le API che utilizzano l'autenticazione integrata di Windows potrebbero richiedere [passaggi aggiuntivi.](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd)

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Registrare l'app nativa e concedere l'accesso all'APIRegister the native app and grant access to the API

Le app native sono programmi sviluppati per l'uso su una determinata piattaforma o dispositivo. Prima che l'app nativa possa connettersi e accedere a un'API, è necessario registrarla in Azure AD. I passaggi seguenti illustrano come registrare un'app nativa e concederle l'accesso all'API Web pubblicata tramite il proxy di applicazione.

Per registrare l'app nativa AppProxyNativeAppSample:

1. Nella pagina **Panoramica** di Azure Active Directory selezionare **Registrazioni app**e nella parte superiore del riquadro **Registrazioni app** selezionare **Nuova registrazione.**
   
1. Nella pagina **Registra un'applicazione:**
   
   1. In **Nome**immettere *AppProxyNativeAppSample*. 
      
   1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**. 
      
   1. In **Url di reindirizzamento**, selezionare **Client pubblico (mobile & desktop)** e quindi immettere *https:\//appproxynativeapp*. 
      
   1. Seleziona **Registra**e attendi che l'app venga registrata correttamente. 
      
      ![Registrazione nuova applicazione](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
L'app AppProxyNativeAppSample è stata registrata in Azure Active Directory. Per concedere all'app nativa l'accesso all'API Web SecretAPI:

1. Nella pagina Registrazioni app **panoramica** > di Azure Active Directory selezionare l'app **AppProxyNativeAppSample.On the** Azure Active Directory Overview App**Registrations** page, select the AppProxyNativeAppSample app. 
   
1. Nella pagina **AppProxyNativeAppSample** selezionare **Autorizzazioni API** nel riquadro di spostamento sinistro. 
   
1. Nella pagina **Autorizzazioni API** selezionare **Aggiungi un'autorizzazione**.
   
1. Nella prima pagina **Richiedi autorizzazioni API** selezionare la scheda API utilizzata **dall'organizzazione,** quindi cercare e selezionare **SecretAPI**. 
   
1. Nella pagina successiva **Autorizzazioni API richiesta** selezionare la casella di controllo accanto a **user_impersonation**, quindi scegliere **Aggiungi autorizzazioni**. 
   
    ![Selezionare un'API](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. Nella pagina **Autorizzazioni API** è possibile selezionare **Concedi il consenso amministrativo per Contoso** per impedire ad altri utenti di dover acconsentire singolarmente all'app. 

## <a name="configure-the-native-app-code"></a>Configurare il codice nativo dell'app

L'ultimo passaggio consiste nel configurare l'app nativa. Il frammento di codice seguente dal file *di Form1.cs* nell'app di esempio NativeClient fa sì che la libreria ADAL acquisisca il token per richiedere la chiamata API e lo alleghi come portatore all'intestazione dell'app. 
   
   ```csharp
       AuthenticationResult result = null;
       HttpClient httpClient = new HttpClient();
       authContext = new AuthenticationContext(authority);
       result = await authContext.AcquireTokenAsync(todoListResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
       
       // Append the token as bearer in the request header.
       httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
       
       // Call the API.
       HttpResponseMessage response = await httpClient.GetAsync(todoListBaseAddress + "/api/values/4");
   
       // MessageBox.Show(response.RequestMessage.ToString());
       string s = await response.Content.ReadAsStringAsync();
       MessageBox.Show(s);
   ```
   
Per configurare l'app nativa per la connessione ad Azure Active Directory e chiamare il proxy dell'app API, aggiornare i valori segnaposto nel file *App.config* dell'app di esempio NativeClient con i valori di Azure AD: 

- Incollare l'ID directory `<add key="ida:Tenant" value="" />` **(tenant)** nel campo. È possibile trovare e copiare questo valore (un GUID) dalla pagina **Panoramica** di una delle app. 
  
- Incollare l'ID **dell'applicazione** AppProxyNativeAppSample (client) nel `<add key="ida:ClientId" value="" />` campo. È possibile trovare e copiare questo valore (un GUID) dalla pagina **Panoramica** di AppProxyNativeAppSample.You can find and copy this value (a GUID) from the AppProxyNativeAppSample Overview page.
  
- Incollare l'URI di **reindirizzamento** AppProxyNativeAppSample nel `<add key="ida:RedirectUri" value="" />` campo. È possibile trovare e copiare questo valore (un URI) dalla pagina **Autenticazione** AppProxyNativeAppSample.You can find and copy this value (a URI) from the AppProxyNativeAppSample Authentication page. 
  
- Incollare **l'URI dell'ID applicazione** SecretAPI nel `<add key="todo:TodoListResourceId" value="" />` campo. È possibile trovare e copiare questo valore (un URI) dalla pagina **SecretAPI Expose an API.**
  
- Incollare **l'URL** della `<add key="todo:TodoListBaseAddress" value="" />` home page SecretAPI nel campo. È possibile trovare e copiare questo valore (un URL) dalla pagina **SecretAPI Branding.**

Dopo aver configurato i parametri, compilare ed eseguire l'app nativa. Quando si seleziona il pulsante **Accedi,** l'app consente di accedere e quindi viene visualizzata una schermata di operazione riuscita per verificare che sia stata eseguita correttamente connessa a SecretAPI.

![Operazione completata](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active DirectoryTutorial: Add an on-premises application for remote access through Application Proxy in Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Guida introduttiva: Configurare un'applicazione client per accedere alle API Web](../develop/quickstart-configure-app-access-web-apis.md)
- [Come abilitare le applicazioni client native per interagire con le applicazioni proxyHow to enable native client applications to interact with proxy applications](application-proxy-configure-native-client-application.md)
