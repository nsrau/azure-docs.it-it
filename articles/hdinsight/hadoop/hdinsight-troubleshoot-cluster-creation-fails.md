---
title: Risolvere gli errori di creazione del cluster con Azure HDInsight
description: Informazioni su come risolvere i problemi di creazione del cluster per Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.openlocfilehash: 476b8cff23d09d81fe356a6445e27794b267d9a2
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998097"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Risolvere gli errori di creazione del cluster con Azure HDInsight

I problemi seguenti sono le cause principali più comuni per gli errori di creazione del cluster:

- Problemi di autorizzazione
- Restrizioni dei criteri delle risorse
- Firewall
- Blocchi delle risorse
- Versioni di componenti non supportate
- Restrizioni relative ai nomi degli account di archiviazione
- Interruzioni del servizio

## <a name="permissions-issues"></a>Problemi con le autorizzazioni

Se si usa Azure Data Lake Storage generazione 2 e viene visualizzato l'errore "questa richiesta non è autorizzata a eseguire questa operazione con questa autorizzazione", aprire il portale di Azure, passare all'account di archiviazione e in controllo di accesso (IAM) assicurarsi che il **BLOB di archiviazione Al collaboratore dati** o al ruolo **proprietario dati BLOB di archiviazione** è stato assegnato l'accesso all' **identità gestita assegnata dall'utente** per la sottoscrizione. Per istruzioni dettagliate, vedere [configurare le autorizzazioni per l'identità gestita nell'account data Lake storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account) .

Se si usa Data Lake Storage generazione 1, vedere le istruzioni per l'installazione e la configurazione [qui](../hdinsight-hadoop-use-data-lake-store.md). Data Lake Storage generazione 1 non è supportata per i cluster HBase e non è supportata nella versione 4,0 di HDInsight.

Se si usa archiviazione di Azure, assicurarsi che il nome dell'account di archiviazione sia valido durante la creazione del cluster.

## <a name="resource-policy-restrictions"></a>Restrizioni dei criteri delle risorse

I criteri di Azure basati su sottoscrizioni possono negare la creazione di indirizzi IP pubblici. La creazione del cluster HDInsight richiede due indirizzi IP pubblici.  

In generale, i criteri seguenti possono avere un effetto sulla creazione del cluster:

* Criteri che impediscono la creazione di indirizzi IP & i bilanciamenti del carico all'interno della sottoscrizione.
* Criteri che impediscono la creazione dell'account di archiviazione.
* Criteri che impediscono l'eliminazione di risorse di rete (/Load di indirizzi IP).

## <a name="firewalls"></a>Firewall

I firewall nella rete virtuale o nell'account di archiviazione possono negare la comunicazione con gli indirizzi IP di gestione di HDInsight.

Consentire il traffico dagli indirizzi IP nella tabella seguente.

| Indirizzo IP origine | Destination | Direction |
|---|---|---|
| 168.61.49.99 | *: 443 | In ingresso |
| 23.99.5.239 | *: 443 | In ingresso |
| 168.61.48.131 | *: 443 | In ingresso |
| 138.91.141.162 | *: 443 | In ingresso |

Aggiungere anche gli indirizzi IP specifici dell'area in cui viene creato il cluster. Per un elenco degli indirizzi per ogni area di Azure, vedere [indirizzi IP di gestione di HDInsight](../hdinsight-management-ip-addresses.md) .

Se si usa una route Express o un server DNS personalizzato, vedere [pianificare una rete virtuale per Azure HDInsight-connessione di più reti](../hdinsight-plan-virtual-network-deployment.md#multinet).

## <a name="resources-locks"></a>Blocchi di risorse  

Assicurarsi che non siano presenti [blocchi per la rete virtuale e il gruppo di risorse](../../azure-resource-manager/resource-group-lock-resources.md).  

## <a name="unsupported-component-versions"></a>Versioni di componenti non supportate

Assicurarsi di usare una [versione supportata di Azure HDInsight](../hdinsight-component-versioning.md) e tutti i [componenti di Apache Hadoop](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions) nella soluzione.  

## <a name="storage-account-name-restrictions"></a>Restrizioni relative ai nomi degli account di archiviazione

I nomi degli account di archiviazione non possono contenere più di 24 caratteri e non possono contenere un carattere speciale. Queste restrizioni si applicano anche al nome del contenitore predefinito nell'account di archiviazione.

Altre restrizioni di denominazione si applicano anche per la creazione del cluster. Per ulteriori informazioni, vedere [restrizioni relative ai nomi di cluster](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name).

## <a name="service-outages"></a>Interruzioni del servizio

Controllare [lo stato di Azure](https://status.azure.com/status) per eventuali potenziali interruzioni o problemi del servizio.

## <a name="next-steps"></a>Passaggi successivi

* [Estendere Azure HDInsight usando Rete virtuale di Azure](../hdinsight-plan-virtual-network-deployment.md)
* [Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Usare una risorsa di archiviazione di Azure con cluster Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
* [Configurare i cluster di HDInsight con Apache Hadoop, Apache Spark, Apache Kafka e altro ancora](../hdinsight-hadoop-provision-linux-clusters.md)
