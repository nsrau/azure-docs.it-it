---
title: Come usare Azure Kubernetes con Azure Cosmos DB
description: Informazioni su come eseguire il bootstrap in Azure un cluster Kubernetes che usa Azure Cosmos DB (anteprima)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 9dbbc914580d8d80a3f9b7d730574e24b44827c1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093736"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Come usare Azure Kubernetes con Azure Cosmos DB (anteprima)

L'API etcd in Azure Cosmos DB consente di usare Azure Cosmos DB come archivio back-end per Azure Kubernetes. Azure Cosmos DB implementa il protocollo Wire etcd, che consente ai server API del nodo master di usare Azure Cosmos DB esattamente come per accedere a una etcd installata localmente. L'API etcd in Azure Cosmos DB attualmente è disponibile in anteprima. Quando si usa l'API etcd di Azure Cosmos come archivio di backup per Kubernetes, si ottengono i vantaggi seguenti: 

* Non è necessario configurare e gestire etcd manualmente.
* Disponibilità elevata di etcd, garantita da Cosmos (99,99% in una singola area, al 99,999% in più aree).
* Scalabilità elastica di etcd.
* Soluzione protetta per impostazione predefinita e pronta per l'azienda.
* Contratti di servizio completi leader del settore.

Per altre informazioni sull'API etcd in Azure Cosmos DB, vedere l'articolo [Panoramica](etcd-api-introduction.md). Questo articolo illustra come usare il [Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (aks-engine) per il bootstrap in Azure di un cluster Kubernetes che usa [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) anziché un'API etcd installata e configurata localmente. 

## <a name="prerequisites"></a>Prerequisiti

1. Installare la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). È possibile scaricare l'interfaccia della riga di comando di Azure specifica del sistema operativo in uso e installarla.

1. Installare la [versione più recente](https://github.com/Azure/aks-engine/releases) di Azure Kubernetes Engine. Le istruzioni di installazione per diversi sistemi operativi sono disponibili nella pagina di [Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine). È sufficiente seguire la procedura nella sezione **Install AKS Engine** (Installazione di Azure Kubernetes Engine) del documento collegato. Al termine del download, estrarre il file ZIP.

   Azure Kubernetes Engine (**aks-engine**) genera i modelli di Azure Resource Manager per i cluster Kubernetes in Azure. L'input in aks-engine è un file di definizione del cluster che descrive il cluster desiderato, inclusi agente di orchestrazione, funzionalità e agenti. La struttura dei file di input è simile all'API pubblica per Azure Kubernetes Service.

1. L'API etcd in Azure Cosmos DB attualmente è in anteprima. Registrarsi per usare la versione di anteprima all'indirizzo: https://aka.ms/cosmosetcdapi-signup. Dopo aver inviato il modulo, la sottoscrizione verrà inclusa nella whitelist per usare l'API etcd di Cosmos Azure. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Distribuire il cluster con Azure Cosmos DB

1. Aprire una finestra del prompt dei comandi e accedere ad Azure con il comando seguente:

   ```azurecli-interactive
   az login 
   ```

1. Se si dispone di più di una sottoscrizione, passare alla sottoscrizione inserita nella whitelist per l'API etcd di Azure Cosmos DB. È possibile passare alla sottoscrizione necessaria usando il comando seguente:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Successivamente, creare un nuovo gruppo di risorse in cui verranno distribuite le risorse necessarie per il cluster di Azure Kubernetes. Assicurarsi di creare il gruppo di risorse nell'area "centralus". Non è obbligatorio che il gruppo di risorse sia nell'area "centralus". Tuttavia, l'API etcd di Azure Cosmos è attualmente disponibile solo per la distribuzione nell'area "centralus". Pertanto, è consigliabile che il cluster Kubernetes sia condiviso con l'istanza etcd di Cosmos:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Successivamente, creare un'entità servizio per il cluster di Azure Kubernetes in modo che possa comunicare con le risorse che fanno parte dello stesso gruppo di risorse. È possibile creare un'entità servizio usando Azure CLI, PowerShell o il portale di Azure. In questo esempio si userà l'interfaccia della riga di comando per crearla.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Questo comando restituisce i dettagli di un'entità servizio, ad esempio:
   
   ```cmd
   Retrying role assignment creation: 1/36
   {
     "appId": "8415a4e9-4f83-46ca-a704-107457b2e3ab",
     "displayName": "azure-cli-2019-04-19-19-01-46",
     "name": "http://azure-cli-2019-04-19-19-01-46",
     "password": "102aecd3-5e37-4f3d-8738-2ac348c2e6a7",
     "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
   }
   ```
   
   Annotare i campi **AppId** e **Password**, poiché questi parametri verranno usati nei passaggi successivi. 

1. Dal prompt dei comandi, passare alla cartella in cui si trova il file eseguibile di Azure Kubernetes Engine. Ad esempio, nel prompt dei comandi è possibile spostarsi nella cartella come:

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. Aprire un editor di testo di propria scelta e definire un modello di Resource Manager che distribuisce il cluster di Azure Kubernetes con l'API etcd di Azure Cosmos DB. Copiare la definizione JSON seguente nell'editor di testo e salvare il file come `apiModel.json`:

   ```json

   {
    "apiVersion": "vlabs",
    "properties": {
        "orchestratorProfile": {
            "orchestratorType": "Kubernetes",
            "kubernetesConfig": {
                "useManagedIdentity": false
            }
        },
        "masterProfile": {
            "count": 1,
            "dnsPrefix": "",
            "vmSize": "Standard_D2_v3",
            "cosmosEtcd": true
        },
        "agentPoolProfiles": [
            {
                "name": "agent",
                "count": 1,
                "vmSize": "Standard_D2_v3",
                "availabilityProfile": "AvailabilitySet"
            }
        ],
        "linuxProfile": {
            "adminUsername": "azureuser",
            "ssh": {
                "publicKeys": [
                    {
                        "keyData": ""
                    }
                ]
            }
        }
    }
   }
   ```

   Nel file di definizione JSON/cluster, il parametro della chiave da annotare è **"cosmosEtcd": true**. Questo parametro è presente nelle proprietà "masterProfile" e indica la distribuzione per usare l'API etcd di Azure Cosmos invece dell'API etcd normale. 

1. Distribuire il cluster di Azure Kubernetes che usa Azure Cosmos DB con il comando seguente:

   ```cmd
   aks-engine deploy \
     --subscription-id <Your_Azure_subscription_ID> \
     --client-id <Service_ principal_appId> \
     --client-secret <Service_ principal_password> \
     --dns-prefix <Region_unique_dns_name > \
     --location centralus \
     --resource-group <Resource_Group_Name> \
     --api-model <Fully_qualified_path_to_the_template_file>  \
     --force-overwrite
   ```

   Azure Kubernetes Engine utilizza una definizione del cluster che descrive la forma, la dimensione e la configurazione desiderata di Azure Kubernetes. Esistono numerose funzionalità che è possibile abilitare tramite la definizione del cluster. In questo esempio si useranno i parametri seguenti:

   * **subscription-id:** ID della sottoscrizione di Azure con l'API etcd di Azure Cosmos DB abilitata.
   * **client-id:** L'appId dell'entità servizio. `appId` è stato restituito come output nel passaggio 4.
   * **Client-secret:** Password dell'entità servizio o una password generata casualmente. Questo valore è stato restituito come output nel parametro 'password' nel passaggio 4. 
   * **dnsPrefix:** Un nome DNS univoco dell'area. Questo valore fa parte del nome host (sono valori di esempio: myprod1, staging).
   * **location:**  Posizione in cui il cluster deve essere distribuito, attualmente è supportato solo "centralus".

   > [!Note]
   > L'API etcd di Azure Cosmos etcd è attualmente disponibile solo per la distribuzione nell'area "centralus". 
 
   * **api-model:** Percorso completo del file del modello.
   * **force-overwrite:** Questa opzione viene usata per sovrascrivere automaticamente i file esistenti nella directory di output.
 
   Il comando seguente illustra un esempio di distribuzione:

   ```cmd
   aks-engine deploy \
     --subscription-id 1234fc61-1234-1234-1234-be1234d12c1b \
     --client-id 1234a4e9-4f83-46ca-a704-107457b2e3ab \
     --client-secret 123aecd3-5e37-4f3d-8738-2ac348c2e6a7 \
     --dns-prefix aks-sg-test \
     --location centralus \
     --api-model "C:\Users\demouser\Downloads\apiModel.json"  \
     --force-overwrite
   ```

## <a name="verify-the-deployment"></a>Verificare la distribuzione

La distribuzione del modello richiede alcuni minuti. Al termine della distribuzione, il prompt dei comandi visualizzerà l'output seguente:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

Il gruppo di risorse ora contiene le risorse, ad esempio: macchina virtuale, account di Azure Cosmos (API etcd), rete virtuale, set di disponibilità e altre risorse necessarie per il cluster Kubernetes. 

Il nome dell'account di Azure Cosmos corrisponderà al prefisso DNS specificato a cui verrà aggiunto k8s. Il provisioning dell'account di Azure Cosmos verrà effettuato automaticamente con un database denominato **EtcdDB** e un contenitore denominato **EtcdData**. Il contenitore archivierà tutti i dati relativi a etcd. Il provisioning del contenitore viene effettuato con un determinato numero di unità richieste ed è possibile [ridimensionare (aumentare/ridurre) la velocità effettiva](scaling-throughput.md) in base al carico di lavoro. 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [lavorare con il database, i contenitori e gli elementi di Azure Cosmos](databases-containers-items.md)
* Informazioni su come [ottimizzare i costi della velocità effettiva di provisioning](optimize-cost-throughput.md)

