---
title: Individuare l'endpoint di gestione delle istanze gestiteDiscover managed instance management endpoint
description: Informazioni su come ottenere l'indirizzo IP pubblico dell'endpoint di gestione di Istanza gestita di database SQL di Azure e verificare la protezione del firewall predefinito
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 03cd89084c2bae3339311f2f684a0d5e7bac1f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825725"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Determinare l'indirizzo IP dell'endpoint di gestione

Il cluster virtuale di Istanza gestita di database SQL di Azure contiene un endpoint di gestione usato da Microsoft per le operazioni di gestione. L'endpoint di gestione è protetto con un firewall predefinito a livello di rete e con la verifica reciproca dei certificati a livello di applicazione. È possibile determinare l'indirizzo IP dell'endpoint di gestione, ma non è possibile accedere a questo endpoint.

Per determinare l'indirizzo IP di gestione, eseguire una `mi-name.zone_id.database.windows.net`ricerca DNS nel nome di dominio completo dell'istanza gestita: . Verrà restituita una voce DNS `trx.region-a.worker.vnet.database.windows.net`simile a . È quindi possibile eseguire una ricerca DNS su questo nome di dominio completo con ".vnet" rimosso. Verrà restituito l'indirizzo IP di gestione. 

Questo PowerShell eseguirà tutto per \<te se\> sostituisci l'FQDN `mi-name.zone_id.database.windows.net`MI con la voce DNS dell'istanza gestita: :
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Per altre informazioni sulle istanze gestite e sulla connettività, vedere [Architettura della connettività di Istanza gestita di database SQL di Azure](sql-database-managed-instance-connectivity-architecture.md).
