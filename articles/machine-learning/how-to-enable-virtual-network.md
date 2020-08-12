---
title: Isolamento rete e privacy
titleSuffix: Azure Machine Learning
description: Usare una rete virtuale di Azure isolata con Azure Machine Learning per proteggere i processi di sperimentazione/training e di inferenza/assegnazione dei punteggi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 58f0a25ebeb7fb715cfba27d7482a031d1fe8c32
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121205"
---
# <a name="network-isolation-during-training-and-inference-with-private-endpoints-and-virtual-networks"></a>Isolamento della rete durante il training e l'inferenza con endpoint privati e reti virtuali
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come proteggere i cicli di vita di Machine Learning isolando Azure Machine Learning processi di training e inferenza in una rete virtuale di Azure (VNET). Una __rete virtuale__ funge da limite di sicurezza, isolando le risorse di Azure dalla rete Internet pubblica. È anche possibile aggiungere una rete virtuale di Azure alla rete locale. Aggiungendo le reti, si può eseguire il training dei modelli in modo sicuro e accedere ai modelli distribuiti per l'inferenza.

È possibile accedere Azure Machine Learning area di lavoro da una rete virtuale usando un __endpoint privato__. L'endpoint privato è un set di indirizzi IP privati all'interno della rete virtuale e l'accesso all'area di lavoro è limitato alla rete virtuale. L'endpoint privato consente di ridurre il rischio di exfiltration dei dati. Per altre informazioni sugli endpoint privati, vedere l'articolo [Collegamento privato di Azure](/azure/private-link/private-link-overview).

> [!NOTE]
> Quando si usa Mozilla Firefox, è possibile che si verifichino problemi di accesso a un'area di lavoro tramite un endpoint privato. Il problema può essere correlato all'impostazione DNS su HTTPS nel browser. Per risolvere questo problema, è consigliabile usare Microsoft Edge o Google Chrome.

Azure Machine Learning si basa su altri servizi di Azure per l'archiviazione dei dati e le risorse di calcolo, usati per il training e la distribuzione di modelli. Queste risorse possono anche essere create all'interno di una rete virtuale. È ad esempio possibile usare l'ambiente di calcolo di Azure Machine Learning per eseguire il training di un modello e quindi distribuire il modello nel servizio Azure Kubernetes. 

## <a name="prerequisites"></a>Prerequisiti

+ Un'[area di lavoro](how-to-manage-workspace.md) di Azure Machine Learning.

+ Conoscenza tecnica generale del [servizio Rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) e delle [reti IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Una rete virtuale e una subnet preesistenti da usare con le risorse di calcolo.

+ Per distribuire le risorse in una rete virtuale o in una subnet, l'account utente deve avere le autorizzazioni per le azioni seguenti nei controlli degli accessi in base al ruolo (RBAC) di Azure:

    - "Microsoft. Network/virtualNetworks/join/Action" sulla risorsa di rete virtuale.
    - "Microsoft. Network/virtualNetworks/subnet/join/Action" sulla risorsa della subnet.

    Per ulteriori informazioni sul controllo degli accessi in base al ruolo con la rete, vedere [ruoli predefiniti di rete](/azure/role-based-access-control/built-in-roles#networking)

## <a name="secure-your-workspace"></a>Proteggere l'area di lavoro

L'area di lavoro Azure Machine Learning può includere un endpoint __pubblico__ o un __endpoint privato__. Un endpoint pubblico è un set di indirizzi IP accessibili sulla rete Internet pubblica, mentre un endpoint privato è un set di indirizzi IP privati all'interno di una rete virtuale. 

Per limitare l'accesso all'area di lavoro in modo che si verifichi solo sugli indirizzi IP privati, usare un endpoint privato.

Poiché la comunicazione con l'area di lavoro è consentita solo dalla rete virtuale, tutti gli ambienti di sviluppo che usano l'area di lavoro devono essere membri della rete virtuale. Ad esempio, una macchina virtuale nella rete virtuale.

> [!IMPORTANT]
> L'endpoint privato non influisce sul piano di controllo di Azure (operazioni di gestione), ad esempio l'eliminazione dell'area di lavoro o la gestione delle risorse di calcolo. Ad esempio la creazione, l'aggiornamento o l'eliminazione di una destinazione di calcolo. Queste operazioni vengono eseguite sulla rete Internet pubblica come di consueto.
>
> L'endpoint privato impedisce l'accesso all'area di lavoro dall'esterno della rete virtuale.

Alcune combinazioni di risorse con un endpoint privato richiedono un'area di lavoro Enterprise Edition. Usare la tabella seguente per capire quali scenari richiedono l'edizione Enterprise:

| Scenario | Enterprise</br>edition | Basic</br>edition |
| ----- |:-----:|:-----:| 
| Nessuna rete virtuale o endpoint privato | ✔ | ✔ |
| Area di lavoro senza endpoint privato. Altre risorse (ad eccezione di Registro Azure Container) in una rete virtuale | ✔ | ✔ |
| Area di lavoro senza endpoint privato. Altre risorse con endpoint privato | ✔ | |
| Area di lavoro con endpoint privato. Altre risorse (ad eccezione di Registro Azure Container) in una rete virtuale | ✔ | ✔ |
| Area di lavoro e qualsiasi altra risorsa con endpoint privato | ✔ | |
| Area di lavoro con endpoint privato. Altre risorse senza endpoint privato o rete virtuale | ✔ | ✔ |
| Registro Azure Container in una rete virtuale | ✔ | |
| Chiavi gestite dal cliente per l'area di lavoro | ✔ | |

> [!WARNING]
> 
> Azure Machine Learning istanze di calcolo non è supportata in un'area di lavoro in cui è abilitato l'endpoint privato.
>
> Azure Machine Learning non supporta l'uso di un servizio Azure Kubernetes con endpoint privato abilitato. In alternativa, è possibile usare il servizio Azure Kubernetes in una rete virtuale. Per altre informazioni, vedere [Proteggere i processi di sperimentazione e inferenza di Azure ML in una rete virtuale di Azure](how-to-enable-virtual-network.md).

Per altre informazioni sugli endpoint privati in Azure, vedere l'articolo relativo al [collegamento privato di Azure](/azure/private-link/private-link-overview) .

### <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Creare un'area di lavoro che usa un endpoint privato

È possibile creare una nuova area di lavoro con un endpoint privato usando il Azure Machine Learning SDK, l'interfaccia della riga di comando, un modello di Azure Resource Manager o il portale di Azure.

__Requisiti__

* Per la rete virtuale usata con l'endpoint privato è necessario disabilitare i criteri di rete. Per ulteriori informazioni, vedere [disabilitare i criteri di rete per un endpoint privato](/azure/private-link/disable-private-endpoint-network-policy).

__Limitazioni__

* Non è possibile connettersi all'area di lavoro tramite la rete Internet pubblica, solo dall'interno della rete virtuale.

* Se si creano più aree di lavoro usando endpoint privati e si usa la stessa zona DNS privata, solo la prima area di lavoro viene aggiunta ai __collegamenti alla rete virtuale__ della zona DNS privata.

    Per ovviare a questa limitazione, aggiungere manualmente il collegamento rete virtuale per le aree di lavoro aggiuntive. Per ulteriori informazioni, vedere [che cos'è un collegamento di rete virtuale](/azure/dns/private-dns-virtual-network-links).

__Configurazione__

Per informazioni su come creare un'area di lavoro usando una rete virtuale e un endpoint privato, insieme ad altre opzioni di configurazione, vedere gli articoli seguenti:

* [Usare un modello di Azure Resource Manager per creare un'area di lavoro per Azure Machine Learning](how-to-create-workspace-template.md).
* [Creare aree di lavoro nel portale](how-to-manage-workspace.md).
* [Creare aree di lavoro con la CLI di Azure](how-to-manage-workspace-cli.md).
* Per usare Python SDK, vedere la documentazione di riferimento relativa a [PrivateEndPointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.private_endpoint.privateendpointconfig?view=azure-ml-py) e [Workspace. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) .

<a id="amlcompute"></a>

## <a name="machine-learning-studio"></a>Machine Learning Studio

__Requisiti__

* Per accedere ai dati in un account di archiviazione, l'account di archiviazione deve trovarsi nella stessa rete virtuale dell'area di lavoro.

* Se i dati vengono archiviati in una rete virtuale, è necessario usare un' [identità gestita](../active-directory/managed-identities-azure-resources/overview.md) dell'area di lavoro per concedere a Studio l'accesso ai dati.

    > [!IMPORTANT]
    > Sebbene la maggior parte di studio funzioni con i dati archiviati in una rete virtuale, i notebook integrati __non lo__supportano. I notebook integrati non supportano l'uso di archiviazione che si trova in una rete virtuale. È invece possibile usare i notebook di Jupyter da un'istanza di calcolo. Per altre informazioni, vedere la sezione [cluster di calcolo & istanze](#compute-instance) .

    Se non si concede l'accesso a Studio, verrà visualizzato questo errore, `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` le operazioni seguenti non saranno disponibili:

    * Visualizzare in anteprima i dati in studio.
    * Visualizza i dati nella finestra di progettazione.
    * Inviare un esperimento AutoML.
    * Avviare un progetto di assegnazione di etichette.

__Limitazioni__

* Azure Machine Learning Studio supporta la lettura dei dati dai seguenti tipi di archivio dati in una rete virtuale:

    * BLOB Azure
    * Azure Data Lake Storage Gen1
    * Azure Data Lake Storage Gen2
    * database SQL di Azure

__Configurazione__

* __Configurare gli archivi dati per usare un'identità gestita__ per accedere ai dati. Questi passaggi aggiungono l'identità gestita dell'area di lavoro come __lettore__ al servizio di archiviazione usando il controllo degli accessi in base al ruolo (RBAC) di Azure. Accesso in __lettura__ consente all'area di lavoro di recuperare le impostazioni del firewall e assicurarsi che i dati non lascino la rete virtuale.

    1. In Studio selezionare __datastores__.

    1. Per creare un nuovo archivio dati, selezionare __+ nuovo archivio dati__. Per aggiornarne uno esistente, selezionare l'archivio dati e selezionare __Aggiorna credenziali__.

    1. Nelle impostazioni dell'archivio dati selezionare __Sì__ per __Consenti Azure Machine Learning servizio di accedere allo spazio di archiviazione usando l'identità gestita dell'area di lavoro__.

    > [!NOTE]
    > Per rendere effettive queste modifiche possono essere necessari fino a 10 minuti.

* Per l' __archiviazione BLOB di Azure__, l'identità gestita dell'area di lavoro deve essere aggiunta anche come [lettore dati BLOB](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) , in modo da poter leggere i dati dall'archivio BLOB.

* La finestra di progettazione USA l'account di archiviazione collegato all'area di lavoro per archiviare l'output per impostazione predefinita. Tuttavia, è possibile specificarlo per archiviare l'output in qualsiasi archivio dati a cui si ha accesso. Se l'ambiente USA reti virtuali, è possibile usare questi controlli per garantire che i dati rimangano protetti e accessibili. Per impostare una nuova risorsa di archiviazione predefinita per una pipeline:

    1. In una bozza di pipeline selezionare l' **icona dell'ingranaggio impostazioni** accanto al titolo della pipeline.
    1. Selezionare la voce **Seleziona archivio dati predefinito** .
    1. Consente di specificare un nuovo archivio dati.

    È anche possibile eseguire l'override dell'archivio dati predefinito in base al modulo. Questo consente di controllare il percorso di archiviazione per ogni singolo modulo.

    1. Selezionare il modulo di cui si desidera specificare l'output.
    1. Espandere la sezione **impostazioni di output** .
    1. Selezionare **Sostituisci impostazioni di output predefinite**.
    1. Selezionare **imposta impostazioni di output**.
    1. Consente di specificare un nuovo archivio dati.

* Se si usa __Azure Data Lake storage Gen2__, è possibile usare gli elenchi di controllo di accesso (ACL) di tipo RBAC e POSIX per controllare l'accesso ai dati all'interno di una rete virtuale.

    Per utilizzare il controllo degli accessi in base al ruolo, aggiungere l'identità gestita dell'area di lavoro al ruolo [lettore dati](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) Per altre informazioni, vedere [Controllo degli accessi in base al ruolo](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control).

    Per usare gli ACL, è possibile assegnare l'accesso all'identità gestita dell'area di lavoro in modo analogo a qualsiasi altro principio di sicurezza. Per altre informazioni, vedere [elenchi di controllo di accesso per file e directory](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

* __Azure Data Lake storage Gen1__ supporta solo gli elenchi di controllo di accesso in stile POSIX. È possibile assegnare l'accesso all'identità gestita dell'area di lavoro alle risorse in modo analogo a qualsiasi altro principio di sicurezza. Per ulteriori informazioni, vedere [controllo di accesso in Azure Data Lake storage Gen1](../data-lake-store/data-lake-store-access-control.md).

* Per accedere ai dati archiviati in un __database SQL di Azure__ tramite identità gestita, è necessario creare un utente indipendente di SQL che esegue il mapping all'identità gestita. Per altre informazioni sulla creazione di un utente da un provider esterno, vedere [creare utenti indipendenti con mapping a identità Azure ad](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

    Dopo aver creato un utente indipendente di SQL, concedere le autorizzazioni tramite il [comando T-SQL Grant](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql).

* Se si __accede a Studio da una risorsa all'interno di una rete virtuale__ , ad esempio un'istanza di calcolo o una macchina virtuale, è necessario consentire il traffico in uscita dalla rete virtuale a Studio. 

    Ad esempio, se si usano gruppi di sicurezza di rete (NSG) per limitare il traffico in uscita, aggiungere una regola a una destinazione di __tag del servizio__ di __AzureFrontDoor. frontend__.

## <a name="use-datastores-and-datasets"></a>Usare gli archivi dati e i set di dati

> [!NOTE]
> Questa sezione illustra l'utilizzo dell'archivio dati e del set di dati per l'esperienza SDK. Per ulteriori informazioni sull'esperienza di studio, vedere la sezione [Machine Learning Studio](#machine-learning-studio).

__Limitazioni__

Per impostazione predefinita, Azure Machine Learning esegue la validità dei dati e i controlli delle credenziali quando si tenta di accedere ai dati tramite l'SDK. Se i dati sono protetti da una rete virtuale, Azure Machine Learning non possono accedere ai dati e non vengono verificati. Per risolvere questo problema, ignorare la convalida durante la creazione di archivi dati e set di dati.

* Quando si usa un __archivio dati__:

    Azure Data Lake Store Gen1 e Azure Data Lake Store Gen2 ignorano la convalida per impostazione predefinita, pertanto non è necessaria alcuna azione aggiuntiva. Per i servizi seguenti, tuttavia, è possibile usare una sintassi simile per ignorare la convalida dell'archivio dati:

    - Archiviazione BLOB di Azure
    - Condivisione file di Azure
    - PostgreSQL
    - database SQL di Azure

    L'esempio di codice seguente crea un nuovo archivio dati BLOB di Azure e imposta `skip_validation=True` .

    ```python
    blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  
                                                            datastore_name=blob_datastore_name,  
                                                            container_name=container_name,  
                                                            account_name=account_name, 
                                                            account_key=account_key, 
                                                            skip_validation=True ) // Set skip_validation to true
    ```

* Quando si usa un __set di dati__:

    La sintassi per ignorare la convalida del set di dati è simile per i tipi di set di dati seguenti:
    - File delimitato
    - JSON 
    - Parquet
    - SQL
    - File

    Il codice seguente crea un nuovo set di dati JSON e imposta `validate=False` .

    ```python
    json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 
                                                   validate=False) 
    ```

## <a name="azure-storage-account"></a>Account di archiviazione di Azure

> [!IMPORTANT]
> È possibile inserire sia l' _account di archiviazione predefinito_ per Azure Machine Learning, sia gli _account di archiviazione non predefiniti_ in una rete virtuale.

__Requisiti__

* L'account di archiviazione deve trovarsi nella stessa rete virtuale e nella stessa subnet delle istanze di calcolo o dei cluster di elaborazione usati per il training o l'inferenza.

__Configurazione__

Per proteggere l'account di archiviazione di Azure usato dall'area di lavoro, abilitare un __endpoint privato__ o un __endpoint del servizio__ per l'account di archiviazione nella rete virtuale.

* Per configurare l'account di archiviazione per l'uso di un __endpoint privato__, vedere l'articolo [usare endpoint privati](/azure/storage/common/storage-private-endpoints.md) .

* Per configurare l'account di archiviazione per l'uso di un __endpoint del servizio__, seguire questa procedura:

    1. Per aggiungere l'account di archiviazione alla rete virtuale usata dall'area di lavoro, usare le informazioni nella sezione __concedere l'accesso da una rete virtuale__ dell'articolo [configurare i firewall e le reti virtuali di archiviazione di Azure](/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network) .
    1. Per consentire l'accesso dai servizi Microsoft nella rete virtuale (ad esempio Azure Machine Learning), usare le informazioni nella sezione __eccezioni__ dell'articolo [configurare i firewall e le reti virtuali di archiviazione di Azure](/azure/storage/common/storage-network-security#exceptions) .
    1. Quando si usa Azure Machine Learning SDK, l'ambiente di sviluppo deve essere in grado di connettersi all'account di archiviazione di Azure. Se l'account di archiviazione si trova all'interno di una rete virtuale, il firewall deve consentire l'accesso dall'indirizzo IP dell'ambiente di sviluppo. Per aggiungere l'indirizzo IP dell'ambiente di sviluppo, usare le informazioni nella sezione __concedere l'accesso da un intervallo IP Internet__ dell'articolo [configurare i firewall e le reti virtuali di archiviazione di Azure](/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range) .

## <a name="azure-container-registry"></a>Registro Azure Container

__Requisiti__

* L'area di lavoro di Azure Machine Learning deve essere di tipo Enterprise. Per informazioni sull'aggiornamento, vedere le istruzioni per [l'aggiornamento a Enterprise Edition](how-to-manage-workspace.md#upgrade).
* L'area dell'area di lavoro Azure Machine Learning deve essere un' [area abilitata per il collegamento privato](https://docs.microsoft.com/azure/private-link/private-link-overview#availability). 
* Il Container Registry di Azure deve essere una versione Premium. Per altre informazioni sull'aggiornamento, vedere [Cambiare SKU](/azure/container-registry/container-registry-skus#changing-skus).
* Registro Azure Container deve essere nella stessa rete virtuale e nella stessa subnet dell'account di archiviazione e delle destinazioni di calcolo usati per il training o l'inferenza.
* L'area di lavoro di Azure Machine Learning deve contenere un [cluster di elaborazione di Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute).

__Limitazioni__

* Quando Registro Azure Container è in una rete virtuale, Azure Machine Learning non può usarlo per compilare direttamente le immagini Docker. In questo caso viene usato il cluster di elaborazione per compilare le immagini.

__Configurazione__

1. Per trovare il nome del Registro Azure Container per la propria area di lavoro, usare uno dei metodi seguenti:

    __Azure portal__

    Nella sezione Panoramica dell'area di lavoro il valore __Registro__ è collegato al Registro Azure Container.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Registro Azure Container per l'area di lavoro" border="true":::

    __Interfaccia della riga di comando di Azure__

    Se è stata installata [l'estensione Machine Learning per l'interfaccia della riga di comando di Azure](reference-azure-machine-learning-cli.md), è possibile usare il comando `az ml workspace show` per visualizzare le informazioni sull'area di lavoro.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Il comando restituisce un valore analogo a `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`. L'ultima parte della stringa è il nome del Registro Azure Container per l'area di lavoro.

1. Per limitare l'accesso alla rete virtuale, seguire la procedura relativa alla [configurazione dell'accesso alla rete per il registro](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). Quando si aggiunge la rete virtuale, selezionare la rete virtuale e la subnet per le risorse di Azure Machine Learning.

1. Usare Python SDK di Azure Machine Learning per configurare un cluster di elaborazione per compilare immagini Docker. Nel frammento di codice riportato di seguito viene illustrato come effettuare questa operazione:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > L'account di archiviazione, il cluster di elaborazione e Registro Azure Container devono essere tutti nella stessa subnet della rete virtuale.
    
    Per altre informazioni, vedere il riferimento per il metodo [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-).

1. Per consentire all'area di lavoro di comunicare con l'istanza di ACR, applicare il modello di Azure Resource Manager seguente:

    > [!WARNING]
    > Questo modello Abilita un endpoint privato per l'area di lavoro e lo modifica in un'area di lavoro aziendale. Non è possibile annullare queste modifiche.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

## <a name="key-vault-instance"></a>Istanza dell'insieme di credenziali delle chiavi 

__Requisiti__

__Limitazioni__

__Configurazione__ 

Per usare Azure Machine Learning funzionalità di sperimentazione con Azure Key Vault dietro una rete virtuale, usare l'articolo [configurare Azure Key Vault firewall e reti virtuali](/azure/key-vault/general/network-security) .

> [!IMPORTANT]
> Quando si seguono i passaggi descritti nell'articolo, usare la stessa rete virtuale usata dalle risorse di calcolo per la sperimentazione. È inoltre necessario __consentire ai servizi Microsoft attendibili di ignorare il firewall__.

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Cluster di elaborazione e istanze di calcolo 

__Requisiti__

* La rete virtuale deve trovarsi nella stessa sottoscrizione e area dell'area di lavoro di Azure Machine Learning.
* La subnet specificata per il cluster di elaborazione o l'istanza di calcolo deve avere indirizzi IP non assegnati sufficienti per contenere il numero di macchine virtuali usate come destinazione. Se la subnet non ha abbastanza indirizzi IP non assegnati, verrà parzialmente allocato un cluster di elaborazione.
* Se si prevede di proteggere la rete virtuale limitando il traffico, è necessario che alcune porte rimangano aperte per il servizio di calcolo.
* Se si prevede di inserire più istanze di calcolo o cluster di elaborazione in una sola rete virtuale, potrebbe essere necessario richiedere un aumento di quota per una o più risorse.
* Se anche gli account di archiviazione di Azure per l'area di lavoro sono protetti in una rete virtuale, devono trovarsi nella stessa rete virtuale dell'istanza di calcolo o del cluster di elaborazione di Azure Machine Learning. 
* Per il corretto funzionamento della funzionalità Jupyter dell'istanza di calcolo, assicurarsi che la comunicazione con il Websocket non sia disabilitata.

__Limitazioni__

* L'istanza di calcolo o il cluster di elaborazione di Machine Learning alloca automaticamente le risorse di rete aggiuntive __nel gruppo di risorse contenente la rete virtuale__. Per ogni istanza o cluster, il servizio alloca le risorse seguenti:

    * Un gruppo di sicurezza di rete
    * Un indirizzo IP pubblico
    * Un bilanciamento del carico
    
    Per i __cluster di calcolo__, queste risorse vengono eliminate e ricreate ogni volta che il cluster si ridimensiona fino a 0 nodi.
    
    Per un' __istanza di calcolo__ , le risorse vengono conservate fino a quando l'istanza non viene eliminata (l'arresto non comporta la rimozione delle risorse).

    Queste risorse sono limitate in base alle [quote delle risorse](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) della sottoscrizione.

__Configurazione__

* Per creare un cluster dell'ambiente di calcolo di Machine Learning seguire questa procedura:

    1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com/), quindi selezionare la sottoscrizione e l'area di lavoro.
    1. Selezionare __Calcolo__ a sinistra.
    1. Selezionare __Cluster di training__ dal centro, quindi selezionare __+__ .
    1. Nella finestra di dialogo di __creazione di un cluster di training__ espandere la sezione __Impostazioni avanzate__.
    1. Per configurare questa risorsa di calcolo per l'uso di una rete virtuale, eseguire le azioni seguenti nella sezione __Configura rete virtuale__:

        1. Nell'elenco a discesa __Gruppo di risorse__ selezionare il gruppo di risorse che contiene la rete virtuale.
        1. Selezionare la rete virtuale che contiene la subnet nell'elenco a discesa __Rete virtuale__.
        1. Nell'elenco a discesa __Subnet__ selezionare la subnet da usare.

    ![Impostazioni della rete virtuale per l'ambiente di calcolo di Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

    È anche possibile creare un cluster dell'ambiente di calcolo di Machine Learning usando Azure Machine Learning SDK. Il codice seguente crea un nuovo cluster dell'ambiente di calcolo di Machine Learning nella subnet `default` di una rete virtuale denominata `mynetwork`:

    ```python
    from azureml.core.compute import ComputeTarget, AmlCompute
    from azureml.core.compute_target import ComputeTargetException

    # The Azure virtual network name, subnet, and resource group
    vnet_name = 'mynetwork'
    subnet_name = 'default'
    vnet_resourcegroup_name = 'mygroup'

    # Choose a name for your CPU cluster
    cpu_cluster_name = "cpucluster"

    # Verify that cluster does not exist already
    try:
        cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
        print("Found existing cpucluster")
    except ComputeTargetException:
        print("Creating new cpucluster")

        # Specify the configuration for the new cluster
        compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                            min_nodes=0,
                                                            max_nodes=4,
                                                            vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                            vnet_name=vnet_name,
                                                            subnet_name=subnet_name)

        # Create the cluster with the specified name and configuration
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

        # Wait for the cluster to be completed, show the output log
        cpu_cluster.wait_for_completion(show_output=True)
    ```

    Al termine del processo di creazione, eseguire il training del modello usando il cluster in un esperimento. Per altre informazioni, vedere [Configurare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md).

    [!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

* Se si usano notebook in un'istanza di calcolo di Azure, è necessario assicurarsi che il notebook sia in esecuzione in una risorsa di calcolo dietro la stessa rete virtuale e la stessa subnet dei dati. 

    Configurare l'istanza di calcolo in modo che si trovi nella stessa rete virtuale durante la creazione in **Impostazioni avanzate**  >  **Configura rete virtuale**. Non è possibile aggiungere un'istanza di calcolo esistente a una rete virtuale.

* Se si prevede di proteggere la rete virtuale limitando il traffico di rete da e verso la rete Internet pubblica, è necessario consentire le comunicazioni in ingresso dal servizio Azure Batch.

    Il servizio Batch aggiunge gruppi di sicurezza di rete a livello di interfacce di rete collegate alle macchine virtuali. Questi gruppi di sicurezza di rete configurano automaticamente le regole in ingresso e in uscita per consentire il traffico seguente:

    - Traffico TCP in ingresso sulle porte 29876 e 29877 da un __tag del servizio__ di __BatchNodeManagement__.

    - (Facoltativo) Traffico TCP in ingresso sulla porta 22 per consentire l'accesso remoto. Usare questa porta solo per effettuare la connessione usando SSH sull'indirizzo IP pubblico.

    - Traffico in uscita su qualsiasi porta verso la rete virtuale.

    - Traffico in uscita su qualsiasi porta verso Internet.

    - Per il traffico TCP in ingresso dell'istanza di calcolo sulla porta 44224 da un __tag del servizio__ di __AzureMachineLearning__.

    > [!IMPORTANT]
    > Prestare attenzione se si modificano o aggiungono regole in ingresso o in uscita nei gruppi di sicurezza di rete configurati per Batch. Se un gruppo di sicurezza di rete blocca la comunicazione con i nodi di calcolo, il servizio dell'ambiente di calcolo imposta i nodi di calcolo come non utilizzabili.
    >
    > Non è necessario specificare i gruppi di sicurezza di rete a livello di subnet perché il servizio Azure Batch configura gruppi di sicurezza di rete propri. Tuttavia, se la subnet che contiene il Azure Machine Learning calcolo ha associato gruppi o un firewall, è necessario consentire anche il traffico indicato in precedenza.

* Se non si vogliono usare le regole in uscita predefinite e si vuole limitare l'accesso in uscita della rete virtuale, seguire questa procedura:

    1. Negare la connessione Internet in uscita usando le regole del gruppo di sicurezza di rete.
    1. Per un'__istanza di calcolo__ o un __cluster di elaborazione__, limitare il traffico in uscita agli elementi seguenti:
        - Archiviazione di Azure, usando __tag del servizio__ di __Storage.RegionName__. Dove `{RegionName}` è il nome di un'area di Azure.
        - Registro Azure Container, usando il __tag del servizio__ di __AzureContainerRegistry.RegionName__. Dove `{RegionName}` è il nome di un'area di Azure.
        - Azure Machine Learning, usando il __tag del servizio__ di __AzureMachineLearning__
        - Azure Resource Manager, usando il __tag del servizio__ di __AzureResourceManager__
        - Azure Active Directory, usando il __tag del servizio__ di __AzureActiveDirectory__

    > [!NOTE]
    > Se si prevede di usare le immagini Docker predefinite fornite da Microsoft e di abilitare le dipendenze gestite dall'utente, è necessario usare anche i __tag di servizio__seguenti:
    >
    > * __MicrosoftContainerRegistry__
    > * __AzureFrontDoor.FirstParty__
    >
    > Questa configurazione è necessaria quando si ha un codice simile ai frammenti di codice seguenti come parte degli script di training:
    >
    > __Training RunConfig__
    > ```python
    > # create a new runconfig object
    > run_config = RunConfiguration()
    > 
    > # configure Docker 
    > run_config.environment.docker.enabled = True
    > # For GPU, use DEFAULT_GPU_IMAGE
    > run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
    > run_config.environment.python.user_managed_dependencies = True
    > ```
    >
    > __Training strumento di valutazione__
    > ```python
    > est = Estimator(source_directory='.',
    >                 script_params=script_params,
    >                 compute_target='local',
    >                 entry_script='dummy_train.py',
    >                 user_managed=True)
    > run = exp.submit(est)
    > ```

* Se si usa il [tunneling forzato](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm) con Azure Machine Learning calcolo, è necessario consentire la comunicazione con la rete Internet pubblica dalla subnet che contiene la risorsa di calcolo. Questa comunicazione viene usata per la pianificazione delle attività e l'accesso ad archiviazione di Azure.

    È possibile eseguire questa operazione in due modi:

    * Usare una [rete virtuale NAT](../virtual-network/nat-overview.md). Un gateway NAT fornisce la connettività Internet in uscita per una o più subnet nella rete virtuale. Per informazioni, vedere [progettazione di reti virtuali con risorse del gateway NAT](../virtual-network/nat-gateway-resource.md).

    * Aggiungere [route definite dall'utente (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) alla subnet che contiene la risorsa di calcolo. Stabilire una route definita dall'utente per ogni indirizzo IP usato dal servizio Azure Batch nell'area in cui si trovano le risorse. Queste route consentono al servizio Batch di comunicare con i nodi di calcolo per la pianificazione delle attività. Aggiungere anche l'indirizzo IP per il servizio Azure Machine Learning in cui si trovano le risorse, poiché è necessario per l'accesso alle istanze di calcolo. Per ottenere un elenco di indirizzi IP del servizio Batch e del servizio Azure Machine Learning, usare uno dei metodi seguenti:

        * Scaricare [gli intervalli IP e i tag del servizio di Azure](https://www.microsoft.com/download/details.aspx?id=56519) e cercare `BatchNodeManagement.<region>` e `AzureMachineLearning.<region>` nel file, dove `<region>` è l'area di Azure.

        * Usare l['interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) per scaricare le informazioni. L'esempio seguente scarica le informazioni relative all'indirizzo IP e filtra le informazioni per l'area Stati Uniti orientali 2:

            ```azurecli-interactive
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
            ```
        
        Quando si aggiungono le route definite dall'utente, definire la route per ogni prefisso dell'indirizzo IP di Batch correlato e impostare __Tipo hop successivo__ su __Internet__. 

        Oltre ai UdR definiti, il traffico in uscita verso archiviazione di Azure deve essere consentito tramite l'appliance di rete locale. In particolare, gli URL per questo traffico si trovano nei formati seguenti: `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` e `<account>.blob.core.windows.net` . 

        Per altre informazioni, vedere [Creare un pool di Azure Batch in una rete virtuale](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Servizio Azure Kubernetes

__Requisiti__

* L'istanza di Azure Kubernetes Service (AKS) e la rete virtuale di Azure devono trovarsi nella stessa area. Se si proteggono gli account di archiviazione di Azure usati dall'area di lavoro in una rete virtuale, è necessario che si trovino nella stessa rete virtuale dell'istanza di AKS.

* Per __pianificare gli indirizzi IP__ per il cluster, seguire i prerequisiti nell'articolo configurare la [funzionalità di rete avanzata in Azure KUBERNETES Service (AKS)](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites) .

* Per limitare le comunicazioni in ingresso e in uscita al cluster del servizio contenitore di Azure, seguire le istruzioni nell'articolo [controllare il traffico in uscita per i nodi del cluster in Azure Kubernetes Service](/azure/aks/limit-egress-traffic) per assicurarsi che _le comunicazioni in uscita da_ AKS siano configurate correttamente. I requisiti di comunicazione in _ingresso_ , se presenti, vengono richiamati nella sezione di configurazione riportata di seguito.

__Limitazioni__

* Se si vuole usare un servizio Azure Kubernetes con collegamento privato abilitato, è necessario collegarlo all'area di lavoro. Non è possibile creare un cluster del servizio Azure Kubernetes con collegamento privato da Azure Machine Learning (SDK, portale, CLI e così via).

__Configurazione__

> [!IMPORTANT]
> Questa sezione contiene più configurazioni. Selezionare quella più adatta alle proprie esigenze.

* Per __usare AKS dietro la rete virtuale con un servizio di bilanciamento del carico pubblico__:

    1. Creare o alleghi il cluster AKS. Se si sta __creando__ un nuovo cluster, è necessario specificare la rete virtuale da usare per il cluster.
    
        L'esempio seguente illustra come creare un nuovo cluster AKS usando Python SDK:

        ```python
        from azureml.core.compute import ComputeTarget, AksCompute

        # Create the compute configuration and set virtual network information
        config = AksCompute.provisioning_configuration(location="eastus2")
        config.vnet_resourcegroup_name = "mygroup"
        config.vnet_name = "mynetwork"
        config.subnet_name = "default"
        config.service_cidr = "10.0.0.0/16"
        config.dns_service_ip = "10.0.0.10"
        config.docker_bridge_cidr = "172.17.0.1/16"

        # Create the compute target
        aks_target = ComputeTarget.create(workspace=ws,
                                        name="myaks",
                                        provisioning_configuration=config)
        ```

        Se si dispone di un cluster del servizio contenitore di Azure già presente dietro la rete virtuale, usare le informazioni disponibili nell'articolo [distribuire in Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) .

    1. Verificare che il gruppo di sicurezza di rete che controlla la rete virtuale disponga di una regola di sicurezza in __ingresso__ abilitata per l'endpoint di assegnazione dei punteggi, in modo che possa essere chiamata dall'esterno della rete virtuale.

* Per __usare AKS dietro la rete virtuale, con un servizio di bilanciamento del carico privato__:

    1. Creare o alleghi il cluster AKS. Se si sta __creando__ un nuovo cluster, è necessario specificare la rete virtuale da usare per il cluster.
    
        L'esempio seguente illustra come creare un nuovo cluster AKS usando Python SDK:

        ```python
        from azureml.core.compute import ComputeTarget, AksCompute

        # Create the compute configuration and set virtual network information
        config = AksCompute.provisioning_configuration(location="eastus2")
        config.vnet_resourcegroup_name = "mygroup"
        config.vnet_name = "mynetwork"
        config.subnet_name = "default"
        config.service_cidr = "10.0.0.0/16"
        config.dns_service_ip = "10.0.0.10"
        config.docker_bridge_cidr = "172.17.0.1/16"

        # Create the compute target
        aks_target = ComputeTarget.create(workspace=ws,
                                        name="myaks",
                                        provisioning_configuration=config)
        ```

        Se si dispone di un cluster del servizio contenitore di Azure già presente dietro la rete virtuale, usare le informazioni disponibili nell'articolo [distribuire in Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) .

    1. Per trovare l'entità servizio o l'ID identità gestita per AKS, usare i seguenti comandi dell'interfaccia della riga di comando di Azure. Sostituire `<aks-cluster-name>` con il nome del cluster. Sostituire `<resource-group-name>` con il nome del gruppo di risorse che _contiene il cluster AKS_:

        ```azurecli-interactive
        az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
        ``` 

        Se questo comando restituisce un valore di `msi` , usare il comando seguente per identificare l'ID entità per l'identità gestita:

        ```azurecli-interactive
        az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
        ```
    1. Per trovare l'ID del gruppo di risorse che contiene la rete virtuale, usare il comando seguente. Sostituire `<resource-group-name>` con il nome del gruppo di risorse che _contiene la rete virtuale_:

        ```azurecli-interactive
        az group show -n <resource-group-name> --query id
        ```

    1. Per aggiungere l'entità servizio o l'identità gestita come collaboratore alla rete, usare il comando seguente. Sostituire `<SP-or-managed-identity>` con l'ID restituito per l'entità servizio o l'identità gestita. Sostituire `<resource-group-id>` con l'ID restituito per il gruppo di risorse che contiene la rete virtuale:

        ```azurecli-interactive
        az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
        ```

    1. Per aggiornare il cluster AKS per l'uso di un servizio di __bilanciamento del carico privato__, usare Python SDK. Il frammento di codice seguente illustra come aggiornare un cluster AKS esistente che è stato aggiunto o collegato all'area di lavoro:
    
        ```python
        import azureml.core
        from azureml.core.compute.aks import AksUpdateConfiguration
        from azureml.core.compute import AksCompute

        # ws = workspace object. Creation not shown in this snippet
        aks_target = AksCompute(ws,"myaks")

        # Change to the name of the subnet that contains AKS
        subnet_name = "default"
        # Update AKS configuration to use an internal load balancer
        update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
        aks_target.update(update_config)
        # Wait for the operation to complete
        aks_target.wait_for_completion(show_output = True)
        ```

* Per __Alleghire AKS usando un endpoint privato__:

    1. Usare il comando dell'interfaccia della riga di comando di Azure seguente per ottenere l' __ID subnet__ della subnet che il cluster AKS utilizzerà. Ad esempio, la subnet predefinita per la rete virtuale:
    
        ```azurecli
        az network vnet show -g myresourcegroup -n myvnet --query subnets[].id
        ```
        
        Questo comando restituisce una matrice di ID per le subnet nella rete virtuale. Il codice JSON seguente è un esempio di una rete virtuale con una sola subnet:

        ```json
        [
            "/subscriptions/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/default"
        ]
        ```

        Se vengono restituiti più ID, selezionare quello che si vuole usare.

    1. Per creare un cluster del servizio contenitore di Azure un endpoint privato, usare le informazioni nella sezione __rete avanzata__ dell'articolo [creare un cluster di servizi Kubernetes di Azure privato](/azure/aks/private-clusters#advanced-networking) . Quando si crea il cluster, usare l'ID subnet del comando precedente con il `--vnet-subnet-id` parametro.

    1. Per aggiungere il cluster, usare le informazioni contenute nell'articolo [distribuire in Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) .

    > [!TIP]
    > Quando si usa questa configurazione e si limitano le comunicazioni in uscita dal cluster AKS, non è necessario consentire la comunicazione sulle porte __1194__ o __9000__. Per altre porte che devono essere consentite, fare riferimento alle linee guida nell'articolo [controllare il traffico in uscita per i nodi del cluster in Azure Kubernetes Service](/azure/aks/limit-egress-traffic) .

## <a name="use-azure-container-instances-aci"></a>Usare Istanze di Azure Container

__Requisiti__

* Le Istanze di Azure Container vengono create dinamicamente quando si distribuisce un modello. Per abilitare Azure Machine Learning per la creazione di Istanze di Azure Container all'interno della rete virtuale, è necessario abilitare la __delega subnet__ per la subnet usata dalla distribuzione.

__Limitazioni__

* La rete virtuale deve trovarsi nello stesso gruppo di risorse dell'area di lavoro Azure Machine Learning.

* Il Container Registry di Azure (ACR) per l'area di lavoro non può essere anche presente nella rete virtuale.

__Configurazione__

Per usare Istanze di Azure Container all'area di lavoro, seguire questa procedura:

1. Per abilitare la delega subnet nella rete virtuale, usare le informazioni contenute nell'articolo relativo ad [aggiunta e rimozione di una delega subnet](../virtual-network/manage-subnet-delegation.md). È possibile abilitare la delega quando si crea una rete virtuale o aggiungerla a una rete esistente.

    > [!IMPORTANT]
    > Quando si abilita la delega, usare `Microsoft.ContainerInstance/containerGroups` come valore di __Delega subnet a un servizio__.

2. Distribuire il modello usando [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), usare i parametri `vnet_name` e `subnet_name`. Impostare questi parametri sul nome e sulla subnet della rete virtuale in cui è stata abilitata la delega.

## <a name="azure-databricks"></a>Azure Databricks

__Requisiti__

* La rete virtuale deve trovarsi nella stessa sottoscrizione e area dell'area di lavoro di Azure Machine Learning.
* Se anche gli account di archiviazione di Azure per l'area di lavoro sono protetti in una rete virtuale, devono trovarsi nella stessa rete virtuale del cluster di Azure Databricks.
* Oltre alle subnet __databricks-private__ e __databricks-public__ usate da Azure Databricks, è necessaria anche la subnet __predefinita__ creata per la rete virtuale.

__Limitazioni__

__Configurazione__

Per informazioni specifiche sull'uso di Azure Databricks con una rete virtuale, vedere [Distribuire Azure Databricks nella rete virtuale di Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Macchina virtuale o cluster HDInsight

__Requisiti__

* Azure Machine Learning supporta solo macchine virtuali che eseguono Ubuntu.
* La porta SSH deve essere abilitata nella macchina virtuale o nel cluster HDInsight.

__Limitazioni__

__Configurazione__

1. Creare una macchina virtuale o un cluster HDInsight usando il portale di Azure o l'interfaccia della riga di comando di Azure e inserire il cluster in una rete virtuale di Azure. Per altre informazioni, vedere gli articoli seguenti:

    * [Creare e gestire reti virtuali di Azure per macchine virtuali Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Estendere Azure HDInsight usando Rete virtuale di Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Un NSG viene creato automaticamente per le macchine virtuali di Azure basate su Linux. Questo NSG consente l'accesso alla porta 22 da qualsiasi origine. Se si vuole limitare l'accesso alla porta SSH, è necessario consentire l'accesso da Azure Machine Learning. Per mantenere l'accesso per Azure ML, è necessario consentire l'accesso da un __servizio di origine__ con un tag del __servizio di origine__ __AzureMachineLearning__. Ad esempio, i comandi dell'interfaccia della riga di comando di Azure seguenti modificano la regola SSH per consentire l'accesso solo da Azure Machine Learning.

    ```azurecli
    # Get default SSH rule
    nsgrule=$(az network nsg rule list --resource-group myResourceGroup --nsg-name myNetworkSecurityGroup --query [0].name -o tsv)
    # Update network security group rule to limit SSH to source service.
    az network nsg rule update --resource-group myResourceGroup --nsg-name myNetworkSecurityGroupBackEnd \
    --name $nsgrule --protocol tcp --direction inbound --priority 100 \
    --source-address-prefix AzureMachineLearning --source-port-range '*' --destination-address-prefix '*' \
    --destination-port-range 22 --access allow
    ```

    Per altre informazioni, vedere la sezione [creare gruppi di sicurezza di rete](/azure/virtual-machines/linux/tutorial-virtual-network#create-network-security-groups) nell'articolo reti virtuali di Azure per macchine virtuali Linux.
    
    Mantenere le regole in ingresso per il gruppo di sicurezza di rete. Per altre informazioni, vedere le regole di sicurezza predefinite in [Gruppi di sicurezza](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

1. Collegare la macchina virtuale o il cluster HDInsight all'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [Configurare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md).

## <a name="next-steps"></a>Passaggi successivi

* [Usare Azure machine learning area di lavoro dietro il firewall di Azure](how-to-access-azureml-behind-firewall.md).
* [Configurare ambienti di training](how-to-set-up-training-targets.md)
* [Configurare endpoint privati](how-to-configure-private-link.md)
* [Dove eseguire la distribuzione dei modelli](how-to-deploy-and-where.md)
* [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md)
