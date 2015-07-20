<properties
	pageTitle="Creare una macchina virtuale Windows con un modello di Gestione risorse e PowerShell"
	description="Utilizzare un modello di Gestione risorse e Powershell di Microsoft Azure per creare una nuova macchina virtuale Windows."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="josephd"/>

# Creazione di una macchina virtuale Windows con un modello di Gestione risorse e Azure PowerShell

È possibile creare facilmente una nuova macchina virtuale Azure basata su Windows utilizzando un modello di Gestione risorse con Azure PowerShell. Questo modello consente di creare una singola macchina virtuale su cui è in esecuzione Windows in una nuova rete virtuale con una singola subnet in un nuovo gruppo di risorse.

![](./media/virtual-machines-create-windows-powershell-resource-manager-template-simple/windowsvm.png)

Prima di iniziare, seguire queste istruzioni per assicurarsi di disporre di Azure, Windows PowerShell e Azure PowerShell configurati e pronti all'uso:

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## Creazione di una macchina virtuale Windows

Attenersi alla procedura seguente per creare una macchina virtuale Windows utilizzando un modello di Gestione risorse nel repository dei modelli Github con Azure PowerShell.

Eseguire i comandi in base all’esempio riportato di seguito e fornire il nome di distribuzione di Azure, il nome del gruppo di risorse e la posizione del datacenter di Azure. Quando si esegue il comando **New-AzureResourceGroupDeployment**, verrà richiesto di fornire i valori dei parametri riportati nella sezione "parameters" del file JSON. Dopo aver specificato tutti i valori dei parametri, il comando crea il gruppo di risorse e la macchina virtuale.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Verrà visualizzata una schermata simile alla seguente:

	cmdlet New-AzureResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: newsaacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso
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
	                    newStorageAccountName  String                     newsaacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso
	                    windowsOSVersion  String                     2012-R2-Datacenter

	Outputs           :

È ora disponibile una nuova macchina virtuale Windows denominata MyWindowsVM nel nuovo gruppo di risorse.

## Risorse aggiuntive

[Provider di calcolo, rete e archiviazione di Azure sotto Gestione risorse di Azure](virtual-machines-azurerm-versus-azuresm.md)

[Panoramica di Gestione risorse di Microsoft Azure](../resource-group-overview.md)

[Creare una macchina virtuale Windows con Gestione risorse di Azure e Azure PowerShell](virtual-machines-create-windows-powershell-resource-manager.md)

[Creare una macchina virtuale Windows con Azure PowerShell e Gestione servizi di Azure](virtual-machines-create-windows-powershell-service-manager.md)

[Macchine virtuali - Documentazione](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Come installare e configurare Azure PowerShell](../powershell-install-configure.md)

<!---HONumber=July15_HO2-->