---
title: Componenti di una strategia di risposta DDoS
description: Informazioni su come usare la protezione DDoS di Azure standard per rispondere agli attacchi DDoS.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 23822ce5b311dc479824128d66bc18a15473862d
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905315"
---
# <a name="components-of-a-ddos-response-strategy"></a>Componenti di una strategia di risposta DDoS

Un attacco DDoS che prende di mira le risorse di Azure richiede solitamente un intervento minimo dal punto di vista dell'utente. L'integrazione della mitigazione DDoS nella strategia di risposta agli eventi imprevisti contribuisce comunque a ridurre al minimo l'impatto sulla continuità aziendale.

## <a name="microsoft-threat-intelligence"></a>Intelligence per le minacce di Microsoft

Microsoft ha a disposizione un'ampia rete di intelligence per le minacce. Questa rete usa le conoscenze collettive di una comunità di sicurezza estesa che supporta Microsoft Online Services, i partner Microsoft e le relazioni all'interno della comunità della sicurezza Internet. 

In qualità di fornitore di infrastrutture critiche, Microsoft riceve avvisi tempestivi sulle minacce e raccoglie informazioni sulle minacce dai propri servizi online e dalla base clienti globale. Microsoft incorpora tutte queste informazioni sulle minacce nei prodotti Protezione DDoS di Azure.

La Microsoft Digital Crimes Unit (DCU) mette inoltre in atto strategie offensive contro le botnet, che sono una fonte comune di comando e controllo per gli attacchi DDoS.

## <a name="risk-evaluation-of-your-azure-resources"></a>Valutazione del rischio delle risorse di Azure.

È imperativo comprendere la portata del rischio di un attacco DDoS su base continuativa. Porsi periodicamente le domande seguenti:

- Quali nuove risorse di Azure pubblicamente disponibili hanno bisogno di protezione?

- È presente un singolo punto di guasto nel servizio? 

- Come possono essere isolati i servizi per limitare l'impatto di un attacco mantenendo comunque i servizi disponibili per i clienti validi?

- Esistono reti virtuali in cui Protezione DDoS Standard dovrebbe essere abilitata, ma non lo è? 

- I miei servizi sono attivi/non attivi con il failover in più regioni?

È essenziale comprendere il comportamento normale di un'applicazione e prepararsi a agire se l'applicazione non si comporta come previsto durante un attacco DDoS. I monitoraggi sono configurati per le applicazioni aziendali cruciali che simulano il comportamento del client e inviano notifiche quando vengono rilevate anomalie rilevanti. Per ottenere informazioni sull'integrità dell'applicazione, vedere procedure consigliate di [monitoraggio e diagnostica](https://docs.microsoft.com/azure/architecture/best-practices/monitoring#monitoring-and-diagnostics-scenarios) .

[Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme. È possibile usare Application Insights per monitorare l'applicazione Web mentre è in esecuzione. Il servizio rileva automaticamente le anomalie nelle prestazioni Sono inclusi gli strumenti di analisi che consentono di diagnosticare i problemi e di capire cosa fanno gli utenti con l'app. Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità.

## <a name="customer-ddos-response-team"></a>Team di risposta del cliente DDoS

La creazione di un team di risposta DDoS è un passo fondamentale per rispondere a un attacco in modo rapido ed efficace. Identificare i contatti nell'organizzazione che sovrintenderanno sia alla pianificazione che all'esecuzione. Questo team di risposta DDoS deve comprendere a fondo il servizio Protezione DDoS di Azure Standard. Assicurarsi che il team possa identificare e mitigare un attacco coordinandosi con i clienti interni ed esterni, incluso il team di supporto Microsoft. 

Si consiglia di usare gli esercizi di simulazione come parte normale della disponibilità del servizio e della pianificazione della continuità. questi esercizi dovrebbero includere i test di scalabilità. Per informazioni su come simulare il traffico di test DDoS sugli endpoint pubblici di Azure, vedere [eseguire test tramite simulazioni](test-through-simulations.md) .

## <a name="alerts-during-an-attack"></a>Avvisi durante un attacco

Protezione DDoS di Azure Standard identifica e mitiga gli attacchi DDoS senza alcun intervento dell'utente. Per ricevere una notifica quando esiste una mitigazione attiva per un indirizzo IP pubblico protetto, è possibile [configurare un avviso](telemetry-monitoring-alerting.md) sulla metrica **sotto attacco DDoS o no** . È possibile scegliere di creare avvisi per le altre metriche DDoS per comprendere la portata dell'attacco, il traffico in corso di eliminazione e altri dettagli.

### <a name="when-to-contact-microsoft-support"></a>Quando contattare il supporto Microsoft

I clienti di protezione DDoS di Azure standard hanno accesso al team DDoS Rapid Response (DRR), che può aiutare ad affrontare l'analisi degli attacchi durante un attacco e l'analisi post-attacco. Vedere la [risposta rapida DDoS](ddos-rapid-response.md) per altri dettagli, ad esempio quando è necessario coinvolgere il team DRR.

## <a name="post-attack-steps"></a>Procedura successiva all'attacco.

È sempre una buona strategia eseguire una valutazione a posteriori dopo un attacco e modificare la strategia di risposta DDoS in base alle esigenze. Ecco alcuni aspetti da considerare:

- C'è stata un'interruzione del servizio o dell'esperienza dell'utente a causa della mancanza di un'architettura scalabile?

- Quali applicazioni o servizi hanno sofferto maggiormente?

- Quanto è stata efficace la strategia di risposta DDoS e come potrebbe essere migliorata?

Se si sospetta di essere sotto attacco DDoS, eseguire l'escalation attraverso i normali canali di supporto di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un piano di protezione DDoS](manage-ddos-protection.md).