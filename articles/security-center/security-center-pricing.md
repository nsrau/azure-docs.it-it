---
title: Prezzi del Centro sicurezza di Azure
description: Il Centro sicurezza di Azure è disponibile in due modalità, con e senza Azure Defender.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 01d7d29e9fffff3be2c98c0ec52c01774439e3f7
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341189"
---
# <a name="pricing-of-azure-security-center"></a>Prezzi del Centro sicurezza di Azure
Il Centro sicurezza di Azure offre la gestione unificata della sicurezza e la protezione avanzata dalle minacce per carichi di lavoro in esecuzione in Azure, in locale e in altri cloud. Offre visibilità e controllo su carichi di lavoro cloud ibridi, difese attive che riducono l'esposizione a minacce e il rilevamento intelligente che consente di tenere il passo con gli attacchi informatici in rapida evoluzione.


## <a name="free-option-vs-azure-defender-enabled"></a>Opzione gratuita rispetto ad Azure Defender abilitato

Il Centro sicurezza è disponibile in due modalità:

- **Azure Defender - Off** (modalità gratuita): il Centro sicurezza senza Azure Defender viene abilitato gratuitamente in tutte le sottoscrizioni di Azure quando si visita per la prima volta il dashboard Centro sicurezza di Azure nel portale di Azure; in alternativa, viene abilitato a livello di codice tramite API. L'uso di questa modalità gratuita prevede criteri di sicurezza, valutazione continua della sicurezza e raccomandazioni pratiche sulla sicurezza per contribuire a proteggere le risorse di Azure.

- **Azure Defender - On** : l'abilitazione di Azure Defender estende le funzionalità della modalità gratuita ai carichi di lavoro in esecuzione nel cloud privato e in altri cloud pubblici, offrendo la gestione unificata della sicurezza e la protezione dalle minacce per tutti i carichi di lavoro del cloud ibrido. Alcune delle principali funzionalità di Azure Defender sono:

    - **Microsoft Defender per endpoint** : Azure Defender per server include [Microsoft Defender per endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) per funzionalità complete di rilevamento di endpoint e risposta. Per altre informazioni sui vantaggi associati all'uso di Microsoft Defender per endpoint insieme ad Azure Defender, vedere [Usare la soluzione di rilevamento di endpoint e risposta integrata nel Centro sicurezza](security-center-wdatp.md).
    - **Analisi delle vulnerabilità per macchine virtuali e registri contenitori** : uno strumento di analisi facile da distribuire in tutte le macchine virtuali rappresenta la soluzione più avanzata del settore per la gestione delle vulnerabilità. È possibile visualizzare, analizzare e correggere i problemi rilevati direttamente all'interno del Centro sicurezza. 
    - **Sicurezza ibrida** : per ottenere una visualizzazione unificata della sicurezza in tutti i carichi di lavoro locali e cloud; applicare i criteri di sicurezza e la valutazione continua della sicurezza dei carichi di lavoro cloud ibridi per garantire la conformità con gli standard di sicurezza; Raccogliere, cercare e analizzare i dati sulla sicurezza provenienti da più origini, tra cui firewall e altre soluzioni dei partner.
    - **Avvisi di protezione dalle minacce** : l'analisi comportamentale avanzata e Microsoft Intelligent Security Graph offrono maggiori possibilità di contrastare gli attacchi informatici in continua evoluzione. Sfrutta i vantaggi delle analisi comportamentali predefinite e di apprendimento automatico per identificare gli attacchi e gli exploit zero-day. Monitora le reti, i computer e i servizi cloud per rilevare attacchi imminenti e attività post violazione. Semplifica l'investigazione con strumenti interattivi e intelligence per le minacce contestuale.
    - **Controlli di accesso e dell'applicazione** : applicando le raccomandazioni basate su Machine Learning e adattate a specifici carichi di lavoro, è possibile creare elenchi di elementi consentiti e rifiutati per bloccare il malware e altre applicazioni indesiderate. È inoltre possibile ridurre la superficie di attacco di rete con accesso JIT controllato alle porte di gestione nelle macchine virtuali di Azure, in modo da ridurre drasticamente l'esposizione ad attacchi di forza bruta e di altro tipo sulla rete.
    - **Funzionalità di sicurezza dei contenitori** : consentono di sfruttare la gestione delle vulnerabilità e la protezione dalle minacce in tempo reale negli ambienti in contenitori. L'abilitazione di tutte le funzionalità di **Azure Defender per i registri contenitori** può richiedere fino a 12 ore. Gli addebiti sono basati sul numero di immagini di contenitori univoche inserite nel registro contenitori. Dopo la prima analisi, non sono più previsti addebiti per l'immagine a meno che non venga modificata e inserita di nuovo. 

## <a name="try-azure-defender-free-for-30-days"></a>Provare Azure Defender gratuitamente per 30 giorni

Azure Defender è gratuito per i primi 30 giorni. Al termine dei 30 giorni, se si sceglie di continuare a usare il servizio, l'utilizzo inizierà a essere addebitato automaticamente.

## <a name="enable-azure-defender"></a>Abilitare Azure Defender

Con Azure Defender è possibile proteggere un'intera sottoscrizione di Azure e le misure di protezione verranno ereditate da tutte le risorse al suo interno.

Per abilitare Azure Defender:

1. Scegliere **Prezzi e impostazioni** dal menu del Centro sicurezza.
1. Selezionare la sottoscrizione da aggiornare.
1. Selezionare **Azure Defender - On** per eseguire l'aggiornamento.
1. Selezionare **Salva** .

Di seguito è riportata la pagina dei prezzi per una sottoscrizione di esempio. Si noterà che i prezzi vengono applicati separatamente per ogni piano di Azure Defender, che è possibile attivare o disattivare singolarmente.

:::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="Pagina dei prezzi del Centro sicurezza nel portale":::

> [!NOTE]
> Per abilitare le funzionalità del Centro sicurezza, incluse le funzionalità di protezione dalle minacce, è necessario abilitare Azure Defender nella sottoscrizione che contiene i carichi di lavoro applicabili. Se il servizio viene abilitato a livello di area di lavoro, non verranno abilitate funzionalità come l'accesso JIT alle VM, i controlli applicazioni adattivi e i rilevamenti di rete per le risorse di Azure. Inoltre, gli unici piani di Azure Defender disponibili a livello di area di lavoro sono Azure Defender per i server e Azure Defender per i server SQL nelle macchine virtuali.
>
> È possibile abilitare **Azure Defender per gli account di archiviazione** a livello di sottoscrizione o di risorsa.
> È possibile abilitare **Azure Defender per SQL** a livello di sottoscrizione o di risorsa.
> È possibile abilitare la protezione dalle minacce per **Database di Azure per MariaDB/MySQL/PostgreSQL** solo a livello di risorsa.


## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati presentati i prezzi del Centro sicurezza. Per informazioni correlate, vedere:

- [Come ottimizzare i costi dei carichi di lavoro di Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Dettagli relativi ai prezzi nella valuta preferita e in base all'area geografica](https://azure.microsoft.com/pricing/details/security-center/)
- Si consiglia di gestire i costi e limitare la quantità di dati raccolti per una soluzione limitandola a un determinato set di agenti. Il [targeting della soluzione](../azure-monitor/insights/solution-targeting.md) consente di applicare un ambito alla soluzione e avere come target un sottoinsieme di computer nell'area di lavoro. Se si usa una soluzione di targeting, il Centro sicurezza di Azure elenca l'area di lavoro come priva di una soluzione.