---
title: Cos'è Azure Stack? | Microsoft Docs
description: Azure Stack consente di eseguire servizi di Azure nel data center.
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
ms.date: 02/25/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.lastreviewed: 02/25/2019
ms.openlocfilehash: 65894ccd9514bce1d429b336f8bd5e6674048e65
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820264"
---
# <a name="what-is-azure-stack"></a>Cos'è Azure Stack?

Microsoft Azure Stack è una piattaforma di cloud ibrido che consente di offrire servizi di Azure nel data center. Questa piattaforma è progettata per supportare requisiti aziendali in continua evoluzione. Azure Stack può abilitare nuovi scenari per le applicazioni moderne, ad esempio ambienti perimetrali e disconnessi, oppure soddisfare specifici requisiti di sicurezza e conformità.

Azure Stack è disponibile in due opzioni di distribuzione per le diverse esigenze.


## <a name="azure-stack-development-kit"></a>Azure Stack Development Kit

Microsoft [Azure Stack Development Kit (ASDK)](./asdk/asdk-what-is.md) è una distribuzione a nodo singolo di Azure Stack che può essere usata per valutare e apprendere Azure Stack.  È anche possibile usare il ASDK come un ambiente di sviluppo per compilare le app usando le API e strumenti che è coerente con Azure.

>[!Note]
>Il ASDK non dovrà essere utilizzato come ambiente di produzione.

Il ASDK presenta le limitazioni seguenti:

* Il ASDK è associato a un singolo Azure Active Directory (Azure AD) o provider di identità di Active Directory Federation Services (ADFS). È possibile creare più utenti in questa directory e assegnare sottoscrizioni a ogni utente.
* Poiché i componenti dello Stack di Azure vengono distribuiti in un singolo computer host, sono disponibili risorse fisiche limitate per le risorse del tenant. Questa configurazione non è destinata alla scala o per la valutazione delle prestazioni.
* Scenari di rete sono limitati a causa il singolo host e i requisiti di distribuzione di interfaccia di rete.

## <a name="azure-stack-integrated-systems"></a>Sistemi integrati di Azure Stack
I sistemi integrati di Azure Stack vengono offerti grazie alla collaborazione tra Microsoft e [partner hardware](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), creando una soluzione che offre innovazione per il cloud e semplicità della gestione del calcolo. Poiché Azure Stack viene offerto come sistema di software e hardware integrato, si hanno la flessibilità e il controllo necessari, nonché la possibilità di apportare innovazione dal cloud. Sistemi Azure Stack integrati dimensioni variano da 4 a 16 nodi e congiuntamente supportati dai partner hardware e Microsoft.  Usare i sistemi integrati di Azure Stack per creare nuovi scenari e distribuire nuove soluzioni per i carichi di lavoro di produzione.

## <a name="next-steps"></a>Passaggi successivi

[Funzionalità e concetti principali](azure-stack-key-features.md)

[Azure Stack: Un'estensione di Azure (pdf)](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)
