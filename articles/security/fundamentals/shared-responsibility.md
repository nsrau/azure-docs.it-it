---
title: Responsabilità condivisa nel cloud-Microsoft Azure
description: Comprendere il modello di responsabilità condivisa e le attività di sicurezza gestite dal provider di servizi cloud e le attività gestite dall'utente.
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
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518369"
---
# <a name="shared-responsibility-in-the-cloud"></a>Responsabilità condivisa nel cloud

Quando si considerano e si valutano i servizi cloud pubblici, è fondamentale comprendere il modello di responsabilità condivisa e le attività di sicurezza gestite dal provider di servizi cloud e quali attività vengono gestite dall'utente. Le responsabilità del carico di lavoro variano a seconda che il carico di lavoro sia ospitato in SaaS (software as a Service), piattaforma distribuita come servizio (PaaS), infrastruttura distribuita come servizio (IaaS) o in un Data Center locale

## <a name="division-of-responsibility"></a>Suddivisione della responsabilità
In un Data Center locale si è proprietari dell'intero stack. Quando si passa al cloud, alcune responsabilità vengono trasferite a Microsoft. Il diagramma seguente illustra le aree di responsabilità tra l'utente e Microsoft, in base al tipo di distribuzione dello stack.

![Aree di responsabilità](./media/shared-responsibility/shared-responsibility.png)

Per tutti i tipi di distribuzione cloud, l'utente è proprietario di dati e identità. È responsabilità dell'utente proteggere tali dati e identità, nonché le risorse locali e i componenti cloud da lui gestiti. Questi ultimi fattori possono variare in base al tipo di servizio.

Indipendentemente dal tipo di distribuzione, le responsabilità seguenti vengono sempre conservate dall'utente:

- Dati
- Endpoint
- Account
- gestione degli accessi

## <a name="cloud-security-advantages"></a>Vantaggi della sicurezza cloud
Il cloud offre vantaggi significativi per la risoluzione di problemi di sicurezza delle informazioni a lungo termine. In un ambiente locale, le organizzazioni si ritrovano probabilmente con responsabilità non ancora gestite e con risorse limitate disponibili da investire nella sicurezza. Tutto questo porta a un ambiente in cui i malintenzionati sono in grado di sfruttare vulnerabilità a più livelli.

Il diagramma seguente illustra un approccio tradizionale in cui molte responsabilità di sicurezza sono soddisfatte a causa di risorse limitate. Nell'approccio abilitato per il cloud è possibile spostare le responsabilità di sicurezza quotidiane per il provider di servizi cloud e riallocare le risorse.

![Vantaggi di sicurezza dell'era cloud](./media/shared-responsibility/cloud-enabled-security.png)

Nell'approccio abilitato per il cloud, è anche possibile sfruttare le funzionalità di sicurezza basate sul cloud per una maggiore efficacia e usare l'Intelligence per il cloud per migliorare il rilevamento delle minacce e il tempo di risposta. Passando le responsabilità al provider di servizi cloud, le organizzazioni possono godere di una sicurezza più ampia e possono così riallocare le relative risorse e i relativi budget ad altre priorità aziendali.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla divisione di responsabilità tra l'utente e Microsoft in una distribuzione SaaS, PaaS e IaaS, vedere [responsabilità condivise per cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).
