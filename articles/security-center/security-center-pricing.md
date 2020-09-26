---
title: Prezzi del Centro sicurezza di Azure
description: Il Centro sicurezza di Azure è disponibile in due modalità con e senza Azure Defender.
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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 393e769f147629d2d40344052ba502b22a18cd2a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314539"
---
# <a name="pricing-of-azure-security-center"></a>Prezzi del Centro sicurezza di Azure
Il Centro sicurezza di Azure offre la gestione unificata della sicurezza e la protezione avanzata dalle minacce per carichi di lavoro in esecuzione in Azure, in locale e in altri cloud. Offre visibilità e controllo su carichi di lavoro cloud ibridi, difese attive che riducono l'esposizione a minacce e il rilevamento intelligente che consente di tenere il passo con gli attacchi informatici in rapida evoluzione.


## <a name="free-option-vs-azure-defender-enabled"></a>Opzione gratuita rispetto ad Azure Defender abilitata

Il Centro sicurezza è disponibile in due modalità:

- **Azure Defender off** (gratuito): il Centro sicurezza senza Azure Defender è abilitato gratuitamente per tutte le sottoscrizioni di Azure quando si visita il dashboard del Centro sicurezza di azure nel portale di Azure per la prima volta o se è abilitato a livello di codice tramite l'API. L'uso di questa modalità gratuita fornisce criteri di sicurezza, valutazione continua della sicurezza e raccomandazioni di sicurezza praticabili che consentono di proteggere le risorse di Azure.

- **Azure Defender on** -abilitazione di Azure Defender estende le funzionalità della modalità gratuita ai carichi di lavoro in esecuzione in cloud privati e pubblici, offrendo la gestione unificata della sicurezza e la protezione dalle minacce nei carichi di lavoro cloud ibridi. Di seguito sono riportate alcune delle principali funzionalità di Azure Defender:

    - **Sicurezza ibrida**: per ottenere una visualizzazione unificata della sicurezza in tutti i carichi di lavoro locali e cloud; applicare i criteri di sicurezza e la valutazione continua della sicurezza dei carichi di lavoro cloud ibridi per garantire la conformità con gli standard di sicurezza; Raccogli, Cerca e analizza i dati di sicurezza da più origini, inclusi firewall e altre soluzioni partner.
    - **Avvisi di protezione dalle minacce** : analisi comportamentale avanzata e i Microsoft Intelligent Security Graph offrono un vantaggio per gli attacchi informatici in continua evoluzione. Sfrutta i vantaggi delle analisi comportamentali predefinite e di apprendimento automatico per identificare gli attacchi e gli exploit zero-day. Monitora le reti, i computer e i servizi cloud per rilevare attacchi imminenti e attività post violazione. Semplifica l'investigazione con strumenti interattivi e intelligence per le minacce contestuale.
    - **Analisi delle vulnerabilità per le macchine virtuali e i registri contenitori** : è possibile distribuire facilmente uno scanner a tutte le macchine virtuali che fornisce la soluzione più avanzata del settore per la gestione delle vulnerabilità. Visualizzare, analizzare e correggere i risultati direttamente nel centro sicurezza. 
    - **Controlli di accesso e delle applicazioni** : blocca il malware e altre applicazioni indesiderate applicando consigli basati su Machine Learning adattati ai tuoi carichi di lavoro specifici per creare elenchi di Consenti e nega. Ridurre la superficie di attacco alla rete con l'accesso just-in-Time controllato alle porte di gestione nelle macchine virtuali di Azure. Questo riduce drasticamente l'esposizione a forza bruta e ad altri attacchi di rete.
    - **Funzionalità di sicurezza del contenitore** : vantaggi della gestione delle vulnerabilità e della protezione dalle minacce in tempo reale negli ambienti in contenitori. Quando si abilita **Azure Defender per i registri contenitori**, potrebbe essere necessario 12hrs fino a quando non vengono abilitate tutte le funzionalità. Gli addebiti sono basati sul numero di immagini di contenitori univoche inserite nel registro di sistema connesso. Dopo che un'immagine è stata analizzata una volta, non sarà più addebitata, a meno che non venga modificata e sottoposta a push ancora una volta. 

## <a name="try-azure-defender-free-for-30-days"></a>Provare Azure Defender gratuitamente per 30 giorni

Azure Defender è gratuito per i primi 30 giorni. Alla fine di 30 giorni, se si sceglie di continuare a usare il servizio, si inizierà a caricare automaticamente l'utilizzo.

## <a name="enable-azure-defender"></a>Abilitare Azure Defender

È possibile proteggere un'intera sottoscrizione di Azure con Azure Defender e le protezioni verranno ereditate da tutte le risorse all'interno della sottoscrizione.

Per abilitare Azure Defender:

1. Dal menu principale del Centro sicurezza selezionare **prezzi & impostazioni**.
1. Selezionare la sottoscrizione che si desidera aggiornare.
1. Selezionare **Azure Defender in** per l'aggiornamento.
1. Selezionare **Salva**.

Di seguito è riportata la pagina dei prezzi per una sottoscrizione di esempio. Si noterà che ogni piano in Azure Defender viene valutato separatamente e può essere impostato singolarmente su on o off.

:::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="Pagina dei prezzi del Centro sicurezza nel portale":::

> [!NOTE]
> Per abilitare tutte le funzionalità del Centro sicurezza, incluse le funzionalità di protezione dalle minacce, è necessario abilitare Azure Defender nella sottoscrizione che contiene i carichi di lavoro applicabili. L'abilitazione a livello di area di lavoro non Abilita l'accesso just-in-time alle macchine virtuali, i controlli delle applicazioni adattivi e i rilevamenti di rete per le risorse di Azure. 
>
> È possibile abilitare **Azure Defender per gli account di archiviazione** a livello di sottoscrizione o di risorsa.
> È possibile abilitare **Azure Defender per SQL** a livello di sottoscrizione o di risorsa.
> È possibile abilitare la protezione dalle minacce per **Database di Azure per MariaDB/MySQL/PostgreSQL** solo a livello di risorsa.


## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati presentati i prezzi del Centro sicurezza. Per materiale correlato, vedere:

- [Come ottimizzare i costi del carico di lavoro di Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Dettagli relativi ai prezzi nella valuta preferita e in base all'area geografica](https://azure.microsoft.com/pricing/details/security-center/)
- Si consiglia di gestire i costi e limitare la quantità di dati raccolti per una soluzione limitandola a un determinato set di agenti. Il [targeting della soluzione](../operations-management-suite/operations-management-suite-solution-targeting.md) consente di applicare un ambito alla soluzione e avere come target un sottoinsieme di computer nell'area di lavoro. Se si usa una soluzione di targeting, il Centro sicurezza di Azure elenca l'area di lavoro come priva di una soluzione.