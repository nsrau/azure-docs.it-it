---
title: "Più indirizzi IP per le macchine virtuali di Azure - Modello | Microsoft Docs"
description: "Informazioni sulla modalità di assegnazione di più indirizzi IP a una macchina virtuale usando un modello di Azure Resource Manager."
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 5267ed6a2d9278b79e9fb98c1067eceae819cb6f
ms.lasthandoff: 03/22/2017


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-an-azure-resource-manager-template"></a>Assegnare più indirizzi IP a una macchina virtuale usando un modello di Azure Resource Manager

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Questo articolo spiega come creare una macchina virtuale (VM) tramite il modello di distribuzione Azure Resource Manager usando un modello di Resource Manager. È impossibile assegnare più indirizzi IP pubblici e privati alla stessa NIC quando si distribuisce una macchina virtuale tramite il modello di distribuzione classico. Per altre informazioni sui modelli di distribuzione di Azure, leggere l'articolo [Understand Azure deployment models](../resource-manager-deployment-model.md) (Informazioni sui modelli di distribuzione di Azure).

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="template-description"></a>Descrizione modello

La distribuzione di un modello consente di creare rapidamente e in modo coerente le risorse di Azure con diversi valori di configurazione. Se non si ha familiarità con i modelli di Azure Resource Manager, leggere l'articolo [Resource Manager template walkthrough](../azure-resource-manager/resource-manager-template-walkthrough.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Guida dettagliata sul modello di Resource Manager). In questo articolo viene usato il modello [Deploy a VM with multiple IP addresses](https://azure.microsoft.com/resources/templates/101-vm-multiple-ipconfig) (Distribuire una macchina virtuale con più indirizzi IP).

<a name="resources"></a>La distribuzione del modello consente di creare le risorse seguenti:

|Risorsa|Nome|Descrizione|
|---|---|---|
|Interfaccia di rete|*myNic1*|Le tre configurazioni IP descritte nella sezione scenario di questo articolo vengono create e assegnate a questa NIC.|
|Risorsa indirizzo IP pubblico|ne vengono creati 2: *myPublicIP* e *myPublicIP2*|A queste risorse vengono assegnati indirizzi IP pubblici statici e vengono assegnate le configurazioni IP descritte nello scenario *IPConfig-1* e *IPConfig-2*.|
|VM|*myVM1*|Macchina virtuale Standard DS3.|
|Rete virtuale|*myVNet1*|Rete virtuale con una subnet denominata *mySubnet*.|
|Account di archiviazione|Univoco per la distribuzione|Account di archiviazione.|

<a name="parameters"></a>Quando si distribuisce il modello, è necessario specificare i valori dei parametri seguenti:

|Nome|Descrizione|
|---|---|
|adminUsername|Nome utente amministratore. Il nome utente deve essere conforme ai [requisiti per il nome utente di Azure](../virtual-machines/virtual-machines-windows-faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|adminPassword|Password amministratore. La password deve essere conforme ai [requisiti per la password di Azure](../virtual-machines/virtual-machines-windows-faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
|dnsLabelPrefix|Nome DNS per PublicIPAddressName1. Il nome DNS viene risolto con uno degli indirizzi IP pubblici assegnati alla VM. Il nome deve essere univoco all'interno dell'area di Azure (percorso) in cui è stata creata la VM.|
|dnsLabelPrefix1|Nome DNS per PublicIPAddressName2. Il nome DNS viene risolto con uno degli indirizzi IP pubblici assegnati alla VM. Il nome deve essere univoco all'interno dell'area di Azure (percorso) in cui è stata creata la VM.|
|OSVersion|La versione di Windows/Linux per la VM. Il sistema operativo è un'immagine con patch completa della specifica versione di Windows/Linux selezionata.|
|imagePublisher|Editor di immagini Windows/Linux per la VM selezionata.|
|imageOffer|Immagine Windows/Linux per la VM selezionata.|

Tutte le risorse distribuite dal modello sono configurate con diverse impostazioni predefinite. È possibile visualizzare queste impostazioni tramite uno dei metodi seguenti:

- **Visualizzare il modello su GitHub:**. se si ha familiarità con i modelli, è possibile visualizzare le impostazioni all'interno del [modello](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json). 
- **Visualizzare le impostazioni dopo la distribuzione:** se non si ha familiarità con i modelli, è possibile distribuire il modello usando la procedura in una delle sezioni seguenti e quindi visualizzare le impostazioni dopo la distribuzione.

È possibile usare il portale di Azure, PowerShell o l'interfaccia della riga di comando (CLI) di Azure per distribuire il modello. Tutti i metodi producono lo stesso risultato. Per distribuire il modello, completare la procedura in una delle sezioni seguenti:

## <a name="deploy-using-the-azure-portal"></a>Distribuire tramite il portale di Azure

Per distribuire il modello tramite il portale di Azure, completare la procedura seguente:

1. Modificare il modello se necessario. Il modello consente di distribuire le risorse e le impostazioni elencate nella sezione [risorse](#resources) di questo articolo. Per altre informazioni sui modelli e sulle modalità di creazione, leggere l'articolo [Creazione di modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Distribuire il modello con uno dei metodi seguenti:
    - **Selezionare il modello nel portale:** completare la procedura riportata nell'articolo [Deploy resources from custom template](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template) (Distribuire le risorse da un modello personalizzato). Scegliere il modello preesistente denominato *101-vm-multiple-ipconfig*.
    - **Direttamente:** fare clic sul pulsante seguente per aprire il modello direttamente nel portale:<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-multiple-ipconfig%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

Indipendentemente dal metodo scelto, è necessario fornire i valori dei [parametri](#parameters) elencati in precedenza in questo articolo. Dopo aver distribuito la VM, connettersi alla VM e aggiungere gli indirizzi IP privati al sistema operativo che si è distribuito completando la procedura riportata nella sezione di questo articolo [Add IP addresses to a VM operating system](#os-config) (Aggiungere indirizzi IP al sistema operativo di una VM). Non aggiungere gli indirizzi IP pubblici al sistema operativo.

## <a name="deploy-using-powershell"></a>Distribuire tramite PowerShell

Per distribuire il modello tramite Powershell, completare la procedura seguente:

1. Distribuire il modello completando i passaggi indicati nell'articolo [Deploy a template with PowerShell](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy) (Distribuire un modello con Powershell). L'articolo descrive più opzioni per la distribuzione di un modello. Se si desidera distribuire tramite il `-TemplateUri parameter`, l'URI per questo modello è *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Se si desidera distribuire usando il parametro `-TemplateFile`, copiare il contenuto del [file modello](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) da GitHub in un nuovo file nel computer. Modificare il contenuto del modello se necessario. Il modello consente di distribuire le risorse e le impostazioni elencate nella sezione [risorse](#resources) di questo articolo. Per altre informazioni sui modelli e sulle modalità di creazione, leggere [Creazione di modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

    Indipendentemente dall'opzione scelta per distribuire il modello, è necessario fornire i valori dei parametri elencati nella sezione [parametri](#parameters) di questo articolo. Se si desidera specificare i parametri usando un file dei parametri, copiare il contenuto del [file dei parametri](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json) da GitHub in un nuovo file nel computer. Modificare i valori nel file. Usare come valore per il parametro `-TemplateParameterFile` il file che è stato creato.
    
    Per determinare i valori validi per i parametri di OSVersion, ImagePublisher e imageOffer, completare la procedura riportata nell'articolo [Esplorare e selezionare immagini di macchine virtuali Windows](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md).

    >[!TIP]
    >Se non si è sicuri di avere un dnslabelprefix disponibile, immettere il comando `Test-AzureRmDnsAvailability -DomainNameLabel <name-you-want-to-use> -Location <location>` per verificarlo. Se è disponibile, il comando restituirà `True`.

2. Dopo aver distribuito la VM, connettersi alla VM e aggiungere gli indirizzi IP privati al sistema operativo che si è distribuito completando la procedura riportata nella sezione di questo articolo [Add IP addresses to a VM operating system](#os-config) (Aggiungere indirizzi IP al sistema operativo di una VM). Non aggiungere gli indirizzi IP pubblici al sistema operativo.

## <a name="deploy-using-the-azure-cli"></a>Distribuire tramite l'interfaccia della riga di comando di Azure

Per distribuire il modello usando l'interfaccia della riga di comando di Azure 1.0, completare la procedura seguente:

1. Distribuire il modello completando i passaggi indicati nell'articolo [Deploy a template with the Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy) (Distribuire un modello con l'interfaccia della riga di comando di Azure). L'articolo descrive più opzioni per la distribuzione del modello. Se si desidera distribuire usando il `--template-uri` (-f), l'URI per questo modello è *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Se si desidera distribuire usando il parametro `--template-file` (-f), copiare il contenuto del [file modello](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) da GitHub in un nuovo file nel computer. Modificare il contenuto del modello se necessario. Il modello consente di distribuire le risorse e le impostazioni elencate nella sezione [risorse](#resources) di questo articolo. Per altre informazioni sui modelli e sulle modalità di creazione, leggere [Creazione di modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

    Indipendentemente dall'opzione scelta per distribuire il modello, è necessario fornire i valori dei parametri elencati nella sezione [parametri](#parameters) di questo articolo. Se si desidera specificare i parametri usando un file dei parametri, copiare il contenuto del [file dei parametri](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json) da GitHub in un nuovo file nel computer. Modificare i valori nel file. Usare come valore per il parametro `--parameters-file` (-e) il file che è stato creato.
    
    Per determinare i valori validi per i parametri di OSVersion, ImagePublisher e imageOffer, completare la procedura riportata nell'articolo [Esplorare e selezionare immagini di macchine virtuali Windows](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md).

2. Dopo aver distribuito la VM, connettersi alla VM e aggiungere gli indirizzi IP privati al sistema operativo che si è distribuito completando la procedura riportata nella sezione di questo articolo [Add IP addresses to a VM operating system](#os-config) (Aggiungere indirizzi IP al sistema operativo di una VM). Non aggiungere gli indirizzi IP pubblici al sistema operativo.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

