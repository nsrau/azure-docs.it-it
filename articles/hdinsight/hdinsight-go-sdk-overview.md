---
title: Azure HDInsight Go SDK
description: Informazioni di riferimento per Azure HDInsight Go SDK
author: tylerfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: tyfox
ms.custom: seodec18
ms.openlocfilehash: 2e5b7816fda89e25dcb0de26f526e5187e0640b9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098559"
---
# <a name="hdinsight-go-management-sdk-preview"></a>Anteprima di HDInsight Go Management SDK

## <a name="overview"></a>Panoramica
HDInsight Go SDK offre classi e funzioni che consentono di gestire i cluster HDInsight. Include operazioni per creare, eliminare, aggiornare, elencare, ridimensionare, eseguire azioni di script, monitorare, ottenere le proprietà dei cluster di HDInsight e altro ancora.

> [!NOTE]  
>Il materiale di riferimenti GoDoc per questo SDK è [disponibile anche qui](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight).

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure. Se non è disponibile, [ottenere una versione di valutazione gratuita](https://azure.microsoft.com/free/).
* [Vai](https://golang.org/dl/).

## <a name="sdk-installation"></a>Installazione dell'SDK

Dal percorso dell'ambiente GOPATH eseguire `go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Authentication

L'SDK deve essere prima autenticato con la sottoscrizione di Azure.  Seguire questo esempio per creare un'entità servizio e usarla per l'autenticazione. Al termine si avrà un'istanza di un `ClustersClient` che contiene molte funzioni, descritte nelle sezioni seguenti, che possono essere usate per operazioni di gestione.

> [!NOTE]  
> Oltre all'esempio seguente esistono altre modalità di autenticazione che possono essere più adatte alle proprie esigenze. Tutte le funzioni sono descritte di seguito: [Funzioni di autenticazione in Azure SDK per Go](https://docs.microsoft.com/go/azure/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Esempio di autenticazione con un'entità servizio

Per prima cosa, accedere ad [Azure Cloud Shell](https://shell.azure.com/bash). Verificare che si stia attualmente usando la sottoscrizione in cui si vuole creare l'entità servizio. 

```azurecli-interactive
az account show
```

Le informazioni sulla sottoscrizione vengono visualizzate in formato JSON.

```json
{
  "environmentName": "AzureCloud",
  "id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "isDefault": true,
  "name": "XXXXXXX",
  "state": "Enabled",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "user": {
    "cloudShellID": true,
    "name": "XXX@XXX.XXX",
    "type": "user"
  }
}
```

Se non si è eseguito l'accesso alla sottoscrizione corretta, selezionare quella corretta eseguendo: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> Se non è già registrata del Provider di risorse di HDInsight da un'altra funzione (ad esempio creando un HDInsight Cluster tramite il portale di Azure), è necessario eseguire questa operazione una volta in precedenza è possibile eseguire l'autenticazione. La registrazione può essere eseguita da [Azure Cloud Shell](https://shell.azure.com/bash) eseguendo questo comando:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Scegliere quindi un nome per l'entità servizio e crearla con il comando seguente:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

Verranno visualizzate le informazioni relative all'entità servizio in formato JSON.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Copiare il frammento di codice seguente e compilare `TENANT_ID`, `CLIENT_ID`, `CLIENT_SECRET` e `SUBSCRIPTION_ID` con le stringhe JSON restituite dopo aver eseguito il comando per creare l'entità servizio.

```golang
package main

import (
    "context"
    "github.com/Azure/go-autorest/autorest/azure/auth"
    hdi "github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight"
    "github.com/Azure/go-autorest/autorest/to"
)

func main() {
    var err error

    // Tenant ID for your Azure Subscription
    var TENANT_ID = ""
    // Your Service Principal App Client ID
    var CLIENT_ID = ""
    // Your Service Principal Client Secret
    var CLIENT_SECRET = ""
    // Azure Subscription ID
    var SUBSCRIPTION_ID = ""

    var credentials = auth.NewClientCredentialsConfig(CLIENT_ID, CLIENT_SECRET, TENANT_ID)
    var client = hdi.NewClustersClient(SUBSCRIPTION_ID)

    client.Authorizer, err = credentials.Authorizer()
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
```

## <a name="cluster-management"></a>Gestione dei cluster

> [!NOTE]  
> Questa sezione presuppone che l'utente abbia già eseguito l'autenticazione e abbia creato un'istanza `ClusterClient` che ha poi archiviato in una variabile chiamata `client`. Le istruzioni per l'autenticazione e l'ottenimento di un `ClusterClient` sono disponibili nella sezione Autenticazione precedente.

### <a name="create-a-cluster"></a>Creare un cluster

Un nuovo cluster può essere creato chiamando `client.Create()`. 

#### <a name="example"></a>Esempio

Questo esempio illustra come creare un cluster [Apache Spark](https://spark.apache.org/) con 2 nodi head e 1 nodo del ruolo di lavoro.

> [!NOTE]  
> È prima necessario creare un gruppo di risorse e un account di archiviazione, come spiegato di seguito. Se sono già stati creati, è possibile ignorare questi passaggi.

##### <a name="creating-a-resource-group"></a>Creazione di un gruppo di risorse

È possibile creare un gruppo di risorse con [Azure Cloud Shell](https://shell.azure.com/bash) eseguendo
```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```
##### <a name="creating-a-storage-account"></a>Creazione di un account di archiviazione

È possibile creare un account di archiviazione con [Azure Cloud Shell](https://shell.azure.com/bash) eseguendo:
```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```
Eseguire ora questo comando per ottenere la chiave per l'account di archiviazione. Questa chiave sarà necessaria per creare un cluster:
```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```
---
Il frammento di codice Go seguente crea un cluster Spark con 2 nodi head e 1 nodo del ruolo di lavoro. Inserire le variabili vuote come spiegato nei commenti. È possibile modificare altri parametri in base alle proprie esigenze.

```golang
// The name for the cluster you are creating
var clusterName = "";
// The name of your existing Resource Group
var resourceGroupName = "";
// Choose a username
var username = "";
// Choose a password
var password = "";
// Replace <> with the name of your storage account
var storageAccount = "<>.blob.core.windows.net";
// Storage account key you obtained above
var storageAccountKey = "";
// Choose a region
var location = "";
var container = "default";

var parameters = hdi.ClusterCreateParametersExtended {
    Location: to.StringPtr(location),
    Tags: make(map[string]*string),
    Properties: &hdi.ClusterCreateProperties {
        ClusterVersion: to.StringPtr("3.6"),
        OsType: hdi.Linux,
        ClusterDefinition: &hdi.ClusterDefinition {
            Kind: to.StringPtr("spark"),
            Configurations: map[string]map[string]interface{}{
                "gateway": {
                    "restAuthCredential.isEnabled": "True",
                    "restAuthCredential.username":  username,
                    "restAuthCredential.password":  password,
                },
            },
        },
        Tier: hdi.Standard,
        ComputeProfile: &hdi.ComputeProfile {
            Roles: &[]hdi.Role {
                hdi.Role {
                    Name: to.StringPtr("headnode"),
                    TargetInstanceCount: to.Int32Ptr(2),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
                hdi.Role {
                    Name: to.StringPtr("workernode"),
                    TargetInstanceCount: to.Int32Ptr(1),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
            },
        },
        StorageProfile: &hdi.StorageProfile {
            Storageaccounts: &[]hdi.StorageAccount {
                hdi.StorageAccount {
                    Name: to.StringPtr(storageAccount),
                    Key: to.StringPtr(storageAccountKey),
                    Container: to.StringPtr(container),
                    IsDefault: to.BoolPtr(true),
                },
            },
        },
    },
}
client.Create(context.Background(), resourceGroupName, clusterName, parameters)
```

### <a name="get-cluster-details"></a>Ottenere i dettagli del cluster

Per ottenere le proprietà di un dato cluster:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Esempio

È possibile usare `get` per verificare che il cluster sia stato creato correttamente.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

L'output sarà simile al seguente:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Elencare cluster

#### <a name="list-clusters-under-the-subscription"></a>Elencare i cluster nella sottoscrizione
```golang
client.List()
```
#### <a name="list-clusters-by-resource-group"></a>Elencare i cluster per gruppo di risorse
```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> Sia `List()` che `ListByResourceGroup()` restituiscono una struct `ClusterListResultPage`. Per ottenere la pagina successiva è possibile chiamare `Next()`. Questa operazione può essere ripetuta fino a quando `ClusterListResultPage.NotDone()` non restituisce `false`, come illustrato nell'esempio seguente.

#### <a name="example"></a>Esempio
L'esempio seguente mostra le proprietà di tutti i cluster per la sottoscrizione corrente:

```golang
page, err := client.List(context.Background())
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, cluster := range page.Values() {
        fmt.Println(*cluster.Name)
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="delete-a-cluster"></a>Eliminazione di un cluster

Per eliminare un cluster:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>Aggiornare i tag del cluster

È possibile aggiornare i tag di un dato cluster nel modo seguente:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```
#### <a name="example"></a>Esempio

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Ridimensionare un cluster

È possibile ridimensionare il numero di nodi di ruolo di lavoro di un dato cluster specificando una nuova dimensione nel modo seguente:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Monitoraggio del cluster

HDInsight Management SDK può essere usato anche per gestire il monitoraggio dei cluster tramite Operations Management Suite (OMS).

Così come è stato creato un `ClusterClient` per le operazioni di gestione, è necessario ora creare un `ExtensionClient` da usare per le operazioni di monitoraggio. Dopo aver completato la sezione Autenticazione precedente, è possibile creare un `ExtensionClient` come descritto di seguito:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Gli esempi di monitoraggio seguenti presuppongono che sia già stato inizializzato un `ExtensionClient` denominato `extClient` e impostato il relativo `Authorizer`, come illustrato in precedenza.

### <a name="enable-oms-monitoring"></a>Abilitare il monitoraggio di OMS

> [!NOTE]  
> Per abilitare il monitoraggio di OMS è necessaria un'area di lavoro Log Analytics esistente. Se non è già stata creata una, è possibile ottenere informazioni su come farlo qui: [Creare un'area di lavoro di Log Analytics nel portale di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

Per abilitare il monitoraggio di OMS nel cluster:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Visualizzare lo stato del monitoraggio di OMS

Per ottenere lo stato di OMS nel cluster:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>Disabilitare il monitoraggio di OMS

Per disabilitare OMS nel cluster:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Azioni script

HDInsight offre una funzione di configurazione denominato "azioni script" che richiama script personalizzati per il cluster.

> [!NOTE]  
> Altre informazioni su come usare le azioni dello script sono disponibili qui: [Personalizzare i cluster HDInsight basati su Linux tramite azioni script](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)

### <a name="execute-script-actions"></a>Eseguire azioni script

È possibile eseguire azioni script in un dato cluster nel modo seguente:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

Per le operazioni "Eliminare un'azione script" ed "Elencare le azioni script persistenti" è necessario creare un `ScriptActionsClient`, analogamente al modo in cui è stato creato un `ClusterClient` per le operazioni di gestione. Dopo aver completato la sezione Autenticazione precedente, è possibile creare un `ScriptActionsClient` come descritto di seguito:

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Gli esempi di azioni script seguenti presuppongono che sia già stato inizializzato un `ScriptActionsClient` denominato `scriptActionsClient` e impostato il relativo `Authorizer`, come illustrato in precedenza.

### <a name="delete-script-action"></a>Eliminare un'azione script

Per eliminare una determinata azione script persistente in un dato cluster:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Elencare le azioni script persistenti

> [!NOTE]  
> `ListByCluster()` restituisce una struct `ScriptActionsListPage`. Per ottenere la pagina successiva è possibile chiamare `Next()`. Questa operazione può essere ripetuta fino a quando `ClusterListResultPage.NotDone()` non restituisce `false`, come illustrato nell'esempio seguente.

Per elencare tutte le azioni script persistenti per il cluster specificato:
```golang
scriptActionsClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Esempio

```golang
page, err := scriptActionsClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="list-all-scripts-execution-history"></a>Elencare la cronologia di esecuzione di tutti gli script

Per eseguire questa operazione è necessario creare un `ScriptExecutionHistoryClient`, analogamente al modo in cui è stato creato un `ClusterClient` per le operazioni di monitoraggio. Dopo aver completato la sezione Autenticazione precedente, è possibile creare un `ScriptActionsClient` come descritto di seguito:

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Gli esempi seguenti presuppongono che sia già stato inizializzato un `ScriptExecutionHistoryClient` denominato `scriptExecutionHistoryClient` e impostato il relativo `Authorizer`, come illustrato in precedenza.

Per elencare la cronologia di esecuzione di tutti gli script per il cluster specificato:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Esempio

Questo esempio visualizza tutti i dettagli di tutte le precedenti esecuzioni di script.

```golang
page, err := scriptExecutionHistoryClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

* Esplorare il [materiale di riferimento GoDoc](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight). I GoDoc forniscono documentazione di riferimento per tutte le funzioni dell'SDK.
