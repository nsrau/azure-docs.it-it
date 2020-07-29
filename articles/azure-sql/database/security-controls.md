---
title: Controlli di sicurezza
description: Elenco di controllo dei controlli di sicurezza per la valutazione del database SQL di Azure
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 97d2cd8e9ba51e4fc6ebab8459b04f4f37e876d3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668424"
---
# <a name="security-controls-for-azure-sql-database-and-sql-managed-instance"></a>Controlli di sicurezza per il database SQL di Azure e SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Questo articolo descrive i controlli di sicurezza incorporati nel database SQL di Azure e in Azure SQL Istanza gestita.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]



## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint di servizio| Sì | Si applica solo al [database SQL](../index.yml) . |
| Supporto per l'inserimento di reti virtuali di Azure| Sì | Si applica solo a [SQL istanza gestita](../managed-instance/sql-managed-instance-paas-overview.md) . |
| Isolamento rete e supporto firewall| Sì | Firewall a livello di database e di server. L'isolamento rete è solo per [SQL istanza gestita](../managed-instance/sql-managed-instance-paas-overview.md) . |
| Supporto del tunneling forzato| Sì | [Istanza gestita SQL](../managed-instance/sql-managed-instance-paas-overview.md) tramite una VPN [ExpressRoute](../expressroute/../index.yml) . |

## <a name="monitoring--logging"></a>Monitoraggio e registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure, ad esempio Log Analytics o Application Insights| Sì | SecureSphere, la soluzione SIEM da impervertito, è supportata anche tramite l'integrazione di [Hub eventi di Azure](../event-hubs/../index.yml) tramite il [controllo SQL](../../azure-sql/database/auditing-overview.md). |
| Registrazione e controllo del piano di controllo e del piano di gestione| Sì | Sì solo per alcuni eventi |
| Registrazione e controllo del piano dati | Sì | Tramite il [controllo SQL](../../azure-sql/database/auditing-overview.md) |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Azure Active Directory (Azure AD) |
| Autorizzazione| Sì | nessuno |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server dei dati inattivi: chiavi gestite da Microsoft | Sì | Denominato "crittografia in uso", come descritto nell'articolo [Always Encrypted](always-encrypted-certificate-store-configure.md). La crittografia lato server utilizza [Transparent Data Encryption](transparent-data-encryption-tde-overview.md).|
| Crittografia in transito:<ul><li>Crittografia di Azure ExpressRoute</li><li>Crittografia in una rete virtuale</li><li>Crittografia tra reti virtuali</ul>| Sì | Uso di HTTPS. |
| Gestione della chiave di crittografia, ad esempio CMK o BYOK| Sì | Sono disponibili la gestione delle chiavi gestita dal servizio e gestita dal cliente. Quest'ultimo viene offerto tramite [Azure Key Vault](../key-vault/../index.yml). |
| Crittografia a livello di colonna fornita da servizi dati di Azure| Sì | Tramite [Always Encrypted](always-encrypted-certificate-store-configure.md). |
| Chiamate API crittografate| Sì | Utilizzando HTTPS/TLS. |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Configurazione-supporto per la gestione, ad esempio il controllo delle versioni della configurazione| No  | nessuno |

## <a name="additional-security-controls-for-sql-database"></a>Controlli di sicurezza aggiuntivi per il database SQL

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Prevenzione: valutazione della vulnerabilità | Sì | Vedere [servizio di valutazione della vulnerabilità SQL consente di identificare le vulnerabilità del database](sql-vulnerability-assessment.md). |
| Prevenzione: individuazione e classificazione dei dati  | Sì | Vedere [database SQL di Azure e SQL data warehouse individuazione dati & classificazione](data-discovery-and-classification-overview.md). |
| Rilevamento: rilevamento minacce | Sì | Vedere [Advanced Threat Protection per il database SQL di Azure](threat-detection-overview.md). |

## <a name="next-steps"></a>Passaggi successivi

- Maggiori informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../../security/fundamentals/security-controls.md).
