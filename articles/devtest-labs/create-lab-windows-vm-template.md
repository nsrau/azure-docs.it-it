---
title: Creare un lab con Azure DevTest Labs e un modello di Azure Resource Manager
description: In questo argomento di avvio rapido verrà creato un lab in Azure DevTest Labs usando un modello di Azure Resource Manager. Un amministratore del lab configura un lab in un'organizzazione, crea le VM al suo interno e configura i criteri.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/26/2020
ms.openlocfilehash: 8688b8265a8ca00a36a569ff4e067c9f36834c6d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537548"
---
# <a name="quickstart-set-up-a-lab-by-using-azure-devtest-labs-arm-template"></a>Avvio rapido: Configurare un lab con un modello di Resource Manager di Azure DevTest Labs
In questo argomento di avvio rapido verrà creato un lab con una macchina virtuale Windows Server 2019 Datacenter usando un modello di Azure Resource Manager. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si eseguono le operazioni seguenti:

> [!div class="checklist"]
> * Rivedere il modello 
> * Distribuire il modello
> * Verificare il modello
> * Risorse di pulizia

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-dtl-create-lab-windows-vm/).

:::code language="json" source="~/quickstart-templates/101-dtl-create-lab-windows-vm/azuredeploy.json" range="1-97" highlight="51-85":::

Nel modello sono definite le risorse seguenti:

- [**Microsoft.DevTestLab/labs**](/azure/templates/microsoft.devtestlab/labs)
- [**Microsoft.DevTestLab labs/virtualnetworks**](/azure/templates/microsoft.devtestlab/labs/virtualnetworks)
- [**Microsoft.DevTestLab labs/virtualmachines**](/azure/templates/microsoft.devtestlab/labs/virtualmachines)

Per altri esempi di modello, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab).

## <a name="deploy-the-template"></a>Distribuire il modello
Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente. 

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

1. Creare un **Nuovo gruppo di risorse** per semplificare la pulizia delle risorse in un secondo momento.
1. Selezionare una **Località** per il gruppo di risorse. 
1. Immettere un **Nome** per il lab. 
1. Immettere un **Nome** per la macchina virtuale. 
1. In **Nome utente** specificare gli utenti che possono accedere alla macchina virtuale. 
1. Immettere la **password** per l'utente. 
1. Selezionare **Accetto le condizioni riportate sopra**. 
1. Selezionare quindi **Acquista**.

    :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="Pagina Distribuisci modello":::

## <a name="validate-the-deployment"></a>Convalidare la distribuzione
1. Selezionare **Notifiche** nella parte superiore per visualizzare lo stato della distribuzione e fare clic sul collegamento **Distribuzione in corso**.

    :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="Notifica della distribuzione":::
2. Nella pagina **Distribuzione - Panoramica** attendere il completamento della distribuzione. Questa operazione, in particolare la creazione di una macchina virtuale, richiede tempo. Selezionare quindi **Vai al gruppo di risorse** o il **nome del gruppo di risorse** come illustrato nell'immagine seguente: 

    :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="Passare al gruppo di risorse":::
3. Nella pagina **Gruppo di risorse** viene visualizzato l'elenco delle risorse nel gruppo di risorse. Verificare che il lab di tipo `DevTest Lab` sia incluso nelle risorse visualizzate. Verranno inoltre visualizzate le risorse dipendenti, ad esempio la rete virtuale e la macchina virtuale nel gruppo di risorse. 

    :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-home-page.png" alt-text="Home page del gruppo di risorse":::
4. Selezionare il lab dall'elenco di risorse per visualizzare la home page per il lab. Verificare che la macchina virtuale Windows Server 2019 Datacenter sia presente nell'elenco **Macchine virtuali personali**. Nell'immagine seguente la sezione **Informazioni di base** è ridotta a icona. 

    :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="Home page del lab":::

    > [!IMPORTANT] 
    > Lasciare aperta questa pagina e seguire le istruzioni nella sezione successiva per pulire le risorse al fine di evitare l'addebito di costi per l'esecuzione del lab e della macchina virtuale in Azure. Se si vuole procedere con l'esercitazione successiva per testare l'accesso alla macchina virtuale nel lab, pulire le risorse dopo aver completato l'esercitazione. 

## <a name="clean-up-resources"></a>Pulire le risorse

1. Prima di tutto, è necessario eliminare il lab per poter eliminare il gruppo di risorse. Non sarà possibile eliminare il gruppo di risorse se contiene un lab al suo interno. Per eliminare il lab, selezionare **Elimina** sulla barra degli strumenti. 

    :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="Pulsante Elimina il lab":::
 2. Nella pagina di conferma digitare il **nome del lab** e selezionare **Elimina**. 
 3. Attendere fino a quando il lab non è stato eliminato. Selezionare l'icona a forma di **Campana** per visualizzare la notifica dell'operazione di eliminazione. L'esecuzione di questo processo richiede tempo. Confermare l'eliminazione del lab, quindi selezionare il **gruppo di risorse** nel menu di navigazione. 
 
    :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="Conferma dell'eliminazione della macchina virtuale nelle notifiche":::
 1. Nella pagina **Gruppo di risorse** selezionare **Elimina gruppo di risorse** dalla barra degli strumenti. Nella pagina di conferma digitare il **nome del gruppo di risorse** e selezionare **Elimina**. Controllare le notifiche per verificare che il gruppo di risorse sia stato eliminato.
 
    :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="Pulsante Elimina gruppo di risorse":::

## <a name="next-steps"></a>Passaggi successivi
In questo argomento di avvio rapido è stato creato un lab con una VM. Per informazioni su come accedere al lab, passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Esercitazione: Accedere al lab](tutorial-use-custom-lab.md)
