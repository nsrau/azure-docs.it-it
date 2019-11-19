---
title: Proteggi le app Web e le API del servizio app Azure
description: Questo articolo consente di iniziare a proteggere le app Web e le API del servizio app Azure nel centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: be9331ccd548628bfc27172c4f6e625bdba1632c
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158909"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Proteggi le app Web e le API del servizio app Azure

App Azure servizio è una piattaforma completamente gestita per la creazione e l'hosting di API e app Web senza doversi preoccupare della gestione dell'infrastruttura. Fornisce funzionalità di gestione, monitoraggio e informazioni operative per soddisfare i requisiti di prestazioni, sicurezza e conformità di livello aziendale. Per ulteriori informazioni, vedere [app Azure Service](https://azure.microsoft.com/services/app-service/).

Per abilitare Advanced Threat Protection per il piano di servizio app Azure, è necessario:

* Sottoscrivere il piano tariffario standard del Centro sicurezza di Azure
* Abilitare il piano di servizio app come illustrato di seguito. Il Centro sicurezza è integrato in modo nativo con il servizio app, eliminando la necessità di distribuzione e onboarding, l'integrazione è trasparente.
* Disporre di un piano di servizio app associato a computer dedicati. I piani supportati sono: Basic, standard, Premium, isolated o Linux. Il Centro sicurezza non supporta i piani gratuito, condiviso o a consumo. Per altre informazioni, vedere [Piani di servizio app](https://azure.microsoft.com/pricing/details/app-service/plans/).

Con il piano di servizio app abilitato, il Centro sicurezza valuta le risorse coperte dal piano di servizio app e genera raccomandazioni sulla sicurezza in base ai risultati ottenuti. Il Centro sicurezza protegge l'istanza di macchina virtuale in cui è in esecuzione il servizio app e l'interfaccia di gestione. Monitora anche le richieste e le risposte inviate da e verso le applicazioni in esecuzione nel servizio app.

Il Centro sicurezza sfrutta la scalabilità del cloud e la visibilità di Azure come provider di servizi cloud per monitorare gli attacchi di app Web comuni. Il Centro sicurezza può individuare gli attacchi alle applicazioni e identificare gli attacchi emergenti, anche se gli utenti malintenzionati sono nella fase di esplorazione, analizzando per identificare le vulnerabilità in più applicazioni ospitate in Azure. In qualità di servizio nativo di Azure, il Centro sicurezza è anche in una posizione univoca per offrire analisi della sicurezza basate su host che coprono i nodi di calcolo sottostanti per questo PaaS, consentendo al centro sicurezza di rilevare gli attacchi contro le applicazioni Web già sfruttate.


## <a name="enabling-monitoring-and-protection-of-app-service"></a>Abilitazione del monitoraggio e della protezione del servizio app

1. Nella portale di Azure scegliere Centro sicurezza.
2. Passare a **prezzi & impostazioni** e scegliere una sottoscrizione.
3. In **Piano tariffario**, nella riga **Servizio app** attivare/disattivare il piano su **Abilitato**.

    [![l'abilitazione dei servizi app nella sottoscrizione al livello standard](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> Il numero di istanze elencate per la **quantità di risorse** rappresenta il numero totale di istanze di calcolo, in tutti i piani di servizio app in questa sottoscrizione, in esecuzione nel momento in cui è stato aperto il pannello piano tariffario.
>
> App Azure servizio offre un'ampia gamma di piani. Il piano di servizio app definisce il set di risorse di calcolo per l'esecuzione di un'app Web. Sono equivalenti alle server farm nell'hosting Web convenzionale. È possibile configurare una o più app per eseguirle nelle stesse risorse di calcolo (o nello stesso piano di servizio app).
>
>Per convalidare il conteggio, passare a "piani di servizio app" nel portale di Azure, in cui è possibile visualizzare il numero di istanze di calcolo usate da ogni piano. 






Per disabilitare il monitoraggio e gli elementi consigliati per il servizio app, ripetere questo processo e impostare il piano di **servizio app** su **Disabilitato**.



## <a name="see-also"></a>Vedere anche
Questo articolo descrive come usare le funzionalità di monitoraggio nel Centro sicurezza di Azure. Per altre informazioni sul Centro sicurezza di Azure, vedere gli articoli seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md): informazioni su come configurare le impostazioni di sicurezza nel Centro sicurezza di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Servizi app](security-center-virtual-machine-protection.md#app-services): visualizzare un elenco di ambienti del servizio app con riepiloghi sull'integrità.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare l'integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure.
