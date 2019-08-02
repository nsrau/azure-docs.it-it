---
title: DNS personalizzato di Istanza gestita di database SQL di Azure | Microsoft Docs
description: Questo argomento descrive le opzioni di configurazione di un DNS personalizzato con un'istanza gestita di database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 588fac1fc48396584188eec44f21a7005dc8ed96
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567562"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurazione di un DNS personalizzato per Istanza gestita di database SQL di Azure

Istanza gestita di database SQL di Azure deve essere distribuita in una [rete virtuale](../virtual-network/virtual-networks-overview.md) di Azure. In alcuni scenari, ad esempio Posta elettronica database e server collegati ad altre istanze SQL nell'ambiente cloud o ibrido, sono richiesti nomi host privati che devono essere risolti dall'istanza gestita. In questo caso, è necessario configurare un DNS personalizzato all'interno di Azure. 

Poiché Istanza gestita usa lo stesso DNS per i propri interventi interni, è necessario configurare il server DNS personalizzato in modo che possa risolvere i nomi di dominio pubblici.

   > [!IMPORTANT]
   > Usare sempre nomi di dominio completi (FQDN) per i server di posta, le istanze di SQL Server e gli altri servizi, anche se sono nella zona DNS privata. Usare ad esempio `smtp.contoso.com` per il server di posta perché il semplice `smtp` non verrà risolto correttamente.

   > [!IMPORTANT]
   > L'aggiornamento di server DNS della rete virtuale non influirà immediatamente Istanza gestita. Istanza gestita la configurazione DNS verrà aggiornata dopo la scadenza del lease DHCP o dopo la upgarade della piattaforma, tutto ciò che viene raggiunto per primo. **Si consiglia agli utenti di impostare la configurazione DNS della rete virtuale prima di creare la prima Istanza gestita.**

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md)
- Per un'esercitazione che illustra come creare una nuova istanza gestita, vedere [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).
- Per informazioni sulla configurazione di una rete virtuale per un'istanza gestita, vedere la [configurazione della rete virtuale per le istanze gestite](sql-database-managed-instance-connectivity-architecture.md).
