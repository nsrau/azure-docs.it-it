---
ms.assetid: 
title: Scenari di sicurezza di Azure Key Vault | Microsoft Docs
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: aeab36153d3a4e004d12206bab92cea9b30400ad
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
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

- [Aree di Azure](https://azure.microsoft.com/regions/)
- [Prodotti Microsoft in base all'area](https://azure.microsoft.com/regions/services/)

Le aree vengono mappate in scenari di sicurezza, visualizzati come intestazioni principali nelle tabelle:

Nei prodotti dell'articolo in base all'area, ad esempio, la scheda **Americhe** contiene Stati Uniti orientali, Stati Uniti centrali, Stati Uniti occidentali, tutti con il mapping eseguito per l'area delle Americhe. 

>[!NOTE]
>Un'eccezione è che il Dipartimento della difesa Stati Uniti orientali e il Dipartimento della difesa Stati Uniti centrali hanno i propri scenari di sicurezza. 

Analogamente, nella scheda **Europa** l'Europa settentrionale e l'Europa occidentale eseguono entrambi il mapping nell'area Europa. Lo stesso vale anche per la scheda **Asia Pacifico**.



