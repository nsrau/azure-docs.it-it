---
title: Ripristino di emergenza per FarmBeatsDisaster recovery for FarmBeats
description: In questo articolo viene descritto come il recupero dei dati protegge dalla perdita dei dati.
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-umha
ms.openlocfilehash: d64735e683ba1133e7d381a68611d204c4068026
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313094"
---
# <a name="disaster-recovery-for-farmbeats"></a>Ripristino di emergenza per FarmBeatsDisaster recovery for FarmBeats

Il recupero dei dati protegge dalla perdita dei dati in un evento come la compressione dell'area di Azure.Data recovery ti protects from losing your data in an event like collapse of Azure region. In tal caso, è possibile avviare il failover e ripristinare i dati archiviati nella distribuzione farmBeats.

Il ripristino dei dati non è una funzionalità predefinita in Azure FarmBeats.Data recovery is not a default feature in Azure FarmBeats. È possibile configurare questa funzionalità manualmente configurando le risorse di Azure necessarie usate da FarmBeats per archiviare i dati in un'area associata ad Azure.You can configure this feature manually by configuring the required Azure resources that are used by FarmBeats to store data in an Azure paired region. Usa approccio attivo – passivo per abilitare il ripristino.

Nelle sezioni seguenti vengono fornite informazioni su come configurare il ripristino dei dati in Azure FarmBeats:The following sections provide information about how you can configure data recovery in Azure FarmBeats:

- [Abilitare la ridondanza dei datiEnable data redundancy](#enable-data-redundancy)
- [Ripristinare il servizio dal backup online](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>Abilitare la ridondanza dei datiEnable data redundancy

FarmBeats archivia i dati in tre servizi di Azure di prima parte, che sono **Archiviazione di Azure,** **Cosmos DB** e Time Series **Insights**. Usare la procedura seguente per abilitare la ridondanza dei dati per questi servizi in un'area di Azure abbinata:Use the following steps to enable data redundancy for these services to a paired Azure region:

1.  **Archiviazione di Azure:** seguire questa linea guida per abilitare la ridondanza dei dati per ogni account di archiviazione nella distribuzione di FarmBbeats.Azure Storage - Follow this guideline to enable data redundancy for each storage account in your FarmBbeats deployment.
2.  **Database Cosmos** di Azure: seguire questa linea guida per abilitare la ridondanza dei dati per l'account Cosmos DB distribuzione FarmBeats.Azure Cosmos DB - Follow this guide line to enable data redundancy for Cosmos DB account your FarmBeats deployment.
3.  **Azure Time Series Insights (TSI):** TSI attualmente non offre ridondanza dei dati. Per recuperare i dati di Time Series Insights, passare al sensore/partner meteo ed eseguire nuovamente il push dei dati nella distribuzione di FarmBeats.

## <a name="restore-service-from-online-backup"></a>Ripristinare il servizio dal backup online

È possibile avviare il failover e ripristinare i dati archiviati per i quali, ognuno degli archivi dati sopra indicati per la distribuzione di FarmBeats. Dopo aver ripristinato i dati per Archiviazione di Azure e Cosmos DB, creare un'altra distribuzione FarmBeats nell'area accoppiata di Azure e quindi configurare la nuova distribuzione per usare i dati degli archivi dati ripristinati (ad esempio Archiviazione di Azure e Cosmos DB) usando i passaggi seguenti:Once you've rerecupero the data for Azure storage and Cosmos DB, create another FarmBeats deployment in the Azure paired region and then configure the new deployment to use data from restored data stores (it e. Azure Storage and Cosmos DB) by using the below steps:

1. [Configurare Cosmos DB](#configure-cosmos-db)
2. [Configurare l'account di archiviazioneConfigure Storage Account](#configure-storage-account)


### <a name="configure-cosmos-db"></a>Configurare Cosmos DB

Copiare la chiave di accesso del database Cosmos ripristinato e aggiornare il nuovo Vault della chiave FarmBeats Datahub.


  ![Ripristino di emergenza](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> Copiare l'URL del database Cosmos ripristinato e aggiornarlo nella nuova configurazione del servizio app FarmBeats Datahub. È ora possibile eliminare l'account Cosmos DB nella nuova distribuzione di FarmBeats.You can now delete Cosmos DB account in the new FarmBeats deployment.

  ![Ripristino di emergenza](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>Configurare l'account di archiviazioneConfigure Storage Account

Copiare la chiave di accesso dell'account di archiviazione ripristinato e aggiornarla nel nuovo archivio chiavi FarmBeats Datahub.

![Ripristino di emergenza](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> Assicurarsi di aggiornare il nome dell'account di archiviazione nel nuovo file di configurazione della macchina virtuale FarmBeats Batch.Ensure to Update Storage Account name in the new FarmBeats Batch VM config file.

![Ripristino di emergenza](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

Analogamente, se è stato abilitato il ripristino dei dati per l'account di archiviazione di Accelerator, seguire il passaggio 2 per aggiornare la chiave di accesso e il nome dell'account di archiviazione dell'acceleratore nella nuova istanza di FarmBeats.Similarly, if you have enabled data recovery for your Accelerator storage account, follow the step 2 to update the accelerator Storage Account access key and name, in the new FarmBeats instance.
