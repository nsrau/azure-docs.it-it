---
title: Più indirizzi VIP per Azure Load Balancer | Microsoft Docs
description: Panoramica di più indirizzi VIP in Azure Load Balancer
services: load-balancer
documentationcenter: na
author: chkuhtz
manager: narayan
editor: ''

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/11/2016
ms.author: chkuhtz

---
# <a name="multiple-vips-for-azure-load-balancer"></a>Più indirizzi VIP per Azure Load Balancer
Azure Load Balancer consente di eseguire il bilanciamento del carico dei servizi su più porte, più indirizzi IP o entrambi. È possibile usare le definizioni di servizio di bilanciamento del carico interno e pubblico per eseguire il bilanciamento del carico dei flussi in un set di macchine virtuali.

Questo articolo descrive i principi fondamentali di questa funzionalità, i concetti importanti e i vincoli. Se si intende esporre i servizi in un solo indirizzo IP, sono disponibili istruzioni semplificate per le configurazioni di servizi di bilanciamento del carico [pubblici](load-balancer-get-started-internet-portal.md) o [interni](load-balancer-get-started-ilb-arm-portal.md). L'aggiunta di più indirizzi VIP è un'operazione incrementale rispetto a una configurazione con un solo indirizzo VIP. Usando i concetti illustrati in questo articolo è possibile espandere una configurazione semplificata in qualsiasi momento.

Quando si definisce un'istanza di Azure Load Balancer, vengono connessi un front-end e un back-end con regole. Il probe di integrità a cui fa riferimento la regola viene usato per determinare il modo in cui i nuovi flussi vengono inviati a un nodo nel pool back-end. Il front-end è definito da un indirizzo IP virtuale (VIP) che è una tupla di 3 elementi costituita da un indirizzo IP (pubblico o interno), un protocollo di trasporto (UDP o TCP) e un numero di porta. Un DIP è un indirizzo IP su una scheda di interfaccia di rete virtuale di Azure collegata a una macchina virtuale nel pool back-end.

La tabella seguente contiene alcune configurazioni front-end di esempio:

| Indirizzo VIP | Indirizzo IP | protocol | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

La tabella descrive quattro front-end diversi. I front-end 1, 2 e 3 rappresentano un unico indirizzo VIP con più regole. Viene usato lo stesso indirizzo IP, ma la porta o il protocollo sono diversi per ogni front-end. I front-end 1 e 4 sono un esempio di più indirizzi VIP, in cui lo stesso protocollo e la stessa porta front-end vengono riutilizzati in più indirizzi VIP.

Azure Load Balancer offre flessibilità nella definizione di regole del servizio di bilanciamento del carico. Una regola dichiara il modo in cui viene eseguito il mapping di un indirizzo e una porta nel front-end all'indirizzo e alla porta di destinazione nel back-end. L'eventuale riutilizzo delle porte back-end tra regole dipende dal tipo di regola. Ogni tipo di regola presenta requisiti specifici che possono influire sulla configurazione degli host e la progettazione dei probe. Esistono due tipi di regole:

1. La regola predefinita senza riutilizzo delle porte back-end
2. La regola con indirizzo IP mobile che prevede il riutilizzo delle porte back-end

Azure Load Balancer consente di combinare i due tipi di regole nella stessa configurazione del servizio di bilanciamento del carico. Il servizio di bilanciamento del carico può usare le regole contemporaneamente per una determinata macchina virtuale o una qualsiasi combinazione, purché siano rispettati i vincoli della regola. Il tipo di regola scelto dipende dai requisiti dell'applicazione e dalla complessità correlata al supporto della configurazione. È necessario valutare quali tipi di regole siano più adatti allo scenario.

Questi scenari verranno approfonditi iniziando con il comportamento predefinito.

## <a name="rule-type-#1:-no-backend-port-reuse"></a>Regola di tipo 1: nessun riutilizzo delle porte back-end
![Illustrazione di più indirizzi VIP](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

In questo scenario, gli indirizzi VIP front-end sono configurati nel modo seguente:

| Indirizzo VIP | Indirizzo IP | protocol | port |
| --- | --- | --- | --- |
| ![Indirizzo VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![Indirizzo VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Il DIP è la destinazione del flusso in ingresso. Nel pool di back-end, ogni macchina virtuale espone il servizio desiderato su una porta univoca in un DIP. Questo servizio è associato al front-end tramite una definizione di regole.

Si definiscono due regole:

| Regola | Mapping frontend | Al pool back-end |
| --- | --- | --- |
| 1 |![Indirizzo VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png)  VIP1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png)  DIP2:80 |
| 2 |![Indirizzo VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png)  VIP2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png)  DIP2:81 |

Il mapping completo in Azure Load Balancer sarà ora il seguente:

| Regola | Indirizzo IP VIP | protocol | port | Destination | port |
| --- | --- | --- | --- | --- | --- |
| ![Regola](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |Indirizzo IP DIP |80 |
| ![Regola](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |Indirizzo IP DIP |81 |

Ogni regola deve produrre un flusso con una combinazione univoca di indirizzo IP di destinazione e porta di destinazione. Modificando la porta di destinazione del flusso, più regole possono inviare flussi allo stesso DIP in porte diverse.

I probe di integrità vengono sempre indirizzati al DIP di una macchina virtuale. È necessario assicurarsi che il probe rifletta lo stato della macchina virtuale.

## <a name="rule-type-#2:-backend-port-reuse-by-using-floating-ip"></a>Regola di tipo 2: riutilizzo delle porte back-end tramite indirizzo IP mobile
Azure Load Balancer consente di riutilizzare la porta front-end in più indirizzi VIP indipendentemente dal tipo di regola usato. Alcuni scenari di applicazione preferiscono o richiedono l'uso della stessa porta da parte di più istanze dell'applicazione in una singola macchina virtuale nel pool back-end. Esempi comuni di riutilizzo delle porte includono il clustering per la disponibilità elevata, le appliance virtuali di rete e l'esposizione di più endpoint TLS senza rieseguire la crittografia di rete.

Per riutilizzare la porta di back-end tra più regole, è necessario abilitare l'indirizzo IP mobile nella definizione della regola.

L'indirizzo IP mobile è una parte di ciò che è noto come Direct Server Return (DSR). Il DSR è costituito da due parti: una topologia di flussi e uno schema di mappatura degli indirizzi IP. A livello di piattaforma, Azure Load Balancer viene sempre eseguito in una topologia di flussi DSR indipendentemente dal fatto che l'indirizzo IP mobile sia abilitato o meno. Ciò significa che la parte in uscita di un flusso viene sempre correttamente riscritta in un flusso direttamente nell'origine.

Con il tipo di regola predefinito, Azure espone uno schema di mappatura degli indirizzi IP tradizionale per il servizio di bilanciamento del carico per semplificarne l'uso. L'abilitazione dell'indirizzo IP mobile modifica lo schema di mappatura degli indirizzi IP per consentire una maggiore flessibilità, come illustrato di seguito.

Il diagramma seguente illustra questa configurazione:

![Illustrazione di più indirizzi VIP](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Per questo scenario, ogni macchina virtuale nel pool back-end ha tre interfacce di rete:

* DIP: una scheda di interfaccia di rete virtuale associata alla macchina virtuale (risorsa NIC di Azure)
* VIP1: un'interfaccia di loopback all'interno del sistema operativo guest configurato con l'indirizzo IP di VIP1
* VIP2: un'interfaccia di loopback all'interno del sistema operativo guest configurato con l'indirizzo IP di VIP2

> [!IMPORTANT]
> La configurazione delle interfacce logiche viene eseguita all'interno del sistema operativo guest. Questa configurazione non viene eseguita o gestita da Azure. Senza questa configurazione, le regole non funzionano. Le definizioni dei probe di integrità usano il DIP della macchina virtuale anziché l'indirizzo VIP logico. Il servizio deve quindi mettere a disposizione le risposte probe su una porta DIP che rifletta lo stato del servizio offerto nell'indirizzo VIP logico.
> 
> 

Si supponga la stessa configurazione front-end dello scenario precedente:

| Indirizzo VIP | Indirizzo IP | protocol | port |
| --- | --- | --- | --- |
| ![Indirizzo VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![Indirizzo VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Si definiscono due regole:

| Regola | Mapping frontend | Al pool back-end |
| --- | --- | --- |
| 1 |![Regola](./media/load-balancer-multivip-overview/load-balancer-rule-green.png)  VIP1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png)  VIP1:80 (in VM1 e VM2) |
| 2 |![Regola](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png)  VIP2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png)  VIP2:80 (in VM1 e VM2) |

La tabella seguente illustra il mapping completo nel servizio di bilanciamento del carico:

| Regola | Indirizzo IP VIP | protocol | port | Destination | port |
| --- | --- | --- | --- | --- | --- |
| ![Indirizzo VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |come indirizzo VIP (65.52.0.1) |come indirizzo VIP (80) |
| ![Indirizzo VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |come indirizzo VIP (65.52.0.2) |come indirizzo VIP (80) |

La destinazione del flusso in ingresso è l'indirizzo VIP nell'interfaccia di loopback della macchina virtuale. Ogni regola deve produrre un flusso con una combinazione univoca di indirizzo IP di destinazione e porta di destinazione. Modificando l'indirizzo IP di destinazione del flusso, è possibile riusare la porta nella stessa macchina virtuale. Il servizio viene esposto al servizio di bilanciamento del carico associandolo all'indirizzo IP e alla porta VIP dell'interfaccia di loopback corrispondente.

Si noti che in questo esempio non viene modificata la porta di destinazione. Anche se questo è uno scenario con indirizzo IP mobile, Azure Load Balancer supporta anche la definizione di una regola per la riscrittura della porta di destinazione back-end e per differenziarla dalla porta di destinazione front-end.

Il tipo di regola con indirizzo IP mobile è alla base di diversi modelli di configurazione del servizio di bilanciamento di carico. Un esempio attualmente disponibile è la configurazione [SQL AlwaysOn con più listener](../virtual-machines/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) . Altri scenari di questo tipo verranno documentati nel tempo.

## <a name="limitations"></a>Limitazioni
* Più configurazioni di indirizzi VIP sono supportate solo con le macchine virtuali IaaS.
* Con la regola dell'indirizzo IP mobile, l'applicazione deve usare il DIP per i flussi in uscita. Se l'applicazione si associa all'indirizzo VIP configurato nell'interfaccia di loopback del sistema operativo guest, SNAT non potrà riscrivere il flusso in uscita e il flusso avrà esito negativo.
* Gli indirizzi IP pubblici hanno un effetto sulla fatturazione. Per altre informazioni, vedere [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Si applicano i limiti delle sottoscrizioni. Per altre informazioni, vedere i [limiti del servizio](../azure-subscription-service-limits.md#networking-limits) .

<!--HONumber=Oct16_HO2-->


