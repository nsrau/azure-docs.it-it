---
title: Controllare il traffico di rete in Azure HDInsight
description: Informazioni sulle tecniche per il controllo del traffico in ingresso e in uscita verso i cluster HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 031dbb0e8c9b9fb8dc37b264f9ba8e1186efc832
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783591"
---
# <a name="control-network-traffic-in-azure-hdinsight"></a>Controllare il traffico di rete in Azure HDInsight

Il traffico di rete nelle reti virtuali di Azure può essere controllato usando i modi seguenti:

* i **gruppi di sicurezza di rete** (NSG) consentono di filtrare il traffico di rete in ingresso e in uscita. Per altre informazioni, vedere il documento [Filtrare il traffico di rete con gruppi di sicurezza di rete](../virtual-network/security-overview.md).

* **Appliance virtuali di rete** (NVA) può essere usato solo con il traffico in uscita. Appliance virtuali replica la funzionalità di dispositivi quali firewall e router. Per altre informazioni, vedere il documento relativo alle [Appliance di rete](https://azure.microsoft.com/solutions/network-appliances).

In qualità di servizio gestito, HDInsight richiede l'accesso illimitato ai servizi di gestione e integrità HDInsight sia per il traffico in ingresso che in uscita dal VNET. Quando si usa gruppi, è necessario assicurarsi che questi servizi possano comunque comunicare con il cluster HDInsight.

![Diagramma delle entità HDInsight create in VNET personalizzati di Azure](./media/control-network-traffic/hdinsight-vnet-diagram.png)

## <a name="hdinsight-with-network-security-groups"></a>HDInsight con gruppi di sicurezza di rete

Se si prevede di usare i **gruppi di sicurezza di rete** per controllare il traffico di rete, eseguire le azioni seguenti prima di installare HDInsight:

1. Identificare l'area di Azure che si intende usare per HDInsight.

2. Identificare i tag di servizio richiesti da HDInsight per l'area geografica. Per altre informazioni, vedere [tag di servizio del gruppo di sicurezza di rete (NSG) per Azure HDInsight](hdinsight-service-tags.md).

3. Creare o modificare i gruppi di sicurezza di rete per la subnet in cui si intende installare HDInsight.

    * __Gruppi di sicurezza di rete__: consentono il traffico __in ingresso__ sulla porta __443__ dagli indirizzi IP. In questo modo, i servizi di gestione HDInsight possono raggiungere il cluster dall'esterno della rete virtuale.

Per ulteriori informazioni sui gruppi di sicurezza di rete, vedere la [Panoramica dei gruppi di sicurezza di rete](../virtual-network/security-overview.md).

## <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Controllo del traffico in uscita dai cluster HDInsight

Per altre informazioni sul controllo del traffico in uscita dai cluster HDInsight, vedere [configurare la restrizione del traffico di rete in uscita per i cluster HDInsight di Azure](hdinsight-restrict-outbound-traffic.md).

### <a name="forced-tunneling-to-on-premises"></a>Tunneling forzato in locale

Il tunneling forzato è una configurazione di routing definita dall'utente in cui tutto il traffico da una subnet è forzato a una rete o a una posizione specifica, ad esempio la rete locale o il firewall. _Non_ è consigliabile eseguire il tunneling forzato di tutto il trasferimento dei dati in locale a causa di grandi volumi di trasferimento dei dati e di potenziali conseguenze sulle prestazioni.

I clienti interessati alla configurazione del tunneling forzato devono usare i [Metastore personalizzati](./hdinsight-use-external-metadata-stores.md) e configurare la connettività appropriata dalla subnet del cluster o dalla rete locale a questi Metastore personalizzati.

Per vedere un esempio della configurazione di UDR con il firewall di Azure, vedere [configurare la restrizione del traffico di rete in uscita per i cluster HDInsight di Azure](hdinsight-restrict-outbound-traffic.md).

## <a name="required-ip-addresses"></a> Indirizzi IP richiesti

Se si usano gruppi di sicurezza di rete o route definite dall'utente per controllare il traffico, vedere [indirizzi IP di gestione di HDInsight](hdinsight-management-ip-addresses.md).

## <a name="required-ports"></a>Porte richieste

Se si intende usare un **firewall** e accedere al cluster dall'esterno su determinate porte, potrebbe essere necessario consentire il traffico sulle porte necessarie per lo scenario. Per impostazione predefinita, non è necessaria alcuna aggiunta speciale di porte, purché il traffico di gestione di Azure illustrato nella sezione precedente sia autorizzato a raggiungere il cluster sulla porta 443.

Per un elenco di porte per servizi specifici, vedere il documento [Porte usate dai servizi Apache Hadoop su HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Per altre informazioni sulle regole del firewall per le appliance virtuali, vedere il documento [Scenario dell'appliance virtuale](../virtual-network/virtual-network-scenario-udr-gw-nva.md).

## <a name="next-steps"></a>Passaggi successivi

* Per esempi di codice ed esempi di creazione di reti virtuali di Azure, vedere [creare reti virtuali per i cluster HDInsight](hdinsight-create-virtual-network.md)di Azure.
* Per un esempio completo di configurazione di HDInsight per la connessione a una rete locale, vedere [Connettere HDInsight alla rete locale](./connect-on-premises-network.md).
* Per altre informazioni sulle reti virtuali di Azure, vedere la [panoramica sulle reti virtuali di Azure](../virtual-network/virtual-networks-overview.md).
* Per altre informazioni sui gruppi di sicurezza di rete, vedere [Gruppi di sicurezza di rete](../virtual-network/security-overview.md).
* Per altre informazioni sulle route definite dall'utente, vedere [Route definite dall'utente e inoltro IP](../virtual-network/virtual-networks-udr-overview.md).
* Per ulteriori informazioni sulle reti virtuali, vedere [Plan reti virtuali for HDInsight](./hdinsight-plan-virtual-network-deployment.md).
