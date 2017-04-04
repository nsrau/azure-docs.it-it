> [!div class="op_single_selector"]
> * [Portale](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [Interfaccia della riga di comando 2.0](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
> * [Interfaccia della riga di comando 1.0](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli-nodejs.md)
> * [Modello](../articles/virtual-network/virtual-network-multiple-ip-addresses-template.md)
>

Una macchina virtuale di Azure può essere associata a una o più interfacce di rete. A ogni scheda di interfaccia di rete possono essere assegnati uno o più indirizzi IP pubblici o privati, statici e dinamici. L'assegnazione di più indirizzi IP a una VM consente di:

* Ospitare più siti Web o servizi con indirizzi IP e certificati SSL diversi in un singolo server.
* Fungere da appliance virtuale di rete, ad esempio un firewall o un servizio di bilanciamento del carico.
* Aggiungere qualsiasi indirizzo IP per qualsiasi scheda di interfaccia di rete a un pool back-end di Azure Load Balancer. In passato, era possibile aggiungere a un pool di back-end solo gli indirizzi IP primari per la scheda di interfaccia di rete primaria. Per altre informazioni su come bilanciare il carico di più configurazioni IP, leggere l'articolo [Load balancing multiple IP configurations](../articles/load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Bilanciamento del carico di più configurazioni IP).

Ogni scheda di interfaccia di rete collegata a una macchina virtuale dispone di una o più configurazioni IP associate. A ogni configurazione viene assegnato un indirizzo IP privato statico o dinamico. Ogni configurazione può anche avere una risorsa di indirizzo IP pubblico associata. Una risorsa indirizzo IP pubblico dispone di un indirizzo IP dinamico o statico pubblico assegnato. Per altre informazioni sugli indirizzi IP in Azure, leggere l'articolo sugli [indirizzi IP in Azure](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md). 

Sono previsti limiti per il numero di indirizzi IP privati che possono essere assegnati a una scheda di rete e per il numero di indirizzi IP pubblici che possono essere usati in una sottoscrizione di Azure. Per informazioni dettagliate, vedere l'articolo [Limiti di Azure](../articles/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
