---
title: Sicurezza aziendale
titleSuffix: Azure Machine Learning
description: 'Usare in modo sicuro Azure Machine Learning: autenticazione, autorizzazione, sicurezza di rete, crittografia dei dati e monitoraggio.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/13/2020
ms.openlocfilehash: 9f3a1c3455aadfbd243cdc6ab2920849c8558841
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414636"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Sicurezza aziendale per Azure Machine Learning

In this article, you'll learn about security features available for Azure Machine Learning.

Quando si usa un servizio cloud, è consigliabile limitare l'accesso solo agli utenti che ne hanno bisogno. Iniziare comprendendo il modello di autenticazione e autorizzazione utilizzato dal servizio. È anche possibile limitare l'accesso alla rete o aggiungere in modo sicuro le risorse nella rete locale con il cloud. Anche la crittografia dei dati è fondamentale, sia inattivi che durante lo spostamento dei dati tra i servizi. Infine, è necessario essere in grado di monitorare il servizio e produrre un log di controllo di tutte le attività.

> [!NOTE]
> Le informazioni contenute in questo articolo funzionano con Azure Machine Learning Python SDK versione 1.0.83.1 o successiva.

## <a name="authentication"></a>Authentication

L'autenticazione a più fattori è supportata se Azure Active Directory (Azure AD) è configurato per usarla. Di seguito è riportato il processo di autenticazione:Here's the authentication process:

1. Il client accede ad Azure AD e ottiene un token di Azure Resource Manager.The client signs in to Azure AD and gets an Azure Resource Manager token.  Gli utenti e le entità servizio sono completamente supportati.
1. The client presents the token to Azure Resource Manager and to all Azure Machine Learning.
1. The Machine Learning service provides a Machine Learning service token to the user compute target (for example, Machine Learning Compute). Questo token viene usato dalla destinazione di calcolo dell'utente per richiamare il servizio Machine Learning al termine dell'esecuzione. L'ambito è limitato all'area di lavoro.

[![Autenticazione in Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

Per altre informazioni, vedere [Configurare l'autenticazione per](how-to-setup-authentication.md)le risorse e i flussi di lavoro di Azure Machine Learning. In questo articolo vengono fornite informazioni ed esempi sull'autenticazione, incluso l'utilizzo di entità servizio e flussi di lavoro automatizzati.

### <a name="authentication-for-web-service-deployment"></a>Autenticazione per la distribuzione del servizio Web

Azure Machine Learning supporta due forme di autenticazione per i servizi Web: chiave e token. Ogni servizio Web può abilitare una sola forma di autenticazione alla volta.

|Metodo di autenticazione|Descrizione|Istanze di Azure Container|Servizio Azure Kubernetes|
|---|---|---|---|
|Chiave|Le chiavi sono statiche e non devono essere aggiornate. Le chiavi possono essere rigenerate manualmente.|Disattivata per impostazione predefinita| Abilitato per impostazione predefinita|
|token|I token scadono dopo un periodo di tempo specificato e devono essere aggiornati.| Non disponibile| Disattivata per impostazione predefinita |

Per esempi di codice, vedere la [sezione Autenticazione del servizio Web](how-to-setup-authentication.md#web-service-authentication).

## <a name="authorization"></a>Autorizzazione

È possibile creare più aree di lavoro, ciascuna delle quali può essere condivisa da più utenti. Quando si condivide un'area di lavoro, è possibile controllarne l'accesso assegnando questi ruoli agli utenti:When you share a workspace, you can control access to it by assigning these roles to users:

* Proprietario
* Collaboratore
* Reader

Nella tabella seguente sono elencate alcune delle principali operazioni di Azure Machine Learning e i ruoli che possono eseguirle:

| Operazione di Azure Machine LearningAzure Machine Learning operation | Proprietario | Collaboratore | Reader |
| ---- |:----:|:----:|:----:|
| Creare un'area di lavoro | ✓ | ✓ | |
| Condividere l'area di lavoro | ✓ | |  |
| Aggiornare l'area di lavoro all'edizione EnterpriseUpgrade workspace to Enterprise edition | ✓ | |
| Creare una destinazione di calcoloCreate compute target | ✓ | ✓ | |
| Collegare l'obiettivo di calcolo | ✓ | ✓ | |
| Allegare archivi dati | ✓ | ✓ | |
| Eseguire esperimento | ✓ | ✓ | |
| Visualizzare le esecuzioni/metriche | ✓ | ✓ | ✓ |
| Registrare il modello | ✓ | ✓ | |
| Creare un'immagine | ✓ | ✓ | |
| Distribuire il servizio Web | ✓ | ✓ | |
| Visualizza modelli/immagini | ✓ | ✓ | ✓ |
| Chiamare il servizio web | ✓ | ✓ | ✓ |

Se i ruoli predefiniti non soddisfano le proprie esigenze, è possibile creare ruoli personalizzati. I ruoli personalizzati sono supportati solo per le operazioni nell'area di lavoro e nel calcolo di Machine Learning.Custom roles are supported only for operations on the workspace and Machine Learning Compute. I ruoli personalizzati possono disporre di autorizzazioni di lettura, scrittura o eliminazione nell'area di lavoro e nella risorsa di calcolo in tale area di lavoro. È possibile rendere il ruolo disponibile a un livello specifico dell'area di lavoro, a un gruppo di risorse specifico o a un livello di sottoscrizione specifico. Per altre informazioni, vedere [Gestire utenti e ruoli in un'area](how-to-assign-roles.md)di lavoro di Azure Machine Learning.For more information, see Manage users and roles in an Azure Machine Learning workspace.

> [!WARNING]
> Azure Machine Learning non è attualmente supportato con la collaborazione business-to-business di Azure Active Directory.Azure Machine Learning is not currently supported with Azure Active Directory business-to-business collaboration.

### <a name="securing-compute-targets-and-data"></a>Protezione degli obiettivi di calcolo e dei dati

I proprietari e i collaboratori possono usare tutte le destinazioni di calcolo e gli archivi dati collegati all'area di lavoro.  

A ogni area di lavoro è inoltre associata un'identità gestita assegnata dal sistema con lo stesso nome dell'area di lavoro. L'identità gestita dispone delle autorizzazioni seguenti per le risorse collegate utilizzate nell'area di lavoro.

Per altre informazioni sulle identità gestite, vedere Identità gestite per le risorse di Azure.For more information on managed [identities, see Managed identities for Azure resources.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

| Risorsa | Autorizzazioni |
| ----- | ----- |
| Area di lavoro | Collaboratore |
| Account di archiviazione | Collaboratore ai dati del BLOB di archiviazione |
| Insieme di credenziali delle chiavi | Accesso a tutte le chiavi, segreti, certificati |
| Registro Azure Container | Collaboratore |
| Gruppo di risorse che contiene l'area di lavoro | Collaboratore |
| Gruppo di risorse che contiene l'insieme di credenziali delle chiavi (se diverso da quello che contiene l'area di lavoro) | Collaboratore |

Non è consigliabile che gli amministratori revochino l'accesso dell'identità gestita alle risorse indicate nella tabella precedente. È possibile ripristinare l'accesso utilizzando l'operazione di risincronizzazione delle chiavi.

Azure Machine Learning crea un'applicazione `aml-` aggiuntiva (il nome inizia con o `Microsoft-AzureML-Support-App-`) con l'accesso a livello di collaboratore nella sottoscrizione per ogni area di lavoro. Ad esempio, se si dispone di un'area di lavoro negli Stati Uniti orientali e una in Nord Europa nella stessa sottoscrizione, verranno visualizzate due di queste applicazioni. Queste applicazioni consentono ad Azure Machine Learning di gestire le risorse di calcolo.

## <a name="network-security"></a>Sicurezza di rete

Azure Machine Learning si basa su altri servizi di Azure per le risorse di calcolo. Le risorse di calcolo (destinazioni di calcolo) vengono usate per eseguire il training e la distribuzione dei modelli. È possibile creare queste destinazioni di calcolo in una rete virtuale. Ad esempio, è possibile usare Macchina virtuale di analisi scientifica dei dati di Azure per eseguire il training di un modello e quindi distribuire il modello in AKS.  

Per altre informazioni, vedere [Come eseguire esperimenti e inferenza in una rete virtuale.](how-to-enable-virtual-network.md)

È anche possibile abilitare Collegamento privato di Azure per l'area di lavoro. Private Link allows you to restrict communications to your workspace from an Azure Virtual Network. Per ulteriori informazioni, vedere [Come configurare Private Link](how-to-configure-private-link.md).

> [!TIP]
> È possibile combinare la rete virtuale e Private Link per proteggere le comunicazioni tra l'area di lavoro e altre risorse di Azure.You can combine virtual network and Private Link together to protect communication between your workspace and other Azure resources. Tuttavia, alcune combinazioni richiedono un'area di lavoro Enterprise edition. Utilizzare la tabella seguente per comprendere quali scenari richiedono l'edizione Enterprise:
>
> | Scenario | Enterprise</br>edition | Basic</br>edition |
> | ----- |:-----:|:-----:| 
> | Nessuna rete virtuale o collegamento privato | ✔ | ✔ |
> | Area di lavoro senza Private Link. Altre risorse (ad eccezione del Registro di sistema del contenitore di Azure) in una rete virtualeOther resources (except Azure Container Registry) in a virtual network | ✔ | ✔ |
> | Area di lavoro senza Private Link. Altre risorse con Private Link | ✔ | |
> | Area di lavoro con Private Link. Altre risorse (ad eccezione del Registro di sistema del contenitore di Azure) in una rete virtualeOther resources (except Azure Container Registry) in a virtual network | ✔ | ✔ |
> | Area di lavoro e qualsiasi altra risorsa con Private Link | ✔ | |
> | Area di lavoro con Private Link. Altre risorse senza Private Link o rete virtuale | ✔ | ✔ |
> | Registro di sistema del contenitore di Azure in una rete virtualeAzure Container Registry in a virtual network | ✔ | |
> | Chiavi gestite dal cliente per l'area di lavoro | ✔ | |
> 

> [!WARNING]
> L'anteprima delle istanze di calcolo di Azure Machine Learning non è supportata in un'area di lavoro in cui è abilitato Private Link.Azure Machine Learning compute instances preview is not supported in a workspace where Private Link is enabled.
> 
> Azure Machine Learning non supporta l'uso di un servizio Azure Kubernetes con collegamento privato abilitato. È invece possibile usare il servizio Azure Kubernetes in una rete virtuale. Per altre informazioni, vedere Proteggere i processi di [sperimentazione e inferenza di Azure ML all'interno di](how-to-enable-virtual-network.md)una rete virtuale di Azure.For more information, see Secure Azure ML experimentation and inference jobs within an Azure Virtual Network .

## <a name="data-encryption"></a>Crittografia dei dati

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi

> [!IMPORTANT]
> Se l'area di lavoro contiene dati sensibili, è consigliabile impostare il [flag hbi_workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) durante la creazione dell'area di lavoro. 

Il `hbi_workspace` flag controlla la quantità di dati raccolti da Microsoft per scopi diagnostici e abilita la crittografia aggiuntiva negli ambienti gestiti Microsoft. Inoltre, consente quanto segue:

* Avvia la crittografia del disco di memoria virtuale locale nel cluster Amlcompute a condizione che non siano stati creati cluster precedenti in tale sottoscrizione. In caso contrario, è necessario generare un ticket di supporto per abilitare la crittografia del disco di memoria virtuale dei cluster di calcolo 
* Pulisce il disco di memoria virtuale locale tra una esecuzione e l'altro
* Passa in modo sicuro le credenziali per l'account di archiviazione, il registro contenitori e l'account SSH dal livello di esecuzione ai cluster di calcolo usando l'insieme di credenziali delle chiaviSecurely passes credentials for your storage account, container registry and SSH account from the execution layer to your compute clusters using your key vault
* Abilita il filtro IP per garantire che i pool batch sottostanti non possano essere chiamati da servizi esterni diversi da AzureMachineLearningService


Per altre informazioni sul funzionamento della crittografia inattivi in Azure, vedere Crittografia dei dati di [Azure inattivi.](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

#### <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure

Azure Machine Learning archivia snapshot, output e log nell'account di archiviazione BLOB di Azure collegato all'area di lavoro di Azure Machine Learning e alla sottoscrizione. Tutti i dati archiviati nell'archiviazione BLOB di Azure vengono crittografati inattivi con le chiavi gestite da Microsoft.All the data stored in Azure Blob storage is encrypted at restwith with Microsoft-managed keys.

Per informazioni su come usare le proprie chiavi per i dati archiviati nell'archiviazione BLOB di Azure, vedere Crittografia di Archiviazione di Azure con chiavi gestite dal cliente in Archiviazione delle chiavi di Azure.For information on how to use your own keys for data stored in Azure Blob storage, see [Azure Storage encryption with customer-managed keys in Azure Key Vault](../storage/common/storage-encryption-keys-portal.md).

I dati di training vengono in genere archiviati anche nell'archiviazione BLOB di Azure in modo che siano accessibili alle destinazioni di elaborazione del training. Questa risorsa di archiviazione non è gestita da Azure Machine Learning ma è montata per calcolare le destinazioni come file system remoto.

Se hai bisogno di __ruotare o revocare__ la chiave, puoi farlo in qualsiasi momento. Quando si ruota una chiave, l'account di archiviazione inizierà a usare la nuova chiave (versione più recente) per crittografare i dati inattivi. Quando si revoca (disabilita) una chiave, l'account di archiviazione si occupa delle richieste non riuscite. Di solito ci vuole un'ora perché la rotazione o la revoca siano efficaci.

Per informazioni sulla rigenerazione delle chiavi di accesso, vedere [Rigenerare le chiavi](how-to-change-storage-access-key.md)di accesso di archiviazione .

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning archivia metriche e metadati in un'istanza di database Cosmos di Azure.Azure Machine Learning stores metrics and metadata in an Azure Cosmos DB instance. Questa istanza è associata a una sottoscrizione Microsoft gestita da Azure Machine Learning.This instance is associated with a Microsoft subscription managed by Azure Machine Learning. Tutti i dati archiviati in Azure Cosmos DB vengono crittografati inattivi con le chiavi gestite da Microsoft.All the data stored in Azure Cosmos DB is encrypted at rest with Microsoft-managed keys.

Per usare le proprie chiavi (gestite dal cliente) per crittografare l'istanza di Azure Cosmos DB, è possibile creare un'istanza Cosmos DB dedicata da usare con l'area di lavoro. È consigliabile usare questo approccio se si desidera archiviare i dati, ad esempio le informazioni sulla cronologia delle esecuzione, all'esterno dell'istanza Cosmos DB multi-tenant ospitata nella sottoscrizione Microsoft.We recommend this approach if you want to store your data, such as run history information, outside of the multi-tenant Cosmos DB instance hosted in our Microsoft subscription. 

Per abilitare il provisioning di un'istanza Cosmos DB nella sottoscrizione con chiavi gestite dal cliente, eseguire le azioni seguenti:To enable provisioning a Cosmos DB instance in your subscription with customer-managed keys, perform the following actions:

* Abilitare le funzionalità chiave gestite dal cliente per Cosmos DB. A questo punto, è necessario richiedere l'accesso per utilizzare questa funzionalità. Per farlo, si [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com)prega di contattare .

* Registrare i provider di risorse di Azure Machine Learning e Azure Cosmos DB nella sottoscrizione, se non è già stato fatto.

* Autorizzare l'app Machine Learning (in Gestione identità e accessi) con le autorizzazioni di collaboratore per la sottoscrizione.

    ![Autorizzare l'app 'Azure Machine Learning' in Gestione di identità e accessi nel portaleAuthorize the 'Azure Machine Learning App' in Identity and Access Management in the portal](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* Usare i parametri seguenti durante la creazione dell'area di lavoro di Azure Machine Learning.Use the following parameters when creating the Azure Machine Learning workspace. Entrambi i parametri sono obbligatori e supportati nei modelli SDK, CLI, REST E Resource Manager.

    * `resource_cmk_uri`: questo parametro è l'URI completo della risorsa della chiave gestita dal cliente nell'insieme di credenziali delle chiavi, incluse le [informazioni sulla versione per la chiave](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: questo parametro è l'ID risorsa dell'insieme di credenziali delle chiavi nella sottoscrizione. Questo insieme di credenziali delle chiavi deve trovarsi nella stessa area geografica e sottoscrizione che verrà usata per l'area di lavoro di Azure Machine Learning.This key vault needs to be in the same region and subscription that you will use for the Azure Machine Learning workspace. 
    
        > [!NOTE]
        > Questa istanza dell'insieme di credenziali delle chiavi può essere diversa dall'insieme di credenziali delle chiavi creato da Azure Machine Learning quando si esegue il provisioning dell'area di lavoro. Se si desidera utilizzare la stessa istanza dell'insieme di credenziali delle chiavi per l'area di lavoro, passare lo stesso insieme di credenziali delle chiavi durante il provisioning dell'area di lavoro utilizzando il [parametro key_vault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

Questa istanza Cosmos DB viene creata in un gruppo di risorse gestito da Microsoft nella sottoscrizione. 

> [!IMPORTANT]
> * Se è necessario eliminare questa istanza cosmo DB, è necessario eliminare l'area di lavoro di Azure Machine Learning che la usa. 
> * L'impostazione predefinita [__unità__](../cosmos-db/request-units.md) richiesta per questo account Cosmos DB è impostata su __8000__. La modifica di questo valore non è supportata. 

Se hai bisogno di __ruotare o revocare__ la chiave, puoi farlo in qualsiasi momento. Quando si ruota una chiave, Cosmos DB inizierà a utilizzare la nuova chiave (ultima versione) per crittografare i dati inattivi. Quando si revoca (disabilita) una chiave, Cosmos DB si occupa delle richieste non riuscite. Di solito ci vuole un'ora perché la rotazione o la revoca siano efficaci.

Per altre informazioni sulle chiavi gestite dal cliente con Cosmos DB, vedere [Configurare le chiavi gestite dal cliente per l'account database Cosmos di Azure.](../cosmos-db/how-to-setup-cmk.md)

#### <a name="azure-container-registry"></a>Registro Azure Container

Tutte le immagini del contenitore nel Registro di sistema (Registro di sistema del contenitore di Azure) vengono crittografate inattivi. Azure crittografa automaticamente un'immagine prima di archiviarla e la decrittografa quando Azure Machine Learning estrae l'immagine.

Per usare le chiavi personalizzate (gestite dal cliente) per crittografare il Registro di sistema del contenitore di Azure, è necessario creare un aCR personalizzato e collegarlo durante il provisioning dell'area di lavoro o crittografare l'istanza predefinita creata al momento del provisioning dell'area di lavoro.

Per un esempio di creazione di un'area di lavoro usando un Registro contenitori di Azure esistente, vedere gli articoli seguenti:For an example of creating a workspace using an existing Azure Container Registry, see the following articles:

* [Creare un'area di lavoro per Azure Machine Learning con l'interfaccia della riga di comando di Azure.Create](how-to-manage-workspace-cli.md)a workspace for Azure Machine Learning with Azure CLI .
* [Usare un modello di Azure Resource Manager per creare un'area di lavoro per Azure Machine LearningUse an Azure Resource Manager template to create a workspace for Azure Machine Learning](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Istanza di contenitore di Azure

È possibile crittografare una risorsa istanza contenitore di Azure distribuita usando chiavi gestite dal cliente. La chiave gestita dal cliente usata per ACI può essere archiviata nell'insieme di credenziali delle chiavi di Azure per l'area di lavoro. Per informazioni sulla generazione di una chiave, vedere [Crittografare i dati con una chiave gestita dal cliente](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Per usare la chiave quando si distribuisce un modello nell'istanza del contenitore di Azure, creare una nuova configurazione di distribuzione usando `AciWebservice.deploy_configuration()`. Fornire le informazioni chiave utilizzando i seguenti parametri:

* `cmk_vault_base_url`: URL dell'insieme di credenziali delle chiavi che contiene la chiave.
* `cmk_key_name`: il nome della chiave.
* `cmk_key_version`: la versione della chiave.

Per altre informazioni sulla creazione e l'uso di una configurazione di distribuzione, vedere gli articoli seguenti:For more information on creating and using a deployment configuration, see the following articles:

* [AciWebservice.deploy_configuration() di](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) riferimento
* [Dove e come eseguire la distribuzione](how-to-deploy-and-where.md)
* [Distribuire un modello a Istanze di Azure Container](how-to-deploy-azure-container-instance.md)

Per ulteriori informazioni sull'utilizzo di una chiave gestita dal cliente con ACI, vedere [Crittografare i dati con una chiave gestita dal cliente](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

#### <a name="azure-kubernetes-service"></a>Servizio Azure Kubernetes

È possibile crittografare una risorsa del servizio Azure Kubernetes distribuita usando le chiavi gestite dal cliente in qualsiasi momento. Per altre informazioni, vedere Portare le proprie chiavi con il [servizio Azure Kubernetes.](../aks/azure-disk-customer-managed-keys.md) 

Questo processo consente di crittografare sia i dati che il disco del sistema operativo delle macchine virtuali distribuite nel cluster Kubernetes.

> [!IMPORTANT]
> Questo processo funziona solo con AKS K8s versione 1.17 o successiva. Azure Machine Learning ha aggiunto il supporto per AKS 1.17 il 13 gennaio 2020.Azure Machine Learning added support for AKS 1.17 on Jan Jan 13, 2020.

#### <a name="machine-learning-compute"></a>Calcolo dell'apprendimento automatico

Il disco del sistema operativo per ogni nodo di calcolo archiviato in Archiviazione di Azure viene crittografato con chiavi gestite da Microsoft negli account di archiviazione di Azure Machine Learning.The OS disk for each compute node stored in Azure Storage is encrypted with Microsoft-managed keys in Azure Machine Learning storage accounts. Questa destinazione di calcolo è effimera e i cluster vengono in genere ridimensionati quando non vengono accodate esecuzioni. Viene eseguito il deprovisioning della macchina virtuale sottostante e viene eliminato il disco del sistema operativo. Crittografia disco di Azure non è supportata per il disco del sistema operativo.

Ogni macchina virtuale dispone anche di un disco temporaneo locale per le operazioni del sistema operativo. Se si desidera, è possibile usare il disco per gestire temporaneamente i dati di training. Il disco viene crittografato per impostazione predefinita per le aree di lavoro con il `hbi_workspace` parametro impostato su `TRUE`. Questo ambiente è di breve durata solo per la durata dell'esecuzione e il supporto della crittografia è limitato solo alle chiavi gestite dal sistema.

#### <a name="azure-databricks"></a>Azure Databricks

I databrick di Azure possono essere usati nelle pipeline di Azure Machine Learning.Azure Databricks can be used in Azure Machine Learning pipelines. Per impostazione predefinita, il file system DBFS (Databricks) usato da Azure Databricks viene crittografato usando una chiave gestita da Microsoft.By default, the Databricks File System (DBFS) used by Azure Databricks is encrypted using a Microsoft-managed key. Per configurare Azure Databricks per l'uso di chiavi gestite dal cliente, vedere [Configure customer-managed keys on default (root) DBFS](/azure/databricks/security/customer-managed-keys-dbfs).

### <a name="encryption-in-transit"></a>Crittografia in transito

Azure Machine Learning usa TLS per proteggere le comunicazioni interne tra vari microservizi di Azure Machine Learning.Azure Machine Learning uses TLS to secure internal communication between various Azure Machine Learning microservices. Tutto l'accesso ad Archiviazione di Azure avviene anche tramite un canale sicuro.

Per proteggere le chiamate esterne all'endpoint di punteggio, Azure Machine Learning usa TLS. Per altre informazioni, vedere [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning.For](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service)more information, see Use TLS to secure a web service through Azure Machine Learning .

### <a name="using-azure-key-vault"></a>Uso di Azure Key Vault

Azure Machine Learning usa l'istanza dell'insieme di credenziali delle chiavi di Azure associata all'area di lavoro per archiviare credenziali di vario tipo:Azure Machine Learning uses the Azure Key Vault instance associated with the workspace to store credentials of various kinds:

* Stringa di connessione dell'account di archiviazione associata
* Password per le istanze di Azure Container Repository
* Stringhe di connessione agli archivi datiConnection strings to data stores

Le password e le chiavi SSH per calcolare destinazioni come Azure HDInsight e le macchine virtuali vengono archiviate in un insieme di credenziali delle chiavi separato associato alla sottoscrizione Microsoft.SSH passwords and keys to compute targets like Azure HDInsight and VMs are stored in a separate key vault that's associated with the Microsoft subscription. Azure Machine Learning non archivia password o chiavi fornite dagli utenti. Al contrario, genera, autorizza e archivia le proprie chiavi SSH per connettersi alle macchine virtuali e HDInsight per eseguire gli esperimenti.

A ogni area di lavoro è associata un'identità gestita assegnata dal sistema con lo stesso nome dell'area di lavoro. Questa identità gestita ha accesso a tutte le chiavi, segreti e certificati nell'insieme di credenziali delle chiavi.

## <a name="data-collection-and-handling"></a>Raccolta e gestione dei dati

### <a name="microsoft-collected-data"></a>Dati raccolti da Microsoft

Microsoft può raccogliere informazioni di identificazione non utente, ad esempio i nomi delle risorse (ad esempio il nome del set di dati o il nome dell'esperimento di Machine Learning) o le variabili di ambiente del processo per scopi diagnostici. Tutti questi dati vengono archiviati utilizzando le chiavi gestite da Microsoft nell'archiviazione ospitata in abbonamenti di proprietà Microsoft e rispettano la politica sulla privacy standard di Microsoft e gli standard di [gestione dei dati.](https://privacy.microsoft.com/privacystatement)

Microsoft consiglia inoltre di non archiviare informazioni riservate, ad esempio i segreti della chiave dell'account, nelle variabili di ambiente. Le variabili di ambiente vengono registrate, crittografate e archiviate da noi. Analogamente quando si assegna una denominazione a [runid](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py), evitare di includere informazioni riservate quali nomi utente o nomi di progetto segreti. Queste informazioni possono essere visualizzate nei log di telemetria accessibili ai tecnici del supporto tecnico Microsoft.This information may appear in telemetry logs accessible to Microsoft Support engineers.

È possibile rifiutare esplicitamente i `hbi_workspace` dati `TRUE` di diagnostica raccolti impostando il parametro su durante il provisioning dell'area di lavoro. Questa funzionalità è supportata quando si usa i modelli AzureML Python SDK, CLI, REST API o Azure Resource Manager.

### <a name="microsoft-generated-data"></a>Dati generati da Microsoft

Quando si usano servizi come Automated Machine Learning, Microsoft può generare dati temporanei pre-elaborati per il training di più modelli. Questi dati vengono archiviati in un archivio dati nell'area di lavoro, che consente di applicare i controlli di accesso e la crittografia in modo appropriato.

È anche possibile crittografare le [informazioni di diagnostica registrate dall'endpoint distribuito](how-to-enable-app-insights.md) nell'istanza di Azure Application Insights.You may also want to encrypt diagnostic information logged from your deployed endpoint into your Azure Application Insights instance.

## <a name="monitoring"></a>Monitoraggio

### <a name="metrics"></a>Metriche

È possibile usare le metriche di Monitoraggio di Azure per visualizzare e monitorare le metriche per l'area di lavoro di Azure Machine Learning.You can use Azure Monitor metrics to view and monitor metrics for your Azure Machine Learning workspace. Nel [portale di Azure](https://portal.azure.com)selezionare l'area di lavoro e quindi selezionare **Metriche:**

[![Screenshot che mostra metriche di esempio per un'area di lavoro](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

Le metriche includono informazioni su esecuzioni, distribuzioni e registrazioni.

Per altre informazioni, vedere [Metriche in Monitoraggio di Azure.For](/azure/azure-monitor/platform/data-platform-metrics)more information, see Metrics in Azure Monitor.

### <a name="activity-log"></a>Log attività

È possibile visualizzare il registro attività di un'area di lavoro per visualizzare le varie operazioni eseguite nell'area di lavoro. Il log include informazioni di base come il nome dell'operazione, l'initatore dell'evento e il timestamp.

Questa schermata mostra il log attività di un'area di lavoro:This screenshot shows the activity log of a workspace:

[![Schermata che mostra il log attività di un'area di lavoro](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

I dettagli della richiesta di punteggio vengono archiviati in Application Insights.Scoring request details are stored in Application Insights. Application Insights viene creato nella sottoscrizione quando si crea un'area di lavoro. Le informazioni registrate includono campi quali:

* Metodo HTTP
* UserAgent
* ComputeType
* RichiestaUrl
* StatusCode
* RequestId
* Duration

> [!IMPORTANT]
> Alcune azioni nell'area di lavoro di Azure Machine Learning non registrano informazioni nel log attività. Ad esempio, l'inizio di un'esecuzione di training e la registrazione di un modello non vengono registrati.
>
> Alcune di queste azioni vengono visualizzate nell'area **Attività** dell'area di lavoro, ma queste notifiche non indicano chi ha avviato l'attività.

## <a name="data-flow-diagrams"></a>Diagrammi di flusso dei dati

### <a name="create-workspace"></a>Creare un'area di lavoro

Nel diagramma seguente viene illustrato il flusso di lavoro per la creazione dell'area di lavoro.

* Accedere ad Azure AD da uno dei client di Azure Machine Learning supportati (interfaccia della riga di comando di Azure, Python SDK, portale di Azure) e richiedere il token di Azure Resource Manager appropriato.
* Chiamare Azure Resource Manager per creare l'area di lavoro. 
* Azure Resource Manager contatta il provider di risorse di Azure Machine Learning per eseguire il provisioning dell'area di lavoro.

Durante la creazione dell'area di lavoro vengono create risorse aggiuntive nella sottoscrizione dell'utente:Additional resources are created in the user's subscription during workspace creation:

* Archivio delle chiavi (per archiviare i segreti)
* Un account di archiviazione di Azure (inclusi BLOB e condivisione file)An Azure storage account (including blob and file share)
* Registro di sistema del contenitore di Azure (per archiviare le immagini Docker per l'inferenza/punteggio e la sperimentazione)Azure Container Registry (to store Docker images for inference/scoring and experimentation)
* Application Insights (per archiviare i dati di telemetria)Application Insights (to store telemetry)

L'utente può anche eseguire il provisioning di altre destinazioni di calcolo collegate a un'area di lavoro, ad esempio il servizio Azure Kubernetes o le macchine virtuali, in base alle esigenze.

[![Creare un flusso di lavoro dell'area di lavoro](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Salvare il codice sorgente (script di formazione)

Nel diagramma seguente viene illustrato il flusso di lavoro dello snapshot del codice.

Associati a un'area di lavoro di Azure Machine Learning sono directory (esperimenti) che contengono il codice sorgente (script di formazione). Questi script vengono archiviati nel computer locale e nel cloud (nell'archivio BLOB di Azure per la sottoscrizione). Gli snapshot del codice vengono utilizzati per l'esecuzione o l'ispezione per il controllo cronologico.

[![Flusso di lavoro dello snapshot del codiceCode snapshot workflow](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>Formazione

Nel diagramma seguente viene illustrato il flusso di lavoro di formazione.

* Azure Machine Learning viene chiamato con l'ID snapshot per lo snapshot del codice salvato nella sezione precedente.
* Azure Machine Learning crea un ID di esecuzione (facoltativo) e un token del servizio Machine Learning, che viene usato successivamente dalle destinazioni di calcolo come Machine Learning Compute/VMs per comunicare con il servizio Machine Learning.Azure Machine Learning creates a run ID (optional) and a Machine Learning service token, which is later used by compute targets like Machine Learning Compute/VMs to communicate with the Machine Learning service.
* È possibile scegliere una destinazione di calcolo gestita (ad esempio Machine Learning Compute) o una destinazione di calcolo non gestita (ad esempio le macchine virtuali) per eseguire processi di training. Ecco i flussi di dati per entrambi gli scenari:Here are the data flows for both scenarios:
   * Macchine virtuali/HDInsight, accessibili dalle credenziali SSH in un insieme di credenziali delle chiavi nella sottoscrizione Microsoft.VMs/HDInsight, accessed by SSH credentials in a key vault in the Microsoft subscription. Azure Machine Learning esegue il codice di gestione sulla destinazione di calcolo che:Azure Machine Learning runs management code on the compute target that:

   1. Prepara l'ambiente. (Docker è un'opzione per le macchine virtuali e i computer locali. Vedere i passaggi seguenti per Machine Learning Compute per comprendere il funzionamento degli esperimenti in esecuzione nei contenitori Docker.)
   1. Scarica il codice.
   1. Imposta le variabili di ambiente e le configurazioni.
   1. Esegue gli script utente (lo snapshot del codice menzionato nella sezione precedente).

   * Machine Learning Compute, accessibile tramite un'identità gestita dall'area di lavoro.
Poiché Machine Learning Compute è una destinazione di calcolo gestita, ovvero gestita da Microsoft, viene eseguita con la sottoscrizione Microsoft.Because Machine Learning Compute is a managed compute target (that is, it's managed by Microsoft) it runs under your Microsoft subscription.

   1. La costruzione remota di Docker è stata espulsa, se necessario.
   1. Il codice di gestione viene scritto nella condivisione File di Azure dell'utente.
   1. Il contenitore viene avviato con un comando iniziale. Ovvero, il codice di gestione come descritto nel passaggio precedente.

#### <a name="querying-runs-and-metrics"></a>Esecuzione di query e metriche

Nel diagramma di flusso seguente questo passaggio si verifica quando la destinazione di calcolo del training scrive le metriche di esecuzione in Azure Machine Learning dall'archiviazione nel database Cosmos DB. I client possono chiamare Azure Machine Learning.Clients can call Azure Machine Learning. Machine Learning a sua volta estrarrà le metriche dal database Cosmos DB e le restituirà al client.

[![Flusso di lavoro di formazione](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>Creazione di servizi Web

Nel diagramma seguente viene illustrato il flusso di lavoro di inferenza. L'inferenza, o punteggio del modello, è la fase in cui il modello distribuito viene usato per la stima, in genere sui dati di produzione.

Di seguito sono riportati i dettagli:

* L'utente registra un modello usando un client come Azure Machine Learning SDK.
* L'utente crea un'immagine utilizzando un modello, un file di partitura e altre dipendenze del modello.
* L'immagine Docker viene creata e archiviata nel Registro di sistema del contenitore di Azure.The Docker image is created and stored in Azure Container Registry.
* Il servizio Web viene distribuito nella destinazione di calcolo (istanze del contenitore/AKS) usando l'immagine creata nel passaggio precedente.
* I dettagli della richiesta di punteggio vengono archiviati in Application Insights, che si trova nella sottoscrizione dell'utente.
* La telemetria viene inviata anche alla sottoscrizione Microsoft/Azure.Telemetry is also pushed to the Microsoft/Azure subscription.

[![Flusso di lavoro di inferenza](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i servizi Web di Azure Machine Learning con TLSSecure Azure Machine Learning web services with TLS](how-to-secure-web-service.md)
* [Usare un modello di Machine Learning distribuito come servizio WebConsume a Machine Learning model deployed as a web service](how-to-consume-web-service.md)
* [Come eseguire previsioni in batch](how-to-use-parallel-run-step.md)
* [Monitorare i modelli di Azure Machine Learning con Application InsightsMonitor your Azure Machine Learning models with Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Usare Azure Machine Learning con la rete virtuale di AzureUse Azure Machine Learning with Azure Virtual Network](how-to-enable-virtual-network.md)
* [Best practices for building recommendation systems](https://github.com/Microsoft/Recommenders) (Procedure consigliate per creare sistemi di raccomandazione)
* [Creare un'API per raccomandazioni in tempo reale in Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
