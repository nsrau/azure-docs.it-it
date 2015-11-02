<properties
	pageTitle="WordPress su Ubuntu con il modello ARM | Microsoft Azure"
	description="Distribuire facilmente un singolo server WordPress in Ubuntu utilizzando un modello di gestione delle risorse e il portale di anteprima di Azure, Azure PowerShell o Azure CLI."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="davidmu"/>

# Distribuzione di un server WordPress in Ubuntu con un modello di Gestione risorse di Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica. Non è possibile creare questa risorsa con il modello di distribuzione classica.

Utilizzare le istruzioni riportate in questo articolo per distribuire un dominio WordPress in Ubuntu utilizzando un modello di gestione delle risorse. Questo modello consente di creare una singola macchina virtuale in una nuova rete virtuale.

![](./media/virtual-machines-workload-template-wordpress/one-server-wordpress.png)

È possibile eseguire il modello con il portale di anteprima di Azure, Azure PowerShell o con l'interfaccia della riga di comando di Azure.

## Portale di anteprima di Azure

Per distribuire il carico di lavoro usando un modello di gestione risorse e il portale di anteprima di Azure, fare clic [qui](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwordpress-single-vm-ubuntu%2Fazuredeploy.json).

![](./media/virtual-machines-workload-template-wordpress/azure-portal-template.png)

1.	Per il riquadro **Modello**, fare clic su **Salva**.
2.	Fare clic su **Parametri**. Nel riquadro **Parametri** immettere nuovi valori, selezionare dai valori consentiti o accettare i valori predefiniti e quindi fare clic su **OK**.
3.	Se necessario, fare clic su **Sottoscrizione** e selezionare la sottoscrizione di Azure corretta.
4.	Fare clic su **Gruppo di risorse** e selezionare un gruppo di risorse esistente. In alternativa, fare clic su **O crea nuovi** per creare una nuova istanza per questo carico di lavoro.
5.	Se necessario, fare clic su **Percorso del gruppo di risorse** e selezionare il percorso corretto di Azure.
6.	Se necessario, fare clic su **Note legali** per esaminare i termini e il contratto di utilizzo del modello.
7.	Fare clic su **Crea**.

A seconda del modello, la compilazione del carico di lavoro richiede tempi diversi. Al termine, si disporrà di un nuovo server WordPress in Ubuntu nel gruppo di risorse nuove o esistenti.

## Azure PowerShell

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Inserire un nome per la distribuzione di Azure, un nuovo nome gruppo di risorse e la posizione del data center di Azure, nel seguente set di comandi. Rimuovere tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/wordpress-single-vm-ubuntu/azuredeploy.json"
	New-AzureRmResourceGroup -Name $RGName -Location $locName
	New-AzureRmResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Di seguito è fornito un esempio.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/wordpress-single-vm-ubuntu/azuredeploy.json"
	New-AzureRmResourceGroup -Name $RGName -Location $locName
	New-AzureRmResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Successivamente, eseguire il blocco di comandi nel prompt di Azure PowerShell.

Quando si esegue il comando **New-AzureRmResourceGroupDeployment**, verrà richiesto di fornire i valori per una serie di parametri. Dopo avere specificato tutti i valori dei parametri, **New-AzureRmResourceGroupDeployment** crea e configura le macchine virtuali.

Al termine, si disporrà di un nuovo server WordPress in Ubuntu nel gruppo di risorse nuove.

## Interfaccia della riga di comando di Azure

Prima di iniziare, accertarsi di avere la versione corretta di Azure CLI installata, di avere effettuato l'accesso e di avere impostato la nuova modalità di gestione delle risorse. Per informazioni dettagliate, fare clic [qui](virtual-machines-deploy-rmtemplates-azure-cli.md#getting-ready).

Per prima cosa, creare un nuovo gruppo di risorse. Utilizzare il seguente comando e specificare il nome del gruppo e la posizione del data center di Azure in cui si desidera distribuire.

	azure group create <group name> <location>

Successivamente, utilizzare il seguente comando e specificare il nome del nuovo gruppo di risorse e il nome di una distribuzione di Azure.

	azure group deployment create --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/wordpress-single-vm-ubuntu/azuredeploy.json <group name> <deployment name>

Di seguito è fornito un esempio.

	azure group create wordpress eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/wordpress-single-vm-ubuntu/azuredeploy.json wordpress wpdevtest

Quando si esegue il comando **Crea gruppo di distribuzione Azure**, verrà richiesto di fornire i valori per una serie di parametri. Dopo aver specificato i valori dei parametri, Azure crea e configura le macchine virtuali.

Al termine, si disporrà di un nuovo server WordPress in Ubuntu nel gruppo di risorse nuove.

## Risorse aggiuntive

[Distribuzione e gestione delle macchine virtuali utilizzando i modelli di Gestione risorse di Azure e Azure PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

[Provider di calcolo, rete e archiviazione in Gestione risorse di Microsoft Azure](virtual-machines-azurerm-versus-azuresm.md)

[Panoramica di Gestione risorse di Microsoft Azure](../resource-group-overview.md)

[Distribuzione e gestione delle macchine virtuali utilizzando i modelli di Gestione risorse di Azure e l'interfaccia della riga di comando di Azure](virtual-machines-deploy-rmtemplates-azure-cli.md)

[Macchine virtuali - Documentazione](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Come installare e configurare Azure PowerShell](../install-configure-powershell.md)

<!---HONumber=Oct15_HO4-->