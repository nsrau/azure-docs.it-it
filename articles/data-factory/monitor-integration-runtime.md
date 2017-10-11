---
title: Monitoraggio del runtime di integrazione in Azure Data Factory | Documentazione Microsoft
description: Informazioni su come monitorare diversi tipi di runtime di integrazione in Azure Data Factory.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: f2e1957a02d72d79a245ec3be705ba46dcc41e2c
ms.contentlocale: it-it
ms.lasthandoff: 09/28/2017

---

# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitoraggio di un runtime di integrazione in Azure Data Factory  
Il **runtime di integrazione** è l'infrastruttura di calcolo usata da Azure Data Factory per fornire varie funzionalità di integrazione di dati in diversi ambienti di rete. Esistono tre tipi di runtime di integrazione offerti da Data Factory:

- Runtime di integrazione di Azure
- Runtime di integrazione self-hosted
- Runtime di integrazione SSIS di Azure

Per ottenere lo stato di un'istanza di runtime di integrazione (IR), eseguire il comando PowerShell seguente: 

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Il cmdlet restituisce informazioni diverse per diversi tipi di runtime di integrazione. In questo articolo vengono illustrate le proprietà e gli stati per ogni tipo di runtime di integrazione.  

## <a name="azure-integration-runtime"></a>Runtime di integrazione di Azure
La risorsa di calcolo per un runtime di integrazione di Azure è completamente gestita in modo elastico in Azure. Nella tabella seguente vengono fornite descrizioni per le proprietà restituite dal comando **Get AzureRmDataFactoryV2IntegrationRuntime**:

### <a name="properties"></a>Proprietà
Nella tabella seguente vengono fornite descrizioni per le proprietà restituite dal cmdlet per un runtime di integrazione Azure:

| Proprietà | Descrizione |
-------- | ------------- | 
| Nome | Nome del runtime di integrazione di Azure. |  
| Stato | Stato del runtime di integrazione di Azure. | 
| Percorso | Percorso del runtime di integrazione di Azure. Per altri dettagli sul percorso di un runtime di integrazione di Azure, vedere [Introduzione al runtime di integrazione](concepts-integration-runtime.md). |
| DataFactoryName | Nome della data factory a cui appartiene il runtime di integrazione di Azure. | 
| ResourceGroupName | Nome del gruppo di risorse a cui appartiene la data factory.  |
| Descrizione | Descrizione del runtime di integrazione di Azure.  |

### <a name="status"></a>Stato
La tabella seguente indica i possibili stati di un runtime di integrazione di Azure:

| Stato | Commenti/Scenari | 
| ------ | ------------------ |
| Online | Il runtime di integrazione di Azure è online e pronto per essere utilizzato. | 
| Offline | Il runtime di integrazione di Azure è offline a causa di un errore interno. |

## <a name="self-hosted-integration-runtime"></a>Runtime di integrazione self-hosted
Nella presente sezione vengono fornite descrizioni per le proprietà restituite dal cmdlet Get AzureRmDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> La proprietà restituite e lo stato contengono informazioni sul runtime di integrazione self-hosted generale e su ogni nodo nel runtime.  

### <a name="properties"></a>Proprietà

La tabella seguente fornisce le descrizioni delle proprietà di monitoraggio per **ogni nodo**:

| Proprietà | Descrizione | 
| -------- | ----------- | 
| Nome | Nome del runtime di integrazione self-hosted e dei nodi associati. Il nodo è un computer Windows locale su cui è installato il runtime di integrazione self-hosted. |  
| Stato | Lo stato del runtime di integrazione self-hosted generale e di ogni nodo. Esempio: Online/Offline/Limitato e così via. Per informazioni su questi stati, vedere la sezione successiva. | 
| Versione | La versione del runtime di integrazione self-hosted e di ogni nodo. La versione del runtime di integrazione self-hosted viene determinata in base alla versione della maggior parte dei nodi del gruppo. Se nella configurazione del runtime di integrazione self-hosted sono presenti nodi con versioni diverse, solo i nodi con lo stesso numero di versione del runtime di integrazione self-hosted funzionano correttamente. Gli altri sono in modalità limitata e devono essere aggiornati manualmente (solo se l'aggiornamento automatico non riesce). | 
| Memoria disponibile | Memoria disponibile in un nodo di runtime di integrazione self-hosted. Questo valore è uno snapshot in tempo quasi reale. | 
| Uso della CPU | Utilizzo della CPU da parte di un nodo di runtime di integrazione self-hosted. Questo valore è uno snapshot in tempo quasi reale. |
| Rete (in/out) | Utilizzo del network da parte di un nodo di runtime di integrazione self-hosted. Questo valore è uno snapshot in tempo quasi reale. | 
| Processi simultanei (in esecuzione/limite) | Numero di processi o di attività in esecuzione in ogni nodo. Questo valore è uno snapshot in tempo quasi reale. Per limite si intende il numero massimo di processi simultanei per ogni nodo. Questo valore viene definito in base alle dimensioni del computer. È possibile accrescere il limite per aumentare le prestazioni dell'esecuzione dei processi simultanei negli scenari avanzati, in cui CPU/memoria/rete sono sottoutilizzate, ma è in corso il timeout delle attività. Questa capacità è disponibile anche in un runtime di integrazione self-hosted a nodo singolo. |
| Ruolo | Esistono due tipi di ruoli in un runtime di integrazione self-hosted a più nodi: dispatcher e ruolo di lavoro. Tutti i nodi sono ruoli di lavoro e quindi possono essere tutti usati per eseguire i processi. Esiste un solo nodo dispatcher, che viene usato per eseguire il pull di attività/processi dai servizi cloud e inviarli a nodi ruolo di lavoro diversi. Il nodo dispatcher è anche un nodo di lavoro. |

Alcune impostazioni delle proprietà che sono più appropriate con due o più nodi (scenario di scalabilità orizzontale) nel runtime di integrazione self-hosted. 
  
### <a name="status-per-node"></a>Stato (per ogni nodo)
La tabella seguente indica i possibili stati di un nodo di runtime di integrazione self-hosted:

| Stato | Descrizione |
| ------ | ------------------ | 
| Online | Il nodo è connesso al servizio Data Factory. |
| Offline | Il nodo è offline. |
| Aggiornamento | È in corso l'aggiornamento automatico del nodo. |
| Limitato | La causa è un problema di connettività, ad esempio un problema della porta HTTP 8050, di connettività del bus di servizio o di sincronizzazione delle credenziali. |
| Inactive | Il nodo è in una configurazione diversa da quella della maggior parte degli altri nodi. |

Un nodo può essere inattivo quando non riesce a connettersi agli altri nodi.

### <a name="status-overall-self-hosted-integration-runtime"></a>Stato (runtime di integrazione self-hosted generale)
La tabella seguente indica i possibili stati di un runtime di integrazione self-hosted. Questo stato varia a seconda degli stati di tutti i nodi che appartengono al runtime. 

| Stato | Descrizione |
| ------ | ----------- | 
| Need Registration | Nessun nodo è ancora registrato per questo runtime di integrazione self-hosted. |
| Online | Tutti i nodi sono online. |
| Offline | Nessun nodo è online. |
| Limitato | Non tutti i nodi in questo runtime di integrazione self-hosted sono in uno stato integro. Questo stato è un avviso indicante che qualche nodo potrebbe essere inattivo. La causa di questo stato potrebbe essere un problema di sincronizzazione delle credenziali nel nodo dispatcher/ruolo di lavoro. |

Utilizzare il cmdlet **Get AzureRmDataFactoryV2IntegrationRuntimeMetric** per recuperare il payload JSON che contiene le proprietà dettagliate del runtime di integrazione self-hosted e i relativi valori di snapshot durante la fase di esecuzione del cmdlet.

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

Esempio di output (presuppone che siano presenti due nodi associati a questo runtime di integrazione self-hosted):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```


## <a name="azure-ssis-integration-runtime"></a>Runtime di integrazione SSIS di Azure
Il runtime di integrazione Azure-SSIS è un cluster completamente gestito di macchine virtuali (o nodi) di Azure dedicato all'esecuzione di pacchetti SSIS. Tutte le altre attività di Azure Data Factory non vengono eseguite. Al termine del provisioning, è possibile eseguire una query delle proprietà e monitorare i relativi stati specifici del nodo/generali.

### <a name="properties"></a>Proprietà

| Proprietà/stato | Descrizione |
| --------------- | ----------- |
| CreateTime | L'ora UTC in cui è stato creato il runtime di integrazione di Azure-SSIS. |
| Nodi | I nodi disponibili/allocati del runtime di integrazione SSIS di Azure con errori eseguibili e stati specifici del nodo (avvio/disponibile/riciclo/non disponibile). |
| OtherErrors | Gli errori eseguibili non specifici del nodo del runtime di integrazione SSIS di Azure. |
| LastOperation | Risultato dell'ultima operazione di avvio/arresto del runtime di integrazione SSIS di Azure con errori eseguibili se non è riuscita. |
| Stato | Lo stato generale (iniziale/fase di avvio/avvio/arresto/arrestato) del runtime di integrazione SSIS di Azure. |
| Percorso | Il percorso del runtime di integrazione SSIS di Azure. |
| NodeSize | Le dimensioni di ogni nodo del runtime di integrazione SSIS di Azure. |
| NodeCount | Il numero di nodi nel runtime di integrazione SSIS di Azure. |
| MaxParallelExecutionsPerNode | Il numero di esecuzioni parallele per ogni nodo nel runtime di integrazione SSIS di Azure. |
| CatalogServerEndpoint | L'endpoint del server di istanza gestita/database SQL di Azure esistente per ospitare il database SSIS. |
| CatalogAdminUserName | Il nome utente amministratore del server di istanza gestita/database SQL di Azure esistente. Il servizio Data Factory utilizza queste informazioni per preparare e gestire il database SSIS per conto dell'utente. |
| CatalogAdminPassword | La password amministratore del server di istanza gestita/database SQL di Azure esistente. |
| CatalogPricingTier | Il piano tariffario per il database SSIS ospitato dal server del database SQL di Azure esistente.  Non applicabile all’istanza gestita SQL di Azure che ospita il database SSIS. |
| VNetId | L’ID della risorsa di rete virtuale (VNet) per aggiungere un runtime di integrazione SSIS di Azure. |
| Subnet | Il nome della subnet per aggiungere un runtime di integrazione SSIS di Azure. |
| ID | L’ID di risorsa del runtime di integrazione SSIS di Azure. |
| Tipo | Il tipo (gestito/self-hosted) di runtime di integrazione SSIS di Azure. |
| ResourceGroupName | Il nome del gruppo di risorse di Azure, in cui sono stati creati la data factory e il runtime di integrazione SSIS di Azure. |
| DataFactoryName | Nome della data factory di Azure. |
| Nome | Il nome del runtime di integrazione SSIS di Azure. |
| Descrizione | La descrizione del runtime di integrazione SSIS di Azure. |

  
### <a name="status-per-node"></a>Stato (per ogni nodo)

| Stato | Descrizione |
| ------ | ----------- | 
| Avvio in corso | Questo nodo è in corso di preparazione. |
| Disponibile | Questo nodo è pronto per la distribuzione/esecuzione di pacchetti SSIS. |
| Riciclo | Questo nodo è in corso di riparazione/riavvio. |
| Non disponibile | Questo nodo non è pronto per la distribuzione/esecuzione di pacchetti SSIS da parte dell’utente e presenta errori eseguibili o problemi che è stato possibile risolvere. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Stato (generale del runtime di integrazione SSIS di Azure)

| Stato generale | Descrizione | 
| -------------- | ----------- | 
| Initial | I nodi del runtime di integrazione SSIS di Azure non sono stati allocati/preparati. | 
| Avvio in corso | L’allocazione/preparazione dei nodi del runtime di integrazione SSIS di Azure è in corso e la fatturazione è stata avviata. |
| Started | I nodi del runtime di integrazione SSIS di Azure sono stati allocati/preparati e sono pronti per poter distribuire o eseguire pacchetti SSIS. |
| Arresto in corso  | I nodi del runtime di integrazione SSIS di Azure vengono rilasciati. |
| Arrestato | L’allocazione/preparazione dei nodi del runtime di integrazione SSIS di Azure è stata eseguita e la fatturazione è stata avviata. |

Vedere gli articoli seguenti per ulteriori informazioni sul runtime di integrazione SSIS di Azure:

- [Runtime di integrazione SSIS di Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo vengono fornite le informazioni sui runtime di integrazione in generale incluso il runtime di integrazione SSIS di Azure. 
- [Esercitazione: distribuire i pacchetti SSIS in Azure](tutorial-deploy-ssis-packages-azure.md). In questo articolo vengono fornite le istruzioni passo per passo per creare un runtime di integrazione SSIS di Azure e utilizzare un database Azure SQL per ospitare il catalogo SSIS. 
- [Procedura: come creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md). Questo articolo amplia l'esercitazione e fornisce istruzioni sull'utilizzo dell’istanza gestita di database SQL di Azure (anteprima privata) e sull’aggiunta del runtime di integrazione a un VNet. 
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). In questo articolo viene illustrato come arrestare, avviare o rimuovere un runtime di integrazione SSIS di Azure. Viene inoltre mostrato come scalare orizzontalmente il runtime di integrazione SSIS di Azure aggiungendo più nodi al runtime di integrazione. 
- [Aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md). Questo articolo fornisce informazioni sull'aggiunta di un runtime di integrazione SSIS di Azure a una rete virtuale di Azure (VNet). Indica inoltre i passaggi per usare il portale di Azure per configurare VNet in modo che il runtime di integrazione SSIS di Azure possa essere aggiunto alla rete virtuale. 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti per il monitoraggio di pipeline in modi diversi: 

- [Guida introduttiva: creare una data factory](quickstart-create-data-factory-dot-net.md).
- [Utilizzare Monitoraggio di Azure per monitorare le pipeline di Data Factory](monitor-using-azure-monitor.md)
