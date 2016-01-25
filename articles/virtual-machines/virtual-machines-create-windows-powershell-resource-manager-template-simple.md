<properties
	pageTitle="Creare una macchina virtuale con un modello | Microsoft Azure"
	description="Utilizzare un modello di Gestione risorse e Powershell di Microsoft Azure per creare una nuova macchina virtuale Windows."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="davidmu"/>

# Creare una macchina virtuale Windows con un modello di Gestione risorse e PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.


È possibile creare facilmente una nuova macchina virtuale Microsoft Azure utilizzando un modello di Gestione risorse con Azure PowerShell. Questo modello consente di creare una singola macchina virtuale su cui è in esecuzione Windows in una nuova rete virtuale con una singola subnet in un nuovo gruppo di risorse.

![](./media/virtual-machines-create-windows-powershell-resource-manager-template-simple/windowsvm.png)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Creare una macchina virtuale Windows

Attenersi alla seguente procedura per creare una macchina virtuale Windows utilizzando un modello di Gestione risorse nell'archivio dei modelli Github con Microsoft Azure PowerShell.

Nei comandi seguenti, inserire il nome che si desidera usare per la distribuzione, il nome per il nuovo gruppo di risorse e il percorso in cui la risorsa deve essere creata.


		$deployName="<deployment name>"
		$RGName="<resource group name>"
		$locName="<Azure location, such as West US>"
		$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
		New-AzureRmResourceGroup –Name $RGName –Location $locName
		New-AzureRmResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI


Quando si esegue il comando **New-AzureRmResourceGroupDeployment**, verrà richiesto di fornire i valori dei parametri riportati nella sezione "parameters" del file JSON. Dopo aver specificato tutti i valori dei parametri, il comando crea il gruppo di risorse e la macchina virtuale.

Dovrebbe essere visualizzata una schermata analoga alla seguente:


		cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
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
											Name                   Type                       Value
											===============        =========================  ==========
											newStorageAccountName  String                     newsaacct
											adminUsername          String                     WinAdmin1
											adminPassword          SecureString
											dnsNameForPublicIP     String                     contoso
											windowsOSVersion       String                     2012-R2-Datacenter
		Outputs           :


## Passaggi successivi

Scopri come gestire la macchina virtuale appena creata leggendo [Gestire le macchine virtuali usando Gestione risorse di Azure e PowerShell](virtual-machines-deploy-rmtemplates-powershell.md).

<!---HONumber=AcomDC_0114_2016-->