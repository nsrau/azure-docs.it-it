---
title: File di inclusione
description: File di inclusione
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 11/06/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: d479c3087b971aa17cf145e0111890da07381eab
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94386617"
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
|Nomi di dominio completo nelle regole di rete|Per prestazioni ottimali non superare più di 1000 nomi di dominio completo in tutte le regole di rete per singolo firewall.|

<sup>1</sup> Se è necessario aumentare questi limiti, contattare il supporto di Azure.
