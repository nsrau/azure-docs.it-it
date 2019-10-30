---
title: Accedere alle API locali con Azure AD proxy di applicazione
description: Il proxy di applicazione di Azure Active Directory consente alle app native di accedere in modo sicuro alle API e alla logica di business ospitate in locale o in macchine virtuali cloud.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: b741f42bb215df59903fed7ed84094b7d037ce65
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063021"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Proteggere l'accesso alle API locali con Azure AD proxy di applicazione

È possibile che siano presenti API per la logica di business in esecuzione in locale o ospitate in macchine virtuali nel cloud. Le app native per Android, iOS, Mac o Windows devono interagire con gli endpoint dell'API per usare i dati o fornire l'interazione dell'utente. Azure AD proxy di applicazione e le [librerie di autenticazione Azure Active Directory (adal)](/azure/active-directory/develop/active-directory-authentication-libraries) consentono alle app native di accedere in modo sicuro alle API locali. Azure Active Directory Application Proxy è una soluzione più veloce e sicura rispetto all'apertura delle porte del firewall e al controllo dell'autenticazione e dell'autorizzazione a livello di app. 

Questo articolo illustra la configurazione di una soluzione Azure AD proxy di applicazione per l'hosting di un servizio API Web a cui possono accedere le app native. 

## <a name="overview"></a>Panoramica

Il diagramma seguente illustra un modo tradizionale per pubblicare le API locali. Per questo approccio è necessario aprire le porte in ingresso 80 e 443.

![Accesso tradizionale alle API](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

Il diagramma seguente illustra come è possibile usare Azure AD proxy di applicazione per pubblicare in modo sicuro le API senza aprire le porte in ingresso:

![Accesso all'API del proxy dell'applicazione Azure AD](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Il proxy dell'applicazione Azure AD costituisce il backbone della soluzione, funziona come endpoint pubblico per l'accesso all'API e fornisce l'autenticazione e l'autorizzazione. È possibile accedere alle API da una vasta gamma di piattaforme usando le librerie [adal](/azure/active-directory/develop/active-directory-authentication-libraries) . 

Poiché Azure AD l'autenticazione e l'autorizzazione del proxy di applicazione sono basate su Azure AD, è possibile usare Azure AD l'accesso condizionale per garantire che solo i dispositivi attendibili possano accedere alle API pubblicate tramite il proxy di applicazione. Usare Azure AD join o Azure AD ibrido aggiunto per i desktop e Intune gestito per i dispositivi. È anche possibile sfruttare le funzionalità di Azure Active Directory Premium come Azure Multi-Factor Authentication e la sicurezza basata su Machine Learning di [Azure Identity Protection](/azure/active-directory/active-directory-identityprotection).

## <a name="prerequisites"></a>Prerequisiti

Per seguire questa procedura dettagliata, è necessario:

- Accesso amministrativo a una directory di Azure con un account che può creare e registrare app
- L'API Web di esempio e le app client native da [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>Pubblicare l'API tramite il proxy di applicazione

Per pubblicare un'API all'esterno della Intranet tramite il proxy di applicazione, si segue lo stesso modello di pubblicazione delle app Web. Per altre informazioni, vedere [esercitazione: aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory](application-proxy-add-on-premises-application.md).

Per pubblicare l'API Web SecretAPI tramite il proxy di applicazione:

1. Compilare e pubblicare il progetto SecretAPI di esempio come app Web ASP.NET nel computer locale o nell'Intranet. Assicurarsi che sia possibile accedere all'app Web in locale. 
   
1. Nella [portale di Azure](https://portal.azure.com)selezionare **Azure Active Directory** nel percorso di spostamento a sinistra. Quindi, nella pagina **Panoramica** selezionare **applicazioni aziendali**.
   
1. Nella parte superiore della pagina **applicazioni aziendali-tutte le applicazioni** selezionare **nuova applicazione**.
   
1. In **applicazioni locali**della pagina **Sfoglia Azure ad Gallery** selezionare **Aggiungi un'applicazione locale**. Viene visualizzata la pagina **Aggiungi applicazione locale** .
   
1. Se non è installato un connettore del proxy di applicazione, verrà richiesto di installarlo. Selezionare **Scarica connettore proxy di applicazione** per scaricare e installare il connettore. 
   
1. Dopo aver installato il connettore del proxy di applicazione, nella pagina **Aggiungi un'applicazione locale** :
   
   1. Immettere *SecretAPI* accanto a **nome**.
      
   1. Immettere l'URL usato per accedere all'API dall'interno della Intranet accanto a **URL interno**. 
      
   1. Assicurarsi che la **pre-autenticazione** sia impostata su **Azure Active Directory**. 
      
   1. Selezionare **Aggiungi** nella parte superiore della pagina e attendere che venga creata l'app.
   
   ![Aggiungi app per le API](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. Nella pagina **applicazioni aziendali-tutte le applicazioni** selezionare l'app **SecretAPI** . 
   
1. Nella pagina **SecretAPI-Overview** selezionare **Proprietà** nel percorso di spostamento a sinistra.
   
1. Non si vuole che le API siano disponibili per gli utenti finali nel pannello **app** , quindi impostare **Visible sugli utenti** su **No** nella parte inferiore della pagina delle **Proprietà** e quindi selezionare **Save (Salva**).
   
   ![Non visibile agli utenti](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
L'API Web è stata pubblicata tramite Azure AD proxy di applicazione. A questo punto, aggiungere gli utenti che possono accedere all'app. 

1. Nella pagina **SecretAPI-Overview** selezionare **utenti e gruppi** nel percorso di spostamento a sinistra.
   
1. Nella pagina **utenti e gruppi** selezionare **Aggiungi utente**.  
   
1. Nella pagina **Aggiungi assegnazione** selezionare **utenti e gruppi**. 
   
1. Nella pagina **utenti e gruppi** cercare e selezionare gli utenti che possono accedere all'app, incluso almeno manualmente. Dopo aver selezionato tutti gli utenti, selezionare **Seleziona**. 
   
   ![Selezionare e assegnare un utente](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. Nella pagina **Aggiungi assegnazione** selezionare **assegna**. 

> [!NOTE]
> Le API che usano l'autenticazione integrata di Windows potrebbero richiedere [passaggi aggiuntivi](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd).

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Registrare l'app nativa e concedere l'accesso all'API

Le app native sono programmi sviluppati per l'uso in una piattaforma o un dispositivo specifico. Prima che l'app nativa possa connettersi e accedere a un'API, è necessario registrarla in Azure AD. La procedura seguente illustra come registrare un'app nativa e concedergli l'accesso all'API Web pubblicata tramite il proxy di applicazione.

Per registrare l'app nativa AppProxyNativeAppSample:

1. Nella pagina **panoramica** Azure Active Directory selezionare **registrazioni app**, quindi nella parte superiore del riquadro **registrazioni app** selezionare **nuova registrazione**.
   
1. Nella pagina **registra un'applicazione** :
   
   1. In **nome**immettere *AppProxyNativeAppSample*. 
      
   1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**. 
      
   1. In **URL di reindirizzamento**, a discesa e selezionare **client pubblico (Mobile & desktop)** , quindi immettere *https:\//appproxynativeapp*. 
      
   1. Selezionare **Register (registra**) e attendere che l'app venga registrata correttamente. 
      
      ![Registrazione nuova applicazione](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
A questo punto è stata registrata l'app AppProxyNativeAppSample in Azure Active Directory. Per concedere all'app nativa l'accesso all'API Web SecretAPI:

1. Nella pagina **panoramica** Azure Active Directory > **registrazioni** per l'app selezionare l'app **AppProxyNativeAppSample** . 
   
1. Nella pagina **AppProxyNativeAppSample** selezionare **autorizzazioni API** nel percorso di spostamento a sinistra. 
   
1. Nella pagina **autorizzazioni API** selezionare **Aggiungi un'autorizzazione**.
   
1. Nella prima pagina **autorizzazioni API richiesta** selezionare le **API utilizzate dall'organizzazione** e quindi cercare e selezionare **SecretAPI**. 
   
1. Nella pagina **autorizzazioni API richiesta** successiva selezionare la casella di controllo accanto a **user_impersonation**e quindi selezionare **Aggiungi autorizzazioni**. 
   
    ![Selezionare un'API](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. Tornando alla pagina **autorizzazioni API** , è possibile selezionare **Concedi all'amministratore il consenso a Contoso** per impedire ad altri utenti di dover acconsentire singolarmente all'app. 

## <a name="configure-the-native-app-code"></a>Configurare il codice dell'app nativa

L'ultimo passaggio consiste nel configurare l'app nativa. Il frammento di codice seguente dal file *Form1.cs* nell'app di esempio NativeClient fa in modo che la libreria adal acquisisca il token per la richiesta della chiamata API e lo colleghi come Bearer all'intestazione dell'app. 
   
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
   
Per configurare l'app nativa per connettersi a Azure Active Directory e chiamare il proxy dell'app per le API, aggiornare i valori segnaposto nel file *app. config* dell'app di esempio NativeClient con i valori da Azure ad: 

- Incollare l' **ID di directory (tenant)** nel campo `<add key="ida:Tenant" value="" />`. È possibile trovare e copiare questo valore (un GUID) dalla pagina **Panoramica** di una delle app. 
  
- Incollare l' **ID dell'applicazione AppProxyNativeAppSample (client)** nel campo `<add key="ida:ClientId" value="" />`. È possibile trovare e copiare questo valore (un GUID) dalla pagina di **Panoramica** di AppProxyNativeAppSample.
  
- Incollare l' **URI di reindirizzamento** AppProxyNativeAppSample nel campo `<add key="ida:RedirectUri" value="" />`. È possibile trovare e copiare questo valore (un URI) dalla pagina di **autenticazione** AppProxyNativeAppSample. 
  
- Incollare l' **URI dell'ID applicazione** SecretAPI nel campo `<add key="todo:TodoListResourceId" value="" />`. È possibile trovare e copiare questo valore (un URI) dal SecretAPI **esporre una** pagina dell'API.
  
- Incollare l' **URL della Home page** di SecretAPI nel campo `<add key="todo:TodoListBaseAddress" value="" />`. È possibile trovare e copiare questo valore (un URL) dalla pagina di **personalizzazione** di SecretAPI.

Dopo aver configurato i parametri, compilare ed eseguire l'app nativa. Quando si seleziona il pulsante **Accedi** , l'app consente di accedere e quindi Visualizza una schermata di operazione completata per verificare che sia stata connessa correttamente a SecretAPI.

![Success](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Guida introduttiva: configurare un'applicazione client per accedere alle API Web](../develop/quickstart-configure-app-access-web-apis.md)
- [Come abilitare le applicazioni client native per l'interazione con le applicazioni proxy](application-proxy-configure-native-client-application.md)
