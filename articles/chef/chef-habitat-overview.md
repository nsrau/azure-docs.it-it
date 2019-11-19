---
title: Usare Habitat per distribuire l'applicazione in Azure
description: Informazioni su come distribuire in modo coerente l'applicazione nei contenitori e nelle macchine virtuali di Azure
keywords: azure, chef, devops, macchine virtuali, panoramica, automazione, habitat
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: de444836c04d1d37a19a006ce4eafdcae867ca5a
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158051"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Usare Habitat per distribuire l'applicazione in Azure
[Habitat](https://www.habitat.sh/) è un sistema di runtime e creazione di pacchetti di applicazione che raggruppa l'applicazione e la sua automazione come unità di distribuzione. Viene così creata la massima portabilità per l'applicazione, in modo che possa essere distribuita a contenitori, macchine virtuali, computer, bare metal o PaaS senza riscrittura o riassemblaggio.

Questo articolo descrive i vantaggi principali dell'uso di Habitat.

## <a name="modernize-and-move-legacy-applications"></a>Eseguire lo spostamento e la modernizzazione delle applicazioni legacy
Con Habitat è possibile creare nuovi pacchetti per le applicazioni legacy, così che non sia più necessario usarle con sistemi operativi o middleware meno recenti. L'elemento ottenuto risulta sia portabile che facilmente riprogrammabile in infrastrutture più recenti, come le macchine virtuali o i contenitori in esecuzione nel cloud.

## <a name="accelerate-container-adoption"></a>Accelerare l'adozione di contenitore
Habitat risolve la distribuzione continua di applicazioni complesse e orientate ai microservizi rappresentando accuratamente le dipendenze di runtime. Permette inoltre di andare oltre la semplice distribuzione blu/verde dei singoli componenti e di progettare comportamenti di distribuzione sofisticati senza generare flussi di orchestrazione complessa.

## <a name="run-any-application-anywhere"></a>Eseguire qualsiasi applicazione da qualsiasi posizione
Con Habitat le applicazioni possono essere eseguite senza modifiche in qualsiasi ambiente di runtime. Sono supportati tutti gli ambienti, da macchine virtuali e ambienti bare metal a contenitori (ad esempio Docker), sistemi di gestione cluster (ad esempio Mesosphere o Kubernetes) e sistemi PaaS (ad esempio Pivotal Cloud Foundry).

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrazione nel flusso di lavoro DevOps di Chef
Il progetto Habitat è parte di un progetto open source del software Chef, che vanta una forte community di supporto. Habitat si avvale dell'esperienza di Chef nell'automazione di infrastrutture per offrire alle applicazioni funzionalità di automazione senza precedenti. Chef gestisce il supporto commerciale per Habitat e garantisce un'integrazione uniforme tra Habitat e Chef Automate per l'automatizzazione completa del ciclo di rilascio dell'applicazione, dallo sviluppo alla distribuzione.

## <a name="next-steps"></a>Passaggi successivi

* [Provare Habitat](https://www.habitat.sh/learn/)
