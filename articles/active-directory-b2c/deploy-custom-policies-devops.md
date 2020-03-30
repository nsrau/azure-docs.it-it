---
title: Distribuire criteri personalizzati con le pipeline di AzureDeploy custom policies with Azure Pipelines
titleSuffix: Azure AD B2C
description: Informazioni su come distribuire criteri personalizzati di Azure AD B2C in una pipeline di ci-ani/CD usando le pipeline di Azure nei servizi DevOps di Azure.Learn how to deploy Azure AD B2C custom policies in a CI/CD pipeline by using Azure Pipelines in Azure DevOps Services.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b23b60ae49a4973fa04e6fa5f795f99536e32e7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188750"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Distribuire criteri personalizzati con le pipeline di AzureDeploy custom policies with Azure Pipelines

Usando una pipeline di integrazione e distribuzione (CI/CD) continua configurata nelle pipeline di [Azure,][devops-pipelines]è possibile includere i criteri personalizzati di Azure AD B2C nella distribuzione del software e nell'automazione del controllo del codice. Durante la distribuzione in ambienti B2C di Azure AD diversi, ad esempio sviluppo, test e produzione, è consigliabile rimuovere i processi manuali ed eseguire test automatizzati tramite Pipeline di Azure.As you deploy to different Azure AD B2C environments, for example dev, test, and production, we recommend that you remove manual processes and perform automated testing by using Azure Pipelines.

Per abilitare le pipeline di Azure per gestire i criteri personalizzati in Azure AD B2C sono necessari tre passaggi principali:There are three primary steps required for enabling Azure Pipelines to manage custom policies within Azure AD B2C:

1. Creare una registrazione dell'applicazione Web nel tenant B2C di Azure ADCreate a web application registration in your Azure AD B2C tenant
1. Configurare un repository di AzureConfigure an Azure Repo
1. Configurare una pipeline di AzureConfigure an Azure Pipeline

> [!IMPORTANT]
> La gestione dei criteri personalizzati di Azure AD B2C con `/beta` una pipeline di Azure usa attualmente le operazioni di anteprima disponibili nell'endpoint api Microsoft Graph.Managing Azure AD B2C custom policies with an Azure Pipeline currently uses **preview** operations available on the Microsoft Graph API endpoint. L'uso di queste API nelle applicazioni di produzione non è supportato. Per altre informazioni, vedere informazioni di [riferimento sull'endpoint beta dell'API REST di Microsoft Graph.For](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta)more information, see the Microsoft Graph REST API beta endpoint reference .

## <a name="prerequisites"></a>Prerequisiti

* [Tenant B2C](tutorial-create-tenant.md)di Azure AD e credenziali per un utente nella directory con il ruolo [di amministratore dei criteri B2C IEFAzure](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator) AD B2C tenant , and credentials for a user in the directory with the B2C IEF Policy Administrator role
* [Criteri personalizzati](custom-policy-get-started.md) caricati nel tenant
* [App di gestione](microsoft-graph-get-started.md) registrata nel tenant con l'autorizzazione dell'API Microsoft Graph *Policy.ReadWrite.TrustFramework*
* [Pipeline di Azure](https://azure.microsoft.com/services/devops/pipelines/)e accesso a un progetto di Servizi DevOps di AzureAzure Pipeline , and access to an [Azure DevOps Services project][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Le credenziali client concedono il flusso

Lo scenario descritto di seguito usa le chiamate da servizio a servizio tra le pipeline di Azure e Azure AD B2C usando il flusso di [concessione](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md)delle credenziali client OAuth 2.0. Questo flusso di concessione consente a un servizio Web come Azure Pipelines (il client riservato) di usare le proprie credenziali anziché rappresentare un utente per l'autenticazione quando si chiama un altro servizio Web (l'API Microsoft Graph, in questo caso). Azure Pipelines obtains a token non-interactively, then makes requests to the Microsoft Graph API.

## <a name="register-an-application-for-management-tasks"></a>Registrare un'applicazione per le attività di gestioneRegister an application for management tasks

Come indicato in [Prerequisiti,](#prerequisites)è necessaria una registrazione dell'applicazione che gli script di PowerShell, eseguiti dalle pipeline di Azure, possono essere usate per accedere alle risorse nel tenant.

Se si dispone già di una registrazione dell'applicazione utilizzata per le attività di automazione, assicurarsi che sia stata concessa l'autorizzazione**Criteri di** > Microsoft**Graph.ReadWrite.TrustFramework** all'interno delle **autorizzazioni API** della registrazione dell'app. **Microsoft Graph** > 

Per istruzioni sulla registrazione di un'applicazione di gestione, vedere [Gestire Azure AD B2C con Microsoft Graph.](microsoft-graph-get-started.md)

## <a name="configure-an-azure-repo"></a>Configurare un repository di AzureConfigure an Azure Repo

Con un'applicazione di gestione registrata, è possibile configurare un repository per i file di criteri.

1. Accedere all'organizzazione dei servizi DevOps di Azure.Sign in to your Azure DevOps Services organization.
1. [Creare un nuovo progetto][devops-create-project] o selezionarne uno esistente.
1. Nel progetto, accedere a **Repository** e selezionare la pagina **File.** Selezionare un repository esistente o crearne uno per questo esercizio.
1. Creare una cartella denominata *B2CAssets*. Assegnare al file segnaposto il nome *richiesto README.md* e **Commit** del file. È possibile rimuovere questo file in un secondo momento, se lo si desidera.
1. Aggiungere i file di criteri B2C di Azure AD alla cartella *B2CAssets.Add* your Azure AD B2C policy files to the B2CAssets folder. Sono inclusi *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*e qualsiasi altro criterio creato. Registrare il nome file di ogni file di criteri B2C di Azure AD da usare in un passaggio successivo (vengono usati come argomenti di script di PowerShell).
1. Creare una cartella denominata *Script* nella directory radice del repository, denominare il file segnaposto *DeployToB2c.ps1*. Non eseguire il commit del file a questo punto, lo farai in un passaggio successivo.
1. Incollare lo script di PowerShell seguente in *DeployToB2c.ps1*, quindi eseguire il **commit** del file. Lo script acquisisce un token da Azure AD e chiama l'API Microsoft Graph per caricare i criteri all'interno della cartella *B2CAssets* nel tenant B2C di Azure AD.

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>Configurare la pipeline di AzureConfigure your Azure pipeline

Con il repository inizializzato e popolato con i file di criteri personalizzati, è possibile configurare la pipeline di rilascio.

### <a name="create-pipeline"></a>Creare una pipeline

1. Accedere all'organizzazione dei servizi DevOps di Azure e passare al progetto.
1. Nel progetto selezionare **Pipelines** > **Releases** > **New pipeline**.
1. In **Selezionare un modello**selezionare Processo **vuoto**.
1. Immettere un **nome di fase**, ad esempio *DeployCustomPolicies*, quindi chiudere il riquadro.
1. Selezionare **Aggiungi un elemento**e in Tipo di **origine**selezionare **Archivio di Azure**.
    1. Scegliere il repository di origine contenente la cartella *Scripts* popolata con lo script di PowerShell.
    1. Scegliere un **ramo predefinito**. Se è stato creato un nuovo repository nella sezione precedente, il ramo predefinito è *master*.
    1. Lasciare l'impostazione **Versione predefinita** più recente dal *ramo predefinito*.
    1. Immettere un **alias di origine** per il repository. Ad esempio, *policyRepo*. Non includere spazi nel nome dell'alias.
1. Selezionare **Aggiungi**
1. Rinominare la pipeline in base alle finalità. Ad esempio, Distribuire la pipeline *dei criteri personalizzati*.
1. Selezionare **Salva** per salvare la configurazione della pipeline.

### <a name="configure-pipeline-variables"></a>Configurare le variabili della pipeline

1. Selezionare la scheda **Variabili.**
1. Aggiungere le seguenti variabili in Pipeline variabili e impostare i relativi valori come specificato:Add the following variables under **Pipeline variables** and set their values as specified:

    | Nome | valore |
    | ---- | ----- |
    | `clientId` | **ID applicazione (client)** dell'applicazione registrata in precedenza. |
    | `clientSecret` | Valore del **segreto client** creato in precedenza. <br /> Modificare il tipo di variabile in **secret** (selezionare l'icona del lucchetto). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, dove *tenant-b2c* è il nome del tenant B2C di Azure AD. |

1. Selezionare **Salva** per salvare le variabili.

### <a name="add-pipeline-tasks"></a>Aggiungere attività della pipeline

Aggiungere quindi un'attività per distribuire un file di criteri.

1. Selezionare la scheda **Attività.**
1. Selezionare **Processo agente**, quindi**+** selezionare il segno più ( ) per aggiungere un'attività al processo dell'agente.
1. Cercare e selezionare **PowerShell**. Non selezionare "Azure PowerShell", "PowerShell nei computer di destinazione" o un'altra voce di PowerShell.Do not select "Azure PowerShell", "PowerShell on target machines, or another PowerShell entry.
1. Selezionare l'attività **Script di PowerShell** appena aggiunta.
1. Immettere i valori seguenti per l'attività Script di PowerShell:Enter following values for the PowerShell Script task:
    * **Versione dell'attività**: 2
    * **Nome visualizzato**: il nome del criterio che l'attività deve caricare. Ad esempio, *B2C_1A_TrustFrameworkBase*.
    * **Tipo**: Percorso file
    * **Percorso script**: selezionare i ellissi (***...***), passare alla cartella *Script* e quindi selezionare il file *DeployToB2C.ps1.*
    * **Argomenti:**

        Immettere i seguenti valori per **Argomenti**. Sostituire `{alias-name}` con l'alias specificato nella sezione precedente.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Ad esempio, se l'alias specificato è *policyRepo*, la riga dell'argomento deve essere:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Selezionare **Salva** per salvare il processo dell'agente.

L'attività appena aggiunta carica *un* file di criteri in Azure AD B2C. Prima di procedere, attivare manualmente il processo (**Crea versione**) per assicurarsi che venga completato correttamente prima di creare attività aggiuntive.

Se l'attività viene completata correttamente, aggiungere attività di distribuzione eseguendo i passaggi precedenti per ognuno dei file di criteri personalizzati. Modificare `-PolicyId` i `-PathToFile` valori degli argomenti e per ogni criterio.

il `PolicyId` è un valore trovato all'inizio di un file di criteri XML all'interno del nodo TrustFrameworkPolicy. Ad esempio, `PolicyId` il codice XML dei criteri seguente è *B2C_1A_TrustFrameworkBase:*

```XML
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Quando si eseguono gli agenti e si caricano i file di criteri, assicurarsi che vengano caricati nell'ordine seguente:When running the agents and uploading the policy files, ensure they're uploaded in this order:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml (Modifica.xml)*
1. *PasswordReset.xml*

Identity Experience Framework applica questo ordine quando la struttura di file è basata su una catena gerarchica.

## <a name="test-your-pipeline"></a>Testare la pipeline

Per testare la pipeline di rilascio:To test your release pipeline:

1. Selezionare **Pipeline** e quindi **Rilasci**.
1. Selezionare la pipeline creata in precedenza, ad esempio *DeployCustomPolicies*.
1. Selezionare **Crea versione**, quindi **Crea** per accodare la versione.

Verrà visualizzato un banner di notifica che indica che una versione è stata accodata. Per visualizzarne lo stato, selezionare il collegamento nel banner di notifica o selezionarlo nell'elenco della scheda **Rilasci.**

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:

* [Chiamate da servizio a servizio con credenziali clientService-to-service calls using client credentials](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Servizi DevOps di AzureAzure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines
