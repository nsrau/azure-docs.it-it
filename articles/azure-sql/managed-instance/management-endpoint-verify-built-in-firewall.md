---
title: Verificare la sicurezza della porta nel firewall incorporato
description: Informazioni su come verificare la protezione firewall incorporata in Azure SQL Istanza gestita.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 1d2fffabba3615394bdf96ed487177bf21f3ecec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708706"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Verificare il firewall integrato Istanza gestita SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Per le regole di [sicurezza in ingresso obbligatorie](connectivity-architecture-overview.md#mandatory-inbound-security-rules) di sql Azure istanza gestita è necessario che le porte di gestione 9000, 9003, 1438, 1440 e 1452 siano aperte da **qualsiasi origine** nel gruppo di sicurezza di rete (NSG) che protegga SQL istanza gestita. Anche se queste porte sono aperte a livello di NSG, sono protetti a livello di rete dal firewall predefinito.

## <a name="verify-firewall"></a>Verifica del firewall

Per verificare queste porte, usare qualsiasi strumento scanner della sicurezza per testare queste porte. Lo screenshot seguente illustra come usare uno di questi strumenti.

![Verifica del firewall predefinito](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su SQL Istanza gestita e sulla connettività, vedere [architettura di connettività sql istanza gestita di Azure](connectivity-architecture-overview.md).
