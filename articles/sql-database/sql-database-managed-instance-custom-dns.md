---
title: DNS personalizzato dell'istanza gestita
description: Questo argomento descrive le opzioni di configurazione di un DNS personalizzato con un'istanza gestita di database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 1b27d316114b84434a84fc8226eba9328a803dc8
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774372"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurazione di un DNS personalizzato per Istanza gestita di database SQL di Azure

Istanza gestita di database SQL di Azure deve essere distribuita in una [rete virtuale](../virtual-network/virtual-networks-overview.md) di Azure. In alcuni scenari, ad esempio Posta elettronica database e server collegati ad altre istanze SQL nell'ambiente cloud o ibrido, sono richiesti nomi host privati che devono essere risolti dall'istanza gestita. In questo caso, è necessario configurare un DNS personalizzato all'interno di Azure. 

Poiché l'Istanza gestita usa lo stesso DNS per i propri interventi interni, configurare il server DNS personalizzato in modo che possa risolvere i nomi di dominio pubblici.

> [!IMPORTANT]
> Usare sempre un nome di dominio completo (FQDN) per il server di posta, l'istanza di SQL Server e per gli altri servizi, anche se sono nella zona DNS privata. Ad esempio, utilizzare `smtp.contoso.com` per il server di posta elettronica perché `smtp` non risolverà correttamente il problema. Per creare un server collegato o una replica che fa riferimento a macchine virtuali di SQL all'interno della stessa rete virtuale è necessario anche un nome di dominio completo e un suffisso DNS predefinito. Ad esempio: `SQLVM.internal.cloudapp.net`. Per altre informazioni, vedere [Risoluzione dei nomi con l'uso del proprio server DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> L'aggiornamento di server DNS della rete virtuale non influirà immediatamente sull'Istanza gestita. La configurazione del DNS dell'Istanza gestita viene aggiornata dopo la scadenza del lease DHCP o dopo l'aggiornamento della piattaforma, a seconda dell'evento che si verifica per primo. **Si consiglia agli utenti di impostare la configurazione del DNS della rete virtuale prima di creare la prima Istanza gestita.**

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md)
- Per un'esercitazione che illustra come creare una nuova istanza gestita, vedere [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).
- Per informazioni sulla configurazione di una rete virtuale per un'istanza gestita, vedere la [configurazione della rete virtuale per le istanze gestite](sql-database-managed-instance-connectivity-architecture.md).
