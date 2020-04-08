---
title: File di inclusione
description: File di inclusione
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/07/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: fb95301d4faa958a677c42bc8092ac52a7c95c8b
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80813843"
---
| Risorsa | Limite |
| --- | --- |
| Velocità effettiva dei datiData throughput |30 Gbps<sup>1</sup> |
|Regole|10,000. Tutti i tipi di regole combinati.|
|Regole DNAT massime|298<br>Se il protocollo di una regola è configurato sia per TCP che per UDP, viene conteggiato come due regole.|
|Dimensioni minime di AzureFirewallSubnet |/26|
|Intervallo di porte nelle regole di rete e delle applicazioni|1 - 65535|
|Indirizzi IP pubblici|100 massimo (attualmente, le porte SNAT vengono aggiunte solo per i primi cinque indirizzi IP pubblici).|
|Indirizzi IP dei gruppi IP|50 gruppi IP o meno: massimo 5000 indirizzi IP individuali per istanza del firewall.<br>51 - 100 gruppi IP: 500 indirizzi IP individuali per ogni istanza del firewall.<br><br>Per altre informazioni, vedere [Gruppi IP (anteprima)See IP Groups (preview) in Azure Firewall](../articles/firewall/ip-groups.md#ip-address-limits)
|Tabella di route|Per impostazione predefinita, AzureFirewallSubnet ha una route 0.0.0.0/0 con il valore NextHopType impostato su **Internet**.<br><br>Connettività diretta al Firewall di Azure. Se AzureFirewallSubnet apprende una route predefinita per la rete locale tramite BGP, è necessario eseguirla con un UDR 0.0.0.0/0 con il valore **NextHopType** impostato come **Internet** per mantenere la connettività Internet diretta. Per impostazione predefinita, Firewall di Azure non supporta il tunneling forzato a una rete locale.<br><br>Tuttavia, se la configurazione richiede il tunneling forzato a una rete locale, Microsoft effettuerà assistenza caso per caso. Contattare il supporto tecnico per poter esaminare il caso. Se il caso viene accettato, la sottoscrizione verrà inserita nell'elenco elementi consentiti e la connettività Internet del firewall verrà mantenuta.|

<sup>1 : il</sup> nome del Se è necessario aumentare questi limiti, contattare il supporto di Azure.If you need to increase these limits, contact Azure Support.
