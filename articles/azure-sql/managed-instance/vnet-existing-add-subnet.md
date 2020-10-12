---
title: Configurare una rete virtuale esistente
titleSuffix: Azure SQL Managed Instance
description: Questo articolo descrive come configurare una rete virtuale e una subnet esistenti in cui è possibile distribuire Istanza gestita SQL di Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 03/17/2020
ms.openlocfilehash: 461acc07ee2217a38f7bb59805d4c7e0de4a1e22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617656"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-managed-instance"></a>Configurare una rete virtuale esistente per Istanza gestita di SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Istanza gestita SQL di Azure deve essere distribuito in una [rete virtuale](../../virtual-network/virtual-networks-overview.md) di Azure e la subnet dedicata solo per le istanze gestite. È possibile usare la rete virtuale e la subnet esistenti se sono configurate in base ai [requisiti della rete virtuale di SQL istanza gestita](connectivity-architecture-overview.md#network-requirements).

Se uno degli scenari seguenti si applica al proprio caso, è possibile convalidare e modificare la rete usando lo script presentato in questo articolo:

- Si ha una nuova subnet che non è ancora configurata.
- Non si è certi che la subnet sia conforme ai [requisiti](connectivity-architecture-overview.md#network-requirements).
- Si vuole verificare che la subnet sia ancora conforme ai [requisiti di rete](connectivity-architecture-overview.md#network-requirements) dopo aver apportato modifiche.

> [!Note]
> È possibile creare un'istanza gestita solo nelle reti virtuali create tramite il modello di distribuzione Azure Resource Manager. Le reti virtuali di Azure create tramite il modello di distribuzione classica non sono supportate. Calcolare le dimensioni della subnet seguendo le indicazioni riportate nell'articolo [determinare le dimensioni della subnet per SQL istanza gestita](vnet-subnet-determine-size.md) . Non è possibile ridimensionare la subnet dopo che vi sono state distribuite le risorse.
>
> Dopo la creazione dell'istanza gestita, lo stato di trasferimento dell'istanza o di VNet a un altro gruppo di risorse o a una sottoscrizione non è supportato.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Convalidare e modificare una rete virtuale esistente

Se si vuole creare un'istanza gestita all'interno di una subnet esistente, è consigliabile usare lo script di PowerShell seguente per preparare la subnet:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

Lo script prepara la subnet in tre passaggi:

1. Validate: convalida la rete virtuale e la subnet selezionate per i requisiti di rete di SQL Istanza gestita.
2. Conferma: Mostra all'utente un set di modifiche che devono essere apportate per preparare la subnet per la distribuzione di SQL Istanza gestita. Chiede inoltre all'utente di dare il proprio consenso.
3. Preparazione: configura correttamente la rete virtuale e la subnet.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [che cos'è SQL istanza gestita?](sql-managed-instance-paas-overview.md).
- Per un'esercitazione che illustra come creare una rete virtuale, creare un'istanza gestita e ripristinare un database da un backup del database, vedere [creare un'istanza gestita](instance-create-quickstart.md).
- Per questioni relative al DNS, vedere [Configurazione di un DNS personalizzato](custom-dns-configure.md).
