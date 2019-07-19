---
title: Creare una rete virtuale per Istanza gestita di database SQL di Azure | Microsoft Docs
description: Questo articolo descrive come creare una rete virtuale in cui è possibile distribuire Istanza gestita di database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 01/15/2019
ms.openlocfilehash: d6e205c23545eb4a01ce58a8bc2b63c58200e32a
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228281"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Creare una rete virtuale per Istanza gestita di database SQL di Azure

Questo articolo descrive come creare una rete virtuale e una subnet valide in cui è possibile distribuire Istanza gestita di database SQL di Azure.

Istanza gestita di database SQL di Azure deve essere distribuita all'interno di una [rete virtuale](../virtual-network/virtual-networks-overview.md) di Azure. Questa distribuzione consente di:

- Proteggere l'indirizzo IP privato
- Stabilire una connessione a un'istanza gestita direttamente da una rete locale
- Connettere un'istanza gestita a un server collegato o a un altro archivio dati locale
- Connettere un'istanza gestita a risorse di Azure  

> [!Note]
> È consigliabile [determinare le dimensioni della subnet per Istanza gestita](sql-database-managed-instance-determine-size-vnet-subnet.md) prima di distribuire la prima istanza. Non è possibile ridimensionare la subnet dopo che sono state inserite le risorse.
>
> Se si prevede di usare una rete virtuale esistente, è necessario modificare la configurazione di rete in base all'istanza gestita. Per altre informazioni, vedere [Configurare una rete virtuale esistente per Istanza gestita](sql-database-managed-instance-configure-vnet-subnet.md).
>
> Dopo la creazione di un'istanza gestita, lo stato di trasferimento dell'istanza gestita o VNet a un altro gruppo di risorse o a una sottoscrizione non è supportato.


## <a name="create-a-virtual-network"></a>Crea rete virtuale

Il modo più semplice per creare e configurare una rete virtuale è quello di usare un modello di distribuzione di Azure Resource Manager.

1. Accedere al portale di Azure.

2. Selezionare il pulsante **Distribuisci in Azure**:

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Questo pulsante apre un modulo che è possibile usare per configurare l'ambiente di rete in cui distribuire Istanza gestita.

   > [!Note]
   > Questo modello di Azure Resource Manager distribuirà una rete virtuale con due subnet. Una subnet, denominata **ManagedInstances**, è riservata per Istanza gestita e ha una tabella di route preconfigurata. L'altra subnet, denominata **Default**, viene usata per le altre risorse che devono accedere a Istanza gestita, ad esempio Macchine virtuali di Azure.

3. Configurare l'ambiente di rete. Nel modulo seguente è possibile configurare i parametri dell'ambiente di rete:

   ![Modello di Resource Manager per configurare la rete di Azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   È possibile modificare i nomi della rete virtuale e delle subnet e anche gli intervalli IP associati alle risorse di rete. Dopo che si è fatto clic sul pulsante **Acquista**, il modulo procederà a creare e configurare l'ambiente. Se non sono necessarie due subnet è possibile eliminare quella predefinita.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md).
- Altre informazioni sull'[architettura della connettività per l'istanza gestita](sql-database-managed-instance-connectivity-architecture.md).
- Informazioni su come [modificare una rete virtuale esistente per Istanza gestita](sql-database-managed-instance-configure-vnet-subnet.md).
- Per un'esercitazione che mostra come creare una rete virtuale e un'istanza gestita e ripristinare un database da un backup, vedere [Creare un'istanza gestita di database SQL di Azure](sql-database-managed-instance-get-started.md).
- Per questioni relative al DNS, vedere [Configurazione di un DNS personalizzato](sql-database-managed-instance-custom-dns.md).
