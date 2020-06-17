---
title: 'Avvio rapido: Usare un modello di Resource Manager per creare una VM Ubuntu Linux'
description: Questo argomento di avvio rapido illustra come usare un modello di Resource Manager per creare una macchina virtuale Linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: 0e9ce74d62bb45c84f8bca2d71579b05c0ba656a
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485711"
---
# <a name="quickstart-create-an-ubuntu-linux-virtual-machine-using-a-resource-manager-template"></a>Avvio rapido: Creare una macchina virtuale Ubuntu Linux con un modello di Resource Manager

Questo argomento di avvio rapido illustra come usare un modello di Resource Manager per distribuire una macchina virtuale (VM) Ubuntu Linux in Azure. 

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/).

:::code language="json" source="~/quickstart-templates/101-vm-simple-linux/azuredeploy.json" range="1-261" highlight="110-260":::


Nel modello sono definite diverse risorse:

- [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/Microsoft.Network/virtualNetworks/subnets): per creare una subnet.
- [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): per creare un account di archiviazione.
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/Microsoft.Network/publicIPAddresses): per creare un indirizzo IP pubblico.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/Microsoft.Network/networkSecurityGroups): per creare un gruppo di sicurezza di rete.
- [**Microsoft.Network/virtualNetworks**](/azure/templates/Microsoft.Network/virtualNetworks): per creare una rete virtuale.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/Microsoft.Network/networkInterfaces): per creare una scheda di interfaccia di rete.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/Microsoft.Compute/virtualMachines): per creare una macchina virtuale.



## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Il modello crea un insieme di credenziali delle chiavi e un segreto.

    [![Distribuzione in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-linux%2fazuredeploy.json)

1. Selezionare o immettere i valori seguenti. Usare i valori predefiniti, se disponibili.

    - **Sottoscrizione**: selezionare una sottoscrizione di Azure.
    - **Gruppo di risorse**: selezionare un gruppo di risorse esistente nell'elenco a discesa oppure selezionar **Crea nuovo**, immettere un nome univoco per il gruppo di risorse e quindi fare clic su **OK**.
    - **Località**: selezionare una località.  Ad esempio **Stati Uniti centrali**.
    - **Nome utente amministratore**: specificare un nome utente, ad esempio *azureuser*.
    - **Tipo di autenticazione**: È possibile scegliere se usare una chiave SSH o una password.
    - **Password amministratore o chiave** a seconda dell'opzione scelta per il tipo di autenticazione:
        - La **password**, se si sceglie questa opzione, dovrà essere costituita almeno da 12 caratteri e soddisfare i [requisiti di complessità definiti](faq.md#what-are-the-password-requirements-when-creating-a-vm).
        - Se si sceglie **sshPublicKey**, incollare il contenuto della chiave pubblica.
    - **Prefisso etichetta DNS**: immettere un identificatore univoco da usare come parte dell'etichetta DNS.
    - **Versione del sistema operativo Ubuntu**: selezionare la versione di Ubuntu da eseguire nella macchina virtuale.
    - **Località**: il valore predefinito è la stessa località del gruppo di risorse, se esiste già.
    - **Dimensioni della macchina virtuale**: selezionare le [dimensioni ](sizes.md) da usare per la macchina virtuale.
    - **Nome rete virtuale**: il nome da usare per la rete virtuale.
    - **Nome subnet**: il nome della subnet che dovrà essere usata dalla VM.
    - **Nome del gruppo di sicurezza di rete**: il nome per il gruppo di sicurezza di rete.
1. Selezionare **Rivedi e crea**. Al termine della convalida, selezionare **Crea** per creare e distribuire la VM.


Per distribuire il modello, si usa il portale di Azure. Oltre al portale di Azure, è anche possibile usare l'interfaccia della riga di comando di Azure, Azure PowerShell e l'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../../azure-resource-manager/templates/deploy-cli.md).

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
> [Esercitazioni per le macchine virtuali di Linux in Azure](./tutorial-manage-vm.md)