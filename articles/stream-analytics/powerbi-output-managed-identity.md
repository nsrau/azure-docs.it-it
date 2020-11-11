---
title: Usare l'identità gestita per autenticare il processo di analisi di flusso di Azure per Power BI output
description: Questo articolo descrive come usare identità gestite per autenticare il processo di analisi di flusso di Azure per Power BI output.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/10/2020
ms.openlocfilehash: 469610d7195835a4b68e4d887c5be57a8926ba37
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489506"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>Usare l'identità gestita per autenticare il processo di analisi di flusso di Azure per Power BI

[Autenticazione dell'identità gestita](../active-directory/managed-identities-azure-resources/overview.md) per l'output in Power bi consente ai processi di analisi di flusso di accedere direttamente a un'area di lavoro all'interno dell'account di Power bi. Questa funzionalità consente di automatizzare completamente le distribuzioni dei processi di analisi di flusso, perché non è più necessario per consentire a un utente di accedere in modo interattivo a Power BI tramite il portale di Azure. Inoltre, i processi a esecuzione prolungata che scrivono in Power BI sono ora più supportati, poiché non sarà necessario riautorizzare periodicamente il processo.

Questo articolo illustra come abilitare l'identità gestita per gli output Power BI di un processo di analisi di flusso tramite la portale di Azure e tramite una distribuzione di Azure Resource Manager.

## <a name="prerequisites"></a>Prerequisiti

Per usare questa funzionalità, è necessario quanto segue:

- Un account Power BI con una [licenza Pro](/power-bi/service-admin-purchasing-power-bi-pro).

- Un'area di lavoro aggiornata all'interno dell'account Power BI. Per ulteriori informazioni, vedere l' [annuncio di Power bi](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/) di questa funzionalità.

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Creare un processo di analisi di flusso usando il portale di Azure

1. Creare un nuovo processo di analisi di flusso o aprire un processo esistente nel portale di Azure. Dalla barra dei menu disponibile sul lato sinistro della schermata selezionare **identità gestita** situata in **Configura**. Verificare che sia selezionata l'opzione "Usa identità gestita assegnata dal sistema" e quindi fare clic sul pulsante **Salva** nella parte inferiore della schermata.

   ![Configurare l'identità gestita di analisi di flusso](./media/common/stream-analytics-enable-managed-identity.png)

2. Prima di configurare l'output, consentire al processo di analisi di flusso di accedere all'area di lavoro di Power BI seguendo le istruzioni riportate nella sezione [fornire l'accesso al processo di analisi di flusso all'area](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) di lavoro Power BI di questo articolo.

3. Passare alla sezione **output** del processo di analisi di flusso, selezionare **+ Aggiungi** e quindi scegliere **Power bi**. Quindi, selezionare il pulsante **autorizza** e accedere con l'account Power bi.

   ![Autorizza con account Power BI](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. Una volta autorizzato, un elenco a discesa verrà popolato con tutte le aree di lavoro a cui si ha accesso. Selezionare l'area di lavoro autorizzata nel passaggio precedente. Quindi selezionare **identità gestita** come "modalità di autenticazione". Infine, selezionare il pulsante **Salva** .

   ![Configurare l'output Power BI con identità gestita](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Distribuzione Azure Resource Manager

Azure Resource Manager consente di automatizzare completamente la distribuzione del processo di analisi di flusso. È possibile distribuire modelli di Gestione risorse usando Azure PowerShell o l' [interfaccia](/cli/azure/)della riga di comando di Azure. Gli esempi seguenti usano l'interfaccia della riga di comando di Azure.


1. È possibile creare una risorsa **Microsoft. StreamAnalytics/streaming** con un'identità gestita includendo la proprietà seguente nella sezione delle risorse del modello di gestione risorse:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   Questa proprietà indica Azure Resource Manager di creare e gestire l'identità per il processo di analisi di flusso. Di seguito è riportato un esempio di Gestione risorse modello che distribuisce un processo di analisi di flusso con identità gestita abilitata e un sink di output Power BI che usa l'identità gestita:

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

    Distribuire il processo sopra riportato al gruppo di risorse **ExampleGroup** usando il comando dell'interfaccia della riga di comando di Azure seguente:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Dopo aver creato il processo, usare Azure Resource Manager per recuperare la definizione completa del processo.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    Il comando precedente restituirà una risposta simile alla seguente:

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

    Se si prevede di usare l'API REST di Power BI per aggiungere il processo di analisi di flusso all'area di lavoro Power BI, prendere nota del messaggio "principalId" restituito.

3. Ora che il processo è stato creato, passare alla sezione [fornire l'accesso del processo di analisi di flusso all'area](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) di lavoro Power BI di questo articolo.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Consentire al processo di analisi di flusso di accedere all'area di lavoro Power BI

Ora che il processo di analisi di flusso è stato creato, può essere concesso l'accesso a un'area di lavoro Power BI. Una volta ottenuto l'accesso al processo, attendere alcuni minuti per la propagazione dell'identità.

### <a name="use-the-power-bi-ui"></a>Usare l'interfaccia utente di Power BI

   > [!Note]
   > Per aggiungere il processo di analisi di flusso all'area di lavoro di Power BI usando l'interfaccia utente, è anche necessario abilitare l'accesso all'entità servizio nelle **impostazioni dello sviluppatore** nel portale di amministrazione di Power bi. Per altri dettagli, vedere [Introduzione a un'entità servizio](/power-bi/developer/embed-service-principal) .

1. Passare alle impostazioni di accesso dell'area di lavoro. Per altri dettagli, vedere questo articolo: [concedere l'accesso all'area di lavoro](/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace).

2. Digitare il nome del processo di analisi di flusso nella casella di testo e selezionare **collaboratore** come livello di accesso.

3. Selezionare **Aggiungi** e chiudere il riquadro.

   ![Aggiungere un processo di analisi di flusso a Power BI area di lavoro](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>Usare i cmdlet di Power BI PowerShell

1. Installare i cmdlet di Power BI `MicrosoftPowerBIMgmt` PowerShell.

   > [!Important]
   > Assicurarsi di usare la versione 1.0.821 o successiva dei cmdlet.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Accedere a Power BI.

```powershell
Login-PowerBI
```

3. Aggiungere il processo di analisi di flusso come collaboratore all'area di lavoro.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>Usare l'API REST di Power BI

Il processo di analisi di flusso può essere aggiunto anche come collaboratore all'area di lavoro usando direttamente l'API REST "Aggiungi utente gruppo". La documentazione completa per questa API si trova qui: [gruppi-Aggiungi utente gruppo](/rest/api/power-bi/groups/addgroupuser).

**Richiesta di esempio**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
Corpo della richiesta
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="remove-managed-identity"></a>Rimuovi identità gestita

L'identità gestita creata per un processo di analisi di flusso viene eliminata solo quando il processo viene eliminato. Non è possibile eliminare l'identità gestita senza eliminare il processo. Se non si vuole più usare l'identità gestita, è possibile modificare il metodo di autenticazione per l'output. L'identità gestita continuerà a esistere fino a quando il processo non verrà eliminato e verrà usato se si decide di usare di nuovo l'autenticazione dell'identità gestita.

## <a name="limitations"></a>Limitazioni
Di seguito sono riportate le limitazioni di questa funzionalità:

- Le aree di lavoro Power BI classiche non sono supportate.

- Account Azure senza Azure Active Directory.

- L'accesso multi-tenant non è supportato. L'entità servizio creata per un determinato processo di analisi di flusso deve trovarsi nello stesso tenant Azure Active Directory in cui è stato creato il processo e non può essere usata con una risorsa che risiede in un tenant di Azure Active Directory diverso.

- L' [identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/overview.md) non è supportata. Ciò significa che non è possibile immettere un'entità servizio personalizzata da usare per il processo di analisi di flusso. L'entità servizio deve essere generata da analisi di flusso di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Integrazione del dashboard di Power BI con Analisi di flusso di Azure](./stream-analytics-power-bi-dashboard.md)
* [Informazioni sugli output di Analisi di flusso di Azure](./stream-analytics-define-outputs.md)