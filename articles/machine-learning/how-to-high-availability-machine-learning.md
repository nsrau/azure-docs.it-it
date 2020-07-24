---
title: Come aumentare la resilienza
titleSuffix: Azure Machine Learning
description: Informazioni su come rendere le risorse Azure Machine Learning correlate più resilienti alle interruzioni usando una configurazione a disponibilità elevata.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 07/16/2020
ms.openlocfilehash: 4f2bf239e1157f5c927c4449857ad5f7793ccb49
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098909"
---
# <a name="increase-the-resiliency-of-azure-machine-learning"></a>Aumentare la resilienza di Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come rendere più resilienti le risorse Azure Machine Learning correlate usando configurazioni a disponibilità elevata. I servizi di Azure da cui dipende Azure Machine Learning possono essere configurati per la disponibilità elevata. Questo articolo fornisce informazioni sui servizi che possono essere configurati per la disponibilità elevata e collegamenti a informazioni sulla configurazione di queste risorse.

> [!NOTE]
> Azure Machine Learning non offre un'opzione di ripristino di emergenza.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Informazioni sui servizi di Azure per Azure Machine Learning

Azure Machine Learning dipende da più servizi di Azure e presenta diversi livelli. Ne viene eseguito il provisioning nella sottoscrizione (cliente). L'utente è responsabile della configurazione della disponibilità elevata di questi servizi. Alcuni vengono creati in una sottoscrizione Microsoft e sono gestiti da Microsoft.

* **Infrastruttura Azure Machine Learning**: ambiente gestito da Microsoft per Azure machine learning area di lavoro.

* **Risorse associate**: risorse sottoposte a provisioning nella sottoscrizione durante la creazione dell'area di lavoro Azure Machine Learning. Includono archiviazione di Azure, Azure Key Vault, Azure Container Registry (ACR) e Application Insights. L'utente è responsabile delle impostazioni di disponibilità elevata per queste risorse.
  * L'archiviazione predefinita include dati come modello, dati del log di training e set di dati.
  * Key Vault dispone di credenziali per archiviazione, ACR, archivi dati.
  * ACR ha un'immagine Docker per il training e l'inferenza dell'ambiente.
  * Application Insights è per il monitoraggio Azure Machine Learning.

* **Risorse di calcolo**: risorse create dopo la distribuzione dell'area di lavoro. Ad esempio, è possibile creare un'istanza di calcolo o un cluster di calcolo per eseguire il training di un modello di machine learning.
  * Istanza di calcolo e cluster di calcolo: ambiente di sviluppo di modelli gestiti da Microsoft.
  * Altre risorse: si tratta delle risorse di calcolo che possono essere associate a Azure Machine Learning come Azure Kubernetes Service (AKS), Azure Databricks, istanze di contenitore di Azure (ACI) e HDInsight. L'utente è responsabile dell'impostazione della disponibilità elevata.

* **Archivi dati aggiuntivi**: Azure Machine Learning possibile montare archivi dati aggiuntivi, ad esempio archiviazione di azure, Azure Data Lake storage e database SQL di Azure per i dati di training.  Si trovano all'interno della sottoscrizione e l'utente è responsabile dell'impostazione della disponibilità elevata.

Nella tabella seguente vengono illustrati i servizi gestiti da Microsoft, gestiti dall'utente e a disponibilità elevata per impostazione predefinita:

| Service | Gestito da | HA per impostazione predefinita |
| ----- | ----- | ----- |
| **Infrastruttura Azure Machine Learning** | Microsoft | |
| **Risorse associate** |
| Archiviazione di Azure | Tu | |
| Insieme di credenziali chiave di Azure | Tu | ✓ |
| Registro Azure Container | Tu | |
| Application Insights | Tu | ND |
| **Risorse di calcolo** |
| Istanza di calcolo | Microsoft |  |
| Cluster di calcolo | Microsoft |  |
| Altre risorse, ad esempio il servizio Azure Kubernetes, <br>Azure Databricks, istanza di contenitore di Azure, Azure HDInsight | Tu |  |
| **Archivi dati aggiuntivi** , ad esempio archiviazione di Azure, database SQL di Azure<br> Database di Azure per PostgreSQL, database di Azure per MySQL, <br>Azure Databricks file system | Tu | |

Usare le informazioni riportate nella parte restante di questo documento per apprendere le azioni necessarie per rendere i servizi a disponibilità elevata.

## <a name="associated-resources"></a>Risorse associate

> [!IMPORTANT]
> Azure Machine Learning non supporta il failover dell'account di archiviazione predefinito con archiviazione con ridondanza geografica (GRS) o archiviazione con ridondanza geografica (GZRS) o archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) o archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS).

Verificare l'impostazione della disponibilità elevata di ogni risorsa con le informazioni seguenti.

* **Archiviazione di Azure**: per configurare un'impostazione di disponibilità elevata, vedere [ridondanza di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).
* **Azure Key Vault**: fornisce un servizio a disponibilità elevata predefinito e non è richiesta alcuna azione da utente.  Vedere [Azure Key Vault disponibilità e ridondanza](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance).
* **Azure container Registry**: scegliere lo SKU Premium per la replica geografica. Vedere [replica geografica in Azure container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
* **Application Insights**: non fornisce un'impostazione di disponibilità elevata. È possibile ottimizzare il periodo di conservazione dei dati e i dettagli nella [raccolta, conservazione e archiviazione dei dati in Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#how-long-is-the-data-kept).

## <a name="compute-resources"></a>Risorse di calcolo

Verificare l'impostazione della disponibilità elevata di ogni risorsa con la documentazione seguente.

* **Servizio Azure Kubernetes**: vedere le [procedure consigliate per la continuità aziendale e il ripristino di emergenza in Azure KUBERNETES Service (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) e [creare un cluster Azure Kubernetes Service (AKS) che usa le zone di disponibilità](https://docs.microsoft.com/azure/aks/availability-zones). Se il cluster AKS è stato creato da Azure Machine Learning (usando l'interfaccia della riga di comando di studio, SDK o ML), la disponibilità elevata tra aree non è supportata.
* **Azure Databricks**: vedere [ripristino di emergenza a livello di area per Azure Databricks cluster](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).
* **Istanza di contenitore di Azure**: l'agente di orchestrazione ACI è responsabile del failover. Vedere [istanze di contenitore di Azure e agenti di orchestrazione di contenitori](https://docs.microsoft.com/azure/container-instances/container-instances-orchestrator-relationship).
* **Azure HDInsight**: vedere [Servizi a disponibilità elevata supportati da Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-components).

## <a name="additional-data-stores"></a>Archivi dati aggiuntivi

Verificare l'impostazione della disponibilità elevata di ogni risorsa con la documentazione seguente.

* **Contenitore BLOB di Azure/condivisione file di Azure/Azure Data Lake Gen2**: uguale alla risorsa di archiviazione predefinita.
* **Azure Data Lake Gen1**: vedere le[linee guida per il ripristino di emergenza per i dati Azure Data Lake storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-disaster-recovery-guidance).
* **Database SQL di Azure**: vedere [disponibilità elevata e database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability).
* **Database di Azure per PostgreSQL**: vedere [concetti relativi alla disponibilità elevata in database di Azure per PostgreSQL-server singolo](https://docs.microsoft.com/azure/postgresql/concepts-high-availability).
* **Database di Azure per MySQL**: vedere [comprendere la continuità aziendale nel database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/concepts-business-continuity).
* **File System di databricks**: vedere [ripristino di emergenza a livello di area per i cluster Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Se si specifica una chiave personalizzata (chiave gestita dal cliente) per distribuire Azure Machine Learning area di lavoro, viene anche eseguito il provisioning di Cosmos DB all'interno della sottoscrizione. In tal caso, l'utente è responsabile della disponibilità elevata. Vedere [disponibilità elevata con Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability)

## <a name="next-steps"></a>Passaggi successivi

Per distribuire Azure Machine Learning con le risorse associate con le impostazioni di disponibilità elevata, usare un [modello di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced).