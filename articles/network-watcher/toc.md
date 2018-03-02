# [Documentazione su Azure Network Watcher](index.md)

# Panoramica
## [Informazioni su Network Watcher](network-watcher-monitoring-overview.md)
## [Topologia](network-watcher-topology-overview.md)
## [Acquisizione pacchetti variabile](network-watcher-packet-capture-overview.md)
## [Registrazione dei flussi dei gruppi di sicurezza di rete](network-watcher-nsg-flow-logging-overview.md)
## [Verifica del flusso IP](network-watcher-ip-flow-verify-overview.md)
## [Visualizzazione di un gruppo di sicurezza](network-watcher-security-group-view-overview.md)
## [Hop successivo](network-watcher-next-hop-overview.md)
## [Risoluzione dei problemi di connettività VPN](network-watcher-troubleshoot-overview.md)
## [Risolvere i problemi relativi alle connessioni](network-watcher-connectivity-overview.md)
## [Limiti del servizio](../azure-subscription-service-limits.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-watcher-limits)

# Attività iniziali
## [Configurare Network Watcher](network-watcher-create.md)
## Gestire l'acquisizione pacchetti
### [Azure portal](network-watcher-packet-capture-manage-portal.md)
### [Azure PowerShell](network-watcher-packet-capture-manage-powershell.md)
### [Interfaccia della riga di comando di Azure 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
### [Interfaccia della riga di comando di Azure](network-watcher-packet-capture-manage-cli.md)
### [REST](network-watcher-packet-capture-manage-rest.md)
## Verificare i flussi IP verso e da una VM
### [Azure portal](network-watcher-check-ip-flow-verify-portal.md)
### [Azure PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
### [Interfaccia della riga di comando di Azure 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
### [Interfaccia della riga di comando di Azure](network-watcher-check-ip-flow-verify-cli.md)
### [REST](network-watcher-check-ip-flow-verify-rest.md)
## Analizzare l'hop successivo
### [Azure portal](network-watcher-check-next-hop-portal.md)
### [Azure PowerShell](network-watcher-check-next-hop-powershell.md)
### [Interfaccia della riga di comando di Azure 1.0](network-watcher-check-next-hop-cli-nodejs.md)
### [Interfaccia della riga di comando di Azure](network-watcher-check-next-hop-cli.md)
### [REST](network-watcher-check-next-hop-rest.md)
## Visualizzare la topologia di rete
### [Azure PowerShell](network-watcher-topology-powershell.md)
### [Interfaccia della riga di comando di Azure 1.0](network-watcher-topology-cli-nodejs.md)
### [Interfaccia della riga di comando di Azure](network-watcher-topology-cli.md)
### [REST](network-watcher-topology-rest.md)
## Risolvere i problemi relativi a gateway e connessioni VPN
### [Azure portal](network-watcher-troubleshoot-manage-portal.md)
### [Azure PowerShell](network-watcher-troubleshoot-manage-powershell.md)
### [Interfaccia della riga di comando di Azure 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
### [Interfaccia della riga di comando di Azure](network-watcher-troubleshoot-manage-cli.md)
### [REST](network-watcher-troubleshoot-manage-rest.md)
## Analizzare la visualizzazione dei gruppi di sicurezza di rete
### [Azure PowerShell](network-watcher-security-group-view-powershell.md)
### [Interfaccia della riga di comando di Azure 1.0](network-watcher-security-group-view-cli-nodejs.md)
### [Interfaccia della riga di comando di Azure](network-watcher-security-group-view-cli.md)
### [REST](network-watcher-security-group-view-rest.md)
## Risolvere i problemi relativi alle connessioni
### [Azure portal](network-watcher-connectivity-portal.md)
### [Azure PowerShell](network-watcher-connectivity-powershell.md)
### [Interfaccia della riga di comando di Azure](network-watcher-connectivity-cli.md)
### [REST](network-watcher-connectivity-rest.md)
## Configurare i log dei flussi dei gruppi di sicurezza di rete
### [Azure portal](network-watcher-nsg-flow-logging-portal.md)
### [Azure PowerShell](network-watcher-nsg-flow-logging-powershell.md)
### [Interfaccia della riga di comando di Azure 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
### [Interfaccia della riga di comando di Azure](network-watcher-nsg-flow-logging-cli.md)
### [REST](network-watcher-nsg-flow-logging-rest.md)

# Procedure
## [Monitorare il gateway VPN con Automazione di Azure](network-watcher-monitor-with-azure-automation.md)
## [Diagnosticare la connettività locale tramite il gateway VPN](network-watcher-diagnose-on-premises-connectivity.md)
## [Monitorare le connessioni di rete](connection-monitor.md)
## [Eseguire la conformità e il controllo nella rete usando PowerShell](network-watcher-nsg-auditing-powershell.md)
## [Analizzare i log del gateway applicazione in Azure Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)
## Acquisizione pacchetti
### [Trovare le anomalie](network-watcher-deep-packet-inspection.md)
### [Monitoraggio proattivo della rete con Funzioni di Azure](network-watcher-alert-triggered-packet-capture.md)
### [Eseguire il rilevamento di intrusioni usando strumenti open source](network-watcher-intrusion-detection-open-source-tools.md)
### [Visualizzare i modelli di traffico di rete usando strumenti open source](network-watcher-using-open-source-tools.md)
## Analizzare i log dei flussi dei gruppi di sicurezza di rete
### [Leggere i log del flusso del gruppo di sicurezza di rete](network-watcher-read-nsg-flow-logs.md)
### [Usare Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
### [Usare Elastic Stack](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
### [Usare Grafana](network-watcher-nsg-grafana.md)
### [Usare Graylog](network-watcher-analyze-nsg-flow-logs-graylog.md)
## [Determinare la latenza relativa tra una posizione e un'area di Azure](view-relative-latencies.md)

# riferimento
## [Interfaccia della riga di comando di Azure](/cli/azure/network/watcher)
## [Azure PowerShell](/powershell/module/azurerm.network/#network_watcher)
## [Java](/java/api/com.microsoft.azure.management.network)
## [Ruby](http://www.rubydoc.info/gems/azure_mgmt_network/Azure/Network/Mgmt/V2016_09_01/Models/NetworkWatcher) 
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.mgmt.network.html#azure.mgmt.network.NetworkManagementClient.network_watchers)

## [.NET](/dotnet/api)
## [REST](/rest/api/networkwatcher/)
# Risorse correlate
## [Rete virtuale](/azure/virtual-network/)
## [Macchine virtuali](/azure/virtual-machines/)
## [Gateway VPN](/azure/vpn-gateway/)
# Risorse
## [Roadmap per Azure](https://azure.microsoft.com/roadmap/)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork)
## [Prezzi](https://azure.microsoft.com/pricing/details/network-watcher/)
## [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=network-watcher)
## [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/network-watcher)
