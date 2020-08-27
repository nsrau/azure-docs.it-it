---
title: Creare un servizio Collegamento privato in Collegamento privato di Azure
description: In questo argomento di avvio rapido si usa un modello di Azure Resource Manager (modello di Resource Manager) per creare un servizio Collegamento privato.
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: dce80d70af7cd711cf852a60b98ad65b6d21117f
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705249"
---
# <a name="quickstart-create-a-private-link-service-by-using-an-arm-template"></a>Avvio rapido: Creare un servizio Collegamento privato con un modello di Resource Manager

In questo argomento di avvio rapido si usa un modello di Azure Resource Manager (modello di Resource Manager) per creare un servizio Collegamento privato.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

È anche possibile completare questa guida di avvio rapido usando il [portale di Azure](create-private-link-service-portal.md), [Azure PowerShell](create-private-link-service-powershell.md) o l'[interfaccia della riga di comando di Azure](create-private-link-service-cli.md).

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

È necessario un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Rivedere il modello

Questo modello crea un servizio Collegamento privato.

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-privatelink-service/).

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json":::

Nel modello sono definite più risorse di Azure:

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): è disponibile una sola rete virtuale per ogni macchina virtuale.
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers): bilanciamento del carico che espone le macchine virtuali che ospitano il servizio.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): sono disponibili due interfacce di rete, una per ogni macchina virtuale.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): sono disponibili due macchine virtuali, una che ospita il servizio e una che verifica la connessione all'endpoint privato.
- [**Microsoft.Compute/virtualMachines/extensions**](/azure/templates/Microsoft.Compute/virtualMachines/extensions): estensione che consente di installare un server Web.
- [**Microsoft.Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices): servizio Collegamento privato per esporre il servizio.
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses): sono disponibili due indirizzi IP pubblici, uno per ogni macchina virtuale.
- [**Microsoft.Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints): endpoint privato per accedere al servizio.

## <a name="deploy-the-template"></a>Distribuire il modello

Ecco come distribuire il modello di Resource Manager in Azure:

1. Selezionare **Distribuisci in Azure** per accedere ad Azure e aprire il modello. Il modello crea una macchina virtuale, un'istanza di Load Balancer Standard, un servizio Collegamento privato, un endpoint privato, una rete e una macchina virtuale da convalidare.

   [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

2. Selezionare o creare un gruppo di risorse.
3. Specificare il nome utente e la password dell'amministratore della macchina virtuale.
4. Esaminare le condizioni per l'utilizzo del servizio. Per accettarle, selezionare **Accetto le condizioni riportate sopra** > **Acquista**.

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

> [!NOTE]
> Il modello di Resource Manager genera un nome univoco per la risorsa macchina virtuale myConsumerVm<b>{uniqueid}</b>. Sostituire il valore generato per **{uniqueid}** .

### <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Connettersi alla macchina virtuale _myConsumerVm{uniqueid}_ da Internet come indicato di seguito:

1.  Nella barra di ricerca del portale immettere _myConsumerVm{uniqueid}_ .

2.  Selezionare **Connetti**. Verrà visualizzata la finestra **Connetti alla macchina virtuale**.

3.  Selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione _.rdp_.

4.  Aprire il file con estensione rdp scaricato.

    a. Quando richiesto, selezionare **Connetti**.

    b. Immettere il nome utente e la password specificati quando è stata creata la macchina virtuale.
    
    > [!NOTE]
    > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un account diverso** per specificare le credenziali immesse al momento della creazione della macchina virtuale.

5.  Selezionare **OK**.

6.  Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

7.  Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.

### <a name="access-the-http-service-privately-from-the-vm"></a>Accedere al servizio http privatamente dalla macchina virtuale

Di seguito viene illustrata la procedura per la connessione al servizio http dalla macchina virtuale tramite l'endpoint privato.

1.  Passare al Desktop remoto di _myConsumerVm{uniqueid}_ .
2.  Aprire un browser e immettere l'indirizzo dell'endpoint privato: `http://10.0.0.5/`.
3.  Verrà visualizzata la pagina IIS predefinita.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con il servizio Collegamento privato non sono più necessarie, eliminare il gruppo di risorse. Oltre al servizio Collegamento privato verranno rimosse tutte le risorse correlate.

Per eliminare il gruppo di risorse, chiamare il cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Collegamento privato di Azure](private-link-overview.md).
