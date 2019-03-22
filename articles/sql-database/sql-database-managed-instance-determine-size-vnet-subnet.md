---
title: Determinare la dimensione di una rete virtuale/subnet esistente per Istanza gestita di database SQL di Azure | Microsoft Docs
description: Questo articolo descrive come calcolare le dimensioni della subnet in cui verranno distribuite le istanze gestite del database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 02/22/2019
ms.openlocfilehash: 05440698d40a2175fd47384fa2db94ad80d79aa4
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773597"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Determinare le dimensioni di una subnet della rete virtuale per Istanza gestita di database SQL di Azure

Istanza gestita di database SQL di Azure deve essere distribuita in una [rete virtuale](../virtual-network/virtual-networks-overview.md) di Azure.

Il numero di istanze gestite che possono essere distribuite nella subnet della rete virtuale dipende dalle dimensioni della subnet (intervallo di subnet).

Quando si crea un'istanza gestita, Azure alloca un determinato numero di macchine virtuali in base al livello selezionato durante il provisioning. Poiché queste macchine virtuali sono associate alla subnet, richiedono indirizzi IP. Per assicurare una disponibilità elevata durante le normali operazioni e la manutenzione del servizio, Azure può allocare ulteriori macchine virtuali. Di conseguenza, il numero di indirizzi IP necessari in una subnet è maggiore del numero di istanze gestite nella subnet stessa.

Come da progettazione, un'istanza gestita necessita di un minimo di 16 indirizzi IP in una subnet e può usare fino a 256 indirizzi IP. Di conseguenza, è possibile usare una subnet mask tra /28 e da/24 quando si definisce gli intervalli IP di subnet. Un bit di maschera di rete/28 (14 host per ogni rete) è una dimensione valida per un unico scopo generale o la distribuzione aziendali cruciali. Un maschera di bit di/27 (30 host per ogni rete) è ideale per una più distribuzioni a istanza gestita all'interno della stessa rete virtuale. Le impostazioni di bit di maschera di /26 (62 host) e da/24 (254 host) consente un'ulteriore aumento delle istanze della rete virtuale per supportare ulteriori istanze gestite.

> [!IMPORTANT]
> Una dimensione di subnet con 16 indirizzi IP è il minimo con conseguente rischio limitato per le altre istanza gestita di scalabilità. È consigliabile scegliere una subnet con un prefisso /27 o inferiore.

## <a name="determine-subnet-size"></a>Determinare le dimensioni della subnet

Se si prevede di distribuire più istanze gestite all'interno della subnet ed è necessario ottimizzare le dimensioni della subnet, usare questi parametri per formulare un calcolo:

- Cinque indirizzi IP nella subnet sono dedicati ad Azure
- Ogni istanza per utilizzo generico necessita di due indirizzi
- Ogni istanza business critical richiede quattro indirizzi

**Esempio**: si prevede di avere tre istanze gestite di utilizzo generico e due business critical. Questo significa che sono necessari 5 + 3 * 2 + 2 * 4 = 19 indirizzi IP. Poiché gli intervalli IP sono definiti in potenza di 2, è necessario l'intervallo IP di 32 (2^5) indirizzi IP. Pertanto è necessario riservare la subnet con subnet mask /27.

> [!IMPORTANT]
> Il calcolo visualizzato in precedenza diventa obsoleto con altri miglioramenti.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md).
- Altre informazioni sull'[architettura della connettività per l'istanza gestita](sql-database-managed-instance-connectivity-architecture.md).
- Vedere come [creare una rete virtuale in cui distribuire istanze gestite](sql-database-managed-instance-create-vnet-subnet.md).
- Per questioni relative al DNS, vedere [Configuring a Custom DNS](sql-database-managed-instance-custom-dns.md) (Configurazione di un DNS personalizzato).
