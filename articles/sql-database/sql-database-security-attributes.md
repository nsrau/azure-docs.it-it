---
title: Attributi di sicurezza per il Database SQL di Azure
description: Un elenco di controllo degli attributi di protezione per la valutazione di Database SQL di Azure
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 6c495456a5a3295abe5460ff6b5586e41fab2d95
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001030"
---
# <a name="security-attributes-for-azure-sql-database"></a>Attributi di sicurezza per il Database SQL di Azure

Questo articolo sono descritti gli attributi di sicurezza comuni incorporati nel Database SQL di Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

Database SQL di Azure include sia [database singolo](sql-database-single-index.yml) e [istanza gestita](sql-database-managed-instance.md). Le voci seguenti si applicano a entrambe le offerte, tranne se non diversamente specificato.

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Sì | Detto "crittografia in uso", come descritto nell'articolo [Always Encrypted](sql-database-always-encrypted.md). Crittografia lato servizio viene utilizzato [crittografia dati trasparente](transparent-data-encryption-azure-sql.md) (TDE).|
| Crittografia in transito:<ul><li>Crittografia di ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Sì | Uso di HTTPS. |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| Sì | Gestione delle chiavi gestite dal servizio sia gestita dal cliente vengono offerti (quest'ultimo attraverso [Azure Key Vault](../key-vault/index.yml). |
| Crittografia a livello di colonna (Azure Data Services)| Sì | Attraverso [Always Encrypted](sql-database-always-encrypted.md). |
| Chiamate API crittografate| Sì | Tramite HTTPS/SSL. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| Sì | Si applica a [singolo database](sql-database-single-index.yml) solo. |
| Supportare l'inserimento di rete virtuale| Sì | Si applica a [istanza gestita](sql-database-managed-instance.md) solo. |
| Isolamento di rete e supporto firewall| Sì | In entrambi database - e a livello di server; del firewall isolamento di rete [istanza gestita](sql-database-managed-instance.md) solo |
| Supporto di tunneling forzato| Sì | [istanza gestita](sql-database-managed-instance.md) tramite [Azure ExpressRoute](../expressroute/index.yml) VPN |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Sì | La soluzione SIEM di terze parti cybersicurezza di Imperva (SecureSphere) è supportata anche, tramite [hub eventi di Azure](../event-hubs/index.yml) integrazione tramite [SQL audit](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Azure Active Directory. |
| Authorization| Sì |  |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Sì | Sì per solo alcuni eventi. |
| Controllo e registrazione di piano dati | Sì | Tramite [SQL audit](sql-database-auditing.md). |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| N.  | | 

## <a name="additional-security-attributes-for-sql-database"></a>Attributi di sicurezza aggiuntive per il Database SQL

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Preventive: valutazione della vulnerabilità | Sì | Visualizzare [valutazione della vulnerabilità SQL service consente di identificare le vulnerabilità dei database](sql-vulnerability-assessment.md). |
| Preventive: individuazione dati e classificazione  | Sì | Visualizzare [Database SQL di Azure e SQL Data Warehouse individuazione dati e classificazione](sql-database-data-discovery-and-classification.md). |
| Il rilevamento: rilevamento delle minacce | Sì | Visualizzare [Advanced Threat Protection per il Database SQL di Azure](sql-database-threat-detection-overview.md). |
