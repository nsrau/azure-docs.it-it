---
title: Esempi dell'interfaccia della riga di comando di Azure per Load Balancer
titleSuffix: Azure Load Balancer
description: Esempi dell'interfaccia della riga di comando di Azure
services: load-balancer
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.openlocfilehash: fcc2a579f2fe9048dd58cd8b52c8c704c894936b
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87499348"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Esempi dell'interfaccia della riga di comando di Azure per Load Balancer

La tabella seguente include collegamenti a script Bash compilati tramite l'interfaccia della riga di comando di Azure.

* [Eseguire il bilanciamento del carico per il traffico verso le macchine virtuali per la disponibilità elevata](./scripts/load-balancer-linux-cli-sample-nlb.md)

  Consente di creare più macchine virtuali in una configurazione a disponibilità elevata e con bilanciamento del carico.

* [Bilanciare il carico delle macchine virtuali tra zone di disponibilità](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md)

  Crea tre macchine virtuali in diverse zone di disponibilità all'interno di un'area e di Load Balancer Standard con un indirizzo IP front-end con ridondanza della zona. Questa configurazione del bilanciamento del carico facilita la protezione di app e dati da un poco probabile errore o perdita di un intero data center.

* [Bilanciare il carico delle VM in una zona di disponibilità specifica](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)

  Crea tre macchine virtuali, un Load Balancer Standard con indirizzo IP front-end di zona che consente di allineare il percorso di dati e le risorse in una singola zona per una determinata area.

* [Eseguire il bilanciamento del carico per più siti Web sulle VM](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md)

  Crea due VM con più configurazioni IP, unite a un set di disponibilità di Azure, accessibili tramite un servizio di bilanciamento del carico di Azure.
