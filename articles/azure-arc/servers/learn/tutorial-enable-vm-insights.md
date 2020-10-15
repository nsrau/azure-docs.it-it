---
title: 'Esercitazione: monitorare una macchina virtuale ibrida con Monitoraggio di Azure per le macchine virtuali'
description: Informazioni su come raccogliere e analizzare i dati da una macchina virtuale ibrida in Monitoraggio di Azure.
ms.topic: tutorial
ms.date: 09/23/2020
ms.openlocfilehash: 97ab390570f434295a5aa836ef994640f6dc14f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335416"
---
# <a name="tutorial-monitor-a-hybrid-machine-with-azure-monitor-for-vms"></a>Esercitazione: Monitorare una macchina virtuale ibrida con Monitoraggio di Azure per le macchine virtuali

[Monitoraggio di Azure](../overview.md) può raccogliere dati direttamente dalle macchine virtuali ibride in un'area di lavoro Log Analytics per l'analisi dettagliata e la correlazione. Questa operazione comporta in genere l'installazione dell'[agente di Log Analytics](../../../azure-monitor/platform/agents-overview.md#log-analytics-agent) nella macchina virtuale usando uno script, manualmente o un metodo automatico a seconda degli standard di gestione della configurazione. Nei server abilitati per Arc è stato introdotto di recente il supporto per installare le [estensioni macchina virtuale](../manage-vm-extensions.md) agente di Log Analytics e Dependency Agent per Windows e Linux, per consentire a Monitoraggio di Azure di raccogliere i dati dalle macchine virtuali non di Azure.

Questa esercitazione illustra come configurare e raccogliere i dati dalle macchine virtuali Linux o Windows abilitando Monitoraggio di Azure per le macchine virtuali seguendo un set di procedure facilitate, che semplificano l'esperienza e impiegano meno tempo.  

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* La funzionalità Estensione macchina virtuale è disponibile solo nell'elenco delle [aree supportate](../overview.md#supported-regions).

* Vedere [Sistemi operativi supportati](../../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) per assicurarsi che il sistema operativo delle macchine virtuali da abilitare sia supportato da Monitoraggio di Azure per le macchine virtuali.

* Esaminare i requisiti del firewall per l'agente di Log Analytics indicati in [Panoramica dell'agente di Log Analytics](../../../azure-monitor/platform/log-analytics-agent.md#network-requirements). Dependency Agent per la mappa di Monitoraggio di Azure per le macchine virtuali non trasmette dati e non richiede modifiche ai firewall o alle porte.

## <a name="sign-in-to-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="enable-azure-monitor-for-vms"></a>Abilita Monitoraggio di Azure per le macchine virtuali

1. Avviare il servizio Azure Arc nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Macchine virtuali - Azure Arc**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Cercare i server abilitati per Arc in Tutti i servizi" border="false":::

1. Nella pagina **Macchine virtuali - Azure Arc** selezionare la macchina virtuale connessa creato nell'articolo dell'[avvio rapido](quick-enable-hybrid-vm.md).

1. Dal riquadro a sinistra nella sezione **Monitoraggio** selezionare **Informazioni dettagliate** e quindi **Abilita**.

    :::image type="content" source="./media/tutorial-enable-vm-insights/insights-option.png" alt-text="Cercare i server abilitati per Arc in Tutti i servizi" border="false":::

1. Nella pagina **Onboarding di Informazioni dettagliate** di Monitoraggio di Azure verrà richiesto di creare un'area di lavoro. Per questa esercitazione, non è consigliabile selezionare un'area di lavoro Log Analytics esistente se ne è già presente una. Selezionare l'impostazione predefinita,ovvero un'area di lavoro con un nome univoco nella stessa area della macchina virtuale connessa registrata. Questa area di lavoro viene creata e configurata automaticamente.

    :::image type="content" source="./media/tutorial-enable-vm-insights/enable-vm-insights.png" alt-text="Cercare i server abilitati per Arc in Tutti i servizi" border="false":::

1. Durante la configurazione, si riceveranno messaggi di stato. Questo processo richiede alcuni minuti per l'installazione delle estensioni nella macchina virtuale connessa.

    :::image type="content" source="./media/tutorial-enable-vm-insights/onboard-vminsights-vm-portal-status.png" alt-text="Cercare i server abilitati per Arc in Tutti i servizi" border="false":::

    Al termine, si riceverà un messaggio indicante che l'onboarding della macchina virtuale è stato completato e che le Informazioni dettagliate sono state distribuite.

## <a name="view-data-collected"></a>Visualizzare i dati raccolti

Al termine della distribuzione e della configurazione, selezionare **Informazioni dettagliate**, quindi selezionare la scheda **Prestazioni**. Nella scheda Prestazioni verrà visualizzato un gruppo selezionato di contatori delle prestazioni raccolti dal sistema operativo guest della macchina virtuale. Scorrere verso il basso per visualizzare altri contatori e spostare il puntatore del mouse su un grafico per visualizzare i valori di media e percentile acquisiti a partire dal momento in cui è stata installata l'estensione macchina virtuale Log Analytics nella macchina virtuale.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-performance-charts.png" alt-text="Cercare i server abilitati per Arc in Tutti i servizi" border="false":::

Selezionare **Mappa** per aprire la funzionalità mappe che mostra i processi in esecuzione nella macchina virtuale e le relative dipendenze. Selezionare **Proprietà** per aprire il riquadro delle proprietà, se non è già aperto.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-map.png" alt-text="Cercare i server abilitati per Arc in Tutti i servizi" border="false":::

Espandere i processi della macchina virtuale. Selezionare uno dei processi per visualizzarne i dettagli ed evidenziarne le dipendenze.

Selezionare di nuovo la macchina virtuale e quindi **Eventi del log**. Verrà visualizzato un elenco delle tabelle archiviate nell'area di lavoro Log Analytics per la macchina virtuale. L'elenco sarà diverso a seconda che si usi una macchina virtuale Windows o Linux. Selezionare la tabella **Evento**, che include tutti gli **eventi** del registro eventi di Windows. Verrà aperto Log Analytics con una query semplice per recuperare le voci del registro eventi.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Monitoraggio di Azure, vedere l'articolo seguente:

> [!div class="nextstepaction"]
> [Panoramica di Monitoraggio di Azure](../../../azure-monitor/overview.md)