---
title: 'Avvio rapido: Indirizzare il traffico Web con il modello di Resource Manager'
titleSuffix: Azure Application Gateway
description: Questo argomento di avvio rapido illustra come usare un modello di Resource Manager per creare un gateway applicazione di Azure che indirizza il traffico Web alle macchine virtuali in un pool back-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 08/27/2020
ms.author: victorh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: 3ea5c891a0e3709c1ce469be2e9101a0825acfcb
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093872"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---arm-template"></a>Avvio rapido: Indirizzare il traffico Web con un gateway applicazione Azure - Modello di Resource Manager

In questo argomento di avvio rapido si usa un modello di Azure Resource Manager per creare un gateway applicazione di Azure. Successivamente, verrà testato il gateway applicazione per verificare che funzioni correttamente.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

È anche possibile completare questa guida di avvio rapido usando il [portale di Azure](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md) o l'[interfaccia della riga di comando di Azure](quick-create-cli.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Rivedere il modello

Per semplicità, questo modello crea una configurazione semplice con un indirizzo IP front-end pubblico, un listener di base per ospitare un singolo sito nel gateway applicazione, una regola di gestione delle richieste di base e due macchine virtuali nel pool back-end.

Il modello usato in questo argomento di avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/ag-docs-qs/)

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json":::

Nel modello sono definite più risorse di Azure:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses): una per il gateway applicazione e due per le macchine virtuali.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): due macchine virtuali
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): due per le macchine virtuali
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions): per configurare IIS e le pagine Web

## <a name="deploy-the-template"></a>Distribuire il modello

Distribuire il modello di Resource Manager in Azure:

1. Selezionare **Distribuisci in Azure** per accedere ad Azure e aprire il modello. Il modello crea un gateway applicazione, l'infrastruttura di rete e due macchine virtuali nel pool back-end che esegue IIS.

   [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

2. Selezionare o creare il gruppo di risorse, digitare il nome utente e la password dell'amministratore delle macchine virtuali.
3. Selezionare **Rivedi e crea** e quindi **Crea**.

   Il completamento della distribuzione può richiedere più di 20 minuti.

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

Nonostante l'installazione di IIS non sia necessaria per creare il gateway applicazione, è stata eseguita per verificare se il gateway applicazione è stato creato correttamente in Azure. Usare IIS per testare il gateway applicazione:

1. Trovare l'indirizzo IP pubblico del gateway applicazione nella pagina **Panoramica** corrispondente. ![Registrare l'indirizzo IP pubblico del gateway applicazione](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) In alternativa, è possibile selezionare **Tutte le risorse**, immettere *myAGPublicIPAddress* nella casella di ricerca e quindi selezionare la voce corrispondente nei risultati della ricerca. Azure mostra l'indirizzo IP pubblico nella pagina **Panoramica**.
2. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser per passare a tale indirizzo.
3. Controllare la risposta. Una risposta valida verifica che il gateway applicazione sia stato creato correttamente e possa connettersi al back-end.

   ![Testare il gateway applicazione](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Aggiornare più volte il browser per visualizzare le connessioni sia a myVM1 che a myVM2.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con il gateway applicazione non sono più necessarie, eliminare il gruppo di risorse. Oltre al gateway applicazione vengono rimosse tutte le risorse correlate.

Per eliminare il gruppo di risorse, chiamare il cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire il traffico Web con un gateway applicazione mediante l'interfaccia della riga di comando di Azure](./tutorial-manage-web-traffic-cli.md)
