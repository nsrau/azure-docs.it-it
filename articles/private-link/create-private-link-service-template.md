---
title: Creare un servizio Collegamento privato in Collegamento privato di Azure
description: In questa guida di avvio rapido si usa un modello di Azure Resource Manager per creare un servizio Collegamento privato.
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: c9ed628501e8fa02b816a1564b91620404dfc379
ms.sourcegitcommit: 1383842d1ea4044e1e90bd3ca8a7dc9f1b439a54
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/16/2020
ms.locfileid: "84817627"
---
# <a name="quickstart-create-a-private-link-service-by-using-an-azure-resource-manager-template"></a>Avvio rapido: Creare un servizio Collegamento privato con un modello di Azure Resource Manager

In questa guida di avvio rapido si usa un modello di Azure Resource Manager per creare un servizio Collegamento privato.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

È anche possibile completare questa guida di avvio rapido usando il [portale di Azure](create-private-link-service-portal.md), [Azure PowerShell](create-private-link-service-powershell.md) o l'[interfaccia della riga di comando di Azure](create-private-link-service-cli.md).

## <a name="prerequisite"></a>Prerequisito

È necessario un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-link-service"></a>Creare un servizio Collegamento privato

Questo modello crea un servizio Collegamento privato.

### <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/).

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json" range="001-432" highlight="263-289":::

Nel modello sono definite più risorse di Azure:

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): è disponibile una sola rete virtuale per ogni macchina virtuale.
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers): bilanciamento del carico che espone le macchine virtuali che ospitano il servizio.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): sono disponibili due interfacce di rete, una per ogni macchina virtuale.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): sono disponibili due macchine virtuali, una che ospita il servizio e una che verifica la connessione all'endpoint privato.
- [**Microsoft.Compute/virtualMachines/extensions**](/azure/templates/Microsoft.Compute/virtualMachines/extensions): estensione che consente di installare un server Web.
- [**Microsoft.Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices): servizio Collegamento privato per esporre il servizio.
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses): sono disponibili due indirizzi IP pubblici, uno per ogni macchina virtuale.
- [**Microsoft.Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints): endpoint privato per accedere al servizio.

### <a name="deploy-the-template"></a>Distribuire il modello

Di seguito è riportata la procedura per distribuire il modello di Azure Resource Manager in Azure:

1. Selezionare **Distribuisci in Azure** per accedere ad Azure e aprire il modello. Il modello crea una macchina virtuale, un'istanza di Load Balancer Standard, un servizio Collegamento privato, un endpoint privato, una rete e una macchina virtuale da convalidare.

   [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Selezionare o creare un gruppo di risorse.
3. Specificare il nome utente e la password dell'amministratore della macchina virtuale.
4. Esaminare le condizioni per l'utilizzo del servizio. Per accettarle, selezionare **Accetto le condizioni riportate sopra** > **Acquista**.

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

> [!NOTE]
> Il modello di Azure Resource Manager genera un nome univoco per la risorsa macchina virtuale myConsumerVm<b>{uniqueid}</b>. Sostituire il valore generato per **{uniqueid}** .

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
2.  Aprire un browser e immettere l'indirizzo dell'endpoint privato: http://10.0.0.5/.
3.  Verrà visualizzata la pagina IIS predefinita.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con il servizio Collegamento privato non sono più necessarie, eliminare il gruppo di risorse. Oltre al servizio Collegamento privato verranno rimosse tutte le risorse correlate.

Per eliminare il gruppo di risorse, chiamare il cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Collegamento privato di Azure](private-link-overview.md).
