---
title: Configurare la disponibilità elevata per un gruppo di server di database di Azure per PostgreSQL-iperscala (CITUS)
description: Come abilitare o disabilitare la disponibilità elevata
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5ed29be1e890ddf2c4208ce9c03f01ce44f0e0d1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515915"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Configurare la disponibilità elevata di CITUS (iperscale)

Database di Azure per PostgreSQL-iperscalabilità (CITUS) fornisce disponibilità elevata (HA) per evitare tempi di inattività del database. Con la disponibilità elevata abilitata, ogni nodo in un gruppo di server riceverà un standby. Se il nodo originale diventa non integro, il relativo standby verrà promosso per sostituirlo.

> [!IMPORTANT]
> Poiché HA raddoppiato il numero di server nel gruppo, il costo sarà anche doppio.

È possibile abilitare la disponibilità elevata durante la creazione del gruppo di server o successivamente nella scheda **Configura** del gruppo di server nel portale di Azure. L'interfaccia utente è simile in entrambi i casi. Trascinare il dispositivo di scorrimento per la **disponibilità elevata** su on o off:

![dispositivo di scorrimento a disponibilità elevata](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Fare clic sul pulsante **Salva** per applicare la selezione. L'abilitazione di disponibilità elevata può richiedere del tempo quando il gruppo di server effettua il provisioning dei dati in standby e li trasmette.

Nella scheda **Panoramica** per il gruppo di server vengono elencati tutti i nodi e i relativi standby, insieme a una colonna a **disponibilità elevata** che indica se la disponibilità elevata è stata abilitata per ogni nodo.

### <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [disponibilità elevata](concepts-hyperscale-high-availability.md).
