---
title: Architetture di riferimento per la protezione DDoS di Azure
description: Informazioni sulle architetture di riferimento per la protezione DDoS di Azure.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 3371b9cc0848e387c0150ca9aa7e7a971cecba1a
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905395"
---
# <a name="ddos-protection-reference-architectures"></a>Architetture di riferimento di Protezione DDoS

Protezione DDoS standard è progettato [per i servizi distribuiti in una rete virtuale](/azure/virtual-network/virtual-network-for-azure-services). Per altri servizi si applica il servizio Protezione DDoS Basic predefinito. Le architetture di riferimento seguenti sono indicate per scenari, con i modelli di architettura raggruppati.

## <a name="virtual-machine-windowslinux-workloads"></a>Carichi di lavoro della macchina virtuale (Windows/Linux)

### <a name="application-running-on-load-balanced-vms"></a>Applicazione in esecuzione in macchine virtuali con bilanciamento del carico

Questa architettura di riferimento mostra un set di procedure consolidate per l'esecuzione di più macchine virtuali Windows in un set di scalabilità dietro a un servizio di bilanciamento del carico, in modo da migliorare la disponibilità e la scalabilità. Questa architettura può essere usata per qualsiasi carico di lavoro senza stato, ad esempio un server web.

![Diagramma dell'architettura di riferimento per un'applicazione in esecuzione in macchine virtuali con bilanciamento del carico](./media/ddos-best-practices/image-9.png)

In questa architettura un carico di lavoro viene distribuito tra più istanze di macchina virtuale. L'indirizzo IP pubblico è unico e il traffico Internet viene distribuito alle macchine virtuali tramite un servizio di bilanciamento del carico. Protezione DDoS Standard è abilitata nella rete virtuale di Azure Load Balancer (Internet) che ha l'indirizzo IP pubblico associato.

Il servizio di bilanciamento del carico distribuisce le richieste Internet in ingresso alle istanze delle macchine virtuali. I set di scalabilità di macchine virtuali consentono di aumentare o ridurre il numero di macchine virtuali manualmente o automaticamente in base a regole predefinite. Questo è importante se la risorsa è sotto attacco DDoS. Vedere questo [articolo](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm) per altre informazioni su questa architettura di riferimento.

### <a name="application-running-on-windows-n-tier"></a>Applicazione in esecuzione in una VM Windows a più livelli

È possibile implementare un'architettura a più livelli in diversi modi. Il diagramma seguente mostra una tipica applicazione Web a tre livelli. Questa architettura è basata sull'architettura descritta nell'articolo [Eseguire macchine virtuali con carico bilanciato per la scalabilità e la disponibilità](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). I livelli Web e business usano macchine virtuali con carico bilanciato.

![Diagramma dell'architettura di riferimento per un'applicazione in esecuzione in macchine virtuali Windows a più livelli](./media/ddos-best-practices/image-10.png)

In questa architettura, il servizio Protezione DDoS Standard è abilitato nella rete virtuale. Tutti gli indirizzi IP pubblici nella rete virtuale sono protetti da DDoS per il livello 3 e 4. Per la protezione di livello 7, distribuire il gateway applicazione nello SKU del Web application firewall. Vedere questo [articolo](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier) per altre informazioni su questa architettura di riferimento.

> [!NOTE]
> Gli scenari in cui una singola macchina virtuale è in esecuzione dietro un indirizzo IP pubblico non sono supportati.

### <a name="paas-web-application"></a>Applicazione Web PaaS

Questa architettura di riferimento mostra l'esecuzione di un'applicazione di servizio App di Azure in una singola area. Questa architettura mostra dei set di procedure collaudate per un'applicazione Web che usa il [servizio app di Azure](https://azure.microsoft.com/documentation/services/app-service/) e [Database SQL di Azure](https://azure.microsoft.com/documentation/services/sql-database/).
Viene configurata un'area di stand-by per gli scenari di failover.

![Diagramma dell'architettura di riferimento per un'applicazione Web PaaS](./media/ddos-best-practices/image-11.png)

Gestione traffico di Azure indirizza le richieste in ingresso al gateway applicazione in una delle aree. Durante il normale funzionamento, le richieste vengono indirizzate al gateway applicazione nell'area attiva. Se quell'area non è più disponibile, Gestione traffico effettua il failover al gateway applicazione nell'area di stand-by.

Tutto il traffico da Internet destinato all'applicazione Web viene incanalato verso l'[indirizzo IP pubblico del gateway applicazione](/azure/application-gateway/application-gateway-web-app-overview) tramite Gestione traffico. In questo scenario, lo stesso servizio app (app Web) non è esposto direttamente verso l'esterno ed è protetto dal gateway applicazione. 

È consigliabile configurare lo SKU WAF del gateway applicazioni (modalità di prevenzione) per proteggersi dagli attacchi di livello 7 (HTTP/HTTPS/Web Socket). Le app Web sono inoltre configurate per [accettare il traffico solo dall'indirizzo IP del gateway applicazione](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).

Vedere questo [articolo](/azure/architecture/reference-architectures/app-service-web-app/multi-region) per altre informazioni su questa architettura di riferimento.

## <a name="mitigation-for-non-web-paas-services"></a>Mitigazione dei rischi per Servizi PaaS non web

### <a name="hdinsight-on-azure"></a>HDInsight in Azure

Questa architettura di riferimento mostra la configurazione di Protezione DDoS Standard per un [cluster Azure HDInsight](/azure/hdinsight/). Assicurarsi che il cluster HDInsight sia collegato a una rete virtuale e che Protezione DDoS sia abilitato sulla rete virtuale.

![Riquadri "HDInsight" e "Impostazioni avanzate", con le impostazioni della rete virtuale](./media/ddos-best-practices/image-12.png)

![Selezione per l'abilitazione di Protezione DDoS](./media/ddos-best-practices/image-13.png)

In questa architettura, il traffico destinato al cluster HDInsight da Internet viene indirizzato all'indirizzo IP pubblico associato al servizio di bilanciamento del carico del gateway di HDInsight. Il bilanciamento del carico di gateway invia quindi il traffico ai nodi head o direttamente ai nodi di lavoro. Dato che Protezione DDoS Standard è abilitata nella rete virtuale HDInsight, tutti gli indirizzi IP pubblici della rete virtuale ottengono la protezione DDoS per i livelli 3 e 4. Questa architettura di riferimento può essere combinata con le architetture di riferimento a più livelli e a più areee.

Per altre informazioni su questa architettura di riferimento, vedere la documentazione [Estendere Azure HDInsight usando Rete virtuale di Azure](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json).


> [!NOTE]
> L'ambiente del servizio app di Azure per PowerApps o la gestione API in una rete virtuale con un IP pubblico non sono supportati in modo nativo.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un piano di protezione DDoS](manage-ddos-protection.md).