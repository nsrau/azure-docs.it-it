---
title: Individuare l'endpoint di gestione di Istanza gestita di database SQL di Azure | Microsoft Docs
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
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: b7eb9ecd6b94aad263346ad6b5c45b694e0bd46f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797959"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Determinare l'indirizzo IP dell'endpoint di gestione

Il cluster virtuale di Istanza gestita di database SQL di Azure contiene un endpoint di gestione usato da Microsoft per le operazioni di gestione. L'endpoint di gestione è protetto con un firewall predefinito a livello di rete e con la verifica reciproca dei certificati a livello di applicazione. È possibile determinare l'indirizzo IP dell'endpoint di gestione, ma non è possibile accedere a questo endpoint.

## <a name="determine-ip-address"></a>Determinare l'indirizzo IP

Si supponga che l'host di Istanza gestita sia `mi-demo.xxxxxx.database.windows.net`. Eseguire `nslookup` usando il nome host.

![Risoluzione del nome host interno](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

Eseguire ora un altro comando `nslookup` per il nome evidenziato rimuovendo il segmento `.vnet.`. Quando si esegue questo comando, si otterrà l'indirizzo IP pubblico.

![Risoluzione dell'indirizzo IP pubblico](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle istanze gestite e sulla connettività, vedere [Architettura della connettività di Istanza gestita di database SQL di Azure](sql-database-managed-instance-connectivity-architecture.md).
