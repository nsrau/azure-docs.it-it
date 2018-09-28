---
title: DNS personalizzato di Istanza gestita di database SQL di Azure | Microsoft Docs
description: Questo argomento descrive le opzioni di configurazione di un DNS personalizzato con un'istanza gestita di database SQL di Azure.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 09/23/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 2d1bb7e8522da32dd33933261ea41b578f8afac1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949486"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurazione di un DNS personalizzato per Istanza gestita di database SQL di Azure

Istanza gestita di database SQL di Azure deve essere distribuita in una [rete virtuale](../virtual-network/virtual-networks-overview.md) di Azure. In alcuni scenari, ad esempio server collegati ad altre istanze SQL nell'ambiente cloud o ibrido, sono richiesti nomi host privati che devono essere risolti dall'istanza gestita. In questo caso, è necessario configurare un DNS personalizzato all'interno di Azure. Poiché Istanza gestita usa lo stesso DNS per i meccanismi interni, la configurazione del DNS della rete virtuale deve essere compatibile con Istanza gestita. 

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
- Per un'esercitazione che illustra come creare una nuova istanza gestita, vedere [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).
- Per informazioni sulla configurazione di una rete virtuale per un'istanza gestita, vedere la [configurazione della rete virtuale per le istanze gestite](sql-database-managed-instance-vnet-configuration.md).
