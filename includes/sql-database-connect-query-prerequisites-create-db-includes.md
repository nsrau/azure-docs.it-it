---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 01/28/2019
ms.author: genemi
ms.openlocfilehash: edeaa932abe4d1882f957d0ed26aaddd97dabe3f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198062"
---
<!-- sql-database-connect-query-prerequisites-create-db-includes.md -->

- Un database SQL di Azure situato in un [server logico](https://docs.microsoft.com/azure/sql-database/sql-database-single-index) o in un'[istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index). Per creare un database, è possibile usare una delle tecniche elencate di seguito.

| Server logico | Istanza gestita |
| --- | --- |
| [Portale](../articles/sql-database/sql-database-get-started-portal.md) | [Portale](../articles/sql-database/sql-database-managed-instance-get-started.md) |
| [CLI](../articles/sql-database/sql-database-get-started-cli.md) | |
| [PowerShell](../articles/sql-database/sql-database-get-started-powershell.md) | |

- **Solo server logico**: una regola del firewall configurata a livello di server che consente di connettersi al server logico. Per altre informazioni, vedere [Creare una regola del firewall a livello di server](../articles/sql-database/sql-database-get-started-portal-firewall.md).
- **Solo istanza gestita**: connessione configurata dal computer che accede all'istanza gestita. È possibile usare le opzioni seguenti:
  - [Macchina virtuale di Azure](../articles/sql-database/sql-database-managed-instance-configure-vm.md) nella stessa rete virtuale di Azure dell'istanza gestita che può accedere all'istanza.
  - [Connessione da punto a sito](../articles/sql-database/sql-database-managed-instance-configure-p2s.md) nel computer che consentirà al computer di essere aggiunto alla rete virtuale in cui si trova l'istanza gestita e di usare l'istanza gestita come qualsiasi altra istanza di SQL Server nella rete.
