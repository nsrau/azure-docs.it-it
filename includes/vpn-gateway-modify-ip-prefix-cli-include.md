---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: f222d4a7f4724506112a47eff61ccc48354dd622
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556431"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>Per modificare i prefissi degli indirizzi IP del gateway di rete locale senza connessione gateway

Se non si ha una connessione gateway e si vogliono aggiungere o rimuovere prefissi degli indirizzi IP, usare lo stesso comando usato per creare il gateway di rete locale, [az network local-gateway create](/cli/azure/network/local-gateway). È anche possibile usare questo comando per aggiornare l'indirizzo IP del gateway per il dispositivo VPN. Per sovrascrivere le impostazioni correnti, usare il nome esistente del gateway di rete locale. Se si usa un nome diverso, creare un nuovo gateway di rete locale, invece di sovrascrivere il valore esistente.

Ogni volta che si apporta una modifica, deve essere specificato l'intero elenco di prefissi, non solo quelli che si vuole modificare. Specificare solo i prefissi da mantenere. In questo caso, 10.0.0.0/24 e 20.0.0.0/24.

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>Per modificare i prefissi degli indirizzi IP del gateway di rete locale con connessione gateway esistente

Se si ha una connessione gateway e si vogliono aggiungere o rimuovere prefissi degli indirizzi IP, è possibile aggiornare i prefissi usando il comando [az network local-gateway update](/cli/azure/network/local-gateway). Questo comporterà periodi di inattività per la connessione VPN. Quando si modificano i prefissi degli indirizzi IP, non è necessario eliminare il gateway VPN.

Ogni volta che si apporta una modifica, deve essere specificato l'intero elenco di prefissi, non solo quelli che si vuole modificare. In questo esempio 10.0.0.0/24 e 20.0.0.0/24 sono già presenti. Vengono aggiunti i prefissi 30.0.0.0/24 e 40.0.0.0/24 e vengono specificati tutti e 4 i prefissi durante l'aggiornamento.

```azurecli
az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 --name VNet1toSite2 -g TestRG1
```