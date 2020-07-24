---
title: Prezzi dei livelli del Centro sicurezza di Azure
description: Il Centro sicurezza di Azure è disponibile in due livelli, gratuito e standard. In questa pagina viene illustrato come eseguire l'aggiornamento da gratuito a standard.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2020
ms.author: memildin
ms.openlocfilehash: 882f8ab7e4f55809759f3a94fd04fb2c410fe188
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089720"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>Eseguire l'aggiornamento al livello standard per la sicurezza avanzata

Il Centro sicurezza di Azure offre la gestione unificata della sicurezza e la protezione avanzata dalle minacce per carichi di lavoro in esecuzione in Azure, in locale e in altri cloud. Offre visibilità e controllo su carichi di lavoro cloud ibridi, difese attive che riducono l'esposizione a minacce e il rilevamento intelligente che consente di tenere il passo con gli attacchi informatici in rapida evoluzione.

## <a name="pricing-tiers"></a>Piani tariffari
Il Centro sicurezza è disponibile in due livelli:

- Il livello **gratuito** è abilitato in tutte le sottoscrizioni di Azure quando si visita il dashboard del Centro sicurezza di azure nel portale di Azure per la prima volta o se è abilitato a livello di codice tramite l'API. Il livello gratuito fornisce criteri di sicurezza, valutazione continua della sicurezza e raccomandazioni di sicurezza praticabili che consentono di proteggere le risorse di Azure.

- Il livello **standard** estende le funzionalità del livello gratuito ai carichi di lavoro in esecuzione in cloud privati e pubblici, offrendo una gestione unificata della sicurezza e la protezione dalle minacce nei carichi di lavoro cloud ibridi. Il livello standard aggiunge anche funzionalità di protezione dalle minacce, che usano l'analisi comportamentale incorporata e l'apprendimento automatico per identificare gli attacchi e gli exploit zero-day, i controlli di accesso e delle applicazioni per ridurre l'esposizione agli attacchi di rete e ai malware e altro ancora. Inoltre, il livello standard aggiunge l'analisi delle vulnerabilità per le macchine virtuali. È possibile provare gratuitamente il livello standard. Il Centro sicurezza standard supporta le risorse di Azure, tra cui macchine virtuali, set di scalabilità di macchine virtuali, servizio app, server SQL e account di archiviazione. Se si dispone del Centro sicurezza di Azure standard, è possibile rifiutare esplicitamente il supporto in base al tipo di risorsa. 

La maggior parte delle valutazioni della sicurezza del livello gratuito per le macchine virtuali, oltre a molti avvisi di sicurezza del livello standard, richiede l'installazione dell'agente di Log Analytics. È possibile abilitare il provisioning automatico nel centro sicurezza per distribuire automaticamente l'agente nelle VM di Azure.

## <a name="try-standard-tier-free-for-30-days"></a>Prova gratuitamente il livello standard per 30 giorni
Il livello standard è gratuito per i primi 30 giorni. Al termine dei 30 giorni, se si sceglie di continuare a usare il servizio, i costi inizieranno a essere addebitati automaticamente.

È possibile aggiornare un'intera sottoscrizione di Azure al livello standard, che viene ereditato da tutte le risorse all'interno della sottoscrizione.

Per ottenere il livello standard:

1. Selezionare **prezzi & impostazioni** nel menu principale del **Centro sicurezza** .
2. Selezionare la sottoscrizione che si desidera aggiornare a standard.
3. Selezionare **Piano tariffario**.
4. Selezionare **standard** per l'aggiornamento.
5. Fare clic su **Salva**.

[![Prezzi del Centro sicurezza](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> Per abilitare tutte le funzionalità del Centro sicurezza, incluse le funzionalità di protezione dalle minacce, è necessario applicare il piano tariffario standard alla sottoscrizione contenente i carichi di lavoro applicabili. La configurazione dei prezzi per un'area di lavoro non Abilita l'accesso just-in-time alle macchine virtuali, i controlli delle applicazioni adattivi e i rilevamenti di rete per le risorse di Azure. 
>
> È possibile abilitare la protezione dalle minacce per gli **account di archiviazione di Azure** a livello di sottoscrizione o di risorsa.
> È possibile abilitare la protezione dalle minacce per i **server SQL del database SQL di Azure** a livello di sottoscrizione o di risorsa.
> È possibile abilitare la protezione dalle minacce per **Database di Azure per MariaDB/MySQL/PostgreSQL** solo a livello di risorsa.


## <a name="why-upgrade-to-standard"></a>Perché eseguire l'aggiornamento al livello standard?
Il Centro sicurezza offre maggiore sicurezza e protezione dalle minacce per i carichi di lavoro di cloud ibridi, tra cui:

- **Sicurezza ibrida**: per ottenere una visualizzazione unificata della sicurezza in tutti i carichi di lavoro locali e cloud; applicare i criteri di sicurezza e la valutazione continua della sicurezza dei carichi di lavoro cloud ibridi per garantire la conformità con gli standard di sicurezza; Raccogli, Cerca e analizza i dati di sicurezza da più origini, inclusi firewall e altre soluzioni partner.
- **Avvisi di sicurezza** : usare l'analisi avanzata e il Microsoft Intelligent Security Graph per ottenere un limite rispetto agli attacchi informatici in continua evoluzione. Sfrutta i vantaggi delle analisi comportamentali predefinite e di apprendimento automatico per identificare gli attacchi e gli exploit zero-day. Monitora le reti, i computer e i servizi cloud per rilevare attacchi imminenti e attività post violazione. Semplifica l'investigazione con strumenti interattivi e intelligence per le minacce contestuale.
- **Analisi delle vulnerabilità per le macchine virtuali** : è possibile distribuire facilmente uno scanner a tutte le macchine virtuali che fornisce la soluzione più avanzata del settore per la gestione delle vulnerabilità. Visualizzare, analizzare e correggere i risultati direttamente nel centro sicurezza. 
- **Controlli di accesso e applicazioni** : blocca il malware e altre applicazioni indesiderate applicando i consigli per l'inserimento nell'elenco elementi consentiti di Machine Learning adattati ai carichi di lavoro Ridurre la superficie di attacco alla rete con l'accesso just-in-Time controllato alle porte di gestione nelle macchine virtuali di Azure. Questo riduce drasticamente l'esposizione a forza bruta e ad altri attacchi di rete.
- **Funzionalità di sicurezza del contenitore** : vantaggi della gestione delle vulnerabilità e della protezione dalle minacce in tempo reale negli ambienti in contenitori. Quando si Abilita la risorsa registri contenitori, potrebbe essere necessario 12hrs fino a quando non vengono abilitate tutte le funzionalità. Gli addebiti sono basati sul numero di immagini di contenitori univoche inserite nel registro di sistema connesso. Dopo che un'immagine è stata analizzata una volta, non verrà addebitata di nuovo, a meno che non venga modificata e sottoposta a push ancora una volta. 




## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati presentati i prezzi del Centro sicurezza. Per ulteriori informazioni sulla sicurezza avanzata del livello standard e sulla protezione avanzata dalle minacce, vedere:

- [Protezione dalle minacce nel Centro sicurezza di Azure](threat-protection.md)
- [Controllo di accesso just-in-time alle macchine virtuali](security-center-just-in-time.md)
- [Panoramica della sicurezza dei contenitori](container-security.md)
- [Dettagli relativi ai prezzi nella valuta preferita e in base all'area geografica](https://azure.microsoft.com/pricing/details/security-center/)