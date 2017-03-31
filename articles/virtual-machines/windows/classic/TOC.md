# Panoramica
## [Informazioni sulle macchine virtuali](../../virtual-machines-windows-about.md)
## [Dischi e dischi rigidi virtuali](../../../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
## [Reti virtuali](../../../virtual-network/virtual-networks-overview.md)
## [Domande frequenti](faq.md)
## [Confrontare macchine virtuali, siti Web e servizi cloud](../../../app-service-web/choose-web-site-cloud-service-vm.md)
## [Contenitori](../../virtual-machines-windows-containers.md)

# Introduzione
## [Creare una macchina virtuale con il portale](tutorial.md)
## [Accedere a una VM](connect-logon.md)
## [Installare Azure PowerShell](/powershell/azureps-cmdlets-docs)
## [Installare l'interfaccia da riga di comando di Azure](../../../cli-install-nodejs.md)

# Procedure

## Usare le risorse di archiviazione
### [Collegare un disco dati](attach-disk.md)
### [Scollegare un disco dati](detach-disk.md)
### [Usare l'unità D: come disco dati](../../virtual-machines-windows-change-drive-letter.md)

## Rete
### [Come configurare gli endpoint](setup-endpoints.md)
### [Connect VMs with a VNet or Cloud Service](connect-vms.md) (Connettere VM a una rete virtuale o a un servizio cloud)
### [Connect Classic VNets to Resource Manager VNets](../../../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md) (Connettere reti virtuali classiche a reti virtuali di Resource Manager)
### [Creare un servizio di bilanciamento del carico](../../../load-balancer/load-balancer-get-started-internet-classic-portal.md)
### [Gestire NSG usando Azure PowerShell](../../../virtual-network/virtual-networks-create-nsg-classic-ps.md)

## Distribuisci
### [Creare una VM personalizzata](createportal.md)
### [Create and configure a VM using Azure PowerShell](create-powershell.md) (Creare e configurare una VM tramite Azure PowerShell)
### [Capture a Windows VM](capture-image.md) (Acquisire una VM Windows)
### [Create and upload a VHD using PowerShell](createupload-vhd.md) (Creare e caricare un disco rigido virtuale tramite PowerShell)
### [Automatizzare la distribuzione di macchine virtuali di Azure con Chef](../../virtual-machines-windows-chef-automation.md)
### [Create and Manage VMs in Visual Studio](manage-visual-studio.md) (Creare e gestire VM in Visual Studio)
### [Creare una macchina virtuale per un'app Web con Visual Studio](web-app-visual-studio.md)
### [Eseguire un'attività a elevato utilizzo di calcolo in Java](java-run-compute-intensive-task.md)
### [Applicazione Web Django Hello World](python-django-web-app.md)

## Configurare
### [Reimpostare una password o il servizio Desktop remoto](../../virtual-machines-windows-reset-rdp.md)
### [Installare e configurare Symantec Endpoint Protection](install-symantec.md)
### [Installare e configurare Trend Micro Deep Security come servizio](install-trend.md)
### [Configurare un set di disponibilità](configure-availability.md)
### [Resize a Windows VM created in the classic deployment model](resize-vm.md) (Ridimensionare una VM Windows create nel modello di distribuzione classica)

## Manage
### [Migrazione dal modello classico a Resource Manager](../../virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
### [Manage your VMs using Azure PowerShell](manage-psh.md) (Gestire le VM con Azure PowerShell)
### [Informazioni sull'agente e sulle estensioni delle VM](agents-and-extensions.md)
### [Gestire le estensioni di VM](manage-extensions.md)
### [Estensione Script personalizzato per le VM](extensions-customscript.md)
### [Inserimento dei dati personalizzati in una VM di Azure](inject-custom-data.md)

## Pianificazione
### [About images](about-images.md) (Informazioni sulle immagini)
### [Dimensioni delle VM](../../virtual-machines-windows-sizes.md)
#### [Informazioni sulle macchine virtuali serie H e serie A a elevato utilizzo di calcolo](../../virtual-machines-windows-a8-a9-a10-a11-specs.md)
### [Manutenzione pianificata per le VM di Azure](../../virtual-machines-windows-planned-maintenance.md)
### [Linee guida sull'implementazione dei servizi di infrastruttura di Azure](../../virtual-machines-windows-infrastructure-subscription-accounts-guidelines.md)

## Gestire i carichi di lavoro
### [High-performance Computing (HPC)](../../virtual-machines-windows-hpcpack-cluster-options.md)
#### [Ridimensionare automaticamente le risorse](hpcpack-cluster-node-autogrowshrink.md)
#### [Gestire i nodi di calcolo](hpcpack-cluster-node-manage.md)
#### [Creare un cluster](hpcpack-cluster-powershell-script.md)
#### [Configurare un cluster per l'esecuzione di applicazioni MPI](hpcpack-rdma-cluster.md)
#### [Eseguire carichi di lavoro di Excel e SOA](../../virtual-machines-windows-excel-cluster-hpcpack.md)
#### [Creare il nodo head con un'immagine del Marketplace](../../virtual-machines-windows-hpcpack-cluster-headnode.md)
#### [Inviare processi da locale ad Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md)
### [MongoDB](install-mongodb.md)
### [MySQL](mysql-2008r2.md)
### [Oracle](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html#support)
####[Configurare Oracle Data Guard per Azure](configure-oracle-data-guard.md)
####[Configurare Oracle GoldenGate per Azure](configure-oracle-goldengate.md)
####[Considerazioni varie sulle immagini](oracle-considerations.md)
####[Elenco di immagini VM Oracle](oracle-images.md)
### [SAP](sap-get-started.md)
### [SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
### [Tomcat](java-run-tomcat-app-server.md)

## Risoluzione dei problemi
### [Connessioni Desktop remoto](../../virtual-machines-windows-troubleshoot-rdp-connection.md)
####[Procedura dettagliata per la risoluzione dei problemi di connessione di Desktop remoto](../../virtual-machines-windows-detailed-troubleshoot-rdp.md)
### [Accedere a un'applicazione](../../virtual-machines-windows-troubleshoot-app-connection.md)
### [Problemi della distribuzione classica con la creazione di una nuova VM](troubleshoot-deployment-new-vm.md)
### [Problemi della distribuzione classica con il riavvio o il ridimensionamento di una VM esistente](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
## [Reimpostare la password RDP](reset-rdp.md)

# Riferimento
## [PowerShell](/powershell/azureps-cmdlets-docs)
## [Interfaccia della riga di comando di Azure](/cli/azure/vm)
## [Java](/java/api)
## [.NET](/dotnet/api/microsoft.azure.management.compute)
## [Creare modelli di Resource Manager](../../../resource-group-authoring-templates.md)
## [Modelli della community](https://azure.microsoft.com/documentation/templates)
## [REST di calcolo](/rest/api/compute)
## [REST di rete](/rest/api)
## [REST di archiviazione](/rest/api/storageservices)

# Risorse
## [Prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)
## [Disponibilità internazionale](https://azure.microsoft.com/regions/services/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-machine)
## [Video](https://azure.microsoft.com/documentation/videos/index/?services=virtual-machines)
