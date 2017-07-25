# Panoramica
## [Informazioni sulla rete di Azure](networking-overview.md)
## Architettura
### [Data center virtuali](networking-virtual-datacenter.md)
### [Routing asimmetrico con più percorsi di rete](../expressroute/expressroute-asymmetric-routing.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Progettazioni di reti protette](../best-practices-network-security.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Topologia hub-spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)
### [Procedure consigliate per la sicurezza di rete](../security/azure-security-network-security-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Appliance virtuali di rete a disponibilità elevata](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha )
### [Combinare i metodi di bilanciamento del carico](../traffic-manager/traffic-manager-load-balancing-azure.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Pianificare e progettare
### [Reti virtuali](../virtual-network/virtual-network-vnet-plan-design-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Filtro del traffico](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Connettività locale](../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fnetworking%2ftoc.json)
##  Concetti
### [Reti virtuali](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Bilanciamento carico di rete](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Bilanciamento del carico dell'applicazione](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [DNS](../dns/dns-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Distribuzione del traffico basato su DNS](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Connettersi all'ambiente locale: VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Connettersi all'ambiente locale: rete dedicata](../expressroute/expressroute-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)

# Introduzione
## [Creare la prima rete virtuale](../virtual-network/virtual-network-get-started-vnet-subnet.md?toc=%2fazure%2fnetworking%2ftoc.json)

# Procedure
## Connettività Internet
### [Server pubblici per il bilanciamento del carico di rete](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Server pubblici per il bilanciamento del carico dell'applicazione](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Proteggere le applicazioni Web](../application-gateway/application-gateway-web-application-firewall-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Distribuire il traffico in diverse località](../traffic-manager/traffic-manager-configure-geographic-routing-method.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Connettività interna
### [Server privati per il bilanciamento del carico di rete](../load-balancer/load-balancer-get-started-ilb-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Server privati per il bilanciamento del carico dell'applicazione](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Connettere reti virtuali: stessa località](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Connettere reti virtuali: località diverse](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Connettività locale
### [Connettersi a una rete locale: rete dedicata](../expressroute/expressroute-howto-circuit-portal-resource-manager.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Connettersi a una rete locale: VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
## gestione
### [Visualizzare la topologia di rete](../network-watcher/network-watcher-topology-powershell.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Gestire l'acquisizione pacchetti](../network-watcher/network-watcher-packet-capture-manage-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Determinare l'hop successivo del routing](../network-watcher/network-watcher-check-next-hop-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Verificare i flussi IP per una macchina virtuale](../network-watcher/network-watcher-check-ip-flow-verify-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Script di esempio
### [Interfaccia della riga di comando di Azure](cli-samples.md)
### [PowerShell](powershell-samples.md)
## Esercitazioni
### [Bilanciare il carico delle VM](../virtual-machines/linux/tutorial-load-balance-nodejs.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Connettere un computer a una rete virtuale](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)


# Riferimento
## [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/network)
## [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/?view=azurermps-3.8.0)
## [.Net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.network?view=azuremgmtnetwork-9.1.0-preview)
## [Node.js](https://azure.microsoft.com/develop/nodejs/#azure-sdk)
## [REST](https://msdn.microsoft.com/library/mt163658.aspx)

# Risorse
## [Creare modelli](/azure/azure-resource-manager/resource-group-authoring-templates?toc=%2fazure%2fnetworking%2ftoc.json)
## [Roadmap per Azure](https://azure.microsoft.com/roadmap/)
## [Modelli della community](https://azure.microsoft.com/resources/templates/)
## [Blog sulle reti](http://azure.microsoft.com/blog/topics/networking)
## [Prezzi](https://azure.microsoft.com/pricing)
## [Disponibilità internazionale](https://azure.microsoft.com/regions/services/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [Video](https://azure.microsoft.com/resources/videos/index/?services=virtual-network)

