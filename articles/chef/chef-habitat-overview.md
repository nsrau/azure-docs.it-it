---
title: Usare Habitat per distribuire l'applicazione in Azure
description: Informazioni su come distribuire in modo coerente l'applicazione nei contenitori e nelle macchine virtuali di Azure
keywords: azure, chef, devops, macchine virtuali, panoramica, automazione, habitat
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 73c6834eb53c0496fa673dd25ab90abc18a6a139
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267077"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Usare Habitat per distribuire l'applicazione in Azure
[Habitat](https://www.habitat.sh/) è un progetto open source innovativo, che offre un approccio completamente nuovo alla gestione delle applicazioni. In Habitat l'applicazione e la sua automazione diventano l'unità di sviluppo. Quando le applicazioni vengono incapsulate in un "habitat" leggero, l'ambiente di runtime, sia esso un contenitore, un ambiente bare-metal o PaaS, non è più l'elemento determinante e non vincola l'applicazione. 

Questo articolo descrive i vantaggi dell'uso di Habitat.

## <a name="support-for-the-modern-application"></a>Supporto per le applicazioni moderne
I pacchetti di Habitat includono tutto ciò di cui un'applicazione ha bisogno per essere eseguita durante l'intero ciclo di vita. I componenti di base di Habitat sono:
- Formato di creazione pacchetto: le applicazioni in un pacchetto Habitat sono atomiche, non modificabili e controllabili.
- Il supervisore di Habitat esegue pacchetti di applicazioni con riconoscimento delle relazioni peer, della strategia di aggiornamento e dei criteri di sicurezza di tali pacchetti. Il supervisore di Habitat configura e gestisce l'applicazione per qualsiasi ambiente presente.
- L'ecosistema Habitat offre anche un servizio di compilazione che accetta un piano di compilazione Habitat, crea il pacchetto Habitat e lo pubblica in una destinazione.

## <a name="run-any-application-anywhere"></a>Eseguire qualsiasi applicazione da qualsiasi posizione
Con Habitat le applicazioni possono essere eseguite senza modifiche in qualsiasi ambiente di runtime. Sono supportati tutti gli ambienti, da macchine virtuali e ambienti bare metal a contenitori (ad esempio Docker), sistemi di gestione cluster (ad esempio Mesosphere o Kubernetes) e sistemi PaaS (ad esempio Pivotal Cloud Foundry).

## <a name="easily-port-legacy-applications"></a>Portabilità facile per le applicazioni legacy
Quando le applicazioni legacy vengono incapsulate in un pacchetto Habitat, sono indipendenti dell'ambiente per il quale sono state create in origine. I pacchetti possono essere trasferiti rapidamente ad ambienti più moderni, come il cloud o i contenitori. Dato che i pacchetti Habitat hanno un'interfaccia standard e rivolta verso l'esterno, anche la gestione delle applicazioni legacy diventa molto più semplice.

## <a name="improve-the-container-experience"></a>Migliorare l'esperienza con i contenitori
Habitat riduce la complessità della gestione dei contenitori negli ambienti di produzione. Dato che automatizza la configurazione dell'applicazione in un contenitore, Habitat aiuta gli sviluppatori a superare le sfide che si presentano con la migrazione delle applicazioni basate su contenitori dagli ambienti di sviluppo alla produzione.

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrazione nel flusso di lavoro DevOps di Chef
Il progetto Habitat è sponsorizzato da Chef. Habitat si avvale dell'esperienza di Chef nell'automazione di infrastrutture per offrire alle applicazioni funzionalità di automazione senza precedenti. Chef gestisce il supporto commerciale per Habitat e garantisce un'integrazione uniforme tra Habitat e Chef Delivery per l'automatizzazione completa del ciclo di rilascio dell'applicazione, dallo sviluppo alla distribuzione.

## <a name="next-steps"></a>Passaggi successivi
* [Creare una macchina virtuale Windows in Azure usando Chef](/azure/virtual-machines/windows/chef-automation)