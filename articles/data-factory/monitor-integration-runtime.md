---
title: Monitoraggio del runtime di integrazione in Azure Data Factory | Documentazione Microsoft
description: Informazioni su come monitorare diversi tipi di runtime di integrazione in Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/25/2018
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: b62cbe75730da8c5764839d41887deb7e6cd0e90
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122632"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitoraggio di un runtime di integrazione in Azure Data Factory  
Il **runtime di integrazione** è l'infrastruttura di calcolo usata da Azure Data Factory per fornire varie funzionalità di integrazione di dati in diversi ambienti di rete. Esistono tre tipi di runtime di integrazione offerti da Data Factory:

- Runtime di integrazione di Azure
- Runtime di integrazione self-hosted
- Runtime di integrazione SSIS di Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per ottenere lo stato di un'istanza di runtime di integrazione (IR), eseguire il comando PowerShell seguente: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Il cmdlet restituisce informazioni diverse per diversi tipi di runtime di integrazione. In questo articolo vengono illustrate le proprietà e gli stati per ogni tipo di runtime di integrazione.  

## <a name="azure-integration-runtime"></a>Runtime di integrazione di Azure
La risorsa di calcolo per un runtime di integrazione di Azure è completamente gestita in modo elastico in Azure. Nella tabella seguente fornisce le descrizioni per le proprietà restituite per il **Get-AzDataFactoryV2IntegrationRuntime** comando:

### <a name="properties"></a>Properties
Nella tabella seguente vengono fornite descrizioni per le proprietà restituite dal cmdlet per un runtime di integrazione Azure:

| Proprietà | DESCRIZIONE |
-------- | ------------- | 
| Name | Nome del runtime di integrazione di Azure. |  
| State | Stato del runtime di integrazione di Azure. | 
| Location | Percorso del runtime di integrazione di Azure. Per altri dettagli sul percorso di un runtime di integrazione di Azure, vedere [Introduzione al runtime di integrazione](concepts-integration-runtime.md). |
| DataFactoryName | Nome della data factory a cui appartiene il runtime di integrazione di Azure. | 
| ResourceGroupName | Nome del gruppo di risorse a cui appartiene la data factory.  |
| Description | Descrizione del runtime di integrazione di Azure.  |

### <a name="status"></a>Stato
La tabella seguente indica i possibili stati di un runtime di integrazione di Azure:

| Stato | Commenti/Scenari | 
| ------ | ------------------ |
| Online | Il runtime di integrazione di Azure è online e pronto per essere utilizzato. | 
| Offline | Il runtime di integrazione di Azure è offline a causa di un errore interno. |

## <a name="self-hosted-integration-runtime"></a>Runtime di integrazione self-hosted
In questa sezione vengono fornite descrizioni per le proprietà restituite dal cmdlet Get-AzDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> La proprietà restituite e lo stato contengono informazioni sul runtime di integrazione self-hosted generale e su ogni nodo nel runtime.  

### <a name="properties"></a>Properties

La tabella seguente fornisce le descrizioni delle proprietà di monitoraggio per **ogni nodo**:

| Proprietà | DESCRIZIONE | 
| -------- | ----------- | 
| NOME | Nome del runtime di integrazione self-hosted e dei nodi associati. Il nodo è un computer Windows locale su cui è installato il runtime di integrazione self-hosted. |  
| Stato | Lo stato del runtime di integrazione self-hosted generale e di ogni nodo. Esempio: Online/Offline/Limitato e così via. Per informazioni su questi stati, vedere la sezione successiva. | 
| Versione | La versione del runtime di integrazione self-hosted e di ogni nodo. La versione del runtime di integrazione self-hosted viene determinata in base alla versione della maggior parte dei nodi del gruppo. Se nella configurazione del runtime di integrazione self-hosted sono presenti nodi con versioni diverse, solo i nodi con lo stesso numero di versione del runtime di integrazione self-hosted funzionano correttamente. Gli altri sono in modalità limitata e devono essere aggiornati manualmente (solo se l'aggiornamento automatico non riesce). | 
| Memoria disponibile | Memoria disponibile in un nodo di runtime di integrazione self-hosted. Questo valore è uno snapshot in tempo quasi reale. | 
| Uso della CPU | Utilizzo della CPU da parte di un nodo di runtime di integrazione self-hosted. Questo valore è uno snapshot in tempo quasi reale. |
| Rete (in/out) | Utilizzo del network da parte di un nodo di runtime di integrazione self-hosted. Questo valore è uno snapshot in tempo quasi reale. | 
| Processi simultanei (in esecuzione/limite) | **In esecuzione**. Numero di processi o di attività in esecuzione in ogni nodo. Questo valore è uno snapshot in tempo quasi reale. <br/><br/>**Limite**. Per limite si intende il numero massimo di processi simultanei per ogni nodo. Questo valore viene definito in base alle dimensioni del computer. È possibile aumentare il limite per incrementare il numero di processi simultanei in esecuzione negli scenari avanzati, in cui si verifica il timeout delle attività anche in caso di sottoutilizzo di CPU, memoria o rete. Questa capacità è disponibile anche in un runtime di integrazione self-hosted a nodo singolo. |
| Ruolo | Esistono due tipi di ruoli in un runtime di integrazione self-hosted a più nodi: dispatcher e ruolo di lavoro. Tutti i nodi sono ruoli di lavoro e quindi possono essere tutti usati per eseguire i processi. Esiste un solo nodo dispatcher, che viene usato per eseguire il pull di attività/processi dai servizi cloud e inviarli a nodi ruolo di lavoro diversi. Il nodo dispatcher è anche un nodo di lavoro. |

Alcune impostazioni delle proprietà sono più appropriate quando sono presenti due o più nodi nel runtime di integrazione self-hosted, ovvero in uno scenario in cui viene incrementato il numero di istanze.

#### <a name="concurrent-jobs-limit"></a>Limite di processi simultanei

Il valore predefinito del limite di processi simultanei è impostato in base alle dimensioni del computer. I fattori usati per calcolare questo valore variano in base alla quantità di RAM e al numero di core di CPU del computer. Pertanto, quanto maggiori sono il numero di core e la quantità di memoria, tanto più elevato sarà il valore predefinito del limite di processi simultanei.

Per aumentare questo limite è necessario incrementare il numero di nodi. In questo modo, infatti, il limite di processi simultanei corrisponde alla somma dei valori limite di processi simultanei di tutti i nodi disponibili.  Se ad esempio un nodo consente di eseguire un massimo di 12 processi simultanei, aggiungendo altri tre nodi simili sarà possibile eseguire un massimo di 48 processi simultanei, ovvero 4 x 12. È consigliabile aumentare il limite di processi simultanei solo quando si nota uno scarso utilizzo delle risorse con i valori predefiniti su ogni nodo.

È possibile sostituire il valore predefinito calcolato nel portale di Azure. Selezionare Autore > Connessioni > Runtime di integrazione > Modifica > Nodi > modificare il limite di processi simultanei per ogni nodo. È anche possibile usare il comando di PowerShell [update-Azdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) comando.
  
### <a name="status-per-node"></a>Stato (per ogni nodo)
La tabella seguente indica i possibili stati di un nodo di runtime di integrazione self-hosted:

| Stato | DESCRIZIONE |
| ------ | ------------------ | 
| Online | Il nodo è connesso al servizio Data Factory. |
| Offline | Il nodo è offline. |
| Aggiornamento | È in corso l'aggiornamento automatico del nodo. |
| Limitato | La causa è un problema di connettività, ad esempio un problema della porta HTTP 8050, di connettività del bus di servizio o di sincronizzazione delle credenziali. |
| Inactive | Il nodo è in una configurazione diversa da quella della maggior parte degli altri nodi. |

Un nodo può essere inattivo quando non riesce a connettersi agli altri nodi.

### <a name="status-overall-self-hosted-integration-runtime"></a>Stato (runtime di integrazione self-hosted generale)
La tabella seguente indica i possibili stati di un runtime di integrazione self-hosted. Questo stato varia a seconda degli stati di tutti i nodi che appartengono al runtime. 

| Stato | DESCRIZIONE |
| ------ | ----------- | 
| Need Registration | Nessun nodo è ancora registrato per questo runtime di integrazione self-hosted. |
| Online | Tutti i nodi sono online. |
| Offline | Nessun nodo è online. |
| Limitato | Non tutti i nodi in questo runtime di integrazione self-hosted sono in uno stato integro. Questo stato è un avviso indicante che qualche nodo potrebbe essere inattivo. La causa di questo stato potrebbe essere un problema di sincronizzazione delle credenziali nel nodo dispatcher/ruolo di lavoro. |

Usare la **Get-AzDataFactoryV2IntegrationRuntimeMetric** cmdlet per recuperare il payload JSON che contiene la modalità self-hosted le proprietà di runtime di integrazione, e i valori relativi snapshot durante la fase di esecuzione del cmdlet.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
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

### <a name="properties"></a>Properties

| Proprietà/stato | DESCRIZIONE |
| --------------- | ----------- |
| CreateTime | L'ora UTC in cui è stato creato il runtime di integrazione di Azure-SSIS. |
| Nodi | I nodi disponibili/allocati del runtime di integrazione SSIS di Azure con errori eseguibili e stati specifici del nodo (avvio/disponibile/riciclo/non disponibile). |
| OtherErrors | Gli errori eseguibili non specifici del nodo del runtime di integrazione SSIS di Azure. |
| LastOperation | Risultato dell'ultima operazione di avvio/arresto del runtime di integrazione SSIS di Azure con errori eseguibili se non è riuscita. |
| Stato | Lo stato generale (iniziale/fase di avvio/avvio/arresto/arrestato) del runtime di integrazione SSIS di Azure. |
| Località | Il percorso del runtime di integrazione SSIS di Azure. |
| NodeSize | Le dimensioni di ogni nodo del runtime di integrazione SSIS di Azure. |
| NodeCount | Il numero di nodi nel runtime di integrazione SSIS di Azure. |
| MaxParallelExecutionsPerNode | Il numero di esecuzioni parallele per ogni nodo nel runtime di integrazione SSIS di Azure. |
| CatalogServerEndpoint | L'endpoint del server di istanza gestita/database SQL di Azure esistente per ospitare il database SSIS. |
| CatalogAdminUserName | Il nome utente amministratore del server di istanza gestita/database SQL di Azure esistente. Il servizio Data Factory utilizza queste informazioni per preparare e gestire il database SSIS per conto dell'utente. |
| CatalogAdminPassword | La password amministratore del server di istanza gestita/database SQL di Azure esistente. |
| CatalogPricingTier | Il piano tariffario per il database SSIS ospitato dal server di database SQL di Azure esistente.  Non applicabile all'Istanza gestita di database SQL di Azure che ospita il database SSIS. |
| VNetId | L'ID della risorsa di rete virtuale per aggiungere un runtime di integrazione SSIS di Azure. |
| Subnet | Il nome della subnet per aggiungere un runtime di integrazione SSIS di Azure. |
| ID | L’ID di risorsa del runtime di integrazione SSIS di Azure. |
| Type | Il tipo (gestito/self-hosted) di runtime di integrazione SSIS di Azure. |
| ResourceGroupName | Il nome del gruppo di risorse di Azure, in cui sono stati creati la data factory e il runtime di integrazione SSIS di Azure. |
| DataFactoryName | Nome della data factory di Azure. |
| NOME | Il nome del runtime di integrazione SSIS di Azure. |
| DESCRIZIONE | La descrizione del runtime di integrazione SSIS di Azure. |

  
### <a name="status-per-node"></a>Stato (per ogni nodo)

| Stato | DESCRIZIONE |
| ------ | ----------- | 
| Avvio in corso | Questo nodo è in corso di preparazione. |
| Disponibile | Questo nodo è pronto per la distribuzione/esecuzione di pacchetti SSIS. |
| Riciclo | Questo nodo è in corso di riparazione/riavvio. |
| Non disponibile | Questo nodo non è pronto per la distribuzione/esecuzione di pacchetti SSIS da parte dell’utente e presenta errori eseguibili o problemi che è stato possibile risolvere. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Stato (generale del runtime di integrazione SSIS di Azure)

| Stato generale | DESCRIZIONE | 
| -------------- | ----------- | 
| Initial | I nodi del runtime di integrazione SSIS di Azure non sono stati allocati/preparati. | 
| Avvio in corso | L’allocazione/preparazione dei nodi del runtime di integrazione SSIS di Azure è in corso e la fatturazione è stata avviata. |
| Started | I nodi del runtime di integrazione SSIS di Azure sono stati allocati/preparati e sono pronti per poter distribuire o eseguire pacchetti SSIS. |
| Stopping  | I nodi del runtime di integrazione SSIS di Azure vengono rilasciati. |
| Arrestato | L’allocazione/preparazione dei nodi del runtime di integrazione SSIS di Azure è stata eseguita e la fatturazione è stata avviata. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Monitorare il runtime di integrazione SSIS di Azure nel portale di Azure

Le schermate seguenti illustrano come selezionare il runtime di integrazione SSIS di Azure per monitorare e fornire un esempio delle informazioni visualizzate.

![Selezionare il runtime di integrazione SSIS di Azure da monitorare](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Vedere le informazioni sul runtime di integrazione SSIS di Azure](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Monitorare il runtime di integrazione SSIS di Azure con PowerShell

Usare uno script simile a quello dell'esempio seguente per verificare lo stato del runtime di integrazione SSIS di Azure.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Altre informazioni sul runtime di integrazione di Azure-SSIS

Vedere gli articoli seguenti per ulteriori informazioni sul runtime di integrazione SSIS di Azure:

- [Runtime di integrazione SSIS di Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). In questo articolo vengono fornite le informazioni sui runtime di integrazione in generale incluso il runtime di integrazione SSIS di Azure. 
- [Esercitazione: distribuire i pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md). In questo articolo vengono fornite le istruzioni passo per passo per creare un runtime di integrazione SSIS di Azure e utilizzare un database SQL di Azure per ospitare il catalogo SSIS. 
- [Procedura: Creare un runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md). Questo articolo amplia l'esercitazione e offre istruzioni sull'uso dell'Istanza gestita di database SQL di Azure e sull'aggiunta del runtime di integrazione a una rete virtuale. 
- [Gestire un runtime di integrazione SSIS di Azure](manage-azure-ssis-integration-runtime.md). In questo articolo viene illustrato come arrestare, avviare o rimuovere un runtime di integrazione SSIS di Azure. Viene inoltre mostrato come scalare orizzontalmente il runtime di integrazione SSIS di Azure aggiungendo più nodi al runtime di integrazione. 
- [Aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md). Questo articolo offre informazioni concettuali sull'aggiunta di un runtime di integrazione Azure-SSIS a una rete virtuale di Azure. Indica inoltre i passaggi per usare il portale di Azure per configurare la rete virtuale in modo che il runtime di integrazione Azure-SSIS possa essere aggiunto alla rete virtuale. 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti per il monitoraggio di pipeline in modi diversi: 

- [Guida introduttiva: creare una data factory](quickstart-create-data-factory-dot-net.md).
- [Utilizzare Monitoraggio di Azure per monitorare le pipeline di Data Factory](monitor-using-azure-monitor.md)
