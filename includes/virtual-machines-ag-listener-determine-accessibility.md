È importante tenere presente che esistono due modi per configurare un listener del gruppo di disponibilità in Azure. Questi metodi differiscono nel tipo di servizio di bilanciamento del carico di Azure che si utilizza quando si crea il listener. Nella tabella seguente sono descritte le differenze:

| Bilanciamento del carico | Implementazione | Utilizzare quando: |
| ------------- | -------------- | ----------- |
| **Esterno** | Utilizza l’**indirizzo IP virtuale pubblico**del servizio cloud che ospita le macchine virtuali. | È necessario accedere al listener dall'esterno della rete virtuale, tra cui da Internet. |
| **Interno** | Utilizza il**bilanciamento di carico interno (ILB)**con un indirizzo privato per il listener. | E’ possibile accedere al listener solo dall'interno della stessa rete virtuale. Questa modalità include i VPN da sito a sito in scenari ibridi. |

>[AZURE.IMPORTANT] Per un listener che usa l'indirizzo VIP pubblico del servizio cloud (servizio di bilanciamento del carico esterno), non si incorrerà in spese di uscita fin quando il client, il listener e i database si troveranno nella stessa area di Azure. In caso contrario, i dati restituiti tramite il listener saranno considerati in uscita e verranno addebitati i normali costi di trasferimento dati.

ILB può essere configurato solo in reti virtuali con un ambito regionale. Le reti virtuali esistenti che sono state configurate per un gruppo di affinità non possono usare il bilanciamento del carico interno. Per altre informazioni, vedere [Bilanciamento del carico interno](../articles/load-balancer/load-balancer-internal-overview.md).

