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
ms.openlocfilehash: df1ffa07c9b813ee3da4952bbcc394f43c69b7ac
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204228"
---
# <a name="security-attributes-for-azure-sql-database"></a>Attributi di sicurezza per il Database SQL di Azure

Questo articolo sono descritti gli attributi di sicurezza comuni incorporati nel Database SQL di Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

Database SQL di Azure include sia [database singolo](sql-database-single-index.yml) e [istanza gestita](sql-database-managed-instance.md). Le voci seguenti si applicano a entrambe le offerte, tranne se non diversamente specificato.

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Sì | Detto "crittografia in uso", come descritto nell'articolo [Always Encrypted](sql-database-always-encrypted.md). Crittografia lato servizio viene utilizzato [crittografia dati trasparente](transparent-data-encryption-azure-sql.md) (TDE).|
| Crittografia in transito:<ul><li>Crittografia di ExpressRoute</li><li>Crittografia nella rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Sì | Uso di HTTPS. |
| Gestione delle chiavi di crittografia (CMK, BYOK e così via)| Sì | Gestione delle chiavi gestite dal servizio sia gestita dal cliente vengono offerti (quest'ultimo attraverso [Azure Key Vault](../key-vault/index.yml). |
| Crittografia a livello di colonna (Servizi dati di Azure)| Sì | Attraverso [Always Encrypted](sql-database-always-encrypted.md). |
| Chiamate API crittografate| Sì | Tramite HTTPS/SSL. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint di servizio| Sì | Si applica a [singolo database](sql-database-single-index.yml) solo. |
| Supporto aggiunta rete virtuale| Sì | Si applica a [istanza gestita](sql-database-managed-instance.md) solo. |
| Supporto isolamento rete/protezione tramite firewall| Sì | In entrambi database - e a livello di server; del firewall isolamento di rete [istanza gestita](sql-database-managed-instance.md) solo |
| Supporto tunneling forzato | Sì | [istanza gestita](sql-database-managed-instance.md) tramite [Azure ExpressRoute](../expressroute/index.yml) VPN |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Sì | La soluzione SIEM di terze parti cybersicurezza di Imperva (SecureSphere) è supportata anche, tramite [hub eventi di Azure](../event-hubs/index.yml) integrazione tramite [SQL audit](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Gestione degli accessi - Autenticazione| Sì | Azure Active Directory. |
| Gestione degli accessi - Autorizzazione| Sì |  |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Piano di gestione/controllo - Registrazione e controllo| Sì | Sì per solo alcuni eventi. |
| Piano dati - Registrazione e controllo | Sì | Tramite [SQL audit](sql-database-auditing.md). |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| No   | | 

## <a name="additional-security-attributes-for-sql-database"></a>Attributi di sicurezza aggiuntive per il Database SQL

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Preventive: valutazione della vulnerabilità | Sì | Visualizzare [valutazione della vulnerabilità SQL service consente di identificare le vulnerabilità dei database](sql-vulnerability-assessment.md). |
| Preventive: individuazione dati e classificazione  | Sì | Visualizzare [Database SQL di Azure e SQL Data Warehouse individuazione dati e classificazione](sql-database-data-discovery-and-classification.md). |
| Il rilevamento: rilevamento delle minacce | Sì | Visualizzare [Advanced Threat Protection per il Database SQL di Azure](sql-database-threat-detection-overview.md). |
