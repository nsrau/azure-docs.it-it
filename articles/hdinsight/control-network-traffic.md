---
title: Controllare il traffico di rete in Azure HDInsight
description: Informazioni sulle tecniche per controllare il traffico in ingresso e in uscita verso i cluster HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: a33bc5816ded7cdca75737b02add0a6ca8821700
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400195"
---
# <a name="control-network-traffic-in-azure-hdinsight"></a>Controllare il traffico di rete in Azure HDInsight

Il traffico di rete nelle reti virtuali di Azure può essere controllato usando i modi seguenti:

* i **gruppi di sicurezza di rete** (NSG) consentono di filtrare il traffico di rete in ingresso e in uscita. Per altre informazioni, vedere il documento [Filtrare il traffico di rete con gruppi di sicurezza di rete](../virtual-network/security-overview.md).

* **Appliance di rete virtuale** (NVA) può essere usato solo con il traffico in uscita. Le NVA replicano le funzionalità di dispositivi come i firewall e i router. Per altre informazioni, vedere il documento relativo alle [Appliance di rete](https://azure.microsoft.com/solutions/network-appliances).

Come servizio gestito, HDInsight richiede l'accesso senza restrizioni ai servizi di gestione e integrità di HDinsight sia per il traffico in ingresso che per quello in uscita dalla rete virtuale. Quando si usano i gruppi di sicurezza di rete è necessario assicurarsi che questi servizi possano ancora comunicare con il cluster HDInsight.

![Diagramma delle entità HDInsight create nella VNET personalizzata di Azure](./media/control-network-traffic/hdinsight-vnet-diagram.png)

## <a name="hdinsight-with-network-security-groups"></a>HDInsight con i gruppi di sicurezza di rete

Se si intende usare **gruppi di sicurezza di rete** per controllare il traffico di rete, eseguire le azioni seguenti prima di installare HDInsight:

1. Identificare l'area di Azure che si intende usare per HDInsight.

2. Identificare i tag del servizio richiesti da HDInsight per l'area geografica. Sono disponibili diversi modi per ottenere questi tag del servizio:
    1. Consultare l'elenco dei tag del servizio pubblicati nei [tag del servizio del gruppo di sicurezza di rete (NSG) per Azure HDInsight](hdinsight-service-tags.md). 
    2. Se l'area non è presente nell'elenco, usare l' [API di individuazione tag di servizio](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) per trovare un tag di servizio per l'area.
    3. Se non è possibile usare l'API, scaricare il [file JSON del tag del servizio](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) e cercare l'area desiderata.


3. Creare o modificare i gruppi di sicurezza di rete per la subnet in cui si intende installare HDInsight.

    * __Gruppi di sicurezza di rete__: consentono il traffico __in ingresso__ sulla porta __443__ dagli indirizzi IP. Ciò garantisce che i servizi di gestione di HDInsight possano raggiungere il cluster dall'esterno della rete virtuale. Per i cluster abilitati per __proxy REST Kafka__, consentire il traffico __in ingresso__ sulla porta __9400__. In questo modo si garantisce che il server proxy REST Kafka sia raggiungibile.

Per altre informazioni sui gruppi di sicurezza di rete, vedere [Che cos'è un gruppo di sicurezza di rete](../virtual-network/security-overview.md).

## <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Controllo del traffico in uscita dai cluster HDInsight

Per altre informazioni sul controllo del traffico in uscita dai cluster HDInsight, vedere [configurare il traffico di rete in uscita per i cluster di Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

### <a name="forced-tunneling-to-on-premises"></a>Tunneling forzato a istanze locali

Il tunneling forzato è una configurazione di routing definita dall'utente in cui tutto il traffico da una subnet viene spinto verso una rete o un percorso specifico, ad esempio la rete locale o il firewall. Il tunneling forzato di tutto il trasferimento dei dati in locale è _non_ consigliato a causa di volumi elevati di trasferimento dei dati e del potenziale impatto sulle prestazioni.

I clienti che sono interessati a configurare il tunneling forzato devono usare [metastore personalizzati](./hdinsight-use-external-metadata-stores.md) e configurare la connettività appropriata dalla subnet del cluster o dalla rete locale a questi metastore personalizzati.

Per un esempio della configurazione di UDR con il firewall di Azure, vedere [Configurare il traffico di rete in uscita per i cluster di Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

## <a name="required-ports"></a>Porte richieste

Se si intende usare un **firewall** e accedere al cluster dall'esterno su determinate porte, potrebbe essere necessario consentire il traffico sulle porte necessarie per lo scenario. Per impostazione predefinita, non è necessario definire uno speciale elenco delle porte consentite, purché al traffico di gestione di Azure illustrato nella sezione precedente venga consentito di raggiungere il cluster sulla porta 443.

Per un elenco di porte per servizi specifici, vedere il documento [Porte usate dai servizi Apache Hadoop su HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Per altre informazioni sulle regole del firewall per le appliance virtuali, vedere il documento [Scenario dell'appliance virtuale](../virtual-network/virtual-network-scenario-udr-gw-nva.md).

## <a name="next-steps"></a>Passaggi successivi

* Per esempi di codice ed esempi di creazione di reti virtuali di Azure, vedere [Creare reti virtuali per i cluster di Azure HDInsight](hdinsight-create-virtual-network.md).
* Per un esempio completo di configurazione di HDInsight per la connessione a una rete locale, vedere [Connettere HDInsight alla rete locale](./connect-on-premises-network.md).
* Per altre informazioni sulle reti virtuali di Azure, vedere la [panoramica sulle reti virtuali di Azure](../virtual-network/virtual-networks-overview.md).
* Per altre informazioni sui gruppi di sicurezza di rete, vedere [Gruppi di sicurezza di rete](../virtual-network/security-overview.md).
* Per altre informazioni sulle route definite dall'utente, vedere [Route definite dall'utente e inoltro IP](../virtual-network/virtual-networks-udr-overview.md).
* Per ulteriori informazioni sulle reti virtuali, vedere [Pianificare reti virtuali per HDInsight](./hdinsight-plan-virtual-network-deployment.md).
