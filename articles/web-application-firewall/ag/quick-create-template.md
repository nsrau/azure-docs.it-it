---
title: "Avvio rapido: Creare un'istanza di WAF di Azure v2 nel gateway applicazione - Modello di Azure Resource Manager"
titleSuffix: Azure Application Gateway
description: Informazioni su come usare un modello di Azure Resource Manager per creare un'istanza di Web Application Firewall v2 nel gateway applicazione di Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: quickstart
ms.date: 04/02/2020
ms.author: victorh
ms.openlocfilehash: 081bab0cd930d90ca0d359461e4a41b15ba4911b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075505"
---
# <a name="quickstart-create-an-azure-waf-v2-on-application-gateway-using-an-arm-template"></a>Avvio rapido: Creare un'istanza di WAF di Azure v2 nel gateway applicazione con un modello di Azure Resource Manager

In questo argomento di avvio rapido si userà un modello di Azure Resource Manager per creare un'istanza di Web Application Firewall v2 do Azure nel gateway applicazione.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Rivedere il modello

Questo modello crea una semplice istanza di Web Application Firewall v2 nel gateway applicazione di Azure. La procedura include un indirizzo IP front-end pubblico, le impostazioni HTTP, una regola con un listener di base sulla porta 80 e un pool back-end. Viene creato un criterio WAF con una regola personalizzata per bloccare il traffico verso il pool back-end in base a un tipo di corrispondenza degli indirizzi IP.

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/ag-docs-wafv2/).

:::code language="json" source="~/quickstart-templates/ag-docs-wafv2/azuredeploy.json" range="001-404" highlight="314-358":::

Nel modello sono definite più risorse di Azure:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies**](/azure/templates/microsoft.network/ApplicationGatewayWebApplicationFirewallPolicies)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses): una per il gateway applicazione e due per le macchine virtuali.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): due macchine virtuali
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): due per le macchine virtuali
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions): per configurare IIS e le pagine Web

## <a name="deploy-the-template"></a>Distribuire il modello

Distribuire il modello di Resource Manager in Azure:

1. Selezionare **Distribuisci in Azure** per accedere ad Azure e aprire il modello. Il modello crea un gateway applicazione, l'infrastruttura di rete e due macchine virtuali nel pool back-end che esegue IIS.

   [![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

2. Selezionare o creare un gruppo di risorse.
3. Selezionare **Accetto le condizioni riportate sopra** e quindi **Acquista**. La distribuzione può richiedere almeno 10 minuti.

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

Nonostante l'installazione di IIS non sia necessaria per creare il gateway applicazione, è stata eseguita nei server back-end per verificare se Azure ha creato un'istanza di WAF v2 nel gateway applicazione.

Usare IIS per testare il gateway applicazione:

1. Trovare l'indirizzo IP pubblico del gateway applicazione nella pagina **Panoramica** corrispondente. ![Registrare l'indirizzo IP pubblico del gateway applicazione](../../application-gateway/media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) In alternativa, è possibile selezionare **Tutte le risorse**, immettere *myAGPublicIPAddress* nella casella di ricerca e quindi selezionare la voce corrispondente nei risultati della ricerca. Azure mostra l'indirizzo IP pubblico nella pagina **Panoramica**.
2. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser per passare a tale indirizzo.
3. Controllare la risposta. Una risposta **403 accesso negato** verifica che l'istanza di WAF è stata creata correttamente e blocca le connessioni al pool back-end.
4. Cambiare la regola personalizzata in **Consenti traffico**.
  Eseguire lo script di Azure PowerShell seguente, sostituendo il nome del gruppo di risorse:
   ```azurepowershell
   $rg = "<your resource group name>"
   $AppGW = Get-AzApplicationGateway -Name myAppGateway -ResourceGroupName $rg
   $pol = Get-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg
   $pol[0].customrules[0].action = "allow"
   $rule = $pol.CustomRules
   Set-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg -CustomRule $rule
   $AppGW.FirewallPolicy = $pol
   Set-AzApplicationGateway -ApplicationGateway $AppGW
   ```

   Aggiornare più volte il browser per visualizzare le connessioni sia a myVM1 che a myVM2.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con il gateway applicazione non sono più necessarie, eliminare il gruppo di risorse. Oltre al gateway applicazione vengono rimosse tutte le risorse correlate.

Per eliminare il gruppo di risorse, chiamare il cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Creare un gateway applicazione con un'istanza di Web Application Firewall tramite il portale di Azure](application-gateway-web-application-firewall-portal.md)