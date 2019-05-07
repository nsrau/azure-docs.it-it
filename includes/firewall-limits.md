---
title: File di inclusione
description: File di inclusione
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 5/3/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8709d4d903bd31ff94d04ec61e226857e4190407
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150332"
---
| Resource | Limite predefinito |
| --- | --- |
| Velocità effettiva dei dati |30 Gbps<sup>1</sup> |
|Regole|10.000, tutte le regole di combinare tipi.|
|Dimensioni minime di AzureFirewallSubnet |/26|
|Intervallo di porte nelle regole di rete e delle applicazioni|0-64.000 Sono in corso interventi per ovviare a questa limitazione.|
|Tabella di route|Per impostazione predefinita, AzureFirewallSubnet dispone di una route 0.0.0.0/0 con il valore NextHopType impostata **Internet**.<br><br>Firewall di Azure deve avere connettività Internet diretta. Se il AzureFirewallSubnet apprende una route predefinita alla rete locale tramite BGP, è necessario sostituire questo con una route UDR 0.0.0.0/0 con il **NextHopType** impostare il valore come **Internet** mantenere diretto Connettività Internet. Per impostazione predefinita, i Firewall di Azure non supporta il tunneling forzato a una rete locale.<br><br>Tuttavia, se la configurazione richiede il tunneling forzato a una rete locale, Microsoft supporterà il caso per caso. Contattare il supporto in modo da consentire di rivedere il caso. Se accettato, verrà elenco elementi consentiti la sottoscrizione e assicurare che venga mantenuta la connettività Internet di firewall necessarie.|

<sup>1</sup>se è necessario aumentare questi limiti, contattare il supporto tecnico di Azure.
