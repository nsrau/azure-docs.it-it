---
title: File di SharePoint-QnA Maker
titleSuffix: Azure Cognitive Services
description: Aggiungere origini dati di SharePoint protette alla Knowledge base per arricchire la Knowledge base con domande e risposte che possono essere protette con Active Directory.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: da73f42c17a3688e7f1f464ec4a3bbe77cbc9229
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955201"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Aggiungere un'origine dati di SharePoint protetta alla Knowledge base

Aggiungere origini dati di SharePoint protette alla Knowledge base per arricchire la Knowledge base con domande e risposte che possono essere protette con Active Directory. 

Quando si aggiunge un documento SharePoint protetto alla Knowledge base, come QnA Maker Manager, è necessario richiedere Active Directory autorizzazione per QnA Maker. Una volta che questa autorizzazione viene fornita da gestione Active Directory per QnA Maker per l'accesso a SharePoint, non è necessario specificarla nuovamente. Ogni ulteriore documento aggiuntivo alla Knowledge base non richiede l'autorizzazione se si trova nella stessa risorsa di SharePoint. 

Se il gestore QnA Maker Knowledge base non è il Active Directory Manager, sarà necessario comunicare con il gestore Active Directory per completare questo processo.

## <a name="add-supported-file-types-to-knowledge-base"></a>Aggiungere tipi di file supportati alla Knowledge base

È possibile aggiungere tutti i tipi di [file](../Concepts/data-sources-supported.md) supportati da QnA Maker da un sito di SharePoint alla Knowledge base. Se la risorsa file è protetta, potrebbe essere necessario concedere le [autorizzazioni](#permissions) .

1. Dalla libreria con il sito di SharePoint, selezionare il menu `...`con i puntini di sospensione del file.
1. Copiare l'URL del file.

   ![Ottenere l'URL del file di SharePoint selezionando il menu con i puntini di sospensione del file e quindi copiando l'URL.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. Nella pagina **Impostazioni** del portale di QnA Maker [aggiungere l'URL](edit-knowledge-base.md#add-datasource) alla Knowledge base. 

### <a name="images-with-sharepoint-files"></a>Immagini con file di SharePoint

Se i file includono immagini, non vengono estratti. È possibile aggiungere l'immagine, dal portale di QnA Maker, dopo che il file è stato estratto in coppie QnA.

Aggiungere l'immagine con la sintassi Markdown seguente: 

```markdown
![Explanation or description of image](URL of public image)
```

Il testo racchiuso tra parentesi quadre `[]`,, spiega l'immagine. L'URL racchiuso tra parentesi, `()`, è il collegamento diretto all'immagine. 

Quando si esegue il test della coppia QnA nel pannello di test interattivo, nel portale QnA Maker viene visualizzata l'immagine anziché il testo Markdown. Ciò consente di verificare che l'immagine possa essere recuperata pubblicamente dall'applicazione client.

## <a name="permissions"></a>Autorizzazioni

La concessione di autorizzazioni si verifica quando un file protetto da un server SharePoint viene aggiunto a una Knowledge base. A seconda della configurazione di SharePoint e delle autorizzazioni della persona che aggiunge il file, potrebbe essere necessario:

* nessun passaggio aggiuntivo: la persona che aggiunge il file dispone di tutte le autorizzazioni necessarie.
* viene descritta la procedura sia per la [gestione della Knowledge base](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) sia per la [gestione Active Directory](#active-directory-manager-grant-file-read-access-to-qna-maker).

Vedere i passaggi elencati di seguito. 

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Gestione Knowledge Base: aggiungere un'origine dati SharePoint nel portale di QnA Maker

Quando il gestore di **QnA Maker** aggiunge un documento SharePoint protetto a una Knowledge base, il gestore della Knowledge base avvia una richiesta di autorizzazione che deve essere completata da Active Directory Manager.

La richiesta inizia con un popup per l'autenticazione a un account Active Directory. 

![Autenticare l'account utente](../media/add-sharepoint-datasources/authenticate-user-account.png)

Una volta che l'account è stato selezionato da QnA Maker Manager, l'amministratore di Azure Active Directory riceverà un avviso che deve consentire l'accesso all'app QnA Maker (non all'QnA Maker Manager) alla risorsa di SharePoint. Il gestore di Azure Active Directory dovrà eseguire questa operazione per ogni risorsa di SharePoint, ma non per tutti i documenti presenti nella risorsa. 

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Gestione Active Directory: concedere l'accesso in lettura ai file QnA Maker

Il gestore di Active Directory (non il responsabile della gestione QnA Maker) deve concedere l'accesso ai QnA Maker per accedere alla risorsa di SharePoint selezionando [questo collegamento](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) per autorizzare l'app del portale di QnA Maker SharePoint Enterprise a disporre delle autorizzazioni di lettura file. 

![Azure Active Directory Manager concede l'autorizzazione in modo interattivo](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`. 
-->
<!--
### Grant access from the interactive pop-up window 

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions. 

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**. 

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**. 

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**. 

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows. 

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Concedere l'accesso dall'interfaccia di amministrazione di Azure Active Directory

1. Il gestore Active Directory accede al portale di Azure e apre le **[applicazioni aziendali](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)** . 

1. `QnAMakerPortalSharePoint` Cercare l'app selezionare l'QnA Maker. 

    [![Cercare QnAMakerPortalSharePoint nell'elenco di app aziendali](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. In **sicurezza**passare a **autorizzazioni**. Selezionare **concedi il consenso dell'amministratore per l'organizzazione**. 

    [![Selezionare utente autenticato per Active Directory amministratore](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Selezionare un account di accesso con autorizzazioni per concedere le autorizzazioni per il Active Directory. 


  
<!--

## Add SharePoint data source with APIs

You need to get the SharePoint file's URI before adding it to QnA Maker. 

## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64). 

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes. 

    * Remove the equal character, `=` from the end of the value. 
    * Replace `/` with `_`. 
    * Replace `+` with `-`. 
    * Append `u!` to be beginning of the string. 

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center. 
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**. 
1. Select **Delete** to remove permissions. 

-->

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Collaborare alla Knowledge base](collaborate-knowledge-base.md)
