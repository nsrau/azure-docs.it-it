---
title: Controlli di sicurezza
description: Elenco di controllo dei controlli di sicurezza per la valutazione del database SQL di Azure
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 58070cab1221b9d9585784d82cf1a48afcedb8af
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802836"
---
# <a name="security-controls-for-azure-sql-database"></a>Controlli di sicurezza per il database SQL di Azure

Questo articolo descrive i controlli di sicurezza incorporati nel database SQL di Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

Il database SQL include sia il [database singolo](sql-database-single-index.yml) che l' [istanza gestita](sql-database-managed-instance.md). Le voci seguenti si applicano a entrambe le offerte tranne se diversamente specificato.

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì | Si applica solo a [database singolo](sql-database-single-index.yml) . |
| Supporto per l'inserimento di reti virtuali di Azure| Sì | Si applica solo all' [istanza gestita](sql-database-managed-instance.md) . |
| Isolamento rete e supporto firewall| Sì | Firewall a livello di database e di server. L'isolamento rete è solo per l' [istanza gestita](sql-database-managed-instance.md) . |
| Supporto del tunneling forzato| Sì | [Istanza gestita](sql-database-managed-instance.md) tramite una VPN [ExpressRoute](../expressroute/index.yml) . |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure, ad esempio Log Analytics o Application Insights| Sì | SecureSphere, la soluzione SIEM da impervertito, è supportata anche tramite l'integrazione di [Hub eventi di Azure](../event-hubs/index.yml) tramite il [controllo SQL](sql-database-auditing.md). |
| Registrazione e controllo del piano di controllo e del piano di gestione| Sì | Sì solo per alcuni eventi |
| Registrazione e controllo del piano dati | Sì | Tramite il [controllo SQL](sql-database-auditing.md) |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Autenticazione| Sì | Azure Active Directory (Azure AD) |
| Autorizzazione| Sì | Nessuna |

## <a name="data-protection"></a>Protezione dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server: chiavi gestite da Microsoft | Sì | Denominato "crittografia in uso", come descritto nell'articolo [Always Encrypted](sql-database-always-encrypted.md). La crittografia lato server utilizza [Transparent Data Encryption](transparent-data-encryption-azure-sql.md).|
| Crittografia in transito:<ul><li>Crittografia di Azure ExpressRoute</li><li>Crittografia in una rete virtuale</li><li>Crittografia tra reti virtuali</ul>| Sì | Viene usato HTTPS. |
| Gestione della chiave di crittografia, ad esempio CMK o BYOK| Sì | Sono disponibili la gestione delle chiavi gestita dal servizio e gestita dal cliente. Quest'ultimo viene offerto tramite [Azure Key Vault](../key-vault/index.yml). |
| Crittografia a livello di colonna fornita da servizi dati di Azure| Sì | Tramite [Always Encrypted](sql-database-always-encrypted.md). |
| Chiamate API crittografate| Sì | Utilizzando HTTPS/SSL. |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Configurazione-supporto per la gestione, ad esempio il controllo delle versioni della configurazione| No  | Nessuna |

## <a name="additional-security-controls-for-sql-database"></a>Controlli di sicurezza aggiuntivi per il database SQL

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Prevenzione: valutazione della vulnerabilità | Sì | Vedere [servizio di valutazione della vulnerabilità SQL consente di identificare le vulnerabilità del database](sql-vulnerability-assessment.md). |
| Prevenzione: individuazione e classificazione dei dati  | Sì | Vedere [database SQL di Azure e SQL data warehouse individuazione dati & classificazione](sql-database-data-discovery-and-classification.md). |
| Rilevamento: rilevamento minacce | Sì | Vedere [Advanced Threat Protection per il database SQL di Azure](sql-database-threat-detection-overview.md). |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).
