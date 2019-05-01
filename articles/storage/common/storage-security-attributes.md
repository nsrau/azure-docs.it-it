---
title: Attributi di sicurezza comuni per l'archiviazione di Azure
description: Un elenco di controllo di attributi di sicurezza comuni per la valutazione di archiviazione di Azure
services: storage
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 7868b52fee991d4b9323fa0b7969aeca4dc83cdb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64711960"
---
# <a name="common-security-attributes-for-azure-storage"></a>Attributi di sicurezza comuni per l'archiviazione di Azure

La sicurezza è integrata in ogni aspetto di un servizio di Azure. Questo articolo sono descritti gli attributi di sicurezza comuni integrati in archiviazione di Azure. 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Sì |  |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Sì | Supporto di meccanismi standard HTTPS/TLS.  Gli utenti possono anche crittografare i dati prima di essere trasmesso al servizio. |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| Sì | Visualizzare [crittografia del servizio di archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Crittografia a livello di colonna (Azure Data Services)| N/D |  |
| Chiamate API crittografate| Sì |  |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| Sì |  |
| Supportare l'inserimento di rete virtuale| N/D |  |
| Isolamento di rete e supporto firewall| Sì | |
| Supporto di tunneling forzato| N/D |  |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Sì | Metriche di monitoraggio di Azure disponibili a questo punto, i log di anteprima inizia |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Azure Active Directory, chiave condivisa, il token di accesso condiviso. |
| Authorization| Sì | Autorizzazione di supporto tramite RBAC, POSIX ACL e i token di firma di accesso condiviso |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo | Sì | Log attività di Azure Resource Manager |
| Controllo e registrazione di piano dati| Sì | I log di diagnostica del servizio e anteprima di partenza la registrazione di monitoraggio di Azure  |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Sì | Supporta il controllo delle versioni del Provider di risorse tramite le API di Azure Resource Manager |