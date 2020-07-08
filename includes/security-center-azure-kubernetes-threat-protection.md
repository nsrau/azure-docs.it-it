---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: ba695337a1da1e440895f6b9f6fe2eb34f5041e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800606"
---
Il Centro sicurezza offre protezione dalle minacce in tempo reale per gli ambienti basati su contenitori e genera avvisi per le attività sospette. È possibile usare queste informazioni per risolvere rapidamente i problemi di sicurezza e migliorare la sicurezza dei contenitori.

Il Centro sicurezza offre protezione dalle minacce a diversi livelli: 

* **Livello host** : l'agente di log Analytics monitora Linux per le attività sospette. L'agente attiva avvisi per le attività sospette che hanno origine dal nodo o da un contenitore in esecuzione su di esso. Esempi di attività di questo tipo includono rilevamento web shell e connessione con indirizzi IP sospetti noti.

    Per informazioni più dettagliate sulla sicurezza dell'ambiente in contenitori, l'agente monitora l'analisi dei contenitori. Attiverà avvisi per eventi quali la creazione di contenitori con privilegi, l'accesso sospetto ai server API e la presenza di server SSH (Secure Shell) in esecuzione all'interno di un contenitore Docker.

    >[!IMPORTANT]
    > Se si sceglie di non installare gli agenti negli host, si riceverà solo una parte dei vantaggi della protezione dalle minacce e degli avvisi di sicurezza. Si riceveranno comunque gli avvisi relativi all'analisi di rete e alle comunicazioni con server dannosi.

    Per un elenco degli avvisi a livello di host AKS, vedere la [tabella di riferimento degli avvisi](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost).


* A **livello di cluster del servizio Azure Kubernetes**, la protezione dalle minacce si basa sull'analisi dei log di controllo di Kubernetes. Per abilitare questo monitoraggio **senza agente**, aggiungere l'opzione Kubernetes alla sottoscrizione dalla pagina **Prezzi e impostazioni** (vedere [Prezzi](https://docs.microsoft.com/azure/security-center/security-center-pricing)). Per generare avvisi a questo livello, il Centro sicurezza monitora i servizi gestiti dal servizio Azure Kubernetes usando i log recuperati da quest'ultimo. Esempi di eventi a questo livello includono dashboard di Kubernetes esposti, creazione di ruoli con privilegi elevati e creazione di montaggi sensibili.

    >[!NOTE]
    > Il Centro sicurezza genera avvisi di sicurezza per le azioni e le distribuzioni del servizio Azure Kubernetes che si verificano dopo l'abilitazione dell'opzione Kubernetes nelle impostazioni della sottoscrizione. 

    Per un elenco degli avvisi a livello di cluster del servizio Azure Kubernetes, vedere la [tabella di riferimento degli avvisi](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster).

Inoltre, il team globale di ricercatori Microsoft che si occupano di sicurezza monitora costantemente il panorama delle minacce. Aggiungono avvisi e vulnerabilità specifici dei contenitori man mano che vengono individuati.

> [!TIP]
> È possibile simulare gli avvisi relativi ai contenitori seguendo le istruzioni riportate in [questo post di blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).