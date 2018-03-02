---
title: Proteggere i connettori personalizzati con Azure AD - App per la logica di Azure | Microsoft Docs
description: Aggiungere protezione e autenticazione per l'API e il connettore con Azure Active Directory (Azure AD)
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: dae76a4230a1cfbe8970bbb1015041fde7765f49
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2018
---
# <a name="add-security-to-your-api-and-connector-with-azure-active-directory-azure-ad"></a>Aggiungere protezione per l'API e il connettore con Azure Active Directory (Azure AD)

Per proteggere le chiamate tra l'API e il connettore personalizzato, aggiungere l'autenticazione di Azure AD per l'API Web e il connettore. Per questo scenario verranno create due app di Azure Active Directory (Azure AD): un'app di Azure AD consente di proteggere l'API Web, mentre l'altra consente di proteggere la registrazione del connettore e aggiunge l'accesso delegato. 

In questa esercitazione viene usata l'API di Azure Resource Manager come esempio. Azure Resource Manager consente di gestire i componenti per una soluzione creata in Azure, ad esempio database, macchine virtuali e app Web. Un connettore personalizzato per Azure Resource Manager può essere utile per la gestione delle risorse di Azure dai flussi di lavoro. Per altre informazioni, vedere [Panoramica di Gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md).

## <a name="prerequisites"></a>prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iniziare con un [account Azure gratuito](https://azure.microsoft.com/free/). In alternativa, iscriversi per ottenere una [sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/pricing/purchase-options/).

* Per questa esercitazione, il [file OpenAPI di esempio per Azure Resource Manager](https://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json)

  > [!NOTE] 
  > Il file OpenAPI di esempio non definisce tutte le operazioni di Azure Resource Manager e attualmente contiene solo l'operazione per [elencare tutte le sottoscrizioni](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_List). È possibile modificare questo file OpenAPI o creare un altro file OpenAPI tramite l'[editor online OpenAPI](http://editor.swagger.io/).
  >
  > Le informazioni in questa esercitazione sono applicabili a qualsiasi API RESTful per cui si vuole usare l'autenticazione di Azure AD.

## <a name="1-create-your-first-azure-ad-app-for-securing-your-web-api"></a>1. Creare la prima app di Azure AD per proteggere l'API Web

La prima app di Azure AD esegue l'autenticazione quando il connettore personalizzato chiama l'API, ovvero l'API di Azure Resource Manager in questo esempio.

1. Accedere al [portale di Azure](https://portal.azure.com). Se sono disponibili più tenant di Azure Active Directory, verificare di avere effettuato l'accesso alla directory corretta controllando la directory indicata sotto il nome utente. 

   ![Verificare la directory](./media/custom-connector-azure-active-directory-authentication/check-user-directory.png)

   > [!TIP]
   > Per cambiare directory, scegliere il nome utente e quindi selezionare la directory desiderata.

2. Nel menu principale di Azure scegliere **Azure Active Directory** in modo da visualizzare la directory corrente.

   ![Scegliere "Azure Active Directory"](./media/custom-connector-azure-active-directory-authentication/azure-active-directory.png)

   > [!TIP]
   > Se nel menu principale di Azure non compare **Azure Active Directory**, scegliere **Tutti i servizi**. Nella casella **Filtro** digitare "Azure Active Directory" come filtro e quindi scegliere **Azure Active Directory**.

3. Nel menu della directory scegliere **Registrazioni per l'app** in **Gestisci**. Nell'elenco delle app registrate scegliere **+ Registrazione nuova applicazione**.

   ![Scegliere "Registrazioni per l'app", "Registrazione nuova applicazione"](./media/custom-connector-azure-active-directory-authentication/add-app-registrations.png)

4. In **Crea** specificare i dettagli per l'app di Azure AD, come descritto nella tabella, quindi scegliere **Crea**. 

   ![Creare l'app di Azure AD](./media/custom-connector-azure-active-directory-authentication/create-app1-registration.png)

   | Impostazione | Valore consigliato | Descrizione | 
   | ------- | --------------- | ----------- | 
   | **Nome** | *nome-app-api-web* | Nome dell'app di Azure AD per l'API Web | 
   | **Tipo di applicazione** | **App Web/API** | Tipo dell'app | 
   | **URL di accesso** | `https://login.windows.net` | | 
   |||| 

5. Quando si torna all'elenco **Registrazioni per l'app** della directory, selezionare l'app di Azure AD.

   ![Selezionare l'app di Azure AD nell'elenco](./media/custom-connector-azure-active-directory-authentication/app1-registration-created.png)

6. Quando viene visualizzata la pagina dei dettagli dell'app, assicurarsi di **copiare e salvare l'*ID applicazione* dell'app in un luogo sicuro**. Questo ID è necessario per un uso successivo.

   ![Salvare "ID applicazione" per un uso successivo](./media/custom-connector-azure-active-directory-authentication/application-id-app1.png)

7. A questo punto specificare un URL di risposta per l'app di Azure AD. Nel menu **Impostazioni** dell'app scegliere **URL di risposta**. Immettere l'URL e quindi scegliere **Salva**.

   ![URL di risposta](./media/custom-connector-azure-active-directory-authentication/add-reply-url1.png)

   | Impostazione | Valore consigliato | DESCRIZIONE | 
   | ------- | --------------- | ----------- | 
   | **URL di risposta** | Per l'API personalizzata immettere questo URL: </br>`https://{your-web-app-root-URL}/.auth/login/aad/callback` | | 
   | **Autorizzazioni delegate** | {non necessario} | | 
   | **Chiave client** | {non necessario} | | 
   |||| 

   > [!TIP]
   > Se il menu **Impostazioni** non è stato visualizzato in precedenza, scegliere **Impostazioni** qui:
   >
   > ![Scegliere "Impostazioni"](./media/custom-connector-azure-active-directory-authentication/show-app1-settings-menu.png)

## <a name="2-create-your-second-azure-ad-app-for-your-custom-connector"></a>2. Creare la seconda app di Azure AD per il connettore personalizzato

La seconda app di Azure AD consente di proteggere la registrazione del connettore personalizzato e di aggiungere l'accesso delegato all'API Web protetta dalla prima app di Azure AD. 

> [!IMPORTANT]
> Assicurarsi che entrambe le app di Azure AD siano presenti nella stessa directory.

1. Tornare all'elenco **Registrazioni per l'app** e scegliere di nuovo **+ Registrazione nuova applicazione**.

   ![Scegliere "Registrazioni per l'app", "Registrazione nuova applicazione"](./media/custom-connector-azure-active-directory-authentication/add-app-registrations2.png)

2. In **Crea** specificare i dettagli per la seconda app di Azure AD, come descritto nella tabella, quindi scegliere **Crea**. 

   ![Creare l'app di Azure AD](./media/custom-connector-azure-active-directory-authentication/create-app2-registration.png)

   | Impostazione | Valore consigliato | Descrizione | 
   | ------- | --------------- | ----------- | 
   | **Nome** | *nome-connettore* | Nome dell'app di Azure AD del connettore | 
   | **Tipo di applicazione** | **App Web/API** | Tipo dell'app | 
   | **URL di accesso** | `https://login.windows.net` | | 
   |||| 

3. Quando si torna all'elenco **Registrazioni per l'app** della directory, selezionare la seconda app di Azure AD.

   ![Selezionare la seconda app di Azure AD nell'elenco](./media/custom-connector-azure-active-directory-authentication/app2-registration-created.png)

4. Quando viene visualizzata la pagina dei dettagli dell'app, assicurarsi di **copiare e salvare anche l'*ID applicazione* di questa app in un luogo sicuro**. Questo ID è necessario per un uso successivo.

   ![Salvare "ID applicazione" per un uso successivo](./media/custom-connector-azure-active-directory-authentication/application-id-app2.png)

5. A questo punto specificare un URL di risposta per l'app di Azure AD. Nel menu **Impostazioni** dell'app scegliere **URL di risposta**. Immettere l'URL e quindi scegliere **Salva**.

   | Impostazione | Valore consigliato | 
   | ------- | --------------- | 
   | **URL di risposta** | Per il connettore personalizzato di Azure Resource Manager immettere questo URL: `https://msmanaged-na.consent.azure-apim.net/redirect` | 
   ||| 

   > [!TIP]
   > Se il menu **Impostazioni** non è stato visualizzato in precedenza, scegliere **Impostazioni** qui:
   >
   > ![Scegliere "Impostazioni"](./media/custom-connector-azure-active-directory-authentication/show-app2-settings-menu.png)

6. Nel menu **Impostazioni** scegliere **Autorizzazioni necessarie** > **Aggiungi**.

   ![Autorizzazioni necessarie > Aggiungi](./media/custom-connector-azure-active-directory-authentication/add-api-access1-select-permissions.png)

7. Quando viene aperto il menu **Aggiungi accesso all'API** scegliere **Selezionare un'API** > 
**API Gestione dei servizi Microsoft Azure** > **Seleziona**.

   ![Selezionare un'API](./media/custom-connector-azure-active-directory-authentication/add-api-access2-select-api.png)

8. Scegliere **Selezionare le autorizzazioni** dal menu **Aggiungi accesso all'API**. In **Autorizzazioni delegate** scegliere **Accesso a Gestione dei servizi di Azure come utenti dell'organizzazione** > **Seleziona**.

   ![Scegliere "Autorizzazioni delegate" > "Accesso a Gestione dei servizi di Azure come utenti dell'organizzazione"](./media/custom-connector-azure-active-directory-authentication/add-api-access3-select-permissions.png)

   In caso contrario, per le altre opzioni:

   | Opzione | Valore consigliato | DESCRIZIONE | 
   | ------ | --------------- | ----------- | 
   | **Autorizzazioni delegate** | | Selezionare le autorizzazioni per l'accesso delegato all'API Web | 
   |||| 

9. A questo punto, scegliere **Fatto** dal menu **Aggiungi accesso all'API**.

   ![Menu "Aggiungi accesso all'API" > "Fatto"](./media/custom-connector-azure-active-directory-authentication/add-api-access4-done.png)

10. Generare ora una *chiave client*, o "segreto", per l'app di Azure AD del connettore. 

    1. Tornare al menu **Impostazioni** e scegliere **Chiavi**. 
    Specificare un nome per la chiave con un massimo di 16 caratteri, selezionare un periodo di scadenza e quindi scegliere **Salva**.

       ![Creare una chiave client](./media/custom-connector-azure-active-directory-authentication/add-key.png)

    2. Quando viene visualizzata la chiave generata, **assicurarsi di copiare e salvare la chiave in un luogo sicuro** prima di uscire dalla pagina **Chiavi**.
    
       ![Copiare e salvare la chiave manualmente](./media/custom-connector-azure-active-directory-authentication/save-key.png)

11. Dopo aver salvato la chiave, è possibile chiudere il menu **Impostazioni**.

## <a name="3-add-azure-ad-authentication-to-your-web-api-app"></a>3. Aggiungere l'autenticazione di Azure AD all'app per le API Web

Attivare ora l'autenticazione per l'API Web con la prima app di Azure AD. Per altre informazioni, vedere [Come configurare l'applicazione del servizio app per l'uso dell'account di accesso di Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

1. Nel [portale di Azure](https://portal.azure.com) trovare l'app per le API Web pubblicata in precedenza in [Creare connettori personalizzati dalle API Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md).

2. In **Impostazioni** scegliere **Autenticazione/Autorizzazione**. 

   1. In **Autenticazione servizio app** scegliere **Attivata**.
   2. In **Azione da eseguire quando la richiesta non è autenticata** selezionare **Accedi con Azure Active Directory**.
   3. In **Provider di autenticazione** selezionare **Azure Active Directory**. 

   ![Scegliere "Autenticazione/Autorizzazione"](./media/custom-connector-azure-active-directory-authentication/web-api-app-authentication.png)

3. Nella pagina **Impostazioni di Azure Active Directory**:

   1. In **Modalità di gestione** scegliere **Rapida**.

   2. Selezionare ora l'app di Azure AD usata dall'app per le API Web per l'autenticazione. 
   Scegliere **Seleziona app AD esistente** > **App Azure AD**.

   3. In **Applicazioni Azure AD** selezionare l'app di Azure AD creata in precedenza per l'app per le API Web. 
   
   4. Scegliere **OK** fino a tornare alla pagina **Autenticazione/Autorizzazione**.

   Ad esempio: 

   ![Scegliere l'app di Azure AD che rappresenta l'app per le API Web](./media/custom-connector-azure-active-directory-authentication/web-api-app-select-azure-ad-app.png)

4. Nella pagina **Autenticazione/Autorizzazione** scegliere **Salva**.

   ![Salvare le impostazioni di autenticazione](./media/custom-connector-azure-active-directory-authentication/web-api-app-azure-ad-app-save.png)

   L'app per le API Web può ora usare Azure AD per l'autenticazione.

## <a name="4-set-up-azure-ad-authentication-in-your-web-apis-openapi-file"></a>4. Configurare l'autenticazione di Azure AD nel file OpenAPI dell'API Web

Aprire il file OpenAPI per l'app per le API Web in modo da poter aggiungere l'oggetto `securityDefintions` e l'autenticazione in Azure AD nella proprietà `host`. Di seguito è riportato un esempio che mostra la proprietà `host` e l'oggetto `securityDefinitions`:

``` json
// Your OpenAPI file header appears here...

"host": "{your-web-api-app-root-url}",
"schemes": [
    "https" // Make sure this is https!
],
"securityDefinitions": {
    "AAD": {
        "type": "oauth2",
        "flow": "accessCode",
        "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
        "tokenUrl": "https://login.windows.net/common/oauth2/token",
        "scopes": {}
    }
},

// Your OpenAPI document continues here...
```
A questo punto si è pronti per creare e registrare il connettore personalizzato.

## <a name="next-steps"></a>Passaggi successivi

* [Registrare il connettore](../logic-apps/logic-apps-custom-connector-register.md)
* [Domande frequenti sui connettori personalizzati](../logic-apps/custom-connector-faq.md)
