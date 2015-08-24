<properties
	pageTitle="Creare un set di disponibilità utilizzando i modelli di Gestione risorse di Azure"
	description="Viene descritto come utilizzare il modello di set di disponibilità e la sintassi dei modelli"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2015"
	ms.author="kathydav"/>

# Creare un set di disponibilità utilizzando i modelli di Gestione risorse di Azure

È possibile creare facilmente un set di disponibilità per una macchina virtuale utilizzando Azure PowerShell o l'interfaccia della riga di comando di Azure e un modello di Gestione risorse. Questo modello consente di creare un set di disponibilità.

Prima di iniziare, assicurarsi che Azure PowerShell e l’interfaccia della riga di comando di Azure siano configurati e pronti all'uso.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up](../../includes/xplat-getting-set-up.md)]


## Creare un set di disponibilità con un modello di Gestione risorse

Attenersi alla seguente procedura per creare un set di disponibilità per una macchina virtuale utilizzando un modello di Gestione risorse nell'archivio dei modelli Github con Microsoft Azure PowerShell.

### Passaggio 1: scaricare il file JSON

Designare una cartella locale come posizione per i file di modello JSON e crearla (ad esempio, C:\\Azure\\Templates\\availability).

Sostituire il nome della cartella, quindi copiare ed eseguire questi comandi.

	$folderName="<folder name, such as C:\Azure\Templates\availability>"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-2-FDs-no-resource-loops/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)

### Passaggio 2: raccogliere i dettagli per i parametri obbligatori

Quando si utilizza un modello, sarà necessario fornire dettagli quali posizione e nome del set. Per scoprire quali parametri sono necessari per un modello, effettuare una delle seguenti operazioni:

- Esaminare l'elenco di parametri [qui](http://azure.microsoft.com/documentation/templates/201-2-vms-2-FDs-no-resource-loops/).
- Aprire il file JSON in un editor di testo o strumento di propria scelta. Cercare la sezione "parameters" nella parte superiore del file contenente il set di parametri necessari per configurare la macchina virtuale dal modello.

Raccogliere le informazioni necessarie per poterle immettere. Tali informazioni verranno richieste quando si esegue il comando per distribuire il modello.

### Passaggio 3: Creare il set di disponibilità

Nelle sezioni seguenti viene illustrato come utilizzare Azure PowerShell o l'interfaccia della riga di comando di Azure per eseguire questa operazione.

### Uso di Azure PowerShell

Inserire un nome per la distribuzione di Microsoft Azure, il nome gruppo di risorse, la posizione di Microsoft Azure, la cartella del file JSON salvato, quindi eseguire questi comandi.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\availability>"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Quando si esegue il comando **New-AzureResourceGroupDeployment**, viene richiesto di fornire i valori dei parametri riportati nella sezione **"parameters"** del file JSON. Dopo aver eseguito questa operazione, il comando consente di creare il gruppo di risorse e il set di disponibilità.

Di seguito è riportato un esempio del set dei comandi PowerShell per il modello.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$folderName="C:\Azure\Templates[thing]"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Dovrebbe essere visualizzata una schermata analoga alla seguente:

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	dnsNameForPublicIP: 131.107.89.211
	adminUserName: WebAdmin1
	adminPassword: *******
	vmSourceImageName: a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd
	...

Per rimuovere il gruppo di risorse e tutte le relative risorse (l'account di archiviazione, le macchine virtuali e la rete virtuale), utilizzare questo comando.

	Remove-AzureResourceGroup –Name "<resource group name>"


## Utilizzare l'interfaccia della riga di comando di Azure

Attenersi alla seguente procedura per creare un set di disponibilità utilizzando un modello di Gestione risorse nell'archivio dei modelli Github con un comando dell’interfaccia della riga di comando di Azure.

	azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-2-FDs-no-resource-loops/azuredeploy.json

<!---HONumber=August15_HO7-->