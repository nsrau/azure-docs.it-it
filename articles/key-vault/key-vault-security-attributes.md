---
title: Attributi di sicurezza per Azure Key Vault
description: Elenco di controllo degli attributi di sicurezza per la valutazione Azure Key Vault
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 836d025c5bc69da9606c9a6172ac6a43caaaf29b
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444010"
---
# <a name="security-attributes-for-azure-key-vault"></a>Attributi di sicurezza per Azure Key Vault

Questo articolo descrive gli attributi di sicurezza incorporati in Azure Key Vault. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia| Sì | Vengono crittografati tutti gli oggetti. |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Tutte le comunicazioni vengono effettuate tramite chiamate API crittografate |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| Sì | Il cliente controlla tutte le chiavi nel Key Vault. Quando si specificano le chiavi supportate del modulo di protezione hardware (HSM), un HSM di livello 2 di FIPS protegge la chiave, il certificato o il segreto. |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D |  |
| Chiamate API crittografate| Sì | Uso di HTTPS. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì | Uso degli endpoint di servizio della rete virtuale (VNet). |
| Supporto di VNet Injection| No |  |
| Isolamento rete e supporto del firewall| Yes | Uso delle regole del firewall di VNet. |
| Supporto del tunneling forzato| No |  |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Viene usato Log Analytics. |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | L'autenticazione avviene tramite Azure Active Directory. |
| Authorization| Sì | Vengono usati i criteri di accesso dell'insieme di credenziali delle chiavi. |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Piano di gestione/controllo - Registrazione e controllo| Yes | Viene usato Log Analytics. |
| Registrazione e controllo del piano dati| Sì | Viene usato Log Analytics. |

## <a name="access-controls"></a>Controlli di accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Piano di gestione/controllo - Controlli di accesso | Yes | Controllo degli accessi in base al ruolo di Azure Resource Manager |
| Piano dati - Controlli di accesso (a ogni livello di servizio) | Yes | Criteri di accesso insieme di credenziali delle chiavi |