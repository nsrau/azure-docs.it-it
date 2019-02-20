---
title: Informazioni dettagliate sul gruppo di risorse di Monitoraggio di Azure | Microsoft Docs
description: Informazioni sull'integrità e le prestazioni delle applicazioni e dei servizi distribuiti a livello di gruppo di risorse con Monitoraggio di Azure
services: azure-monitor
author: NumberByColors
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: d5c07e0d4aca8bda42ea9f78a1475ea7bb5861f0
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997023"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Monitorare i gruppi di risorse con Monitoraggio di Azure (anteprima)

Le applicazioni moderne sono spesso complesse e altamente distribuite, con molti componenti distinti che si integrano per fornire un servizio. Per gestire questa complessità, Monitoraggio di Azure fornisce informazioni dettagliate specifiche per i gruppi di risorse. In questo modo è possibile analizzare e diagnosticare con facilità i problemi riscontrati dalle singole risorse, offrendo al contempo un contesto dell'integrità e delle prestazioni del gruppo di risorse e dell'applicazione nel suo complesso.

## <a name="access-insights-for-resource-groups"></a>Accedere alle informazioni dettagliate per i gruppi di risorse

1. Selezionare **Gruppi di risorse** sulla barra di spostamento a sinistra.
2. Selezionare uno dei gruppi di risorse da esplorare. Se il numero di gruppi di risorse è elevato, talvolta può essere utile applicare un filtro in base alla sottoscrizione.
3. Per accedere alle informazioni dettagliate per un gruppo di risorse, fare clic su **Informazioni dettagliate** nel menu a sinistra del gruppo.

![Screenshot della pagina di panoramica delle informazioni dettagliate per i gruppi di risorse](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Risorse con avvisi attivi e problemi di integrità

La pagina di panoramica mostra il numero di avvisi attivati e ancora attivi, con le lo stato attuale di Integrità risorse di Azure per ogni risorsa. Tutte queste informazioni consentono di individuare rapidamente le risorse per cui si stanno verificando problemi. Gli avvisi consentono di rilevare i problemi nel codice e come è stata configurata l'infrastruttura. Integrità risorse di Azure rileva problemi con la piattaforma di Azure, che non sono specifici per le singole applicazioni.

![Screenshot del riquadro Integrità risorse di Azure](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Integrità risorse di Azure

Per visualizzare Integrità risorse di Azure, selezionare la casella **Mostra integrità delle risorse di Azure** sopra la tabella. Questa colonna è nascosta per impostazione predefinita per consentire un rapido caricamento della pagina.

![Screenshot con grafico dell'integrità delle risorse aggiunto](./media/resource-group-insights/0003-overview.png)

Per impostazione predefinita, le risorse sono raggruppate per livello di app e tipo di risorsa. **Livello di app** è una categorizzazione semplice dei tipi di risorse, che esiste solo nel contesto della pagina di panoramica delle informazioni dettagliate sulle risorse. Sono disponibili tipi di risorse per codice dell'applicazione, infrastruttura di calcolo, reti, archiviazione e database. Gli strumenti di gestione ricevono livelli di app personalizzati e ogni altra risorsa viene categorizzata come appartenente al livello app **Altro**. Questo raggruppamento è utile per esaminare in modo immediato i sottosistemi integri e non integri dell'applicazione.

## <a name="diagnose-issues-in-your-resource-group"></a>Diagnosticare i problemi nel gruppo di risorse

Nella pagina delle informazioni dettagliate sul gruppo di risorse sono disponibili diversi altri strumenti per la diagnostica dei problemi.

   |         |          |
   | ---------------- |:-----|
   | [**Avvisi**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)      |  Consente di visualizzare, creare e gestire gli avvisi. |
   | [**Metriche**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) | Consente di visualizzare ed esplorare i dati in base alle metriche.    |
   | [**Log attività**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) | Indicano gli eventi a livello di sottoscrizione che si sono verificati in Azure.  |
   | [**Mappa delle applicazioni**](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) | Consente di esplorare la topologia dell'applicazione distribuita per identificare i colli di bottiglia delle prestazioni o le aree sensibili agli errori. |

## <a name="failures-and-performance"></a>Errori e prestazioni

Come procedere quando l'applicazione è lenta o gli utenti hanno segnalato errori? La ricerca in tutte le risorse per isolare i problemi richiede molto tempo.

Le schede **Prestazioni** ed **Errori** semplificano questo processo combinando le viste diagnostiche delle prestazioni e degli errori per molti tipi di risorse comuni.

Per la maggior parte dei tipi di risorse verrà aperta una raccolta di modelli di cartelle di lavoro di Monitoraggio di Azure. Ogni cartella di lavoro creata può essere personalizzata, salvata, condivisa con il team e riutilizzata in futuro per diagnosticare problemi simili.

### <a name="investigate-failures"></a>Esaminare gli errori

Per provare la scheda Errori, selezionare **Errori** in **Esamina** nel menu a sinistra.

Dopo avere effettuato la selezione, la barra dei menu a sinistra cambia, rendendo disponibili nuove opzioni.

![Screenshot del riquadro di panoramica degli errori](./media/resource-group-insights/00004-failures.png)

Quando si sceglie il servizio app, verrà visualizzata una raccolta di modelli di cartelle di lavoro di Monitoraggio di Azure.

![Screenshot della raccolta di cartella di lavoro dell'applicazione](./media/resource-group-insights/0005-failure-insights-workbook.png)

Scegliendo il modello per le informazioni dettagliate sugli errori verrà visualizzata la cartella di lavoro.

![Screenshot del report degli errori](./media/resource-group-insights/0006-failure-visual.png)

È possibile selezionare una delle righe. La selezione viene quindi rappresentata in una vista dei dettagli in formato grafico.

![Screenshot dei dettagli degli errori](./media/resource-group-insights/0007-failure-details.png)

Con le cartelle di lavoro non è più necessario eseguire attività difficili come creare visualizzazioni e report personalizzati in un formato facilmente utilizzabile. Alcuni utenti possono voler regolare solo i parametri predefiniti, ma è possibile personalizzare completamente le cartelle di lavoro.

Per avere un'idea del funzionamento interno della cartella di lavoro, selezionare **Modifica** nella barra superiore.

![Screenshot dell'opzione di modifica aggiuntiva](./media/resource-group-insights/0008-failure-edit.png)

Vengono visualizzate numerose caselle **Modifica** accanto ai vari elementi della cartella di lavoro. Selezionare la casella **Modifica** sotto la tabella delle operazioni.

![Screenshot delle caselle di modifica](./media/resource-group-insights/0009-failure-edit-graph.png)

Viene mostrata la query di log sottostante alla base della visualizzazione della tabella.

 ![Screenshot della finestra della query di log](./media/resource-group-insights/0010-failure-edit-query.png)

È possibile modificare direttamente la query. In alternativa è possibile usarla come riferimento e prenderla in prestito da qui quando si progetta una cartella di lavoro con parametri personalizzati.

### <a name="investigate-performance"></a>Esaminare le prestazioni

Viene visualizzata automaticamente una raccolta di cartelle di lavoro. Per il servizio app la cartella di lavoro predefinita delle prestazioni applicative offre la vista seguente:

 ![Screenshot della vista delle prestazioni](./media/resource-group-insights/0011-performance.png)

In questo caso, se si seleziona l'opzione di modifica si noterà che questo set di visualizzazioni si basa sulle metriche di Monitoraggio di Azure.

 ![Screenshot della vista delle prestazioni con Metriche di Azure](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="enabling-access-to-alerts"></a>Abilitazione dell'accesso agli avvisi

Per visualizzare gli avvisi in Monitoraggio di Azure per i gruppi di risorse, un utente con ruolo Proprietario o Collaboratore per questa sottoscrizione deve aprire Monitoraggio di Azure per i gruppi di risorse per qualsiasi gruppo di risorse nella sottoscrizione. Ciò consentirà a tutti gli utenti con accesso in lettura di visualizzare gli avvisi in Monitoraggio di Azure per i gruppi di risorse per tutti i gruppi di risorse nella sottoscrizione. Se si ha il ruolo Proprietario o Collaboratore, aggiornare questa pagina tra qualche minuto.

Monitoraggio di Azure per i gruppi di risorse si basa sul sistema Gestione avvisi di Monitoraggio di Azure per recuperare lo stato dell'avviso. Gestione avvisi non è configurata per impostazione predefinita per ogni gruppo di risorse e sottoscrizione e può essere abilitata solo da un utente con ruolo Proprietario o Collaboratore. Può essere abilitata in uno dei modi seguenti:
* Aprire Monitoraggio di Azure per i gruppi di risorse per qualsiasi gruppo di risorse nella sottoscrizione.
* In alternativa, passare alla sottoscrizione, fare clic su **Provider di risorse** e quindi su **Register for Alerts.Management** (Iscriviti a Gestione avvisi).

## <a name="next-steps"></a>Passaggi successivi

- [Cartelle di lavoro di Monitoraggio di Azure](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)
- [Integrità risorse di Azure](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Avvisi di Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)
