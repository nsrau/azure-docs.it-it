---
title: Accesso in locale le API con Azure AD Application Proxy
description: Proxy applicazione Azure Active Directory consente alle App native di accedere in modo sicuro le API e la logica di business si ospitano un'istanza locale o nel cloud di macchine virtuali.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: c2b99525e3d0a61c02dc502fcd0927ea65993e5b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108551"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Proteggere l'accesso alle API locali con Azure AD Application Proxy

Può avere la logica di business le API in esecuzione in locale o ospitato in macchine virtuali nel cloud. Le app Android, iOS, Mac o Windows native necessario interagire con gli endpoint API per usare i dati o fornire l'interazione dell'utente. Proxy applicazione Azure AD e il [Azure Active Directory Authentication librerie (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries) "Let" le app native accedere in modo sicuro le API in locale. Azure Active Directory Application Proxy è una soluzione più rapida e sicura rispetto all'apertura di porte del firewall e controllo dell'autenticazione e autorizzazione a livello dell'app. 

Questo articolo illustra l'impostazione di una soluzione di Proxy applicazione Azure AD per l'hosting di un servizio API web che le app native possono accedere. 

## <a name="overview"></a>Panoramica

Il diagramma seguente mostra una tradizionale di pubblicare API in locale. Questo approccio richiede l'apertura di porte in ingresso 80 e 443.

![Accesso all'API tradizionale](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

Il diagramma seguente mostra come è possibile usare Azure AD Application Proxy per la pubblicazione in modo sicuro le API senza aprire alcuna porta in ingresso:

![Accesso API Proxy di applicazione AD Azure](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Il Proxy di applicazione Azure AD costituisce la struttura portante della soluzione, funziona come un endpoint pubblico per l'accesso all'API e fornire l'autenticazione e autorizzazione. È possibile accedere le API da una vasta gamma di piattaforme usando il [ADAL](/azure/active-directory/develop/active-directory-authentication-libraries) librerie. 

Poiché l'autenticazione Proxy applicazione Azure AD e l'autorizzazione si avvalgono di Azure AD, è possibile utilizzare l'accesso condizionale di Azure AD per assicurarsi che solo i dispositivi attendibili possono accedere alle API pubblicate tramite il Proxy dell'applicazione. Usare aggiunta ad Azure AD o aggiunto ad Azure AD ibrido per desktop e gestiti da Intune per i dispositivi. È inoltre possibile sfruttare i vantaggi delle funzionalità di Azure Active Directory Premium, ad esempio Azure multi-Factor Authentication e la sicurezza di supporto di learning macchina del [Azure Identity Protection](/azure/active-directory/active-directory-identityprotection).

## <a name="prerequisites"></a>Prerequisiti

Per seguire questa procedura dettagliata, è necessario:

- Accesso amministrativo a una directory di Azure, con un account che è possibile creare e registrare le app
- L'API web di esempio e le app client native da [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>Pubblicare l'API tramite il Proxy di applicazione

Per pubblicare un'API esterna alla rete intranet tramite il Proxy di applicazione, è seguire lo stesso modello di pubblicazione di App web. Per altre informazioni, vedere [Esercitazione: Aggiungere un'applicazione in locale per l'accesso remoto tramite il Proxy di applicazione in Azure Active Directory](application-proxy-add-on-premises-application.md).

Per pubblicare l'API web SecretAPI tramite il Proxy di applicazione:

1. Creare e pubblicare il progetto di esempio SecretAPI come un'app web ASP.NET nel computer locale o nella rete intranet. Verificare che è possibile accedere all'app web in locale. 
   
1. Nel [portale di Azure](https://portal.azure.com), selezionare **Azure Active Directory** nel riquadro di spostamento a sinistra. Quindi, nella **Overview** pagina, selezionare **applicazioni aziendali**.
   
1. In cima il **applicazioni aziendali - tutte le applicazioni** pagina, selezionare **nuova applicazione**.
   
1. Nel **aggiungere un'applicazione** nella pagina **Aggiungi app personalizzata**, selezionare **applicazione On-premises**. 
   
1. Se non si dispone di un connettore del Proxy dell'applicazione installata, verrà richiesto di installarlo. Selezionare **Download connettore Proxy di applicazione** per scaricare e installare il connettore. 
   
1. Dopo aver installato il connettore del Proxy dell'applicazione, nelle **aggiungere la propria applicazione in locale** pagina:
   
   1. Immettere *SecretAPI* accanto a **nome**.
      
   1. Immettere l'URL usato per accedere all'API all'interno dell'intranet accanto a **Url interno**. 
      
   1. Assicurarsi che **preautenticazione** è impostata su **Azure Active Directory**. 
      
   1. Selezionare **Add** nella parte superiore della pagina e attesa per l'app da creare.
   
   ![Aggiungere app per le API](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. Nel **applicazioni aziendali - tutte le applicazioni** pagina, selezionare la **SecretAPI** app. 
   
1. Nel **SecretAPI - Overview** pagina, selezionare **proprietà** nel riquadro di spostamento a sinistra.
   
1. Non si desidera che siano disponibili agli utenti finali dell'API di **MyApps** panel, in modo da impostare **visibile agli utenti** al **No** nella parte inferiore del **proprietà**pagina e quindi selezionare **salvare**.
   
   ![Non visibile agli utenti](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
È stata pubblicata l'API web tramite il Proxy di applicazione Azure AD. A questo punto, aggiungere gli utenti possono accedere all'app. 

1. Nel **SecretAPI - Overview** pagina, selezionare **utenti e gruppi** nel riquadro di spostamento a sinistra.
   
1. Nel **utenti e gruppi** pagina, selezionare **Add user**.  
   
1. Nel **Aggiungi assegnazione** pagina, selezionare **utenti e gruppi**. 
   
1. Nel **utenti e gruppi** pagina, cercare e selezionare gli utenti che possono accedere all'app, almeno compreso te stesso. Dopo aver selezionato tutti gli utenti, selezionare **seleziona**. 
   
   ![Seleziona e si assegna utente](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. Nella **Aggiungi assegnazione** pagina, selezionare **assegnare**. 

> [!NOTE]
> Le API che usano l'autenticazione integrata di Windows potrebbero richiedere [passaggi aggiuntivi](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd).

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Registrare l'app nativa e concedere l'accesso all'API

App native sono programmi sviluppati per l'uso in un dispositivo o piattaforma specifica. Prima che l'app nativa possa connettersi e accedere a un'API, è necessario registrarlo in Azure AD. La procedura seguente illustra come registrare un'app nativa e concedere l'accesso all'API pubblicata tramite il Proxy applicazione web.

Per registrare l'app nativa AppProxyNativeAppSample:

1. In Azure Active Directory **Overview** pagina, selezionare **registrazioni per l'App**e nella parte superiore del **registrazioni per l'App** riquadro, selezionare **nuova registrazione** .
   
1. Nel **registrare un'applicazione** pagina:
   
   1. Sotto **Name**, immettere *AppProxyNativeAppSample*. 
      
   1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**. 
      
   1. Sotto **URL di reindirizzamento**, l'elenco a discesa e selezionare **client pubblico (per dispositivi mobili e desktop)** , quindi immettere *https:\//appproxynativeapp*. 
      
   1. Selezionare **registrare**e attendere che l'app deve essere registrato correttamente. 
      
      ![Registrazione nuova applicazione](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
Ora aver registrato l'app AppProxyNativeAppSample in Azure Active Directory. Per concedere all'app nativa l'accesso all'API web SecretAPI:

1. In Azure Active Directory **Overview** > **registrazioni per l'App** pagina, selezionare il **AppProxyNativeAppSample** app. 
   
1. Nel **AppProxyNativeAppSample** pagina, selezionare **autorizzazioni delle API** nel riquadro di spostamento a sinistra. 
   
1. Nel **le autorizzazioni API** pagina, selezionare **aggiungere un'autorizzazione**.
   
1. Il primo **le autorizzazioni API Request** pagina, selezionare il **API Usa la mia organizzazione** scheda e quindi cercare e selezionare **SecretAPI**. 
   
1. Al successivo **le autorizzazioni API Request** pagina, selezionare la casella di controllo accanto a **user_impersonation**e quindi selezionare **aggiungere autorizzazioni**. 
   
    ![Selezionare un'API](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. Nella **le autorizzazioni API** pagina, è possibile selezionare **concedere il consenso dell'amministratore di Contoso** per impedire ad altri utenti di dover singolarmente fornire il consenso all'app. 

## <a name="configure-the-native-app-code"></a>Configurare il codice dell'app nativa

L'ultimo passaggio consiste nel configurare l'app nativa. Il frammento seguente del *Form1.cs* file nell'app di esempio NativeClient fa sì che la libreria ADAL acquisire il token per richiedere la chiamata all'API e associarlo come bearer per l'intestazione dell'app. 
   
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
   
Per configurare l'app nativa per la connessione ad Azure Active Directory e chiamare il Proxy di App API, aggiornare i valori segnaposto nel *app. config* file dell'app di esempio NativeClient con i valori da Azure AD: 

- Incollare il **ID di Directory (tenant)** nel `<add key="ida:Tenant" value="" />` campo. È possibile trovare e copiare questo valore (GUID) dal **Panoramica** pagina di una delle app. 
  
- Incollare il AppProxyNativeAppSample **ID applicazione (client)** nel `<add key="ida:ClientId" value="" />` campo. È possibile trovare e copiare questo valore (GUID) dal AppProxyNativeAppSample **Panoramica** pagina.
  
- Incollare il AppProxyNativeAppSample **URI di reindirizzamento** nel `<add key="ida:RedirectUri" value="" />` campo. È possibile trovare e copiare questo valore (URI) dal AppProxyNativeAppSample **autenticazione** pagina. 
  
- Incollare il SecretAPI **URI ID applicazione** nel `<add key="todo:TodoListResourceId" value="" />` campo. È possibile trovare e copiare questo valore (URI) dal SecretAPI **esporre un'API** pagina.
  
- Incollare il SecretAPI **URL della home page** nel `<add key="todo:TodoListBaseAddress" value="" />` campo. È possibile trovare e copiare questo valore (URL) dal SecretAPI **Branding** pagina.

Dopo aver configurato i parametri, compilare ed eseguire l'app nativa. Quando si seleziona il **Accedi** pulsante, l'app consente di accedere e quindi visualizza una schermata di completamento per confermare che non è stato connesso al SecretAPI.

![Riuscito](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Aggiungere un'applicazione in locale per l'accesso remoto tramite il Proxy di applicazione in Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Avvio rapido: Configurare un'applicazione client per accedere alle API web](../develop/quickstart-configure-app-access-web-apis.md)
- [Come abilitare le applicazioni client native interagire con applicazioni proxy](application-proxy-configure-native-client-application.md)
