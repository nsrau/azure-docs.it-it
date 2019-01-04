---
title: File di inclusione
description: File di inclusione
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 12/14/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 7d905550114bb76a0a091146b3972bab4a652022
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429989"
---
| Risorsa | Limite predefinito |
| --- | --- |
| Dati elaborati |1000 TB/firewall/mese <sup>1</sup> |
|Regole|10.000 - tutti i tipi di regole combinati|
|Peering globale|Non supportati. È necessaria almeno una distribuzione del firewall per area.|
|Numero massimo di porte in una singola regola di rete|15<br>Un intervallo di porte (ad esempio: da 2 a 10) viene conteggiato come due.
|Dimensioni minime di AzureFirewallSubnet |/26|
|Intervallo di porte nelle regole di rete e delle applicazioni|0-64.000 Sono in corso interventi per ovviare a questa limitazione.|
|


<sup>1</sup> Contattare il supporto tecnico Azure nel caso in cui sia necessario aumentare questi limiti.
