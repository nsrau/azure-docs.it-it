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
ms.openlocfilehash: ce7f3eafa57cbd993be98f4a2da3d89cb312f9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190685"
---
# <a name="security-controls-for-azure-sql-database"></a>Controlli di sicurezza per il database SQL di AzureSecurity controls for Azure SQL Database

Questo articolo documenta i controlli di sicurezza incorporati nel database SQL di Azure.This article documents the security controls that are built into Azure SQL Database.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

Il database SQL include sia [un singolo database che](sql-database-single-index.yml) [un'istanza gestita.](sql-database-managed-instance.md) Le seguenti voci si applicano a entrambe le offerte tranne dove diversamente indicato.

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto dell'endpoint di servizioService endpoint support| Sì | Si applica solo a [un singolo database.](sql-database-single-index.yml) |
| Supporto per l'inserimento di rete virtuale di AzureAzure Virtual Network injection support| Sì | Si applica solo [all'istanza gestita.](sql-database-managed-instance.md) |
| Isolamento della rete e supporto firewall| Sì | Firewall sia a livello di database che a livello di server. L'isolamento rete è solo per [l'istanza gestita.Network isolation](sql-database-managed-instance.md) is for managed instance only. |
| Supporto per il tunneling forzato| Sì | [Istanza gestita](sql-database-managed-instance.md) tramite una VPN [ExpressRoute.Managed](../expressroute/index.yml) instance via an ExpressRoute VPN. |

## <a name="monitoring--logging"></a>Monitoraggio della registrazione dei &

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per il monitoraggio di Azure, ad esempio Log Analytics o Application InsightsAzure monitoring support, such as Log Analytics or Application Insights| Sì | SecureSphere, la soluzione SIEM di Imperva, è supportata anche tramite l'integrazione di [Hub eventi](../event-hubs/index.yml) di Azure tramite il [controllo SQL.](sql-database-auditing.md) |
| Controllo e controllo del piano di controllo e del piano di gestione| Sì | Sì solo per alcuni eventi |
| Registrazione e controllo dei piani dati | Sì | Tramite [l'audit SQL](sql-database-auditing.md) |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Azure Active Directory (Azure AD) |
| Autorizzazione| Sì | nessuno |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server inattivi: chiavi gestite da MicrosoftServer-side encryption at rest: Microsoft-managed keys | Sì | Denominato "crittografia in uso", come descritto nell'articolo [Always Encrypted](sql-database-always-encrypted.md). La crittografia lato server utilizza la [crittografia dei dati trasparente.](transparent-data-encryption-azure-sql.md)|
| Crittografia in transito:<ul><li>Crittografia di Azure ExpressRouteAzure ExpressRoute encryption</li><li>Crittografia in una rete virtualeEncryption in a virtual network</li><li>Crittografia tra reti virtuali</ul>| Sì | Uso di HTTPS. |
| Gestione delle chiavi di crittografia, ad esempio CMK o BYOK| Sì | Viene offerta sia la gestione delle chiavi gestita dal servizio che la gestione delle chiavi gestite dal cliente. Quest'ultimo viene offerto tramite L'insieme di credenziali delle chiavi di [Azure.](../key-vault/index.yml) |
| Crittografia a livello di colonna fornita dai servizi dati di AzureColumn-level encryption provided by Azure data services| Sì | Tramite [Always Encrypted](sql-database-always-encrypted.md). |
| Chiamate API crittografate| Sì | Utilizzo di HTTPS/TLS. |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto della gestione della configurazione, ad esempio il controllo delle versioni della configurazioneConfiguration-management support, such as versioning of configuration| No  | nessuno |

## <a name="additional-security-controls-for-sql-database"></a>Controlli di sicurezza aggiuntivi per il database SQLAdditional security controls for SQL Database

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Prevenzione: valutazione della vulnerabilità | Sì | Vedere [Il servizio SQL Vulnerability Assessment consente](sql-vulnerability-assessment.md)di identificare le vulnerabilità del database. |
| Prevenzione: individuazione e classificazione dei dati  | Sì | Vedere Classificazione & di [individuazione dei dati del database SQL di Azure e del data warehouse SQL](sql-database-data-discovery-and-classification.md). |
| Rilevamento: rilevamento delle minacce | Sì | Vedere Advanced Threat Protection per il database SQL di [Azure.](sql-database-threat-detection-overview.md) |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui controlli di sicurezza incorporati nei servizi di Azure.Learn more about the [built-in security controls across Azure services](../security/fundamentals/security-controls.md).
