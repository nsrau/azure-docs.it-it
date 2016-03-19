È importante tenere presente che esistono due modi per configurare un listener del gruppo di disponibilità in Azure. Questi metodi differiscono nel tipo di servizio di bilanciamento del carico di Azure che si utilizza quando si crea il listener. Nella tabella seguente sono descritte le differenze:

| Bilanciamento del carico | Implementazione | Utilizzare quando: |
| ------------- | -------------- | ----------- |
| **Esterno** | Utilizza l’**indirizzo IP virtuale pubblico**del servizio cloud che ospita le macchine virtuali. | È necessario accedere al listener dall'esterno della rete virtuale, tra cui da Internet. |
| **Interno** | Utilizza il**bilanciamento di carico interno (ILB)**con un indirizzo privato per il listener. | E’ possibile accedere al listener solo dall'interno della stessa rete virtuale. Questa modalità include i VPN da sito a sito in scenari ibridi. |

>[AZURE.IMPORTANT]Per un listener che utilizza l’indirizzo VIP pubblico del servizio cloud (servizio di bilanciamento del carico esterno), tutti i dati restituiti tramite il listener sono considerati in uscita e addebitati alle normali tariffe di trasferimento dati in Azure. Ciò vale anche se il client si trova nella stessa rete virtuale e datacenter del listener e dei database. Ciò non avviene con un listener che utilizza ILB.

ILB può essere configurato solo in reti virtuali con un ambito regionale. Le reti virtuali esistenti che sono state configurate per un gruppo di affinità non possono usare il bilanciamento del carico interno. Per altre informazioni, vedere [Bilanciamento del carico interno](../articles/load-balancer/load-balancer-internal-overview.md).

<!---HONumber=Oct15_HO3-->