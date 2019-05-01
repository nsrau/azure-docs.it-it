---
title: Attributi di sicurezza comuni per l'inoltro del Bus di servizio di Azure
description: Un elenco di controllo di attributi di sicurezza comuni per la valutazione di inoltro del Bus di servizio di Azure
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: f8827ac290393c9f394c3b13149555a1a2aa6df9
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927493"
---
# <a name="common-security-attributes-for-azure-service-bus-relay"></a>Attributi di sicurezza comuni per l'inoltro del Bus di servizio di Azure

La sicurezza è integrata in ogni aspetto di un servizio di Azure. Questo articolo sono descritti gli attributi comuni di sicurezza integrati di inoltro del Bus di servizio di Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>|  N/D | Inoltro è un web socket e non rende persistenti i dati. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Crittografia nella rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Sì | Il servizio richiede TLS. |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| No  | Utilizza solo certificati TLS Microsoft.  |
| Crittografia a livello di colonna (Azure Data Services)| N/D | |
| Chiamate API crittografate| Sì | HTTPS. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| No  |  |
| Isolamento di rete e supporto firewall| No  |  |
| Supporto di tunneling forzato| N/D | Endpoint di inoltro è il tunnel TLS  |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Sì | |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Via SAS. |
| Authorization|  Sì | Via SAS. |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Sì | Attraverso [Azure Resource Manager](../azure-resource-manager/index.yml). |
| Controllo e registrazione di piano dati| Sì | Esito positivo di connessione / errore e gli errori e registrati.  |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Sì | Attraverso [Azure Resource Manager](../azure-resource-manager/index.yml).|
