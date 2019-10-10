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
ms.openlocfilehash: 9b15ebc40e99c1cd454396ccde5cca6b1a46abbc
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244755"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurazione di un DNS personalizzato per Istanza gestita di database SQL di Azure

Istanza gestita di database SQL di Azure deve essere distribuita in una [rete virtuale](../virtual-network/virtual-networks-overview.md) di Azure. In alcuni scenari, ad esempio Posta elettronica database e server collegati ad altre istanze SQL nell'ambiente cloud o ibrido, sono richiesti nomi host privati che devono essere risolti dall'istanza gestita. In questo caso, è necessario configurare un DNS personalizzato all'interno di Azure. 

Poiché Istanza gestita usa lo stesso DNS per i propri interventi interni, configurare il server DNS personalizzato in modo che possa risolvere i nomi di dominio pubblici.

> [!IMPORTANT]
> Usare sempre un nome di dominio completo (FQDN) per il server di posta elettronica, l'istanza di SQL Server e per altri servizi, anche se si trovano all'interno della zona DNS privata. Ad esempio, utilizzare `smtp.contoso.com` per il server di posta elettronica perché `smtp` non verrà risolto correttamente. Per creare un server collegato o una replica che fa riferimento a macchine virtuali SQL all'interno della stessa rete virtuale è necessario anche un nome di dominio completo e un suffisso DNS predefinito. Ad esempio `SQLVM.internal.cloudapp.net`. Per altre informazioni, vedere [risoluzione dei nomi che usa il proprio server DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> L'aggiornamento di server DNS della rete virtuale non influirà immediatamente Istanza gestita. Istanza gestita configurazione DNS viene aggiornata dopo la scadenza del lease DHCP o dopo la upgarade della piattaforma, a seconda di quale si verifica per primo. **Si consiglia agli utenti di impostare la configurazione DNS della rete virtuale prima di creare la prima Istanza gestita.**

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md)
- Per un'esercitazione che illustra come creare una nuova istanza gestita, vedere [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).
- Per informazioni sulla configurazione di una rete virtuale per un'istanza gestita, vedere la [configurazione della rete virtuale per le istanze gestite](sql-database-managed-instance-connectivity-architecture.md).
