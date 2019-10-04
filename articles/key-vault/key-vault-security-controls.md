---
title: Controlli di sicurezza per Azure Key Vault
description: Elenco di controllo dei controlli di sicurezza per la valutazione Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: d7f92a309a3d0b5d8e85f1e270d5590f46496a77
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886370"
---
# <a name="security-controls-for-azure-key-vault"></a>Controlli di sicurezza per Azure Key Vault

Questo articolo descrive i controlli di sicurezza incorporati in Azure Key Vault. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì | Uso degli endpoint di servizio della rete virtuale (VNet). |
| Supporto di VNet Injection| No |  |
| Isolamento rete e supporto del firewall| Yes | Uso delle regole del firewall di VNet. |
| Supporto del tunneling forzato| No |  |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Viene usato Log Analytics. |
| Piano di gestione/controllo - Registrazione e controllo| Yes | Viene usato Log Analytics. |
| Registrazione e controllo del piano dati| Yes | Viene usato Log Analytics. |

## <a name="identity"></a>identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | L'autenticazione avviene tramite Azure Active Directory. |
| Authorization| Sì | Vengono usati i criteri di accesso dell'insieme di credenziali delle chiavi. |

## <a name="data-protection"></a>Protezione dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server: Chiavi gestite da Microsoft | Sì | Vengono crittografati tutti gli oggetti. |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | Sì | Il cliente controlla tutte le chiavi nel Key Vault. Quando si specificano le chiavi supportate del modulo di protezione hardware (HSM), un HSM di livello 2 di FIPS protegge la chiave, il certificato o il segreto. |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D |  |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Tutte le comunicazioni vengono effettuate tramite chiamate API crittografate |
| Chiamate API crittografate| Yes | Uso di HTTPS. |

## <a name="access-controls"></a>Controlli di accesso

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Piano di gestione/controllo - Controlli di accesso | Sì | Controllo degli accessi in base al ruolo di Azure Resource Manager |
| Piano dati - Controlli di accesso (a ogni livello di servizio) | Yes | Criteri di accesso insieme di credenziali delle chiavi |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).