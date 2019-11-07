---
title: Esempio di script di Azure PowerShell - Creare regole personalizzate di WAF
description: Esempio di script di Azure PowerShell - Creare regole personalizzate di web application firewall nel gateway applicazione
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: sample
ms.date: 09/30/2019
ms.author: victorh
ms.openlocfilehash: 950f71c284268a9aa2773eb57213e266622d85bd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509555"
---
# <a name="create-waf-custom-rules-with-azure-powershell"></a>Creare regole personalizzate di WAF con Azure PowerShell

Questo script crea un web application firewall del gateway applicazione che usa regole personalizzate. La regola personalizzata blocca il traffico se l'intestazione della richiesta contiene l'*evilbot* User-Agent.

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-powershell-module"></a>Modulo di Azure PowerShell

Se si sceglie di installare e usare Azure PowerShell in locale, per questo script è necessario il modulo Azure PowerShell versione 2.1.0 o successiva.

1. Per trovare la versione, eseguire `Get-Module -ListAvailable Az`. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps).
2. Eseguire `Connect-AzAccount` per creare una connessione con Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/application-gateway/waf-rules/waf-custom-rules.ps1 "Custom WAF rules")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire il comando seguente per rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name CustomRulesTest
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare la distribuzione. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Crea la configurazione della subnet. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Crea la rete virtuale con le configurazioni di subnet. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Crea l'indirizzo IP pubblico per il gateway applicazione. |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Crea la configurazione che associa una subnet al gateway applicazione. |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Crea la configurazione che assegna un indirizzo IP pubblico al gateway applicazione. |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Assegna una porta da usare per accedere al gateway applicazione. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Crea un pool back-end per un gateway applicazione. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Configura le impostazioni per un pool back-end. |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Crea un listener. |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Crea una regola di routing. |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Specifica il livello e la capacità per un gateway applicazione. |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Creare un gateway applicazione. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. |
|[New-AzApplicationGatewayAutoscaleConfiguration](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|Crea una configurazione di scalabilità automatica per il gateway applicazione.|
|[New-AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|Crea una variabile di corrispondenza per la condizione del firewall.|
|[New-AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|Crea una condizione di corrispondenza per la regola personalizzata.|
|[New-AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|Crea una nuova regola personalizzata per i criteri di firewall del gateway applicazione.|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|Crea criteri di firewall per il gateway applicazione.|
|[New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|Crea una configurazione di WAF per un gateway applicazione.|

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle regole personalizzate di WAF, vedere [Regole personalizzate per il web application firewall](../ag/custom-waf-rules-overview.md)
- Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).
