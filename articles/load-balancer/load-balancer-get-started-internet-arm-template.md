<properties 
   pageTitle="Creare un servizio di bilanciamento del carico Internet in Gestione risorse con un modello | Microsoft Azure"
   description="Informazioni su come creare un servizio di bilanciamento del carico Internet in Gestione risorse con un modello ARM."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="joaoma" />

# Introduzione alla creazione del servizio di bilanciamento del carico Internet tramite un modello ARM

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione Gestione risorse. Vedere [Informazioni su come creare un servizio di bilanciamento del carico Internet tramite il modello di distribuzione classica](load-balancer-get-started-internet-classic-portal.md)


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## Distribuire il modello ARM tramite clic per la distribuzione

Il modello di esempio disponibile nel repository pubblico usa un file di parametro che contiene i valori predefiniti usati per generare lo scenario descritto in precedenza. Distribuire [questo modello](http://go.microsoft.com/fwlink/?LinkId=544801) tramite clic per la distribuzione, fare clic su **Distribuisci in Azure**, sostituire i valori del parametro predefinito se necessario e seguire le istruzioni nel portale.

## Distribuire il modello ARM tramite PowerShell

Per distribuire il modello ARM scaricato tramite PowerShell, attenersi alla procedura seguente.

1. Se è la prima volta che si utilizza Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](../../articles/powershell-install-configure.md) e seguire le istruzioni fino al termine della procedura per accedere ad Azure e selezionare la sottoscrizione desiderata.

2. Per creare un gruppo di risorse usando il modello, eseguire il cmdlet **New-AzureRmResourceGroupDeployment**.

		New-AzureRmResourceGroupDeployment -Name TestRG -Location uswest `
		    -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
		    -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'	

## Distribuire il modello ARM tramite l'interfaccia della riga di comando di Azure

Per distribuire il modello ARM tramite l'interfaccia della riga di comando di Azure, seguire la procedura di seguito.

1. Se l'interfaccia della riga di comando di Azure non è mai stata usata, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../../articles/xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si selezionano l'account e la sottoscrizione di Azure.
2. Eseguire il comando **azure config mode** per passare alla modalità Gestione risorse, come illustrato di seguito.

		azure config mode arm

	Di seguito è riportato l'output previsto per il comando precedente:

		info:    New mode is arm

3. Dal browser passare a ****https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json**, copiare il contenuto del file JSON e incollarlo in un nuovo file nel computer. Per questo scenario, copiare i valori seguenti in un file denominato **c:\\lb\\azuredeploy.parameters.json**.
4. Eseguire il cmdlet **azure group deployment create** per distribuire il nuovo servizio di bilanciamento del carico usando i file di modello e dei parametri scaricati e modificati in precedenza. Nell'elenco riportato dopo l'output sono indicati i parametri usati.

		azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' -e 'c:\lb\azuredeploy.parameters.json'

## Passaggi successivi

[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0323_2016-->