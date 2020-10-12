---
title: Come aggiornare le regole di avviso o le regole di azione quando la risorsa di destinazione si sposta in un'altra area di Azure
description: Informazioni di base e istruzioni su come aggiornare le regole di avviso o le regole di azione quando la risorsa di destinazione viene spostata in un'altra area di Azure.
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/26/2020
ms.subservice: alerts
ms.openlocfilehash: 4ea5c8552d35db67a1d2caf20c0143c74cdd642e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86505483"
---
# <a name="how-to-update-alert-rules-or-action-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>Come aggiornare le regole di avviso o le regole di azione quando la risorsa di destinazione si sposta in un'altra area di Azure

Questo articolo descrive il motivo per cui le regole di [avviso](./alerts-overview.md) e le [regole di azione](./alerts-action-rules.md) esistenti possono essere interessate quando si spostano altre risorse di Azure tra le aree e come identificare e risolvere tali problemi. Vedere la documentazione principale sullo [spostamento delle risorse](../../azure-resource-manager/management/move-region.md) per ulteriori informazioni su quando è utile spostare le risorse tra le aree e un elenco di controllo della progettazione di un processo di spostamento.

## <a name="why-the-problem-exists"></a>Perché il problema esiste

Le regole di avviso e le regole di azione fanno riferimento ad altre risorse di Azure. Gli esempi includono [macchine virtuali di Azure](../../site-recovery/azure-to-azure-tutorial-migrate.md), [SQL di Azure](../../azure-sql/database/move-resources-across-regions.md)e archiviazione di [Azure](../../storage/common/storage-account-move.md). Quando si spostano le risorse a cui fanno riferimento le regole, è probabile che le regole smettano di funzionare correttamente perché non riescono a trovare le risorse a cui fanno riferimento.

Esistono due motivi principali per cui le regole potrebbero smettere di funzionare dopo aver spostato le risorse di destinazione:

- L'ambito della regola è il riferimento esplicito alla risorsa precedente.
- La regola di avviso è basata sulle metriche.

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>L'ambito della regola si riferisce in modo esplicito alla risorsa precedente

Quando si sposta una risorsa, nella maggior parte dei casi cambia l'ID di risorsa. Dietro le quinte, il sistema replica la risorsa nella nuova area prima di eliminarla dall'area precedente. Questo processo richiede che due risorse e quindi due ID di risorsa diversi esistano simultaneamente per un periodo di tempo ridotto. Poiché gli ID di risorsa devono essere univoci, è necessario creare un nuovo ID durante il processo. 

**In che modo lo stato di trasferimento della risorsa influisce sulle regole esistenti?**

Le regole di avviso e le regole azione hanno un ambito di risorse a cui si applicano. L'ambito può essere un'intera sottoscrizione, un gruppo di risorse o una o più risorse specifiche.
Ad esempio, ecco una regola con un ambito con due risorse (due macchine virtuali):

![Regola di avviso per più risorse](media/alerts-resource-move/multi-resource-alert-rule.png)

Se l'ambito della regola indica in modo esplicito una risorsa e tale risorsa ha spostato e modificato l'ID della risorsa, la regola cercherà una risorsa errata o inesistente e quindi avrà esito negativo.

**Come risolvere il problema?**

Aggiornare o ricreare la regola interessata in modo che punti alla nuova risorsa. Il processo di aggiornamento dell'ambito è disponibile più avanti in questo articolo.

Il problema si applica a questi tipi di regole:

- Regole di avviso del log attività
- Regole di azione
- Avvisi classici
- Avvisi per le metriche: per altre informazioni, vedere le regole di avviso della sezione successiva [basate sulle metriche](#alert-rules-based-on-metrics).

> [!NOTE]
> Le regole di avviso di ricerca log e le regole di avviso per il rilevamento intelligente non sono interessate perché il relativo ambito è un'area di lavoro o Application Insights. Nessuno di questi ambiti supporta attualmente lo spostamento dell'area.

## <a name="alert-rules-based-on-metrics"></a>Regole di avviso basate sulle metriche

Le metriche emesse dalle risorse di Azure sono a livello di area. Ogni volta che una risorsa viene spostata in una nuova area, inizia a emettere le metriche nella nuova area. Di conseguenza, tutte le regole di avviso basate sulle metriche devono essere aggiornate o ricreate in modo che puntino al flusso della metrica corrente nell'area corretta.

Questa spiegazione si applica sia alle regole di [avviso delle metriche](alerts-metric-overview.md) che alle [regole di avviso del test di disponibilità](../app/monitor-web-app-availability.md).

Se **tutte** le risorse nell'ambito sono state spostate, non è necessario ricreare la regola. È possibile aggiornare solo qualsiasi campo della regola di avviso, ad esempio la descrizione della regola di avviso e salvarlo.
Se **solo alcune** risorse nell'ambito sono state spostate, è necessario rimuovere le risorse spostate dalla regola esistente e creare una nuova regola che copra solo le risorse spostate.

## <a name="procedures-to-fix-problems"></a>Procedure per la risoluzione dei problemi

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>Identificazione delle regole associate a una risorsa spostata dalla portale di Azure

- **Per le regole di avviso** , passare ad avvisi > gestire le regole di avviso > filtrare in base alla sottoscrizione contenitore e alla risorsa spostata.
> [!NOTE]
> Le regole di avviso del log attività non supportano questo processo. Non è possibile aggiornare l'ambito di una regola di avviso del log attività e fare in modo che punti a una risorsa in un'altra sottoscrizione. È invece possibile creare una nuova regola che sostituirà quella precedente.

- **Per le regole di azione** , passare ad avvisi > Gestisci azioni > regole di azione (anteprima) > filtrare per la sottoscrizione contenitore e la risorsa spostata.

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>Modificare l'ambito di una regola dalla portale di Azure

1. Aprire la regola identificata nel passaggio precedente facendo clic su di essa.
2. In **risorsa**fare clic su **modifica** e modificare l'ambito, se necessario.
3. Modificare le altre proprietà della regola in base alle esigenze.
4. Fare clic su **Salva**.

![Modificare l'ambito della regola di avviso](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>Modificare l'ambito di una regola utilizzando modelli di Azure Resource Manager

1. Ottenere il modello di Azure Resource Manager della regola.  Per esportare il modello di una regola dal portale di Azure:
   1. Passare alla sezione gruppi di risorse nel portale e aprire il gruppo di risorse che contiene la regola.
   2. Nella sezione Panoramica selezionare la casella di controllo **Mostra tipo nascosto** e filtrare in base al tipo pertinente della regola.
   3. Selezionare la regola pertinente per visualizzare i relativi dettagli.
   4. In **Impostazioni**selezionare **Esporta modello**.
2. Modificare il modello. Se necessario, suddividere in due regole (rilevanti per alcuni casi di avvisi delle metriche, come indicato in precedenza).
3. Ridistribuire il modello.

### <a name="change-scope-of-a-rule-using-rest-api"></a>Modificare l'ambito di una regola con l'API REST

1. Ottenere la regola esistente ([avvisi metrica](/rest/api/monitor/metricalerts/get), [avvisi del log attività](/rest/api/monitor/activitylogalerts/get))
2. Modificare l'ambito ([avvisi del log attività](/rest/api/monitor/activitylogalerts/update))
3. Ridistribuire la regola ([avvisi metrica](/rest/api/monitor/metricalerts/createorupdate), [avvisi del log attività](/rest/api/monitor/activitylogalerts/createorupdate))

### <a name="change-scope-of-a-rule-using-powershell"></a>Modificare l'ambito di una regola con PowerShell

1. Ottenere la regola esistente ([avvisi metrica](/powershell/module/az.monitor/get-azmetricalertrulev2), [avvisi del log attività](/powershell/module/az.monitor/get-azactivitylogalert), [regole di azione](/powershell/module/az.alertsmanagement/get-azactionrule)).
2. Modificare l'ambito. Se necessario, suddividere in due regole (rilevanti per alcuni casi di avvisi delle metriche, come indicato in precedenza).
3. Ridistribuire la regola ([avvisi metrica](/powershell/module/az.monitor/add-azmetricalertrulev2), [avvisi del log attività](/powershell/module/az.monitor/enable-azactivitylogalert), [regole di azione](/powershell/module/az.alertsmanagement/set-azactionrule)).

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>Modificare l'ambito di una regola usando l'interfaccia della riga di comando di Azure

1.  Ottenere la regola esistente ([avvisi metrica](/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-show), [avvisi del log attività](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)).
2.  Aggiornare direttamente l'ambito della regola ([avvisi metrica](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-update), [avvisi del log attività](/cli/azure/monitor/activity-log/alert/scope))
3.  Se necessario, suddividere in due regole (rilevanti per alcuni casi di avvisi delle metriche, come indicato in precedenza).

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla risoluzione di altri problemi con le [notifiche di avviso](alerts-troubleshoot.md), gli [avvisi delle metriche](alerts-troubleshoot-metric.md)e gli avvisi del [log](alerts-troubleshoot-log.md). 
