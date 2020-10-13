---
title: File di inclusione
description: File di inclusione
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 07/30/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 59699d493880034ad1d26a56c63a9ed8401ef371
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87507392"
---
| Risorsa | Limite |
| --- | --- |
| Velocità effettiva dei dati |30 Gbps<sup>1</sup> |
|Regole|10.000. Tutti i tipi di regole combinati.|
|Numero massimo di regole DNAT|298 per un singolo indirizzo IP pubblico.<br>Eventuali indirizzi IP pubblici aggiuntivi contribuiscono al numero di porte SNAT disponibili, ma riducono il numero di regole DNAT disponibili. Ad esempio, due indirizzi IP pubblici consentono 297 regole DNAT. Se il protocollo di una regola è configurato sia per TCP che per UDP, viene conteggiato come due regole.|
|Dimensioni minime di AzureFirewallSubnet |/26|
|Intervallo di porte nelle regole di rete e delle applicazioni|1 - 65535|
|Indirizzi IP pubblici|Massimo 250. Nelle regole DNAT è possibile usare tutti gli indirizzi IP pubblici, che contribuiscono alle porte SNAT disponibili.|
|Indirizzi IP in gruppi IP|Massimo 100 gruppi IP per firewall.<br>Massimo 5000 singoli indirizzi IP o prefissi IP per ogni gruppo IP.
|Tabella di route|Per impostazione predefinita, AzureFirewallSubnet prevede una route 0.0.0.0/0 con il valore NextHopType impostato su **Internet**.<br><br>Connettività diretta al Firewall di Azure. Se AzureFirewallSubnet apprende una route predefinita alla rete locale tramite BGP è necessario sostituirla con una route UDR 0.0.0.0/0 con il valore **NextHopType** impostato su **Internet** per mantenere connettività diretta a Internet. Per impostazione predefinita, Firewall di Azure non supporta il tunneling forzato a una rete locale.<br><br>Tuttavia, se la configurazione richiede il tunneling forzato a una rete locale, Microsoft effettuerà assistenza caso per caso. Contattare il supporto tecnico per poter esaminare il caso. Se il caso viene accettato, la sottoscrizione verrà inserita nell'elenco elementi consentiti e la connettività Internet del firewall verrà mantenuta.|

<sup>1</sup> Se è necessario aumentare questi limiti, contattare il supporto di Azure.
