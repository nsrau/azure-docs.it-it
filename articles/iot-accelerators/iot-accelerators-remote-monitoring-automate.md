---
title: 'Esercitazione: Rilevare i problemi dei dispositivi in una soluzione di monitoraggio remoto basata su Azure | Microsoft Docs'
description: Questa esercitazione mostra come usare regole e azioni per rilevare automaticamente problemi dei dispositivi in base alle soglie nella soluzione di monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 7ebe81565bc6bd28747f3a251b39a3c010493307
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2018
ms.locfileid: "51819101"
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

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="review-the-existing-rules"></a>Esaminare le regole esistenti

La pagina **Regole** nell'acceleratore di soluzione mostra un elenco di tutte le regole correnti:

[![Pagina delle regole](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-expanded.png#lightbox)

Per visualizzare solo le regole valide per i dispositivi refrigeratore, applicare un filtro. Quando si seleziona una regola nell'elenco, è possibile visualizzare altre informazioni sulla regola o modificarla:

[![Visualizzare i dettagli delle regole](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-expanded.png#lightbox)

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

## <a name="create-an-advanced-rule"></a>Creare una regola avanzata

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

Per disattivare temporaneamente una regola, è possibile disabilitarla nell'elenco delle regole. Selezionare la regola da disabilitare e quindi scegliere **Disable** (Disabilita). Il valore di **Status** (Stato) per la regole nell'elenco cambia per indicare che la regola è ora disabilitata. È possibile riabilitare una regola precedentemente disabilitata usando la stessa procedura.

[![Disabilitare una regola](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-expanded.png#lightbox)

È possibile attivare e disattivare più regole contemporaneamente selezionando più regole nell'elenco.

## <a name="delete-a-rule"></a>Eliminare una regola

Per eliminare definitivamente una regola, è possibile eliminarla nell'elenco di regole. Selezionare la regola da eliminare e quindi scegliere **Delete** (Elimina).

[![Eliminare una regola](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdelete-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdelete-expanded.png#lightbox)

Dopo aver confermato che si vuole eliminare la regola, l'utente ha la possibilità di eliminare tutti gli avvisi associati alla regola dalla pagina **Manutenzione**.

[![Eliminare una regola](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdeletetidy-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdeletetidy-expanded.png#lightbox)

È possibile eliminare solo una regola alla volta.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha mostrato come utilizzare la pagina **Regole** nell'acceleratore della soluzione di monitoraggio remoto per creare e gestire regole che attivano gli avvisi nella soluzione. Per informazioni su come usare l'acceleratore della soluzione per gestire e configurare i dispositivi collegati, passare all'esercitazione successiva.

> [!div class="nextstepaction"]
> [Configurare e gestire i dispositivi connessi alla soluzione di monitoraggio](iot-accelerators-remote-monitoring-manage.md)