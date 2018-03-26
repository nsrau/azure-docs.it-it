# [Documentazione su Load Balancer](index.md)

# Panoramica
## [Che cos'è il servizio di bilanciamento del carico?](load-balancer-overview.md)
## [Informazioni su Load Balancer Standard](load-balancer-standard-overview.md)
## [Probe del servizio di bilanciamento del carico](load-balancer-custom-probe-overview.md)
## [Porte a disponibilità elevata](load-balancer-ha-ports-overview.md)
## [Supporto per IPv6](load-balancer-ipv6-overview.md)
## [Più front-end](load-balancer-multivip-overview.md)
## [Connessioni in uscita](load-balancer-outbound-connections.md)
## [Load Balancer Standard e zone di disponibilità](load-balancer-standard-availability-zones.md)
## [Metriche e diagnostica di Load Balancer Standard](load-balancer-standard-diagnostics.md)

# Attività iniziali
## [Creare un'istanza di Load Balancer Basic](load-balancer-get-started-internet-portal.md)
### [Creare un'istanza di Load Balancer Basic (interfaccia della riga di comando)](load-balancer-get-started-internet-arm-cli.md)
### [Creare un'istanza di Load Balancer Basic (PowerShell)](load-balancer-get-started-internet-arm-ps.md)
## [Creare un'istanza di Load Balancer Standard](load-balancer-standard-public-portal.md)
### [Creare un'istanza di Load Balancer Standard (interfaccia della riga di comando)](load-balancer-standard-public-cli.md)

# Procedure

## [Creare il servizio di bilanciamento del carico pubblico con IPv6](load-balancer-ipv6-internet-ps.md)
### [Creare il servizio di bilanciamento del carico pubblico con IPv6 (interfaccia della riga di comando)](load-balancer-ipv6-internet-cli.md)
### [Creare il servizio di bilanciamento del carico pubblico con IPv6 (modello)](load-balancer-ipv6-internet-template.md)
## [Creare uno standard di servizio di bilanciamento del carico pubblico con ridondanza della zona](load-balancer-get-started-internet-az-portal.md)
### [Creare uno standard di servizio di bilanciamento del carico pubblico con ridondanza della zona (PowerShell)](load-balancer-get-started-internet-az-powershell.md)
### [Creare uno standard di servizio di bilanciamento del carico pubblico con ridondanza della zona (interfaccia della riga di comando)](load-balancer-get-started-internet-az-cli.md)
## [Creare un'istanza di Load Balancer Basic (modello)](load-balancer-get-started-internet-arm-template.md)
## [Configurare il servizio di bilanciamento del carico interno](load-balancer-get-started-ilb-arm-portal.md)
### [Configurare il servizio di bilanciamento del carico interno (PowerShell)](load-balancer-get-started-ilb-arm-ps.md)
### [Configurare il servizio di bilanciamento del carico interno (interfaccia della riga di comando)](load-balancer-get-started-ilb-arm-cli.md)
### [Configurare il servizio di bilanciamento del carico interno (modello)](load-balancer-get-started-ilb-arm-template.md)
## [Configurare il timeout di inattività TCP per il servizio di bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
## [Configurare la modalità di distribuzione per Load Balancer](load-balancer-distribution-mode.md)
## [Combinazione di servizi di bilanciamento del carico](../traffic-manager/traffic-manager-load-balancing-azure.md?toc=%2fazure%2fload-balancer%2ftoc.json)
## [Uso di più configurazioni IP](load-balancer-multiple-ip.md)
### [Uso di più configurazioni IP (interfaccia della riga di comando)](load-balancer-multiple-ip-cli.md)
### [Uso di più configurazioni IP (PowerShell)](load-balancer-multiple-ip-powershell.md)
## [Analisi dei log per il servizio di bilanciamento del carico di Azure](load-balancer-monitor-log.md)
## [Configurare le porte a disponibilità elevata per il servizio di bilanciamento del carico interno](load-balancer-configure-ha-ports.md)

## Risolvere problemi
### [Risoluzione dei problemi di Azure Load Balancer](load-balancer-troubleshoot.md)

## Articoli sul modello di distribuzione classica
### [Connessioni in uscita (versione classica)](load-balancer-outbound-connections-classic.md)
## [Configurare più indirizzi VIP per il servizio cloud](load-balancer-multivip.md)
### [Configurare il servizio di bilanciamento del carico interno per i servizi cloud](load-balancer-get-started-ilb-classic-cloud.md)
#### [Configurare il servizio di bilanciamento del carico interno per i servizi cloud (PowerShell)](load-balancer-get-started-ilb-classic-ps.md)
#### [Configurare il servizio di bilanciamento del carico interno per i servizi cloud (interfaccia della riga di comando)](load-balancer-get-started-ilb-classic-cli.md)
### [Configurare il servizio di bilanciamento del carico pubblico (PowerShell classico)](load-balancer-get-started-internet-classic-ps.md)
#### [Configurare il servizio di bilanciamento del carico pubblico (cloud classico)](load-balancer-get-started-internet-classic-cloud.md)
#### [Configurare il servizio di bilanciamento del carico pubblico (interfaccia della riga di comando classica)](load-balancer-get-started-internet-classic-cli.md)

# riferimento
## [Esempi di codice](https://azure.microsoft.com/en-us/resources/samples/?service=load-balancer)
## [Azure PowerShell](/powershell/module/azurerm.network)
## [Interfaccia della riga di comando di Azure per Azure](/cli/azure/network/lb)
## [.NET](/dotnet/api/microsoft.azure.management.network.models)
## [Java](/java/api/com.microsoft.azure.management.network)
## [Node.JS](http://azure.github.io/azure-sdk-for-node/azure-arm-network/latest/LoadBalancers.html)
## [Ruby](http://www.rubydoc.info/gems/azure_mgmt_network/Azure/ARM/Network/LoadBalancers)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.mgmt.network.operations.html#azure.mgmt.network.operations.LoadBalancersOperations)
## [REST](https://msdn.microsoft.com/library/azure/mt163651.aspx)

# Risorse correlate
## [Gateway applicazione](/azure/application-gateway/)
## [ExpressRoute](/azure/expressroute/)
## [Rete virtuale](/azure/virtual-network/)
## [Gateway VPN](/azure/vpn-gateway/)
## [Macchina virtuale](/azure/virtual-machines/)
## [Gestione traffico](/azure/traffic-manager/)
## [DNS](/azure/dns/)

# Risorse
## [Roadmap per Azure](https://azure.microsoft.com/roadmap/?category=networking)
## [Prezzi](https://azure.microsoft.com/pricing/details/load-balancer/)
## [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)
## [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=load-balancer)
