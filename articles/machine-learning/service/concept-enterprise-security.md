---
title: Sicurezza aziendale
titleSuffix: Azure Machine Learning service
description: 'Usare in modo sicuro il servizio Azure Machine Learning: autenticazione, autorizzazione, sicurezza di rete, crittografia dei dati e monitoraggio.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/07/2019
ms.openlocfilehash: 81e8601ac83d43bde0767e38eb387f489d76125b
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165235"
---
# <a name="enterprise-security-for-the-azure-machine-learning-service"></a>Sicurezza aziendale per il servizio Azure Machine Learning

In questo articolo verranno fornite informazioni sulle funzionalità di sicurezza disponibili per il servizio Azure Machine Learning.

Quando si usa un servizio cloud, una procedura consigliata consiste nel limitare l'accesso solo agli utenti che lo richiedono. Per iniziare, è necessario comprendere il modello di autenticazione e autorizzazione utilizzato dal servizio. Potrebbe anche essere opportuno limitare l'accesso alla rete o unire in modo sicuro le risorse nella rete locale con il cloud. La crittografia dei dati è anche fondamentale, sia inattivi che durante lo spostamento dei dati tra i servizi. Infine, è necessario essere in grado di monitorare il servizio e produrre un log di controllo di tutte le attività.

## <a name="authentication"></a>Authentication

La funzionalità autenticazione a più fattori è supportata se Azure Active Directory (Azure AD) è configurata per l'utilizzo. Ecco il processo di autenticazione:

1. Il client accede per Azure AD e ottiene un token di Azure Resource Manager.  Gli utenti e le entità servizio sono completamente supportati.
1. Il client presenta il token per Azure Resource Manager e per tutti i servizi di Azure Machine Learning.
1. Il servizio Machine Learning fornisce un token di servizio Machine Learning alla destinazione di calcolo dell'utente, ad esempio ambiente di calcolo di Machine Learning. Questo token viene usato dalla destinazione di calcolo utente per richiamare il servizio Machine Learning al termine dell'esecuzione. L'ambito è limitato all'area di lavoro.

[![Autenticazione nel servizio Azure Machine Learning](./media/enterprise-readiness/authentication.png)](./media/enterprise-readiness/authentication-expanded.png)

### <a name="authentication-for-web-service-deployment"></a>Autenticazione per la distribuzione del servizio Web

Azure Machine Learning supporta due forme di autenticazione per i servizi Web: chiave e token. Ogni servizio Web può abilitare solo una forma di autenticazione alla volta.

|Metodo di autenticazione|Istanze di Azure Container|Servizio Azure Kubernetes|
|---|---|---|
|Chiave|Disabilitato per impostazione predefinita| Abilitato per impostazione predefinita|
|Token| Non disponibile| Disabilitato per impostazione predefinita |

#### <a name="authentication-with-keys"></a>Autenticazione con chiavi

Quando si Abilita l'autenticazione della chiave per una distribuzione, si creano automaticamente le chiavi di autenticazione.

* L'autenticazione è abilitata per impostazione predefinita quando si esegue la distribuzione in Azure Kubernetes Service (AKS).
* Per impostazione predefinita, l'autenticazione è disabilitata quando si esegue la distribuzione in istanze di contenitore di Azure.

Per abilitare l'autenticazione della chiave, `auth_enabled` usare il parametro quando si crea o si aggiorna una distribuzione.

Se è abilitata l'autenticazione della chiave, è `get_keys` possibile usare il metodo per recuperare una chiave di autenticazione primaria e secondaria:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se è necessario rigenerare una chiave, usare [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

#### <a name="authentication-with-tokens"></a>Autenticazione con token

Quando si Abilita l'autenticazione basata su token per un servizio Web, gli utenti devono presentare un token Web JSON Azure Machine Learning al servizio Web per accedervi.

* Per impostazione predefinita, l'autenticazione del token è disabilitata quando si esegue la distribuzione nel servizio Azure Kubernetes.
* L'autenticazione del token non è supportata quando si esegue la distribuzione in istanze di contenitore di Azure.

Per controllare l'autenticazione del token, `token_auth_enabled` usare il parametro quando si crea o si aggiorna una distribuzione.

Se è abilitata l'autenticazione basata su token, `get_token` è possibile usare il metodo per recuperare un token JSON Web (JWT) e l'ora di scadenza del token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> È necessario richiedere un nuovo token dopo l' `refresh_by` ora del token.
>
> Si consiglia di creare l'area di lavoro Azure Machine Learning nella stessa area del cluster del servizio Azure Kubernetes. 
>
> Per eseguire l'autenticazione con un token, il servizio Web effettuerà una chiamata all'area in cui viene creata l'area di lavoro Azure Machine Learning. Se l'area dell'area di lavoro non è disponibile, non sarà possibile recuperare un token per il servizio Web, anche se il cluster si trova in un'area diversa dall'area di lavoro. Il risultato è che Autenticazione di Azure AD non è disponibile fino a quando l'area dell'area di lavoro non sarà nuovamente disponibile. 
>
> Inoltre, maggiore è la distanza tra l'area del cluster e l'area dell'area di lavoro, più tempo sarà necessario per recuperare un token.

## <a name="authorization"></a>Authorization

È possibile creare più aree di lavoro, ciascuna delle quali può essere condivisa da più utenti. Quando si condivide un'area di lavoro, è possibile controllarne l'accesso assegnando questi ruoli agli utenti:

* Proprietario
* Collaboratore
* Reader

Nella tabella seguente sono elencate alcune delle principali operazioni del servizio Azure Machine Learning e i ruoli che possono eseguirli:

| Operazione del servizio Azure Machine Learning | Proprietario | Collaboratore | Reader |
| ---- |:----:|:----:|:----:|
| Crea area di lavoro | ✓ | ✓ | |
| Condividere l'area di lavoro | ✓ | |  |
| Creare una destinazione di calcolo | ✓ | ✓ | |
| Connetti destinazione di calcolo | ✓ | ✓ | |
| Connetti archivi dati | ✓ | ✓ | |
| Eseguire esperimento | ✓ | ✓ | |
| Visualizza esecuzioni/metriche | ✓ | ✓ | ✓ |
| Registrare il modello | ✓ | ✓ | |
| Crea immagine | ✓ | ✓ | |
| Distribuire il servizio Web | ✓ | ✓ | |
| Visualizza modelli/immagini | ✓ | ✓ | ✓ |
| Chiama servizio Web | ✓ | ✓ | ✓ |

Se i ruoli predefiniti non soddisfano le proprie esigenze, è possibile creare ruoli personalizzati. I ruoli personalizzati sono supportati solo per le operazioni nell'area di lavoro e ambiente di calcolo di Machine Learning. I ruoli personalizzati possono disporre di autorizzazioni di lettura, scrittura o eliminazione per l'area di lavoro e per la risorsa di calcolo in tale area di lavoro. È possibile rendere il ruolo disponibile a livello di area di lavoro specifico, a un livello di gruppo di risorse specifico o a un livello di sottoscrizione specifico. Per altre informazioni, vedere [gestire utenti e ruoli in un'area di lavoro Azure Machine Learning](how-to-assign-roles.md).

### <a name="securing-compute-targets-and-data"></a>Protezione dei dati e delle destinazioni di calcolo

I proprietari e i collaboratori possono usare tutte le destinazioni di calcolo e gli archivi dati collegati all'area di lavoro.  

A ogni area di lavoro è associata anche un'identità gestita assegnata dal sistema con lo stesso nome dell'area di lavoro. L'identità gestita ha le autorizzazioni seguenti per le risorse associate utilizzate nell'area di lavoro.

Per altre informazioni sulle identità gestite, vedere [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

| Risorsa | Autorizzazioni |
| ----- | ----- |
| Area di lavoro | Collaboratore |
| Account di archiviazione | Collaboratore ai dati dei BLOB di archiviazione |
| Insieme di credenziali delle chiavi | Accesso a tutte le chiavi, segreti, certificati |
| Registro contenitori di Azure | Collaboratore |
| Gruppo di risorse che contiene l'area di lavoro | Collaboratore |
| Gruppo di risorse che contiene l'insieme di credenziali delle chiavi (se diverso da quello che contiene l'area di lavoro) | Collaboratore |

Non è consigliabile che gli amministratori revocano l'accesso dell'identità gestita alle risorse indicate nella tabella precedente. È possibile ripristinare l'accesso tramite l'operazione di risincronizzazione delle chiavi.

Il servizio Azure Machine Learning crea un'applicazione aggiuntiva (il nome inizia `aml-`con) con accesso a livello di collaboratore nella sottoscrizione per ogni area dell'area di lavoro. Se, ad esempio, si dispone di un'area di lavoro negli Stati Uniti orientali e di un'altra area di lavoro in Europa settentrionale nella stessa sottoscrizione, verranno visualizzate due di queste applicazioni. Queste applicazioni consentono al servizio Azure Machine Learning di semplificare la gestione delle risorse di calcolo.

## <a name="network-security"></a>Sicurezza di rete

Il servizio Azure Machine Learning si basa su altri servizi di Azure per le risorse di calcolo. Le risorse di calcolo (destinazioni di calcolo) vengono usate per eseguire il training e la distribuzione dei modelli. È possibile creare queste destinazioni di calcolo in una rete virtuale. Ad esempio, è possibile usare Data Science Virtual Machine di Azure per eseguire il training di un modello e quindi distribuire il modello in AKS.  

Per ulteriori informazioni, vedere [come eseguire esperimenti e inferenza in una rete virtuale](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Crittografia dati

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi

#### <a name="azure-blob-storage"></a>Archivio BLOB di Azure

Il servizio Azure Machine Learning archivia snapshot, output e log nell'account di archiviazione BLOB di Azure associato all'area di lavoro del servizio Azure Machine Learning e alla sottoscrizione. Tutti i dati archiviati nell'archivio BLOB di Azure vengono crittografati a riposo con le chiavi gestite da Microsoft.

Per informazioni su come usare le proprie chiavi per i dati archiviati nell'archivio BLOB di Azure, vedere [crittografia di archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

I dati di training vengono in genere archiviati anche nell'archivio BLOB di Azure in modo che sia accessibile alle destinazioni di calcolo di training. Questo spazio di archiviazione non è gestito da Azure Machine Learning ma montato per le destinazioni di calcolo come file system remoto.

Per informazioni sulla rigenerazione delle chiavi di accesso per gli account di archiviazione di Azure usati con l'area di lavoro, vedere rigenerazione delle [chiavi di accesso alle archiviazione](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Il servizio Azure Machine Learning archivia metriche e metadati nell'istanza Azure Cosmos DB associata a una sottoscrizione Microsoft gestita da Azure Machine Learning Service. Tutti i dati archiviati in Azure Cosmos DB vengono crittografati a riposo con chiavi gestite da Microsoft.

#### <a name="azure-container-registry"></a>Registro contenitori di Azure

Tutte le immagini del contenitore nel registro di sistema (Container Registry di Azure) vengono crittografate a riposo. Azure crittografa automaticamente un'immagine prima di archiviarla e la decrittografa in tempo reale quando il servizio Azure Machine Learning estrae l'immagine.

#### <a name="machine-learning-compute"></a>Ambiente di calcolo di Machine Learning

Il disco del sistema operativo per ogni nodo di calcolo archiviato in archiviazione di Azure è crittografato con le chiavi gestite da Microsoft negli account di archiviazione del servizio Azure Machine Learning. Questa destinazione di calcolo è effimera e i cluster vengono in genere ridimensionati quando nessuna esecuzione viene accodata. Viene effettuato il deprovisioning della macchina virtuale sottostante e viene eliminato il disco del sistema operativo. Crittografia dischi di Azure non è supportata per il disco del sistema operativo.

Ogni macchina virtuale dispone anche di un disco temporaneo locale per le operazioni del sistema operativo. Se lo si desidera, è possibile utilizzare il disco per organizzare i dati di training. Il disco non è crittografato.
Per altre informazioni sul funzionamento della crittografia dei dati inattivi in Azure, vedere la pagina relativa [alla crittografia dei dati](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)inattivi di Azure.

### <a name="encryption-in-transit"></a>Crittografia in transito

È possibile usare SSL per proteggere la comunicazione interna tra Azure Machine Learning microservizi e per proteggere le chiamate esterne all'endpoint di assegnazione dei punteggi. Tutti gli accessi di archiviazione di Azure si verificano anche su un canale sicuro.

Per altre informazioni, vedere [usare SSL per proteggere un servizio Web tramite Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Utilizzo di Azure Key Vault

Il servizio Azure Machine Learning utilizza l'istanza Azure Key Vault associata all'area di lavoro per archiviare le credenziali di diversi tipi:

* Stringa di connessione dell'account di archiviazione associato
* Password per le istanze del repository contenitore di Azure
* Stringhe di connessione agli archivi dati

Le password e le chiavi SSH per le destinazioni di calcolo come Azure HDInsight e le macchine virtuali vengono archiviate in un insieme di credenziali delle chiavi separato associato alla sottoscrizione Microsoft. Il servizio Azure Machine Learning non archivia le password o le chiavi fornite dagli utenti. Al contrario, genera, autorizza e archivia le proprie chiavi SSH per connettersi alle macchine virtuali e HDInsight per eseguire gli esperimenti.

A ogni area di lavoro è associata un'identità gestita assegnata dal sistema con lo stesso nome dell'area di lavoro. Questa identità gestita può accedere a tutte le chiavi, i segreti e i certificati nell'insieme di credenziali delle chiavi.

## <a name="monitoring"></a>Monitoraggio

### <a name="metrics"></a>metrics

È possibile usare le metriche di monitoraggio di Azure per visualizzare e monitorare le metriche per l'area di lavoro del servizio Azure Machine Learning. Nella [portale di Azure](https://portal.azure.com)selezionare l'area di lavoro e quindi selezionare **metrica**:

[![Screenshot che mostra le metriche di esempio per un'area di lavoro](./media/enterprise-readiness/workspace-metrics.png)](./media/enterprise-readiness/workspace-metrics-expanded.png)

Le metriche includono informazioni sulle esecuzioni, le distribuzioni e le registrazioni.

Per altre informazioni, vedere [metriche in monitoraggio di Azure](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Log attività

È possibile visualizzare il log attività di un'area di lavoro per visualizzare varie operazioni eseguite sull'area di lavoro. Il log include informazioni di base, ad esempio il nome dell'operazione, l'iniziatore di eventi e il timestamp.

Questa schermata mostra il log attività di un'area di lavoro:

[![Screenshot che mostra il log attività di un'area di lavoro](./media/enterprise-readiness/workspace-activity-log.png)](./media/enterprise-readiness/workspace-activity-log-expanded.png)

I dettagli della richiesta di assegnazione dei punteggi vengono archiviati in Application Insights. Quando si crea un'area di lavoro, Application Insights viene creato nella sottoscrizione. Le informazioni registrate includono campi come HTTPMethod, UserAgent, ComputeType, RequestUrl, StatusCode, RequestId e Duration.

> [!IMPORTANT]
> Alcune azioni nell'area di lavoro Azure Machine Learning non registrano le informazioni nel log attività. Ad esempio, l'inizio di un'esecuzione del training e la registrazione di un modello non vengono registrate.
>
> Alcune di queste azioni vengono visualizzate nell'area **attività** dell'area di lavoro, ma queste notifiche non indicano chi ha avviato l'attività.

## <a name="data-flow-diagrams"></a>Diagrammi di flusso di dati

### <a name="create-workspace"></a>Crea area di lavoro

Il diagramma seguente illustra il flusso di lavoro Crea area di lavoro.

* L'utente accede al Azure AD da uno dei client del servizio di Azure Machine Learning supportati (interfaccia della riga di comando di Azure, Python SDK, portale di Azure) e richiede il token di Azure Resource Manager appropriato.
* L'utente chiama Azure Resource Manager per creare l'area di lavoro. 
* Azure Resource Manager Contatta il provider di risorse del servizio Azure Machine Learning per il provisioning dell'area di lavoro.

Le risorse aggiuntive vengono create nella sottoscrizione dell'utente durante la creazione dell'area di lavoro:

* Key Vault (per archiviare i segreti)
* Un account di archiviazione di Azure (incluse la condivisione file e BLOB)
* Azure Container Registry (per archiviare le immagini Docker per l'inferenza, il punteggio e la sperimentazione)
* Application Insights (per archiviare i dati di telemetria)

L'utente può anche effettuare il provisioning di altre destinazioni di calcolo collegate a un'area di lavoro, ad esempio il servizio o le VM Kubernetes di Azure, in base alle esigenze.

[![Crea flusso di lavoro area di lavoro](./media/enterprise-readiness/create-workspace.png)](./media/enterprise-readiness/create-workspace-expanded.png)

### <a name="save-source-code-training-scripts"></a>Salva codice sorgente (script di training)

Il diagramma seguente illustra il flusso di lavoro snapshot del codice.

Associate a un'area di lavoro del servizio Azure Machine Learning sono directory (esperimenti) che contengono il codice sorgente (script di training). Questi script vengono archiviati nel computer locale e nel cloud (nell'archivio BLOB di Azure per la sottoscrizione). Gli snapshot del codice vengono usati per l'esecuzione o l'ispezione per il controllo cronologico.

[![Flusso di lavoro snapshot del codice](./media/enterprise-readiness/code-snapshot.png)](./media/enterprise-readiness/code-snapshot-expanded.png)

### <a name="training"></a>Formazione

Il diagramma seguente illustra il flusso di lavoro di training.

* Il servizio Azure Machine Learning viene chiamato con l'ID dello snapshot del codice salvato nella sezione precedente.
* Il servizio Azure Machine Learning crea un ID esecuzione (facoltativo) e un token del servizio Machine Learning, che verrà usato in seguito da destinazioni di calcolo come ambiente di calcolo di Machine Learning/VM per comunicare con il servizio Machine Learning.
* Per eseguire i processi di training, è possibile scegliere una destinazione di calcolo gestita, ad esempio ambiente di calcolo di Machine Learning, o una destinazione di calcolo non gestita, ad esempio macchine virtuali. Ecco i flussi di dati per entrambi gli scenari:
   * VM/HDInsight, a cui si accede tramite credenziali SSH in un insieme di credenziali delle chiavi nella sottoscrizione Microsoft. Il servizio Azure Machine Learning esegue il codice di gestione nella destinazione di calcolo che:

   1. Prepara l'ambiente. (Docker è un'opzione per le macchine virtuali e i computer locali. Ambiente di calcolo di Machine Learning per informazioni sull'esecuzione di esperimenti sui contenitori Docker, vedere i passaggi seguenti.
   1. Scarica il codice.
   1. Imposta le configurazioni e le variabili di ambiente.
   1. Esegue gli script utente (lo snapshot del codice indicato nella sezione precedente).

   * Ambiente di calcolo di Machine Learning, a cui si accede tramite un'identità gestita dall'area di lavoro.
Poiché ambiente di calcolo di Machine Learning è una destinazione di calcolo gestita (ovvero, è gestita da Microsoft) viene eseguita con la sottoscrizione Microsoft.

   1. Se necessario, la costruzione remota di Docker viene avviata.
   1. Il codice di gestione viene scritto nella condivisione File di Azure dell'utente.
   1. Il contenitore viene avviato con un comando iniziale. Ovvero il codice di gestione come descritto nel passaggio precedente.

#### <a name="querying-runs-and-metrics"></a>Esecuzione di query su esecuzioni e metriche

Nel diagramma di flusso riportato di seguito questo passaggio si verifica quando la destinazione di calcolo di training scrive le metriche di esecuzione nel servizio Azure Machine Learning dalla risorsa di archiviazione nel database di Cosmos DB. I client possono chiamare il servizio Azure Machine Learning. Machine Learning effettuerà a sua volta il pull delle metriche dal database Cosmos DB e le restituirà al client.

[![Flusso di lavoro di training](./media/enterprise-readiness/training-and-metrics.png)](./media/enterprise-readiness/training-and-metrics-expanded.png)

### <a name="creating-web-services"></a>Creazione di servizi Web

Il diagramma seguente illustra il flusso di lavoro di inferenza. L'inferenza o il punteggio del modello è la fase in cui il modello distribuito viene usato per la stima, più comunemente sui dati di produzione.

Di seguito sono riportati i dettagli:

* L'utente registra un modello usando un client come Azure Machine Learning SDK.
* L'utente crea un'immagine usando un modello, un file di punteggio e altre dipendenze del modello.
* L'immagine Docker viene creata e archiviata in Container Registry di Azure.
* Il servizio Web viene distribuito nella destinazione di calcolo (istanze di contenitore/AKS) usando l'immagine creata nel passaggio precedente.
* I dettagli della richiesta di assegnazione dei punteggi vengono archiviati in Application Insights, che si trova nella sottoscrizione dell'utente.
* Viene anche effettuato il push della telemetria alla sottoscrizione Microsoft/Azure.

[![Flusso di lavoro inferenza](./media/enterprise-readiness/inferencing.png)](./media/enterprise-readiness/inferencing-expanded.png)

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i servizi Web di Azure Machine Learning con SSL](how-to-secure-web-service.md)
* [Utilizzare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Come eseguire previsioni in batch](how-to-run-batch-predictions.md)
* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
* [SDK del servizio Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Usare il servizio Azure Machine Learning con rete virtuale di Azure](how-to-enable-virtual-network.md)
* [Best practices for building recommendation systems](https://github.com/Microsoft/Recommenders) (Procedure consigliate per creare sistemi di raccomandazione)
* [Creare un'API per raccomandazioni in tempo reale in Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
