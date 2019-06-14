---
title: File di SharePoint - QnA Maker
titleSuffix: Azure Cognitive Services
description: Aggiungere le origini dati Sharepoint protette alla knowledge base per arricchire la knowledge base con domande e risposte che possono essere protetta con Active Directory.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/05/2019
ms.author: tulasim
ms.openlocfilehash: e479cf1729b7dcd2ed2f2470f2a935bdf94af80b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65954961"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Aggiungere un'origine dati Sharepoint protetta alla knowledge base

Aggiungere le origini dati Sharepoint protette alla knowledge base per arricchire la knowledge base con domande e risposte che possono essere protetta con Active Directory. 

Quando si aggiunge un documento protetto di Sharepoint per la knowledge base, come la gestione di QnA Maker, è necessario richiedere l'autorizzazione di Active Directory per QnA Maker. Dopo aver concesso l'autorizzazione di gestione di Active Directory per QnA Maker per l'accesso a Sharepoint, non deve necessariamente essere fornito nuovamente. Ogni aggiunta successiva documento alla knowledge base autorizzazione non è necessario se è nella stessa risorsa di Sharepoint. 

Se non la gestione della knowledge base di QnA Maker è la gestione di Active Directory, è necessario comunicare con la gestione di Active Directory per completare questo processo.

## <a name="add-supported-file-types-to-knowledge-base"></a>Aggiungere tipi di file supportati alla knowledge base

È possibile aggiungere tutte supportate QnA Maker [tipi di file](../Concepts/data-sources-supported.md) da un server Sharepoint per l'articolo della knowledge base. Potrebbe essere necessario concedere [autorizzazioni](#permissions) se la risorsa del file è protetto.

1. Dal server di Sharepoint, selezionare il menu di puntini di sospensione del file, `...`.
1. Copiare l'URL del file.

    ![Ottenere l'URL di file di Sharepoint selezionando il menu di puntini di sospensione del file quindi copiare l'URL.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. Nel portale di QnA Maker, sul **le impostazioni** pagina [aggiungere l'URL](edit-knowledge-base.md#add-datasource) alla knowledge base. 

## <a name="permissions"></a>Autorizzazioni

Concessione di autorizzazioni, si verifica quando viene aggiunto un file protetto da un server Sharepoint a una knowledge base. A seconda della configurazione di Sharepoint remota e le autorizzazioni della persona aggiunta del file, ciò potrebbe implicare:

* senza ulteriori operazioni, la persona che aggiunge il file contiene tutte le autorizzazioni necessarie.
* i passaggi da entrambe [gestione knowledge base](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) e [manager di Active Directory](#active-directory-manager-grant-file-read-access-to-qna-maker).

Vedere i passaggi elencati di seguito. 

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Gestione della Knowledge base: aggiungere l'origine dati Sharepoint nel portale di QnA Maker

Quando la **manager di QnA Maker** aggiunge un documento protetto di Sharepoint a una knowledge base, l'articolo della knowledge base manager avvia una richiesta di autorizzazione che deve effettuare la gestione di Active Directory.

La richiesta inizia con un messaggio popup per l'autenticazione a un account di Active Directory. 

![Autenticare Account utente](../media/add-sharepoint-datasources/authenticate-user-account.png)

Dopo il gestore di QnA Maker consente di selezionare l'account, amministratore di Active Directory riceverà una notifica che è necessario consentire QnA Maker app (non gestione QnA Maker) di accedere alla risorsa di Sharepoint. La gestione di Active Directory sarà necessario eseguire questa operazione per tutte le risorse di Sharepoint, ma non tutti i documenti in tale risorsa. 

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Gestione di Active directory: concedere l'accesso in lettura di file per QnA Maker

La gestione di Active Directory (non gestione QnA Maker) deve concedere l'accesso a QnA Maker per accedere alla risorsa Sharepoint selezionando [questo collegamento](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) per autorizzare l'app di QnA Maker portale Sharepoint enterprise per avere file letto autorizzazioni. 

![Gestione di Azure Active Directory concede l'autorizzazione in modo interattivo](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharepoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`. 
-->
<!--
### Grant access from the interactive pop-up window 

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharepoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharepoint**, and a list of permissions requested. Select **Accept** to provide those permissions. 

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**. 

1. Search for and select the **QnAMakerPortalSharepoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

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

1. La gestione di Active Directory esegue l'accesso al portale di Azure e viene aperto  **[applicazioni aziendali](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)** . 

1. Cercare `QnAMakerPortalSharepoint` selezionare l'app di QnA Maker. 

    [![Cercare QnAMakerPortalSharepoint nell'elenco di App aziendali](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. Sotto **sicurezza**passare alla **autorizzazioni**. Selezionare **concedere il consenso dell'amministratore per organizzazione**. 

    [![Selezionare l'utente autenticato per l'amministratore di Active Directory](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Selezionare un account di accesso con le autorizzazioni per concedere le autorizzazioni per il servizio Active Directory. 


  
<!--

## Add Sharepoint data source with APIs

You need to get the Sharepoint file's URI before adding it to QnA Maker. 

## Get Sharepoint File URI

Use the following steps to transform the Sharepoint URL into a sharing token.

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

### Add or update a Sharepoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<sharepoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from Sharepoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center. 
1. When you select the **QnAMakerPortalSharepoint**, select **Overview**. 
1. Select **Delete** to remove permissions. 

-->

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Collaborazione nella tua knowledge base](collaborate-knowledge-base.md)
