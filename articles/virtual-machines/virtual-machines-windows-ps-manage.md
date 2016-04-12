<properties
	pageTitle="Gestire le macchine virtuali di gestione delle risorse di Azure | Microsoft Azure"
	description="Gestire le macchine virtuali usando Gestione risorse di Azure, i modelli e PowerShell."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="davidmu"/>

# Gestire le macchine virtuali usando Gestione risorse di Azure e PowerShell

> [AZURE.SELECTOR]
- [PowerShell](virtual-machines-windows-ps-manage.md)
- [CLI](virtual-machines-linux-cli-deploy-templates.md)

<br>


L'uso dei modelli di Azure PowerShell e Gestione risorse offre numerosi vantaggi e grande flessibilità nella gestione delle risorse in Microsoft Azure. È possibile usare le attività disponibili in questo articolo per gestire le risorse delle macchine virtuali.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.

Queste attività usano solo PowerShell:

- [Visualizzare informazioni relative a una macchina virtuale](#displayvm)
- [Avviare una macchina virtuale](#start)
- [Arrestare una macchina virtuale](#stop)
- [Riavviare una macchina virtuale](#restart)
- [Eliminare una macchina virtuale](#delete)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## <a id="displayvm"></a>Visualizzare informazioni relative a una macchina virtuale

Nel comando seguente sostituire *resource group name* con il nome del gruppo di risorse che include la macchina virtuale e *VM name* con il nome della macchina virtuale, quindi eseguirlo:

	Get-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Verrà visualizzata una schermata simile alla seguente:


	ResourceGroupName        : rg1
	Id                       : /subscriptions/{subscription-id}/resourceGroups/
															rg1/providers/Microsoft.Compute/virtualMachines/vm1
	Name                     : vm1
	Type                     : Microsoft.Azure.Management.Compute.Models.VirtualMachineGetResponse
	Location                 : westus
	Tags                     : {}
	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          :  {
																"VirtualMachineSize": "Standard_D1"
															}
	InstanceView             : null
	Location                 : westus
	Name                     : vm1
	NetworkProfile           :  {
																"NetworkInterfaces": [
																	{
																		"Primary": null,
																		"ReferenceUri": "/subscriptions/{subscription-id}/resourceGroups/
																		rg1/providers/Microsoft.Network/networkInterfaces/nc1"
																	}
																]
															}
	OSProfile                :  {
																"AdminPassword": null,
																"AdminUsername": "WinAdmin1",
																"ComputerName": "vm1",
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
	StorageProfile           : 	{
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
																		"Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
																	}
																}
															}
	DataDiskNames            :  {}
	NetworkInterfaceIDs      : { /subscriptions/{subscription-id}/resourceGroups/
																rg1/providers/Microsoft.Network/networkInterfaces/nc1}

Il video seguente illustra l'esecuzione di questa attività:

[AZURE.VIDEO displaying-information-about-a-virtual-machine-in-microsoft-azure-with-powershell]

## <a id="start"></a>Avviare una macchina virtuale

Nel comando seguente sostituire *resource group name* con il nome del gruppo di risorse che include la macchina virtuale e *VM name* con il nome della macchina virtuale, quindi eseguirlo:

	Start-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Verrà visualizzata una schermata simile alla seguente:

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 06935ddf-6e89-48d2-b46a-229493e3e9d1
	Output              :
	Error               :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	EndTime             : 4/28/2015 11:11:41 AM -07:00
	TrackingOperationId : c1aa0a70-4f4f-4d6c-a8ac-7ea35c004ce0

Il video seguente illustra l'esecuzione di questa attività:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="stop"></a>Arrestare una macchina virtuale

Nel comando seguente sostituire *resource group name* con il nome del gruppo di risorse che include la macchina virtuale e *VM name* con il nome della macchina virtuale, quindi eseguirlo:

	Stop-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Verrà richiesta una conferma:

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Verrà visualizzata una schermata simile alla seguente:

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	Output              :
	Error               :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	EndTime             : 4/28/2015 11:11:41 AM -07:00
	TrackingOperationId : e1705973-d266-467e-8655-920016145347

Il video seguente illustra l'esecuzione di questa attività:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="restart"></a>Riavviare una macchina virtuale

Nel comando seguente sostituire *resource group name* con il nome del gruppo di risorse che include la macchina virtuale e *VM name* con il nome della macchina virtuale, quindi eseguirlo:

	Restart-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Verrà visualizzata una schermata simile alla seguente:

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 4b05891c-fdff-4c9a-89ca-e4f1d7691aed
	Output              :
	Error               :
	StartTime           : 1/5/2016 12:06:53 PM -08:00
	EndTime             : 1/5/2016 12:06:54 PM -08:00
	TrackingOperationId : 5aeeab89-45ab-41b9-84ef-9e9a7e732207


Il video seguente illustra l'esecuzione di questa attività:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="delete"></a>Eliminare una macchina virtuale

Nel comando seguente sostituire *resource group name* con il nome del gruppo di risorse che include la macchina virtuale e *VM name* con il nome della macchina virtuale, quindi eseguirlo:

	Remove-AzureRmVM -ResourceGroupName "resource group name" –Name "VM name"

> [AZURE.NOTE] È possibile utilizzare il parametro **-Force** per ignorare la richiesta di conferma.

Se non si usa il parametro -Force, verrà richiesta una conferma:

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Verrà visualizzata una schermata simile alla seguente:

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 2d723b40-ce1f-4b11-a603-dc659a13b6f0
	Output              :
	Error               :
	StartTime           : 1/5/2016 12:10:28 PM -08:00
	EndTime             : 1/5/2016 12:12:12 PM -08:00
	TrackingOperationId : d138ab29-83bf-4948-9d13-dab87db1a639

Il video seguente illustra l'esecuzione di questa attività:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

<!---HONumber=AcomDC_0323_2016-->