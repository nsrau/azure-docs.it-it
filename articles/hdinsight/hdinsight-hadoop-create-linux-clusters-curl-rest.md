---
title: Creare cluster Azure HDInsight (Hadoop) usando cURL e REST | Documentazione Microsoft
description: "Informazioni su come creare cluster HDInsight tramite cURL, i modelli di Azure Resource Manager e l&quot;API REST di Azure. È possibile specificare il tipo di cluster (Hadoop, HBase o Storm) o utilizzare gli script per installare i componenti personalizzati."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 98be5893-2c6f-4dfa-95ec-d4d8b5b7dcb5
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 110f3aa9ce4848c9350ea2e560205aa762decf7a
ms.openlocfilehash: c8ed0760650f7fb3c85eef4ec7f72c60bb0efd00
ms.lasthandoff: 02/21/2017


---
# <a name="create-hdinsight-clusters-using-curl-and-the-azure-rest-api"></a>Creare cluster HDInsight tramite cURL e l'API REST di Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Informazioni su come creare un cluster HDInsight tramite un modello di Azure Resource Manager e l'API REST di Azure.

L'API REST di Azure consente di eseguire operazioni di gestione su servizi ospitati nella piattaforma Azure, inclusa la creazione di nuove risorse, ad esempio cluster HDInsight.

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Interfaccia della riga di comando di Azure 2.0** (anteprima). L'interfaccia della riga di comando di Azure viene utilizzata per creare un'entità servizio, quindi viene utilizzata per generare i token di autenticazione per le richieste all'API REST di Azure. Per altre informazioni sull'anteprima dell'interfaccia della riga di comando di Azure 2.0, vedere [Introduzione all'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

* **cURL**. Questa utilità è disponibile tramite il sistema di gestione del pacchetto o può essere scaricata da [http://curl.haxx.se/](http://curl.haxx.se/).

  > [!NOTE]
  > Se si utilizza PowerShell per eseguire i comandi in questo documento, è necessario innanzitutto rimuovere l'alias `curl` creato per impostazione predefinita. L'alias usa Invoke-WebRequest invece di cURL. Se non si rimuove questo alias, è possibile ricevere errori per alcuni dei comandi usati in questo documento.
  >
  > Per rimuovere l'alias, usare il comando seguente dal prompt di PowerShell:
  >
  > `Remove-item alias:curl`
  >
  > Una volta rimosso l'alias, sarà possibile utilizzare la versione di cURL installata nel sistema.

### <a name="access-control-requirements"></a>Requisiti di controllo di accesso

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-template"></a>Creare un modello

I modelli di Azure Resource Manager sono documenti JSON che descrivono un **gruppo di risorse** e tutte le risorse in esso contenute, ad esempio HDInsight. Questo approccio basato su modelli consente di definire le risorse necessarie per HDInsight in un singolo modello.

Di seguito è riportata una fusione dei file di modello e di parametri ricavati da [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password), che consente di creare un cluster basato su Linux usando una password per proteggere l'account utente SSH.

   ```json
   {
       "properties": {
           "template": {
               "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
               "contentVersion": "1.0.0.0",
               "parameters": {
                   "location": {
                       "type": "string",
                       "allowedValues": ["Central US",
                       "East Asia",
                       "East US",
                       "Japan East",
                       "Japan West",
                       "North Europe",
                       "South Central US",
                       "Southeast Asia",
                       "West Europe",
                       "West US"],
                       "metadata": {
                           "description": "The location where all azure resources are deployed."
                       }
                   },
                   "clusterType": {
                       "type": "string",
                       "allowedValues": ["hadoop",
                       "hbase",
                       "storm",
                       "spark"],
                       "metadata": {
                           "description": "The type of the HDInsight cluster to create."
                       }
                   },
                   "clusterName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the HDInsight cluster to create."
                       }
                   },
                   "clusterLoginUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                       }
                   },
                   "clusterLoginPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "sshUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to remotely access the cluster."
                       }
                   },
                   "sshPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "clusterStorageAccountName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the storage account to be created and be used as the cluster's storage."
                       }
                   },
                   "clusterWorkerNodeCount": {
                       "type": "int",
                       "defaultValue": 4,
                       "metadata": {
                           "description": "The number of nodes in the HDInsight cluster."
                       }
                   }
               },
               "variables": {
                   "defaultApiVersion": "2015-05-01-preview",
                   "clusterApiVersion": "2015-03-01-preview"
               },
               "resources": [{
                   "name": "[parameters('clusterStorageAccountName')]",
                   "type": "Microsoft.Storage/storageAccounts",
                   "location": "[parameters('location')]",
                   "apiVersion": "[variables('defaultApiVersion')]",
                   "dependsOn": [],
                   "tags": {

                   },
                   "properties": {
                       "accountType": "Standard_LRS"
                   }
               },
               {
                   "name": "[parameters('clusterName')]",
                   "type": "Microsoft.HDInsight/clusters",
                   "location": "[parameters('location')]",
                   "apiVersion": "[variables('clusterApiVersion')]",
                   "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                   "tags": {

                   },
                   "properties": {
                       "clusterVersion": "3.5",
                       "osType": "Linux",
                       "clusterDefinition": {
                           "kind": "[parameters('clusterType')]",
                           "configurations": {
                               "gateway": {
                                   "restAuthCredential.isEnabled": true,
                                   "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                   "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                               }
                           }
                       },
                       "storageProfile": {
                           "storageaccounts": [{
                               "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                               "isDefault": true,
                               "container": "[parameters('clusterName')]",
                               "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                           }]
                       },
                       "computeProfile": {
                           "roles": [{
                               "name": "headnode",
                               "targetInstanceCount": "2",
                               "hardwareProfile": {
                                   "vmSize": "Standard_D3"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           },
                           {
                               "name": "workernode",
                               "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                               "hardwareProfile": {
                                   "vmSize": "Standard_D3"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           }]
                       }
                   }
               }],
               "outputs": {
                   "cluster": {
                       "type": "object",
                       "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                   }
               }
           },
           "mode": "incremental",
           "Parameters": {
               "location": {
                   "value": "North Europe"
               },
               "clusterName": {
                   "value": "newclustername"
               },
               "clusterType": {
                   "value": "hadoop"
               },
               "clusterStorageAccountName": {
                   "value": "newstoragename"
               },
               "clusterLoginUserName": {
                   "value": "admin"
               },
               "clusterLoginPassword": {
                   "value": "changeme"
               },
               "sshUserName": {
                   "value": "sshuser"
               },
               "sshPassword": {
                   "value": "changeme"
               }
           }
       }
   }
   ```

Questo esempio viene usato nei passaggi di questo documento. Sostituire i *valori* di esempio nella sezione **Parametri** con i valori relativi al cluster in uso.

> [!IMPORTANT]
> Il modello usa il numero di nodi di lavoro predefinito (4) per un cluster HDInsight. Se si prevedono più di 32 nodi di lavoro, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.
>
> Per altre informazioni sulle dimensioni di nodo e i costi associati, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="log-in-to-your-azure-subscription"></a>Accedere alla sottoscrizione di Azure

Seguire i passaggi illustrati [Introduzione all'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) e connettersi alla sottoscrizione tramite il comando `az login`.

## <a name="create-a-service-principal"></a>Creare un’entità servizio

> [!NOTE]
> Questa procedura costituisce una sintesi della sezione *Creare un'entità servizio con password* dell'articolo [Usare l'interfaccia della riga di comando di Azure per creare un'entità servizio per accedere alle risorse](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md#create-service-principal-with-password). Con questi passaggi viene creata un'entità servizio usata per autenticare l'API REST di Azure.

1. Dalla riga di comando, usare il comando seguente per elencare le sottoscrizioni di Azure.

   ```bash
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    Nell'elenco selezionare la sottoscrizione che si vuole usare e annotare i valori nelle colonne **Subscription_ID** e __Tenant_ID__. Salvare questi valori.

2. Eseguire i comandi seguenti per creare un'applicazione in Azure Active Directory.

   ```bash
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Sostituire i valori di `--display-name`, `--homepage` e `--identifier-uris` con valori personalizzati. Specificare una password per la nuova voce di Active Directory.

   > [!NOTE]
   > I valori `--home-page` e `--identifier-uris` non devono fare riferimento a una pagina Web reale ospitata in Internet. Devono essere URI univoci.

   Il valore restituito da questo comando è l'__ID app__ per la nuova applicazione. Salvare il valore.

3. Eseguire il comando seguente per creare un'entità servizio mediante l'**ID app**.

   ```bash
   az ad sp create --id <App ID> --query 'objectId'
   ```

     Il valore restituito da questo comando è l'__ID oggetto__. Salvare il valore.

4. Assegnare il ruolo **Owner** all'entità servizio usando il valore **ID oggetto** precedentemente restituito. Usare anche l'**ID sottoscrizione** ottenuto in precedenza.

   ```bash
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Ottenere un token di autenticazione

Usare il comando seguente per recuperare un token di autenticazione:

    curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
    -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
    -H "Content-Type: application/x-www-form-urlencoded" \
    --data-urlencode "client_id=AppID" \
    --data-urlencode "grant_type=client_credentials" \
    --data-urlencode "client_secret=password" \
    --data-urlencode "resource=https://management.azure.com/"

    Replace **TenantID**, **AppID**, and **password** with the values obtained or used previously.

    If this request is successful, you receive a 200 series response and the response body contains a JSON document.

    The JSON document returned by this request contains an element named **access_token**. The value of **access_token** is used to authentication requests to the REST API.

   ```json
   {
       "token_type":"Bearer",
       "expires_in":"3599",
       "expires_on":"1463409994",
       "not_before":"1463406094",
       "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
   }
   ```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per creare un gruppo di risorse, seguire questa procedura.

* Sostituire **SubscriptionID** con l'ID sottoscrizione ricevuto durante la creazione dell'entità servizio.
* Sostituire **AccessToken** con il token di accesso ricevuto nel passaggio precedente.
* Sostituire **DataCenterLocation** con il data center in cui si vuole creare il gruppo di risorse e le risorse. Ad esempio "Stati Uniti centrali del sud".
* Sostituire **ResourceGroupName** con il nome che si vuole usare per questo gruppo:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

Se la richiesta ha esito positivo, si riceve una risposta serie 200 e il corpo della risposta contiene un documento JSON che include le informazioni del gruppo. L'elemento `"provisioningState"` contiene un valore di `"Succeeded"`.

## <a name="create-a-deployment"></a>Creare una distribuzione

Usare il comando seguente per distribuire il modello nel gruppo di risorse.

* Sostituire **SubscriptionID** e **AccessToken** con i valori usati in precedenza.
* Sostituire **ResourceGroupName** con il nome del gruppo di risorse creato nella sezione precedente.
* Sostituire **DeploymentName** con il nome che si vuole usare per questa distribuzione.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]
> Se il modello è stato salvato in un file, è possibile usare il comando seguente invece di `-d "{ template and parameters}"`:
>
> `--data-binary "@/path/to/file.json"`

Se la richiesta ha esito positivo, si riceve una risposta serie 200 e il corpo della risposta contiene un documento JSON che include le informazioni dell'operazione di distribuzione.

> [!IMPORTANT]
> La distribuzione è stata inviata, ma non è stata completata in questo momento. Possono essere necessari diversi minuti, in genere circa 15, per completare la distribuzione.

## <a name="check-the-status-of-a-deployment"></a>Controllare lo stato di una distribuzione

Per verificare lo stato della distribuzione, usare il comando seguente:

* Sostituire **SubscriptionID** e **AccessToken** con i valori usati in precedenza.
* Sostituire **ResourceGroupName** con il nome del gruppo di risorse creato nella sezione precedente.

```bash
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

Questo comando restituisce un documento JSON che contiene informazioni sull'operazione di distribuzione. L'elemento `"provisioningState"` contiene lo stato della distribuzione. Se contiene un valore di `"Succeeded"`, la distribuzione è stata completata.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un cluster HDInsight, usare le informazioni seguenti per acquisire familiarità con il cluster.

### <a name="hadoop-clusters"></a>Cluster Hadoop

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Cluster HBase

* [Introduzione a HBase in HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Sviluppare applicazioni Java per HBase in HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Cluster Storm

* [Sviluppare topologie Java per Storm in HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usare i componenti di Python in Storm in HDInsight](hdinsight-storm-develop-python-topology.md)
* [Distribuire e monitorare le topologie con Storm in HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

