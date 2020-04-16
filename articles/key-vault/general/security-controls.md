---
title: Controlli di sicurezza per l'insieme di credenziali delle chiavi di AzureSecurity controls for Azure Key Vault
description: Elenco di controllo dei controlli di sicurezza per la valutazione dell'insieme di credenziali delle chiavi di Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: cd6602f68b63e2c236e7f3905d33b88fbda36ed2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429863"
---
# <a name="security-controls-for-azure-key-vault"></a>Controlli di sicurezza per l'insieme di credenziali delle chiavi di AzureSecurity controls for Azure Key Vault

Questo articolo documenta i controlli di sicurezza integrati in Archiviazione delle chiavi di Azure.This article documents the security controls built into Azure Key Vault. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto dell'endpoint di servizioService endpoint support| Sì | Utilizzo di endpoint del servizio di rete virtuale (VNet). |
| Supporto per l'inserimento di vNet| No |  |
| Isolamento della rete e supporto firewall| Sì | Utilizzo delle regole del firewall della rete virtuale. |
| Supporto per il tunneling forzato| No |  |

## <a name="monitoring--logging"></a>Monitoraggio della registrazione dei &

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per il monitoraggio di Azure (analisi dei log, informazioni dettagliate sulle app e così via)Azure monitoring support (Log analytics, App insights, etc.)| Sì | Viene usato Log Analytics. |
| Piano di gestione/controllo - Registrazione e controllo| Sì | Viene usato Log Analytics. |
| Registrazione e controllo dei piani dati| Sì | Viene usato Log Analytics. |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | L'autenticazione avviene tramite Azure Active Directory. |
| Autorizzazione| Sì | Vengono usati i criteri di accesso dell'insieme di credenziali delle chiavi. |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server inattivi: chiavi gestite da MicrosoftServer-side encryption at rest: Microsoft-managed keys | Sì | Vengono crittografati tutti gli oggetti. |
| Crittografia lato server inattivi: chiavi gestite dal cliente (BYOK)Server-side encryption at rest: customer-managed keys (BYOK) | Sì | Il cliente controlla tutte le chiavi nell'insieme di credenziali delle chiavi. Quando vengono specificate chiavi supportate HSM (Hardware Security Module), un HSM FIPS di livello 2 protegge la chiave, il certificato o il segreto. |
| Crittografia a livello di colonna (Azure Data Services)Column level encryption (Azure Data Services)| N/D |  |
| Crittografia in transito (ad esempio la crittografia ExpressRoute, nella crittografia della rete virtuale e crittografia VNet-VNet)Encryption in transit (such as ExpressRoute encryption, in VNet encryption, and VNet-VNet encryption)| Sì | Tutte le comunicazioni vengono effettuate tramite chiamate API crittografate |
| Chiamate API crittografate| Sì | Uso di HTTPS. |

## <a name="access-controls"></a>Controlli di accesso

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Piano di gestione/controllo - Controlli di accesso | Sì | Controllo degli accessi in base al ruolo di Azure Resource Manager |
| Piano dati - Controlli di accesso (a ogni livello di servizio) | Sì | Criteri di accesso insieme di credenziali delle chiavi |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui controlli di sicurezza incorporati nei servizi di Azure.Learn more about the [built-in security controls across Azure services](../../security/fundamentals/security-controls.md).