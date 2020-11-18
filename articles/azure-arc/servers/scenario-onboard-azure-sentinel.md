---
title: Caricare il server Azure Arc abilitato in Sentinel di Azure
description: Informazioni su come aggiungere i server abilitati per Azure Arc a Sentinel di Azure e monitorare in modo proattivo lo stato di sicurezza.
ms.date: 11/16/2020
ms.topic: conceptual
ms.openlocfilehash: 60018e710613a27bcb5c00f38ee90be6ff46c61d
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94811052"
---
# <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Caricare i server abilitati per Azure Arc in Sentinel di Azure

Questo articolo ha lo scopo di aiutare l'utente a caricare il server abilitato per Azure Arc in [Sentinel di Azure](../../sentinel/overview.md) e iniziare a raccogliere gli eventi correlati alla sicurezza. Azure Sentinel offre un'unica soluzione per il rilevamento degli avvisi, la visibilità delle minacce, la ricerca proattiva e la risposta alle minacce nell'intera azienda.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che siano soddisfatti i requisiti seguenti:

- Un'[area di lavoro Log Analytics](../../azure-monitor/platform/data-platform-logs.md). Per altre informazioni sulle aree di lavoro Log Analytics, vedere [Progettazione della distribuzione dei log di Monitoraggio di Azure](../../azure-monitor/platform/design-logs-deployment.md).

- Azure Sentinel è [abilitato nella sottoscrizione](../../sentinel/quickstart-onboard.md).

- Il computer o il server è connesso ai server abilitati per Azure Arc.

## <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Caricare i server abilitati per Azure Arc in Sentinel di Azure

Azure Sentinel è dotato di un numero di connettori per le soluzioni Microsoft, disponibile in modalità predefinita e fornisce l'integrazione in tempo reale. Per i computer fisici e le macchine virtuali, è possibile installare l'agente di Log Analytics che raccoglie i log e li inoltra ad Azure Sentinel. I server abilitati per Arc supportano la distribuzione dell'agente di Log Analytics usando i metodi seguenti:

- Uso del framework delle estensioni VM.

    Questa funzionalità in Azure Arc Enabled Server consente di distribuire l'estensione di macchina virtuale Log Analytics Agent in un server Windows e/o Linux non Azure. Le estensioni di macchina virtuale possono essere gestite usando i metodi seguenti nei computer ibridi o nei server gestiti da server abilitati per Arc:

    - Il[portale di Azure](manage-vm-extensions-portal.md)
    - L'[interfaccia della riga di comando di Azure](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - [Modelli di gestione risorse](manage-vm-extensions-template.md) di Azure

- Uso di criteri di Azure.

    Con questo approccio, è possibile usare i criteri di [distribuzione di log Analytics Agent di Azure Policy to Linux o Windows Azure Arc](../../governance/policy/samples/built-in-policies.md#monitoring) per controllare se nel server abilitato per Arc è installato log Analytics Agent. Se l'agente non è installato, lo distribuisce automaticamente utilizzando un'attività di correzione. In alternativa, se si prevede di monitorare i computer con Monitoraggio di Azure per le macchine virtuali, usare invece l'iniziativa [abilita monitoraggio di Azure per le macchine virtuali](../../governance/policy/samples/built-in-initiatives.md#monitoring) per installare e configurare l'agente di log Analytics.

Si consiglia di installare l'agente di Log Analytics per Windows o Linux usando criteri di Azure.

Dopo che i server abilitati per Arc sono connessi, i dati iniziano a eseguire lo streaming in Sentinel di Azure ed è possibile iniziare a usare. È possibile visualizzare i log nelle [cartelle di lavoro predefinite](../../sentinel/quickstart-get-visibility.md) e iniziare a creare le query in Log Analytics per [esaminare i dati](../../sentinel/tutorial-investigate-cases.md).

## <a name="next-steps"></a>Passaggi successivi

Iniziare a [rilevare minacce con Azure Sentinel](../../sentinel/tutorial-detect-threats-built-in.md).