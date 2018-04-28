---
title: Cos'è Azure Stack? | Microsoft Docs
description: Stack di Azure consente di eseguire servizi di Azure nel Data Center.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: d54f392eddfcca99e7fe0b037b9efd0a4e90433c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-azure-stack"></a>Cos'è Azure Stack?

Stack di Microsoft Azure è una piattaforma di cloud ibrido che consente di fornire servizi di Azure dal Data Center dell'organizzazione.  Stack di Azure è progettato per abilitare i nuovi scenari per le moderne applicazioni negli scenari chiave, ad esempio il bordo e ambienti disconnessi o sicurezza e conformità requisiti specifici.  Stack di Azure è disponibile in due opzioni di distribuzione in base alle esigenze.

## <a name="azure-stack-integrated-systems"></a>Sistemi integrati di Azure Stack
Stack di sistemi integrati vengono offerti tramite una relazione di Microsoft Azure e [partner hardware](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), creazione di una soluzione che offre innovazione paced cloud bilanciato con semplicità di gestione.  Poiché Azure Stack viene offerto come un sistema integrato di hardware e software, viene offerta la giusta quantità di flessibilità e controllo, mentre ancora adozione innovazione dal cloud.  Azure Stack integrati sistemi intervallo dimensioni da nodi di 4-12 e congiuntamente sono supportati dal partner hardware Microsoft.  Usare sistemi Azure Stack integrato per abilitare i nuovi scenari per i carichi di lavoro di produzione.    

## <a name="azure-stack-development-kit"></a>Azure Stack Development Kit
Microsoft [Azure Stack Development Kit (ASDK)](.\asdk\asdk-what-is.md) è una distribuzione a nodo singolo dello Stack di Azure, è possibile utilizzare per valutare e acquisire informazioni sullo Stack di Azure.  È anche possibile utilizzare il ASDK come un ambiente di sviluppo in cui è possibile sviluppare usando le API e strumenti coerenti con Azure. Il ASDK non dovrà essere utilizzato come un ambiente di produzione.

Il ASDK presenta le limitazioni seguenti:
* ASDK viene associato a un singolo Azure Active Directory (Azure AD) o un provider di identità di Active Directory Federation Services (ADFS). È possibile creare più utenti in questa directory e assegnare sottoscrizioni a ogni utente.
* Essendo tutti i componenti distribuiti in un singolo computer, sono disponibili risorse fisiche limitate per le risorse del tenant. Questa configurazione non è destinata la valutazione di scalabilità o prestazioni.
* Gli scenari di rete sono limitati a causa del requisito relativo a un singolo host o una singola scheda di interfaccia di rete.  

## <a name="next-steps"></a>Passaggi successivi
[Funzionalità e concetti principali](azure-stack-key-features.md)

[Stack di Azure: Un'estensione di Azure (pdf)](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)

