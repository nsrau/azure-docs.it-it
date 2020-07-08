---
title: Distribuire criteri personalizzati con Azure Pipelines
titleSuffix: Azure AD B2C
description: Informazioni su come distribuire Azure AD B2C criteri personalizzati in una pipeline di integrazione continua/recapito continuo usando Azure Pipelines in Azure DevOps Services.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 411fa207323a9bff6cfcc3b17769203c444dd844
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388681"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Distribuire criteri personalizzati con Azure Pipelines

Usando una pipeline di integrazione e recapito continua configurata in [Azure Pipelines][devops-pipelines], è possibile includere i Azure ad B2C criteri personalizzati nell'automazione del controllo del codice e della distribuzione del software. Quando si esegue la distribuzione in ambienti Azure AD B2C diversi, ad esempio sviluppo, test e produzione, è consigliabile rimuovere i processi manuali ed eseguire test automatizzati usando Azure Pipelines.

Sono necessari tre passaggi principali per abilitare Azure Pipelines per gestire i criteri personalizzati all'interno Azure AD B2C:

1. Creare una registrazione dell'applicazione Web nel tenant di Azure AD B2C
1. Configurare un repository di Azure
1. Configurare una pipeline di Azure

> [!IMPORTANT]
> La gestione di Azure AD B2C criteri personalizzati con una pipeline di Azure usa attualmente le operazioni di **Anteprima** disponibili nell'endpoint API Microsoft Graph `/beta` . L'uso di queste API nelle applicazioni di produzione non è supportato. Per ulteriori informazioni, vedere il [riferimento all'endpoint dell'API REST di Microsoft Graph beta](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta).

## <a name="prerequisites"></a>Prerequisiti

* [Azure ad B2C tenant](tutorial-create-tenant.md)e credenziali per un utente nella directory con il ruolo di [amministratore dei criteri B2C Framework dell'esperienza](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)
* [Criteri personalizzati](custom-policy-get-started.md) caricati nel tenant
* [App di gestione](microsoft-graph-get-started.md) registrata nel tenant con i criteri di autorizzazione dell'API Microsoft Graph *. ReadWrite. TrustFramework*
* [Pipeline di Azure](https://azure.microsoft.com/services/devops/pipelines/)e accesso a un [progetto Azure DevOps Services][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Flusso di concessione di credenziali client

Lo scenario descritto di seguito consente di usare le chiamate da servizio a servizio tra Azure Pipelines e Azure AD B2C usando il flusso di [concessione delle credenziali client](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md)OAuth 2,0. Questo flusso di concessione consente a un servizio Web come Azure Pipelines (il client riservato) di usare le proprie credenziali anziché rappresentare un utente per eseguire l'autenticazione quando chiama un altro servizio Web (l'API Microsoft Graph, in questo caso). Azure Pipelines ottiene un token in modo non interattivo, quindi effettua richieste all'API Microsoft Graph.

## <a name="register-an-application-for-management-tasks"></a>Registrare un'applicazione per le attività di gestione

Come indicato nei [prerequisiti](#prerequisites), è necessaria la registrazione di un'applicazione che gli script di PowerShell, eseguiti da Azure Pipelines, possono usare per accedere alle risorse nel tenant.

Se si dispone già di una registrazione dell'applicazione usata per le attività di automazione, verificare che sia stata concessa l'autorizzazione **Microsoft Graph**  >  **policy policy**  >  **. ReadWrite. TrustFramework** all'interno delle **autorizzazioni API** della registrazione dell'app.

Per istruzioni sulla registrazione di un'applicazione di gestione, vedere [manage Azure ad B2C with Microsoft Graph](microsoft-graph-get-started.md).

## <a name="configure-an-azure-repo"></a>Configurare un repository di Azure

Con un'applicazione di gestione registrata, si è pronti per configurare un repository per i file dei criteri.

1. Accedere all'organizzazione Azure DevOps Services.
1. [Creare un nuovo progetto][devops-create-project] o selezionarne uno esistente.
1. Nel progetto passare a **repository** e selezionare la pagina **file** . Selezionare un repository esistente o crearne uno per questo esercizio.
1. Creare una cartella denominata *B2CAssets*. Denominare il file segnaposto obbligatorio *Readme.MD* ed eseguire il **commit** del file. Se lo si desidera, è possibile rimuovere il file in un secondo momento.
1. Aggiungere i file dei criteri di Azure AD B2C alla cartella *B2CAssets* Sono inclusi *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*e qualsiasi altro criterio creato. Registrare il nome file di ogni file di criteri di Azure AD B2C per l'uso in un passaggio successivo, che vengono usati come argomenti dello script di PowerShell.
1. Creare una cartella denominata *Scripts* nella directory radice del repository, denominare il file segnaposto *DeployToB2c.ps1*. Non eseguire il commit del file in questo momento, in un passaggio successivo.
1. Incollare lo script di PowerShell seguente in *DeployToB2c.ps1*, quindi eseguire il **commit** del file. Lo script acquisisce un token da Azure AD e chiama l'API Microsoft Graph per caricare i criteri all'interno della cartella *B2CAssets* nel tenant di Azure ad B2C.

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

## <a name="configure-your-azure-pipeline"></a>Configurare la pipeline di Azure

Con il repository inizializzato e popolato con i file dei criteri personalizzati, si è pronti per configurare la pipeline di rilascio.

### <a name="create-pipeline"></a>Creare una pipeline

1. Accedere all'organizzazione Azure DevOps Services e passare al progetto.
1. Nel progetto selezionare **pipeline**  >  **rilascia**la  >  **nuova pipeline**.
1. In **Seleziona un modello**selezionare **processo vuoto**.
1. Immettere un **nome**per la fase, ad esempio *DeployCustomPolicies*, quindi chiudere il riquadro.
1. Selezionare **Aggiungi un artefatto**e in **tipo di origine**selezionare **repository di Azure**.
    1. Scegliere il repository di origine contenente la cartella *Scripts* compilata con lo script di PowerShell.
    1. Scegliere un **ramo predefinito**. Se è stato creato un nuovo repository nella sezione precedente, il ramo predefinito è *Master*.
    1. Lasciare l'impostazione di **versione predefinita** *più recente dal ramo predefinito*.
    1. Immettere un **alias di origine** per il repository. Ad esempio, *policyRepo*. Non includere spazi nel nome alias.
1. Selezionare **Aggiungi**
1. Rinominare la pipeline in modo che corrisponda allo scopo. Ad esempio, *distribuire la pipeline dei criteri personalizzata*.
1. Selezionare **Save (Salva** ) per salvare la configurazione della pipeline.

### <a name="configure-pipeline-variables"></a>Configurare le variabili della pipeline

1. Selezionare la scheda **variabili** .
1. Aggiungere le variabili seguenti in **variabili pipeline** e impostare i relativi valori come specificato:

    | Nome | valore |
    | ---- | ----- |
    | `clientId` | **ID applicazione (client)** dell'applicazione registrata in precedenza. |
    | `clientSecret` | Valore del **segreto client** creato in precedenza. <br /> Modificare il tipo di variabile in **segreto** (selezionare l'icona di blocco). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, dove *-B2C-tenant* è il nome del tenant del Azure ad B2C. |

1. Selezionare **Save (Salva** ) per salvare le variabili.

### <a name="add-pipeline-tasks"></a>Aggiungi attività pipeline

Aggiungere quindi un'attività per distribuire un file di criteri.

1. Selezionare la scheda **attività** .
1. Selezionare **processo agente**, quindi selezionare il segno più ( **+** ) per aggiungere un'attività al processo dell'agente.
1. Cercare e selezionare **PowerShell**. Non selezionare "Azure PowerShell", "PowerShell nei computer di destinazione" o un'altra voce di PowerShell.
1. Selezionare l'attività **script di PowerShell** appena aggiunta.
1. Immettere i valori seguenti per l'attività script di PowerShell:
    * **Versione attività**: 2. *
    * **Nome visualizzato**: il nome dei criteri che questa attività deve caricare. Ad esempio, *B2C_1A_TrustFrameworkBase*.
    * **Tipo**: percorso file
    * **Percorso script**: selezionare i puntini di sospensione (***...***), passare alla cartella *script* e quindi selezionare il file *DeployToB2C.ps1* .
    * **Argomenti:**

        Immettere i valori seguenti per gli **argomenti**. Sostituire `{alias-name}` con l'alias specificato nella sezione precedente.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Se, ad esempio, l'alias specificato è *policyRepo*, la riga dell'argomento deve essere:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Selezionare **Save (Salva** ) per salvare il processo dell'agente.

L'attività appena aggiunta carica *un* file di criteri per Azure ad B2C. Prima di procedere, attivare manualmente il processo (**Crea versione**) per assicurarsi che venga completato correttamente prima di creare altre attività.

Se l'attività viene completata correttamente, aggiungere le attività di distribuzione eseguendo i passaggi precedenti per ognuno dei file di criteri personalizzati. Modificare i `-PolicyId` `-PathToFile` valori degli argomenti e per ogni criterio.

`PolicyId`È un valore trovato all'inizio di un file di criteri XML all'interno del nodo TrustFrameworkPolicy. Ad esempio, `PolicyId` nell'XML dei criteri seguente viene *B2C_1A_TrustFrameworkBase*:

```xml
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Quando si eseguono gli agenti e si caricano i file dei criteri, assicurarsi che siano caricati nell'ordine seguente:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

Il Framework dell'esperienza di identità impone questo ordine quando la struttura dei file è compilata in una catena gerarchica.

## <a name="test-your-pipeline"></a>Testare la pipeline

Per testare la pipeline di rilascio:

1. Selezionare **pipeline** e quindi **versioni**.
1. Selezionare la pipeline creata in precedenza, ad esempio *DeployCustomPolicies*.
1. Selezionare **Crea versione**, quindi selezionare **Crea** per accodare la versione.

Verrà visualizzato un banner di notifica che indica che una versione è stata accodata. Per visualizzarne lo stato, selezionare il collegamento nel banner di notifica oppure selezionarlo nell'elenco della scheda **versioni** .

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:

* [Chiamate da servizio a servizio mediante le credenziali client](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines
