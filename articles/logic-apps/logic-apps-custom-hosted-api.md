---
title: Chiamare un&quot;API personalizzata in App per la logica di Azure | Documentazione Microsoft
description: Uso dell&quot;API personalizzata ospitata con App per la logica
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
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: 32a2b4435761e8bc26c184ee00613539e4639552


---
# <a name="using-your-custom-api-hosted-on-app-service-with-logic-apps"></a>Uso dell'API personalizzata ospitata nel servizio app con App per la logica
Anche se la funzionalità App per la logica include un set completo di oltre 40 connettori, è possibile chiamare l'API personalizzata che può eseguire il codice personalizzato. Uno dei modi più semplici e scalabili per ospitare un'API Web personalizzata consiste nell'uso del servizio app. Questo articolo illustra come chiamare un'API Web ospitata in un'app per le API, in un'app Web o in un'app per dispositivi mobili del servizio app.

Per informazioni sulla compilazione di API come trigger o azione all'interno di app per la logica, vedere [questo articolo](../logic-apps/logic-apps-create-api-app.md).

## <a name="deploy-your-web-app"></a>Distribuire l'app Web
Prima di tutto è necessario distribuire l'API come un'app Web nel servizio app. Queste istruzioni riguardano la distribuzione di base: [Creare un'app Web ASP.NET](../app-service-web/web-sites-dotnet-get-started.md).  Sebbene sia possibile chiamare le API da qualsiasi app per la logica, per un'esperienza ottimale si consiglia di aggiungere metadati Swagger per facilitare l'integrazione con le azioni delle app per la logica.  Per informazioni dettagliate, vedere la pagina sull' [aggiunta di swagger](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui).

### <a name="api-settings"></a>Impostazioni API
Per consentire alla finestra di progettazione delle app per la logica di analizzare Swagger, è importante abilitare CORS e impostare le proprietà APIDefinition dell'app Web.  La configurazione nel portale di Azure è molto semplice.  Aprire il pannello delle impostazioni dell'app Web e nella sezione API impostare "Definizione API" sull'URL del file swagger.json (in genere https://{nome}.azurewebsites.net/swagger/docs/v1) e aggiungere un criterio CORS per "*" per consentire le richieste dalla finestra di progettazione delle app per la logica.

## <a name="calling-into-the-api"></a>Chiamata all'API
Nel portale delle app per la logica, se CORS e le proprietà di definizione API sono state impostate, sarà possibile aggiungere facilmente le azioni API personalizzate all'interno del flusso.  Nella finestra di progettazione è possibile specificare la ricerca dei siti Web di sottoscrizione per visualizzare l'elenco dei siti con URL swagger definito.  È inoltre possibile usare l'azione HTTP + Swagger per puntare a uno swagger e visualizzare l'elenco delle azioni e degli input disponibili.  Infine, è sempre possibile creare una richiesta usando l'azione HTTP per chiamare un'API, incluse le API che non dispongono o non espongono un documento swagger.

Per proteggere l'API, sono disponibili due metodi:

1. Non è richiesta alcuna modifica del codice: per proteggere l'API è possibile usare Azure Active Directory senza richiedere modifiche del codice o la ridistribuzione.
2. Applicare l'autenticazione di base, l'autenticazione AAD o l'autenticazione del certificato nel codice dell'API.

## <a name="securing-calls-to-your-api-without-a-code-change"></a>Protezione delle chiamate all'API senza modifica del codice
In questa sezione si creeranno due applicazioni di Azure Active Directory, una per l'app per la logica e una per l'app Web.  Si autenticheranno le chiamate all'app Web usando l'entità servizio (ID client e segreto) associata all'applicazione AAD per l'app per la logica. Infine si includerà l'ID applicazione nella definizione di app per la logica.

### <a name="part-1-setting-up-an-application-identity-for-your-logic-app"></a>Parte 1: Configurazione dell'identità applicazione per l'app per la logica
Ecco ciò che usa l'app per la logica per autenticarsi con Active Directory. È *necessario* eseguire questa operazione una sola volta per la directory. Ad esempio, si può scegliere di usare la stessa identità per tutte le app per la logica, anche se è possibile creare identità univoche per ogni app per la logica, se necessario. A questo scopo, è possibile usare l'interfaccia utente oppure PowerShell.

#### <a name="create-the-application-identity-using-the-azure-classic-portal"></a>Creare l'identità dell'applicazione tramite il portale di Azure classico
1. Passare ad [Active Directory nel portale di Azure classico](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) e selezionare la directory usata per l'app Web.
2. Fare clic sulla scheda **Applicazioni** .
3. In fondo alla pagina fare clic su **Aggiungi** nella barra dei comandi.
4. Assegnare un Nome all'identità e fare clic sulla freccia Avanti.
5. Nelle due caselle di testo inserire una stringa univoca formattata come dominio e fare clic sul segno di spunta.
6. Fare clic sulla scheda **Configura** per questa applicazione.
7. Copiare l' **ID client**che verrà usato nell'app per la logica.
8. Nella sezione **Chiavi** fare clic su **Seleziona durata** e scegliere 1 o 2 anni.
9. Fare clic sul pulsante **Salva** nella parte inferiore della schermata. Potrebbe essere necessario attendere alcuni secondi.
10. Ora assicurarsi di copiare la chiave nella casella. Verrà usata anche nell'app per la logica.

#### <a name="create-the-application-identity-using-powershell"></a>Creare l'identità dell'applicazione tramite PowerShell
1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. Assicurarsi di copiare i valori di **ID tenant**, **ID applicazione** e password usati.

### <a name="part-2-protect-your-web-app-with-an-aad-app-identity"></a>Parte 2: Proteggere l'app Web con un'identità dell'app AAD
Se l'app Web è già stata distribuita, si può semplicemente abilitarla nel portale. In caso contrario, si può fare in modo che l'autorizzazione venga abilitata come parte della distribuzione di Gestione risorse di Azure.

#### <a name="enable-authorization-in-the-azure-portal"></a>Abilitare l'autorizzazione nel portale di Azure
1. Passare all'app Web e fare clic su **Impostazioni** nella barra dei comandi.
2. Fare clic su **Autorizzazione/Autenticazione**.
3. Impostare su **Attivata**.

A questo punto viene creata automaticamente un'applicazione. L'ID client di questa applicazione è necessario nella parte 3, quindi sarà necessario:

1. Passare ad [Active Directory nel portale di Azure classico](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) e selezionare la directory.
2. Cercare l'app nella casella di ricerca.
3. Fare clic sull'app nell'elenco.
4. Fare clic sulla scheda **Configura** .
5. Dovrebbe essere visualizzato l' **ID client**

#### <a name="deploying-your-web-app-using-an-arm-template"></a>Distribuzione dell'app Web con un modello ARM
Prima di tutto, è necessario creare un'applicazione per l'app Web. Dovrà essere diversa dall'applicazione usata per l'app per la logica. Per iniziare, seguire la procedura descritta sopra nella parte 1, usando però per **HomePage** e **IdentifierUris** l'effettivo https://**URL** dell'app Web.

> [!NOTE]
> Quando si crea l'applicazione per l'app Web, è necessario usare l' [approccio basato sul portale di Azure classico](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory), poiché il cmdlet di PowerShell non configura le autorizzazioni necessarie per l'accesso degli utenti a un sito Web.
> 
> 

Dopo aver ottenuto l'ID client e l'ID tenant, includere il codice seguente come risorsa secondaria dell'app Web nel modello di distribuzione:

```
"resources" : [
    {
        "apiVersion" : "2015-08-01",
        "name" : "web",
        "type" : "config",
        "dependsOn" : [
            "[concat('Microsoft.Web/sites/','parameters('webAppName'))]"
        ],
        "properties" : {
            "siteAuthEnabled": true,
            "siteAuthSettings": {
              "clientId": "<<clientID>>",
              "issuer": "https://sts.windows.net/<<tenantID>>/",
            }
        }
    }
]
```

Per eseguire automaticamente e nello stesso momento la distribuzione di un'app Web vuota e di un'app per la logica che usano AAD, fare clic sul pulsante seguente:

[![Distribuzione in Azure](media/logic-apps-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

Per un modello completo, vedere la pagina relativa alle [chiamate dell'app per la logica a un'API personalizzata ospitata in un servizio app e protetta da AAD](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json).

### <a name="part-3-populate-the-authorization-section-in-the-logic-app"></a>Parte 3: Popolare la sezione Autorizzazione nell'app per la logica
Nella sezione **Authorization** dell'azione **HTTP**: `{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| Elemento | Descrizione |
| --- | --- |
| type |Tipo di autenticazione. Per l'autenticazione ActiveDirectoryOAuth, il valore è ActiveDirectoryOAuth. |
| tenant |Identificativo del tenant usato per identificare il tenant di Active Directory. |
| audience |Obbligatorio. Risorsa a cui ci si connette. |
| clientID |Identificativo del client per l'applicazione Azure AD. |
| secret |Obbligatorio. Segreto del client che richiede il token. |

Questa impostazione è già configurata nel modello precedente, ma se si crea direttamente l'app per la logica, sarà necessario includere l'intera sezione relativa all'autorizzazione.

## <a name="securing-your-api-in-code"></a>Protezione dell'API nel codice
### <a name="certificate-auth"></a>Autenticazione del certificato
È possibile usare i certificati client per convalidare le richieste in ingresso all'app Web. Per informazioni su come configurare il codice, vedere [Come configurare l'autenticazione reciproca TLS per un'app Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md) .

Nella sezione *Authorization* è necessario specificare: `{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`.

| Elemento | Descrizione |
| --- | --- |
| type |Obbligatorio. Tipo di autenticazione. Per i certificati client SSL, il valore deve essere ClientCertificate. |
| pfx |Obbligatorio. I contenuti del file PFX in codifica Base64. |
| password |Obbligatorio. La password per accedere al file PFX. |

### <a name="basic-auth"></a>Autenticazione di base
Per convalidare le richieste in ingresso, è possibile usare l'autenticazione di base, ovvero nome utente e password. L'autenticazione di base è un modello comune ed è possibile eseguirla in qualsiasi linguaggio usato per compilare l'app.

Nella sezione *Authorization* è necessario specificare: `{"type": "basic","username": "test","password": "test"}`.

| Elemento | Descrizione |
| --- | --- |
| type |Obbligatorio. Tipo di autenticazione. Per l'autenticazione di Base, il valore deve essere Basic. |
| username |Obbligatorio. Nome utente da autenticare. |
| password |Obbligatorio. Password da autenticare. |

### <a name="handle-aad-auth-in-code"></a>Gestire l'autorizzazione AAD nel codice
Per impostazione predefinita, l'autenticazione di Azure Active Directory che si abilita nel portale non esegue un'autorizzazione con granularità fine. Ad esempio, non blocca l'API su un utente o un'app specifica, ma solo su un tenant particolare.

Se si vuole limitare l'API solo all'app per la logica, ad esempio nel codice, è possibile estrarre l'intestazione che contiene il JWT e verificare chi è il chiamante, rifiutando le richieste che non corrispondono.

Proseguendo, se si vuole implementarla interamente nel codice e sfruttare la funzionalità del portale, è possibile leggere questo articolo: [Usare Active Directory per l'autenticazione nel servizio app di Azure](../app-service-web/web-sites-authentication-authorization.md).

È comunque necessario seguire la procedura precedente per creare un'identità dell'applicazione per l'app per la logica e usarla per chiamare l'API.




<!--HONumber=Jan17_HO3-->


