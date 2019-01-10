---
title: Creazione di reti virtuali con Istanza gestita di database SQL di Azure | Microsoft Docs
description: Questo argomento descrive come creare una rete virtuale (VNet) in cui è possibile distribuire Istanza gestita di database SQL di Azure.
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
ms.date: 09/20/2018
ms.openlocfilehash: a8000fb26ce5496a9c62ba475b862f8f80adf6b7
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041765"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Configurare una rete virtuale per Istanza gestita di database SQL di Azure

Questo argomento descrive come creare una rete virtuale (VNet) e una subnet valide in cui è possibile distribuire Istanza gestita di database SQL di Azure.

Istanza gestita di database SQL di Azure deve essere distribuita in una [rete virtuale](../virtual-network/virtual-networks-overview.md) di Azure. Questa distribuzione consente di:

- Proteggere l'indirizzo IP privato.
- Stabilire una connessione a un'istanza gestita direttamente da una rete locale
- Connettere un'istanza gestita a un server collegato o a un altro archivio dati locale
- Connettere un'istanza gestita a risorse di Azure  

  > [!Note]
  > È consigliabile [determinare le dimensioni della subnet per l'istanza gestita](sql-database-managed-instance-determine-size-vnet-subnet.md) prima di distribuire la prima istanza, perché non è possibile ridimensionare la subnet dopo avervi inserito le risorse.
  > Se si prevede di usare una rete virtuale esistente, è necessario modificare la configurazione di rete in base all'istanza gestita. Per altre informazioni, vedere la sezione [Modificare la rete virtuale esistente per l'istanza gestita](sql-database-managed-instance-configure-vnet-subnet.md).

## <a name="create-a-new-virtual-network"></a>Creare una nuova rete virtuale

Il modo più semplice per creare e configurare una rete virtuale è usare il modello di distribuzione Azure Resource Manager.

1. Accedere al portale di Azure.

2. Usare **Distribuisci in Azure** per distribuire una rete virtuale nel cloud di Azure:

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Questo pulsante apre un modulo che è possibile usare per configurare l'ambiente di rete in cui distribuire l'istanza gestita.

   > [!Note]
   > Questo modello di Azure Resource Manager distribuirà una rete virtuale con due subnet. Una subnet denominata **ManagedInstances** è riservata alle istanze gestite e dispone di una tabella di route preconfigurata, mentre l'altra subnet denominata **Default** viene usata per le altre risorse che devono accedere all'istanza gestita (ad esempio, macchine virtuali di Azure). È possibile rimuovere la subnet **Default** subnet se non è necessaria.

3. Configurare l'ambiente di rete. Nel modulo seguente è possibile configurare i parametri dell'ambiente di rete:

![Configurare la rete di Azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

È possibile modificare facoltativamente i nomi della rete virtuale e delle subnet e gli intervalli IP associati alle risorse di rete. Facendo clic sul pulsante "Acquista", il modulo procederà a creare e configurare l'ambiente. Se non sono necessarie due subnet è possibile eliminare quella predefinita.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md).
- Altre informazioni sull'[architettura della connettività per l'istanza gestita](sql-database-managed-instance-connectivity-architecture.md).
- Informazioni su come [Modificare una rete virtuale esistente per le istanze gestite](sql-database-managed-instance-configure-vnet-subnet.md)
- Per un'esercitazione sulla creazione di una rete virtuale e di un'istanza gestita e sul ripristino di un database da un backup, vedere l'articolo su come [creare un'istanza gestita di database SQL di Azure](sql-database-managed-instance-get-started.md).
- Per questioni relative al DNS, vedere [Configuring a Custom DNS](sql-database-managed-instance-custom-dns.md) (Configurazione di un DNS personalizzato).
