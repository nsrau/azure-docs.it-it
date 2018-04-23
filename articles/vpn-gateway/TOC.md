# [Documentazione del gateway VPN](index.md)

# Panoramica
## [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md)
## [Domande frequenti sul gateway VPN](vpn-gateway-vpn-faq.md)

# Attività iniziali
## Creare un gateway VPN basato su route
### [Portale di Azure](create-routebased-vpn-gateway-portal.md)
### [Azure PowerShell](create-routebased-vpn-gateway-powershell.md)
### [Interfaccia della riga di comando di Azure](create-routebased-vpn-gateway-cli.md)

# Esempi
## [Creare un gateway VPN](./scripts/vpn-gateway-sample-create-vpn-gateway-powershell.md)
## [Da punto a sito - Autenticazione RADIUS](./scripts/vpn-gateway-sample-point-to-site-radius-authentication-powershell.md)
## [Da punto a sito - Autenticazione del certificato](./scripts/vpn-gateway-sample-point-to-site-certificate-authentication-powershell.md)
## [Da sito a sito](./scripts/vpn-gateway-sample-site-to-site-powershell.md)

# Concetti
## [Pianificazione e progettazione per il gateway VPN](vpn-gateway-plan-design.md)
## [Informazioni sulle impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md)
## [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md)
## [Informazioni sui requisiti per la crittografia](vpn-gateway-about-compliance-crypto.md)
## [Informazioni su BGP e su Gateway VPN](vpn-gateway-bgp-overview.md)
## [Informazioni sulla connettività a disponibilità elevata](vpn-gateway-highlyavailable.md)
## [Informazioni sulle connessioni da punto a sito](point-to-site-about.md)
## [Informazioni sul routing da punto a sito](vpn-gateway-about-point-to-site-routing.md)

# Guide alle procedure
## Configurare connessioni da sito a sito
### [Portale di Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
### [Interfaccia della riga di comando di Azure](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
## [Scaricare script di configurazione del dispositivo VPN](vpn-gateway-download-vpndevicescript.md)
## Configurare connessioni da punto a sito - autenticazione del certificato nativa di Azure
### Configurare una VPN P2S
#### [Portale di Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
#### [Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
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
### [Portale di Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
### [Interfaccia della riga di comando di Azure](vpn-gateway-howto-vnet-vnet-cli.md)
## Configurare una connessione da rete virtuale a rete virtuale tra modelli di distribuzione
### [Portale di Azure](vpn-gateway-connect-different-deployment-models-portal.md)
### [Azure PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
## Configurare connessioni coesistenti da sito a sito ed ExpressRoute
### [Azure PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## Configurare connessioni multiple da sito a sito
### [Portale di Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
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
## Modificare le impostazioni del gateway di rete locale
### [Portale di Azure](vpn-gateway-modify-local-network-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-modify-local-network-gateway.md)
### [Interfaccia della riga di comando di Azure](vpn-gateway-modify-local-network-gateway-cli.md)
## [Verificare una connessione di Gateway VPN](vpn-gateway-verify-connection-resource-manager.md)
## [Reimpostare un gateway VPN](vpn-gateway-resetgw-classic.md)
## Eliminare un gateway VPN
### [Portale di Azure](vpn-gateway-delete-vnet-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
## [SKU del gateway (legacy)](vpn-gateway-about-skus-legacy.md)
## Configurare dispositivi VPN di terze parti
### [Panoramica e configurazione di Azure](vpn-gateway-3rdparty-device-config-overview.md)
### [Esempio: dispositivo Cisco ASA (IKEv2/senza BGP)](vpn-gateway-3rdparty-device-config-cisco-asa.md)
## [Risolvere i problemi](vpn-gateway-troubleshoot.md)
### [Impostazioni di un dispositivo firewall o una VPN suggerita dalla community](vpn-gateway-third-party-settings.md)
### [Configurare e convalidare le connessioni VPN o alla rete virtuale](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)
### [Convalidare la velocità effettiva della VPN verso una rete virtuale](vpn-gateway-validate-throughput-to-vnet.md)
### Problemi della connessione da punto a sito
#### [Problemi di connessione da punto a sito](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
#### [Problemi di connessione da punto a sito - Client VPN in Mac OS X](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
### Problemi della connessione da sito a sito
#### [Problemi di connessione da sito a sito](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md)
#### [Disconnessione intermittente della connessione da sito a sito](vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently.md)

## Articoli sul modello di distribuzione classica
### [Configurare una connessione da sito a sito](vpn-gateway-howto-site-to-site-classic-portal.md)
### [Configurare una connessione da punto a sito](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
### [Configurare una connessione tra reti virtuali](vpn-gateway-howto-vnet-vnet-portal-classic.md)
### [Configurare il tunneling forzato](vpn-gateway-about-forced-tunneling.md)
### [Eliminare un gateway VPN](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
### [Configurare più connessioni da sito a sito](vpn-gateway-multi-site.md)
### [Configurare un gateway VPN](vpn-gateway-configure-vpn-gateway-mp.md)
### [Migrazione dal modello classico al modello Resource Manager](vpn-gateway-classic-resource-manager-migration.md)

# Riferimenti
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
## [Subscription and service limits](../azure-subscription-service-limits.md?toc=%2fazure%2fvpn-gateway%2ftoc.json) (Sottoscrizione e limiti del servizio)
## [Prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway)
## [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)
## [Contratto di servizio](https://azure.microsoft.com/support/legal/sla)
## [Video](https://azure.microsoft.com/documentation/videos/index/?services=vpn-gateway)
