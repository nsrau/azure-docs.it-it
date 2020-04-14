---
title: Usare l'identità gestita per autenticare il processo di Analisi di flusso di Azure nell'output di Power BIUse Managed Identity to authenticate your Azure Stream Analytics job to Power BI output
description: Questo articolo descrive come usare le identità gestite per autenticare il processo di Analisi di flusso di Azure nell'output di Power BI.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 31a5195038ef25acadc08e2acbedf8471b25833c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261415"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>Usare l'identità gestita per autenticare il processo di Analisi di flusso di Azure in Power BIUse Managed Identity to authenticate your Azure Stream Analytics job to Power BI

[L'autenticazione dell'identità gestita](../active-directory/managed-identities-azure-resources/overview.md) per l'output in Power BI consente ai processi di Analisi di flusso di accedere direttamente a un'area di lavoro all'interno dell'account Power BI. Questa funzionalità consente di automatizzare completamente le distribuzioni dei processi di Analisi di flusso, poiché non è più necessario per un utente accedere in modo interattivo a Power BI tramite il portale di Azure.This feature allows for deployments of Stream Analytics jobs to be fully automated, since it is no longer required for a user to interactively log in to Power BI via the Azure portal. Inoltre, i processi a esecuzione prolungata che scrivono in Power BI sono ora meglio supportati, poiché non sarà necessario autorizzare periodicamente il processo.

Questo articolo illustra come abilitare l'identità gestita per gli output di Power BI di un processo di Analisi di flusso tramite il portale di Azure e una distribuzione di Azure Resource Manager.This article shows you how to enable Managed Identity for the Power BI output(s) of a Stream Analytics job through the Azure portal and through an Azure Resource Manager deployment.

## <a name="prerequisites"></a>Prerequisiti

Per l'utilizzo di questa funzione sono necessari i seguenti elementi:

- Un account Power BI con una [licenza Pro](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro).

- Area di lavoro aggiornata all'interno dell'account Power BI. Per altri dettagli, vedere L'annuncio di Questa funzionalità di [Power BI.See Power BI's announcement](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/) of this feature for more details.

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Creare un processo di Analisi di flusso tramite il portale di AzureCreate a Stream Analytics job using the Azure portal

1. Creare un nuovo processo di Analisi di flusso o aprire un processo esistente nel portale di Azure.Create a new Stream Analytics job or open an existing job in the Azure portal. Dalla barra dei menu situata sul lato sinistro dello schermo, selezionare **Identità gestita** in **Configura**. Assicurarsi che l'opzione "Usa identità gestita assegnata dal sistema" sia selezionata, quindi selezionare il pulsante **Salva** nella parte inferiore dello schermo.

   ![Configurare l'identità gestita di Analisi di flussoConfigure Stream Analytics managed identity](./media/common/stream-analytics-enable-managed-identity.png)

2. Prima di configurare l'output, assegnare al processo Analisi di flusso l'accesso al processo di Power BI seguendo le istruzioni nella sezione [Concedere al processo di Analisi di flusso l'accesso al processo](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) di Power BI di questo articolo.

3. Passare alla sezione **Output** del processo di Analisi del flusso, selezionare **Aggiungi**e quindi scegliere **Power BI**. Selezionare quindi il pulsante **Autorizza** e accedere con l'account Power BI.

   ![Autorizzare con l'account Power BIAuthorize with Power BI account](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. Una volta autorizzato, un elenco a discesa verrà popolato con tutte le aree di lavoro a cui si ha accesso. Selezionare l'area di lavoro autorizzata nel passaggio precedente. Selezionare **quindi Managed Identity** come "Authentication mode". Infine, selezionare il pulsante **Salva.**

   ![Configurare l'output di Power BI con l'identità gestitaConfigure Power BI output with Managed Identity](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Distribuzione Azure Resource Manager

Azure Resource Manager consente di automatizzare completamente la distribuzione del processo di Analisi di flusso. È possibile distribuire modelli di Resource Manager usando Azure PowerShell o l'interfaccia della riga di comando di Azure.You can deploy Resource Manager templates using either Azure PowerShell or the [Azure CLI.](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) The below examples use the Azure CLI.


1. È possibile creare una risorsa Microsoft.StreamAnalytics/streamingjobs con un'identità gestita includendo la proprietà seguente nella sezione delle risorse del modello di Resource Manager:You can create a **Microsoft.StreamAnalytics/streamingjobs** resource with a Managed Identity by including the following property in the resource section of your Resource Manager template:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   Questa proprietà indica ad Azure Resource Manager di creare e gestire l'identità per il processo di Analisi di flusso. Di seguito è riportato un modello di Resource Manager di esempio che distribuisce un processo di Analisi di flusso con l'identità gestita abilitata e un sink di output di Power BI che usa l'identità gestita:Below is an example Resource Manager template that deploys a Stream Analytics job with Managed Identity enabled and a Power BI output sink that uses Managed Identity:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    Distribuire il processo precedente nel gruppo di risorse ExampleGroup usando il comando dell'interfaccia della riga di comando di Azure seguente:Deploy the job above to the Resource group **ExampleGroup** using the below Azure CLI command:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Dopo aver creato il processo, usare Azure Resource Manager per recuperare la definizione completa del processo.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    Il comando di cui sopra restituirà una risposta simile alla seguente:

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Se si prevede di usare l'API REST di Power BI per aggiungere il processo di Analisi di flusso all'area di lavoro di Power BI, prendere nota del "principalId" restituito.

3. Ora che il processo è stato creato, passare alla sezione Concedere al processo di [Analisi di flusso l'accesso all'area di lavoro](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) di Power BI di questo articolo.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Concedere al processo di Analisi di flusso l'accesso al processo di Power BI

Ora che il processo di Analisi di flusso è stato creato, può essere concesso l'accesso a un'area di lavoro di Power BI.

### <a name="use-the-power-bi-ui"></a>Usare l'interfaccia utente di Power BIUse the Power BI UI

   > [!Note]
   > Per aggiungere il processo Analisi di flusso all'area di lavoro di Power BI tramite l'interfaccia utente, è inoltre necessario abilitare l'accesso all'entità servizio nelle **impostazioni dello sviluppatore** nel portale di amministrazione di Power BI. Per altre informazioni, vedere [Introduzione a un'entità servizio.](https://docs.microsoft.com/power-bi/developer/embed-service-principal)

1. Passare alle impostazioni di accesso dell'area di lavoro. Per ulteriori informazioni, vedere questo articolo: [Concedere l'accesso all'area di lavoro.](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace)

2. Digita il nome del processo di Analisi di flusso nella casella di testo e seleziona **Collaboratore** come livello di accesso.

3. Selezionare **Aggiungi** e chiudere il riquadro.

   ![Aggiungere il processo di Analisi di flusso all'area di lavoro di Power BIAdd Stream Analytics job to Power BI workspace](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>Usare i cmdlet di PowerShell per Power BIUse the PowerShell cmdlets

1. Installare i `MicrosoftPowerBIMgmt` cmdlet di PowerShell di Power BI.

   > [!Important]
   > Assicurarsi di utilizzare la versione 1.0.821 o successiva dei cmdlet.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Accedere a Power BI.

```powershell
Login-PowerBI
```

3. Aggiungere il processo di Analisi di flusso come collaboratore all'area di lavoro.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>Usare l'API REST di Power BIUse the Power BI REST API

Il processo Analisi di flusso può anche essere aggiunto come collaboratore all'area di lavoro usando direttamente l'API REST "Aggiungi utente gruppo". La documentazione completa per questa API è disponibile qui: [Gruppi - Aggiungi utente gruppo](https://docs.microsoft.com/rest/api/power-bi/groups/addgroupuser).

**Richiesta di esempio**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
Request Body
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="limitations"></a>Limitazioni
Di seguito sono riportate le limitazioni di questa funzione:

- Le aree di lavoro di Power BI classiche non sono supportate.

- Azure accounts without Azure Active Directory.

- L'accesso multi-tenant non è supportato. L'entità servizio creata per un determinato processo di Analisi di flusso deve risiedere nello stesso tenant di Azure Active Directory in cui è stato creato il processo e non può essere usata con una risorsa che risiede in un tenant di Azure Active Directory diverso.

- [L'identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/overview.md) non è supportata. Ciò significa che non è possibile immettere la propria entità servizio per essere utilizzato dal processo di Analisi di flusso. L'entità servizio deve essere generata da Analisi di flusso di Azure.The service principal must be generated by Azure Stream Analytics.

## <a name="next-steps"></a>Passaggi successivi

* [Integrazione del dashboard di Power BI con Analisi di flusso di Azure](./stream-analytics-power-bi-dashboard.md)
* [Informazioni sugli output di Analisi di flusso di Azure](./stream-analytics-define-outputs.md)
