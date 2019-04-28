---
title: Sunto sulla sicurezza dei contenitori in Microsoft Azure
description: Sunto del white paper sulla sicurezza dei contenitori in Microsoft Azure.
author: TomShinder
ms.author: TomSh
ms.date: 09/05/2018
ms.topic: article
ms.service: security
ms.openlocfilehash: 0f892767bb7ab8202a27f298b1cb708d9802aaa4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60590104"
---
# <a name="container-security-in-microsoft-azure"></a>Sicurezza dei contenitori in Microsoft Azure
## <a name="abstract"></a>Sunto

La tecnologia dei contenitori sta causando una modifica strutturale nel mondo del cloud computing. I contenitori consentono di eseguire più istanze di un'applicazione in una singola istanza di un sistema operativo usando quindi in modo più efficiente le risorse. I contenitori offrono flessibilità e coerenza alle organizzazioni. Consentono la distribuzione continua perché l'applicazione può essere sviluppata in un desktop, testata in una macchina virtuale e quindi distribuita per la produzione nel cloud. I contenitori offrono flessibilità, semplificazione delle operazioni, scalabilità e costi ridotti grazie all'ottimizzazione delle risorse.

Poiché la tecnologia dei contenitori è relativamente nuova, molti professionisti IT hanno preoccupazioni relative alla sicurezza riguardo alla mancanza di visibilità e all'utilizzo in un ambiente di produzione. I team di sviluppo spesso non sono a conoscenza delle procedure consigliate per la sicurezza. Questo white paper può aiutare i team operativi e gli sviluppatori a selezionare gli approcci per proteggere lo sviluppo e le distribuzioni dei contenitori nella piattaforma di Microsoft Azure.

Questo documento descrive i contenitori, la distribuzione e la gestione dei contenitori e i servizi di piattaforma nativa. Vengono inoltre descritti i problemi di sicurezza runtime che si verificano con l'uso dei contenitori nella piattaforma Azure. Nelle immagini e negli esempi questo documento è incentrato su Docker come modello di contenitore e su Kubernetes come agente di orchestrazione. La maggior parte delle raccomandazioni sulla sicurezza si applicano anche ad altri modelli di contenitore di partner Microsoft sulla piattaforma Azure.

[Scarica il white paper](https://azure.microsoft.com/mediahandler/files/resourcefiles/container-security-in-microsoft-azure/Open%20Container%20Security%20in%20Microsoft%20Azure.pdf)