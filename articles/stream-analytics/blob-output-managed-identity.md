---
title: Autenticare l'output BLOB con Managed Identity Azure Stream AnalyticsAuthenticate blob output with Managed Identity Azure Stream Analytics
description: Questo articolo descrive come usare le identità gestite per autenticare il processo di Analisi di flusso di Azure nell'output di archiviazione BLOB di Azure.This article describes how to use managed identities to authenticate your Azure Stream Analytics job to Azure Blob storage output.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 13f48a9e0bc3ed8f8c4d5f1b7da4b6c03f54cdf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129965"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage-output"></a>Usare l'identità gestita per autenticare il processo di Analisi di flusso di Azure nell'output di Archiviazione BLOB di AzureUse Managed Identity to authenticate your Azure Stream Analytics job to Azure Blob Storage output

[L'autenticazione dell'identità gestita](../active-directory/managed-identities-azure-resources/overview.md) per l'output nell'archiviazione BLOB di Azure offre ai processi di Analisi di flusso l'accesso diretto a un account di archiviazione anziché usare una stringa di connessione. In addition to improved security, this feature also enables you to write data to a storage account in a Virtual Network (VNET) within Azure.

Questo articolo illustra come abilitare l'identità gestita per gli output BLOB di un processo di Analisi di flusso tramite il portale di Azure e una distribuzione di Azure Resource Manager.This article shows you how to enable Managed Identity for the Blob output(s) of a Stream Analytics job through the Azure portal and through an Azure Resource Manager deployment.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Creare il processo di Analisi di flusso tramite il portale di AzureCreate the Stream Analytics job using the Azure portal

1. Creare un nuovo processo di Analisi di flusso o aprire un processo esistente nel portale di Azure.Create a new Stream Analytics job or open an existing job in the Azure portal. Dalla barra dei menu situata sul lato sinistro dello schermo, selezionare **Identità gestita** in **Configura**. Assicurarsi che l'opzione "Usa identità gestita assegnata dal sistema" sia selezionata, quindi fare clic sul pulsante **Salva** nella parte inferiore dello schermo.

   ![Configurare l'identità gestita di Analisi di flussoConfigure Stream Analytics managed identity](./media/common/stream-analytics-enable-managed-identity.png)

2. Nella finestra delle proprietà di output del sink di output di Archiviazione BLOB di Azure selezionare l'elenco a discesa Modalità di autenticazione e scegliere **Identità gestita**. Per informazioni sulle altre proprietà di output, vedere Informazioni sugli output di Analisi di flusso di [Azure.](./stream-analytics-define-outputs.md) Al termine, fare clic su **Salva**.

   ![Configurare l'output di archiviazione BLOB di AzureConfigure Azure Blob storage output](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. Ora che il processo è stato creato, vedere la sezione Concedere al processo di [Analisi di flusso l'accesso all'account](#give-the-stream-analytics-job-access-to-your-storage-account) di archiviazione di questo articolo.

## <a name="azure-resource-manager-deployment"></a>Distribuzione Azure Resource Manager

L'uso di Azure Resource Manager consente di automatizzare completamente la distribuzione del processo di Analisi di flusso. È possibile distribuire modelli di Resource Manager usando Azure PowerShell o l'interfaccia della riga di comando di Azure.You can deploy Resource Manager templates using either Azure PowerShell or the [Azure CLI.](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) The below examples use the Azure CLI.


1. È possibile creare una risorsa Microsoft.StreamAnalytics/streamingjobs con un'identità gestita includendo la proprietà seguente nella sezione delle risorse del modello di Resource Manager:You can create a **Microsoft.StreamAnalytics/streamingjobs** resource with a Managed Identity by including the following property in the resource section of your Resource Manager template:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Questa proprietà indica ad Azure Resource Manager di creare e gestire l'identità per il processo di Analisi di flusso. Di seguito è riportato un modello di Resource Manager di esempio che distribuisce un processo di Analisi di flusso con l'identità gestita abilitata e un sink di output BLOB che usa l'identità gestita:Below is an example Resource Manager template that deploys a Stream Analytics job with Managed Identity enabled and a Blob output sink that uses Managed Identity:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
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
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
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

    Il processo precedente può essere distribuito nel gruppo di risorse ExampleGroup usando il comando dell'interfaccia della riga di comando di Azure seguente:The above job can be deployed to the Resource group **ExampleGroup** using the below Azure CLI command:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Dopo aver creato il processo, è possibile usare Azure Resource Manager per recuperare la definizione completa del processo.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    Il comando di cui sopra restituirà una risposta simile alla seguente:

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
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
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   Prendere nota di **principalId** dalla definizione del processo, che identifica l'identità gestita del processo all'interno di Azure Active Directory e verrà usato nel passaggio successivo per concedere al processo di Analisi di flusso l'accesso all'account di archiviazione.

3. Ora che il processo è stato creato, vedere la sezione Concedere al processo di [Analisi di flusso l'accesso all'account](#give-the-stream-analytics-job-access-to-your-storage-account) di archiviazione di questo articolo.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>Concedere al processo di Analisi di flusso l'accesso all'account di archiviazione

Esistono due livelli di accesso che puoi scegliere per fornire il tuo lavoro di Analisi di flusso:

1. **Accesso a livello di contenitore:** questa opzione consente al processo di accedere a un contenitore esistente specifico.
2. **Accesso a livello** di account: questa opzione consente al processo di accedere in generale all'account di archiviazione, inclusa la possibilità di creare nuovi contenitori.

A meno che il processo non sia necessario per creare contenitori per conto dell'utente, è consigliabile scegliere Accesso a livello di **contenitore** poiché questa opzione concederà al processo il livello minimo di accesso richiesto. Entrambe le opzioni sono illustrate di seguito per il portale di Azure e la riga di comando.

### <a name="grant-access-via-the-azure-portal"></a>Concedere l'accesso tramite il portale di AzureGrant access via the Azure portal

#### <a name="container-level-access"></a>Accesso a livello di contenitore

1. Passare al riquadro di configurazione del contenitore all'interno dell'account di archiviazione.

2. Selezionare **Controllo di accesso (IAM)** sul lato sinistro.

3. Nella sezione "Aggiungi assegnazione ruolo" fare clic su **Aggiungi**.

4. Nel riquadro di assegnazione dei ruoli:

    1. Impostare il **ruolo su** "Collaboratore dati BLOB di archiviazione"Set the Role to "Storage Blob Data Contributor"
    2. Verificare che l'elenco a discesa **Assegna accesso a** sia impostato su "Utente, gruppo o entità servizio di Azure AD".
    3. Digita il nome del tuo lavoro di Analisi di flusso nel campo di ricerca.
    4. Selezionare il processo di Analisi di flusso e fare clic su **Salva**.

   ![Concedere l'accesso al contenitoreGrant container access](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>Accesso a livello di account

1. Passare all'account di archiviazione.

2. Selezionare **Controllo di accesso (IAM)** sul lato sinistro.

3. Nella sezione "Aggiungi assegnazione ruolo" fare clic su **Aggiungi**.

4. Nel riquadro di assegnazione dei ruoli:

    1. Impostare il **ruolo su** "Collaboratore dati BLOB di archiviazione"Set the Role to "Storage Blob Data Contributor"
    2. Verificare che l'elenco a discesa **Assegna accesso a** sia impostato su "Utente, gruppo o entità servizio di Azure AD".
    3. Digita il nome del tuo lavoro di Analisi di flusso nel campo di ricerca.
    4. Selezionare il processo di Analisi di flusso e fare clic su **Salva**.

   ![Concedere l'accesso all'account](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>Concedere l'accesso tramite la riga di comandoGrant access via the command line

#### <a name="container-level-access"></a>Accesso a livello di contenitore

To give access to a specific container, run the following command using the Azure CLI:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>Accesso a livello di account

To give access to the entire account, run the following command using the Azure CLI:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>Abilitare l'accesso VNET

Quando si configurano **i firewall e le reti virtuali**dell'account di archiviazione, è possibile consentire facoltativamente il traffico di rete da altri servizi Microsoft attendibili. Quando Analisi di flusso esegue l'autenticazione tramite Managed Identity, fornisce la prova che la richiesta proviene da un servizio attendibile. Di seguito sono riportate le istruzioni per abilitare questa eccezione di accesso VNET.

1.  Passare al riquadro "Firewall e reti virtuali" all'interno del riquadro di configurazione dell'account di archiviazione.
2.  Verificare che l'opzione "Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione" sia abilitata.
3.  Se è stata abilitata, fare clic su **Salva**.

   ![Abilitare l'accesso VNET](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="limitations"></a>Limitazioni
Di seguito sono riportate le attuali limitazioni di questa funzione:

1. Account di archiviazione di Azure classici.

2. Azure accounts without Azure Active Directory.

3. L'accesso multi-tenant non è supportato. L'entità servizio creata per un determinato processo di Analisi di flusso deve risiedere nello stesso tenant di Azure Active Directory in cui è stato creato il processo e non può essere usata con una risorsa che risiede in un tenant di Azure Active Directory diverso.

4. [L'identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/overview.md) non è supportata. Ciò significa che l'utente non è in grado di immettere la propria entità servizio per essere utilizzato dal processo di Analisi di flusso. L'entità servizio deve essere generata da Analisi di flusso di Azure.The service principal must be generated by Azure Stream Analytics.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sugli output di Analisi di flusso di Azure](./stream-analytics-define-outputs.md)
* [Partizionamento dell'output dei BLOB personalizzato in Analisi di flusso di Azure](./stream-analytics-custom-path-patterns-blob-storage-output.md)
