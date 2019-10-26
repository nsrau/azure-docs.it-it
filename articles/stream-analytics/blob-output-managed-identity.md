---
title: Autenticare l'output di BLOB con l'identità gestita analisi di flusso di Azure
description: Questo articolo descrive come usare le identità gestite per autenticare il processo di analisi di flusso di Azure nell'output di archiviazione BLOB di Azure.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 4215dc85da6d507b08f23cf248044ea807fe5319
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935166"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage-output-preview"></a>Usare l'identità gestita per autenticare il processo di analisi di flusso di Azure nell'output di archiviazione BLOB di Azure (anteprima)

[L'autenticazione dell'identità gestita per l'](../active-directory/managed-identities-azure-resources/overview.md) output nell'archiviazione BLOB di Azure è disponibile per l'analisi di flusso di Azure come anteprima. Questo consente ai processi di analisi di flusso di accedere direttamente a un account di archiviazione anziché usare una stringa di connessione. Oltre a una maggiore sicurezza, questa funzionalità consente anche di scrivere i dati in un account di archiviazione in una rete virtuale (VNET) in Azure.

Questo articolo illustra come abilitare l'identità gestita per gli output del BLOB di un processo di analisi di flusso tramite il portale di Azure e tramite una distribuzione di Azure Resource Manager.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Creare il processo di analisi di flusso usando il portale di Azure

1. Creare un nuovo processo di analisi di flusso o aprire un processo esistente nel portale di Azure. Dalla barra dei menu disponibile sul lato sinistro della schermata selezionare **identità gestita** situata in **Configura**. Verificare che sia selezionata l'opzione "Usa identità gestita assegnata dal sistema" e quindi fare clic sul pulsante **Salva** nella parte inferiore della schermata.

   ![Configurare l'identità gestita di analisi di flusso](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-enable-managed-identity.png)

2. Nella finestra Proprietà output del sink di output di archiviazione BLOB di Azure selezionare l'elenco a discesa modalità di autenticazione e scegliere **identità gestita**. Per informazioni sulle altre proprietà di output, vedere [comprendere gli output di analisi di flusso di Azure](./stream-analytics-define-outputs.md). Al termine, fare clic su **Salva**.

   ![Configurare l'output dell'archiviazione BLOB di Azure](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. Ora che il processo è stato creato, vedere la sezione [concedere l'accesso al processo di analisi di flusso all'account di archiviazione](#give-the-stream-analytics-job-access-to-your-storage-account) di questo articolo.

## <a name="azure-resource-manager-deployment"></a>Distribuzione Azure Resource Manager

L'uso di Azure Resource Manager consente di automatizzare completamente la distribuzione del processo di analisi di flusso. È possibile distribuire modelli di Gestione risorse usando Azure PowerShell o l' [interfaccia](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)della riga di comando di Azure. Gli esempi seguenti usano l'interfaccia della riga di comando di Azure.


1. È possibile creare una risorsa **Microsoft. StreamAnalytics/streaming** con un'identità gestita includendo la proprietà seguente nella sezione delle risorse del modello di gestione risorse:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Questa proprietà indica Azure Resource Manager di creare e gestire l'identità per il processo di analisi di flusso. Di seguito è riportato un esempio di Gestione risorse modello che distribuisce un processo di analisi di flusso con identità gestita abilitata e un sink di output BLOB che usa l'identità gestita:

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

    Il processo precedente può essere distribuito nel gruppo di risorse **ExampleGroup** usando il comando dell'interfaccia della riga di comando di Azure seguente:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Dopo aver creato il processo, è possibile usare Azure Resource Manager per recuperare la definizione completa del processo.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    Il comando precedente restituirà una risposta simile alla seguente:

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

   Prendere nota del **PrincipalId** dalla definizione del processo, che identifica l'identità gestita del processo all'interno Azure Active Directory e verrà usato nel passaggio successivo per concedere al processo di analisi di flusso l'accesso all'account di archiviazione.

3. Ora che il processo è stato creato, vedere la sezione [concedere l'accesso al processo di analisi di flusso all'account di archiviazione](#give-the-stream-analytics-job-access-to-your-storage-account) di questo articolo.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>Consentire al processo di analisi di flusso di accedere all'account di archiviazione

Ci sono due livelli di accesso che è possibile scegliere di assegnare al processo di analisi di flusso:

1. **Accesso a livello di contenitore:** questa opzione consente al processo di accedere a un contenitore esistente specifico.
2. **Accesso a livello di account:** questa opzione fornisce al processo l'accesso generale all'account di archiviazione, inclusa la possibilità di creare nuovi contenitori.

A meno che non sia necessario il processo per creare contenitori per conto dell'utente, è consigliabile scegliere l' **accesso a livello di contenitore** poiché questa opzione concede al processo il livello minimo di accesso necessario. Entrambe le opzioni sono illustrate di seguito per la portale di Azure e la riga di comando.

### <a name="grant-access-via-the-azure-portal"></a>Concedi l'accesso tramite il portale di Azure

#### <a name="container-level-access"></a>Accesso a livello di contenitore

1. Passare al riquadro di configurazione del contenitore all'interno dell'account di archiviazione.

2. Selezionare **controllo di accesso (IAM)** sul lato sinistro.

3. Nella sezione "aggiungere un'assegnazione di ruolo" fare clic su **Aggiungi**.

4. Nel riquadro assegnazione ruolo:

    1. Impostare il **ruolo** su "collaboratore dati BLOB di archiviazione"
    2. Verificare che l'elenco a discesa **assegna accesso a** sia impostato su "Azure ad utente, gruppo o entità servizio".
    3. Digitare il nome del processo di analisi di flusso nel campo di ricerca.
    4. Selezionare il processo di analisi di flusso e fare clic su **Salva**.

   ![Concessione dell'accesso al contenitore](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>Accesso a livello di account

1. Passare all'account di archiviazione.

2. Selezionare **controllo di accesso (IAM)** sul lato sinistro.

3. Nella sezione "aggiungere un'assegnazione di ruolo" fare clic su **Aggiungi**.

4. Nel riquadro assegnazione ruolo:

    1. Impostare il **ruolo** su "collaboratore dati BLOB di archiviazione"
    2. Verificare che l'elenco a discesa **assegna accesso a** sia impostato su "Azure ad utente, gruppo o entità servizio".
    3. Digitare il nome del processo di analisi di flusso nel campo di ricerca.
    4. Selezionare il processo di analisi di flusso e fare clic su **Salva**.

   ![Concessione dell'accesso all'account](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>Concessione dell'accesso tramite la riga di comando

#### <a name="container-level-access"></a>Accesso a livello di contenitore

Per concedere l'accesso a un contenitore specifico, eseguire il comando seguente usando l'interfaccia della riga di comando di Azure:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>Accesso a livello di account

Per concedere l'accesso all'intero account, eseguire il comando seguente usando l'interfaccia della riga di comando di Azure:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>Abilitare l'accesso VNET

Quando si configurano i **firewall e le reti virtuali**dell'account di archiviazione, è possibile facoltativamente consentire il traffico di rete da altri servizi Microsoft attendibili. Quando analisi di flusso esegue l'autenticazione con l'identità gestita, fornisce la prova che la richiesta proviene da un servizio attendibile. Di seguito sono riportate le istruzioni per abilitare questa eccezione di accesso VNET.

1.  Passare al riquadro "firewall e reti virtuali" nel riquadro di configurazione dell'account di archiviazione.
2.  Verificare che l'opzione "Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione" sia abilitata.
3.  Se è stato abilitato, fare clic su **Salva**.

   ![Abilitare l'accesso VNET](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="limitations"></a>Limitazioni
Di seguito sono riportate le limitazioni correnti di questa funzionalità:

1. Account di archiviazione di Azure classici.

2. Account Azure senza Azure Active Directory.

3. L'accesso multi-tenant non è supportato. L'entità servizio creata per un determinato processo di analisi di flusso deve trovarsi nello stesso tenant Azure Active Directory in cui è stato creato il processo e non può essere usata con una risorsa che risiede in un tenant di Azure Active Directory diverso.

4. L' [identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/overview.md) non è supportata. Ciò significa che l'utente non è in grado di immettere la propria entità servizio per l'uso da parte del processo di analisi di flusso. L'entità servizio deve essere generata da analisi di flusso di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sugli output di Analisi di flusso di Azure](./stream-analytics-define-outputs.md)
* [Partizionamento dell'output del BLOB personalizzato di analisi di flusso di Azure](./stream-analytics-custom-path-patterns-blob-storage-output.md)
