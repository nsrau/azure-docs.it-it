---
title: Azure Defender per Kubernetes - Vantaggi e funzionalità
description: Informazioni sui vantaggi e sulle funzionalità di Azure Defender per Kubernetes.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: dd92e1529b889671bc29939f7e9611eceac7ee20
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370509"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Introduzione ad Azure Defender per Kubernetes

Il servizio Azure Kubernetes è il servizio gestito da Microsoft per lo sviluppo, la distribuzione e la gestione di applicazioni in contenitori.

Insieme, il Centro sicurezza di Azure e il servizio Azure Kubernetes formano la migliore offerta di sicurezza Kubernetes nativa del cloud e forniscono protezione avanzata dell'ambiente, protezione dei carichi di lavoro e protezione in fase di esecuzione, come descritto di seguito.

Per rilevare le minacce per i cluster Kubernetes, abilitare **Azure Defender per Kubernetes** .

Per abilitare il rilevamento delle minacce a livello di host per i nodi del servizio Azure Kubernetes per Linux, abilitare [Azure Defender per server](defender-for-servers-introduction.md).

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibile a livello generale|
|Prezzi:|**Azure Defender per Kubernetes** è soggetto alle tariffe visualizzate nella [pagina dei prezzi](security-center-pricing.md)|
|Autorizzazioni e ruoli obbligatori:|Il ruolo **Amministratore della sicurezza** può ignorare gli avvisi.<br>Il **ruolo con autorizzazioni di lettura per la sicurezza** può visualizzare i risultati.|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Quali sono i vantaggi di Azure Defender per Kubernetes?

### <a name="run-time-protection"></a>Protezione in fase di esecuzione

Tramite l'analisi continua delle origini del servizio Azure Kubernetes seguenti, il Centro sicurezza offre protezione dalle minacce in tempo reale per gli ambienti in contenitori e genera avvisi per le minacce e le attività dannose rilevate a livello di host *e* di cluster del servizio Azure Kubernetes. È possibile usare queste informazioni per risolvere rapidamente i problemi di sicurezza e migliorare la sicurezza dei contenitori.

Il Centro sicurezza offre protezione dalle minacce a diversi livelli: 

- **Livello di host (fornito da Azure Defender per server)** : usando lo stesso agente di Log Analytics usato dal Centro sicurezza in altre macchine virtuali, Azure Defender monitora i nodi del servizio Azure Kubernetes per Linux per rilevare attività sospette, come il rilevamento della shell Web e la connessione con indirizzi IP sospetti noti. L'agente monitora anche le analisi specifiche dei contenitori, come la creazione di contenitori con privilegi, l'accesso sospetto ai server API e la presenza di server SSH (Secure Shell) in esecuzione all'interno di un contenitore Docker.

    Per un elenco degli avvisi a livello di host del servizio Azure Kubernetes, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-containerhost).

    >[!IMPORTANT]
    > Se si sceglie di non installare gli agenti negli host, si riceverà solo una parte dei vantaggi della protezione dalle minacce e degli avvisi di sicurezza. Si riceveranno comunque gli avvisi relativi all'analisi di rete e alle comunicazioni con server dannosi.

- **Livello di cluster del servizio Azure Kubernetes (fornito da Azure Defender per Kubernetes)** : la protezione dalle minacce a questo livello è basata sull'analisi dei log di controllo di Kubernetes. Per abilitare questo monitoraggio **senza agente** , abilitare Azure Defender. Per generare avvisi a questo livello, il Centro sicurezza monitora i servizi gestiti dal servizio Azure Kubernetes usando i log recuperati da quest'ultimo. Esempi di eventi a questo livello includono dashboard di Kubernetes esposti, creazione di ruoli con privilegi elevati e creazione di montaggi sensibili.

    Per un elenco degli avvisi a livello di cluster del servizio Azure Kubernetes, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-akscluster).

    >[!NOTE]
    > Il Centro sicurezza genera avvisi di sicurezza per le azioni e le distribuzioni del servizio Azure Kubernetes che si verificano dopo l'abilitazione dell'opzione Kubernetes nelle impostazioni della sottoscrizione. 

Inoltre, il team globale di ricercatori Microsoft che si occupano di sicurezza monitora costantemente il panorama delle minacce. Aggiungono avvisi e vulnerabilità specifici dei contenitori man mano che vengono individuati.

> [!TIP]
> È possibile simulare gli avvisi relativi ai contenitori seguendo le istruzioni riportate in [questo post di blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).



## <a name="how-does-security-centers-kubernetes-protection-work"></a>Come funziona la protezione di Kubernetes del Centro sicurezza?

Di seguito è riportato un diagramma generale dell'interazione tra il Centro sicurezza di Azure, il servizio Azure Kubernetes e Criteri di Azure.

Come si può vedere, gli elementi ricevuti e analizzati dal Centro sicurezza includono:

- log di controllo del server API
- eventi di sicurezza non elaborati dall'agente di Log Analytics
- informazioni di configurazione del cluster dal cluster del servizio Azure Kubernetes
- configurazione dei carichi di lavoro da Criteri di Azure (tramite il **componente aggiuntivo Criteri di Azure per Kubernetes** ). [Altre informazioni sulle procedure consigliate per la protezione dei carichi di lavoro con il controllo ammissione di Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Architettura generale dell'interazione tra il Centro sicurezza di Azure, il servizio Azure Kubernetes e Criteri di Azure" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender per Kubernetes - Domande frequenti

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>È possibile ottenere le protezioni del servizio Azure Kubernetes anche senza l'agente di Log Analytics?

Come già accennato, il piano facoltativo **Azure Defender per Kubernetes** fornisce protezione a livello di cluster, mentre l'agente di Log Analytics di **Azure Defender per server** protegge i nodi. 

È consigliabile distribuire entrambi per ottenere la protezione più completa possibile.

Se si sceglie di non installare gli agenti negli host, si riceverà solo una parte dei vantaggi della protezione dalle minacce e degli avvisi di sicurezza. Si riceveranno comunque gli avvisi relativi all'analisi di rete e alle comunicazioni con server dannosi.


### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>Il servizio Azure Kubernetes consente di installare estensioni di VM personalizzate nei nodi del servizio?

Per essere monitorati da Azure Defender, i nodi del servizio Azure Kubernetes devono eseguire l'agente di Log Analytics. 

Il servizio Azure Kubernetes è un servizio gestito e, poiché l'agente di log Analytics è un'estensione gestita da Microsoft, è supportato anche nei cluster del servizio Azure Kubernetes.



### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Se il cluster esegue già un agente di Monitoraggio di Azure per i contenitori, è necessario anche l'agente di Log Analytics?

Per essere monitorati da Azure Defender, i nodi del servizio Azure Kubernetes devono eseguire l'agente di Log Analytics.

Se i cluster eseguono già l'agente di Monitoraggio di Azure per i contenitori, è possibile installare anche l'agente di Log Analytics e i due agenti possono interagire tra loro senza problemi.

[Altre informazioni sull'agente di Monitoraggio di Azure per i contenitori](../azure-monitor/insights/container-insights-manage-agent.md).


## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni sulla protezione di Kubernetes del Centro sicurezza, incluso Azure Defender per Kubernetes. 

Per i materiali correlati, vedere gli articoli seguenti: 

- [Abilitare Azure Defender](security-center-pricing.md)
- [Esportare gli avvisi in un sistema di informazioni di sicurezza e gestione degli eventi Azure Sentinel o di terze parti](continuous-export.md)
- [Tabella di riferimento degli avvisi](alerts-reference.md)