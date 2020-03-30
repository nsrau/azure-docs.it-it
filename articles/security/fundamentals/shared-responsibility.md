---
title: Responsabilità condivisa nel cloud - Microsoft Azure
description: Comprendere il modello di responsabilità condivisa e le attività di sicurezza gestite dal provider di cloud e le attività gestite dall'utente.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: na
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2019
ms.author: terrylan
ms.openlocfilehash: 8f16105d6bda1798828bc423ec8a158d49e0cf2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518369"
---
# <a name="shared-responsibility-in-the-cloud"></a>Responsabilità condivisa nel cloud

Quando si considerano e si valutano i servizi cloud pubblici, è fondamentale comprendere il modello di responsabilità condivisa e le attività di sicurezza gestite dal provider di cloud e le attività gestite dall'utente. Le responsabilità del carico di lavoro variano a seconda che il carico di lavoro sia ospitato in Software as a Service (SaaS), Platform as a Service (PaaS), Infrastructure as a Service (IaaS) o in un data center locale

## <a name="division-of-responsibility"></a>Suddivisione della responsabilità
In un data center locale, si è proprietari dell'intero stack. Come ci si sposta nel cloud alcune responsabilità di trasferimento a Microsoft. Il diagramma seguente illustra le aree di responsabilità tra l'utente e Microsoft, in base al tipo di distribuzione dello stack.

![Aree di responsabilità](./media/shared-responsibility/shared-responsibility.png)

Per tutti i tipi di distribuzione cloud, l'utente è proprietario di dati e identità. È responsabilità dell'utente proteggere tali dati e identità, nonché le risorse locali e i componenti cloud da lui gestiti. Questi ultimi fattori possono variare in base al tipo di servizio.

Indipendentemente dal tipo di distribuzione, le seguenti responsabilità vengono sempre mantenute dall'utente:

- Dati
- Endpoint
- Account
- Gestione degli accessi

## <a name="cloud-security-advantages"></a>Vantaggi della sicurezza cloud
Il cloud offre vantaggi significativi per risolvere problemi di sicurezza delle informazioni di lunga data. In un ambiente locale, le organizzazioni si ritrovano probabilmente con responsabilità non ancora gestite e con risorse limitate disponibili da investire nella sicurezza. Tutto questo porta a un ambiente in cui i malintenzionati sono in grado di sfruttare vulnerabilità a più livelli.

Il diagramma seguente mostra un approccio tradizionale in cui molte responsabilità di sicurezza non vengono soddisfatte a causa delle risorse limitate. Nell'approccio abilitato per il cloud, è possibile trasferire le responsabilità di sicurezza giorno per giorno al provider di cloud e riassegnare le risorse.

![Vantaggi di sicurezza dell'era cloud](./media/shared-responsibility/cloud-enabled-security.png)

Nell'approccio abilitato per il cloud, è anche possibile sfruttare le funzionalità di sicurezza basate sul cloud per una maggiore efficacia e utilizzare l'intelligenza cloud per migliorare il rilevamento delle minacce e i tempi di risposta. Passando le responsabilità al provider di servizi cloud, le organizzazioni possono godere di una sicurezza più ampia e possono così riallocare le relative risorse e i relativi budget ad altre priorità aziendali.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla divisione delle responsabilità tra l'utente e Microsoft in una distribuzione SaaS, PaaS e IaaS, vedere [Responsabilità condivise per](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)il cloud computing .
