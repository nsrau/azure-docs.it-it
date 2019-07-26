---
title: Attributi di sicurezza per il database SQL di Azure
description: Elenco di controllo degli attributi di sicurezza per la valutazione del database SQL di Azure
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 1318b3e433224b009b76458b12e82c9bcf94bb7a
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444385"
---
# <a name="security-attributes-for-azure-sql-database"></a>Attributi di sicurezza per il database SQL di Azure

Questo articolo descrive gli attributi di sicurezza incorporati nel database SQL di Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

Il database SQL include sia il [database singolo](sql-database-single-index.yml) che l' [istanza gestita](sql-database-managed-instance.md). Le voci seguenti si applicano a entrambe le offerte tranne se diversamente specificato.

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia, ad esempio lato client o Always Encrypted</ul>| Sì | Denominato "crittografia in uso", come descritto nell'articolo [Always Encrypted](sql-database-always-encrypted.md). La crittografia lato server utilizza [Transparent Data Encryption](transparent-data-encryption-azure-sql.md).|
| Crittografia in transito:<ul><li>Crittografia di Azure ExpressRoute</li><li>Crittografia in una rete virtuale</li><li>Crittografia tra reti virtuali</ul>| Sì | Uso di HTTPS. |
| Gestione della chiave di crittografia, ad esempio CMK o BYOK| Sì | Sono disponibili la gestione delle chiavi gestita dal servizio e gestita dal cliente. Quest'ultimo viene offerto tramite [Azure Key Vault](../key-vault/index.yml). |
| Crittografia a livello di colonna fornita da servizi dati di Azure| Sì | Tramite [Always Encrypted](sql-database-always-encrypted.md). |
| Chiamate API crittografate| Sì | Utilizzando HTTPS/SSL. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì | Si applica solo a [database singolo](sql-database-single-index.yml) . |
| Supporto per l'inserimento di reti virtuali di Azure| Sì | Si applica solo all' [istanza gestita](sql-database-managed-instance.md) . |
| Isolamento rete e supporto firewall| Sì | Firewall a livello di database e di server. L'isolamento rete è solo per l' [istanza gestita](sql-database-managed-instance.md) . |
| Supporto del tunneling forzato| Yes | [Istanza gestita](sql-database-managed-instance.md) tramite una VPN [ExpressRoute](../expressroute/index.yml) . |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure, ad esempio Log Analytics o Application Insights| Sì | SecureSphere, la soluzione SIEM da impervertito, è supportata anche tramite l'integrazione di [Hub eventi di Azure](../event-hubs/index.yml) tramite il [controllo SQL](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Azure Active Directory (Azure AD) |
| Authorization| Sì | Nessuna |

## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di controllo e del piano di gestione| Sì | Sì solo per alcuni eventi |
| Registrazione e controllo del piano dati | Sì | Tramite il [controllo SQL](sql-database-auditing.md) |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Configurazione-supporto per la gestione, ad esempio il controllo delle versioni della configurazione| No  | Nessuna |

## <a name="additional-security-attributes-for-sql-database"></a>Attributi di sicurezza aggiuntivi per il database SQL

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Prevenzione: valutazione della vulnerabilità | Sì | Vedere [servizio di valutazione della vulnerabilità SQL consente di identificare le vulnerabilità del database](sql-vulnerability-assessment.md). |
| Prevenzione: individuazione e classificazione dei dati  | Sì | Vedere [database SQL di Azure e SQL data warehouse individuazione dati & classificazione](sql-database-data-discovery-and-classification.md). |
| Rilevamento: rilevamento minacce | Yes | Vedere [Advanced Threat Protection per il database SQL di Azure](sql-database-threat-detection-overview.md). |
