---
title: Configurare la disponibilità elevata - Hyperscale (Citus) - Database di Azure per PostgreSQLConfigure high availability - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Come abilitare o disabilitare la disponibilità elevataHow to enable or disable high availability
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a8d4b5949b34d16191e9ec10a1dd39faff3660dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977660"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Configurare la disponibilità elevata di Hyperscale (Citus)Configure Hyperscale (Citus) high availability

Database di Azure per PostgreSQL - Hyperscale (Citus) offre disponibilità elevata (HA) per evitare tempi di inattività del database. Con la disponibilità dell'utente abilitata, ogni nodo in un gruppo di server otterrà uno standby. Se il nodo originale diventa non integro, il relativo standby verrà promosso per sostituirlo.

> [!IMPORTANT]
> Poiché HA raddoppia il numero di server nel gruppo, raddoppierà anche il costo.

L'abilitazione della disponibilità elevata è possibile durante la creazione del gruppo di server o in un secondo momento nella scheda Configura per il gruppo di server nel portale di Azure.Enabling HA is possible during server group creation, or afterward in the **Configure** tab for your server group in the Azure portal. In entrambi i casi l'interfaccia utente è simile. Trascinare il dispositivo di scorrimento per **Disponibilità elevata** su YES:

![ha cursore](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Fare clic sul pulsante **Salva** per applicare la selezione. L'abilitazione della disponibilità di sistema può richiedere del tempo in quanto il gruppo di server effettua il provisioning di standby e trasmette i dati.

La scheda **Panoramica** per il gruppo di server elencherà tutti i nodi e i relativi standby, insieme a una colonna **Disponibilità elevata** che indica se la disponibilità elevata è abilitata correttamente per ogni nodo.

![la colonna ha nella panoramica del gruppo di server](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulla [disponibilità elevata](concepts-hyperscale-high-availability.md).
