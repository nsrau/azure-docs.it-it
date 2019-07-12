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
ms.openlocfilehash: 71e87a3295a9cd73dca2f97b3fa04a5d5ff76f84
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798678"
---
# <a name="security-attributes-for-azure-sql-database"></a>Attributi di sicurezza per il Database SQL di Azure

Questo articolo sono descritti gli attributi di sicurezza comuni che sono incorporati nel Database SQL di Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

Il Database SQL include entrambe [database singolo](sql-database-single-index.yml) e [istanza gestita](sql-database-managed-instance.md). Le voci seguenti si applicano a entrambe le offerte indicato diversamente.

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia, ad esempio sul lato client o Always Encrypted</ul>| Yes | Chiamato "crittografia in uso," come descritto nell'articolo [Always Encrypted](sql-database-always-encrypted.md). Crittografia lato server viene utilizzato [crittografia dati trasparente](transparent-data-encryption-azure-sql.md).|
| Crittografia in transito:<ul><li>Crittografia di Azure ExpressRoute</li><li>Crittografia in una rete virtuale</li><li>Crittografia tra le reti virtuali</ul>| Sì | Uso di HTTPS. |
| Chiave di crittografia-gestione, ad esempio chiavi master di colonna o BYOK| Sì | Gestione delle chiavi gestite dal servizio sia gestita dal cliente sono disponibili. Quest'ultimo viene fornito attraverso [Azure Key Vault](../key-vault/index.yml). |
| Crittografia a livello di colonna fornita da servizi dati di Azure| Yes | Attraverso [Always Encrypted](sql-database-always-encrypted.md). |
| Chiamate API crittografate| Sì | Tramite HTTPS/SSL. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| Sì | Si applica a [singolo database](sql-database-single-index.yml) solo. |
| Supportare l'inserimento di rete virtuale Azure| Sì | Si applica a [istanza gestita](sql-database-managed-instance.md) solo. |
| Isolamento di rete e supporto del firewall| Sì | Il firewall a livello di database sia a livello di server. L'isolamento rete è per [istanza gestita](sql-database-managed-instance.md) solo. |
| Supporto di tunneling forzato| Sì | [Istanza gestita](sql-database-managed-instance.md) tramite un [ExpressRoute](../expressroute/index.yml) VPN. |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto, ad esempio Application Insights o Log Analitica di monitoraggio di Azure| Yes | SecureSphere, la soluzione SIEM cybersicurezza di Imperva, è anche supportato tramite [hub eventi di Azure](../event-hubs/index.yml) integrazione tramite [controllo SQL](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | Azure Active Directory (Azure AD) |
| Authorization| Sì | Nessuna |

## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Controllo e piano di controllo e piano di gestione di registrazione| Sì | Sì per solo alcuni eventi |
| Controllo e registrazione di piano dati | Sì | Tramite [SQL audit](sql-database-auditing.md) |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di gestione della configurazione, ad esempio il controllo delle versioni della configurazione| No  | Nessuna |

## <a name="additional-security-attributes-for-sql-database"></a>Attributi di sicurezza aggiuntive per il Database SQL

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Preventive: valutazione della vulnerabilità | Yes | Visualizzare [valutazione della vulnerabilità SQL service consente di identificare le vulnerabilità dei database](sql-vulnerability-assessment.md). |
| Preventive: individuazione dati e classificazione  | Yes | Visualizzare [Database SQL di Azure e SQL Data Warehouse individuazione dati e classificazione](sql-database-data-discovery-and-classification.md). |
| Il rilevamento: rilevamento delle minacce | Sì | Visualizzare [Advanced Threat Protection per il Database SQL di Azure](sql-database-threat-detection-overview.md). |
