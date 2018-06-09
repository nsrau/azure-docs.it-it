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
ms.date: 06/04/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 848df59b01cc0c03b9a5f3dae2644d469c8651bb
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234887"
---
# <a name="what-is-azure-stack"></a>Cos'è Azure Stack?

Microsoft Azure Stack è una piattaforma di cloud ibrido che consente di offrire servizi di Azure nel Data Center. Questa piattaforma è progettata per supportare i requisiti aziendali in continua evoluzione. Abilitano nuovi scenari per le applicazioni moderne, ad esempio edge e ambienti disconnessi, Stack di Azure o soddisfare specifici requisiti di sicurezza e conformità.

Stack di Azure è disponibile in due opzioni di distribuzione in base alle esigenze.

## <a name="azure-stack-integrated-systems"></a>Sistemi integrati di Azure Stack
Stack di sistemi integrati vengono offerti tramite una relazione di Microsoft Azure e [partner hardware](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), creazione di una soluzione che offre innovazione paced cloud e semplicità di gestione di elaborazione. Poiché Azure Stack viene offerto come un sistema di software e hardware integrata, si ha la flessibilità e controllo del codice che è necessario, nonché la possibilità di innovazione dal cloud. Azure Stack integrati sistemi intervallo dimensioni da nodi di 4-12 e congiuntamente sono supportati dal partner hardware Microsoft.  Usare sistemi Azure Stack integrato per creare nuovi scenari e distribuire nuove soluzioni per i carichi di lavoro di produzione.

## <a name="azure-stack-development-kit"></a>Azure Stack Development Kit

Microsoft [Azure Stack Development Kit (ASDK)](.\asdk\asdk-what-is.md) è una distribuzione a nodo singolo dello Stack di Azure, che è possibile utilizzare per valutare e acquisire informazioni sullo Stack di Azure.  È inoltre possibile utilizzare ASDK come un ambiente di sviluppo per compilare le app usando le API e strumenti che è coerente con Azure.

>[!Note]
>Il ASDK non dovrà essere utilizzato come un ambiente di produzione.

Il ASDK presenta le limitazioni seguenti:

* ASDK viene associato a un singolo Azure Active Directory (Azure AD) o un provider di identità di Active Directory Federation Services (ADFS). È possibile creare più utenti in questa directory e assegnare sottoscrizioni a ogni utente.
* Poiché i componenti dello Stack di Azure vengono distribuiti in un computer host, sono disponibili limitato delle risorse fisiche per le risorse tenant. Questa configurazione non è destinata alla valutazione di scalabilità o prestazioni.
* Scenari di rete sono limitati a causa della singolo host e i requisiti di distribuzione di scheda di rete.

## <a name="next-steps"></a>Passaggi successivi

- [Funzionalità e concetti principali](azure-stack-key-features.md)
- [Stack di Azure: Un'estensione di Azure (pdf)](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)
