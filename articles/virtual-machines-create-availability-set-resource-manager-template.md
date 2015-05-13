<properties 
	pageTitle="Creare un set di disponibilità utilizzando i modelli di Gestione risorse di Azure" 
	description="Viene descritto come utilizzare il modello di set di disponibilità e la sintassi dei modelli" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="kathydav"/>

# Creare un set di disponibilità utilizzando i modelli di Gestione risorse di Azure

È possibile creare facilmente un gruppo di disponibilità per una macchina virtuale utilizzando Azure PowerShell o xplat e un modello di Gestione risorse. Questo modello consente di creare un set di disponibilità.
 
Prima di iniziare, assicurarsi che Azure PowerShell ed xplat siano configurati e pronti all'uso. 

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up](../includes/xplat-getting-set-up.md)]


## [Eseguire un'operazione] con un modello di Gestione risorse mediante Azure PowerShell

Attenersi alla seguente procedura [eseguire un’operazione] utilizzando un modello di Gestione risorse nell'archivio dei modelli Github con Microsoft Azure PowerShell.

### Passaggio 1: scaricare il file JSON

Designare una cartella locale come posizione per i file di modello JSON e crearla \(ad esempio, C:\\Azure\\Templates\[thing\]\).

Sostituire il nome della cartella, quindi copiare ed eseguire questi comandi.

	$folderName="<folder name, such as C:\Azure\Templates\[thing]>"
	$webclient = New-Object System.Net.WebClient
	$url = "[Writers: add the URL to the RAW version of the target template in GitHub]"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath) 

### Passaggio 2: \(facoltativo\) visualizzare i parametri

Quando si [esegue un'operazione] con un modello, è necessario specificare un set di parametri di configurazione. Per visualizzare i parametri che è necessario specificare per il modello in un file JSON locale prima di eseguire il comando per creare la macchina virtuale, aprire il file JSON in un editor di testo o lo strumento di propria scelta. Cercare la sezione "parameters" nella parte superiore del file contenente il set di parametri necessari per configurare la macchina virtuale dal modello. Questa è la sezione **"parameters"** per il modello azuredeploy.json:

[Note to writers: Paste in the "parameters" section of the azuredeploy.json and format as code.]

### Passaggio 3: ottenere [informazioni necessarie per completare il modello].

[Note to writers: optional section to gather parameter values if needed.]

### Passaggio 4: [eseguire l’operazione] con il modello.

Inserire un nome per la distribuzione di Microsoft Azure, il nome gruppo di risorse, la posizione di Microsoft Azure, la cartella del file JSON salvato, quindi eseguire questi comandi.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\[thing]>" 
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Quando si esegue il comando **New-AzureResourceGroupDeployment**, verrà richiesto di fornire i valori dei parametri riportati nella sezione **"parameters"** del file JSON. Dopo aver eseguito questa operazione, il comando consente di creare il gruppo di risorse e il set di disponibilità.

Di seguito è riportato un esempio del set dei comandi PowerShell per il modello.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$folderName="C:\Azure\Templates\[thing]"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Dovrebbe essere visualizzata una schermata analoga alla seguente:

[Note to writers: paste in the PowerShell display for the first few prompted parameters, replacing this:]

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	dnsNameForPublicIP: 131.107.89.211
	adminUserName: WebAdmin1
	adminPassword: *******
	vmSourceImageName: a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd
	...

Per rimuovere il gruppo di risorse e tutte le relative risorse \(l'account di archiviazione, le macchine virtuali e la rete virtuale\), utilizzare questo comando.

	Remove-AzureResourceGroup –Name "<resource group name>"


## [Eseguire un'operazione] con un modello di Gestione risorse mediante xplat-cli

Attenersi alla seguente procedura per [eseguire un’operazione] utilizzando un modello di Gestione risorse nell'archivio dei modelli Github con Microsoft Azure PowerShell.

### Passaggio 1: scaricare il file JSON per il modello.

Designare una cartella locale come posizione per i file di modello JSON e crearla \(ad esempio, C:\\Azure\\Templates\[thing\]\).

Sostituire il nome della cartella, quindi eseguire questi comandi.

[Comandi di xplat per scaricare il file di modello]

### Passaggio 2: \(facoltativo\) visualizzare i parametri del modello.

Quando si [esegue un'operazione] con un modello, è necessario specificare un set di parametri di configurazione. Per visualizzare i parametri che è necessario specificare per il modello in un file JSON locale prima di eseguire il comando per creare la macchina virtuale, aprire il file JSON in un editor di testo o lo strumento di propria scelta. Cercare la sezione "parameters" nella parte superiore del file contenente il set di parametri necessari per configurare la macchina virtuale dal modello. Questa è la sezione **"parameters"** per il modello azuredeploy.json:

[Note to writers: Paste in the "parameters" section of the azuredeploy.json and format as code.]

### Passaggio 3: ottenere [informazioni necessarie per completare il modello].

[Note to writers: optional section to gather parameter values if needed.]

### Passaggio 4: [eseguire l’operazione] con il modello.

Compilare con le informazioni necessarie, quindi eseguire questi comandi.

[Comandi di xplat per eseguire il file di modello]

[Spiegazione della modalità di esecuzione del modello di xplat]


Di seguito è riportato un esempio del set dei comandi xplat per il modello.

[Esempio di comando xplat]

Dovrebbe essere visualizzata una schermata analoga alla seguente:

[Note to writers: paste in the xplat display for the first few prompted parameters]


Per rimuovere questo gruppo di risorse e tutte le relative risorse \([elementi nel gruppo di risorse]\), utilizzare questo comando.

[comando xplat]




<!--HONumber=52-->
