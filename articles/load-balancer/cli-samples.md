---
title: Esempi dell'interfaccia della riga di comando di Azure per Load Balancer
titlesuffix: Azure Load Balancer
description: Esempi dell'interfaccia della riga di comando di Azure
services: load-balancer
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.openlocfilehash: 9567dc1425ea74a9f46912532c5b8e4e4afc2fdb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275683"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Esempi dell'interfaccia della riga di comando di Azure per Load Balancer

La tabella seguente include collegamenti a script Bash compilati tramite l'interfaccia della riga di comando di Azure.

| | |
|-|-|
| [Eseguire il bilanciamento del carico per il traffico verso le VM per la disponibilità elevata](./scripts/load-balancer-linux-cli-sample-nlb.md) | Consente di creare più macchine virtuali in una configurazione a disponibilità elevata e con bilanciamento del carico. |
| [Bilanciare il carico delle macchine virtuali tra zone di disponibilità](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Crea tre macchine virtuali in diverse zone di disponibilità all'interno di un'area e di Load Balancer Standard con un indirizzo IP front-end con ridondanza della zona. Questa configurazione del bilanciamento del carico facilita la protezione di app e dati da un poco probabile errore o perdita di un intero data center. |
|[Bilanciare il carico delle VM in una zona di disponibilità specifica](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Crea tre macchine virtuali, un Load Balancer Standard con indirizzo IP front-end di zona che consente di allineare il percorso di dati e le risorse in una singola zona per una determinata area.|
| [Eseguire il bilanciamento del carico per più siti Web sulle VM](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Crea due VM con più configurazioni IP, unite a un set di disponibilità di Azure, accessibili tramite un servizio di bilanciamento del carico di Azure. |
| | |

