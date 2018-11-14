---
title: File di inclusione
description: File di inclusione
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/30/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: f1fd32fc02bee47d229093680166e6a13a5ba721
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2018
ms.locfileid: "49458853"
---
| Risorsa | Limite predefinito |
| --- | --- |
| Dati elaborati |1000 TB/firewall/mese <sup>1</sup> |
|Regole|10.000 - tutti i tipi di regole combinati|
|Peering reti virtuali|Per le implementazioni hub-spoke, massimo 50 reti virtuali spoke.|
|Peering globale|Non supportati. È necessaria almeno una distribuzione del firewall per area.|
|Numero massimo di porte in una singola regola di rete|15<br>Un intervallo di porte (ad esempio: da 2 a 10) viene conteggiato come due.


<sup>1</sup> Contattare il supporto tecnico Azure nel caso in cui sia necessario aumentare questi limiti.
