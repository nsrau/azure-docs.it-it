---
title: Attributi di sicurezza comuni per Azure Key Vault
description: Un elenco di controllo degli attributi di sicurezza comuni per la valutazione di Azure Key Vault
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 3ccfc38136ba3e8ec7c6130658032b7565988e5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60461425"
---
# <a name="common-security-attributes-for-azure-key-vault"></a>Attributi di sicurezza comuni per Azure Key Vault

La sicurezza è integrata in ogni aspetto di un servizio di Azure. Questo articolo sono descritti gli attributi di sicurezza comuni incorporati in Azure Key Vault. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Sì | Vengono crittografati tutti gli oggetti. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Crittografia nella rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Sì | Tutte le comunicazioni vengono effettuate tramite chiamate API crittografate |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| Sì | Il cliente controlla tutte le chiavi nel loro insieme di credenziali delle chiavi. Quando vengono specificate le chiavi module (HSM) supportata di protezione hardware, un modulo di protezione hardware di FIPS livello 2 consente di proteggere la chiave, un certificato o un segreto. |
| Crittografia a livello di colonna (Azure Data Services)| N/D |  |
| Chiamate API crittografate| Sì | Viene usato HTTPS. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| Sì | Vengono usati endpoint di servizio di rete virtuale. |
| supportare l'inserimento di rete virtuale| No  |  |
| Isolamento di rete e supporto firewall| Sì | Vengono usate le regole del firewall della rete virtuale. |
| Supporto di tunneling forzato| No  |  |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Sì | Viene usato Log Analytics. |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | L'autenticazione avviene tramite Azure Active Directory. |
| Authorization| Sì | Vengono usati i criteri di accesso dell'insieme di credenziali delle chiavi. |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Piano di gestione/controllo - Registrazione e controllo| Sì | Viene usato Log Analytics. |
| Controllo e registrazione di piano dati| Sì | Viene usato Log Analytics. |

## <a name="access-controls"></a>Controlli di accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Piano di gestione/controllo - Controlli di accesso | Sì | Controllo degli accessi in base al ruolo di Azure Resource Manager |
| Piano dati - Controlli di accesso (a ogni livello di servizio) | Sì | Criteri di accesso insieme di credenziali delle chiavi |