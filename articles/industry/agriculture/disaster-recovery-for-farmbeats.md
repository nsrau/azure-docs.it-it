---
title: Ripristino di emergenza per FarmBeats
description: Questo articolo descrive il modo in cui il ripristino dei dati protegge dalla perdita di dati.
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-umha
ms.openlocfilehash: 1665c535d4b1fb6190ee5736b688b402f8b4a541
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683901"
---
# <a name="disaster-recovery-for-farmbeats"></a>Ripristino di emergenza per FarmBeats

Il ripristino dei dati protegge dalla perdita di dati in un evento come il collasso di un'area di Azure. In tal caso, è possibile avviare il failover e recuperare i dati archiviati nella distribuzione di FarmBeats.

Il ripristino dei dati non è una funzionalità predefinita di Azure FarmBeats. Questa funzionalità può essere configurata manualmente configurando le risorse di Azure necessarie usate da FarmBeats per archiviare i dati in un'area abbinata di Azure. Usare l'approccio attivo-passivo per abilitare il ripristino.

Le sezioni seguenti forniscono informazioni su come è possibile configurare il ripristino dei dati in Azure FarmBeats:

- [Abilita ridondanza dei dati](#enable-data-redundancy)
- [Ripristinare il servizio dal backup online](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>Abilita ridondanza dei dati

FarmBeats archivia i dati in tre servizi di prima entità di Azure, ovvero **archiviazione di Azure**, **Cosmos DB** e **Time Series Insights**. Usare la procedura seguente per abilitare la ridondanza dei dati per questi servizi in un'area di Azure abbinata:

1.  **Archiviazione di Azure** : seguire questa guida per abilitare la ridondanza dei dati per ogni account di archiviazione nella distribuzione di FarmBeats.
2.  **Azure Cosmos DB** : seguire questa guida per abilitare la ridondanza dei dati per Cosmos DB account per la distribuzione di FarmBeats.
3.  **Azure Time Series Insights (TSI)** : TSI attualmente non offre ridondanza dei dati. Per ripristinare i dati di Time Series Insights, passare a Sensor/partner Meteo ed eseguire di nuovo il push dei dati alla distribuzione di FarmBeats.

## <a name="restore-service-from-online-backup"></a>Ripristinare il servizio dal backup online

È possibile avviare il failover e ripristinare i dati archiviati per i quali, ciascuno degli archivi dati menzionati in precedenza per la distribuzione di FarmBeats. Dopo aver recuperato i dati per archiviazione di Azure e Cosmos DB, creare un'altra distribuzione di FarmBeats nell'area abbinata di Azure e quindi configurare la nuova distribuzione per usare i dati degli archivi dati ripristinati, ad esempio archiviazione di Azure e Cosmos DB, usando i passaggi seguenti:

1. [Configurare Cosmos DB](#configure-cosmos-db)
2. [Configura account di archiviazione](#configure-storage-account)


### <a name="configure-cosmos-db"></a>Configurare Cosmos DB

Copiare la chiave di accesso del Cosmos DB ripristinato e aggiornare il nuovo Key Vault FarmBeats datahub.


  ![Ripristino di emergenza](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> Copiare l'URL del Cosmos DB ripristinato e aggiornarlo nella nuova configurazione del servizio app FarmBeats datahub. È ora possibile eliminare Cosmos DB account nella nuova distribuzione di FarmBeats.

  ![Ripristino di emergenza](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>Configura account di archiviazione

Copiare la chiave di accesso dell'account di archiviazione ripristinato e aggiornarla nel nuovo FarmBeats datahub Key Vault.

![Ripristino di emergenza](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> Assicurarsi di aggiornare il nome dell'account di archiviazione nel nuovo file di configurazione della macchina virtuale FarmBeats batch.

![Ripristino di emergenza](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

Analogamente, se è stato abilitato il ripristino dei dati per l'account di archiviazione acceleratore, seguire il passaggio 2 per aggiornare la chiave di accesso e il nome dell'account di archiviazione dell'acceleratore nella nuova istanza di FarmBeats.
