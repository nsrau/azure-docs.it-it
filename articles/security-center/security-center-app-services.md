---
title: Protezione dei servizi app nel Centro sicurezza di Azure | Microsoft Docs
description: Questo articolo illustra come iniziare a proteggere i servizi app nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: mbaldwin
editor: ''
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2019
ms.author: monhaber
ms.openlocfilehash: bf3ceb0d5f12783957e127b3596ab07b7531ad5a
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55491944"
---
# <a name="protect-app-service-with-azure-security-center"></a>Proteggere il servizio app con il Centro sicurezza di Azure
Questo articolo illustra come usare il Centro sicurezza di Azure per monitorare e proteggere le applicazioni in esecuzione sul servizio app.

Il servizio app consente di creare e ospitare applicazioni Web nel linguaggio di programmazione preferito senza gestire l'infrastruttura. Il servizio app offre scalabilità automatica e disponibilità elevata, supporta sia Windows che Linux e consente le distribuzioni automatizzate da GitHub, Azure DevOps, o qualsiasi repository Git. 

Le vulnerabilità nelle applicazioni Web vengono spesso sfruttate dagli utenti malintenzionati perché dispongono di un'interfaccia comune e dinamica per quasi tutte le organizzazioni su Internet. Le richieste alle applicazioni in esecuzione sul servizio app passano attraverso diversi gateway distribuiti nei data center di Azure in tutto il mondo, responsabili del routing di ogni richiesta all'applicazione corrispondente. 

Il Centro sicurezza di Azure può eseguire valutazioni ed elementi consigliati per le applicazioni in esecuzione sul servizio app nelle sandbox nella macchina virtuale o nelle istanze on demand. Sfruttando la visibilità di Azure come provider di servizi cloud, il Centro sicurezza analizza i log interni del servizio app per monitorare gli attacchi più comuni alle app Web spesso eseguiti su più destinazioni.

Il Centro sicurezza sfrutta la scalabilità del cloud per identificare gli attacchi alle applicazioni del servizio app e concentrarsi sui potenziali attacchi, mentre gli utenti malintenzionati sono in fase di perlustrazione, eseguendo la scansione per identificare le vulnerabilità su più siti Web, ospitati in Azure. Il Centro sicurezza usa modelli di analisi e di Machine Learning per includere tutte le interfacce che consentono ai clienti di interagire con le proprie applicazioni, sia tramite HTTP che tramite i metodi di gestione. Inoltre, come servizio proprietario in Azure, il Centro sicurezza è anche nella posizione unica di offrire analisi di sicurezza basate sugli host che coprono i nodi di calcolo sottostanti per questa PaaS, consentendo al Centro sicurezza di rilevare attacchi alle applicazioni Web già sfruttate.

## <a name="prerequisites"></a>Prerequisiti

Per monitorare e proteggere il servizio app, è necessario disporre di un piano di servizio app associato a macchine dedicate. Questi piani sono: Basic, Standard, Premium, Isolato o Linux. Il Centro sicurezza di Azure non supporta i piani Gratuito, Condiviso o A consumo. Per altre informazioni, vedere [Piani di servizio app](https://azure.microsoft.com/pricing/details/app-service/plans/).

## <a name="security-center-protection"></a>Protezione del Centro sicurezza

Il Centro sicurezza di Azure protegge l'istanza di macchina virtuale in cui è in esecuzione il servizio app e l'interfaccia di gestione. Monitora anche le richieste e le risposte inviate da e verso le applicazioni in esecuzione nel servizio app.

Il Centro sicurezza è integrato in modo nativo con il servizio app, eliminando la necessità di eseguire la distribuzione e l'onboarding; l'integrazione è completamente trasparente.



## <a name="enabling-monitoring-and-protection-of-app-service"></a>Abilitazione del monitoraggio e della protezione del servizio app

1. In Azure scegliere Centro sicurezza.
2. Passare a **Criteri di sicurezza** e scegliere una sottoscrizione.
3. Alla fine della riga della sottoscrizione fare clic su **Modifica impostazioni**.
4. In **Piano tariffario**, nella riga **Servizio app** attivare/disattivare il piano su **Abilitato**.

![attivazione/disattivazione del servizio app](./media/security-center-app-services/app-services-toggle.png)

>[!NOTE]
> Il numero di istanze elencate per la quantità di risorse rappresenta il numero di istanze pertinenti del servizio app attivo al momento dell'apertura del pannello dei piani tariffari. Poiché questo numero può variare in base alle opzioni di scalabilità selezionate, il numero di istanze per le quali viene addebitato il costo verrà modificato di conseguenza.

Per disabilitare il monitoraggio e gli elementi consigliati per il servizio app, ripetere questo processo e impostare il piano di **servizio app** su **Disabilitato**.



## <a name="see-also"></a>Vedere anche 
Questo articolo descrive come usare le funzionalità di monitoraggio nel Centro sicurezza di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md): Informazioni su come configurare le impostazioni di sicurezza nel Centro sicurezza di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): Informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Servizi app](security-center-virtual-machine-protection.md#app-services):  Visualizzare un elenco degli ambienti del servizio app con riepiloghi sull'integrità.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): Informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): Domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): Post di blog sulla sicurezza e sulla conformità di Azure.
