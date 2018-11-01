---
title: Esempi dell'interfaccia della riga di comando di Azure per Load Balancer | Microsoft Docs
description: Esempi dell'interfaccia della riga di comando di Azure
services: load-balancer
documentationcenter: load-balancer
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: kumud
ms.openlocfilehash: 3973b541a0816473641f11391ddb59ca14618e30
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50415288"
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

