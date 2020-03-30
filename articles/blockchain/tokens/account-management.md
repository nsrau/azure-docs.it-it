---
title: Gestione account di Azure Blockchain TokensAzure Blockchain Tokens account management
description: Usando la gestione dell'account dei token Blockchain di Azure, è possibile creare gruppi e collegare account blockchain per controllare l'accesso alle azioni blockchain.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74326108"
---
# <a name="azure-blockchain-tokens-account-management"></a>Gestione account di Azure Blockchain TokensAzure Blockchain Tokens account management

[!INCLUDE [Preview note](./includes/preview.md)]

Per una soluzione blockchain, gli utenti potrebbero richiedere diversi livelli di accesso ai token creati con il servizio Token Blockchain di Azure.For a blockchain solution, users may require different levels of access to the tokens that are created with the Azure Blockchain Tokens service. Nella maggior parte degli scenari blockchain, è necessario pianificare e distribuire diversi account blockchain presenti nella contabilità generale. È inoltre necessario gestire l'accesso tra i partecipanti.Usando la gestione dell'account dei token Blockchain di Azure, è possibile creare gruppi e collegare account blockchain per controllare l'accesso alle azioni blockchain.

## <a name="blockchain-networks"></a>Reti Blockchain

I token Blockchain di Azure consentono la distribuzione e la gestione di token in un set di reti blockchain. È possibile collegare un singolo libro mastro blockchain o diversi registri blockchain al servizio.

## <a name="accounts"></a>Account

Per le reti blockchain connesse ai token Blockchain di Azure, il servizio crea e gestisce le coppie di chiavi private-pubbliche dell'account ed esegue la firma e l'invio delle transazioni. I token Blockchain di Azure forniscono anche il mapping delle identità per associare gli account con l'identità della chiave pubblica nella contabilità generale.

## <a name="groups"></a>Gruppi

Gruppi consente di gestire un numero elevato di account blockchain tra reti connesse. È possibile tenere traccia e controllare quali applicazioni e utenti nella directory hanno la possibilità di usare gli account tramite le API dei token Blockchain di Azure.You can track and audit which applications and users in the directory have the ability to use accounts through Azure Blockchain Tokens APIs. Ad esempio, è possibile raggruppare un set di account che rappresentano diverse linee di business o ruoli diversi e l'accesso ai token blockchain.

È anche possibile associare un gruppo a un utente o a un'entità servizio di Azure Active Directory e questa entità dispone delle autorizzazioni per il gruppo e gli account associati.  

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui [modelli di Token di blockchain di Azure](templates.md) disponibili.
