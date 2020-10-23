---
title: 'Gateway VPN di Azure: eliminare un gateway: portale'
description: Eliminare un gateway di rete virtuale usando il portale di Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 10/16/2020
ms.author: cherylmc
ms.topic: how-to
ms.openlocfilehash: 7d9ae31b5701707589d79fd5f3d7eb0802038eb9
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148229"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Eliminare un gateway di rete virtuale usando il portale

> [!div class="op_single_selector"]
> * [Portale di Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (classic)](vpn-gateway-delete-vnet-gateway-classic-powershell.md) (PowerShell (classico))

Questo articolo consente di eliminare un gateway di rete virtuale. Quando si vuole eliminare un gateway per la configurazione di un gateway VPN, è possibile adottare un paio di approcci diversi.

* Se si vuole eliminare tutto e ricominciare da capo, come nel caso di un ambiente di testing, è possibile eliminare il gruppo di risorse. Quando si elimina un gruppo di risorse, vengono eliminate tutte le risorse all'interno del gruppo. Questo metodo è consigliato solo se non si vuole mantenere alcuna risorsa del gruppo di risorse. Con questo approccio non è possibile eliminare in modo selettivo solo alcune risorse.

* Se si desidera mantenere alcune delle risorse nel gruppo di risorse, eliminare un gateway di rete virtuale diventa leggermente più complicato. Per poter eliminare il gateway di rete virtuale prima è necessario eliminare tutte le risorse che dipendono dal gateway. I passaggi variano a seconda del tipo di connessioni che sono state create e delle risorse dipendenti per ogni connessione.

> [!IMPORTANT]
> I passaggi di questo articolo sono applicabili al modello di distribuzione Resource Manager. Per eliminare un gateway VPN distribuito usando il modello di distribuzione classica, seguire la procedura descritta nell'articolo [eliminare un gateway: classico](vpn-gateway-delete-vnet-gateway-classic-powershell.md) .

## <a name="delete-a-vpn-gateway"></a>Eliminare un gateway VPN

Per eliminare un gateway di rete virtuale, è necessario innanzitutto eliminare ogni risorsa relativa al gateway di rete virtuale. Le risorse devono essere eliminate in un determinato ordine a causa delle dipendenze.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

A questo punto, viene eliminato il gateway di rete virtuale.

### <a name="to-delete-additional-resources"></a>Per eliminare risorse aggiuntive

La procedura seguente consente di eliminare tutte le risorse che non vengono più usate.

#### <a name="to-delete-the-local-network-gateway"></a>Per eliminare il gateway di rete locale

1. In **tutte le risorse**, individuare i gateway di rete locale associati a ogni connessione.
1. Nel pannello **Informazioni generali** del gateway di rete locale, fare clic su **Elimina**.

#### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Per eliminare la risorsa dell'indirizzo IP pubblico del gateway

1. In **Tutte le risorse**, individuare la risorsa dell'indirizzo IP pubblico associata al gateway. Se il gateway di rete virtuale è attivo-attivo, compaiono due indirizzi IP pubblici.
1. Nella pagina **Panoramica** dell'indirizzo IP pubblico fare clic su **Elimina** quindi su **Sì** per confermare.

#### <a name="to-delete-the-gateway-subnet"></a>Per eliminare la subnet del gateway

1. In **Tutte le risorse**, individuare la rete virtuale. 
1. Nel pannello **Subnet**, fare clic su **GatewaySubnet** quindi fare clic su **Elimina**. 
1. Fare clic su **Sì** per confermare l'eliminazione della subnet del gateway.

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="deleterg"></a>Eliminare un gateway VPN eliminando il gruppo di risorse

Se non si è interessati a mantenere risorse del gruppo di risorse e si vuole solo ricominciare da capo, è possibile eliminare un intero gruppo di risorse. Questo è un modo rapido per rimuovere tutto. La procedura seguente si applica solo al modello di distribuzione di Azure Resource Manager.

1. In **Tutte le risorse**, individuare il gruppo di risorse e fare clic per aprire il pannello.
1. Fare clic su **Elimina**. Nel pannello Elimina visualizzare le risorse interessate. Assicurarsi che si desidera eliminare tutte le risorse. In caso contrario, usare i passaggi in Eliminare un gateway VPN all'inizio di questo articolo.
1. Per continuare, digitare il nome del gruppo di risorse che si desidera eliminare, quindi fare clic su **Elimina**.
