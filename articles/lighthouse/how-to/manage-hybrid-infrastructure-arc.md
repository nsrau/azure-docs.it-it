---
title: Gestisci l'infrastruttura ibrida su larga scala con Azure Arc
description: Informazioni su come gestire in modo efficace i computer dei clienti e i cluster Kubernetes all'esterno di Azure.
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 66a798265683045d7ff9f3d8d811141800d08f9b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336616"
---
# <a name="manage-hybrid-infrastructure-at-scale-with-azure-arc"></a>Gestisci l'infrastruttura ibrida su larga scala con Azure Arc

Come provider di servizi, è possibile che siano stati caricati più tenant del cliente nel [Faro di Azure](../overview.md). Azure Lighthouse consente ai provider di servizi di eseguire operazioni su larga scala tra più tenant Azure Active Directory (Azure AD) contemporaneamente, rendendo più efficienti le attività di gestione.

[Azure Arc](../../azure-arc/overview.md) consente di semplificare gli ambienti complessi e distribuiti in locale, perimetrale e multicloud, consentendo la distribuzione di servizi di Azure ovunque ed estendendo la gestione di Azure a qualsiasi infrastruttura.

Con i [server abilitati per Azure Arc](../../azure-arc/servers/overview.md), i clienti possono gestire qualsiasi computer Windows e Linux ospitato all'esterno di Azure nella propria rete aziendale, nello stesso modo in cui gestiscono le macchine virtuali native di Azure. Collegando un computer ibrido ad Azure, esso diventa connesso e viene gestito come una risorsa in Azure. I provider di servizi possono quindi gestire questi computer non Azure insieme alle risorse di Azure dei clienti.

[Azure Arc Enabled Kubernetes (anteprima)](../../azure-arc/kubernetes/overview.md) consente ai clienti di aggiungere e configurare cluster Kubernetes all'interno o all'esterno di Azure. Quando un cluster Kubernetes viene collegato ad Azure Arc, viene visualizzato nella portale di Azure, con un ID Azure Resource Manager e un'identità gestita. I cluster sono collegati alle sottoscrizioni standard di Azure, si trovano in un gruppo di risorse e possono ricevere tag esattamente come qualsiasi altra risorsa di Azure.

Questo argomento fornisce una panoramica del modo in cui i provider di servizi possono usare i server abilitati per Azure Arc e Azure Arc abilitato Kubernetes (anteprima) in un modo scalabile per gestire l'ambiente ibrido dei clienti, con visibilità su tutti i tenant dei clienti gestiti.

> [!TIP]
> Per quanto riguarda i provider di servizi e i clienti in questo argomento, queste indicazioni si applicano anche alle [aziende che usano Azure Lighthouse per gestire più tenant](../concepts/enterprise.md).

## <a name="manage-hybrid-servers-at-scale-with-azure-arc-enabled-servers"></a>Gestire server ibridi su larga scala con i server abilitati per Azure Arc

Come provider di servizi, è possibile gestire computer Windows Server o Linux locali all'esterno di Azure, che i clienti si sono connessi alla propria sottoscrizione usando l' [agente del computer connesso di Azure](../../azure-arc/servers/agent-overview.md).

Quando si visualizzano le risorse per una sottoscrizione delegata nel portale di Azure, verranno visualizzati i computer connessi con l'etichetta **Azure Arc**. È possibile gestire questi computer connessi usando costrutti di Azure, ad esempio criteri di Azure e l'assegnazione di tag, nello stesso modo in cui si gestiscono le risorse di Azure del cliente. È anche possibile lavorare tra i tenant del cliente per gestire tutti i computer ibridi connessi.

Ad esempio, è possibile [garantire che lo stesso set di criteri venga applicato tra le macchine ibride dei clienti](../../azure-arc/servers/learn/tutorial-assign-policy-portal.md). È anche possibile usare il Centro sicurezza di Azure per monitorare la conformità in tutti gli ambienti ibridi dei clienti oppure [usare monitoraggio di Azure per raccogliere i dati direttamente dai computer ibridi](../../azure-arc/servers/learn/tutorial-enable-vm-insights.md) in un'area di lavoro log Analytics. Le [estensioni delle macchine virtuali](../../azure-arc/servers/manage-vm-extensions.md) possono essere distribuite in macchine virtuali Windows e Linux non di Azure, semplificando la gestione delle macchine ibride del cliente.

## <a name="manage-hybrid-kubernetes-clusters-at-scale-with-azure-arc-enabled-kubernetes-preview"></a>Gestire cluster Kubernetes ibridi su larga scala con Azure Arc Enabled Kubernetes (anteprima)

> [!NOTE]
> Azure Arc Enabled Kubernetes è attualmente in fase di anteprima. Al momento non è consigliabile per i carichi di lavoro di produzione.

È possibile gestire i cluster Kubernetes [connessi alla sottoscrizione di un cliente con Azure Arc](../../azure-arc/kubernetes/connect-cluster.md), proprio come se fossero in esecuzione in Azure.

Se il cliente ha creato un [account dell'entità servizio per l'onboarding dei cluster Kubernetes in Azure Arc](../../azure-arc/kubernetes/create-onboarding-service-principal.md), è possibile accedere a questo account dell'entità servizio per l'onboarding e la gestione dei cluster. Questa operazione può essere eseguita dagli utenti nel tenant di gestione a cui è stato concesso il ruolo predefinito "cluster Kubernetes-Azure Arc onboarding" quando la sottoscrizione contenente l'account dell'entità servizio è stata caricata [in Azure Lighthouse](onboard-customer.md).

È possibile distribuire le [configurazioni](../../azure-arc/kubernetes/use-gitops-connected-cluster.md) e i [grafici Helm](../../azure-arc/kubernetes/use-gitops-with-helm.md) usando GitOps per i cluster connessi.

È anche possibile monitorare i cluster connessi con monitoraggio di Azure e [usare criteri di Azure per applicare le configurazioni del cluster su larga scala](../../azure-arc/kubernetes/use-azure-policy.md).

## <a name="next-steps"></a>Passaggi successivi

- Esplorare le informazioni introduttive e gli esempi nel [repository GitHub di Azure Arc](https://github.com/microsoft/azure_arc). 
- Informazioni sugli [scenari supportati per i server abilitati per Azure Arc](../../azure-arc/servers/overview.md#supported-scenarios).
- Informazioni sulle [distribuzioni Kubernetes supportate da Azure Arc](../../azure-arc/kubernetes/overview.md#supported-kubernetes-distributions).
- Informazioni su come [distribuire un criterio su larga scala](policy-at-scale.md).
- Informazioni su come [usare i log di monitoraggio di Azure su larga scala](monitor-at-scale.md).

