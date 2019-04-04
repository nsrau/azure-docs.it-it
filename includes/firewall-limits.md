---
title: File di inclusione
description: File di inclusione
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 3/25/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 0467359cd9d6a067e519a62532f00459bc5f68cb
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58890986"
---
| Risorsa | Limite predefinito |
| --- | --- |
| Velocità effettiva dei dati |30 Gbps<sup>1</sup> |
|Regole|10.000, tutte le regole di combinare tipi.|
|Dimensioni minime di AzureFirewallSubnet |/26|
|Intervallo di porte nelle regole di rete e delle applicazioni|0-64.000 Sono in corso interventi per ovviare a questa limitazione.|
|Tabella di route|Per impostazione predefinita, AzureFirewallSubnet dispone di una route 0.0.0.0/0 con il valore NextHopType impostata **Internet**.<br><br>Se si abilita il tunneling forzato in locale tramite Gateway VPN o ExpressRoute, si potrebbe essere necessario configurare una route definita dall'utente di 0.0.0.0/0 (UDR) con il valore impostato NextHopType come Internet e associarla con i AzureFirewallSubnet in modo esplicito. Sostituisce un gateway predefinito potenziali annuncio BGP alla rete locale. Se l'organizzazione richiede che il tunneling forzato per il Firewall di Azure indirizzare il traffico del gateway predefinito tramite la rete locale, contattare il supporto tecnico. È possibile elenco elementi consentiti viene mantenuta la sottoscrizione per assicurarsi che il firewall necessario la connettività Internet.|

<sup>1</sup>se è necessario aumentare questi limiti, contattare il supporto tecnico di Azure.
