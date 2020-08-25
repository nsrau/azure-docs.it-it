---
title: 'Visualizzare le route valide di un hub virtuale: rete WAN virtuale di Azure | Microsoft Docs'
description: Come visualizzare le route valide per un hub virtuale in una rete WAN virtuale di Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 0f5481531d23eeb579dcabe80e028ed7b482b09f
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2020
ms.locfileid: "88762267"
---
# <a name="view-virtual-hub-effective-routes"></a>Visualizzare le route effettive dell'hub virtuale

È possibile visualizzare tutte le route dell'hub WAN virtuale nel portale di Azure. Questo articolo illustra la procedura per visualizzare le route valide. Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).

## <a name="select-connections-or-route-tables"></a><a name="routing"></a>Selezionare le connessioni o le tabelle di route

1. Passare all'hub virtuale e quindi selezionare **routing**. Nella pagina routing selezionare **Route effettive**.
1. Dall'elenco a discesa è possibile selezionare **tabella di route**. Se non viene visualizzata l'opzione tabella di route, significa che non è stata configurata una tabella di route personalizzata o predefinita in questo hub virtuale.

## <a name="view-output"></a><a name="output"></a>Visualizzare l'output

L'output della pagina mostra i campi seguenti:

* **Prefisso**: prefisso dell'indirizzo noto all'entità corrente (learnt dal router dell'hub virtuale)
* **Tipo di hop successivo**: può essere una connessione di rete virtuale, VPN_S2S_Gateway, ExpressRouteGateway, Hub remoto o firewall di Azure.
* **Hop successivo**: questo è il collegamento all'ID risorsa dell'hop successivo oppure Mostra semplicemente il collegamento che implica l'hub corrente.
* **Origin**: ID risorsa dell'origine di routing.
* **Come percorso**: l'attributo BGP come percorso (sistema autonomo) elenca tutti i numeri As che devono essere attraversati per raggiungere la posizione in cui il prefisso a cui è collegato il percorso è annunciato da.

### <a name="example"></a><a name="example"></a>Esempio

I valori nella tabella di esempio seguente implicano che la connessione o la tabella di route dell'hub virtuale abbia appreso il percorso di 10.2.0.0/24 (un prefisso di ramo). Ha appreso la route a causa del **tipo di hop successivo della vpn** VPN_S2S_Gateway con l'ID di risorsa del gateway VPN **hop successivo** . **Origine route** fa riferimento all'ID risorsa della tabella/connessione del gateway o della route VPN di origine. **As Path** indica il percorso As per il ramo.

Utilizzare la barra di scorrimento nella parte inferiore della tabella per visualizzare il percorso "AS".

| **Prefix** |  **Tipo hop successivo** | **Hop successivo** |  **Origine route** |**Percorso AS** |
| ---        | ---                | ---          | ---               | ---         |
| 10.2.0.0/24| VPN_S2S_Gateway |/Subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw|/Subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw| 20000|

**Considerazioni:**

* Se viene visualizzato 0.0.0.0/0 nell'output **Get effective Routes** , significa che la route esiste in una delle tabelle di route. Tuttavia, se la route è stata configurata per Internet, per la connessione è necessario un flag aggiuntivo **"enableInternetSecurity": true** . La route valida nella scheda di interfaccia di rete della macchina virtuale non visualizzerà la route se il flag "enableInternetSecurity" nella connessione è "false".

* Il campo della **route predefinita propagate** viene visualizzato nel portale WAN virtuale di Azure quando si modifica una connessione di rete virtuale, una connessione VPN o una connessione ExpressRoute. Questo campo indica il flag **enableInternetSecurity** , che è sempre per impostazione predefinita "false" per le connessioni EXPRESSROUTE e VPN, ma "true" per le connessioni di rete virtuale.

* Quando si visualizzano route valide in una scheda di interfaccia di rete della macchina virtuale, se viene visualizzato l'hop successivo come "gateway di rete virtuale", che implica il router dell'hub virtuale quando la macchina virtuale si trova in una connessione a un hub WAN virtuale.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).
* Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).
