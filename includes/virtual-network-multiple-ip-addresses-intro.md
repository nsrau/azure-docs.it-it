> [!div class="op_single_selector"]
> * [Portale](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
> * [Modello](../articles/virtual-network/virtual-network-multiple-ip-addresses-template.md)
>

Una macchina virtuale di Azure può essere associata a una o più interfacce di rete. A ogni scheda di interfaccia di rete possono essere assegnati uno o più indirizzi IP pubblici o privati, statici e dinamici. L'assegnazione di più indirizzi IP a una VM consente di:

* Ospitare più siti Web o servizi con indirizzi IP e certificati SSL diversi in un singolo server.
* Fungere da appliance virtuale di rete, ad esempio un firewall o un servizio di bilanciamento del carico.
* Aggiungere qualsiasi indirizzo IP per qualsiasi scheda di interfaccia di rete a un pool back-end di Azure Load Balancer. In passato, era possibile aggiungere a un pool di back-end solo gli indirizzi IP primari per la scheda di interfaccia di rete primaria. Per altre informazioni su come bilanciare il carico di più configurazioni IP, leggere l'articolo [Load balancing multiple IP configurations](../articles/load-balancer/load-balancer-multiple-ip.md) (Bilanciamento del carico di più configurazioni IP).

Ogni scheda di interfaccia di rete collegata a una macchina virtuale dispone di una o più configurazioni IP associate. A ogni configurazione viene assegnato un indirizzo IP privato statico o dinamico. Ogni configurazione può anche avere una risorsa di indirizzo IP pubblico associata. Una risorsa indirizzo IP pubblico dispone di un indirizzo IP dinamico o statico pubblico assegnato. Per altre informazioni sugli indirizzi IP in Azure, leggere l'articolo sugli [indirizzi IP in Azure](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md). È possibile assegnare al massimo 250 indirizzi IP privati a ogni interfaccia di rete. Anche se è possibile assegnare più indirizzi IP pubblici a ogni interfaccia di rete, sono previsti limiti al numero di indirizzi IP che possono essere usati in una sottoscrizione di Azure. Per informazioni dettagliate, vedere l'articolo [Limiti di Azure](../articles/azure-subscription-service-limits.md#networking-limits).


<!--HONumber=Feb17_HO2-->


