---
title: Resilienza & disponibilità elevata
titleSuffix: Azure Machine Learning
description: Informazioni su come rendere le risorse di Azure Machine Learning più resilienti alle interruzioni usando una configurazione a disponibilità elevata.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 09/16/2020
ms.openlocfilehash: 64665c0b1e32970f29233f5abdd6b2d2d020a6b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90897517"
---
# <a name="increase-azure-machine-learning-resiliency"></a>Aumentare la resilienza Azure Machine Learning



In questo articolo si apprenderà come rendere più resilienti le risorse di Microsoft Azure Machine Learning usando configurazioni a disponibilità elevata. È possibile configurare i servizi di Azure da cui Azure Machine Learning dipende per la disponibilità elevata. Questo articolo identifica i servizi che è possibile configurare per la disponibilità elevata e i collegamenti a informazioni aggiuntive sulla configurazione di queste risorse.

> [!NOTE]
> Azure Machine Learning non offre un'opzione di ripristino di emergenza.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Informazioni sui servizi di Azure per Azure Machine Learning

Azure Machine Learning dipende da più servizi di Azure e presenta diversi livelli. Per alcuni di questi servizi viene effettuato il provisioning nella sottoscrizione (cliente). L'utente è responsabile della configurazione a disponibilità elevata di questi servizi. Altri servizi vengono creati in una sottoscrizione Microsoft e gestiti da Microsoft. 

I servizi di Azure includono:

* **Infrastruttura Azure Machine Learning**: ambiente gestito da Microsoft per l'area di lavoro Azure Machine Learning.

* **Risorse associate**: risorse sottoposte a provisioning nella sottoscrizione durante la creazione dell'area di lavoro Azure Machine Learning. Queste risorse includono archiviazione di Azure, Azure Key Vault, Azure Container Registry e Application Insights. Si è responsabili della configurazione delle impostazioni di disponibilità elevata per queste risorse.
  * L'archiviazione predefinita include dati come modello, dati del log di training e set di dati.
  * Key Vault dispone di credenziali per archiviazione di Azure, Container Registry e archivi dati.
  * Container Registry dispone di un'immagine Docker per il training e l'inferenza degli ambienti.
  * Application Insights è per il monitoraggio Azure Machine Learning.

* **Risorse di calcolo**: risorse create dopo la distribuzione dell'area di lavoro. Ad esempio, è possibile creare un'istanza di calcolo o un cluster di calcolo per eseguire il training di un modello di Machine Learning.
  * Istanza di calcolo e cluster di calcolo: ambienti di sviluppo di modelli gestiti da Microsoft.
  * Altre risorse: risorse Microsoft computing che è possibile aggiungere a Azure Machine Learning, ad esempio Azure Kubernetes Service (AKS), Azure Databricks, istanze di contenitore di Azure e Azure HDInsight. Si è responsabili della configurazione delle impostazioni di disponibilità elevata per queste risorse.

* **Archivi dati aggiuntivi**: Azure Machine Learning possibile montare archivi dati aggiuntivi, ad esempio archiviazione di azure, Azure Data Lake storage e database SQL di Azure per i dati di training.  Questi archivi dati vengono sottoposti a provisioning all'interno della sottoscrizione. Si è responsabili della configurazione delle impostazioni di disponibilità elevata.

La tabella seguente illustra i servizi di Azure gestiti da Microsoft, gestiti dall'utente e a disponibilità elevata per impostazione predefinita.

| Servizio | Gestito da | Disponibilità elevata per impostazione predefinita |
| ----- | ----- | ----- |
| **Infrastruttura Azure Machine Learning** | Microsoft | |
| **Risorse associate** |
| Archiviazione di Azure | Tu | |
| Key Vault | Tu | ✓ |
| Registro Container | Tu | |
| Application Insights | Tu | N/D |
| **Risorse di calcolo** |
| Istanza di calcolo | Microsoft |  |
| Cluster di calcolo | Microsoft |  |
| Altre risorse di calcolo, ad esempio AKS, <br>Azure Databricks, istanze di contenitore, HDInsight | Tu |  |
| **Archivi dati aggiuntivi** , ad esempio archiviazione di Azure, database SQL<br> Database di Azure per PostgreSQL, database di Azure per MySQL, <br>File System Azure Databricks | Tu | |

Nella parte restante di questo articolo vengono descritte le azioni che è necessario eseguire per garantire la disponibilità elevata di ognuno di questi servizi.

## <a name="associated-resources"></a>Risorse associate

> [!IMPORTANT]
> Azure Machine Learning non supporta il failover dell'account di archiviazione predefinito con archiviazione con ridondanza geografica (GRS), archiviazione con ridondanza geografica (GZRS), archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) o archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS).

Assicurarsi di configurare le impostazioni di disponibilità elevata di ogni risorsa facendo riferimento alla documentazione seguente:

* **Archiviazione di Azure**: per configurare le impostazioni di disponibilità elevata, vedere [ridondanza di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).
* **Key Vault**: Key Vault fornisce disponibilità elevata per impostazione predefinita e non richiede alcuna azione da utente.  Vedere [Azure Key Vault disponibilità e ridondanza](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance).
* **Container Registry**: scegliere l'opzione del registro di sistema Premium per la replica geografica. Vedere [replica geografica in Azure container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
* **Application Insights**: Application Insights non fornisce impostazioni di disponibilità elevata. Per modificare il periodo di memorizzazione dei dati e i dettagli, vedere [raccolta dati, conservazione e archiviazione in Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#how-long-is-the-data-kept).

## <a name="compute-resources"></a>Risorse di calcolo

Assicurarsi di configurare le impostazioni di disponibilità elevata di ogni risorsa facendo riferimento alla documentazione seguente:

* **Servizio Azure Kubernetes**: vedere le [procedure consigliate per la continuità aziendale e il ripristino di emergenza in Azure KUBERNETES Service (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) e [creare un cluster Azure Kubernetes Service (AKS) che usa le zone di disponibilità](https://docs.microsoft.com/azure/aks/availability-zones). Se il cluster AKS è stato creato usando il Azure Machine Learning Studio, SDK o l'interfaccia della riga di comando, la disponibilità elevata tra aree non è supportata.
* **Azure Databricks**: vedere [ripristino di emergenza a livello di area per Azure Databricks cluster](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).
* **Istanze di contenitore**: un agente di orchestrazione è responsabile del failover. Vedere [istanze di contenitore di Azure e agenti di orchestrazione di contenitori](https://docs.microsoft.com/azure/container-instances/container-instances-orchestrator-relationship).
* **HDInsight**: vedere [Servizi a disponibilità elevata supportati da Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-components).

## <a name="additional-data-stores"></a>Archivi dati aggiuntivi

Assicurarsi di configurare le impostazioni di disponibilità elevata di ogni risorsa facendo riferimento alla documentazione seguente:

* **Contenitore BLOB di Azure/file di Azure/Data Lake storage Gen2**: uguale alla risorsa di archiviazione predefinita.
* **Data Lake storage Gen1**: vedere [le linee guida per la disponibilità elevata e il ripristino di emergenza per data Lake storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-disaster-recovery-guidance).
* **Database SQL**: vedere [disponibilità elevata per database SQL di Azure e SQL istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability).
* **Database di Azure per PostgreSQL**: vedere [concetti relativi alla disponibilità elevata in database di Azure per PostgreSQL-server singolo](https://docs.microsoft.com/azure/postgresql/concepts-high-availability).
* **Database di Azure per MySQL**: vedere [comprendere la continuità aziendale nel database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/concepts-business-continuity).
* **Azure Databricks file System**: vedere [ripristino di emergenza a livello di area per Azure Databricks cluster](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Se si fornisce una chiave gestita dal cliente per distribuire un'area di lavoro Azure Machine Learning, viene anche eseguito il provisioning di Azure Cosmos DB all'interno della sottoscrizione. In tal caso, si è responsabili della configurazione delle impostazioni di disponibilità elevata. Vedere [disponibilità elevata con Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability).

## <a name="next-steps"></a>Passaggi successivi

Per distribuire Azure Machine Learning con le risorse associate con le impostazioni di disponibilità elevata, usare un [modello di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced).
