---
title: Controlli di sicurezza
titleSuffix: Azure Storage
description: Elenco di controllo dei controlli di sicurezza per la valutazione di Archiviazione di Azure.A checklist of security controls for evaluating Azure Storage.
services: storage
author: msmbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mbaldwin
ms.openlocfilehash: f03f497051367d36bd229a3f358d28a1130ec620
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082357"
---
# <a name="security-controls-for-azure-storage"></a>Controlli di sicurezza per Archiviazione di AzureSecurity controls for Azure Storage

Questo articolo documenta i controlli di sicurezza integrati in Archiviazione di Azure.This article documents the security controls built into Azure Storage.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server inattivi: chiavi gestite da MicrosoftServer-side encryption at rest: Microsoft-managed keys | Sì |  |
| Crittografia lato server inattivi: chiavi gestite dal cliente (BYOK)Server-side encryption at rest: customer-managed keys (BYOK) | Sì | Vedere [Crittografia del servizio di archiviazione usando chiavi gestite dal cliente in Archiviazione delle chiavi di Azure.See Storage Service Encryption using customer-managed keys in Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Crittografia a livello di colonna (Azure Data Services)Column level encryption (Azure Data Services)| N/D |  |
| Crittografia in transito (ad esempio la crittografia ExpressRoute, nella crittografia della rete virtuale e crittografia VNet-VNet)Encryption in transit (such as ExpressRoute encryption, in VNet encryption, and VNet-VNet encryption)| Sì | Supporta i meccanismi HTTPS/TLS standard.  Gli utenti possono anche crittografare i dati prima che vengano trasmessi al servizio. |
| Chiamate API crittografate| Sì |  |

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto dell'endpoint di servizioService endpoint support| Sì |  |
| Supporto dei tag di servizio| Sì | Per altre informazioni sui tag di servizio supportati da Archiviazione di Azure, vedere [Panoramica](../../virtual-network/service-tags-overview.md) dei tag di servizio di Azure.See Azure service tags overview for more information about service tags supported by Azure Storage. |
| Supporto per l'inserimento di vNet| N/D |  |
| Isolamento della rete e supporto firewall| Sì | |
| Supporto per il tunneling forzato| N/D |  |

## <a name="monitoring--logging"></a>Monitoraggio della registrazione dei &

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per il monitoraggio di Azure (analisi dei log, informazioni dettagliate sulle app e così via)Azure monitoring support (Log analytics, App insights, etc.)| Sì | Metriche di Monitoraggio di AzureAzure Monitor Metrics|
| Controllo e gestione del controllo e dell'audit degli aerei | Sì | Azure Resource Manager Activity Log |
| Registrazione e controllo dei piani dati| Sì | Registri diagnostici del servizio.|

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Azure Active Directory, Chiave condivisa, Token di accesso condiviso. |
| Autorizzazione| Sì | Autorizzazione di supporto tramite RBAC, ACL POSIX e token di firma di accesso condivisoSupport Authorization via RBAC, POSIX ACLs, and SAS Tokens |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Supportare il controllo delle versioni del provider di risorse tramite le API di Azure Resource ManagerSupport Resource Provider versioning through Azure Resource Manager APIs |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui controlli di sicurezza incorporati nei servizi di Azure.Learn more about the [built-in security controls across Azure services](../../security/fundamentals/security-controls.md).