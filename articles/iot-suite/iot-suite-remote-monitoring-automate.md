---
title: Rilevare i problemi dei dispositivi nella soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questa esercitazione mostra come usare regole e azioni per rilevare automaticamente problemi dei dispositivi in base alle soglie nella soluzione di monitoraggio remoto.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 5acf35ed19a5b6baa2885fd58cfb7fbbe1ac3cd8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33774653"
---
# <a name="detect-issues-using-threshold-based-rules"></a>Rilevare i problemi usando regole basate su soglie

Questa esercitazione mostra le funzionalità del motore regole nella soluzione di monitoraggio remoto. Per presentare queste funzionalità, l'esercitazione usa uno scenario nell'applicazione IoT Contoso.

Contoso ha implementato una regola che genera un allarme critico quando la pressione segnalata da un dispositivo **Chiller** (Refrigeratore) è maggiore di 250 PSI. In qualità di operatore, è necessario identificare i dispositivi **Chiller** (Refrigeratore) che possono avere sensori problematici osservando i picchi di pressione iniziali. Per identificare questi dispositivi, è necessario creare una regola per generare un avviso quando la pressione è maggiore di 150 PSI.

Si sa inoltre che un avviso critico deve essere attivato quando l'umidità media del dispositivo **Chiller** (Refrigeratore) negli ultimi 5 minuti è maggiore dell'80% e la temperatura del dispositivo **Chiller** (Refrigeratore) negli ultimi 5 minuti è superiore a 75 gradi Fahrenheit.

In questa esercitazione si apprenderà come:

>[!div class="checklist"]
> * Visualizzare le regole nella soluzione
> * Creare una nuova regola
> * Creare una nuova regola con più condizioni
> * Modificare una regola esistente
> * Eliminare una regola

## <a name="prerequisites"></a>prerequisiti

Per seguire questa esercitazione, è necessaria un'istanza distribuita della soluzione di monitoraggio remoto nella sottoscrizione di Azure.

Se la soluzione di monitoraggio remoto non è stata ancora distribuita, completare l'esercitazione [Distribuire l'acceleratore di soluzioni di monitoraggio remoto](iot-suite-remote-monitoring-deploy.md).

## <a name="view-the-rules-in-your-solution"></a>Visualizzare le regole nella soluzione

La pagina **Rules** (Regole) visualizza un elenco di tutte le regole correnti:

![Pagina Rules & Actions (Regole e azioni)](media/iot-suite-remote-monitoring-automate/rulesactions_v2.png)

Per visualizzare solo le regole valide per i dispositivi **Chiller** (Refrigeratore), applicare un filtro:

![Filtrare l'elenco delle regole](media/iot-suite-remote-monitoring-automate/rulesactionsfilter_v2.png)

Quando si seleziona una regola nell'elenco, è possibile visualizzare altre informazioni sulla regola o modificarla:

![Visualizzare i dettagli delle regole](media/iot-suite-remote-monitoring-automate/rulesactionsdetail_v2.png)

Per disabilitare, abilitare o eliminare una o più regole, selezionare più regole nell'elenco:

![Selezionare più regole](media/iot-suite-remote-monitoring-automate/rulesactionsmultiselect_v2.png)

## <a name="create-a-new-rule"></a>Creare una nuova regola

Per aggiungere una nuova regola che generi un avviso quando la pressione in un dispositivo **Chiller** (Refrigeratore) è maggiore di 150 PSI, scegliere **New rule** (Nuova regola):

![Creare una regola](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule_v2.png)

Usare i valori seguenti per creare la regola:

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

Per salvare la nuova regola, scegliere **Apply** (Applica).

È possibile visualizzare i momenti in cui viene attivata la regola nella pagina **Rules** (Regole) o nella pagina **Dashboard**.

## <a name="create-a-new-rule-with-multiple-conditions"></a>Creare una nuova regola con più condizioni

Per creare una nuova regola con più condizioni che genera un avviso critico quando l'umidità media del dispositivo **Chiller** (Refrigeratore) negli ultimi 5 minuti è maggiore dell'80% e la temperatura del dispositivo **Chiller** (Refrigeratore) negli ultimi 5 minuti è superiore a 75 gradi Fahrenheit, selezionare **New rule** (Nuova regola):

![Creare una regola con più condizioni](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule_mult_v2.png)

Usare i valori seguenti per creare la regola:

| Impostazione          | Valore                                 |
| ---------------- | ------------------------------------- |
| Nome regola        | Umidità refrigeratore e temperatura critiche    |
| DESCRIZIONE      | Temperatura e umidità sono critiche |
| Gruppo di dispositivi     | Gruppo di dispositivi **Chillers** (Refrigeratori)             |
| Calcolo      | Media                               |
| Periodo di tempo      | 5                                     |
| Campo condizione 1| umidità                              |
| Operatore condizione 1 | Maggiore di                      |
| Valore condizione 1    | 80                               |
| Livello di gravità  | Critico                              |

Per aggiungere la seconda condizione, fare clic su "+ Add condition" (+ Aggiungi condizione).

![Creare la condizione 2](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2.png)

Sulla nuova condizione, usare i valori seguenti:

| Impostazione          | Valore                                 |
| ---------------- | ------------------------------------- |
| Campo condizione 2| temperatura                           |
| Operatore condizione 2 | Maggiore di                      |
| Valore condizione 2    | 75                                |

Per salvare la nuova regola, scegliere **Apply** (Applica).

È possibile visualizzare i momenti in cui viene attivata la regola nella pagina **Rules** (Regole) o nella pagina **Dashboard**.

## <a name="edit-an-existing-rule"></a>Modificare una regola esistente

Per apportare una modifica a una regola esistente, selezionare la regola nell'elenco delle regole.

![Modificare una regola](media/iot-suite-remote-monitoring-automate/rulesactionsedit_v2.png)

<!--## Disable a rule

To temporarily switch off a rule, you can disable it in the list of rules. Choose the rule to disable, and then choose **Disable**. The **Status** of the rule in the list changes to indicate the rule is now disabled. You can re-enable a rule that you previously disabled using the same procedure.

![Disable rule](media/iot-suite-remote-monitoring-automate/rulesactionsdisable.png)

You can enable and disable multiple rules at the same time if you select multiple rules in the list.-->

<!--## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha mostrato come:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Visualizzare le regole nella soluzione
> * Creare una nuova regola
> * Modificare una regola esistente
> * Eliminare una regola

Ora che si è appreso come rilevare i problemi usando regole basate su soglie, i passaggi successivi consigliati consentono di apprendere come:

* [Gestire e configurare i dispositivi](./iot-suite-remote-monitoring-manage.md).
* [Risolvere e correggere i problemi dei dispositivi](./iot-suite-remote-monitoring-maintain.md).
* [Testare la soluzione con dispositivi simulati](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->