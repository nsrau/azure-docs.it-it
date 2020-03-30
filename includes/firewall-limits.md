---
title: File di inclusione
description: File di inclusione
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 01/22/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 0df38533afe97f010d1050c3ee2a4a69a54d4cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334790"
---
| Risorsa | Limite |
| --- | --- |
| Velocità effettiva dei datiData throughput |30 Gbps<sup>1</sup> |
|Regole|10,000. Tutti i tipi di regole combinati.|
|Regole DNAT massime|299|
|Dimensioni minime di AzureFirewallSubnet |/26|
|Intervallo di porte nelle regole di rete e delle applicazioni|0-64.000 Sono in corso interventi per ovviare a questa limitazione.|
|Indirizzi IP pubblici|100 massimo (attualmente, le porte SNAT vengono aggiunte solo per i primi cinque indirizzi IP pubblici).|
|Tabella di route|Per impostazione predefinita, AzureFirewallSubnet ha una route 0.0.0.0/0 con il valore NextHopType impostato su **Internet**.<br><br>Connettività diretta al Firewall di Azure. Se AzureFirewallSubnet apprende una route predefinita per la rete locale tramite BGP, è necessario eseguirla con un UDR 0.0.0.0/0 con il valore **NextHopType** impostato come **Internet** per mantenere la connettività Internet diretta. Per impostazione predefinita, Firewall di Azure non supporta il tunneling forzato a una rete locale.<br><br>Tuttavia, se la configurazione richiede il tunneling forzato a una rete locale, Microsoft effettuerà assistenza caso per caso. Contattare il supporto tecnico per poter esaminare il caso. Se il caso viene accettato, la sottoscrizione verrà inserita nell'elenco elementi consentiti e la connettività Internet del firewall verrà mantenuta.|

<sup>1 : il</sup> nome del Se è necessario aumentare questi limiti, contattare il supporto di Azure.If you need to increase these limits, contact Azure Support.
