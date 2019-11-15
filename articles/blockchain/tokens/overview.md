---
title: Che cos'è il servizio Token di blockchain di Azure?
description: Token di blockchain di Azure è una piattaforma distribuita come servizio (PaaS) per l'emissione e la gestione di token.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: cd41d52e06a5c1833dca9669881cbe48f362d81d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579759"
---
# <a name="what-is-azure-blockchain-tokens"></a>Che cos'è il servizio Token di blockchain di Azure?

[!INCLUDE [Preview note](./includes/preview.md)]

Token di blockchain di Azure è una piattaforma distribuita come servizio (PaaS) per l'emissione e la gestione di token standardizzati nei libri mastri della blockchain in Azure.

Usando Token di blockchain di Azure è possibile creare token standardizzati per la soluzione blockchain usando un modello di token predefinito. È anche possibile comporre un modello di token personalizzato usando il servizio. Dopo la creazione, usare Token di blockchain di Azure per connettere ed emettere i token in una blockchain. Una volta emessi, i token possono essere gestiti tra più reti di blockchain.

## <a name="templates"></a>Modelli

Usare Token di blockchain di Azure per selezionare un modello di token predefinito o crearne uno personalizzato. Il servizio Token di blockchain di Azure supporta la componibilità dei modelli di token, che consente di creare un modello di token personalizzato basato sui comportamenti supportati. I modelli di token possono essere usati per la maggior parte delle soluzioni blockchain, perché si abbinano alla maggior parte dei token di utilizzo comune. È possibile iniziare con un modello, personalizzarlo e distribuire i token per la soluzione.

Per altre informazioni sui modelli di Token di blockchain di Azure, vedere [Modelli di Token di blockchain di Azure](templates.md).

## <a name="management"></a>Gestione

Token di blockchain di Azure fornisce funzionalità di gestione e API del portale di Azure per connettersi a una rete di blockchain esistente. Attualmente è possibile connettersi al [servizio Azure Blockchain](../service/overview.md) o a un'altra blockchain della famiglia Ethereum.

Una volta connesse a una o più reti di blockchain, le API di Token di blockchain di Azure possono essere usate per emettere e gestire i token per l'uso nella soluzione blockchain. Usando le API, è possibile integrare la gestione dei token nelle applicazioni e nella logica di business. Ad esempio, è possibile gestire i token con l'API REST invece che direttamente nella blockchain.

## <a name="blockchains-and-accounts"></a>Blockchain e account

Token di blockchain di Azure fornisce funzionalità di gestione e API del portale di Azure per creare nuovi gruppi e nuovi account di blockchain nelle reti di blockchain connesse. È possibile creare nuovi account direttamente nelle reti connesse, quindi Token di blockchain di Azure gestirà automaticamente le chiavi private dell'account. Usando i gruppi, è possibile raggruppare account di blockchain diversi di più reti e gestire il controllo di accesso tramite i gruppi.

Per altre informazioni sulla gestione degli account di Token di blockchain di Azure, vedere [Gestione degli account di Token di blockchain di Azure](account-management.md).

## <a name="token-taxonomy-framework"></a>Token Taxonomy Framework

Token di blockchain di Azure è basato su un framework standard, Token Taxonomy Framework (TTF). TTF è un set di materiali creati dal gruppo di lavoro per i token TTI ([Token Taxonomy Initiative](https://entethalliance.org/participate/token-taxonomy-initiative/)). Il gruppo di lavoro TTI definisce una tassonomia aziendale per i token e i relativi comportamenti che possono essere applicati a tutti i principali libri mastri, tra cui Ethereum, Quorum, Corda e Hyperledger Fabric. L'obiettivo del gruppo di lavoro è creare un framework che standardizzi l'uso di token da una prospettiva aziendale per favorire la semplificazione e la democratizzazione dello sviluppo basato su token. Consentendo al settore di definire questi token e il relativo comportamento a livello aziendale, l'implementazione dettagliata dei token viene sottratta dalla logica di business che modifica i token.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui [modelli di Token di blockchain di Azure](templates.md) disponibili.
