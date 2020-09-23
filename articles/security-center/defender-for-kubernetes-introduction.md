---
title: 'Azure Defender per Kubernetes: vantaggi e funzionalità'
description: Scopri i vantaggi e le funzionalità di Azure Defender per Kubernetes.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: e72875135243733a6acf03bb3aa6fb9405392d9d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940323"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Introduzione ad Azure Defender per Kubernetes

Azure Kubernetes Service (AKS) è il servizio gestito di Microsoft per lo sviluppo, la distribuzione e la gestione di applicazioni in contenitori.

Il Centro sicurezza di Azure e il servizio contenitore di Azure formano la migliore offerta di sicurezza Kubernetes nativa del cloud e insieme forniscono protezione avanzata dell'ambiente, protezione del carico di lavoro e protezione in fase di esecuzione, come descritto di seguito.

Per il rilevamento delle minacce per i cluster Kubernetes, abilitare **Azure Defender per Kubernetes**.

Il rilevamento delle minacce a livello di host per i nodi AKS di Linux è disponibile se si abilita [Azure Defender per i server](defender-for-servers-introduction.md).

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato versione:|Disponibile a livello generale|
|Prezzi|**Azure Defender per Kubernetes** viene fatturato come indicato nella [pagina dei prezzi](security-center-pricing.md)|
|Ruoli e autorizzazioni necessari:|L' **amministratore della sicurezza** può ignorare gli avvisi.<br>Il **ruolo con autorizzazioni di lettura per la sicurezza** può visualizzare i risultati.|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Nazionale/sovrano (US Gov, Cina gov, altri gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Quali sono i vantaggi di Azure Defender per Kubernetes?

### <a name="run-time-protection"></a>Protezione in fase di esecuzione

Tramite l'analisi continua delle origini AKS seguenti, il Centro sicurezza offre protezione dalle minacce in tempo reale per gli ambienti in contenitori e genera avvisi per le minacce e attività dannose rilevate a livello di cluster host *e* AKS. È possibile usare queste informazioni per risolvere rapidamente i problemi di sicurezza e migliorare la sicurezza dei contenitori.

Il Centro sicurezza offre protezione dalle minacce a diversi livelli: 

- **Livello host (fornito da Azure Defender per i server)** : usando lo stesso agente log Analytics usato dal centro sicurezza in altre VM, Azure Defender monitora i nodi AKS di Linux per attività sospette, ad esempio il rilevamento della shell Web e la connessione con indirizzi IP sospetti noti. L'agente monitora anche le analisi specifiche del contenitore, ad esempio la creazione di contenitori con privilegi, l'accesso sospetto ai server API e i server Secure Shell (SSH) in esecuzione all'interno di un contenitore docker.

    Per un elenco degli avvisi a livello di host AKS, vedere la [tabella di riferimento degli avvisi](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost).

    >[!IMPORTANT]
    > Se si sceglie di non installare gli agenti negli host, si riceverà solo una parte dei vantaggi della protezione dalle minacce e degli avvisi di sicurezza. Si riceveranno comunque gli avvisi relativi all'analisi di rete e alle comunicazioni con server dannosi.

- **Livello del cluster AKS (fornito da Azure Defender per Kubernetes)** : a livello di cluster, la protezione dalle minacce si basa sull'analisi dei log di controllo di Kubernetes. Per abilitare questo monitoraggio senza **agente** , abilitare Azure Defender. Per generare avvisi a questo livello, il Centro sicurezza monitora i servizi gestiti dal servizio Azure Kubernetes usando i log recuperati da quest'ultimo. Esempi di eventi a questo livello includono dashboard di Kubernetes esposti, creazione di ruoli con privilegi elevati e creazione di montaggi sensibili.

    Per un elenco degli avvisi a livello di cluster del servizio Azure Kubernetes, vedere la [tabella di riferimento degli avvisi](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster).

    >[!NOTE]
    > Il Centro sicurezza genera avvisi di sicurezza per le azioni e le distribuzioni del servizio Azure Kubernetes che si verificano dopo l'abilitazione dell'opzione Kubernetes nelle impostazioni della sottoscrizione. 

Inoltre, il team globale di ricercatori Microsoft che si occupano di sicurezza monitora costantemente il panorama delle minacce. Aggiungono avvisi e vulnerabilità specifici dei contenitori man mano che vengono individuati.

> [!TIP]
> È possibile simulare gli avvisi relativi ai contenitori seguendo le istruzioni riportate in [questo post di blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).



## <a name="how-does-security-centers-kubernetes-protection-work"></a>Come funziona la protezione Kubernetes del Centro sicurezza?

Di seguito è riportato un diagramma di alto livello dell'interazione tra il Centro sicurezza di Azure, il servizio Azure Kubernetes e i criteri di Azure.

È possibile osservare che gli elementi ricevuti e analizzati dal centro sicurezza includono:

- log di controllo dal server API
- eventi di sicurezza non elaborati dall'agente di Log Analytics
- informazioni di configurazione del cluster dal cluster AKS
- configurazione del carico di lavoro da criteri di Azure (tramite il **componente aggiuntivo criteri di Azure per Kubernetes**). [Altre informazioni sulle procedure consigliate per la protezione del carico di lavoro usando il controllo di ammissione Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Architettura di alto livello dell'interazione tra il Centro sicurezza di Azure, il servizio Azure Kubernetes e i criteri di Azure" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender per Kubernetes-domande frequenti

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Posso comunque ottenere le protezioni AKS senza l'agente di Log Analytics?

Come indicato in precedenza, il piano facoltativo di **Azure Defender per Kubernetes** offre protezioni a livello di cluster, l'agente log Analytics di **Azure Defender per i server** protegge i nodi. 

È consigliabile distribuire entrambi, per la protezione più completa possibile.

Se si sceglie di non installare l'agente negli host, si riceverà solo un subset dei vantaggi della protezione dalle minacce e degli avvisi di sicurezza. Si riceveranno comunque gli avvisi relativi all'analisi di rete e alle comunicazioni con server dannosi.


## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state illustrate le informazioni sulla protezione Kubernetes del Centro sicurezza, tra cui Azure Defender per Kubernetes. 

Per i materiali correlati, vedere gli articoli seguenti: 

- [Abilitare Azure Defender](security-center-pricing.md)
- [Esportare avvisi in un sistema di sicurezza di Azure o un sistema SIEM di terze parti](continuous-export.md)
- [Tabella di riferimento degli avvisi](alerts-reference.md)