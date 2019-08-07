---
title: Sicurezza aziendale
titleSuffix: Azure Machine Learning service
description: 'Usare in modo sicuro Azure Machine Learning servizio: autenticazione, autorizzazione, sicurezza di rete, crittografia dei dati e monitoraggio.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/10/2019
ms.openlocfilehash: f0fb6f0d2b2579679ee8a6ec43b3241377701d48
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780908"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Sicurezza aziendale per il servizio Azure Machine Learning

In questo articolo vengono fornite informazioni sulle funzionalità di sicurezza disponibili con il servizio Azure Machine Learning.

Quando si usa un servizio cloud, è consigliabile limitare l'accesso solo agli utenti che ne hanno bisogno. Questa operazione inizia con la comprensione del modello di autenticazione e autorizzazione utilizzato dal servizio. Potrebbe anche essere opportuno limitare l'accesso alla rete o aggiungere in modo sicuro le risorse nella rete locale con quelle nel cloud. La crittografia dei dati è anche fondamentale, sia inattivi che durante lo spostamento dei dati tra i servizi. Infine, è necessario essere in grado di monitorare il servizio e produrre un log di controllo di tutte le attività.

## <a name="authentication"></a>Authentication

La funzionalità autenticazione a più fattori è supportata se Azure Active Directory (Azure AD) è configurato per lo stesso.

* Il client accede Azure AD e ottiene Azure Resource Manager token.  Gli utenti e le entità servizio sono completamente supportati.
* Il client presenta un token per Azure Resource Manager & tutti i servizi Azure Machine Learning
* Azure Machine Learning servizio fornisce un token Azure Machine Learning per il calcolo dell'utente. Ad esempio, ambiente di calcolo di Machine Learning. Questo token Azure Machine Learning viene usato dall'utente calcolo per richiamare il servizio Azure Machine Learning (limita l'ambito all'area di lavoro) al termine dell'esecuzione.

![Screenshot che illustra il funzionamento dell'autenticazione nel servizio Azure Machine Learning](./media/enterprise-readiness/authentication.png)

### <a name="authentication-for-web-service-deployment"></a>Autenticazione per la distribuzione del servizio Web

Azure Machine Learning supporta due forme di autenticazione per i servizi Web, la chiave e il token. Ogni WebService può abilitare solo una forma di autenticazione alla volta.

|Metodo di autenticazione|ACI|Servizio Azure Kubernetes|
|---|---|---|
|Chiave|Disabilitato per impostazione predefinita| Abilitato per impostazione predefinita|
|Token| Non disponibile| Disabilitato per impostazione predefinita |

#### <a name="authentication-with-keys"></a>Autenticazione con chiavi

Quando si Abilita l'autenticazione della chiave per una distribuzione, si creano automaticamente le chiavi di autenticazione.

* L'autenticazione è abilitata per impostazione predefinita quando si esegue la distribuzione nel servizio Azure Kubernetes.
* L'autenticazione è disabilitata per impostazione predefinita quando si esegue la distribuzione in Istanze di Azure Container.

Per abilitare l'autenticazione della chiave, `auth_enabled` utilizzare il parametro durante la creazione o l'aggiornamento di una distribuzione.

Se è abilitata l'autenticazione della chiave, è `get_keys` possibile usare il metodo per recuperare una chiave di autenticazione primaria e secondaria:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se è necessario rigenerare una chiave, usare[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

#### <a name="authentication-with-tokens"></a>Autenticazione con token

Quando si Abilita l'autenticazione basata su token per un servizio Web, un utente deve fornire un token Web JSON Azure Machine Learning al servizio Web per accedervi.

* Per impostazione predefinita, l'autenticazione del token è disabilitata quando si esegue la distribuzione nel servizio Azure Kubernetes.
* L'autenticazione basata su token non è supportata quando si esegue la distribuzione in istanze di contenitore di Azure.

Per controllare l'autenticazione del token, `token_auth_enabled` usare il parametro durante la creazione o l'aggiornamento di una distribuzione.

Se l'autenticazione basata su token è abilitata, `get_token` è possibile usare il metodo per recuperare un token JWT e l'ora di scadenza del token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Sarà necessario richiedere un nuovo token dopo l' `refresh_by` ora del token.
>
> È consigliabile creare l'area di lavoro Azure Machine Learning nella stessa area del cluster del servizio Azure Kubernetes. Per eseguire l'autenticazione con un token, il servizio Web effettuerà una chiamata all'area in cui viene creata l'area di lavoro Azure Machine Learning. Se l'area dell'area di lavoro non è disponibile, non sarà possibile recuperare un token per il servizio Web anche se il cluster si trova in un'area diversa rispetto all'area di lavoro. Questo consente di ottenere Autenticazione di Azure AD non disponibile fino a quando l'area dell'area di lavoro non sarà nuovamente disponibile. Inoltre, maggiore è la distanza tra l'area del cluster e l'area dell'area di lavoro, più tempo sarà necessario per recuperare un token.

## <a name="authorization"></a>Authorization

È possibile creare più aree di lavoro, ciascuna delle quali può essere condivisa da più utenti. Quando si condivide un'area di lavoro, è possibile controllarne l'accesso assegnando agli utenti i ruoli seguenti:

* Proprietario
* Collaboratore
* Reader

Nella tabella seguente sono elencate alcune delle principali operazioni del servizio Azure Machine Learning e i ruoli che possono eseguirli:

| Operazione del servizio Azure Machine Learning | Proprietario | Collaboratore | Reader |
| ---- |:----:|:----:|:----:|
| Crea area di lavoro | ✓ | ✓ | |
| Condividi area di lavoro | ✓ | |  |
| Crea calcolo | ✓ | ✓ | |
| Connetti calcolo | ✓ | ✓ | |
| Connetti archivi dati | ✓ | ✓ | |
| Eseguire un esperimento | ✓ | ✓ | |
| Visualizza esecuzioni/metriche | ✓ | ✓ | ✓ |
| Registrare il modello | ✓ | ✓ | |
| Crea immagine | ✓ | ✓ | |
| Distribuire il servizio Web | ✓ | ✓ | |
| Visualizza modelli/immagini | ✓ | ✓ | ✓ |
| Chiama servizio Web | ✓ | ✓ | ✓ |

Se i ruoli predefiniti non sono sufficienti per le proprie esigenze, è anche possibile creare ruoli personalizzati. Gli unici ruoli personalizzati supportati sono per le operazioni nell'area di lavoro e ambiente di calcolo di Machine Learning. I ruoli personalizzati possono disporre di autorizzazioni di lettura, scrittura o eliminazione per l'area di lavoro e la risorsa di calcolo in tale area di lavoro. È possibile rendere il ruolo disponibile a livello di area di lavoro specifico, a livello di gruppo di risorse specifico o a un livello di sottoscrizione specifico. Per altre informazioni, vedere [gestire utenti e ruoli in un'area di lavoro Azure Machine Learning](how-to-assign-roles.md)

### <a name="securing-compute-and-data"></a>Sicurezza di calcolo e dati

I proprietari e i collaboratori possono usare tutte le destinazioni di calcolo e gli archivi dati collegati all'area di lavoro.  
A ogni area di lavoro è associata anche un'identità gestita assegnata dal sistema (con lo stesso nome dell'area di lavoro) con le autorizzazioni seguenti per le risorse associate utilizzate nell'area di lavoro:

Per altre informazioni sulle identità gestite, vedere [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

| Risorsa | Autorizzazioni |
| ----- | ----- |
| Area di lavoro | Collaboratore |
| Account di archiviazione | Collaboratore ai dati dei BLOB di archiviazione |
| Key Vault | Accesso a tutte le chiavi, segreti, certificati |
| Registro contenitori di Azure | Collaboratore |
| Gruppo di risorse che contiene l'area di lavoro | Collaboratore |
| Gruppo di risorse che contiene il Key Vault (se diverso da quello contenente l'area di lavoro) | Collaboratore |

Si consiglia agli amministratori di non revocare l'accesso dell'identità gestita alle risorse indicate in precedenza. È possibile ripristinare l'accesso con l'operazione di risincronizzazione delle chiavi.

Azure Machine Learning servizio crea un'applicazione aggiuntiva (il nome `aml-`inizia con) con l'accesso a livello di collaboratore nella sottoscrizione per ogni area dell'area di lavoro. Per es. Se si dispone di un'area di lavoro negli Stati Uniti orientali e in un'altra area di lavoro in Europa settentrionale nella stessa sottoscrizione, vengono visualizzate due applicazioni di questo tipo. Questa operazione è necessaria in modo che Azure Machine Learning servizio possa aiutare a gestire le risorse di calcolo.

## <a name="network-security"></a>Sicurezza di rete

Il servizio Azure Machine Learning si basa su altri servizi di Azure per le risorse di calcolo. Le risorse di calcolo (destinazioni di calcolo) vengono usate per eseguire il training e la distribuzione dei modelli. Queste destinazioni di calcolo possono essere create in una rete virtuale. È ad esempio possibile usare un ambiente Microsoft Data Science Virtual Machine per eseguire il training di un modello e quindi distribuire il modello nel servizio Azure Kubernetes.  

Per ulteriori informazioni, vedere [come eseguire esperimenti e inferenza in una rete virtuale](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Crittografia dati

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi

#### <a name="azure-blob-storage"></a>Archivio BLOB di Azure

Azure Machine Learning servizio archivia gli snapshot, gli output e i log nell'account di archiviazione BLOB di Azure associato all'area di lavoro del servizio Azure Machine Learning e si trova nella sottoscrizione dell'utente. Tutti i dati archiviati nell'archivio BLOB di Azure vengono crittografati a riposo usando chiavi gestite da Microsoft.

Per altre informazioni su come importare chiavi personalizzate per i dati archiviati nell'archivio BLOB di Azure, vedere [crittografia del servizio di archiviazione uso delle chiavi gestite dal cliente in Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

I dati di training vengono in genere archiviati anche nell'archivio BLOB di Azure in modo che sia accessibile al calcolo della formazione. Questa risorsa di archiviazione non è gestita da Azure Machine Learning ma montata per il calcolo come file system remoto.

Per informazioni sulla rigenerazione delle chiavi di accesso per gli account di archiviazione di Azure usati con l'area di lavoro, vedere l'articolo rigenerazione delle [chiavi di accesso](how-to-change-storage-access-key.md) alle archiviazioni.

#### <a name="cosmos-db"></a>Cosmos DB

Azure Machine Learning servizio archivia le metriche e i metadati per la Cosmos DB che si trova in una sottoscrizione Microsoft gestita da Azure Machine Learning Service. Tutti i dati archiviati in Cosmos DB vengono crittografati a riposo usando chiavi gestite da Microsoft.

#### <a name="azure-container-registry-acr"></a>Container Registry di Azure (ACR)

Tutte le immagini del contenitore nel registro di sistema (ACR) sono crittografate a riposo. Azure crittografa automaticamente un'immagine prima di archiviarla e la decrittografa al volo quando Azure Machine Learning servizio estrae l'immagine.

#### <a name="machine-learning-compute"></a>Ambiente di calcolo di Machine Learning

Il disco del sistema operativo per ogni nodo di calcolo viene archiviato in archiviazione di Azure viene crittografato usando chiavi gestite da Microsoft negli account di archiviazione del servizio Azure Machine Learning. Questo calcolo è effimero e i cluster vengono in genere ridimensionati in assenza di esecuzioni in coda. Viene effettuato il deprovisioning della macchina virtuale sottostante e il disco del sistema operativo è stato eliminato. Crittografia dischi di Azure non è supportata per il disco del sistema operativo.
Ogni macchina virtuale dispone anche di un disco temporaneo locale per le operazioni del sistema operativo. Questo disco può anche essere utilizzato facoltativamente per organizzare i dati di training. Questo disco non è crittografato.
Per altre informazioni sul funzionamento della crittografia dei dati inattivi in Azure, vedere [crittografia dei dati](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)inattivi di Azure.

### <a name="encryption-in-transit"></a>Crittografia in transito

Sia la comunicazione interna tra diversi Azure Machine Learning microservizi che la comunicazione esterna della chiamata dell'endpoint di assegnazione dei punteggi sono supportate tramite SSL. Tutti gli accessi di archiviazione di Azure sono anche su un canale sicuro.
Per altre informazioni, vedere [proteggere Azure Machine Learning i servizi Web tramite SSL](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Utilizzo di Azure Key Vault

Key Vault istanza associata all'area di lavoro viene utilizzata dal servizio Azure Machine Learning per archiviare le credenziali di diversi tipi:

* Stringa di connessione dell'account di archiviazione associato
* Password per le istanze del repository contenitore di Azure
* Stringhe di connessione agli archivi dati.

Le password e le chiavi SSH per le destinazioni di calcolo, ad esempio HDInsight HDI e VM, vengono archiviate in un Key Vault separato associato alla sottoscrizione Microsoft. Azure Machine Learning servizio archivia le password o le chiavi fornite dall'utente, ma genera, autorizza e archivia le proprie chiavi SSH per connettersi alla macchina virtuale/HDInsight per eseguire gli esperimenti.
A ogni area di lavoro è associata un'identità gestita assegnata dal sistema (con lo stesso nome dell'area di lavoro) che ha accesso a tutte le chiavi, i segreti e i certificati presenti nel Key Vault.

## <a name="monitoring"></a>Monitoraggio

Gli utenti possono visualizzare il log attività nell'area di lavoro per visualizzare varie operazioni eseguite nell'area di lavoro e ottenere le informazioni di base, ad esempio il nome dell'operazione, l'evento avviato da, il timestamp e così via.

La schermata seguente mostra il log attività per un'area di lavoro:

![Screenshot che mostra il log attività in un'area di lavoro](./media/enterprise-readiness/workspace-activity-log.png)

I dettagli della richiesta di assegnazione dei punteggi vengono archiviati in AppInsights, che viene creato nella sottoscrizione dell'utente durante la creazione dell'area di lavoro. Sono inclusi campi come HTTPMethod, UserAgent, ComputeType, RequestUrl, StatusCode, RequestId, Duration e così via.

## <a name="data-flow-diagram"></a>Diagramma del flusso di dati

### <a name="create-workspace"></a>Crea area di lavoro

Il diagramma seguente illustra il flusso di lavoro Crea area di lavoro.
L'utente accede Azure AD da uno dei client del servizio di Azure Machine Learning supportati (CLI, Python SDK, portale di Azure) e richiede il token Azure Resource Manager appropriato. L'utente chiama quindi Azure Resource Manager per creare l'area di lavoro.  Azure Resource Manager Contatta il provider di risorse del servizio Azure Machine Learning per il provisioning dell'area di lavoro.  Le risorse aggiuntive vengono create nella sottoscrizione del cliente durante la creazione dell'area di lavoro:

* Insieme di credenziali delle chiavi (per archiviare i segreti)
* Un account di archiviazione di Azure (incluso BLOB & FileShare)
* Azure Container Registry (per archiviare le immagini Docker per l'inferenza, il punteggio e la sperimentazione)
* Application Insights (per archiviare i dati di telemetria)

Altre risorse di calcolo collegate a un'area di lavoro (servizio Azure Kubernetes, VM e così via) possono anche essere sottoposte a provisioning dai clienti in base alle esigenze.

![Screenshot che illustra il flusso di lavoro Crea area di lavoro](./media/enterprise-readiness/create-workspace.png)

### <a name="save-source-code-training-scripts"></a>Salva codice sorgente (script di training)

Il diagramma seguente illustra il flusso di lavoro snapshot del codice.
Associate a un'area di lavoro del servizio Azure Machine Learning sono directory (esperimenti), che contiene il codice sorgente (script di training).  Questi vengono archiviati nel computer locale del cliente e nel cloud (nell'archivio BLOB di Azure nella sottoscrizione del cliente). Questi snapshot del codice vengono usati per l'esecuzione o l'ispezione per il controllo cronologico.

![Screenshot che illustra il flusso di lavoro Crea area di lavoro](./media/enterprise-readiness/code-snapshot.png)

### <a name="training"></a>Formazione

Il diagramma seguente illustra il flusso di lavoro di training.

* Il servizio Azure Machine Learning viene chiamato con l'ID dello snapshot del codice salvato in precedenza
* Il servizio Azure Machine Learning crea l'ID esecuzione (facoltativo) & token del servizio Azure Machine Learning, che verrà usato in un secondo momento dalle destinazioni di calcolo, ad esempio ambiente di calcolo di Machine Learning/VM, per tornare al servizio Azure Machine Learning
* È possibile scegliere un calcolo gestito, ad esempio Ambiente di calcolo di Machine Learning) o calcolo non gestito (ad esempio VM) per eseguire i processi di training. Il flusso di dati viene illustrato per entrambi gli scenari seguenti:
* (VM/HDInsight – a cui si accede tramite SSH credenziali in Key Vault nella sottoscrizione Microsoft) Azure Machine Learning servizio esegue il codice di gestione nella destinazione di calcolo che:

   1. Prepara l'ambiente. Si noti che Docker è un'opzione anche per le macchine virtuali e locali. Ambiente di calcolo di Machine Learning per informazioni sull'esecuzione dell'esperimento sul contenitore Docker, vedere i passaggi seguenti.
   1. Scarica il codice.
   1. Imposta le variabili di ambiente e le configurazioni.
   1. Esegue lo script utente (snapshot del codice menzionato in precedenza).

* (Ambiente di calcolo di Machine Learning: accesso con identità gestita dell'area di lavoro) Si noti che poiché ambiente di calcolo di Machine Learning è un calcolo gestito che è gestito da Microsoft, di conseguenza viene eseguito nella sottoscrizione Microsoft.

   1. Se necessario, la costruzione remota di Docker viene avviata.
   1. Scrive il codice di gestione nella condivisione file di Azure utente.
   1. Avvia il contenitore con un comando iniziale, ovvero il codice di gestione come descritto nel passaggio precedente.

#### <a name="querying-runs-and-metrics"></a>Esecuzione di query su esecuzioni e metriche

Questo passaggio viene visualizzato nel flusso in cui il training Compute scrive le *metriche di esecuzione* nel servizio Azure Machine Learning da dove viene archiviato nel Cosmos DB. I client possono chiamare Azure Machine Learning servizio che effettuerà a sua volta il pull delle metriche dal Cosmos DB e lo restituirà al client.

![Screenshot che illustra il flusso di lavoro Crea area di lavoro](./media/enterprise-readiness/training-and-metrics.png)

### <a name="creating-web-services"></a>Creazione di servizi Web

Il diagramma seguente illustra il flusso di lavoro di inferenza. L'inferenza o il punteggio del modello è la fase in cui il modello distribuito viene usato per la stima, più comunemente sui dati di produzione.
Vedere i dettagli seguenti:

* L'utente registra un modello usando un client come Azure ML SDK
* L'utente crea un'immagine usando il modello, il file di punteggio e altre dipendenze del modello
* L'immagine Docker viene creata e archiviata in ACR
* Il servizio Web viene distribuito nella destinazione di calcolo (ACI/AKS) usando l'immagine creata in precedenza
* I dettagli della richiesta di assegnazione dei punteggi vengono archiviati in AppInsights, che si trova nella sottoscrizione dell'utente
* Viene anche effettuato il push della telemetria alla sottoscrizione Microsoft/Azure

![Screenshot che illustra il flusso di lavoro Crea area di lavoro](./media/enterprise-readiness/inferencing.png)

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
