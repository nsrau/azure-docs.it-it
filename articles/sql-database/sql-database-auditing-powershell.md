---
title: PowerShell - Gestire il controllo del database SQL di Azure | Documentazione Microsoft
description: "Il controllo dei database SQL di Azure può essere configurato con PowerShell, per tenere traccia degli eventi di database e scriverli in un log di controllo nell&quot;account di archiviazione di Azure."
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 9839dfb02478593de9a6db59d2a462d2b64f957e
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-sql-database-auditing-using-powershell"></a>Configurare e gestire il controllo del database SQL usando PowerShell

La sezione seguente descrive come configurare e gestire il controllo usando PowerShell. Per configurare e gestire il controllo tramite il portale di Azure, vedere [Configurare il controllo nel portale di Azure](sql-database-auditing-portal.md). Per configurare e gestire il controllo tramite l'API REST, vedere [Configurare il controllo con l'API REST](sql-database-auditing-rest.md).

Per una panoramica sul del controllo, vedere [Controllo del database SQL](sql-database-auditing.md).

## <a name="powershell-cmdlets"></a>Cmdlet PowerShell

   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]

## <a name="next-steps"></a>Passaggi successivi

* Per configurare e gestire il controllo tramite il portale di Azure, vedere [Configurare il controllo del database nel portale di Azure](sql-database-auditing-portal.md). 
* Per configurare e gestire il controllo tramite PowerShell, vedere [Configurare il controllo del database con PowerShell](sql-database-auditing-rest.md).
* Per una panoramica sul del controllo, vedere [Controllo del database](sql-database-auditing.md).


[101]: https://msdn.microsoft.com/en-us/library/azure/mt603731(v=azure.200).aspx
[102]: https://msdn.microsoft.com/en-us/library/azure/mt619329(v=azure.200).aspx
[103]: https://msdn.microsoft.com/en-us/library/azure/mt603796(v=azure.200).aspx
[104]: https://msdn.microsoft.com/en-us/library/azure/mt603574(v=azure.200).aspx
[105]: https://msdn.microsoft.com/en-us/library/azure/mt603531(v=azure.200).aspx
[106]: https://msdn.microsoft.com/en-us/library/azure/mt603794(v=azure.200).aspx
[107]: https://msdn.microsoft.com/en-us/library/azure/mt619353(v=azure.200).aspx

