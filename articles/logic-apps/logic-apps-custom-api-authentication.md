---
title: Aggiungere l'autenticazione ad API personalizzate - App per la logica di Azure | Microsoft Docs
description: Configurare l'autenticazione per le chiamate alle API personalizzate da app per la logica
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
ms.openlocfilehash: 2528f4318d92bbfdc1008795876f0240a5e3e4f6
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="secure-calls-to-your-custom-apis-from-logic-apps"></a>Proteggere le chiamate alle API personalizzate da app per la logica

Per proteggere le chiamate alle API, è possibile configurare l'autenticazione di Azure Active Directory (Azure AD) nel portale di Azure, in modo da non dover aggiornare il codice. Oppure, è possibile richiedere e applicare l'autenticazione attraverso il codice dell'API.

## <a name="authentication-options-for-your-api"></a>Opzioni di autenticazione per l'API

È possibile proteggere le chiamate all'API personalizzata nei modi seguenti:

* [Nessuna modifica del codice](#no-code): proteggere l'API con [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) dal portale di Azure, in modo che non sia necessario aggiornare il codice o implementare nuovamente l'API.

  > [!NOTE]
  > Per impostazione predefinita, l'autenticazione di Azure AD che si attiva nel portale di Azure non offre un'autorizzazione con granularità fine. Ad esempio, questa autenticazione blocca l'API a un tenant specifico, non a un determinato utente o app. 

* [Aggiornare il codice dell'API](#update-code): proteggere l'API applicando [l'autenticazione del certificato](#certificate), [l'autenticazione di base](#basic) o [autenticazione di Azure AD](#azure-ad-code) attraverso il codice.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Autenticare le chiamate all'API senza modificare il codice

Ecco i passaggi generali per questo metodo:

1. Creare due identità di applicazione di Azure Active Directory (Azure AD), una per l'app per la logica e una per l'app Web (o l'app per le API).

2. Per autenticare le chiamate all'API, usare le credenziali (ID client e segreto) per l'entità servizio associata all'identità di applicazione Azure AD per l'app per la logica.

3. Includere gli ID applicazione nella definizione di app per la logica.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Parte 1: Creare un'identità di applicazione di Azure AD per l'app per la logica

L'app per la logica usa questa identità di applicazione di Azure AD per l'autenticazione con Azure AD. È sufficiente impostare l'identità una sola volta per la directory. Ad esempio, si può scegliere di usare la stessa identità per tutte le app per la logica, anche se è possibile creare identità univoche per ogni app per la logica. È possibile impostare queste identità nel Portale di Azure o usare [PowerShell](#powershell).

**Creare l'identità di applicazione per l'app per la logica nel portale di Azure**

1. Nel [portale di Azure](https://portal.azure.com "https://portal.azure.com") scegliere **Azure Active Directory**. 

2. Verificare di essere nella stessa directory dell'app Web o app per le API.

   > [!TIP]
   > Per passare da una directory all'altra, scegliere il profilo e selezionare un'altra directory. In alternativa, scegliere **Panoramica** > **Cambia directory**.

3. Nel menu delle directory scegliere da **Gestisci** **Registrazioni per l'app** > **Registrazione nuova applicazione**.

   > [!TIP]
   > Per impostazione predefinita, l'elenco delle registrazioni di app indica tutte le registrazioni di app presenti nella directory. Per visualizzare solo le registrazioni delle proprie app, selezionare **Le mie app** accanto alla casella di ricerca. 

   ![Creare una nuova registrazione di app](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Assegnare un nome all'identità di applicazione, lasciare il **tipo di applicazione** impostato su **App Web/API**, specificare una stringa univoca formattata come dominio per **URL accesso** e scegliere **Crea**.

   ![Specificare nome e URL di accesso per l'identità di applicazione](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   L'identità di applicazione creata per l'app per la logica ora appare nell'elenco delle registrazioni di app.

   ![Identità di applicazione per l'app per la logica](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. Nell'elenco di registrazioni di app selezionare la nuova identità di applicazione. Copiare e salvare l'**ID applicazione** da usare come "ID client" per l'app per la logica nella Parte 3.

   ![Copiare e salvare l'ID applicazione per l'app per la logica](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Se le impostazioni dell'identità di applicazione non sono visibili, scegliere **Impostazioni** o **Tutte le impostazioni**.

7. Scegliere **Chiavi** in **Accesso all'API**. In **Descrizione** specificare un nome per la chiave. Selezionare una **scadenza** per la durata della chiave.

   La chiave che si sta creando agisce come "segreto" o password dell'identità di applicazione per l'app per la logica.

   ![Creare una chiave per l'identità dell'app per la logica](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Scegliere **Salva** nella barra degli strumenti. La chiave viene visualizzata in **Valore**. 
**Assicurarsi di copiare e salvare la chiave** per usarla successivamente, in quanto viene nascosta quando si esce dalla pagina **Chiavi**.

   Quando si configura l'app per la logica nella Parte 3, si specifica questa chiave come "segreto" o password.

   ![Copiare e salvare la chiave per usarla in un momento successivo](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Creare l'identità di applicazione per l'app per la logica in PowerShell**

È possibile eseguire questa attività usando Azure Resource Manager con PowerShell. In PowerShell eseguire questi comandi:

1. `Switch-AzureMode AzureResourceManager`

2. `Add-AzureAccount`

3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password "identity-password"`

4. Assicurarsi di copiare l'**ID tenant** (GUID per il tenant di Azure AD), l'**ID applicazione** e la password usati.

Per altre informazioni, vedere la procedura di [creazione di un'entità servizio con PowerShell per accedere alle risorse](../azure-resource-manager/resource-group-authenticate-service-principal.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Parte 2: Creare un'identità di applicazione di Azure AD per l'app Web o l'app per le API

Se l'app Web o l'app per le API è già stata distribuita, è possibile attivare l'autenticazione e creare l'identità di applicazione nel portale di Azure. In alternativa, è possibile [attivare l'autenticazione quando si effettua la distribuzione con un modello di Azure Resource Manager](#authen-deploy). 

**Creare l'identità di applicazione e attivare l'autenticazione nel portale di Azure per le app distribuite**

1. Nel [portale di Azure](https://portal.azure.com "https://portal.azure.com") individuare e selezionare l'app Web o app per le API. 

2. In **Impostazioni** scegliere **Autenticazione/Autorizzazione**. In **Autenticazione servizio app** **attivare** l'autenticazione. In **Provider di autenticazione** scegliere **Azure Active Directory**.

   ![Attivare l'autenticazione](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Creare un'identità di applicazione per l'app Web o l'app per le API come indicato di seguito. Nella pagina **Impostazioni di Azure Active Directory** impostare **Modalità di gestione** su **Rapida**. Scegliere **Crea nuova App AD**. Assegnare un nome all'identità di applicazione e scegliere **OK**. 

   ![Creare un'identità di applicazione per l'app Web o l'app per le API](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. Nella pagina **Autenticazione/Autorizzazione** scegliere **Salva**.

A questo punto è necessario trovare l'ID client e l'ID tenant per l'identità di applicazione associata all'app Web o app per le API. Questi ID verranno usati nella Parte 3. Continuare con questa procedura per il portale di Azure.

**Trovare l'ID client e l'ID tenant dell'identità di applicazione per l'app Web o app per le API nel portale di Azure**

1. In **Provider di autenticazione** scegliere **Azure Active Directory**. 

   ![Scegliere "Azure Active Directory"](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. Nella pagina **Impostazioni di Azure Active Directory** impostare **Modalità di gestione** su **Avanzata**.

3. Copiare l'**ID client** e salvare il GUID per usarlo nella Parte 3.

   > [!TIP] 
   > Se l'**ID client** e l'**URL dell'autorità di certificazione** non appaiono, provare ad aggiornare il portale di Azure e ripetere il passaggio 1.

4. In **URL autorità di certificazione** copiare e salvare solo il GUID per la Parte 3. È anche possibile usare questo GUID nel modello di distribuzione dell'app Web o app per le API, se necessario.

   Questo GUID è il GUID del tenant specifico ("ID tenant") e deve apparire in questo URL: `https://sts.windows.net/{GUID}`

5. Senza salvare le modifiche, chiudere la pagina **Impostazioni di Azure Active Directory**.

<a name="authen-deploy"></a>

**Attivare l'autenticazione quando si esegue la distribuzione con un modello di Azure Resource Manager**

È comunque necessario creare un'identità di applicazione di Azure AD per l'app Web o app per le API che differisce dall'identità di applicazione per l'app per la logica. Per creare l'identità di applicazione, seguire i passaggi descritti in precedenza nella Parte 2 per il portale di Azure. 

È anche possibile seguire i passaggi della Parte 1, ma assicurarsi di usare l'elemento `https://{URL}` reale dell'app Web o app per le API per **URL accesso** e **URI dell'ID dell'app**. Da questi passaggi è necessario salvare sia l'ID client che l'ID tenant per usarli nel modello di distribuzione dell'app, nonché per la Parte 3.

> [!NOTE]
> Quando si crea l'identità di applicazione di Azure AD per l'app Web o l'app per le API, è necessario usare il portale di Azure anziché PowerShell. Il cmdlet di PowerShell non consente di impostare le autorizzazioni necessarie per l'accesso degli utenti in un sito Web.

Dopo aver ottenuto l'ID client e l'ID tenant, includerli come risorsa secondaria dell'app Web o app per le API nel modello di distribuzione:

``` json
"resources": [ {
    "apiVersion": "2015-08-01",
    "name": "web",
    "type": "config",
    "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
    "properties": {
        "siteAuthEnabled": true,
        "siteAuthSettings": {
            "clientId": "{client-ID}",
            "issuer": "https://sts.windows.net/{tenant-ID}/",
        }
    }
} ]
```

Per implementare automaticamente un'app Web vuota e un'app per la logica con l'autenticazione di Azure Active Directory, [visualizzare il modello completo qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json) oppure fare clic su **Distribuzione in Azure** qui:

[![Distribuzione in Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Parte 3: Compilare la sezione Autorizzazione nell'app per la logica

Questa sezione dell'autorizzazione è già stata configurata nel modello precedente, ma se si crea direttamente l'app per la logica, sarà necessario includere l'intera sezione relativa all'autorizzazione.

Aprire la definizione dell'app per la logica nella visualizzazione Codice, passare alla sezione dell'azione **HTTP**, trovare la sezione dell'**autorizzazione** e includere questa riga:

`{"tenant": "{tenant-ID}", "audience": "{client-ID-from-Part-2-web-app-or-API app}", "clientId": "{client-ID-from-Part-1-logic-app}", "secret": "{key-from-Part-1-logic-app}", "type": "ActiveDirectoryOAuth" }`

| Elemento | Obbligatoria | DESCRIZIONE | 
| ------- | -------- | ----------- | 
| tenant | Sì | Il GUID per il tenant di Azure AD | 
| audience | Sì | GUID per la risorsa di destinazione cui si vuole accedere, che è l'ID client dall'identità di applicazione per l'app Web o l'app per le API | 
| clientId | Sì | GUID per il client che richiede l'accesso, che è l'ID client dall'identità di applicazione per l'app per la logica | 
| secret | Sì | La chiave o la password dall'identità di applicazione per il client che richiede il token di accesso | 
| type | Sì | Il tipo di autenticazione. Per l'autenticazione ActiveDirectoryOAuth, il valore è `ActiveDirectoryOAuth`. | 
|||| 

Ad esempio: 

``` json
{
   "actions": {
      "some-action": {
         "conditions": [],
         "inputs": {
            "method": "post",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         },
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Chiamate API protette con codice

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Autenticazione del certificato

Per convalidare le richieste in ingresso dall'app per la logica all'app Web o all'app per le API, è possibile usare i certificati client. Per configurare il codice, vedere le informazioni relative alla [configurazione dell'autenticazione reciproca TLS](../app-service/app-service-web-configure-tls-mutual-auth.md).

Includere questa riga nella sezione dell'**autorizzazione**: 

`{"type": "clientcertificate", "password": "password", "pfx": "long-pfx-key"}`

| Elemento | Obbligatoria | DESCRIZIONE | 
| ------- | -------- | ----------- | 
| type | Sì | Il tipo di autenticazione. Per i certificati client SSL, il valore deve essere `ClientCertificate`. | 
| password | Sì | La password per accedere al certificato client (file PFX) | 
| pfx | Sì | Contenuti del certificato client con codifica Base64 (file PFX) | 
|||| 

<a name="basic"></a>

#### <a name="basic-authentication"></a>Autenticazione di base

Per convalidare le richieste in ingresso dall'app per la logica all'app Web o app per le API, è possibile usare l'autenticazione di base, ad esempio un nome utente e una password. L'autenticazione di base è un modello comune applicabile a qualsiasi linguaggio usato per compilare l'app Web o app per le API.

Includere questa riga nella sezione dell'**autorizzazione**:

`{"type": "basic", "username": "username", "password": "password"}`.

| Elemento | Obbligatoria | DESCRIZIONE | 
| ------- | -------- | ----------- | 
| type | Sì | Tipo di autenticazione che si vuole usare. Per l'autenticazione di base il valore deve essere `Basic`. | 
| username | Sì | Nome utente che si vuole usare per l'autenticazione | 
| password | Sì | Password che si vuole usare per l'autenticazione | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Autenticazione di Azure Active Directory usando il codice

Per impostazione predefinita, l'autenticazione di Azure AD che si attiva nel portale di Azure non offre un'autorizzazione con granularità fine. Ad esempio, questa autenticazione blocca l'API a un tenant specifico, non a un determinato utente o app. 

Per limitare l'accesso dell'API all'app per la logica usando il codice, estrarre l'intestazione che contiene il token JSON Web (JWT). Verificare l'identità del chiamante e rifiutare le richieste che non corrispondono.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/app-service-authentication-overview.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire e chiamare API personalizzate da flussi di lavoro di app per la logica](../logic-apps/logic-apps-custom-api-host-deploy-call.md)