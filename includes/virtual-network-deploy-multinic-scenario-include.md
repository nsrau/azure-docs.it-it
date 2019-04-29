---
title: File di inclusione
description: File di inclusione
services: virtual-network
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 04/13/2018
ms.date: 06/11/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: d20ef44fd5c117e4e3a568542bb022c451ac23fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743284"
---
## <a name="scenario"></a>Scenario
In questo documento verrà illustrata una distribuzione che usa più schede di rete nelle macchine virtuali in uno scenario specifico. In questo scenario, si ha a disposizione un carico di lavoro IaaS a due livelli ospitato in Azure. Ogni livello viene distribuito nella propria subnet in una rete virtuale (VNet). Il livello di front-end è costituito da diversi server web, raggruppati in un bilanciamento del carico impostato per una disponibilità elevata. Il livello di back-end è costituito da più server di database. I server di database verranno distribuiti con due schede di rete, una per l'accesso al database, l'altra per la gestione. Lo scenario include anche i gruppi di sicurezza di rete (NSGs) per controllare quale traffico è consentito per ogni subnet, e la scheda di rete nella distribuzione. Nella figura seguente viene illustrata l'architettura di base di questo scenario.

![Scenario MultiNIC](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)