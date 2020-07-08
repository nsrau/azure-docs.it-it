---
title: Crea rete virtuale
titleSuffix: Azure SQL Managed Instance
description: Questo articolo descrive come creare una rete virtuale configurata per supportare la distribuzione di Istanza gestita SQL di Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 09/12/2019
ms.openlocfilehash: d6458794b8d3c5ba68109dbc90a075d6a2a577b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84711324"
---
# <a name="create-a-virtual-network-for-azure-sql-managed-instance"></a>Creare una rete virtuale per Istanza gestita di SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Questo articolo illustra come creare una rete virtuale e una subnet valide in cui è possibile distribuire Istanza gestita SQL di Azure.

Istanza gestita SQL di Azure deve essere distribuito in una [rete virtuale](../../virtual-network/virtual-networks-overview.md)di Azure. Questa distribuzione consente di:

- Proteggere l'indirizzo IP privato
- Connessione a SQL Istanza gestita direttamente da una rete locale
- Connessione di SQL Istanza gestita a un server collegato o a un altro archivio dati locale
- Connessione di SQL Istanza gestita alle risorse di Azure  

> [!NOTE]
> È necessario [determinare le dimensioni della subnet per SQL istanza gestita](vnet-subnet-determine-size.md) prima di distribuire la prima istanza. Non è possibile ridimensionare la subnet dopo che sono state inserite le risorse.
>
> Se si prevede di usare una rete virtuale esistente, è necessario modificare la configurazione di rete per adattarla a SQL Istanza gestita. Per ulteriori informazioni, vedere la pagina relativa alla [modifica di una rete virtuale esistente per SQL istanza gestita](vnet-existing-add-subnet.md).
>
> Dopo la creazione di un'istanza gestita, lo stato di trasferimento dell'istanza gestita o della rete virtuale a un altro gruppo di risorse o sottoscrizione non è supportato.  Anche il trasferimento dell'istanza gestita in un'altra subnet non è supportato.
>

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Il modo più semplice per creare e configurare una rete virtuale è quello di usare un modello di distribuzione di Azure Resource Manager.

1. Accedere al portale di Azure.

2. Selezionare il pulsante **Distribuisci in Azure**:

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Questo pulsante apre un modulo che è possibile usare per configurare l'ambiente di rete in cui è possibile distribuire SQL Istanza gestita.

   > [!Note]
   > Questo modello di Azure Resource Manager distribuirà una rete virtuale con due subnet. Una subnet, denominata **ManagedInstances**, è riservata per SQL istanza gestita e dispone di una tabella di route preconfigurata. L'altra subnet, denominata **default**, viene usata per altre risorse che devono accedere a SQL istanza gestita (ad esempio, macchine virtuali di Azure).

3. Configurare l'ambiente di rete. Nel modulo seguente è possibile configurare i parametri dell'ambiente di rete:

   ![Modello di Resource Manager per configurare la rete di Azure](./media/virtual-network-subnet-create-arm-template/create-mi-network-arm.png)

   È possibile modificare i nomi della rete virtuale e delle subnet e anche gli intervalli IP associati alle risorse di rete. Dopo che si è fatto clic sul pulsante **Acquista**, il modulo procederà a creare e configurare l'ambiente. Se non sono necessarie due subnet è possibile eliminare quella predefinita.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [che cos'è SQL istanza gestita?](sql-managed-instance-paas-overview.md).
- Informazioni sull' [architettura di connettività in SQL istanza gestita](connectivity-architecture-overview.md).
- Informazioni su come [modificare una rete virtuale esistente per SQL istanza gestita](vnet-existing-add-subnet.md).
- Per un'esercitazione che illustra come creare una rete virtuale, creare un'istanza gestita e ripristinare un database da un backup del database, vedere [creare un'istanza gestita](instance-create-quickstart.md).
- Per problemi relativi al DNS, vedere [configurare un DNS personalizzato](custom-dns-configure.md).
