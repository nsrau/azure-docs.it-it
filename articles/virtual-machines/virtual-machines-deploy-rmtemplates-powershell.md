<properties
	pageTitle="Gestire le macchine virtuali di gestione delle risorse di Azure | Microsoft Azure"
	description="Gestire le macchine virtuali usando Gestione risorse di Azure, i modelli e PowerShell."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="davidmu"/>

# Gestire le macchine virtuali usando Gestione risorse di Azure e PowerShell

> [AZURE.SELECTOR]
- [Preview Portal](virtual-machines-windows-tutorial.md)
- [PowerShell - Windows](virtual-machines-deploy-rmtemplates-powershell.md)
- [Azure CLI](virtual-machines-deploy-rmtemplates-azure-cli.md)

L'uso dei modelli di Azure PowerShell e Gestione risorse offre numerosi vantaggi e grande flessibilità nella gestione delle risorse in Microsoft Azure. È possibile usare le attività disponibili in questo articolo per creare e gestire le risorse delle macchine virtuali.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-tutorial-classic-portal.md).

Queste attività usano un modello di Gestione risorse e PowerShell:

- [Creare una macchina virtuale](#windowsvm)
- [Creare una macchina virtuale con un disco specializzato](#customvm)
- [Creare più macchine virtuali in una rete virtuale con un servizio di bilanciamento del carico esterno](#multivm)

Queste attività usano solo PowerShell:

- [Rimuovere un gruppo di risorse](#removerg)
- [Visualizzare informazioni relative a una macchina virtuale](#displayvm)
- [Avviare una macchina virtuale](#start)
- [Arrestare una macchina virtuale](#stop)
- [Riavviare una macchina virtuale](#restart)
- [Eliminare una macchina virtuale](#delete)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Modelli e gruppi di risorse di Gestione risorse di Azure

Alcune delle attività incluse in questo articolo illustrano come usare i modelli di Gestione risorse di Azure e PowerShell per distribuire e gestire automaticamente le macchine virtuali di Azure.

La maggior parte delle applicazioni in esecuzione in Microsoft Azure è basata su una combinazione di diversi tipi di risorse cloud, ad esempio una o più macchine virtuali e account di archiviazione, un database SQL o una rete virtuale. I modelli di Gestione risorse di Azure consentono di gestire le diverse risorse insieme usando una descrizione JSON delle risorse e dei parametri di configurazione e di distribuzione associati.

Dopo la definizione di un modello di risorsa basato su JSON, sarà possibile usarlo con un comando PowerShell per distribuire le risorse definite in Azure. È possibile eseguire questi comandi separatamente nella shell dei comandi PowerShell o integrandoli con uno script che contenga la logica di automazione aggiuntiva.

Le risorse create con i modelli di Gestione risorse di Azure verranno distribuite in un *Gruppo di risorse di Azure* nuovo o esistente. Un gruppo di risorse consente di gestire insieme più risorse distribuite sotto forma di gruppo logico. Sarà quindi possibile gestire il ciclo di vita complessivo del gruppo/applicazione.

Se si è interessati alla creazione di modelli, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).

### Creare un gruppo di risorse

Per le attività di creazione di una risorsa, sarà necessario un gruppo di risorse, se non è già disponibile.

Nel comando seguente sostituire *resource group name* con il nome del nuovo gruppo di risorse e *Azure location* con la posizione del data center di Azure in cui si vuole posizionare la risorsa, quindi eseguirlo:

	New-AzureRmResourceGroup -Name "resource group name" -Location "Azure location"

## <a id="windowsvm"></a>ATTIVITÀ: Creare una macchina virtuale

Questa attività usa un modello della raccolta di modelli. Per altre informazioni sul modello, vedere l'articolo relativo alla [distribuzione di una semplice macchina virtuale di Windows negli Stati Uniti occidentali](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).

![](./media/virtual-machines-deploy-rmtemplates-powershell/windowsvm.png)

Nel comando seguente sostituire *deployment name* con il nome da usare per la distribuzione e *resource group name* con il nome del gruppo di risorse esistente, quindi eseguirlo:

	New-AzureRmResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"

Ad esempio:

	New-AzureRmResourceGroupDeployment -Name "TestDeployment" -ResourceGroupName "TestRG" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"

Verrà chiesto di fornire i valori dei parametri nella sezione **parameters** del file JSON:

	cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso

Verrà visualizzata una schermata simile alla seguente:

	VERBOSE: 10:56:59 AM - Template is valid.
	VERBOSE: 10:56:59 AM - Create template deployment 'TestDeployment'.
	VERBOSE: 10:57:08 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 10:57:11 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is running
	VERBOSE: 10:57:11 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is running
	VERBOSE: 10:57:38 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is succeeded
	VERBOSE: 10:57:40 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 10:57:45 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 10:57:45 AM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 11:01:59 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded


	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 6:02:13 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    	Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     saacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso9875
	                    windowsOSVersion  String                     2012-R2-Datacenter

	Outputs           :

Il video seguente illustra l'esecuzione di questa attività:

[AZURE.VIDEO deploy-a-windows-virtual-machine-with-azure-resource-manager-templates-and-powershell]

## <a id="customvm"></a>ATTIVITÀ: Creare una macchina virtuale con un disco specializzato

Questa attività usa un modello della raccolta di modelli. Per altre informazioni sul modello, vedere l'articolo relativo alla [creazione di una macchina virtuale da un disco VHD specializzato](https://azure.microsoft.com/documentation/templates/201-vm-from-specialized-vhd/).

Nel comando seguente sostituire *deployment name* con il nome da usare per la distribuzione e *resource group name* con il nome del gruppo di risorse esistente, quindi eseguirlo:

	New-AzureRmResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"

Ad esempio:

	New-AzureRmResourceGroupDeployment -Name "TestDeployment" -ResourceGroupName "TestRG" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"

Verrà chiesto di fornire i valori dei parametri nella sezione **parameters** del file JSON:

	cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	osDiskVhdUri: http://saacct.blob.core.windows.net/vhds/osdiskforwindows.vhd
	osType: windows
	location: West US
	vmSize: Standard_A3
	...

> [AZURE.NOTE]L'esempio precedente usa un file vhd disponibile nell'account di archiviazione saacct. Il nome del disco è stato fornito come parametro per il modello.

Il video seguente illustra l'esecuzione di questa attività:

[AZURE.VIDEO create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell]

## <a id="multivm"></a>ATTIVITÀ: Creare più macchine virtuali in una rete virtuale con un servizio di bilanciamento del carico esterno

Questa attività usa un modello della raccolta di modelli. Per altre informazioni sul modello, vedere l'articolo relativo alla [creazione di una macchina virtuale da un disco VHD specializzato](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/).

![](./media/virtual-machines-deploy-rmtemplates-powershell/multivmextlb.png)

Nel comando seguente sostituire *deployment name* con il nome da usare per la distribuzione e *resource group name* con il nome del gruppo di risorse esistente, quindi eseguirlo:

	New-AzureRmResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json"

Verrà chiesto di fornire i valori dei parametri nella sezione **parameters** del file JSON:

	cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	adminUserName: WebAdmin1
	adminPassword: *******
	dnsNameforLBIP: web07
	backendPort: 80
	vmNamePrefix: WEBFARM
	...

Il video seguente illustra l'esecuzione di questa attività:

[AZURE.VIDEO deploy-multi-vm-app-with-a-virtual-network-and-load-balancer-in-azure-resource-manager]

## <a id="removerg"></a>ATTIVITÀ: Rimuovere un gruppo di risorse

Nel comando seguente sostituire *resource group name* con il nome del gruppo di risorse da rimuovere, quindi eseguirlo:

	Remove-AzureRmResourceGroup  -Name "resource group name"

> [AZURE.NOTE]È possibile utilizzare il parametro **-Force** per ignorare la richiesta di conferma.

Se non si usa il parametro -Force, verrà richiesta una conferma:

	Confirm
	Are you sure you want to remove resource group 'BuildRG'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Il video seguente illustra l'esecuzione di questa attività:

[AZURE.VIDEO removing-a-resource-group-in-azure]

## <a id="displayvm"></a>ATTIVITÀ: Visualizzare informazioni relative a una macchina virtuale

Nel comando seguente sostituire *resource group name* con il nome del gruppo di risorse che include la macchina virtuale e *VM name* con il nome della macchina virtuale, quindi eseguirlo:

	Get-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Verrà visualizzata una schermata simile alla seguente:

	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          : {
	                             "VirtualMachineSize": "Standard_D1"
	                           }
	Id                       : /subscriptions/{subscription-id}/resourceGroups/BuildRG/providers/Microso
	                           ft.Compute/virtualMachines/MyWindowsVM
	InstanceView             : null
	Location                 : westus
	Name                     : MyWindowsVM
	NetworkProfile           : {
	                             "NetworkInterfaces": [
	                               {
	                                 "Primary": null,
	                                 "ReferenceUri": "/subscriptions/{subscription-id}/resourceGroups/Bu
	                           ildRG/providers/Microsoft.Network/networkInterfaces/myVMNic"
	                               }
	                             ]
	                           }
	OSProfile                : {
	                             "AdminPassword": null,
	                             "AdminUsername": "WinAdmin1",
	                             "ComputerName": "MyWindowsVM",
	                             "CustomData": null,
	                             "LinuxConfiguration": null,
	                             "Secrets": [],
	                             "WindowsConfiguration": {
	                               "AdditionalUnattendContents": [],
	                               "EnableAutomaticUpdates": true,
	                               "ProvisionVMAgent": true,
	                               "TimeZone": null,
	                               "WinRMConfiguration": null
	                             }
	                           }
	Plan                     : null
	ProvisioningState        : Succeeded
	StorageProfile           : {
	                             "DataDisks": [],
	                             "ImageReference": {
	                               "Offer": "WindowsServer",
	                               "Publisher": "MicrosoftWindowsServer",
	                               "Sku": "2012-R2-Datacenter",
	                               "Version": "latest"
	                             },
	                             "OSDisk": {
	                               "OperatingSystemType": "Windows",
	                               "Caching": "ReadWrite",
	                               "CreateOption": "FromImage",
	                               "Name": "osdisk",
	                               "SourceImage": null,
	                               "VirtualHardDisk": {
	                                 "Uri": "http://saacct.blob.core.windows.net/vhds/osdiskforwindowssimple.vhd"
	                               }
	                             },
	                             "SourceImage": null
	                           }
	Tags                     : {}
	Type                     : Microsoft.Compute/virtualMachines

Il video seguente illustra l'esecuzione di questa attività:

[AZURE.VIDEO displaying-information-about-a-virtual-machine-in-microsoft-azure-with-powershell]

## <a id="start"></a>ATTIVITÀ: Avviare una macchina virtuale

Nel comando seguente sostituire *resource group name* con il nome del gruppo di risorse che include la macchina virtuale e *VM name* con il nome della macchina virtuale, quindi eseguirlo:

	Start-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Verrà visualizzata una schermata simile alla seguente:

	EndTime             : 4/28/2015 11:11:41 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	Status              : Succeeded
	TrackingOperationId : e1705973-d266-467e-8655-920016145347
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	StatusCode          : OK

Il video seguente illustra l'esecuzione di questa attività:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="stop"></a>ATTIVITÀ: Arrestare una macchina virtuale

Nel comando seguente sostituire *resource group name* con il nome del gruppo di risorse che include la macchina virtuale e *VM name* con il nome della macchina virtuale, quindi eseguirlo:

	Stop-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Verrà richiesta una conferma:

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Verrà visualizzata una schermata simile alla seguente:

	EndTime             : 4/28/2015 11:09:08 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:06:55 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 0c94dc74-c553-412c-a187-108bdb29657e
	RequestId           : 5cc9ddba-0643-4b5e-82b6-287b321394ee
	StatusCode          : OK

Il video seguente illustra l'esecuzione di questa attività:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="restart"></a>ATTIVITÀ: Riavviare una macchina virtuale

Nel comando seguente sostituire *resource group name* con il nome del gruppo di risorse che include la macchina virtuale e *VM name* con il nome della macchina virtuale, quindi eseguirlo:

	Restart-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Verrà visualizzata una schermata simile alla seguente:

	EndTime             : 4/28/2015 11:16:26 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:16:25 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 390571e0-c804-43ce-88c5-f98e0feb588e
	RequestId           : 7dac33e3-0164-4a08-be33-96205284cb0b
	StatusCode          : OK

Il video seguente illustra l'esecuzione di questa attività:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="delete"></a>ATTIVITÀ: Eliminare una macchina virtuale

Nel comando seguente sostituire *resource group name* con il nome del gruppo di risorse che include la macchina virtuale e *VM name* con il nome della macchina virtuale, quindi eseguirlo:

	Remove-AzureRmVM -ResourceGroupName "resource group name" –Name "VM name"

> [AZURE.NOTE]È possibile utilizzare il parametro **-Force** per ignorare la richiesta di conferma.

Se non si usa il parametro -Force, verrà richiesta una conferma:

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Verrà visualizzata una schermata simile alla seguente:

	EndTime             : 4/28/2015 11:21:55 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:20:13 AM -07:00
	Status              : Succeeded
	TrackingOperationId : f74fad9e-f6bc-46ae-82b1-bfad3952aa44
	RequestId           : 6a30d2e0-63ca-43cf-975b-058631e048e7
	StatusCode          : OK

Il video seguente illustra l'esecuzione di questa attività:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## Risorse aggiuntive
[Modelli di avvio rapido di Azure](http://azure.microsoft.com/documentation/templates/) e [Framework app](virtual-machines-app-frameworks.md)

[Provider di calcolo, rete e archiviazione in Gestione risorse di Azure](virtual-machines-azurerm-versus-azuresm.md)

[Panoramica di Gestione risorse di Azure](resource-group-overview.md)

[Macchine virtuali - Documentazione](http://azure.microsoft.com/documentation/services/virtual-machines/)

<!---HONumber=Oct15_HO4-->