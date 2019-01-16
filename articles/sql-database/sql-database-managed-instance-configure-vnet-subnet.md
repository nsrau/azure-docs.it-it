---
title: Configurazione di una rete virtuale/subnet esistente per Istanza gestita di database SQL di Azure | Microsoft Docs
description: Questo argomento descrive come configurare una rete virtuale e una subnet esistenti in cui è possibile distribuire Istanza gestita di database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 1718177a0902bc7049eb6986e5a1d128eeb3f233
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040959"
---
# <a name="configure-an-existing-vnet-for-azure-sql-database-managed-instance"></a>Configurare una rete virtuale esistente per Istanza gestita di database SQL di Azure

Istanza gestita di database SQL di Azure deve essere distribuita in una [rete virtuale](../virtual-network/virtual-networks-overview.md) di Azure e nella subnet dedicate solo alle istanze gestite. È possibile usare la rete virtuale e la subnet esistenti se sono configurate in base ai [requisiti per la rete virtuale di Istanza gestita](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Se si ha una nuova subnet non ancora configurata, non si è sicuri che la subnet sia allineata ai [requisiti](sql-database-managed-instance-connectivity-architecture.md#network-requirements) o si vuole controllare che la subnet sia ancora conforme ai [requisiti di rete](sql-database-managed-instance-connectivity-architecture.md#network-requirements) dopo avere apportato alcune modifiche, è possibile convalidare e modificare la rete usando lo script illustrato in questa sezione.

  > [!Note]
  > È possibile creare un'istanza gestita solo in reti virtuali di Resource Manager. Le reti virtuali di Azure distribuite usando il modello di distribuzione classica non sono supportate. Calcolare le dimensioni della subnet seguendo le linee guida indicate nell'articolo [Determinare le dimensioni di una subnet della rete virtuale per Istanza gestita di database SQL di Azure](sql-database-managed-instance-determine-size-vnet-subnet.md), in quanto la subnet non può essere ridimensionata dopo avervi distribuito le risorse.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Convalidare e modificare una rete virtuale esistente

Per creare un'istanza gestita all'interno di una subnet esistente, è consigliabile usare lo script di PowerShell seguente per preparare la subnet:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

La preparazione della subnet viene eseguita in tre semplici passaggi:

1. Convalida: la rete virtuale e la subnet selezionate vengono convalidate in base ai requisiti di rete dell'istanza gestita.
2. Conferma: l'utente visualizza una serie di modifiche che devono essere apportate per preparare la subnet per la distribuzione dell'istanza gestita e gli viene richiesto il consenso.
3. Preparazione: la rete virtuale e la subnet sono configurate correttamente.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md)
- Per un'esercitazione sulla creazione di una rete virtuale e di un'istanza gestita e sul ripristino di un database da un backup, vedere l'articolo su come [creare un'istanza gestita di database SQL di Azure](sql-database-managed-instance-get-started.md).
- Per questioni relative al DNS, vedere [Configurazione di un DNS personalizzato](sql-database-managed-instance-custom-dns.md).
