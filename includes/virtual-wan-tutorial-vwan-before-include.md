---
title: File di inclusione
description: File di inclusione
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 66e21aefa5648262ca2fcc61501905f725ac0e4b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180051"
---
Prima di iniziare la configurazione, verificare di soddisfare i criteri seguenti:

* Se esiste già una rete virtuale a cui ci si vuole connettere, verificare che nessuna delle subnet della rete locale si sovrapponga alle reti virtuali a cui ci si vuole connettere. La rete virtuale non richiede una subnet del gateway e non possono essere presenti gateway di rete virtuale. Se non è disponibile una rete virtuale, crearne una seguendo la procedura descritta in questo articolo.
* Ottenere un intervallo di indirizzi IP per l'area dell'hub. L'hub è una rete virtuale e l'intervallo di indirizzi specificati per l'area dell'hub non può sovrapporsi ad alcuna delle reti virtuali esistenti a cui ci si connette. Inoltre non può sovrapporsi agli intervalli di indirizzi a cui ci si connette in locale. Se non si ha familiarità con gli intervalli degli indirizzi IP disponibili nella configurazione della rete locale, è necessario coordinarsi con qualcuno che possa fornire tali dettagli.
* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.