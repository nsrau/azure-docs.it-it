---
title: Informazioni sui token di Azure blockchain
description: I token blockchain di Azure sono una piattaforma distribuita come servizio (PaaS) per il rilascio e la gestione dei token.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: d04c4202f89e58185465669c3da0f7d5e4d7b9bb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518645"
---
# <a name="what-is-azure-blockchain-tokens"></a>Che cosa sono i token blockchain di Azure?

[!INCLUDE [Preview note](./includes/preview.md)]

I token blockchain di Azure sono una piattaforma distribuita come servizio (PaaS) per il rilascio e la gestione di token standardizzati tra i registri di blockchain in Azure.

Usando i token di Azure blockchain è possibile creare token standardizzati per la soluzione blockchain usando un modello di token predefinito. È anche possibile comporre un modello di token personalizzato usando il servizio. Una volta creati, usare i token blockchain di Azure per connettersi ed emettere i token in un blockchain. Una volta emesso, è possibile gestire i token tra più reti blockchain.

## <a name="templates"></a>Modelli

Usare i token blockchain di Azure per selezionare un modello di token predefinito o creare un modello di token personalizzato. I token blockchain di Azure supportano la componibilità dei modelli di token che consente di creare un modello di token personalizzato basato sui comportamenti supportati. I modelli di token possono essere usati per la maggior parte delle soluzioni blockchain poiché eseguono il mapping ai token usati più di frequente. È possibile iniziare con un modello, personalizzarlo e distribuire i token per la soluzione.

Per altre informazioni sui modelli di token blockchain di Azure, vedere [modelli di token blockchain di Azure](templates.md).

## <a name="management"></a>gestione

I token blockchain di Azure forniscono la gestione portale di Azure e le API per connettersi a una rete blockchain esistente. Attualmente, è possibile connettersi al [servizio blockchain di Azure](../service/overview.md) o a un'altra famiglia Ethereum blockchain.

Una volta connessi a una o più reti blockchain, è possibile usare le API dei token blockchain di Azure per rilasciare e gestire i token da usare nella soluzione blockchain. Utilizzando le API, è possibile integrare la gestione dei token nelle applicazioni e nella logica aziendali. Ad esempio, è possibile usare l'API REST per gestire i token anziché gestire i token direttamente nel blockchain.

## <a name="blockchains-and-accounts"></a>Blockchain e account

I token blockchain di Azure forniscono gestione e API portale di Azure per creare nuovi gruppi e nuovi account blockchain in reti blockchain connesse. È possibile creare nuovi account direttamente nelle reti connesse e i token di Azure blockchain gestiscono le chiavi private dell'account per conto dell'utente. Usando i gruppi, è possibile raggruppare diversi account blockchain da più reti e gestire il controllo di accesso tramite i gruppi.

Per altre informazioni sulla gestione degli account dei token blockchain di Azure, vedere Gestione degli account per i [token blockchain di Azure](account-management.md).

## <a name="token-taxonomy-framework"></a>Framework tassonomia token

I token blockchain di Azure si basano su una base basata su standard denominata token Tassonomiy Framework (TTF). TTF è un set di risultati finali creati dal gruppo di lavoro token [Tassonomia Initiative](https://entethalliance.org/participate/token-taxonomy-initiative/) (TTI). Il gruppo di lavoro TTI definisce una tassonomia aziendale per i token e i relativi comportamenti che possono essere applicati a tutti i principali Ledger, tra cui Ethereum, quorum, corda e infrastruttura iperledger. L'obiettivo del gruppo di lavoro è creare un Framework che standardizza l'uso di token da una prospettiva aziendale per favorire la semplificazione e la democratizzazione dello sviluppo basato su token. Consentendo al settore di definire questi token e il relativo comportamento a livello aziendale, l'implementazione dettagliata dei token viene sottratta dalla logica di business che modifica i token.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui [modelli di token blockchain di Azure](templates.md)disponibili.
