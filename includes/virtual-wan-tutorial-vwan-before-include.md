---
title: includere file
description: includere file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 945d701a2a7dffc259c601b4dab9fa1333ccc066
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023525"
---
Prima di iniziare la configurazione, verificare di soddisfare i criteri seguenti:

* Se si dispone già di una rete virtuale a cui ci si vuole connettere, verificare che nessuna delle subnet della rete locale si sovrappongano. La rete virtuale non richiede una subnet del gateway e non può avere alcun gateway di rete virtuale. Se non si ha una rete virtuale, è possibile crearne una usando la procedura descritta in questo articolo.
* Ottenere un intervallo di indirizzi IP per l'area dell'hub. L'hub è una rete virtuale e l'intervallo di indirizzi specificato per l'area hub non può sovrapporsi a una rete virtuale esistente a cui ci si connette. Inoltre, non può sovrapporsi agli intervalli di indirizzi che ci si connette a un'istanza locale. Se non si ha familiarità con gli intervalli di indirizzi IP presenti nella configurazione di rete locale, coordinarsi con qualcuno che possa fornire tali dettagli.
* Se non si ha una sottoscrizione di Azure, prima di iniziare creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).