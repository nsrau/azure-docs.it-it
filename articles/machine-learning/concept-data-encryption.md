---
title: Crittografia dei dati con Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come Azure Machine Learning computing e archivi dati forniscono la crittografia dei dati inattivi e in transito.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/09/2020
ms.openlocfilehash: 211ef9571b5a126686b4583330dc0f80863fd47e
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992047"
---
# <a name="data-encryption-with-azure-machine-learning"></a>Crittografia dei dati con Azure Machine Learning

Azure Machine Learning usa un'ampia gamma di servizi di archiviazione dati di Azure e risorse di calcolo durante il training di modelli e l'inferenza. Ognuno di questi ha una propria storia sul modo in cui forniscono la crittografia per i dati inattivi e in transito. In questo articolo vengono fornite informazioni su ognuna di esse e la soluzione migliore per lo scenario in uso.

> [!IMPORTANT]
> Per la crittografia di livello produzione durante il __Training__, Microsoft consiglia di usare Azure Machine Learning cluster di elaborazione. Per la crittografia di livello produzione durante l' __inferenza__, Microsoft consiglia di usare il servizio Azure Kubernetes.
>
> Azure Machine Learning istanza di calcolo è un ambiente di sviluppo/test. Quando si usa, è consigliabile archiviare i file, ad esempio notebook e script, in una condivisione file. I dati devono essere archiviati in un archivio dati.

## <a name="encryption-at-rest"></a>Crittografia di dati inattivi

> [!IMPORTANT]
> Se l'area di lavoro contiene dati sensibili, è consigliabile impostare il [flag hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) durante la sua creazione. Il `hbi_workspace` flag può essere impostato solo quando viene creata un'area di lavoro. Non può essere modificato per un'area di lavoro esistente.

Il `hbi_workspace` flag controlla la quantità di [dati raccolti da Microsoft per scopi diagnostici](#microsoft-collected-data) e Abilita la [crittografia aggiuntiva negli ambienti gestiti da Microsoft](../security/fundamentals/encryption-atrest.md). Consente inoltre di abilitare le azioni seguenti:

* Avvia la crittografia del disco scratch locale nel cluster di calcolo Azure Machine Learning purché non siano stati creati cluster precedenti nella sottoscrizione. Altrimenti, è necessario inoltrare un ticket di supporto per abilitare la crittografia del disco temporaneo dei cluster di elaborazione 
* Pulisce il disco temporaneo locale tra le esecuzioni
* Passa in modo sicuro le credenziali per l'account di archiviazione, il registro contenitori e l'account SSH dal livello di esecuzione ai cluster di calcolo usando l'insieme di credenziali delle chiavi
* Abilita il filtro IP per assicurarsi che i pool di batch sottostanti non possano essere chiamati da servizi esterni diversi da AzureMachineLearningService
* Si noti che le istanze di calcolo non sono supportate nell'area di lavoro HBI

### <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure

Azure Machine Learning archivia snapshot, output e log nell'account di archiviazione BLOB di Azure collegato all'area di lavoro di Azure Machine Learning e alla sottoscrizione. Tutti i dati archiviati nell'Archiviazione BLOB di Azure vengono crittografati quando inattivi con le chiavi gestite da Microsoft.

Per informazioni su come usare le proprie chiavi per i dati archiviati nell'Archiviazione BLOB di Azure, vedere [Crittografia di Archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md).

Anche i dati di training vengono in genere archiviati nell'Archiviazione BLOB di Azure in modo che siano accessibili alle destinazioni di calcolo per il training. Questa risorsa di archiviazione non è gestita da Azure Machine Learning ma è montata sulle destinazioni di calcolo come file system remoto.

Se è necessario __ruotare o revocare__ la chiave, è possibile farlo in qualsiasi momento. Durante la rotazione di una chiave, l'account di archiviazione inizierà a usare la nuova chiave, ovvero la versione più recente, per crittografare i dati inattivi. Durante la revoca, ovvero la disabilitazione, di una chiave l'account di archiviazione si occupa delle richieste non riuscite. Gli effetti della rotazione o della revoca vengono in genere visualizzati dopo un'ora.

Per informazioni sulla rigenerazione delle chiavi di accesso, vedere [Rigenerare le chiavi di accesso di archiviazione](how-to-change-storage-access-key.md).

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning archivia i metadati in un'istanza di Azure Cosmos DB. Questa istanza è associata a una sottoscrizione Microsoft gestita da Azure Machine Learning. Tutti i dati archiviati in Azure Cosmos DB vengono crittografati quando inattivi con le chiavi gestite da Microsoft.

Per usare le chiavi personali, ovvero gestite dal cliente, per crittografare l'istanza di Azure Cosmos DB, è possibile creare un'istanza di Cosmos DB dedicata da usare con l'area di lavoro. Questo approccio è consigliato se si desidera archiviare i dati, ad esempio le informazioni sulla cronologia di esecuzione, all'esterno dell'istanza di Cosmos DB multi-tenant ospitata nella sottoscrizione Microsoft. 

Per abilitare il provisioning di un'istanza di Cosmos DB nella sottoscrizione con le chiavi gestite dal cliente, eseguire le operazioni seguenti:

* Registrare i provider di risorse Microsoft. MachineLearning e Microsoft.DocumentDB nella sottoscrizione, se non è già stato fatto.

* Usare i parametri seguenti quando si crea l'area di lavoro Azure Machine Learning. Entrambi i parametri sono obbligatori e supportati in SDK, interfaccia della riga di comando, API REST e nei modelli di Resource Manager.

    * `resource_cmk_uri`: questo parametro è l'URI completo della risorsa della chiave gestita dal cliente nell'insieme di credenziali delle chiavi, che include le [informazioni sulla versione per la chiave](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: questo parametro è l'ID della risorsa dell'insieme di credenziali delle chiavi nella sottoscrizione. L'insieme di credenziali delle chiavi deve trovarsi nella stessa area e nella stessa sottoscrizione che verrà usata per l'area di lavoro di Azure Machine Learning. 
    
        > [!NOTE]
        > Questa istanza dell'insieme di credenziali delle chiavi può essere diversa dall'insieme di credenziali delle chiavi creato da Azure Machine Learning durante il provisioning dell'area di lavoro. Se si desidera usare la stessa istanza dell'insieme di credenziali delle chiavi per l'area di lavoro, passare lo stesso insieme di credenziali delle chiavi durante il provisioning dell'area di lavoro usando il [parametro key_vault](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Se è necessario __ruotare o revocare__ la chiave, è possibile farlo in qualsiasi momento. Durante la rotazione di una chiave, Cosmos DB inizierà a usare la nuova chiave, ovvero la versione più recente, per crittografare i dati inattivi. Durante la revoca,ovvero la disabilitazione di una chiave, Cosmos DB si occupa delle richieste non riuscite. Gli effetti della rotazione o della revoca vengono in genere visualizzati dopo un'ora.

Per altre informazioni sulle chiavi gestite dal cliente con Cosmos DB, vedere [Configurare le chiavi gestite dal cliente per l'account Azure Cosmos](../cosmos-db/how-to-setup-cmk.md).

### <a name="azure-container-registry"></a>Registro Azure Container

Tutte le immagini del contenitore presenti nel registro, ovvero nel Registro Azure Container, vengono crittografate quando inattive. Azure crittografa automaticamente un'immagine prima di archiviarla e la decrittografa quando Azure Machine Learning esegue il pull dell'immagine.

Per usare chiavi personali, ovvero gestite dal cliente, per crittografare il Registro Azure Container, è necessario creare un Registro Azure Container personalizzato e collegarlo durante il provisioning dell'area di lavoro oppure crittografare l'istanza predefinita che viene creata al momento del provisioning dell'area di lavoro.

> [!IMPORTANT]
> Azure Machine Learning richiede l'abilitazione dell'account amministratore nella Container Registry di Azure. Per impostazione predefinita, questa impostazione è disabilitata quando si crea un registro contenitori. Per informazioni sull'abilitazione dell'account amministratore, vedere [account amministratore](../container-registry/container-registry-authentication.md#admin-account).
>
> Dopo aver creato un'istanza di Registro Azure Container per un'area di lavoro, non eliminarla. In caso contrario, l'area di lavoro di Azure Machine Learning verrà terminata.

Per un esempio sulla creazione di un'area di lavoro con un Registro Azure Container esistente, vedere gli articoli seguenti:

* [Creare un'area di lavoro per Azure Machine Learning con l'interfaccia della riga di comando di Azure](how-to-manage-workspace-cli.md).
* [Creare un'area di lavoro con Python SDK](how-to-manage-workspace.md?tabs=python#create-a-workspace).
* [Usare un modello di Azure Resource Manager per creare un'area di lavoro per Azure Machine Learning](how-to-create-workspace-template.md)

### <a name="azure-container-instance"></a>Istanza di contenitore di Azure

È possibile crittografare una risorsa dell'istanza di contenitore di Azure distribuita usando chiavi gestite dal cliente. La chiave gestita dal cliente usata per l'istanza di contenitore di Azure può essere archiviata in Azure Key Vault per l'area di lavoro. Per informazioni sulla generazione di una chiave, vedere [Crittografare i dati con una chiave gestita dal cliente](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Per usare la chiave durante la distribuzione di un modello in un'istanza di contenitore di Azure, creare una nuova configurazione di distribuzione usando `AciWebservice.deploy_configuration()`. Specificare le informazioni della chiave usando i parametri seguenti:

* `cmk_vault_base_url`: URL dell'insieme di credenziali delle chiavi contenente la chiave.
* `cmk_key_name`: Nome della chiave.
* `cmk_key_version`: Versione della chiave.

Per altre informazioni sulla creazione e l'uso di una configurazione di distribuzione, vedere gli articoli seguenti:

* Riferimento di [AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Dove e come eseguire la distribuzione](how-to-deploy-and-where.md)
* [Distribuire un modello a Istanze di Azure Container](how-to-deploy-azure-container-instance.md)

Per altre informazioni sull'uso della chiave gestita dal cliente con l'istanza di contenitore di Azure, vedere [Crittografare i dati con una chiave gestita dal cliente](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

### <a name="azure-kubernetes-service"></a>Servizio Azure Kubernetes

È possibile crittografare una risorsa del servizio Azure Kubernetes usando le chiavi gestite dal cliente in qualsiasi momento. Per altre informazioni, vedere [Usare le chiavi personali con il servizio Azure Kubernetes](../aks/azure-disk-customer-managed-keys.md). 

Questo processo consente di crittografare sia i dati che il disco del sistema operativo delle macchine virtuali distribuite nel cluster Kubernetes.

> [!IMPORTANT]
> Questo processo funziona solo con il servizio Azure Kubernetes K8s versione 1.17 o successiva. Il 13 gennaio 2020 Azure Machine Learning ha aggiunto il supporto per il servizio Azure Kubernetes 1.17.

### <a name="machine-learning-compute"></a>Ambiente di calcolo di Machine Learning

Il disco del sistema operativo per ogni nodo di calcolo archiviato in Archiviazione di Azure viene crittografato con le chiavi gestite da Microsoft negli account di archiviazione di Azure Machine Learning. Questa destinazione di calcolo è temporanea e i cluster vengono in genere ridimensionati quando non vengono accodate le esecuzioni. Viene eseguito il deprovisioning della macchina virtuale sottostante e il disco del sistema operativo viene eliminato. Crittografia dischi di Azure non è supportata per il disco del sistema operativo.

Ogni macchina virtuale ha anche un disco temporaneo locale per le operazioni del sistema operativo. Se lo si desidera, è possibile usare il disco per organizzare i dati di training. Il disco è crittografato per impostazione predefinita per le aree di lavoro in cui il parametro `hbi_workspace` è impostato su `TRUE`. Questo ambiente dura solo per il tempo di esecuzione e il supporto della crittografia è limitato solo alle chiavi gestite dal sistema.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks può essere usato nelle pipeline di Azure Machine Learning. Per impostazione predefinita, il file system di Databricks (DBFS) usato da Azure Databricks viene crittografato con una chiave gestita da Microsoft. Per configurare Azure Databricks per l'uso delle chiavi gestite dal cliente, vedere [Configurare chiavi gestite dal cliente in DBFS predefinito (radice)](/azure/databricks/security/customer-managed-keys-dbfs).

### <a name="microsoft-generated-data"></a>Dati generati da Microsoft

Quando si usano servizi quali Machine Learning automatizzato, Microsoft può generare dati temporanei pre-elaborati per il training di più modelli. Questi dati vengono archiviati in un archivio dati dell'area di lavoro, che consente di applicare i controlli di accesso e la crittografia in modo appropriato.

Potrebbe anche essere necessario crittografare le [informazioni di diagnostica registrate nell'endpoint distribuito](how-to-enable-app-insights.md) nell'istanza di Azure Application Insights.

## <a name="encryption-in-transit"></a>Crittografia in transito

Azure Machine Learning usa TLS per proteggere le comunicazioni interne tra diversi microservizi di Azure Machine Learning. Tutti gli accessi di Archiviazione di Azure avvengono inoltre su un canale sicuro.

Per proteggere le chiamate esterne effettuate all'endpoint di assegnazione dei punteggi, Azure Machine Learning utilizza TLS. Per altre informazioni, vedere [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](./how-to-secure-web-service.md).

## <a name="data-collection-and-handling"></a>Raccolta e gestione dei dati

### <a name="microsoft-collected-data"></a>Dati raccolti da Microsoft

Microsoft può raccogliere informazioni di identificazione non relative all'utente, come i nomi delle risorse, ad esempio il nome del set di dati o il nome dell'esperimento di Machine Learning, o le variabili di ambiente del processo per scopi diagnostici. Tutti questi dati vengono archiviati usando le chiavi gestite da Microsoft nell'archiviazione ospitata in sottoscrizioni di proprietà di Microsoft e seguono l'[informativa sulla privacy standard e gli standard di gestione dei dati di Microsoft](https://privacy.microsoft.com/privacystatement).

Microsoft consiglia anche di non archiviare informazioni sensibili, quali i segreti della chiave dell'account, nelle variabili di ambiente. Le variabili di ambiente vengono registrate, crittografate e archiviate da Microsoft. Analogamente, quando si assegna un nome a [run_id](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py), evitare di inserire informazioni sensibili, quali nomi utente o nomi di progetti segreti. Queste informazioni potrebbero essere visualizzate nei log di telemetria accessibili ai tecnici del supporto tecnico Microsoft.

È possibile rifiutare esplicitamente la raccolta dei dati di diagnostica impostando il parametro `hbi_workspace` su `TRUE` durante il provisioning dell'area di lavoro. Questa funzionalità è supportata quando si usa Python SDK di Azure ML, l'interfaccia della riga di comando, le API REST o i modelli di Azure Resource Manager.

## <a name="using-azure-key-vault"></a>Uso di Azure Key Vault

Azure Machine Learning usa l'istanza di Azure Key Vault associata all'area di lavoro per archiviare credenziali di diversi tipi:

* La stringa di connessione all'account di archiviazione associato
* Le password per le istanze del repository del contenitore di Azure
* Le stringhe di connessione agli archivi dati

Le password e le chiavi SSH per le destinazioni di calcolo come Azure HDInsight e le macchine virtuali vengono archiviate in un insieme di credenziali delle chiavi separato associato alla sottoscrizione Microsoft. Azure Machine Learning non archivia le password o le chiavi specificate dagli utenti. Al contrario, genera, autorizza e archivia le proprie chiavi SSH per connettersi alle macchine virtuali e a HDInsight per eseguire gli esperimenti.

A ogni area di lavoro è associata un'identità gestita assegnata dal sistema con lo stesso nome dell'area di lavoro. Questa identità gestita ha accesso a tutte le chiavi, i segreti e i certificati nell'insieme di credenziali delle chiavi.

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi ad archiviazione di Azure](how-to-access-data.md)
* [Recuperare dati da un archivio dati](how-to-create-register-datasets.md)
* [Connettersi ai dati](how-to-connect-data-ui.md)
* [Eseguire il training con set di dati](how-to-train-with-datasets.md)