---
title: Rilevare i problemi dei dispositivi in una soluzione di monitoraggio remoto basata su Azure | Microsoft Docs
description: Questa esercitazione mostra come usare regole e azioni per rilevare automaticamente problemi dei dispositivi in base alle soglie nella soluzione di monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 1e3eaeec1d2eae3c36f285a3e4c536657504cbb8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098482"
---
# <a name="tutorial-detect-issues-with-devices-connected-to-your-monitoring-solution"></a>Esercitazione: rilevare i problemi con i dispositivi connessi alla soluzione di monitoraggio

In questa esercitazione si configura l'acceleratore della soluzione di monitoraggio remoto per rilevare i problemi con i dispositivi IoT connessi. Per rilevare i problemi con i dispositivi, vengono aggiunte regole che generano avvisi nel dashboard della soluzione.

Per inserire regole e avvisi, l'esercitazione usa un dispositivo refrigeratore simulato. Il refrigeratore è gestito da un'organizzazione denominata Contoso ed è connesso all'acceleratore della soluzione di monitoraggio remoto. Contoso ha già implementato una regola che genera un allarme critico quando la pressione in un refrigeratore supera 298 PSI. In qualità di operatore in Contoso, è necessario identificare i dispositivi refrigeratore che possono avere sensori problematici osservando i picchi di pressione iniziali. Per identificare tali dispositivi, si aggiunge una regola che genera un messaggio di avviso quando la pressione nel refrigeratore supera 150 PSI.

Inoltre, è stato richiesto di creare un avviso critico per un refrigeratore quando, negli ultimi cinque minuti, l'umidità media del dispositivo è stata superiore all'80% e la temperatura del dispositivo è stata superiore a 75 gradi Fahrenheit.

In questa esercitazione:

>[!div class="checklist"]
> * Visualizzare le regole nella soluzione
> * Creare una regola
> * Creare una regola con più condizioni
> * Modificare una regola esistente
> * Attivare e disattivare le regole

## <a name="prerequisites"></a>prerequisiti

Per seguire questa esercitazione, è necessaria un'istanza distribuita dell'acceleratore della soluzione di monitoraggio remoto nella sottoscrizione di Azure.

Se l'acceleratore della soluzione di monitoraggio remoto non è stato ancora distribuito, completare la guida introduttiva [Distribuire la soluzione di monitoraggio remoto basata su cloud](quickstart-remote-monitoring-deploy.md).

## <a name="view-the-existing-rules"></a>Visualizzare le regole esistenti

La pagina **Regole** nell'acceleratore di soluzione mostra un elenco di tutte le regole correnti:

[![Pagina delle regole](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-expanded.png#lightbox)

Per visualizzare solo le regole valide per i dispositivi refrigeratore, applicare un filtro:

[![Filtrare l'elenco delle regole](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2-expanded.png#lightbox)

Quando si seleziona una regola nell'elenco, è possibile visualizzare altre informazioni sulla regola o modificarla:

[![Visualizzare i dettagli delle regole](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-expanded.png#lightbox)

Per disabilitare o abilitare una o più regole, selezionare una o più regole nell'elenco:

[![Selezionare più regole](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2-expanded.png#lightbox)

## <a name="create-a-rule"></a>Creare una regola

Per creare una regola che generi un avviso quando la pressione in un dispositivo refrigeratore è maggiore di 150 PSI, fare clic su **Nuova regola**. Usare i valori seguenti per creare la regola:

| Impostazione          | Valore                                 |
| ---------------- | ------------------------------------- |
| Nome regola        | Chiller warning (Avviso refrigeratore)                       |
| DESCRIZIONE      | Chiller pressure has exceeded 150 PSI (La pressione del refrigeratore è maggiore di 150 PSI) |
| Gruppo di dispositivi     | Gruppo di dispositivi **Chillers** (Refrigeratori)             |
| Calcolo      | Istantaneo                               |
| Campo condizione 1| pressure                              |
| Operatore condizione 1 | Maggiore di                      |
| Valore condizione 1    | 150                               |
| Livello di gravità  | Avviso                               |

[![Creare regola di avviso](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-expanded.png#lightbox)

Per salvare la nuova regola, fare clic su **Applica**.

È possibile vedere quando la regola viene attivata nella pagina **Regole** o nella pagina **Dashboard**:

[![Regola di avviso attivata](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-expanded.png#lightbox)

## <a name="create-a-rule-with-multiple-conditions"></a>Creare una regola con più condizioni

Per creare una regola con più condizioni che genera un avviso critico quando negli ultimi cinque minuti per un dispositivo refrigeratore l'umidità media è superiore a 80% e la temperatura media è superiore a 75 gradi fahrenheit, fare clic su **Nuova regola** . Usare i valori seguenti per creare la regola:

| Impostazione          | Valore                                 |
| ---------------- | ------------------------------------- |
| Nome regola        | Umidità refrigeratore e temperatura critiche    |
| DESCRIZIONE      | Temperatura e umidità sono critiche |
| Gruppo di dispositivi     | Gruppo di dispositivi **Chillers** (Refrigeratori)             |
| Calcolo      | Media                               |
| Periodo di tempo      | 5                                     |
| Campo condizione 1| umidità                              |
| Operatore condizione 1 | Maggiore di                      |
| Valore condizione 1    | 80                                |
| Livello di gravità  | Critico                              |

[![Creare regola con più condizioni prima parte](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-expanded.png#lightbox)

Per aggiungere la seconda condizione, fare clic su "+ Add condition" (+ Aggiungi condizione). Utilizzare i seguenti valori per la nuova condizione:

| Impostazione          | Valore                                 |
| ---------------- | ------------------------------------- |
| Campo condizione 2| temperatura                           |
| Operatore condizione 2 | Maggiore di                      |
| Valore condizione 2    | 75                                |

[![Creare regola con più condizioni seconda parte](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-expanded.png#lightbox)

Per salvare la nuova regola, fare clic su **Applica**.

È possibile vedere quando la regola viene attivata nella pagina **Regole** o nella pagina **Dashboard**:

[![Regola con più condizioni attivata](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-expanded.png#lightbox)

## <a name="edit-an-existing-rule"></a>Modificare una regola esistente

Per apportare una modifica a una regola esistente, selezionare la regola nell'elenco delle regole e fare clic su **Modifica**:

[![Modifica regola](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-expanded.png#lightbox)

## <a name="disable-a-rule"></a>Disabilitare una regola

Per disattivare temporaneamente una regola, è possibile disabilitarla nell'elenco delle regole. Scegliere la regola da disabilitare e quindi scegliere **Disable** (Disabilita). Il valore di **Status** (Stato) per la regole nell'elenco cambia per indicare che la regola è ora disabilitata. È possibile riabilitare una regola precedentemente disabilitata usando la stessa procedura.

[![Disabilitare una regola](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-expanded.png#lightbox)

È possibile attivare e disattivare più regole contemporaneamente selezionando più regole nell'elenco.

<!-- ## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare all'esercitazione successiva, lasciare l'acceleratore della soluzione di monitoraggio remoto distribuito. Per ridurre i costi di esecuzione dell'acceleratore della soluzione mentre non è in uso, è possibile arrestare i dispositivi simulati nel pannello delle impostazioni:

[![Sospendere la telemetria](./media/iot-accelerators-remote-monitoring-automate/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-automate/togglesimulation-expanded.png#lightbox)

Quando si è pronti per iniziare l'esercitazione successiva, è possibile riavviare i dispositivi simulati.

Se l'acceleratore della soluzione non è più necessario, eliminarlo dalla pagina [Soluzioni di cui è stato effettuato il provisioning](https://www.azureiotsolutions.com/Accelerators#dashboard):

![Eliminare la soluzione](media/iot-accelerators-remote-monitoring-automate/deletesolution.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha mostrato come utilizzare la pagina **Regole** nell'acceleratore della soluzione di monitoraggio remoto per creare e gestire regole che attivano gli avvisi nella soluzione. Per informazioni su come usare l'acceleratore della soluzione per gestire e configurare i dispositivi collegati, passare all'esercitazione successiva.

> [!div class="nextstepaction"]
> [Configurare e gestire i dispositivi connessi alla soluzione di monitoraggio](iot-accelerators-remote-monitoring-manage.md)