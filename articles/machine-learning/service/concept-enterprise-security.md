---
title: Sicurezza aziendale
titleSuffix: Azure Machine Learning service
description: 'Usare in modo sicuro il servizio di Azure Machine Learning: autenticazione, autorizzazione, sicurezza di rete, la crittografia dei dati e il monitoraggio.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/10/2019
ms.openlocfilehash: e9002b96467d6fa3a5c4fb03fb20bde4e1bf87a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059348"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Sicurezza aziendale per un servizio di Azure Machine Learning

In questo articolo verranno fornite informazioni sulle funzionalità di sicurezza disponibili con il servizio di apprendimento Azure.

Quando si usa un servizio cloud, è consigliabile limitare l'accesso solo agli utenti che ne hanno bisogno. Si inizia descrivendo il modello di autenticazione e autorizzazione usato dal servizio. Si potrebbe inoltre desidera limitare l'accesso alla rete o join in modo sicuro alle risorse nella rete locale con quelli nel cloud. È anche essenziale, sia quando sono inattivi e mentre i dati vengono spostati tra i servizi di crittografia dei dati. Infine, è necessario essere in grado di monitorare il servizio e generare un log di controllo di tutte le attività.

## <a name="authentication"></a>Authentication
Multi-Factor authentication è supportata se Azure Active Directory (Azure AD) è configurato per lo stesso.
* Client registra in Azure AD e ottiene token di Azure Resource Manager.  Utenti ed entità servizio sono completamente supportate.
* Client presenta token di ad Azure Resource Manager e tutti i servizi di Azure Machine Learning
* Servizio Azure Machine Learning fornisce un token di Azure Machine Learning per le risorse di calcolo di utente. Ad esempio, calcolo di Machine Learning. Questo token viene usato dall'utente di Azure Machine Learning di calcolo per richiamare il servizio di Azure Machine Learning (limita l'ambito all'area di lavoro) dopo l'esecuzione è stata completata.

![Screenshot che illustra come funziona l'autenticazione nel servizio Azure Machine Learning](./media/enterprise-readiness/authentication.png)

### <a name="authentication-keys-for-web-service-deployment"></a>Chiavi di autenticazione per la distribuzione del servizio Web

Quando si abilita l'autenticazione per una distribuzione, si creano automaticamente le chiavi di autenticazione.

* L'autenticazione è abilitata per impostazione predefinita quando si esegue la distribuzione nel servizio Azure Kubernetes.
* L'autenticazione è disabilitata per impostazione predefinita quando si esegue la distribuzione in Istanze di Azure Container.

Per controllare l'autenticazione, usare il parametro `auth_enabled` quando si crea o si aggiorna una distribuzione.

Se è abilitata l'autenticazione, è possibile usare il metodo `get_keys` per recuperare una chiave di autenticazione primaria e una secondaria:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se è necessario rigenerare una chiave, usare [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)


## <a name="authorization"></a>Authorization

È possibile creare più aree di lavoro, ciascuna delle quali può essere condivisa da più utenti. Quando si condivide un'area di lavoro, è possibile controllarne l'accesso assegnando agli utenti i ruoli seguenti:
* Proprietario
* Collaboratore
* Reader
    
Nella tabella seguente sono elencate alcune delle operazioni del servizio Azure Machine Learning principali e i ruoli che possono eseguirli:

| Operazione di servizio di Azure Machine Learning | Proprietario | Collaboratore | Reader |
| ---- |:----:|:----:|:----:|
| Crea area di lavoro | ✓ | ✓ | |
| Area di lavoro di condivisione | ✓ | |  |
| Creazione di calcolo | ✓ | ✓ | |
| Collegare calcolo | ✓ | ✓ | |
| Collegare gli archivi dati | ✓ | ✓ | |
| Eseguire un esperimento | ✓ | ✓ | |
| Vista esecuzioni e la metrica | ✓ | ✓ | ✓ |
| Registrare il modello | ✓ | ✓ | |
| Crea immagine | ✓ | ✓ | |
| Distribuire il servizio Web | ✓ | ✓ | |
| Vista modelli/immagini | ✓ | ✓ | ✓ |
| Chiamare il servizio web | ✓ | ✓ | ✓ |

Se i ruoli predefiniti non sono sufficienti per le proprie esigenze, è anche possibile creare ruoli personalizzati. I ruoli personalizzati solo che Supportiamo sono per le operazioni su area di lavoro e calcolo di Machine Learning. I ruoli personalizzati possono avere leggere, scrivere o eliminare le autorizzazioni per l'area di lavoro e la risorsa di calcolo nell'area di lavoro. È possibile rendere disponibile il ruolo a un livello specifico dell'area di lavoro, il livello di un gruppo di risorse specifico o un livello di abbonamento specifico. Per altre informazioni, vedere [gestire utenti e ruoli in un'area di lavoro di Azure Machine Learning](how-to-assign-roles.md)

### <a name="securing-compute-and-data"></a>Protezione di dati e calcolo
I proprietari e collaboratori possono usare tutte calcolo destinazioni e gli archivi dati collegati all'area di lavoro.  
Ogni area di lavoro dispone anche di un assegnato dal sistema gestito identità associato (con lo stesso nome dell'area di lavoro) con le autorizzazioni seguenti per le risorse collegate utilizzate nell'area di lavoro:

Per altre informazioni sulle identità gestita, vedere [gestite le identità per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

| Resource | Autorizzazioni |
| ----- | ----- |
| Area di lavoro | Collaboratore | 
| Account di archiviazione | Collaboratore ai dati dei BLOB di archiviazione | 
| Key Vault | Accesso a tutte le chiavi, segreti, certificati | 
| Registro Azure Container | Collaboratore | 
| Gruppo di risorse che contiene l'area di lavoro | Collaboratore | 
| Gruppo di risorse che contiene l'insieme di credenziali chiave (se diverso da quello contenente l'area di lavoro) | Collaboratore | 

È consigliabile che gli amministratori non viene revocato l'accesso dell'identità gestita per le risorse indicate sopra. È possibile ripristinare l'accesso con l'operazione di risincronizzazione delle chiavi.

Servizio Azure Machine Learning crea un'applicazione aggiuntiva (nome inizia con `aml-`) con l'accesso a livello di collaboratore nella sottoscrizione per ogni area di lavoro. Per es. Se si dispone di un'altra area di lavoro nell'area Europa settentrionale nella stessa sottoscrizione e un'area di lavoro negli Stati Uniti orientali noterai due tipi di applicazioni. Ciò è necessario in modo che Azure Machine Learning servizio consentono di gestire le risorse di calcolo.


## <a name="network-security"></a>Sicurezza di rete

Il servizio Azure Machine Learning si basa su altri servizi di Azure per le risorse di calcolo. Le risorse di calcolo (destinazioni di calcolo) vengono usate per eseguire il training e la distribuzione dei modelli. Queste destinazioni di calcolo possono essere create in una rete virtuale. È ad esempio possibile usare un ambiente Microsoft Data Science Virtual Machine per eseguire il training di un modello e quindi distribuire il modello nel servizio Azure Kubernetes.  

Per altre informazioni, vedere [come eseguire gli esperimenti e l'inferenza in una rete virtuale](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Crittografia dei dati

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi
#### <a name="azure-blob-storage"></a>Archivio BLOB di Azure
Servizio Azure Machine Learning vengono archiviati gli snapshot, output e i log nell'account di archiviazione Blob di Azure che è associato all'area di lavoro del servizio Azure Machine Learning e si trova nella sottoscrizione dell'utente. Tutti i dati archiviati in archiviazione Blob di Azure vengono crittografati a riposo usando chiavi Microsoft-Managed.

Per altre informazioni su come usare chiavi personalizzate per i dati archiviati in archiviazione Blob di Azure, vedere [crittografia del servizio di archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

I dati di training vengano in genere anche archiviati in archiviazione Blob di Azure in modo che sia accessibile al calcolo di training. Questa risorsa di archiviazione non viene gestita da Azure Machine Learning ma montata di calcolo come un file system remoto.

Per informazioni sulla rigenerazione delle chiavi di accesso per gli account di archiviazione di Azure usati con l'area di lavoro, vedere la [rigenerare le chiavi di accesso archiviazione](how-to-change-storage-access-key.md) articolo.

#### <a name="cosmos-db"></a>Cosmos DB
Servizio Azure Machine Learning archivia le metriche e i metadati per Cosmos DB a cui si trova in una sottoscrizione di Microsoft gestita dal servizio Azure Machine Learning. Tutti i dati archiviati in Cosmos DB vengono crittografati a riposo usando chiavi gestite da Microsoft.

#### <a name="azure-container-registry-acr"></a>Registro contenitori di Azure (ACR)
Tutte le immagini del contenitore nel Registro di sistema (ACR) vengono crittografate a riposo. Azure consente di crittografare un'immagine prima di archiviarla automaticamente e lo decrittografa in tempo reale quando il servizio di Azure Machine Learning esegue il pull dell'immagine.

#### <a name="machine-learning-compute"></a>Machine Learning di calcolo
Disco del sistema operativo per ogni nodo di calcolo verrà archiviato in archiviazione di Azure verrà crittografato usando chiavi gestite da Microsoft negli account di archiviazione del servizio di Azure Machine Learning. Questo calcolo è temporaneo e i cluster sono in genere, ridotte qualora non sono presenti esecuzioni in coda. La macchina virtuale sottostante viene effettuato il deprovisioning ed eliminato il disco del sistema operativo. Crittografia dischi di Azure non è supportata per il disco del sistema operativo.
Inoltre, ogni macchina virtuale ha un disco temporaneo locale per le operazioni del sistema operativo. Questo disco può anche facoltativamente utilizzabile per organizzare i dati di training. Questo disco non è crittografato. Per altre informazioni sul funzionamento della crittografia dei dati inattivi in Azure, vedere [crittografia di Azure Data-at-Rest](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest). 

### <a name="encryption-in-transit"></a>Crittografia in transito
Sono supportate entrambe le comunicazioni interne tra diversi microservizi di Azure Machine Learning e le comunicazioni esterne di chiamare l'endpoint di assegnazione dei punteggi tramite SSL. Tutti gli accessi di archiviazione di Azure sono anche su un canale sicuro. Per altre informazioni, vedere [servizi web proteggere Azure Machine Learning tramite SSL](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Utilizzo di Azure Key Vault
Istanza di Key Vault associato con l'area di lavoro viene usato dal servizio Azure Machine Learning per archiviare le credenziali di vari tipi:
* La stringa di connessione di account di archiviazione associato
* Password per le istanze di Azure Container Repository
* Stringhe di connessione ai dati punti vendita. 

Le password SSH e le chiavi per calcolare le destinazioni, ad esempio HDInsight HDI e macchina virtuale vengono archiviate in un insieme di credenziali chiave separata che è associato alla sottoscrizione di Microsoft. Servizio Azure Machine Learning archiviare le password o chiavi fornito dall'utente invece che genera l'errore, autorizza e archivia il proprio le chiavi SSH per connettersi alla macchina virtuale/HDInsight per eseguire gli esperimenti. Ogni area di lavoro ha un'associato assegnato dal sistema gestito identità (con lo stesso nome dell'area di lavoro) che può accedere a tutte le chiavi, segreti e certificati in Key Vault.

 
## <a name="monitoring"></a>Monitoraggio

Gli utenti possono visualizzare il log attività nell'area di lavoro per visualizzare varie operazioni eseguite nell'area di lavoro e ottenere le informazioni di base, ad esempio il nome dell'operazione, l'evento avviato da, timestamp e così via.

Lo screenshot seguente mostra il log attività per un'area di lavoro:

![Log di attività di schermata che mostra in un'area di lavoro](./media/enterprise-readiness/workspace-activity-log.png)


Dettagli della richiesta di assegnazione dei punteggi vengono archiviate in Application Insights, nel quale viene creato nella sottoscrizione dell'utente durante la creazione l'area di lavoro. Questa categoria include campi quali HTTPMethod UserAgent, ComputeType, RequestUrl, StatusCode, RequestId, durata e così via.


## <a name="data-flow-diagram"></a>Diagramma di flusso di dati

### <a name="create-workspace"></a>Crea area di lavoro
Il diagramma seguente illustra il flusso di lavoro di creazione dell'area di lavoro.
Utente effettua l'accesso AD Azure da qualsiasi client del servizio Azure Machine Learning supportati (portale di Azure CLI, SDK, Python) e richiede il token di Azure Resource Manager appropriato.  Utente chiama quindi Azure Resource Manager per creare l'area di lavoro.  Provider di risorse per l'area di lavoro di eseguire il provisioning del servizio Azure Resource Manager contatti di Azure Machine Learning.  Risorse aggiuntive vengono create nella sottoscrizione del cliente durante la creazione dell'area di lavoro:
* Key Vault (per archiviare i segreti)
* Un account di archiviazione di Azure (ad esempio Blob e condivisione file)
* Registro contenitori di Azure (per archiviare le immagini docker per inferenza/di assegnazione dei punteggi e la sperimentazione)
* Application Insights (per archiviare i dati di telemetria)

Altre risorse di calcolo collegati a un'area di lavoro (servizio Kubernetes di Azure, VM e così via) possono anche eseguire il provisioning da parte dei clienti in base alle esigenze. 

![Screenshot di Crea flusso di lavoro dell'area di lavoro](./media/enterprise-readiness/create-workspace.png)

### <a name="save-source-code-training-scripts"></a>Salvare il codice sorgente (script di training)
Il diagramma seguente illustra il flusso di lavoro di codice dello snapshot.
Associato a un Azure Machine Learning dell'area di lavoro del servizio sono directory (esperimenti), che contiene il codice sorgente (script di training).  Questi vengono archiviati nel computer locale del cliente e nel cloud (in un archivio Blob di Azure nella sottoscrizione del cliente). Questi snapshot di codice vengono utilizzati per l'esecuzione o l'ispezione per il controllo cronologici.

![Screenshot di Crea flusso di lavoro dell'area di lavoro](./media/enterprise-readiness/code-snapshot.png)

### <a name="training"></a>Formazione
Il diagramma seguente illustra il flusso di lavoro di training.
* Servizio Azure Machine Learning viene chiamato con l'ID dello snapshot per lo snapshot di codice salvato in precedenza
* Azure Machine Learning Crea servizio di esecuzione (facoltativo) ID e token di servizio di Azure Machine Learning, che viene successivamente utilizzato per le destinazioni di calcolo, ad esempio Machine Learning calcolo/macchina virtuale per comunicare nuovamente con il servizio di Azure Machine Learning
* È possibile scegliere un computer gestito (ad esempio Machine Learning calcolo) o di calcolo non gestito (ad esempio La macchina virtuale) per l'esecuzione dei processi di training. Viene illustrato il flusso di dati per entrambi gli scenari seguenti:
* (Macchina virtuale/HDInsight: a cui si accede usando le credenziali SSH in Azure Key Vault nella sottoscrizione di Microsoft) Servizio Azure Machine Learning esegue codice di gestione sulla destinazione di calcolo che:
    1.  Prepara l'ambiente (Nota: Docker è anche un'opzione per la macchina virtuale o locale. Vedere i passaggi per calcolo di Machine Learning riportato di seguito comprendere l'esperimento come in esecuzione sul funzionamento del contenitore docker)
    2.  Scarica il codice
    3.  Consente di impostare le variabili di ambiente/configs
    4.  Viene eseguito lo script dell'utente (snapshot di codice indicato in precedenza)
* (Machine Learning calcolo – accedere usando identità gestite da parte dell'area di lavoro) Si noti che poiché calcolo di Machine Learning è un calcolo gestito, ovvero è gestita da Microsoft, di conseguenza viene eseguito con la sottoscrizione di Microsoft.
    1.  Costruzione di Docker remota viene avviata se necessario
    2.  Scrive codice per la gestione utente Condivisione file di Azure
    3.  Il contenitore viene avviato con iniziale codice di gestione nel passaggio precedente, ovvero di comando


#### <a name="querying-runs--metrics"></a>L'esecuzione di query viene eseguito e metriche
Questo passaggio è illustrato nel flusso in cui corsi di formazione calcolo scrive i *eseguire le metriche* al servizio Azure Machine Learning da in cui venga archiviato in Cosmos DB. I client possono chiamare il servizio Azure Machine Learning che a sua volta eseguire il pull delle metriche da Cosmos DB e restituirlo al client.

![Screenshot di Crea flusso di lavoro dell'area di lavoro](./media/enterprise-readiness/training-and-metrics.png)

### <a name="creating-web-services"></a>Creazione di servizi web
Il diagramma seguente illustra il flusso di lavoro di inferenza. Inferenza o modello di punteggio, è la fase in cui viene usato il modello distribuito per la stima, in genere sui dati di produzione.
Vedere i dettagli di seguito:
* Utente si registra un modello usando un client, come Azure Machine Learning SDK
* Utente Crea immagine con modello di punteggio file e altre dipendenze di modello
* L'immagine Docker viene creata e archiviata in Registro contenitori di AZURE
* Servizio Web viene distribuito usando l'immagine creata in precedenza nella destinazione di calcolo (ACI/AKS)
* Dettagli della richiesta di assegnazione dei punteggi vengono archiviati in Application Insights, nel quale si trova nella sottoscrizione dell'utente
* Anche push di dati di telemetria a Microsoft o sottoscrizione di Azure

![Screenshot di Crea flusso di lavoro dell'area di lavoro](./media/enterprise-readiness/inferencing.png)

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i servizi Web di Azure Machine Learning con SSL](how-to-secure-web-service.md)
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Come eseguire previsioni in batch](how-to-run-batch-predictions.md)
* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
* [SDK del servizio Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Usare il servizio Azure Machine Learning con le reti virtuali di Azure](how-to-enable-virtual-network.md)
* [Best practices for building recommendation systems](https://github.com/Microsoft/Recommenders) (Procedure consigliate per creare sistemi di raccomandazione)
* [Creare un'API per raccomandazioni in tempo reale in Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
