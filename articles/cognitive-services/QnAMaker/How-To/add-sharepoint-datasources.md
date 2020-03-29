---
title: File di SharePoint - QnA Maker
description: Aggiungere origini dati di SharePoint protette alla Knowledge Base per arricchire la Knowledge Base con domande e risposte che possono essere protette con Active Directory.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 6f05079e39c8afb001bd4ba09d68f435c18efad5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294886"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Aggiungere un'origine dati di SharePoint protetta alla Knowledge Base

Aggiungere origini dati di SharePoint basate su cloud protette alla Knowledge Base per arricchire la Knowledge Base con domande e risposte che possono essere protette con Active Directory.

Quando si aggiunge un documento di SharePoint protetto alla Knowledge Base, come responsabile di QnA Maker, è necessario richiedere l'autorizzazione di Active Directory per QnA Maker. Una volta che questa autorizzazione viene concessa dal gestore di Active Directory a QnA Maker per l'accesso a SharePoint, non deve essere dato di nuovo. Ogni aggiunta di documenti successivi alla Knowledge Base non sarà necessaria l'autorizzazione se si trova nella stessa risorsa di SharePoint.Each subsequent document addition to the knowledge base will not need authorization if it is in the same SharePoint resource.

Se il gestore della Knowledge Base di QnA Maker non è il gestore di Active Directory, sarà necessario comunicare con il responsabile di Active Directory per completare questo processo.

## <a name="prerequisites"></a>Prerequisiti

* SharePoint basato su cloud - QnA Maker utilizza Microsoft Graph per le autorizzazioni. Se SharePoint è locale, non sarà possibile estrarre da SharePoint perché Microsoft Graph non sarà in grado di determinare le autorizzazioni.
* Formato URL - QnA Maker supporta solo gli URL di SharePoint generati per la condivisione e di formato`https://\*.sharepoint.com`

## <a name="add-supported-file-types-to-knowledge-base"></a>Aggiungere tipi di file supportati alla Knowledge Base

È possibile aggiungere tutti i [tipi di file](../Concepts/content-types.md) supportati da QnA Maker da un sito di SharePoint alla Knowledge Base. Potrebbe essere necessario concedere [autorizzazioni](#permissions) se la risorsa file è protetta.

1. Dalla raccolta con il sito di SharePoint selezionare il `...`menu con i puntini di sospensione del file, .
1. Copiare l'URL del file.

   ![Ottenere l'URL del file di SharePoint selezionando il menu con i puntini di sospensione del file, quindi copiando l'URL.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. Nel portale QnA Maker, nella pagina **Impostazioni,** [aggiungere l'URL](manage-knowledge-bases.md#edit-knowledge-base) alla Knowledge Base.

### <a name="images-with-sharepoint-files"></a>Immagini con file di SharePoint

Se i file includono immagini, questi non vengono estratti. È possibile aggiungere l'immagine dal portale QnA Maker dopo che il file è stato estratto in coppie QnA.

Aggiungere l'immagine con la sintassi markdown seguente:

```markdown
![Explanation or description of image](URL of public image)
```

Il testo tra parentesi `[]`quadre, , spiega l'immagine. L'URL tra parentesi, `()`, è il collegamento diretto all'immagine.

Quando si testa la coppia QnA nel pannello di test interattivo, nel portale QnA Maker, viene visualizzata l'immagine, anziché il testo markdown. In questo modo l'immagine può essere recuperata pubblicamente dall'applicazione client.

## <a name="permissions"></a>Autorizzazioni

La concessione delle autorizzazioni si verifica quando un file protetto da un server SharePoint viene aggiunto a una Knowledge Base. A seconda di come è impostato SharePoint e le autorizzazioni della persona che aggiunge il file, ciò potrebbe richiedere:

* nessuna procedura aggiuntiva: la persona che aggiunge il file dispone di tutte le autorizzazioni necessarie.
* da parte di [gestione della Knowledge Base](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) e di Gestione Active [Directory](#active-directory-manager-grant-file-read-access-to-qna-maker).

Vedere i passaggi elencati di seguito.

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Gestione Knowledge Base: aggiungere un'origine dati di SharePoint nel portale QnA Maker

Quando il **gestore QnA Maker** aggiunge un documento di SharePoint protetto a una Knowledge Base, il responsabile della Knowledge Base avvia una richiesta di autorizzazione che il responsabile di Active Directory deve completare.

La richiesta inizia con un popup per l'autenticazione a un account di Active Directory.

![Autentica account utente](../media/add-sharepoint-datasources/authenticate-user-account.png)

Dopo che il responsabile di QnA Maker seleziona l'account, l'amministratore di Azure Active Directory riceverà un avviso che deve consentire all'app QnA Maker (non al responsabile QnA Maker) l'accesso alla risorsa di SharePoint. Il manager di Azure Active Directory dovrà eseguire questa operazione per ogni risorsa di SharePoint, ma non per tutti i documenti in tale risorsa.

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Active Directory manager: concedere l'accesso in lettura aI file a QnA Maker

Il gestore di Active Directory (non il gestore QnA Maker) deve concedere l'accesso a QnA Maker per accedere alla risorsa di SharePoint selezionando [questo collegamento](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) per autorizzare l'app Enterprise di QnA Maker Portal SharePoint a disporre delle autorizzazioni di lettura dei file.

![Azure Active Directory manager concede l'autorizzazione in modo interattivoAzure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

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
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Concedere l'accesso dall'interfaccia di amministrazione di Azure Active DirectoryGrant access from the Azure Active Directory admin center

1. Il gestore di Active Directory accede al portale di Azure e apre **[le applicazioni aziendali.](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**

1. Cerca `QnAMakerPortalSharePoint` l'app Seleziona QnA Maker.

    [![Cercare QnAMakerPortalSharePoint nell'elenco delle app aziendali](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. In **Sicurezza**passare a **Autorizzazioni**. Selezionare **Concedi il consenso dell'amministratore per organizzazione**.

    [![Selezionare l'utente autenticato per l'amministratore di Active Directory](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Selezionare un account di accesso con autorizzazioni per concedere le autorizzazioni per Active Directory.



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
> [Collaborare alla Knowledge Base](collaborate-knowledge-base.md)
