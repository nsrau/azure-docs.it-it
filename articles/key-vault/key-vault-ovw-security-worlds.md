---
title: Scenari di sicurezza di Azure Key Vault | Microsoft Docs
description: L'insieme di credenziali delle chiavi di Azure è un servizio multi-tenant. Usa un pool di HSM in ogni posizione di Azure.It uses a pool of HSMs in each Azure location. Tutte le posizioni in un'area geografica condividono un limite crittografico.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 07/03/2017
ms.openlocfilehash: 3584f83c5e1a5e83d069373395227b70c084eae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457390"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Scenari di sicurezza di Azure Key Vault e confini geografici

Azure Key Vault è un servizio multi-tenant e usa un pool di moduli di protezione Hardware (HSM) in ogni posizione di Azure. 

Tutti i moduli di protezione hardware nelle posizioni di Azure nella stessa area geografica condividono lo stesso limite di crittografia (ambiente di sicurezza Thales). Ad esempio, gli Stati Uniti orientali e gli Stati Uniti occidentali condividono lo stesso scenario di sicurezza perché appartengono alla posizione geografica degli Stati Uniti. Analogamente, tutte le posizioni di Azure in Giappone condividono lo stesso scenario di sicurezza e tutte le posizioni di Azure in Australia, in India e così via. 

## <a name="backup-and-restore-behavior"></a>Comportamento di backup e ripristino

Un backup di una chiave presa da un insieme di credenziali della chiave in una posizione di Azure può essere ripristinato in un insieme di credenziali della chiave in un altra posizione di Azure, purché si verifichino entrambe le condizioni seguenti:

- Entrambe le posizioni di Azure appartengono alla stessa posizione geografica
- Entrambi gli insiemi di credenziali della chiave appartengono alla stessa sottoscrizione di Azure

Ad esempio, un backup preso da una sottoscrizione data di una chiave in un insieme di credenziali della chiave in India occidentale, può essere ripristinato solo per un altro insieme di credenziali della chiave nella stessa sottoscrizione e località geografica: India occidentale, India centrale o India meridionale.

## <a name="regions-and-products"></a>Aree e prodotti

- [Aree di AzureAzure regions](https://azure.microsoft.com/regions/)
- [Prodotti Microsoft in base all'area](https://azure.microsoft.com/regions/services/)

Le aree vengono mappate in scenari di sicurezza, visualizzati come intestazioni principali nelle tabelle:

Nei prodotti dell'articolo in base all'area, ad esempio, la scheda **Americhe** contiene Stati Uniti orientali, Stati Uniti centrali, Stati Uniti occidentali, tutti con il mapping eseguito per l'area delle Americhe. 

>[!NOTE]
>Un'eccezione è che il Dipartimento della difesa Stati Uniti orientali e il Dipartimento della difesa Stati Uniti centrali hanno i propri scenari di sicurezza. 

Analogamente, nella scheda **Europa** l'Europa settentrionale e l'Europa occidentale eseguono entrambi il mapping nell'area Europa. Lo stesso vale anche per la scheda **Asia Pacifico**.



