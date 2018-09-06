---
title: DNS personalizzato di Istanza gestita di database SQL di Azure | Microsoft Docs
description: Questo argomento descrive le opzioni di configurazione di un DNS personalizzato con un'istanza gestita di database SQL di Azure.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: af9afcbf97df5f3d7fa82f6ea0163c714fa4f582
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2018
ms.locfileid: "43051742"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurazione di un DNS personalizzato per Istanza gestita di database SQL di Azure

Un'istanza gestita di database SQL di Azure (anteprima) deve essere distribuita in una [rete virtuale](../virtual-network/virtual-networks-overview.md) di Azure. In alcuni scenari, ad esempio server collegati ad altre istanze SQL nell'ambiente cloud o ibrido, sono richiesti nomi host privati che devono essere risolti dall'istanza gestita. In questo caso, è necessario configurare un DNS personalizzato all'interno di Azure. Poiché Istanza gestita usa lo stesso DNS per i meccanismi interni, la configurazione del DNS della rete virtuale deve essere compatibile con Istanza gestita. 

Per rendere una configurazione DNS personalizzata compatibile con l'istanza gestita, è necessario: 
- Configurare un server DNS personalizzato in modo che sia in grado di risolvere i nomi di dominio pubblico 
- Inserire l'indirizzo IP DNS del resolver ricorsivo di Azure, 168.63.129.16, alla fine dell'elenco del DNS della rete virtuale 
 
## <a name="setting-up-custom-dns-servers-configuration"></a>Definizione della configurazione dei server DNS personalizzati

1. Nel portale di Azure, individuare l'opzione relativa al DNS personalizzato per la propria rete virtuale.

   ![opzione relativa al DNS personalizzato](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Passare a Personalizzato e immettere l'indirizzo IP del server DNS personalizzato, l'indirizzo IP 168.63.129.16 del resolver ricorsivo di Azure. 

   ![opzione relativa al DNS personalizzato](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > Se non si imposta il resolver ricorsivo di Azure nell'elenco DNS, l'istanza gestita entra in stato di errore. Per ripristinare una stato corretto potrebbe essere necessario creare una nuova istanza in una rete virtuale con i criteri di rete conformi, creare i dati a livello di istanza e ripristinare i database. Vedere [Configurazione della rete virtuale](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md)
- Per un'esercitazione che illustra come creare una nuova istanza gestita, vedere [Creare un'istanza gestita](sql-database-managed-instance-create-tutorial-portal.md).
- Per informazioni sulla configurazione di una rete virtuale per un'istanza gestita, vedere la [configurazione della rete virtuale per le istanze gestite](sql-database-managed-instance-vnet-configuration.md).
