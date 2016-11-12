# Panoramica
## [Reti virtuali](virtual-networks-overview.md)
## [Gruppi di sicurezza di rete](virtual-networks-nsg.md)
## [Route definite dall'utente e inoltro IP](virtual-networks-udr-overview.md)
## Indirizzamento IP
### [Gestione risorse](virtual-network-ip-addresses-overview-arm.md)
### [Classico](virtual-network-ip-addresses-overview-classic.md)
## [Peering di rete virtuale](virtual-network-peering-overview.md)
## Macchine virtuali
### [Interfacce di rete](virtual-network-network-interface-overview.md)
### [Risoluzione dei nomi](virtual-networks-name-resolution-for-vms-and-role-instances.md)
## [Continuità aziendale](virtual-network-disaster-recovery-guidance.md)
## [Prezzi](https://azure.microsoft.com/pricing/details/virtual-network)

# Attività iniziali
## [Creare una rete virtuale](virtual-networks-create-vnet-arm-pportal.md)
## [Distribuire una macchina virtuale in una rete virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

# Procedure
## Pianificare e progettare
### [Reti virtuali](virtual-network-vnet-plan-design-arm.md)
### [Gruppi di sicurezza di rete](virtual-networks-nsg.md)

## Distribuire
### Reti virtuali
#### [Portale](virtual-networks-create-vnet-arm-pportal.md)
#### [PowerShell](virtual-networks-create-vnet-arm-ps.md)
#### [CLI](virtual-networks-create-vnet-arm-cli.md)
#### [Modello](virtual-networks-create-vnet-arm-template-click.md)
#### [Portale (versione classica)](virtual-networks-create-vnet-classic-pportal.md)
#### [PowerShell (versione classica)](virtual-networks-create-vnet-classic-netcfg-ps.md)
#### [Interfaccia della riga di comando (versione classica)](virtual-networks-create-vnet-classic-cli.md)

### Gruppi di sicurezza di rete
#### [Portale](virtual-networks-create-nsg-arm-portal.md)
#### [PowerShell](virtual-networks-create-nsg-arm-ps.md)
#### [CLI](virtual-networks-create-nsg-arm-cli.md)
#### [Modello](virtual-networks-create-nsg-arm-template.md)
#### [PowerShell (versione classica)](virtual-networks-create-nsg-classic-ps.md)
#### [Interfaccia della riga di comando (versione classica)](virtual-networks-create-nsg-classic-cli.md)

### Route definite dall'utente
#### [PowerShell](virtual-network-create-udr-arm-ps.md)
#### [CLI](virtual-network-create-udr-arm-cli.md)
#### [Modello](virtual-network-create-udr-arm-template.md)
#### [PowerShell (versione classica)](virtual-network-create-udr-classic-ps.md)
#### [Interfaccia della riga di comando (versione classica)](virtual-network-create-udr-classic-cli.md)

### Peering di rete virtuale
#### [Portale](virtual-networks-create-vnetpeering-arm-portal.md)
#### [PowerShell](virtual-networks-create-vnetpeering-arm-ps.md)
#### [Modello](virtual-networks-create-vnetpeering-arm-template-click.md)

### Macchine virtuali

#### Indirizzi IP pubblici statici
##### [Portale](virtual-network-deploy-static-pip-arm-portal.md)
##### [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [CLI](virtual-network-deploy-static-pip-arm-cli.md)
##### [Modello](virtual-network-deploy-static-pip-arm-template.md)
##### [PowerShell (versione classica)](virtual-networks-reserved-public-ip.md)

#### Indirizzi IP privati statici
##### [Portale](virtual-networks-static-private-ip-arm-pportal.md)
##### [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [CLI](virtual-networks-static-private-ip-arm-cli.md)
##### [Portale (versione classica)](virtual-networks-static-private-ip-classic-pportal.md)
##### [PowerShell (versione classica)](virtual-networks-static-private-ip-classic-ps.md)
##### [Interfaccia della riga di comando (versione classica)](virtual-networks-static-private-ip-classic-cli.md)

#### Più interfacce di rete
##### [PowerShell](virtual-network-deploy-multinic-arm-ps.md)
##### [CLI](virtual-network-deploy-multinic-arm-cli.md)
##### [Modello](virtual-network-deploy-multinic-arm-template.md)
##### [PowerShell (versione classica)](virtual-network-deploy-multinic-classic-ps.md)
##### [Interfaccia della riga di comando (versione classica)](virtual-network-deploy-multinic-classic-cli.md)

#### [Indirizzi IP multipli](virtual-network-multiple-ip-addresses-powershell.md)

### Scenari di connettività
#### [Da rete virtuale a rete virtuale](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Da rete virtuale (Resource Manager) a rete virtuale (versione classica)](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Da rete virtuale a rete locale (VPN)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Da rete virtuale a rete locale (ExpressRoute)](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Architettura di rete ibrida a disponibilità elevata](../guidance/guidance-hybrid-network-expressroute-vpn-failover.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### Scenari di sicurezza
#### [Proteggere le reti con appliance virtuali](virtual-network-scenario-udr-gw-nva.md)
#### [Rete perimetrale tra Azure e Internet](../guidance/guidance-iaas-ra-secure-vnet-dmz.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Servizio cloud e sicurezza di rete](../best-practices-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Rete perimetrale semplice con gruppi di sicurezza di rete](virtual-networks-dmz-nsg-asm.md)
##### [Rete perimetrale con firewall e gruppi di sicurezza di rete](virtual-networks-dmz-nsg-fw-asm.md)
##### [Rete perimetrale con firewall, UDR e gruppi di sicurezza di rete](virtual-networks-dmz-nsg-fw-udr-asm.md)
##### [Applicazione di esempio](virtual-networks-sample-app.md)

## Gestire
### Gruppi di sicurezza di rete
#### [Portale](virtual-network-manage-nsg-arm-portal.md)
#### [PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [Interfaccia della riga di comando](virtual-network-manage-nsg-arm-cli.md)
#### [Log](virtual-network-nsg-manage-log.md)
#### Risolvere i problemi
##### [Portale](virtual-network-nsg-troubleshoot-portal.md)
##### [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### Risolvere i problemi relativi alle route
#### [Portale](virtual-network-routes-troubleshoot-portal.md)
#### [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### Macchine virtuali
#### [Visualizzare e modificare i nomi host](virtual-networks-viewing-and-modifying-hostnames.md)
#### [Spostare una macchina virtuale in una subnet diversa](virtual-networks-move-vm-role-to-subnet.md)

# Riferimento
## [Cmdlet di PowerShell (Resource Manager)](https://msdn.microsoft.com/library/mt163510(v=azure.300))
## [Cmdlet di PowerShell (versione classica)](https://msdn.microsoft.com/library/mt270335(v=azure.300))
## [API (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [API (versione classica)](https://msdn.microsoft.com/library/jj157182.aspx)
## [Blog sulle reti](http://azure.microsoft.com/blog/topics/networking)
## [Domande frequenti](virtual-networks-faq.md)

# Risorse correlate
## [Macchine virtuali](https://azure.microsoft.com/documentation/services/virtual-machines)
## [Gateway applicazione](https://azure.microsoft.com/documentation/services/application-gateway)
## [DNS di Azure](https://azure.microsoft.com/documentation/services/dns)
## [Gestione traffico](https://azure.microsoft.com/documentation/services/traffic-manager)
## [Bilanciamento del carico](https://azure.microsoft.com/documentation/services/load-balancer)
## [Gateway VPN](https://azure.microsoft.com/documentation/services/vpn-gateway)
## [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute)



<!--HONumber=Nov16_HO2-->


