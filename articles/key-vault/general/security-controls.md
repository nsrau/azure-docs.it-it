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
ms.openlocfilehash: cd6602f68b63e2c236e7f3905d33b88fbda36ed2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81429863"
---
# <a name="security-controls-for-azure-key-vault"></a>Controlli di sicurezza per Azure Key Vault

Questo articolo descrive i controlli di sicurezza incorporati in Azure Key Vault. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint di servizio| Sì | Uso degli endpoint di servizio della rete virtuale (VNet). |
| Supporto aggiunta rete virtuale| No |  |
| Isolamento rete e supporto del firewall| Sì | Uso delle regole del firewall di VNet. |
| Supporto del tunneling forzato| No |  |

## <a name="monitoring--logging"></a>Monitoraggio e registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto monitoraggio di Azure (analisi dei log, analisi approfondita dell'app e così via)| Sì | Viene usato Log Analytics. |
| Piano di gestione/controllo - Registrazione e controllo| Sì | Viene usato Log Analytics. |
| Piano dati - Registrazione e controllo| Sì | Viene usato Log Analytics. |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | L'autenticazione avviene tramite Azure Active Directory. |
| Autorizzazione| Sì | Vengono usati i criteri di accesso dell'insieme di credenziali delle chiavi. |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server dei dati inattivi: chiavi gestite da Microsoft | Sì | Vengono crittografati tutti gli oggetti. |
| Crittografia lato server dei dati inattivi: chiavi gestite dal cliente (BYOK) | Sì | Il cliente controlla tutte le chiavi nel Key Vault. Quando si specificano le chiavi supportate del modulo di protezione hardware (HSM), un HSM di livello 2 di FIPS protegge la chiave, il certificato o il segreto. |
| Crittografia a livello di colonna (Servizi dati di Azure)| N/D |  |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Tutte le comunicazioni vengono effettuate tramite chiamate API crittografate |
| Chiamate API crittografate| Sì | Uso di HTTPS. |

## <a name="access-controls"></a>Controlli di accesso

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Piano di gestione/controllo - Controlli di accesso | Sì | Controllo degli accessi in base al ruolo di Azure Resource Manager |
| Piano dati - Controlli di accesso (a ogni livello di servizio) | Sì | Criteri di accesso insieme di credenziali delle chiavi |

## <a name="next-steps"></a>Passaggi successivi

- Maggiori informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../../security/fundamentals/security-controls.md).