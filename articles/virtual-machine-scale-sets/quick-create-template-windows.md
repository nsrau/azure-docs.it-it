---
title: 'Avvio rapido: Creare un set di scalabilità di macchine virtuali Windows con un modello di Azure'
description: Informazioni su come creare rapidamente un set di scalabilità di macchine virtuali Windows con un modello di Azure Resource Manager che distribuisce un'app di esempio e configura le regole di scalabilità automatica
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 03/27/2020
ms.author: jushiman
ms.openlocfilehash: 030479a02b33a92c3917ba112d99c9bcef4f7f32
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010444"
---
# <a name="quickstart-create-a-windows-virtual-machine-scale-set-with-an-azure-template"></a>Guida introduttiva: Creare un set di scalabilità di macchine virtuali Windows con un modello di Azure

Un set di scalabilità di macchine virtuali consente di distribuire e gestire un set di macchine virtuali identiche con scalabilità automatica. È possibile ridimensionare manualmente il numero di VM nel set di scalabilità o definire regole di scalabilità automatica in base all'uso delle risorse, ad esempio la CPU, alla richiesta di memoria o al traffico di rete. Un servizio Azure Load Balancer distribuisce quindi il traffico alle istanze di macchina virtuale nel set di scalabilità. In questa guida introduttiva si crea un set di scalabilità di macchine virtuali e si distribuisce un'applicazione di esempio con un modello di Azure Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

No.

## <a name="create-a-scale-set"></a>Creare un set di scalabilità

I modelli di Azure Resource Manager consentono di distribuire gruppi di risorse correlate. In un singolo modello è possibile creare il set di scalabilità di macchine virtuali, installare le applicazioni e configurare le regole di scalabilità automatica. Con l'uso di variabili e parametri, questo modello può essere riutilizzato per creare altri set di scalabilità o aggiornare i set esistenti. È possibile distribuire i modelli tramite il portale di Azure, l'interfaccia della riga di comando di Azure, Azure PowerShell oppure le pipeline di integrazione continua/distribuzione continua (CI/CD).

### <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/201-vmss-windows-webapp-dsc-autoscale/).

:::code language="json" source="~/quickstart-templates/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json" range="1-397" highlight="236-325":::

Queste risorse sono definite nei modelli seguenti:

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets)
- [**Microsoft.Insights/autoscaleSettings**](/azure/templates/microsoft.insights/autoscalesettings)

#### <a name="define-a-scale-set"></a>Definire un set di scalabilità

La parte evidenziata corrisponde alla definizione delle risorse del set di scalabilità. Per creare un set di scalabilità con un modello, definire le risorse appropriate. Le parti principali del tipo di risorsa del set di scalabilità di macchine virtuali sono le seguenti:

| Proprietà                     | Descrizione della proprietà                                  | Valore di esempio del modello                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | Tipo di risorsa di Azure da creare                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | Nome del set di scalabilità                                       | myScaleSet                                |
| posizione                     | Posizione in cui creare il set di scalabilità                     | Stati Uniti orientali                                   |
| sku.name                     | Dimensioni delle VM per ogni istanza del set di scalabilità                  | Standard_A1                               |
| sku.capacity                 | Numero di istanze di macchina virtuale da creare inizialmente           | 2                                         |
| upgradePolicy.mode           | Modalità di aggiornamento dell'istanza di macchina virtuale in caso di modifiche              | Automatico                                 |
| imageReference               | Piattaforma o immagine personalizzata da usare per le istanze delle macchine virtuali | Microsoft Windows Server 2016 Datacenter  |
| osProfile.computerNamePrefix | Prefisso del nome per ogni istanza di macchina virtuale                     | myvmss                                    |
| osProfile.adminUsername      | Nome utente di ogni istanza di macchina virtuale                        | azureuser                                 |
| osProfile.adminPassword      | Password di ogni istanza di macchina virtuale                        | P@ssw0rd!                                 |

Per personalizzare un modello di set di scalabilità, è possibile modificare le dimensioni o la capacità iniziale delle macchine virtuali. In alternativa, è possibile usare una piattaforma diversa o un'immagine personalizzata.

#### <a name="add-a-sample-application"></a>Aggiungere un'applicazione di esempio

Per testare il set di scalabilità, installare un'applicazione Web di base. Quando si distribuisce un set di scalabilità, le estensioni di VM possono fornire attività di configurazione e automazione post-distribuzione, ad esempio l'installazione di un'app. Gli script possono essere scaricati dall'archiviazione di Azure o da GitHub oppure possono essere forniti al portale di Azure durante il runtime dell'estensione. Per applicare un'estensione al set di scalabilità, si aggiunge la sezione *extensionProfile* all'esempio di risorsa precedente. Il profilo di estensione definisce in genere le proprietà seguenti:

- Tipo di estensione
- Server di pubblicazione dell'estensione
- Versione dell'estensione
- Percorso degli script di configurazione o installazione
- Comandi da eseguire nelle istanze di macchina virtuale

Il modello usa l'estensione DSC di PowerShell per installare un'app ASP.NET MVC che viene eseguita in IIS.

Viene scaricato uno script di installazione da GitHub, come definito in *url*. L'estensione esegue quindi *InstallIIS* dallo script *IISInstall.ps1*, come definito in *function* e *Script*. L'app ASP.NET viene fornita come pacchetto di distribuzione Web scaricato da GitHub, come definito in *WebDeployPackagePath*:

## <a name="deploy-the-template"></a>Distribuire il modello

Per distribuire il modello, selezionare il pulsante **Distribuisci in Azure**. Questo pulsante apre il portale di Azure, carica il modello completo e richiede alcuni parametri quali il nome del set di scalabilità, il numero di istanze e le credenziali di amministratore.

[![Distribuire il modello in Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

È possibile distribuire un modello di Resource Manager anche con Azure PowerShell:

```azurepowershell-interactive
# Create a resource group
New-AzResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateURI https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

Rispondere ai prompt per l'inserimento del nome del set di scalabilità e delle credenziali di amministratore per le istanze di macchina virtuale. Possono essere necessari 10-15 minuti per la creazione del set di scalabilità e l'applicazione dell'estensione per la configurazione dell'app.

## <a name="test-the-deployment"></a>Test della distribuzione

Per vedere il set di scalabilità in azione, accedere all'applicazione Web di esempio in un Web browser. Ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) come segue:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Immettere l'indirizzo IP pubblico del servizio di bilanciamento del carico in un Web browser nel formato *http:\//publicIpAddress/MyApp*. Il servizio di bilanciamento del carico distribuisce il traffico a una delle istanze di macchina virtuale, come illustrato nell'esempio seguente:

![Esecuzione del sito IIS](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile rimuovere il gruppo di risorse e il set di scalabilità con [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Il parametro `-Force` conferma che si desidera eliminare le risorse senza un prompt aggiuntivo a tale scopo. Il parametro `-AsJob` restituisce il controllo al prompt senza attendere il completamento dell'operazione.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato un set di scalabilità Windows con un modello di Azure ed è stata usata l'estensione DSC di PowerShell per installare un'app ASP.NET di base nelle istanze di macchina virtuale. Per altre informazioni, passare all'esercitazione su come creare e gestire i set di scalabilità di macchine virtuali di Azure.

> [!div class="nextstepaction"]
> [Creare e gestire i set di scalabilità di macchine virtuali di Azure](tutorial-create-and-manage-powershell.md)
