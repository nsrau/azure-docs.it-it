---
title: Individuare l'indirizzo IP dell'endpoint di gestione
titleSuffix: Azure SQL Managed Instance
description: Informazioni su come ottenere l'indirizzo IP pubblico dell'endpoint di gestione di SQL Istanza gestita di Azure e verificare la protezione del firewall incorporata
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: a9a2b904bd7526f00a8f8a5d013be0c1e42e38a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617367"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>Determinare l'indirizzo IP dell'endpoint di gestione-Istanza gestita SQL di Azure 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Il cluster virtuale Azure SQL Istanza gestita contiene un endpoint di gestione usato da Azure per le operazioni di gestione. L'endpoint di gestione è protetto con un firewall predefinito a livello di rete e con la verifica reciproca dei certificati a livello di applicazione. È possibile determinare l'indirizzo IP dell'endpoint di gestione, ma non è possibile accedere a questo endpoint.

Per determinare l'indirizzo IP di gestione, eseguire una [ricerca DNS](/windows-server/administration/windows-commands/nslookup) sull'FQDN di SQL istanza gestita: `mi-name.zone_id.database.windows.net` . Verrà restituita una voce DNS simile a `trx.region-a.worker.vnet.database.windows.net` . È quindi possibile eseguire una ricerca DNS su questo FQDN con ". vnet" rimosso. Verrà restituito l'indirizzo IP di gestione. 

Questo codice di PowerShell eseguirà tutte le operazioni se si sostituisce \<MI FQDN\> con la voce DNS di SQL istanza gestita: `mi-name.zone_id.database.windows.net` :
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Per altre informazioni su SQL Istanza gestita e sulla connettività, vedere [architettura di connettività sql istanza gestita di Azure](connectivity-architecture-overview.md).
