---
title: Crittografia di Azure HDInsight in transito
description: Informazioni sulle funzionalità di sicurezza per fornire la crittografia in transito per il cluster HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: ac210adbc29075f27739b08dcf83eeedbeea3ed5
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630363"
---
# <a name="ipsec-encryption-in-transit-for-azure-hdinsight"></a>Crittografia IPSec in transito per Azure HDInsight

Questo articolo illustra l'implementazione della crittografia in transito per la comunicazione tra i nodi del cluster HDInsight di Azure.

## <a name="background"></a>Sfondo

Azure HDInsight offre un'ampia gamma di funzionalità di sicurezza per la protezione dei dati aziendali. Queste soluzioni sono raggruppate in base ai pilastri di sicurezza perimetrale, autenticazione, autorizzazione, controllo, crittografia e conformità. La crittografia può essere applicata ai dati inattivi e in transito.

La crittografia dei computer inattivi è coperta dalla crittografia lato server negli account di archiviazione di Azure, nonché dalla crittografia del disco nelle macchine virtuali di Azure che fanno parte del cluster HDInsight.

La crittografia dei dati in transito in HDInsight viene eseguita con [Transport Layer Security (TLS)](../transport-layer-security.md) per accedere ai gateway del cluster e a [Internet Protocol Security (IPSec)](https://wikipedia.org/wiki/IPsec) tra i nodi del cluster. È possibile abilitare facoltativamente IPSec tra tutti i nodi head, i nodi del ruolo di lavoro, i nodi perimetrali e i nodi Zookeeper. Non è abilitata per il traffico tra i nodi gateway o [ID Broker](./identity-broker.md) che sono macchine virtuali basate su Windows e altri nodi basati su Linux nel cluster.

## <a name="enable-encryption-in-transit"></a>Abilitare la crittografia in transito

### <a name="azure-portal"></a>Portale di Azure

Per creare un nuovo cluster con la crittografia in transito abilitata usando il portale di Azure, seguire questa procedura:

1. Avviare il normale processo di creazione del cluster. Vedere [creare cluster basati su Linux in HDInsight usando il portale di Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) per i passaggi iniziali per la creazione del cluster.
1. Completare le schede **nozioni di base** e **archiviazione** . Passare alla scheda **sicurezza e rete** .

    :::image type="content" source="media/encryption-in-transit/create-cluster-security-networking-tab.png" alt-text="Scheda Crea cluster-sicurezza e rete.":::

1. Nella scheda **sicurezza e rete** selezionare la casella **di controllo Abilita crittografia in transito** .

    :::image type="content" source="media/encryption-in-transit/enable-encryption-in-transit.png" alt-text="Crea cluster: Abilita la crittografia in transito.":::

### <a name="create-a-cluster-with-encryption-in-transit-enabled-through-the-azure-cli"></a>Creare un cluster con la crittografia in transito abilitata tramite l'interfaccia della riga di comando di Azure

La crittografia in transito viene abilitata tramite la `isEncryptionInTransitEnabled` Proprietà.

È possibile [scaricare un modello di esempio e un file di parametri](https://github.com/Azure-Samples/hdinsight-enterprise-security). Prima di usare il modello e il frammento di codice dell'interfaccia della riga di comando di Azure, sostituire i segnaposto seguenti con i valori corretti:

| Segnaposto | Descrizione |
|---|---|
| `<SUBSCRIPTION_ID>` | ID della sottoscrizione di Azure |
| `<RESOURCE_GROUP>` | Il gruppo di risorse in cui si vuole creare il nuovo cluster e l'account di archiviazione. |
| `<STORAGEACCOUNTNAME>` | L'account di archiviazione esistente da usare con il cluster. Il nome deve essere nel formato `ACCOUNTNAME.blob.core.windows.net` |
| `<CLUSTERNAME>` | Il nome del cluster HDInsight. |
| `<PASSWORD>` | Password scelta per l'accesso al cluster tramite SSH e il dashboard di Ambari. |
| `<VNET_NAME>` | Rete virtuale in cui verrà distribuito il cluster. |

Il frammento di codice seguente esegue i passaggi iniziali seguenti:

1. Accede all'account Azure.
1. Imposta la sottoscrizione attiva in cui verranno eseguite le operazioni di creazione.
1. Consente di creare un nuovo gruppo di risorse per le nuove attività di distribuzione.
1. Distribuire il modello per creare un nuovo cluster.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus2

az group deployment create --name HDInsightEnterpriseSecDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-enterprise-security.json \
    --parameters parameters.json
```

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica della sicurezza aziendale in Azure HDInsight](hdinsight-security-overview.md)
* [Sincronizzare Azure Active Directory utenti a un cluster HDInsight](../disk-encryption.md).
