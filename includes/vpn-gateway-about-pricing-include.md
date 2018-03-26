---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 205c67377e4bff66c02e3ee508c0f6f4e2823608
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
Si pagano due cose: i costi di calcolo orari per il gateway di rete virtuale e il trasferimento dei dati in uscita dal gateway di rete virtuale. Le informazioni sui prezzi sono disponibili nella pagina [Prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway) .

**Costi di calcolo per il gateway di rete virtuale**<br>Ogni gateway di rete virtuale ha un costo di calcolo orario. Il prezzo dipende dallo SKU del gateway specificato quando si crea un gateway di rete virtuale. Il costo è relativo al gateway in sé e va aggiunto al trasferimento dei dati che passano dal gateway.

**Costi di trasferimento dati**<br>I costi per il trasferimento dei dati vengono calcolati in base al traffico in uscita dal gateway di rete virtuale di origine.

* Se si invia il traffico al dispositivo VPN locale, i costi verranno addebitati insieme alla tariffa per il trasferimento dei dati in uscita da Internet.
* Se si invia il traffico tra reti virtuali in aree diverse, i prezzi dipendono dall'area.
* Se si invia il traffico solo tra reti virtuali nella stessa area, non sono previsti costi per i dati. Il traffico tra reti virtuali nella stessa area è gratuito.