---
title: Sicurezza aziendale
titleSuffix: Azure Machine Learning
description: 'Usare Azure Machine Learning in modo sicuro: autenticazione, autorizzazione, sicurezza di rete, crittografia dei dati e monitoraggio.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/19/2020
ms.openlocfilehash: 5afa6b9127317fcd1a683651be86cdfe078cfcd6
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259445"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Sicurezza aziendale per Azure Machine Learning

In questo articolo vengono illustrate le funzionalità di sicurezza disponibili per Azure Machine Learning.

Quando si usa un servizio cloud, è consigliabile limitare l'accesso solo agli utenti che ne hanno bisogno. Per iniziare, è necessario comprendere i modelli di autenticazione e autorizzazione usati dal servizio. È anche possibile limitare l'accesso alla rete o unire in modo sicuro le risorse nella rete locale con il cloud. Anche la crittografia dei dati è fondamentale, sia quando sono inattivi che durante lo spostamento tra i servizi. Infine, è necessario poter monitorare il servizio e produrre un log di controllo di tutte le attività.

> [!NOTE]
> Le informazioni contenute in questo articolo sono destinate a Python SDK per Azure Machine Learning versione 1.0.83.1 o successiva.

## <a name="authentication"></a>Authentication

L'autenticazione a più fattori è supportata se Azure Active Directory (Azure AD) è configurato per usarla. Ecco illustrato il processo di autenticazione:

1. Il client accede ad Azure AD e ottiene un token di Azure Resource Manager.  Gli utenti e le entità servizio sono completamente supportati.
1. Il client presenta il token ad Azure Resource Manager e ad Azure Machine Learning.
1. Il servizio Machine Learning offre un token di servizio Machine Learning alla destinazione di calcolo dell'utente, ad esempio all'ambiente di calcolo di Machine Learning. Il token viene usato dalla destinazione di calcolo dell'utente per richiamare il servizio Machine Learning al termine dell'esecuzione. L'ambito è limitato all'area di lavoro.

[![Autenticazione in Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

Per altre informazioni, vedere [Configurare l'autenticazione per le risorse e i flussi di lavoro di Azure Machine Learning](how-to-setup-authentication.md). Questo articolo contiene informazioni ed esempi sull'autenticazione, tra cui l'uso di entità servizio e flussi di lavoro automatizzati.

### <a name="authentication-for-web-service-deployment"></a>Autenticazione per la distribuzione del servizio Web

Azure Machine Learning supporta due forme di autenticazione per i servizi Web: chiave e token. Ogni servizio Web può abilitare solo una forma di autenticazione alla volta.

|Metodo di autenticazione|Descrizione|Istanze di Azure Container|Servizio Azure Kubernetes|
|---|---|---|---|
|Chiave|Le chiavi sono statiche e non è necessario aggiornarle. Possono essere rigenerate manualmente.|Disattivata per impostazione predefinita| Abilitata per impostazione predefinita|
|token|I token scadono dopo un periodo di tempo specificato ed è necessario aggiornarli.| Non disponibile| Disattivato per impostazione predefinita |

Per esempi di codice, vedere la [sezione relativa all'autenticazione del servizio Web](how-to-setup-authentication.md#web-service-authentication).

## <a name="authorization"></a>Autorizzazione

È possibile creare più aree di lavoro, ciascuna delle quali può essere condivisa da più utenti. Quando si condivide un'area di lavoro, è possibile controllarne l'accesso assegnando agli utenti questi ruoli:

* Proprietario
* Collaboratore
* Reader

La tabella seguente elenca alcune delle principali operazioni di Azure Machine Learning e i ruoli che possono eseguirle:

| Operazioni di Azure Machine Learning | Proprietario | Collaboratore | Reader |
| ---- |:----:|:----:|:----:|
| Creare un'area di lavoro | ✓ | ✓ | |
| Condividere l'area di lavoro | ✓ | |  |
| Aggiornare l'area di lavoro all'edizione Enterprise | ✓ | |
| Creare una destinazione di calcolo | ✓ | ✓ | |
| Collegare la destinazione di calcolo | ✓ | ✓ | |
| Collegare gli archivi dati | ✓ | ✓ | |
| Eseguire esperimento | ✓ | ✓ | |
| Visualizzare esecuzioni/metriche | ✓ | ✓ | ✓ |
| Registrare il modello | ✓ | ✓ | |
| Creare un'immagine | ✓ | ✓ | |
| Distribuire il servizio Web | ✓ | ✓ | |
| Visualizzare modelli o immagini | ✓ | ✓ | ✓ |
| Chiamare un servizio Web | ✓ | ✓ | ✓ |

Se i ruoli predefiniti non soddisfano le esigenze personali, è possibile creare ruoli personalizzati. I ruoli personalizzati sono supportati solo per le operazioni nell'area di lavoro e nell'ambiente di calcolo di Machine Learning. I ruoli personalizzati possono avere autorizzazioni di lettura, scrittura o eliminazione nell'area di lavoro e nella risorsa di calcolo in tale area di lavoro. È possibile rendere il ruolo disponibile per un livello di area di lavoro specifico, per un livello di gruppo di risorse specifico o per un livello di sottoscrizione specifico. Per altre informazioni, vedere [Gestire gli utenti e i ruoli in un'area di lavoro di Azure Machine Learning](how-to-assign-roles.md).

> [!WARNING]
> Azure Machine Learning è supportata con la collaborazione Business to Business di Azure Machine Learning, ma attualmente non è supportata con la collaborazione Business to Consumer di Azure Active Directory.

### <a name="securing-compute-targets-and-data"></a>Protezione dei dati e delle destinazioni di calcolo

I proprietari e i collaboratori possono usare tutte le destinazioni di calcolo e gli archivi dati collegati all'area di lavoro.  

A ogni area di lavoro è associata anche un'identità gestita assegnata dal sistema con lo stesso nome dell'area di lavoro. L'identità gestita ha le autorizzazioni seguenti per le risorse collegate usate nell'area di lavoro.

Per altre informazioni sulle identità gestite, vedere [Identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

| Risorsa | Autorizzazioni |
| ----- | ----- |
| Area di lavoro | Collaboratore |
| Account di archiviazione | Collaboratore ai dati del BLOB di archiviazione |
| Insieme di credenziali delle chiavi | Accesso a tutte le chiavi, i segreti e i certificati |
| Registro Azure Container | Collaboratore |
| Gruppo di risorse che contiene l'area di lavoro | Collaboratore |
| Gruppo di risorse che contiene l'insieme di credenziali delle chiavi se diverso da quello che contiene l'area di lavoro. | Collaboratore |

Non è consigliabile che gli amministratori revochino l'accesso dell'identità gestita alle risorse indicate nella tabella precedente. È possibile ripristinare l'accesso usando l'operazione di risincronizzazione delle chiavi.

Azure Machine Learning crea un'applicazione aggiuntiva, il cui nome inizia con `aml-` o `Microsoft-AzureML-Support-App-`, con accesso a livello di collaboratore nella sottoscrizione per ogni area geografica dell'area di lavoro. Se, ad esempio, si dispone di un'area di lavoro negli Stati Uniti orientali e una in Europa settentrionale nella stessa sottoscrizione, verranno visualizzate due applicazioni. Queste applicazioni consentono all'utente di gestire le risorse di calcolo con Azure Machine Learning.

## <a name="network-security"></a>Sicurezza di rete

Azure Machine Learning si basa su altri servizi di Azure per le risorse di calcolo. Le risorse di calcolo (destinazioni di calcolo) vengono usate per eseguire il training e la distribuzione dei modelli. Queste destinazioni di calcolo possono essere create in una rete virtuale. È ad esempio possibile usare una Data Science Virtual Machine di Azure per eseguire il training di un modello e distribuirlo nel servizio Azure Kubernetes.  

Per altre informazioni, vedere [Come eseguire in modo sicuro gli esperimenti e l'inferenza in una rete virtuale isolata](how-to-enable-virtual-network.md).

È anche possibile abilitare il collegamento privato di Azure per l'area di lavoro. Il collegamento privato consente di limitare le comunicazioni all'area di lavoro da una rete virtuale di Azure. Per altre informazioni, vedere [Come configurare il collegamento privato](how-to-configure-private-link.md).

## <a name="data-encryption"></a>Crittografia dei dati

> [!IMPORTANT]
> Per la crittografia di livello produzione durante il __Training__, Microsoft consiglia di usare Azure Machine Learning cluster di elaborazione. Per la crittografia di livello produzione durante l' __inferenza__, Microsoft consiglia di usare il servizio Azure Kubernetes.
>
> Azure Machine Learning istanza di calcolo è un ambiente di sviluppo/test. Quando si usa, è consigliabile archiviare i file, ad esempio notebook e script, in una condivisione file. I dati devono essere archiviati in un archivio dati.

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi

> [!IMPORTANT]
> Se l'area di lavoro contiene dati sensibili, è consigliabile impostare il [flag hbi_workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) durante la sua creazione. 

Il `hbi_workspace` flag controlla la quantità di dati raccolti da Microsoft per scopi diagnostici e Abilita la crittografia aggiuntiva negli ambienti gestiti da Microsoft. Consente inoltre di abilitare le azioni seguenti:

* Avvia la crittografia del disco scratch locale nel cluster di calcolo Azure Machine Learning purché non siano stati creati cluster precedenti nella sottoscrizione. Altrimenti, è necessario inoltrare un ticket di supporto per abilitare la crittografia del disco temporaneo dei cluster di elaborazione 
* Pulisce il disco temporaneo locale tra le esecuzioni
* Passa in modo sicuro le credenziali per l'account di archiviazione, il registro contenitori e l'account SSH dal livello di esecuzione ai cluster di calcolo usando l'insieme di credenziali delle chiavi
* Abilita il filtro IP per assicurarsi che i pool di batch sottostanti non possano essere chiamati da servizi esterni diversi da AzureMachineLearningService


Per altre informazioni sul funzionamento della crittografia dei dati inattivi in Azure, vedere [Crittografia dei dati inattivi di Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

#### <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure

Azure Machine Learning archivia snapshot, output e log nell'account di archiviazione BLOB di Azure collegato all'area di lavoro di Azure Machine Learning e alla sottoscrizione. Tutti i dati archiviati nell'Archiviazione BLOB di Azure vengono crittografati quando inattivi con le chiavi gestite da Microsoft.

Per informazioni su come usare le proprie chiavi per i dati archiviati nell'Archiviazione BLOB di Azure, vedere [Crittografia di Archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault](../storage/common/storage-encryption-keys-portal.md).

Anche i dati di training vengono in genere archiviati nell'Archiviazione BLOB di Azure in modo che siano accessibili alle destinazioni di calcolo per il training. Questa risorsa di archiviazione non è gestita da Azure Machine Learning ma è montata sulle destinazioni di calcolo come file system remoto.

Se è necessario __ruotare o revocare__ la chiave, è possibile farlo in qualsiasi momento. Durante la rotazione di una chiave, l'account di archiviazione inizierà a usare la nuova chiave, ovvero la versione più recente, per crittografare i dati inattivi. Durante la revoca, ovvero la disabilitazione, di una chiave l'account di archiviazione si occupa delle richieste non riuscite. Gli effetti della rotazione o della revoca vengono in genere visualizzati dopo un'ora.

Per informazioni sulla rigenerazione delle chiavi di accesso, vedere [Rigenerare le chiavi di accesso di archiviazione](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning archivia le metriche e i metadati in un'istanza di Azure Cosmos DB. Questa istanza è associata a una sottoscrizione Microsoft gestita da Azure Machine Learning. Tutti i dati archiviati in Azure Cosmos DB vengono crittografati quando inattivi con le chiavi gestite da Microsoft.

Per usare le chiavi personali, ovvero gestite dal cliente, per crittografare l'istanza di Azure Cosmos DB, è possibile creare un'istanza di Cosmos DB dedicata da usare con l'area di lavoro. Questo approccio è consigliato se si desidera archiviare i dati, ad esempio le informazioni sulla cronologia di esecuzione, all'esterno dell'istanza di Cosmos DB multi-tenant ospitata nella sottoscrizione Microsoft. 

Per abilitare il provisioning di un'istanza di Cosmos DB nella sottoscrizione con le chiavi gestite dal cliente, eseguire le operazioni seguenti:

* Registrare i provider di risorse Microsoft. MachineLearning e Microsoft.DocumentDB nella sottoscrizione, se non è già stato fatto.

* Autorizzare l'app Machine Learning nella gestione delle identità e degli accessi con le autorizzazioni di collaboratore per la sottoscrizione.

    ![Autorizzare l'"app Azure Machine Learning" nella gestione delle identità e degli accessi nel portale](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* Usare i parametri seguenti quando si crea l'area di lavoro Azure Machine Learning. Entrambi i parametri sono obbligatori e supportati in SDK, interfaccia della riga di comando, API REST e nei modelli di Resource Manager.

    * `resource_cmk_uri`: questo parametro è l'URI completo della risorsa della chiave gestita dal cliente nell'insieme di credenziali delle chiavi, che include le [informazioni sulla versione per la chiave](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: questo parametro è l'ID della risorsa dell'insieme di credenziali delle chiavi nella sottoscrizione. L'insieme di credenziali delle chiavi deve trovarsi nella stessa area e nella stessa sottoscrizione che verrà usata per l'area di lavoro di Azure Machine Learning. 
    
        > [!NOTE]
        > Questa istanza dell'insieme di credenziali delle chiavi può essere diversa dall'insieme di credenziali delle chiavi creato da Azure Machine Learning durante il provisioning dell'area di lavoro. Se si desidera usare la stessa istanza dell'insieme di credenziali delle chiavi per l'area di lavoro, passare lo stesso insieme di credenziali delle chiavi durante il provisioning dell'area di lavoro usando il [parametro key_vault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

L'istanza di Cosmos DB viene creata in un gruppo di risorse gestito da Microsoft nella sottoscrizione. Al gruppo di risorse gestito viene assegnato un nome nel formato `<AML Workspace Resource Group Name><GUID>`.

> [!IMPORTANT]
> * Se è necessario eliminare questa istanza di Cosmos DB, si deve eliminare l'area di lavoro di Azure Machine Learning che la usa. 
> * Le [__Unità richiesta__](../cosmos-db/request-units.md) per l'account Cosmos DB sono impostate su __8000__. La modifica di questo valore non è supportata. 

Se è necessario __ruotare o revocare__ la chiave, è possibile farlo in qualsiasi momento. Durante la rotazione di una chiave, Cosmos DB inizierà a usare la nuova chiave, ovvero la versione più recente, per crittografare i dati inattivi. Durante la revoca,ovvero la disabilitazione di una chiave, Cosmos DB si occupa delle richieste non riuscite. Gli effetti della rotazione o della revoca vengono in genere visualizzati dopo un'ora.

Per altre informazioni sulle chiavi gestite dal cliente con Cosmos DB, vedere [Configurare le chiavi gestite dal cliente per l'account Azure Cosmos](../cosmos-db/how-to-setup-cmk.md).

#### <a name="azure-container-registry"></a>Registro Azure Container

Tutte le immagini del contenitore presenti nel registro, ovvero nel Registro Azure Container, vengono crittografate quando inattive. Azure crittografa automaticamente un'immagine prima di archiviarla e la decrittografa quando Azure Machine Learning esegue il pull dell'immagine.

Per usare chiavi personali, ovvero gestite dal cliente, per crittografare il Registro Azure Container, è necessario creare un Registro Azure Container personalizzato e collegarlo durante il provisioning dell'area di lavoro oppure crittografare l'istanza predefinita che viene creata al momento del provisioning dell'area di lavoro.

> [!IMPORTANT]
> Azure Machine Learning richiede l'abilitazione dell'account amministratore nella Container Registry di Azure. Per impostazione predefinita, questa impostazione è disabilitata quando si crea un registro contenitori. Per informazioni sull'abilitazione dell'account amministratore, vedere [account amministratore](/azure/container-registry/container-registry-authentication#admin-account).
>
> Dopo aver creato un'istanza di Registro Azure Container per un'area di lavoro, non eliminarla. In caso contrario, l'area di lavoro di Azure Machine Learning verrà terminata.

Per un esempio sulla creazione di un'area di lavoro con un Registro Azure Container esistente, vedere gli articoli seguenti:

* [Creare un'area di lavoro per Azure Machine Learning con l'interfaccia della riga di comando di Azure](how-to-manage-workspace-cli.md).
* [Usare un modello di Azure Resource Manager per creare un'area di lavoro per Azure Machine Learning](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Istanza di contenitore di Azure

È possibile crittografare una risorsa dell'istanza di contenitore di Azure distribuita usando chiavi gestite dal cliente. La chiave gestita dal cliente usata per l'istanza di contenitore di Azure può essere archiviata in Azure Key Vault per l'area di lavoro. Per informazioni sulla generazione di una chiave, vedere [Crittografare i dati con una chiave gestita dal cliente](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Per usare la chiave durante la distribuzione di un modello in un'istanza di contenitore di Azure, creare una nuova configurazione di distribuzione usando `AciWebservice.deploy_configuration()`. Specificare le informazioni della chiave usando i parametri seguenti:

* `cmk_vault_base_url`: URL dell'insieme di credenziali delle chiavi contenente la chiave.
* `cmk_key_name`: Nome della chiave.
* `cmk_key_version`: Versione della chiave.

Per altre informazioni sulla creazione e l'uso di una configurazione di distribuzione, vedere gli articoli seguenti:

* Riferimento di [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Dove e come eseguire la distribuzione](how-to-deploy-and-where.md)
* [Distribuire un modello a Istanze di Azure Container](how-to-deploy-azure-container-instance.md)

Per altre informazioni sull'uso della chiave gestita dal cliente con l'istanza di contenitore di Azure, vedere [Crittografare i dati con una chiave gestita dal cliente](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

#### <a name="azure-kubernetes-service"></a>Servizio Azure Kubernetes

È possibile crittografare una risorsa del servizio Azure Kubernetes usando le chiavi gestite dal cliente in qualsiasi momento. Per altre informazioni, vedere [Usare le chiavi personali con il servizio Azure Kubernetes](../aks/azure-disk-customer-managed-keys.md). 

Questo processo consente di crittografare sia i dati che il disco del sistema operativo delle macchine virtuali distribuite nel cluster Kubernetes.

> [!IMPORTANT]
> Questo processo funziona solo con il servizio Azure Kubernetes K8s versione 1.17 o successiva. Il 13 gennaio 2020 Azure Machine Learning ha aggiunto il supporto per il servizio Azure Kubernetes 1.17.

#### <a name="machine-learning-compute"></a>Ambiente di calcolo di Machine Learning

Il disco del sistema operativo per ogni nodo di calcolo archiviato in Archiviazione di Azure viene crittografato con le chiavi gestite da Microsoft negli account di archiviazione di Azure Machine Learning. Questa destinazione di calcolo è temporanea e i cluster vengono in genere ridimensionati quando non vengono accodate le esecuzioni. Viene eseguito il deprovisioning della macchina virtuale sottostante e il disco del sistema operativo viene eliminato. Crittografia dischi di Azure non è supportata per il disco del sistema operativo.

Ogni macchina virtuale ha anche un disco temporaneo locale per le operazioni del sistema operativo. Se lo si desidera, è possibile usare il disco per organizzare i dati di training. Il disco è crittografato per impostazione predefinita per le aree di lavoro in cui il parametro `hbi_workspace` è impostato su `TRUE`. Questo ambiente dura solo per il tempo di esecuzione e il supporto della crittografia è limitato solo alle chiavi gestite dal sistema.

#### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks può essere usato nelle pipeline di Azure Machine Learning. Per impostazione predefinita, il file system di Databricks (DBFS) usato da Azure Databricks viene crittografato con una chiave gestita da Microsoft. Per configurare Azure Databricks per l'uso delle chiavi gestite dal cliente, vedere [Configurare chiavi gestite dal cliente in DBFS predefinito (radice)](/azure/databricks/security/customer-managed-keys-dbfs).

### <a name="encryption-in-transit"></a>Crittografia in transito

Azure Machine Learning usa TLS per proteggere le comunicazioni interne tra diversi microservizi di Azure Machine Learning. Tutti gli accessi di Archiviazione di Azure avvengono inoltre su un canale sicuro.

Per proteggere le chiamate esterne effettuate all'endpoint di assegnazione dei punteggi, Azure Machine Learning utilizza TLS. Per altre informazioni, vedere [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Uso di Azure Key Vault

Azure Machine Learning usa l'istanza di Azure Key Vault associata all'area di lavoro per archiviare credenziali di diversi tipi:

* La stringa di connessione all'account di archiviazione associato
* Le password per le istanze del repository del contenitore di Azure
* Le stringhe di connessione agli archivi dati

Le password e le chiavi SSH per le destinazioni di calcolo come Azure HDInsight e le macchine virtuali vengono archiviate in un insieme di credenziali delle chiavi separato associato alla sottoscrizione Microsoft. Azure Machine Learning non archivia le password o le chiavi specificate dagli utenti. Al contrario, genera, autorizza e archivia le proprie chiavi SSH per connettersi alle macchine virtuali e a HDInsight per eseguire gli esperimenti.

A ogni area di lavoro è associata un'identità gestita assegnata dal sistema con lo stesso nome dell'area di lavoro. Questa identità gestita ha accesso a tutte le chiavi, i segreti e i certificati nell'insieme di credenziali delle chiavi.

## <a name="data-collection-and-handling"></a>Raccolta e gestione dei dati

### <a name="microsoft-collected-data"></a>Dati raccolti da Microsoft

Microsoft può raccogliere informazioni di identificazione non relative all'utente, come i nomi delle risorse, ad esempio il nome del set di dati o il nome dell'esperimento di Machine Learning, o le variabili di ambiente del processo per scopi diagnostici. Tutti questi dati vengono archiviati usando le chiavi gestite da Microsoft nell'archiviazione ospitata in sottoscrizioni di proprietà di Microsoft e seguono l'[informativa sulla privacy standard e gli standard di gestione dei dati di Microsoft](https://privacy.microsoft.com/privacystatement).

Microsoft consiglia anche di non archiviare informazioni sensibili, quali i segreti della chiave dell'account, nelle variabili di ambiente. Le variabili di ambiente vengono registrate, crittografate e archiviate da Microsoft. Analogamente, quando si assegna un nome a [run_id](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py), evitare di inserire informazioni sensibili, quali nomi utente o nomi di progetti segreti. Queste informazioni potrebbero essere visualizzate nei log di telemetria accessibili ai tecnici del supporto tecnico Microsoft.

È possibile rifiutare esplicitamente la raccolta dei dati di diagnostica impostando il parametro `hbi_workspace` su `TRUE` durante il provisioning dell'area di lavoro. Questa funzionalità è supportata quando si usa Python SDK di Azure ML, l'interfaccia della riga di comando, le API REST o i modelli di Azure Resource Manager.

### <a name="microsoft-generated-data"></a>Dati generati da Microsoft

Quando si usano servizi quali Machine Learning automatizzato, Microsoft può generare dati temporanei pre-elaborati per il training di più modelli. Questi dati vengono archiviati in un archivio dati dell'area di lavoro, che consente di applicare i controlli di accesso e la crittografia in modo appropriato.

Potrebbe anche essere necessario crittografare le [informazioni di diagnostica registrate nell'endpoint distribuito](how-to-enable-app-insights.md) nell'istanza di Azure Application Insights.

## <a name="monitoring"></a>Monitoraggio

### <a name="metrics"></a>Metriche

È possibile usare le metriche di Monitoraggio di Azure per visualizzare e monitorare le metriche per l'area di lavoro Azure Machine Learning. Nel [portale di Azure](https://portal.azure.com) selezionare l'area di lavoro e quindi **Metrica**:

[![Screenshot che illustra le metriche di esempio per un'area di lavoro](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

Le metriche includono informazioni su esecuzioni, distribuzioni e registrazioni.

Per altre informazioni, vedere [Metriche in Monitoraggio di Azure](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Log attività

È possibile visualizzare il log attività di un'area di lavoro per visualizzare varie operazioni eseguite nell'area di lavoro. Il log include informazioni di base quali il nome dell'operazione, l'iniziatore dell'evento e il timestamp.

Questo screenshot mostra il log attività di un'area di lavoro:

[![Screenshot che mostra il log attività di un'area di lavoro](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

I dettagli della richiesta di punteggio vengono archiviati in Application Insights. Quando si crea un'area di lavoro, Application Insights viene creato nella sottoscrizione. Le informazioni registrate includono campi come:

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* StatusCode
* RequestId
* Duration

> [!IMPORTANT]
> Alcune azioni nell'area di lavoro Azure Machine Learning non registrano le informazioni nel log attività. Ad esempio, l'avvio di un'esecuzione del training e la registrazione di un modello non vengono registrate.
>
> Alcune di queste azioni vengono visualizzate nell'area **Attività** dell'area di lavoro, ma queste notifiche non specificano chi ha avviato l'attività.

## <a name="data-flow-diagrams"></a>Diagrammi di flusso dei dati

### <a name="create-workspace"></a>Creare un'area di lavoro

Il diagramma seguente mostra il flusso di lavoro di creazione dell'area di lavoro.

* L'utente accede ad Azure AD da uno dei client di Azure Machine Learning supportati, quali l'interfaccia della riga di comando di Azure, Python SDK, il portale di Azure, e richiede il token di Azure Resource Manager adeguato.
* Chiama Azure Resource Manager per creare l'area di lavoro. 
* Azure Resource Manager contatta il provider di risorse di Azure Machine Learning per eseguire il provisioning dell'area di lavoro.

Durante la creazione dell'area di lavoro, nella sottoscrizione dell'utente vengono create anche altre risorse:

* Un insieme di credenziali delle chiavi per l'archiviazione dei segreti
* Un account di archiviazione di Azure che include un BLOB e la condivisione file
* Registro Azure Container per archiviare le immagini Docker per l'inferenza o il punteggio e la sperimentazione
* Application Insights per archiviare la telemetria

L'utente può anche eseguire il provisioning di altre destinazioni di calcolo collegate a un'area di lavoro, ad esempio il servizio Azure Kubernetes o le macchine virtuali, in base alle esigenze.

[![Flusso di lavoro di creazione di un'area di lavoro](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Salvare il codice sorgente (script di training)

Il diagramma seguente mostra il flusso di lavoro dello snapshot del codice.

All'area di lavoro Azure Machine Learning sono associate le directory (esperimenti) che contengono il codice sorgente (script di training). Questi script vengono archiviati nel computer locale e nel cloud, ovvero in Archiviazione BLOB di Azure per la sottoscrizione. Gli snapshot del codice vengono usati per l'esecuzione o l'ispezione per il controllo cronologico.

[![Flusso di lavoro dello snapshot del codice](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>Formazione

Il diagramma seguente mostra il flusso di lavoro di training.

* Azure Machine Learning viene chiamato con l'ID dello snapshot del codice salvato nella sezione precedente.
* Azure Machine Learning crea un ID di esecuzione (facoltativo) e un token del servizio Machine Learning, che verrà usato in seguito dalle destinazioni di calcolo come ambiente di calcolo di Machine Learning o delle macchine virtuali per comunicare con il servizio Machine Learning.
* È possibile scegliere una destinazione di calcolo gestita, ad esempio l'ambiente di calcolo di Machine Learning, o una destinazione di calcolo non gestita, ad esempio le macchine virtuali, per eseguire i processi di training. Ecco illustrati i flussi di dati per entrambi gli scenari:
   * Macchine virtuali o HDInsight, a cui si accede tramite credenziali SSH in un insieme di credenziali delle chiavi nella sottoscrizione Microsoft. Azure Machine Learning esegue il codice di gestione nella destinazione di calcolo che:

   1. Prepara l'ambiente. Docker è un'opzione per le macchine virtuali e i computer locali. Vedere la procedura seguente relativa all'ambiente di calcolo di Machine Learning per informazioni sull'esecuzione di esperimenti nei contenitori Docker.
   1. Scarica il codice.
   1. Configura le variabili di ambiente e imposta le configurazioni.
   1. Esegue gli script utente, ovvero lo snapshot del codice indicato nella sezione precedente.

   * Ambiente di calcolo di Machine Learning, a cui si accede tramite un'identità gestita dall'area di lavoro.
Poiché l'ambiente di calcolo di Machine Learning è una destinazione di calcolo gestita, ovvero gestita da Microsoft, viene eseguita nella sottoscrizione Microsoft.

   1. Se necessario, viene avviata la costruzione di Docker remoto.
   1. Il codice di gestione viene scritto nella condivisione di File di Azure dell'utente.
   1. Il contenitore viene avviato con un comando iniziale, ovvero il codice di gestione come descritto nel passaggio precedente.

#### <a name="querying-runs-and-metrics"></a>Query relative a esecuzioni e metriche

Nel diagramma di flusso seguente, questo passaggio si verifica quando la destinazione di calcolo di training riscrive le metriche di esecuzione in Azure Machine Learning dalla risorsa di archiviazione nel database di Cosmos DB. I client possono chiamare Azure Machine Learning. Machine Learning effettuerà a sua volta il pull delle metriche dal database di Cosmos DB e le restituirà al client.

[![Flusso di lavoro di training](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>Creazione di servizi Web

Il diagramma seguente mostra il flusso di lavoro di inferenza. L'inferenza o il punteggio del modello è la fase in cui il modello distribuito viene usato per la stima, più comunemente sui dati di produzione.

Di seguito sono riportati i dettagli:

* L'utente registra un modello usando un client come Azure Machine Learning SDK.
* L'utente crea un'immagine usando un modello, un file di punteggio e altre dipendenze del modello.
* L'immagine Docker viene creata e archiviata in Registro Azure Container.
* Il servizio Web viene distribuito nella destinazione di calcolo, ovvero in Istanze di Container o nel servizio Azure Kubernetes, usando l'immagine creata nel passaggio precedente.
* I dettagli della richiesta di punteggio vengono archiviati in Application Insights, che si trova nella sottoscrizione dell'utente.
* Viene anche effettuato il push della telemetria nella sottoscrizione di Microsoft o Azure.

[![Flusso di lavoro di inferenza](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i servizi Web di Azure Machine Learning con TLS](how-to-secure-web-service.md)
* [Come usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Usare Azure Machine Learning con Firewall di Azure](how-to-access-azureml-behind-firewall.md)
* [Usare Azure Machine Learning con Rete virtuale di Azure](how-to-enable-virtual-network.md)
* [Best practices for building recommendation systems](https://github.com/Microsoft/Recommenders) (Procedure consigliate per creare sistemi di raccomandazione)
* [Creare un'API per raccomandazioni in tempo reale in Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
