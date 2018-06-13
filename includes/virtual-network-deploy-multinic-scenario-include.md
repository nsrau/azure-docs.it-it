---
title: File di inclusione
description: File di inclusione
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: cab04a7eafbc21e0d26cd5a287f3dbee8d3d22b7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "31805105"
---
## <a name="scenario"></a>Scenario
In questo documento verrà illustrata una distribuzione che usa più schede di rete nelle macchine virtuali in uno scenario specifico. In questo scenario, si ha a disposizione un carico di lavoro IaaS a due livelli ospitato in Azure. Ogni livello viene distribuito nella propria subnet in una rete virtuale (VNet). Il livello di front-end è costituito da diversi server web, raggruppati in un bilanciamento del carico impostato per una disponibilità elevata. Il livello di back-end è costituito da più server di database. I server di database verranno distribuiti con due schede di rete, una per l'accesso al database, l'altra per la gestione. Lo scenario include anche i gruppi di sicurezza di rete (NSGs) per controllare quale traffico è consentito per ogni subnet, e la scheda di rete nella distribuzione. Nella figura seguente viene illustrata l'architettura di base di questo scenario.

![Scenario MultiNIC](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

