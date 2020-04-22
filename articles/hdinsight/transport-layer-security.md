---
title: Sicurezza del livello di trasporto in Azure HDInsightTransport layer security in Azure HDInsight
description: Transport layer security (TLS) e Secure Sockets Layer (SSL) sono protocolli crittografici che forniscono la sicurezza delle comunicazioni su una rete di computer.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: fbe602581ebcea6385fb9cc9953d8e48272ce429
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771963"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Sicurezza del livello di trasporto in Azure HDInsightTransport layer security in Azure HDInsight

Le connessioni al cluster HDInsight `https://CLUSTERNAME.azurehdinsight.net` tramite l'endpoint del cluster pubblico vengono propesie via proxy tramite i nodi gateway del cluster. Queste connessioni sono protette utilizzando un protocollo denominato TLS. L'applicazione di versioni successive di TLS sui gateway migliora la sicurezza per queste connessioni. Per ulteriori informazioni sui motivi per cui è consigliabile utilizzare le versioni più recenti di TLS, vedere [Risoluzione del problema TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).

Per impostazione predefinita, i cluster di Azure HDInsight accettano connessioni TLS 1.2 su endpoint HTTPS pubblici e versioni precedenti per la compatibilità con le versioni precedenti. È possibile controllare la versione TLS minima supportata nei nodi del gateway durante la creazione del cluster usando il portale di Azure o un modello di Resource Manager.You can control the minimum TLS version supported on the gateway nodes during cluster creation using either the Azure portal, or a Resource Manager template. Per il portale, selezionare la versione TLS dalla scheda **Sicurezza e rete** durante la creazione del cluster. Per un modello di Resource Manager in fase di distribuzione, usare la proprietà **minSupportedTlsVersion.For** a Resource Manager template at deployment time, use the minSupportedTlsVersion property. Per un modello di esempio, vedere Modello minimo di guida TLS 1.2 di HDInsight.For a sample template, see [HDInsight minimum TLS 1.2 Quickstart template](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls). Questa proprietà supporta tre valori: "1.0", "1.1" e "1.2", che corrispondono rispettivamente a TLS 1.0, TLS 1.1 e TLS 1.2.

> [!IMPORTANT]
> A partire dal 30 giugno 2020, Azure HDInsight applicherà TLS 1.2 o versioni successive per tutte le connessioni HTTPS. È consigliabile assicurarsi che tutti i client siano pronti per gestire TLS 1.2 o versioni successive. Per altre informazioni, vedere Applicazione di [Azure HDInsight TLS 1.2.For](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/)more information, see Azure HDInsight TLS 1.2 Enforcement .

## <a name="next-steps"></a>Passaggi successivi

* [Pianificare una rete virtuale per Azure HDInsightPlan a virtual network for Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)
* [Creare reti virtuali per i cluster di Azure HDInsight.Create virtual networks for Azure HDInsight clusters](hdinsight-create-virtual-network.md).
* [Gruppi di sicurezza di rete](../virtual-network/security-overview.md).
