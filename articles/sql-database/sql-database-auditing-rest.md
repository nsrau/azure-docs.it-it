---
title: API REST - Gestire il controllo del database SQL di Azure | Documentazione Microsoft
description: "Il controllo dei database SQL di Azure può essere configurato tramite l&quot;API REST, per tenere traccia di tutti gli eventi di database e scriverli in un log di controllo nell&quot;account di archiviazione di Azure."
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
ms.openlocfilehash: 9516dd1ae6a67fb9402591fe1906ee7554c1495c
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-sql-database-auditing-using-the-rest-api"></a>Configurare e gestire il controllo del database SQL usando l'API REST

Questo argomento descrive come configurare e gestire il controllo usando l'API REST. Per configurare e gestire il controllo tramite il portale di Azure, vedere [Configurare il controllo nel portale di Azure](sql-database-auditing-portal.md). Per configurare e gestire il controllo tramite PowerShell, vedere [Configurare il controllo con PowerShell](sql-database-auditing-powershell.md).

Per una panoramica sul del controllo, vedere [Controllo del database SQL](sql-database-auditing.md).

## <a name="rest-api---blob-auditing"></a>API REST - Controllo BLOB

   * [Creare o aggiornare i criteri controllo BLOB del database](https://msdn.microsoft.com/library/azure/mt695939.aspx)
   * [Creare o aggiornare i criteri controllo BLOB del server](https://msdn.microsoft.com/library/azure/mt771861.aspx)
   * [Ottenere i criteri controllo BLOB del database](https://msdn.microsoft.com/library/azure/mt695938.aspx)
   * [Ottenere i criteri controllo BLOB del server](https://msdn.microsoft.com/library/azure/mt771860.aspx)
   * [Ottenere il risultato dell'operazione di controllo BLOB del server](https://msdn.microsoft.com/library/azure/mt771862.aspx)


## <a name="rest-api---table-auditing"></a>API REST - Controllo Tabella

   * [Creare o aggiornare i criteri controllo del database](https://msdn.microsoft.com/library/azure/mt604471.aspx)
   * [Creare o aggiornare i criteri controllo del server](https://msdn.microsoft.com/library/azure/mt604383.aspx)
   * [Ottenere i criteri controllo del database](https://msdn.microsoft.com/library/azure/mt604381.aspx)
   * [Ottenere i criteri controllo del server](https://msdn.microsoft.com/library/azure/mt604382.aspx)

## <a name="next-steps"></a>Passaggi successivi

* Per configurare e gestire il controllo tramite il portale di Azure, vedere [Configurare il controllo del database nel portale di Azure](sql-database-auditing-portal.md). 
* Per configurare e gestire il controllo tramite PowerShell, vedere [Configurare il controllo del database con PowerShell](sql-database-auditing-powershell.md).
* Per una panoramica sul del controllo, vedere [Controllo del database](sql-database-auditing.md).
