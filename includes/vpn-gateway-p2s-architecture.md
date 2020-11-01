---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/30/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e3ad8fcb6cd5cf68b02ac522d0e5ef5a18ba8a3c
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93144997"
---
Le connessioni di autenticazione dei certificati di Azure native da punto a sito usano gli elementi seguenti, che vengono configurati in questo esercizio:

* Un gateway VPN RouteBased.
* La chiave pubblica (file CER) per un certificato radice, caricato in Azure. Il certificato, dopo essere stato caricato, viene considerato un certificato attendibile e viene usato per l'autenticazione.
* Un certificato client generato dal certificato radice. Il certificato client installato su ogni computer client che si connetterà alla rete virtuale. Questo certificato viene usato per l'autenticazione client.
* Configurazione del client VPN. Il client VPN viene configurato usando i file di configurazione del client VPN. Questi file contengono le informazioni necessarie per la connessione del client alla VNet. I file configurano il client VPN esistente, nativo del sistema operativo. Ogni client che si connette deve essere configurato usando le impostazioni nei file di configurazione.
