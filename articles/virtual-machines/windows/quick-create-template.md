---
title: 'Avvio rapido: Usare un modello di Resource Manager per creare una VM Windows'
description: Questo argomento di avvio rapido illustra come usare un modello di Resource Manager per creare una macchina virtuale Windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: 6ef929a2934d8480ce6d1eca8bb7ba3b70580110
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84551989"
---
# <a name="quickstart-create-a-windows-virtual-machine-using-a-resource-manager-template"></a>Avvio rapido: Creare una macchina virtuale Windows con un modello di Resource Manager

Questo argomento di avvio rapido illustra come usare un modello di Resource Manager per distribuire una macchina virtuale (VM) Windows in Azure. 

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

No.

## <a name="create-a-windows-virtual-machine"></a>Creare una macchina virtuale Windows

### <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

:::code language="json" source="~/quickstart-templates/101-vm-simple-windows/azuredeploy.json" range="1-225" highlight="67-224":::


Nel modello sono definite diverse risorse:

- [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/Microsoft.Network/virtualNetworks/subnets): per creare una subnet.
- [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): per creare un account di archiviazione.
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/Microsoft.Network/publicIPAddresses): per creare un indirizzo IP pubblico.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/Microsoft.Network/networkSecurityGroups): per creare un gruppo di sicurezza di rete.
- [**Microsoft.Network/virtualNetworks**](/azure/templates/Microsoft.Network/virtualNetworks): per creare una rete virtuale.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/Microsoft.Network/networkInterfaces): per creare una scheda di interfaccia di rete.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/Microsoft.Compute/virtualMachines): per creare una macchina virtuale.



### <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Il modello crea un insieme di credenziali delle chiavi e un segreto.

    [![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-windows%2fazuredeploy.json)

1. Selezionare o immettere i valori seguenti. Usare i valori predefiniti, se disponibili.

    - **Sottoscrizione**: selezionare una sottoscrizione di Azure.
    - **Gruppo di risorse**: selezionare un gruppo di risorse esistente nell'elenco a discesa oppure selezionar **Crea nuovo**, immettere un nome univoco per il gruppo di risorse e quindi fare clic su **OK**.
    - **Località**: selezionare una località.  Ad esempio **Stati Uniti centrali**.
    - **Nome utente amministratore**: specificare un nome utente, ad esempio *azureuser*.
    - **Password amministratore**: specificare una password da usare per l'account amministratore. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](faq.md#what-are-the-password-requirements-when-creating-a-vm).
    - **Prefisso etichetta DNS**: immettere un identificatore univoco da usare come parte dell'etichetta DNS.
    - **Versione del sistema operativo Windows**: selezionare la versione di Windows da eseguire nella macchina virtuale.
    - **Dimensioni della macchina virtuale**: selezionare le [dimensioni ](sizes.md) da usare per la macchina virtuale.
    - **Località**: il valore predefinito è la stessa località del gruppo di risorse, se esiste già.
1. Selezionare **Rivedi e crea**. Al termine della convalida, selezionare **Crea** per creare e distribuire la VM.


Per distribuire il modello, si usa il portale di Azure. Oltre al portale di Azure, è anche possibile usare Azure PowerShell, l'interfaccia della riga di comando di Azure e l'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

È possibile usare il portale di Azure per verificare la macchina virtuale e altre risorse create. Al termine della distribuzione, selezionare **Vai al gruppo di risorse** per visualizzare la macchina virtuale e altre risorse.


## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse per eliminare la VM e tutte le risorse contenute al suo interno. 

1. Selezionare **Gruppo di risorse**.
1. Nella pagina del gruppo di risorse selezionare **Elimina**.
1. Quando richiesto, digitare il nome del gruppo di risorse e quindi selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata distribuita una semplice macchina virtuale usando un modello di Resource Manager. Per altre informazioni sulle macchine virtuali di Azure, passare all'esercitazione per le VM di Linux.


> [!div class="nextstepaction"]
> [Esercitazioni per le macchine virtuali di Windows in Azure](./tutorial-manage-vm.md)
