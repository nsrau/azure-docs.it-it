---
title: Transport Layer Security in Azure HDInsight
description: Transport Layer Security (TLS) e SSL (Secure Sockets Layer) sono protocolli crittografici che garantiscono la protezione delle comunicazioni su una rete di computer.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: b74ca75b26d4d98c79091683f428eb39e5827665
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82183501"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Transport Layer Security in Azure HDInsight

Le connessioni al cluster HDInsight tramite l'endpoint del cluster pubblico `https://CLUSTERNAME.azurehdinsight.net` vengono trasmesse tramite proxy tramite nodi del gateway del cluster. Queste connessioni sono protette usando un protocollo denominato TLS. L'applicazione di versioni superiori di TLS nei gateway migliora la sicurezza per queste connessioni. Per ulteriori informazioni sui motivi per cui è consigliabile utilizzare le versioni più recenti di TLS, vedere la pagina relativa [alla risoluzione del problema tls 1,0](https://docs.microsoft.com/security/solving-tls1-problem).

Per impostazione predefinita, i cluster Azure HDInsight accettano le connessioni TLS 1,2 sugli endpoint HTTPS pubblici e le versioni precedenti per la compatibilità con le versioni precedenti. È possibile controllare la versione minima di TLS supportata nei nodi del gateway durante la creazione del cluster usando il portale di Azure o un modello di Gestione risorse. Per il portale, selezionare la versione TLS dalla scheda **sicurezza e rete** durante la creazione del cluster. Per un modello di Gestione risorse in fase di distribuzione, utilizzare la proprietà **minSupportedTlsVersion** . Per un modello di esempio, vedere il [modello di avvio rapido di TLS 1,2 di HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls). Questa proprietà supporta tre valori: "1,0", "1,1" e "1,2", che corrispondono rispettivamente a TLS 1.0 +, TLS 1.1 + e TLS 1.2 +.

> [!IMPORTANT]
> A partire dal 30 giugno 2020, Azure HDInsight imporrà TLS 1,2 o versioni successive per tutte le connessioni HTTPS. Si consiglia di assicurarsi che tutti i client siano pronti per gestire TLS 1,2 o versioni successive. Per altre informazioni, vedere [Azure HDINSIGHT TLS 1,2 imposizione](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/).

## <a name="next-steps"></a>Passaggi successivi

* [Pianificare una rete virtuale per Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)
* [Creare reti virtuali per i cluster HDInsight di Azure](hdinsight-create-virtual-network.md).
* [Gruppi di sicurezza di rete](../virtual-network/security-overview.md).
