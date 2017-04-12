---
title: Chiamare le API personalizzate in App per la logica di Azure | Microsoft Docs
description: Chiamare le API personalizzate ospitate nel Servizio app di Azure con App per la logica di Azure
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2016
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 9d169bcc0b1e89866d04cced474ee9c0e6ba6952
ms.lasthandoff: 04/06/2017


---
# <a name="call-custom-apis-hosted-on-azure-app-service-with-azure-logic-apps"></a>Chiamare le API personalizzate ospitate nel Servizio app di Azure con App per la logica di Azure

Sebben App per la logica di Azure includa oltre 40 connettori per diversi servizi, è possibile chiamare l'API personalizzata che può eseguire il codice personale. Il Servizio app di Azure offre uno dei modi più semplici e scalabili per ospitare le API Web personalizzate. Questo articolo illustra come chiamare un'API Web ospitata in un'app per le API, in un'app Web o in un'app per dispositivi mobili del servizio app.
Informazioni sulla [creazione di API come trigger o azione nelle app per la logica](../logic-apps/logic-apps-create-api-app.md).

## <a name="deploy-your-web-app"></a>Distribuire l'app Web

Prima di tutto è necessario distribuire l'API come un'app Web nel Servizio app di Azure. Informazioni sulla [distribuzione di base quando si crea un'app Web ASP.NET](../app-service-web/app-service-web-get-started-dotnet.md). Sebbene sia possibile chiamare le API da qualsiasi app per la logica, per un'esperienza ottimale si consiglia di aggiungere i metadati Swagger per facilitare l'integrazione con le azioni delle app per la logica. Informazioni su come [aggiungere i metadati Swagger](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui).

### <a name="api-settings"></a>Impostazioni API

Affinché la Progettazione app per la logica analizzi i metadati Swagger, è necessario abilitare CORS e impostare le proprietà di definizione dell'API per l'app Web. 

1.    Nel Portale di Azure selezionare l'app Web.
2.    Nel pannello visualizzato, individuare l'**API**e selezionare **Definizione API**. Impostare la **Posizione di definizione dell'API** all'URL del file swagger.json.

    In genere, questo URL è https://{name}.azurewebsites.net/swagger/docs/v1).

3.    Per consentire le richieste da Progettazione app per la logica in **API**, selezionare **CORS**e impostare un criterio CORS per "*".

## <a name="call-into-your-custom-api"></a>Chiamare l'API personalizzata

Se CORS e le proprietà di definizione API sono state impostate, sarà possibile aggiungere facilmente le azioni API personalizzate all'interno del flusso di lavoro nel portale di App per la logica. Nella finestra di Progettazione app per la logica è possibile sfogliare i siti Web di sottoscrizione per elencare i siti Web con URL swagger definito. È inoltre possibile puntare a uno Swagger ed elencare le azioni e gli input disponibili usando l'azione HTTP + Swagger. Infine, è sempre possibile creare una richiesta usando l'azione HTTP per chiamare un'API, incluse le API che non dispongono o non espongono un documento Swagger.

Per proteggere l'API, sono disponibili due metodi:

*    Non è necessaria alcuna modifica nel codice. Per proteggere l'API è possibile usare Azure Active Directory senza richiedere le modifiche del codice o la ridistribuzione.
*    Nel codice API applicare l'autenticazione di base, l'autenticazione del certificato o l'autenticazione di Azure Active Directory.

## <a name="secure-calls-to-your-api-without-changing-code"></a>Chiamate protette all'API senza modificare il codice

In questa sezione si creeranno due applicazioni di Azure Active Directory, una per l'app per la logica e una per l'app Web. Autenticare le chiamate all'app Web usando l'entità servizio (ID client e segreto) associata all'app Azure Active Directory dell'app per la logica. Infine includere l'ID applicazione nella definizione di app per la logica.

### <a name="part-1-set-up-an-application-identity-for-your-logic-app"></a>Parte 1: Configurare l'identità applicazione per l'app per la logica

L'app per la logica usa questa identità applicazione per l'autenticazione con Azure Active Directory. È sufficiente impostare l'identità una sola volta per la directory. Ad esempio, si può scegliere di usare la stessa identità per tutte le app per la logica, anche se è possibile creare identità univoche per ogni app per la logica. È possibile impostare queste identità nel Portale di Azure o usare PowerShell.

#### <a name="create-the-application-identity-in-the-azure-classic-portal"></a>Creare l'identità applicazione tramite il portale di Azure classico

1. Nel portale di Azure classico passare ad [Azure Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory). 
2. Selezionare la directory che si usa per l'app Web.
3. Selezionare la scheda **Applicazioni**. In fondo alla pagina fare clic su **Aggiungi** nella barra dei comandi.
5. Assegnare un nome all'identità dell'app e fare clic sulla freccia Avanti.
6. In **Proprietà dell'app** inserire una stringa univoca formattata come dominio e fare clic sul segno di spunta.
7. Scegliere la scheda **Configura**. Passare a **ID client** e copiare l'ID client per usarlo nell'app per la logica.
8. In **Chiavi**, aprire l'elenco **Seleziona durata** e selezionare la durata della chiave.
9. Nella parte inferiore della pagina fare clic su **Salva**. Potrebbe essere necessario attendere alcuni secondi.
10. Assicurarsi di copiare la chiave che verrà visualizzata in **Chiavi** così da poterla usare nell'app per la logica.

#### <a name="create-the-application-identity-using-powershell"></a>Creare l'identità dell'applicazione tramite PowerShell

1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. Assicurarsi di copiare l'**ID tenant**, l'**ID applicazione** e la password usati.

### <a name="part-2-protect-your-web-app-with-an-azure-active-directory-app-identity"></a>Parte 2: Proteggere l'app Web con un'identità dell'app di Azure Active Directory

Se l'app Web è già stata distribuita, è possibile abilitare l'autorizzazione nel Portale di Azure. In caso contrario, si può fare in modo che l'autorizzazione venga abilitata come parte della distribuzione di Azure Resource Manager.

#### <a name="enable-authorization-in-the-azure-portal"></a>Abilitare l'autorizzazione nel Portale di Azure

1. Individuare e selezionare l'app Web. In **Impostazioni** scegliere **Autenticazione/Autorizzazione**.
2. In **Autenticazione servizio app**, **attivare** l'autenticazione e scegliere **Salva**.

A questo punto viene creata automaticamente un'applicazione. L'ID client di questa applicazione è necessario nella parte 3, quindi sarà necessario seguire questa procedura:

1. Nel portale di Azure classico passare ad [Azure Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory).
2.    Selezionare la propria directory.
3. Nella casella di ricerca individuare l'app.
4. Nell'elenco selezionare l'app.
5. Scegliere la scheda **Configura** in cui verrà visualizzato l'**ID client**.

#### <a name="deploy-your-web-app-using-an-azure-resource-manager-template"></a>Distribuire l'app Web usando un modello di Azure Resource Manager

In primo luogo, è necessario creare un'applicazione per l'app Web diversa dall'applicazione usata per l'app per la logica. Per iniziare, seguire la procedura descritta in precedenza nella parte 1, usando però per **HomePage** e **IdentifierUris** l'effettivo https://**URL** dell'app Web.

> [!NOTE]
> Quando si crea l'applicazione per l'app Web, è necessario usare il [portale di Azure classico](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory). Il cmdlet di PowerShell non consente di impostare le autorizzazioni necessarie per l'accesso degli utenti in un sito Web.

Dopo aver ottenuto l'ID client e l'ID tenant, includere questa parte come risorsa secondaria dell'app Web nel modello di distribuzione:

```
"resources": [
    {
        "apiVersion": "2015-08-01",
        "name": "web",
        "type": "config",
        "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
        "properties": {
            "siteAuthEnabled": true,
            "siteAuthSettings": {
              "clientId": "<<clientID>>",
              "issuer": "https://sts.windows.net/<<tenantID>>/",
            }
        }
    }
]
```

Per eseguire automaticamente e nello stesso momento la distribuzione di un'app Web vuota e di un'app per la logica che usano Azure Active Directory, fare clic su **Distribuisci in Azure**:

[![Distribuzione in Azure](media/logic-apps-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

Per un modello completo, vedere [Chiamate dell'app per la logica di un'API personalizzata ospitata in un servizio app e protetta da Azure Active Directory](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json).

### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Parte 3: Compilare la sezione Autorizzazione nell'app per la logica

Nella sezione **Autorizzazione** dell'azione **HTTP**:

`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| Elemento | Descrizione |
| ------- | ----------- |
| type |Tipo di autenticazione. Per l'autenticazione ActiveDirectoryOAuth, il valore è `ActiveDirectoryOAuth`. |
| tenant |Identificativo del tenant usato per identificare il tenant di Active Directory. |
| audience |Obbligatorio. Risorsa a cui ci si connette. |
| clientID |Identificativo del client per l'applicazione Azure AD. |
| secret |Obbligatorio. Segreto del client che richiede il token. |

Questa sezione dell'autorizzazione è già stata configurata nel modello precedente, ma se si crea direttamente l'app per la logica, sarà necessario includere l'intera sezione relativa all'autorizzazione.

## <a name="secure-your-api-in-code"></a>Proteggere l'API nel codice

### <a name="certificate-authentication"></a>Autenticazione del certificato

È possibile usare i certificati client per convalidare le richieste in ingresso all'app Web. Per informazioni su come configurare il codice, vedere [Come configurare l'autenticazione reciproca TLS per un'app Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md).

Nella sezione **Autorizzazione** è necessario includere: 

`{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`

| Elemento | Descrizione |
| ------- | ----------- |
| type |Obbligatorio. Tipo di autenticazione. Per i certificati client SSL, il valore deve essere `ClientCertificate`. |
| pfx |Obbligatorio. I contenuti del file PFX in codifica Base64. |
| password |Obbligatorio. La password per accedere al file PFX. |

### <a name="basic-authentication"></a>Autenticazione di base

Per convalidare le richieste in ingresso, è possibile usare l'autenticazione di base, ovvero nome utente e password. L'autenticazione di base è un modello comune applicabile a qualsiasi linguaggio usato per compilare l'app.

Nella sezione **Autorizzazione** è necessario includere:

`{"type": "basic","username": "test","password": "test"}`.

| Elemento | Descrizione |
| --- | --- |
| type |Obbligatorio. Tipo di autenticazione. Per l'autenticazione di Base, il valore deve essere `Basic`. |
| username |Obbligatorio. Nome utente da autenticare. |
| password |Obbligatorio. Password da autenticare. |

### <a name="handle-azure-active-directory-authentication-in-code"></a>Gestire l'autenticazione di Azure Active Directory nel codice

Per impostazione predefinita, l'autenticazione di Azure Active Directory che si abilita nel Portale di Azure non offre un'autorizzazione con granularità fine. Ad esempio, questa autenticazione non blocca l'API su un utente o un'app specifica, ma solo su un tenant particolare.

Per limitare l'API all'app per la logica, ad esempio, nel codice, estrarre l'intestazione con il token JWT. Verificare l'identità del chiamante e rifiutare le richieste che non corrispondono.

Per approfondimenti su come implementare l'autenticazione interamente nel codice e non usare la funzionalità del Portale di Azure, vedere [Eseguire l'autenticazione con l'istanza locale di Active Directory nell'app Azure](../app-service-web/web-sites-authentication-authorization.md).
È necessario seguire la procedura precedente per creare un'identità dell'applicazione per l'app per la logica e usarla per chiamare l'API.
