<properties
	pageTitle="Distribuzione delle farm di SharePoint con i modelli ARM|Microsoft Azure"
	description="Distribuire facilmente una farm di SharePoint a tre o nove server con i modelli di Gestione risorse e il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/21/2016"
	ms.author="josephd"/>

# Distribuzione delle farm di SharePoint con i modelli di Gestione risorse di Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica. Non è possibile creare questa risorsa con il modello di distribuzione classico.

Utilizzare le istruzioni riportate in questo articolo per distribuire una nuova farm di SharePoint Server 2013 a tre o nove server utilizzando i modelli di gestione risorse.

## Distribuire una farm di SharePoint a tre server

Per una farm di SharePoint Server 2013 base, un modello di gestione risorse crea tre macchine virtuali in una nuova rete virtuale in tre diverse subnet.

![](./media/virtual-machines-workload-template-sharepoint/three-server-sharepoint-farm.png)

È possibile eseguire il modello con il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure.

> [AZURE.NOTE] È anche possibile creare questa configurazione tramite l'elemento [SharePoint 2013 non-HA Farm](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/) nella sezione Marketplace del portale di Azure.

### Portale di Azure

Per distribuire il carico di lavoro usando un modello di Gestione risorse e il portale di Azure, fare clic [qui](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsharepoint-three-vm%2Fazuredeploy.json).

![](./media/virtual-machines-workload-template-sharepoint/azure-portal-template.png)

1.	Fare clic su **Parametri**. Nel riquadro**Parametri** immettere nuovi valori, selezionare dai valori consentiti o accettare i valori predefiniti e quindi fare clic su **OK**.
2.	Se necessario, fare clic su **Sottoscrizione** e selezionare la sottoscrizione di Azure corretta.
3.	Fare clic su **Gruppo di risorse** e selezionare un gruppo di risorse esistente. In alternativa, fare clic su **O crea nuovi** per creare una nuova istanza per questo carico di lavoro.
4.	Se necessario, fare clic su **Località del gruppo di risorse** e selezionare la località di Azure corretta.
6.	Fare clic su **Note legali** per esaminare le condizioni e il contratto che regolamentano l'utilizzo del modello e quindi fare clic su **Acquista**.
7.	Fare clic su **Crea**.

A seconda del modello, la compilazione del carico di lavoro richiede tempi diversi. Al termine, si disporrà di una nuova farm di SharePoint a tre server nel gruppo di risorse nuove o esistenti.

### Azure PowerShell

> [AZURE.NOTE] Il set di comandi seguente utilizza Azure PowerShell 1.0 e versioni successive. Per altre informazioni, vedere [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Inserire un nome per la distribuzione di Azure, un nuovo nome gruppo di risorse e la posizione del data center di Azure, nel seguente set di comandi. Rimuovere tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Di seguito è fornito un esempio.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Successivamente, eseguire il blocco di comandi nel prompt di Azure PowerShell.

Quando si esegue il comando **New-AzureRMResourceGroupDeployment**, verrà richiesto di fornire i valori per una serie di parametri. Dopo aver specificato i valori dei parametri, **New-AzureRMResourceGroupDeployment** crea e configura le macchine virtuali.

Al termine, si disporrà di una nuova farm di SharePoint a tre server nel gruppo di risorse nuove.

### Interfaccia della riga di comando di Azure

Prima di iniziare, accertarsi di avere la versione corretta di Azure CLI installata, di avere effettuato l'accesso e di avere impostato la nuova modalità di gestione delle risorse. Per informazioni dettagliate, fare clic [qui](virtual-machines-deploy-rmtemplates-azure-cli.md#getting-ready).

Per prima cosa, creare un nuovo gruppo di risorse. Utilizzare il seguente comando e specificare il nome del gruppo e la posizione del data center di Azure in cui si desidera distribuire.

	azure group create <group name> <location>

Successivamente, utilizzare il seguente comando e specificare il nome del nuovo gruppo di risorse e il nome di una distribuzione di Azure.

	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json <group name> <deployment name>

Di seguito è fornito un esempio.

	azure group create sp3serverfarm eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json sp3serverfarm spdevtest

Quando si esegue il comando **Crea gruppo di distribuzione Azure**, verrà richiesto di fornire i valori per una serie di parametri. Dopo aver specificato i valori dei parametri, Azure crea e configura le macchine virtuali.

Si avrà ora una nuova farm di SharePoint a tre server nel gruppo di risorse nuove.

## Distribuire una farm di SharePoint a nove server

Per una farm di SharePoint Server 2013 ad alta disponibilità, un modello di gestione risorse crea nove macchine virtuali in una nuova rete virtuale in quattro diverse subnet.

![](./media/virtual-machines-workload-template-sharepoint/nine-server-sharepoint-farm.png)

> [AZURE.NOTE] È anche possibile creare questa configurazione tramite l'elemento [SharePoint 2013 HA Farm](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/) nella sezione Marketplace del portale di Azure.

### Portale di Azure

Per distribuire il carico di lavoro usando un modello di Gestione risorse e il portale di Azure, fare clic [qui](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsharepoint-server-farm-ha%2Fazuredeploy.json).

![](./media/virtual-machines-workload-template-sharepoint/azure-portal-template.png)

1.	Fare clic su **Parametri**. Nel riquadro**Parametri** immettere nuovi valori, selezionare dai valori consentiti o accettare i valori predefiniti e quindi fare clic su **OK**.
2.	Se necessario, fare clic su **Sottoscrizione** e selezionare la sottoscrizione di Azure corretta.
3.	Fare clic su **Gruppo di risorse** e selezionare un gruppo di risorse esistente. In alternativa, fare clic su **O crea nuovi** per creare una nuova istanza per questo carico di lavoro.
4.	Se necessario, fare clic su **Località del gruppo di risorse** e selezionare la località di Azure corretta.
5.	Fare clic su **Note legali** per esaminare le condizioni e il contratto che regolamentano l'utilizzo del modello e quindi fare clic su **Acquista**.
6.	Fare clic su **Crea**.

A seconda del modello, la compilazione del carico di lavoro richiede tempi diversi. Al termine, si disporrà di una nuova farm di SharePoint a nove server nel gruppo di risorse nuove o esistenti.

### Azure PowerShell

> [AZURE.NOTE] Il set di comandi seguente utilizza Azure PowerShell 1.0 e versioni successive. Per altre informazioni, vedere [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Inserire un nome per la distribuzione di Azure, un nuovo nome gruppo di risorse e la posizione del data center di Azure, nel seguente set di comandi. Rimuovere tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Di seguito è fornito un esempio.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Successivamente, eseguire il blocco di comandi al prompt dei comandi di Azure PowerShell.

Quando si esegue il comando **New-AzureRMResourceGroupDeployment**, verrà richiesto di fornire i valori per una serie di parametri. Dopo aver specificato i valori dei parametri, **New-AzureRMResourceGroupDeployment** crea e configura le macchine virtuali.

Al termine, si disporrà di una nuova farm di SharePoint a nove server nel gruppo di risorse nuove.

### Interfaccia della riga di comando di Azure

Prima di iniziare, accertarsi di avere la versione corretta di Azure CLI installata, di avere effettuato l'accesso e di avere impostato la nuova modalità di gestione delle risorse. Per i dettagli, fare clic [qui](virtual-machines-deploy-rmtemplates-azure-cli.md#getting-ready).

Fare clic su Crea un nuovo gruppo di risorse. Utilizzare il seguente comando e specificare il nome del gruppo e la posizione del data center di Azure in cui si desidera distribuire.

	azure group create <group name> <location>

Successivamente, utilizzare il seguente comando e specificare il nome del nuovo gruppo di risorse e il nome di una distribuzione di Azure.

	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json <group name> <deployment name>

Di seguito è fornito un esempio.

	azure group create sphaserverfarm eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json sphaserverfarm spdevtest

Quando si esegue il comando **Crea gruppo di distribuzione Azure**, verrà richiesto di fornire i valori per una serie di parametri. Dopo aver specificato i valori dei parametri, Azure crea e configura le macchine virtuali.

Al termine, si disporrà di una nuova farm di SharePoint Server 2013 a nove server nel gruppo di risorse nuove.


## Passaggio successivo

- Trovare altre configurazioni di [SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) nei servizi di infrastruttura di Azure.

<!---HONumber=AcomDC_0128_2016-->