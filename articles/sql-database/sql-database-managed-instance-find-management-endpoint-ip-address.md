---
title: Individuazione Istanza gestita di database SQL di Azure endpoint di gestione
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
ms.openlocfilehash: 0cc3ca3a2b8130e52d8c7fc670c0ee7997e4aa47
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73688040"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Determinare l'indirizzo IP dell'endpoint di gestione

Il cluster virtuale di Istanza gestita di database SQL di Azure contiene un endpoint di gestione usato da Microsoft per le operazioni di gestione. L'endpoint di gestione è protetto con un firewall predefinito a livello di rete e con la verifica reciproca dei certificati a livello di applicazione. È possibile determinare l'indirizzo IP dell'endpoint di gestione, ma non è possibile accedere a questo endpoint.

Per determinare l'indirizzo IP di gestione, eseguire una ricerca DNS sull'FQDN dell'istanza gestita: `mi-name.zone_id.database.windows.net`. Verrà restituita una voce DNS simile `trx.region-a.worker.vnet.database.windows.net`. È quindi possibile eseguire una ricerca DNS su questo FQDN con ". vnet" rimosso. Verrà restituito l'indirizzo IP di gestione. 

Questa operazione verrà eseguita da PowerShell se si sostituisce \<MI FQDN\> con la voce DNS dell'istanza gestita: `mi-name.zone_id.database.windows.net`:
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Per altre informazioni sulle istanze gestite e sulla connettività, vedere [Architettura della connettività di Istanza gestita di database SQL di Azure](sql-database-managed-instance-connectivity-architecture.md).
