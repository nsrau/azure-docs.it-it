---
title: Individuazione del firewall incorporato dell'istanza gestita
description: Informazioni su come verificare la protezione del firewall predefinito nell'Istanza gestita di database SQL di Azure.
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
ms.openlocfilehash: 555ef56aafa37a1e1d384f945b04f9237adc5f7d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821809"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Verifica del firewall predefinito nell'Istanza gestita di database SQL di Azure

Le [regole di sicurezza in ingresso obbligatorie](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) dell'Istanza gestita di database SQL di Azure richiedono che le porte di gestione 9000, 9003, 1438, 1440, 1452 siano aperte da **Any source** nel gruppo di sicurezza di rete (NSG) che protegge l'Istanza gestita di database SQL di Azure. Anche se queste porte sono aperte a livello di NSG, sono protetti a livello di rete dal firewall predefinito.

## <a name="verify-firewall"></a>Verifica del firewall

Per verificare queste porte, usare qualsiasi strumento scanner della sicurezza per testare queste porte. Lo screenshot seguente illustra come usare uno di questi strumenti.

![Verifica del firewall predefinito](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle istanze gestite e sulla connettività, vedere [Architettura della connettività di Istanza gestita di database SQL di Azure](sql-database-managed-instance-connectivity-architecture.md).