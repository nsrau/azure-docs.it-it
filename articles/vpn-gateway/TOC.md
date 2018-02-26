# Panoramica
## [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md)
## [Domande frequenti sul gateway VPN](vpn-gateway-vpn-faq.md)
## [Sottoscrizione e limiti del servizio](../azure-subscription-service-limits.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)

# Attività iniziali
## [Pianificazione e progettazione per il gateway VPN](vpn-gateway-plan-design.md)
## [Informazioni sulle impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md)
## [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md)
## [Informazioni sui requisiti per la crittografia](vpn-gateway-about-compliance-crypto.md)
## [Informazioni su BGP e su Gateway VPN](vpn-gateway-bgp-overview.md)
## [Informazioni sulla connettività a disponibilità elevata](vpn-gateway-highlyavailable.md)
## [Informazioni sulle connessioni da punto a sito](point-to-site-about.md)

# Procedure
## Configurare connessioni da sito a sito
### [Azure portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
### [Interfaccia della riga di comando di Azure](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
### [Portale di Azure (classico)](vpn-gateway-howto-site-to-site-classic-portal.md)

## [Scaricare script di configurazione del dispositivo VPN](vpn-gateway-download-vpndevicescript.md)

## Configurare connessioni da punto a sito - autenticazione del certificato nativa di Azure
### Configurare una VPN P2S
#### [Azure portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
#### [Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
#### [Portale di Azure (classico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
### Generare certificati autofirmati
#### [Azure PowerShell](vpn-gateway-certificates-point-to-site.md)
#### [Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
### [Creare e installare i file di configurazione del client VPN](point-to-site-vpn-client-configuration-azure-cert.md)
### [Installare certificati client](point-to-site-how-to-vpn-client-install-azure-cert.md)

## Configurare connessioni da punto a sito - autenticazione RADIUS
### Configurare una VPN P2S
#### [Azure PowerShell](point-to-site-how-to-radius-ps.md)
### [Creare e installare i file di configurazione del client VPN](point-to-site-vpn-client-configuration-radius.md)
### [Integrare l'autenticazione RADIUS VPN da punto a sito con il Server dei criteri di rete](vpn-gateway-radiuis-mfa-nsp.md)

## Configurare connessioni tra reti virtuali
### [Azure portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
### [Interfaccia della riga di comando di Azure](vpn-gateway-howto-vnet-vnet-cli.md)
### [Portale di Azure (classico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
## Configurare una connessione da rete virtuale a rete virtuale tra modelli di distribuzione
### [Azure portal](vpn-gateway-connect-different-deployment-models-portal.md)
### [Azure PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
## Configurare connessioni coesistenti da sito a sito ed ExpressRoute
### [Azure PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## Configurare connessioni multiple da sito a sito
### [Azure portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
### [Azure PowerShell (classico)](vpn-gateway-multi-site.md)
## Connettere più dispositivi VPN basati su criteri
### [Azure PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md)
## Configurare i criteri IPsec/IKE per le connessioni
### [Azure PowerShell](vpn-gateway-ipsecikepolicy-rm-powershell.md)
## Configurare le connessioni attivo/attivo a disponibilità elevata
### [Azure PowerShell](vpn-gateway-activeactive-rm-powershell.md)
## Configurare BGP per un gateway VPN
### [Azure PowerShell](vpn-gateway-bgp-resource-manager-ps.md)
### [Interfaccia della riga di comando di Azure](bgp-how-to-cli.md)
## È possibile configurare il tunneling forzato?
### [Azure PowerShell](vpn-gateway-forced-tunneling-rm.md)
### [Azure PowerShell (classico)](vpn-gateway-about-forced-tunneling.md)
## Modificare le impostazioni del gateway di rete locale
### [Azure portal](vpn-gateway-modify-local-network-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-modify-local-network-gateway.md)
### [Interfaccia della riga di comando di Azure](vpn-gateway-modify-local-network-gateway-cli.md)
## [Verificare una connessione di Gateway VPN](vpn-gateway-verify-connection-resource-manager.md)
## [Reimpostare un gateway VPN](vpn-gateway-resetgw-classic.md)
## Eliminare un gateway VPN
### [Azure portal](vpn-gateway-delete-vnet-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
### [Azure PowerShell (classico)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
## [Configurare un gateway VPN (versione classica)](vpn-gateway-configure-vpn-gateway-mp.md)
## [SKU del gateway (legacy)](vpn-gateway-about-skus-legacy.md)
## Configurare dispositivi VPN di terze parti
### [Panoramica e configurazione di Azure](vpn-gateway-3rdparty-device-config-overview.md)
### [Esempio: dispositivo Cisco ASA (IKEv2/senza BGP)](vpn-gateway-3rdparty-device-config-cisco-asa.md)
## [Migrazione dal modello classico al modello Resource Manager](vpn-gateway-classic-resource-manager-migration.md)
## [Risolvere i problemi](vpn-gateway-troubleshoot.md)
### [Convalidare la velocità effettiva della VPN verso una rete virtuale](vpn-gateway-validate-throughput-to-vnet.md)
### [Impostazioni di un dispositivo firewall o una VPN suggerita dalla community](vpn-gateway-third-party-settings.md)
### [Problema di connessione da punto a sito](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
### [Disconnessione intermittente della connessione da sito a sito](vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently.md)
### [La connessione da sito a sito non riesce a connettersi](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md) 
### [Configurare e convalidare le connessioni VPN o alla rete virtuale](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)

# riferimento
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#vpn)
## [Azure PowerShell (classico)](/powershell/module/azure/?view=azuresmps-3.7.0#networking)
## [REST](/rest/api/network/virtualnetworkgateways)
## [REST (classico)](https://msdn.microsoft.com/library/jj154113)
## [Interfaccia della riga di comando di Azure](/cli/azure/network/vnet-gateway)

# Risorse correlate
## [Rete virtuale](/azure/virtual-network/)
## [Gateway applicazione](/azure/application-gateway/)
## [DNS di Azure](/azure/dns/)
## [Gestione traffico](/azure/traffic-manager/)
## [Bilanciamento del carico](/azure/load-balancer/)
## [ExpressRoute](/azure/expressroute/)

# Risorse
## [Roadmap per Azure](https://azure.microsoft.com/roadmap/?category=networking)
## [Blog](https://azure.microsoft.com/blog/topics/networking)
## [Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway)
## [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)
## [Contratto di servizio](https://azure.microsoft.com/support/legal/sla)
## [Video](https://azure.microsoft.com/documentation/videos/index/?services=vpn-gateway)
