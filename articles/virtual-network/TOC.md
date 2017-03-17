# Panoramica
## [Reti virtuali](virtual-networks-overview.md)
## [Route definite dall'utente e inoltro IP](virtual-networks-udr-overview.md)
## [Peering di rete virtuale](virtual-network-peering-overview.md)
## [Continuità aziendale](virtual-network-disaster-recovery-guidance.md)
## [Domande frequenti](virtual-networks-faq.md)
## Indirizzamento IP
### [Gestione risorse](virtual-network-ip-addresses-overview-arm.md)
### [Classico](virtual-network-ip-addresses-overview-classic.md)
## Macchine virtuali
### [Interfacce di rete](virtual-network-network-interface-overview.md)
### [Risoluzione dei nomi](virtual-networks-name-resolution-for-vms-and-role-instances.md)

# Attività iniziali
## [Creare la prima rete virtuale](virtual-network-get-started-vnet-subnet.md)

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
#### [Portale](virtual-networks-create-nsg-arm-pportal.md)
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

### [Interfacce di rete](virtual-network-network-interface.md)

### [Indirizzi IP pubblici](virtual-network-public-ip-address.md)

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

#### Più indirizzi IP
##### [portale di Azure](virtual-network-multiple-ip-addresses-portal.md)
##### [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [CLI](virtual-network-multiple-ip-addresses-cli.md)
##### [Modello](virtual-network-multiple-ip-addresses-template.md)

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

## Configurare
### Rete accelerata per macchine virtuali
#### [Portale di Azure](virtual-network-accelerated-networking-portal.md)
#### [PowerShell](virtual-network-accelerated-networking-powershell.md)
### [Ottimizzare la velocità della rete VM](virtual-network-optimize-network-bandwidth.md)
### Elenchi di controllo di accesso
#### [Portale classico](virtual-networks-acl.md)
#### [PowerShell](virtual-networks-acl-powershell.md)

## Gestisci
### Gruppi di sicurezza di rete
#### [Portale](virtual-network-manage-nsg-arm-portal.md)
#### [PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [Interfaccia della riga di comando](virtual-network-manage-nsg-arm-cli.md)
#### [Log](virtual-network-nsg-manage-log.md)
### Macchine virtuali
#### [Visualizzare e modificare i nomi host](virtual-networks-viewing-and-modifying-hostnames.md)
#### [Spostare una macchina virtuale in una subnet diversa](virtual-networks-move-vm-role-to-subnet.md)

## Risolvere problemi
### Gruppi di sicurezza di rete
#### [Portale](virtual-network-nsg-troubleshoot-portal.md)
#### [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### Route
#### [Portale](virtual-network-routes-troubleshoot-portal.md)
#### [PowerShell](virtual-network-routes-troubleshoot-powershell.md)

# Riferimento
## [PowerShell (Resource Manager)](/powershell/resourcemanager/azurerm.network/v3.4.0/azurerm.network)
## [PowerShell (Classic)](/powershell/servicemanagement/azure.networking/v3.4.0/azure.networking) (PowerShell (classico))
## [Interfaccia della riga di comando di Azure](/cli/azure/network)
## [Java](/java/api/)
## [REST (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [REST (classico)](https://msdn.microsoft.com/library/jj157182.aspx)


# Risorse correlate
## [Macchine virtuali](/azure/virtual-machines/)
## [Gateway applicazione](/azure/application-gateway/)
## [DNS di Azure](/azure/dns/)
## [Gestione traffico](/azure/traffic-manager/)
## [Bilanciamento del carico](/azure/load-balancer/)
## [Gateway VPN](/azure/vpn-gateway/)
## [ExpressRoute](/azure/expressroute/)

# Risorse
## [Blog sulle reti](http://azure.microsoft.com/blog/topics/networking)
## [Forum sulle reti](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Prezzi](https://azure.microsoft.com/pricing/details/virtual-network)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
