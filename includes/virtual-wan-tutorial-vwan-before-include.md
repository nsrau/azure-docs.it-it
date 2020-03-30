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
ms.openlocfilehash: 945d701a2a7dffc259c601b4dab9fa1333ccc066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74896598"
---
Prima di iniziare la configurazione, verificare che siano soddisfatti i criteri seguenti:

* Se si dispone già di una rete virtuale a cui connettersi, verificare che nessuna delle subnet della rete locale si sovrapponga. La rete virtuale non richiede una subnet gateway e non può avere gateway di rete virtuale. Se non si dispone di una rete virtuale, è possibile crearne una seguendo la procedura descritta in questo articolo.
* Ottenere un intervallo di indirizzi IP per l'area dell'hub. L'hub è una rete virtuale e l'intervallo di indirizzi specificato per l'area hub non può sovrapporsi a una rete virtuale esistente a cui ci si connette. Non può inoltre sovrapporsi agli intervalli di indirizzi a cui ci si connette in locale. Se non si ha familiarità con gli intervalli di indirizzi IP che si trovano nella configurazione di rete locale, coordinarsi con un utente in grado di fornire tali dettagli.
* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.