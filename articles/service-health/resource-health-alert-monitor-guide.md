---
title: Creare avvisi di Integrità risorse usando il portale di Azure
description: Creare un avviso usando portale di Azure che inviano una notifica quando le risorse di Azure diventano non disponibili.
ms.topic: conceptual
ms.date: 6/23/2020
ms.openlocfilehash: 127aad1691f7bb6b6c64332eefde734a809ab75f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540677"
---
# <a name="configure-resource-health-alerts-using-azure-portal"></a>Configurare avvisi di integrità risorse tramite il portale di Azure

Questo articolo illustra come configurare gli avvisi del log attività per le notifiche di integrità delle risorse usando il portale di Azure.

Integrità risorse di Azure comunica lo stato di integrità attuale e cronologico delle risorse di Azure. Integrità risorse di Azure invia una notifica quasi in tempo reale quando tali risorse subiscono una modifica al loro stato di integrità. Creare avvisi di Integrità risorse di Azure a livello di codice consente agli utenti di creare e personalizzare gli avvisi in blocco.

Le notifiche di integrità delle risorse vengono archiviate nel [log attività di Azure](../azure-monitor/platform/platform-logs-overview.md) , dato il notevole volume di informazioni archiviate nel log attività, è disponibile un'interfaccia utente separata per semplificare la visualizzazione e la configurazione degli avvisi per le notifiche di integrità delle risorse.
È possibile ricevere un avviso quando la risorsa di Azure invia notifiche sull'integrità delle risorse alla sottoscrizione di Azure. È possibile configurare l'avviso in base a:

* La sottoscrizione interessata.
* Tipi di risorse interessati.
* Il gruppo o i gruppi di risorse interessati.
* Risorse interessate.
* Lo stato o gli eventi delle risorse interessate.
* Le risorse hanno interessato gli Stati.
* Il tipo o i tipi di risorse interessati dalla risorsa.

È anche possibile configurare l'utente a cui deve essere inviato l'avviso:

* Selezionare un gruppo di azione esistente.
* Creare un nuovo gruppo di azione che può essere usato per avvisi futuri.

Per altre informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi di azioni](../azure-monitor/platform/action-groups.md).

Per informazioni su come configurare gli avvisi di notifica di integrità delle risorse usando Azure Resource Manager modelli, vedere [modelli di gestione risorse](./resource-health-alert-arm-template-guide.md).
Integrità risorse avviso utilizzando portale di Azure

## <a name="resource-health-alert-using-azure-portal"></a>Avviso Integrità risorse tramite il portale di Azure

1. Nel [portale](https://portal.azure.com/)di Azure selezionare **integrità del servizio**.

    ![Selezione dell'integrità del servizio](./media/resource-health-alert-monitor-guide/service-health-selection.png)
2. Nella sezione **integrità risorse** selezionare integrità del **servizio**.
3. Selezionare **Aggiungi avviso integrità risorse** e compilare i campi.
4. In destinazione avviso selezionare la **sottoscrizione**, i **tipi di risorse**, i **gruppi di risorse** e la **risorsa** per cui si vuole ricevere un avviso.

    ![Selezione selezione destinazione](./media/resource-health-alert-monitor-guide/alert-target.png)

5. In condizione di avviso selezionare:
    1. **Stato dell'evento** per il quale si desidera ricevere un avviso. Livello di gravità dell'evento: attivo, risolto, in corso, aggiornato
    2. Lo **stato della risorsa** per cui si desidera ricevere un avviso. Lo stato della risorsa dell'evento: disponibile, non disponibile, sconosciuto, danneggiato
    3. **Tipo di motivo** per cui si desidera ricevere un avviso. La motivo dell'evento: avvio della piattaforma, selezione della condizione di avviso avviata dall'utente ![ selezione dello stato](./media/resource-health-alert-monitor-guide/alert-condition.png)
6. In Definire i dettagli dell'avviso fornire i dettagli seguenti:
    1. **Nome regola di avviso**: nome della nuova regola di avviso.
    2. **Descrizione**: descrizione della nuova regola di avviso.
    3. **Salva avviso nel gruppo di risorse**: selezionare il gruppo di risorse in cui salvare la nuova regola.
7. In **Gruppo di azioni**, specificare il gruppo di azioni da assegnare a questa nuova regola di avviso dal menu a discesa. In alternativa, [creare un nuovo gruppo di azioni](../azure-monitor/platform/action-groups.md) e assegnarlo alla nuova regola. Per creare un nuovo gruppo, selezionare + **nuovo gruppo**.
8. Per abilitare le regole dopo averle create, selezionare **Sì** per l'opzione **Abilita regola alla creazione**.
9. Selezionare **Crea regola di avviso**.

Viene creata la nuova regola di avviso per il log attività e nell'angolo superiore destro viene visualizzato un messaggio di conferma.
È possibile abilitare, disabilitare, modificare o eliminare una regola. Altre informazioni su [come gestire le regole del log attività](../azure-monitor/platform/alerts-activity-log.md#view-and-manage-in-the-azure-portal).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su Integrità risorse:

* [Panoramica di Integrità risorse di Azure](Resource-health-overview.md)
* [Tipi di risorse e controlli di integrità disponibili tramite Integrità risorse di Azure](resource-health-checks-resource-types.md)

Creare avviso di integrità dei servizi di Azure:

* [Configurare gli avvisi per Integrità dei servizi di Azure](./alerts-activity-log-service-notifications-portal.md) 
* [Schema degli eventi del log attività di Azure](../azure-monitor/platform/activity-log-schema.md)
* [Configurare avvisi di integrità risorse di Azure tramite modelli di Gestione risorse](./resource-health-alert-arm-template-guide.md)
