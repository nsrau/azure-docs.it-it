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
ms.openlocfilehash: 92c2e79910e40721a0ef62d44825bd1f3e19fc79
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548213"
---
| Gruppi | Limite predefinito |
| --- | --- |
| Velocità effettiva dei dati |30 Gbps<sup>1</sup> |
|Regole|10.000. Tutti i tipi di regole combinati.|
|Numero massimo di regole DNAT|299|
|Dimensioni minime di AzureFirewallSubnet |/26|
|Intervallo di porte nelle regole di rete e delle applicazioni|0-64.000 Sono in corso interventi per ovviare a questa limitazione.|
|Indirizzi IP pubblici|100 massimo (attualmente, le porte SNAT vengono aggiunte solo per i primi cinque indirizzi IP pubblici).|
|Tabella di route|Per impostazione predefinita, AzureFirewallSubnet dispone di una route 0.0.0.0/0 con il valore NextHopType impostato su **Internet**.<br><br>Connettività diretta al Firewall di Azure. Se il AzureFirewallSubnet apprende una route predefinita per la rete locale tramite BGP, è necessario eseguire l'override di con un UDR 0.0.0.0/0 con il valore **NextHopType** impostato su **Internet** per mantenere la connettività Internet diretta. Per impostazione predefinita, Firewall di Azure non supporta il tunneling forzato a una rete locale.<br><br>Tuttavia, se la configurazione richiede il tunneling forzato a una rete locale, Microsoft effettuerà assistenza caso per caso. Contattare il supporto tecnico per poter esaminare il caso. Se il caso viene accettato, la sottoscrizione verrà inserita nell'elenco elementi consentiti e la connettività Internet del firewall verrà mantenuta.|

<sup>1</sup> Se è necessario aumentare questi limiti, contattare il supporto tecnico di Azure.
