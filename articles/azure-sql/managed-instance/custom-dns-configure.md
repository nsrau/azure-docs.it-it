---
title: DNS personalizzato
titleSuffix: Azure SQL Managed Instance
description: Questo argomento descrive le opzioni di configurazione per un DNS personalizzato con Istanza gestita SQL di Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 07/17/2019
ms.openlocfilehash: 9fdd61c5799724ab3ab098584f85ce52cdd6ccf4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91283259"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Configurare un DNS personalizzato per Istanza gestita SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Istanza gestita SQL di Azure deve essere distribuito in una [rete virtuale di Azure (VNet)](../../virtual-network/virtual-networks-overview.md). In alcuni scenari, ad esempio la posta elettronica del database e i server collegati ad altre istanze di SQL Server nell'ambiente cloud o ibrido, sono richiesti nomi host privati che devono essere risolti dall'Istanza gestita di SQL. In questo caso, è necessario configurare un DNS personalizzato all'interno di Azure. 

Poiché SQL Istanza gestita usa lo stesso DNS per i propri interventi interni, configurare il server DNS personalizzato in modo che possa risolvere i nomi di dominio pubblici.

> [!IMPORTANT]
> Usare sempre un nome di dominio completo (FQDN) per il server di posta elettronica, per l'istanza di SQL Server e per altri servizi, anche se si trovano all'interno della zona DNS privata. Ad esempio, utilizzare `smtp.contoso.com` per il server di posta elettronica perché `smtp` non risolverà correttamente il problema. La creazione di un server collegato o di una replica che fa riferimento a SQL Server VM all'interno della stessa rete virtuale richiede anche un nome di dominio completo e un suffisso DNS predefinito. Ad esempio: `SQLVM.internal.cloudapp.net`. Per altre informazioni, vedere [Risoluzione dei nomi con l'uso del proprio server DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> L'aggiornamento di server DNS della rete virtuale non influirà immediatamente su SQL Istanza gestita. La configurazione DNS di SQL Istanza gestita viene aggiornata dopo la scadenza del lease DHCP o dopo l'aggiornamento della piattaforma, a seconda di quale si verifica per primo. **Si consiglia agli utenti di impostare la configurazione DNS della rete virtuale prima di creare la prima istanza gestita.**

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere informazioni su [Azure SQL istanza gestita](sql-managed-instance-paas-overview.md).
- Per un'esercitazione che illustra come creare una nuova istanza gestita, vedere [creare un'istanza gestita](instance-create-quickstart.md).
- Per informazioni sulla configurazione di un VNet per un'istanza gestita, vedere la pagina relativa alla [configurazione di VNet per le istanze gestite](connectivity-architecture-overview.md).
