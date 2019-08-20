---
title: Slot di distribuzione di funzioni di Azure
description: Informazioni su come creare e usare gli slot di distribuzione con funzioni di Azure
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: funzioni di Azure, funzioni
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 08/12/2019
ms.author: cshoe
ms.openlocfilehash: a75299906098a0149df5417835569a30a4e585ff
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69626184"
---
# <a name="azure-functions-deployment-slots"></a>Slot di distribuzione di funzioni di Azure

Gli slot di distribuzione di funzioni di Azure consentono all'app per le funzioni di eseguire istanze diverse denominate "slot". Gli slot sono ambienti diversi esposti tramite un endpoint disponibile pubblicamente. Viene sempre eseguito il mapping di un'istanza dell'app allo slot di produzione ed è possibile scambiare istanze assegnate a uno slot su richiesta. Le app per le funzioni in esecuzione nel piano di servizio app possono avere più slot, mentre in consumo è consentito un solo slot.

Di seguito viene illustrato il modo in cui le funzioni sono interessate dallo swapping degli slot:

- Il reindirizzamento del traffico è semplice. Nessuna richiesta viene eliminata a causa di uno scambio.
- Se una funzione viene eseguita durante uno scambio, l'esecuzione continua e i trigger successivi vengono instradati all'istanza dell'app scambiata.

> [!NOTE]
> Gli slot non sono disponibili per il piano a consumo Linux.

## <a name="why-use-slots"></a>Perché usare gli slot?

L'uso degli slot di distribuzione presenta diversi vantaggi. Negli scenari seguenti vengono descritti gli utilizzi comuni per gli slot:

- **Ambienti diversi per scopi diversi**: L'uso di slot diversi offre la possibilità di distinguere le istanze dell'app prima di effettuare lo scambio in produzione o in uno slot di staging.
- **Preriscaldamento**: La distribuzione in uno slot invece che direttamente nell'ambiente di produzione consente all'app di scaldarsi prima di andare in tempo reale. Inoltre, l'uso di slot riduce la latenza per i carichi di lavoro attivati da HTTP. Le istanze vengono riscaldate prima della distribuzione, riducendo l'avvio a freddo per le funzioni appena distribuite.
- **Fallback semplici**: Dopo uno scambio con la produzione, lo slot con un'app precedentemente preparata ha ora l'app di produzione precedente. Se le modifiche scambiate nello slot di produzione non sono quelle desiderate, è possibile invertire immediatamente lo scambio per riportare l'ultima istanza di una nota.

## <a name="swap-operations"></a>Operazioni di scambio

Durante uno scambio, uno slot viene considerato l'origine e l'altro la destinazione. Lo slot di origine ha l'istanza dell'applicazione applicata allo slot di destinazione. I passaggi seguenti assicurano che lo slot di destinazione non riscontri tempi di inattività durante uno scambio:

1. **Applica impostazioni:** Le impostazioni dello slot di destinazione vengono applicate a tutte le istanze dello slot di origine. Le impostazioni di produzione, ad esempio, vengono applicate all'istanza di gestione temporanea. Le impostazioni applicate includono le categorie seguenti:
    - Impostazioni dell'app [specifiche dello slot](#manage-settings) e stringhe di connessione (se applicabile)
    - Impostazioni di [distribuzione continua](../app-service/deploy-continuous-deployment.md) (se abilitate)
    - Impostazioni di [autenticazione del servizio app](../app-service/overview-authentication-authorization.md) (se abilitate)

1. **Attendere i riavvii e la disponibilità:** Lo scambio attende che ogni istanza nello slot di origine completi il riavvio e sia disponibile per le richieste. Se un'istanza non viene riavviata, l'operazione di scambio Ripristina tutte le modifiche apportate allo slot di origine e interrompe l'operazione.

1. **Routing degli aggiornamenti:** Se tutte le istanze dello slot di origine sono state attivate correttamente, i due slot completano lo scambio cambiando le regole di routing. Dopo questo passaggio, lo slot di destinazione (ad esempio, lo slot di produzione) dispone dell'app precedentemente riscaldata nello slot di origine.

1. **Ripetere l'operazione:** Ora che lo slot di origine ha l'app pre-swap nello slot di destinazione, eseguire la stessa operazione applicando tutte le impostazioni e riavviando le istanze per lo slot di origine.

Tenere presente quanto segue:

- In qualsiasi momento dell'operazione di scambio, l'inizializzazione delle app scambiate viene eseguita nello slot di origine. Lo slot di destinazione rimane online mentre è in corso la preparazione dello slot di origine, indipendentemente dal fatto che lo scambio abbia esito positivo o negativo.

- Per scambiare uno slot di staging con lo slot di produzione, assicurarsi che lo slot di produzione sia *sempre* lo slot di destinazione. In questo modo, l'operazione di scambio non influisce sull'app di produzione.

- Le impostazioni relative alle origini eventi e alle associazioni devono essere configurate come [impostazioni dello slot di distribuzione](#manage-settings) *prima di avviare uno scambio*. Contrassegnando come "appiccicoso" in anticipo, gli eventi e gli output vengono indirizzati all'istanza corretta.

## <a name="manage-settings"></a>Gestire le impostazioni

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>Creare un'impostazione di distribuzione

È possibile contrassegnare le impostazioni come un'impostazione di distribuzione che lo rende "appiccicoso". Un'impostazione appiccicosa non scambia con l'istanza dell'app.

Se si crea un'impostazione di distribuzione in uno slot, assicurarsi di creare la stessa impostazione con un valore univoco in qualsiasi altro slot associato a uno scambio. In questo modo, quando il valore di un'impostazione non cambia, i nomi delle impostazioni rimangono coerenti tra gli slot. Questa coerenza del nome garantisce che il codice non tenti di accedere a un'impostazione definita in uno slot ma non in un'altra.

Per creare un'impostazione di distribuzione, attenersi alla procedura seguente:

- Passare a *slot* nell'app per le funzioni
- Fare clic sul nome dello slot
- In *funzionalità della piattaforma > impostazioni generali*fare clic su **configurazione** .
- Fare clic sul nome dell'impostazione che si vuole usare con lo slot corrente
- Fare clic sulla casella di controllo **Impostazioni slot di distribuzione**
- Fare clic su **OK**
- Quando l'impostazione del pannello scompare, fare clic su **Salva** per conservare le modifiche

![Impostazione dello slot di distribuzione](./media/functions-deployment-slots/azure-functions-deployment-slots-deployment-setting.png)

## <a name="deployment"></a>Distribuzione

Gli slot sono vuoti quando si crea uno slot. Per distribuire l'applicazione in uno slot, è possibile usare qualsiasi [tecnologia di distribuzione supportata](./functions-deployment-technologies.md) .

## <a name="scaling"></a>Ridimensionamento

Tutti gli slot si adattano allo stesso numero di ruoli di lavoro dello slot di produzione.

- Per i piani a consumo, lo slot viene ridimensionato con l'app per le funzioni ridimensionata.
- Per i piani di servizio app, l'app viene ridimensionata a un numero fisso di thread di lavoro. Gli slot vengono eseguiti nello stesso numero di processi di lavoro del piano dell'app.

## <a name="add-a-slot"></a>Aggiungi uno slot

È possibile aggiungere uno slot tramite l' [interfaccia](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) della riga di comando o tramite il portale. I passaggi seguenti illustrano come creare un nuovo slot nel portale:

1. Passare all'app per le funzioni e fare clic sul **segno più** accanto a *slot*.

    ![Aggiungi slot di distribuzione di funzioni di Azure](./media/functions-deployment-slots/azure-functions-deployment-slots-add.png)

1. Immettere un nome nella casella di testo e fare clic sul pulsante **Crea** .

    ![Nome slot di distribuzione di funzioni di Azure](./media/functions-deployment-slots/azure-functions-deployment-slots-add-name.png)

## <a name="swap-slots"></a>Scambia slot

È possibile scambiare gli slot tramite l' [interfaccia](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) della riga di comando o tramite il portale. I passaggi seguenti illustrano come scambiare gli slot nel portale:

1. Passare all'app per le funzioni
1. Fare clic sul nome dello slot di origine che si desidera scambiare
1. Dalla scheda *Panoramica* fare clic sul pulsante ![ **Scambia** per scambiare lo slot di distribuzione di funzioni di Azure](./media/functions-deployment-slots/azure-functions-deployment-slots-swap.png)
1. Verificare le impostazioni di configurazione per lo scambio e fare clic su **Scambia** ![scambio funzioni di Azure slot di distribuzione](./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png)

Durante l'esecuzione dell'operazione di scambio, l'operazione potrebbe richiedere qualche istante.

## <a name="roll-back-a-swap"></a>Eseguire il rollback di uno scambio

Se uno scambio genera un errore o si vuole semplicemente annullare uno scambio, è possibile eseguire il rollback allo stato iniziale. Per tornare allo stato precedente allo scambio, eseguire un altro scambio per invertire lo scambio.

## <a name="remove-a-slot"></a>Rimuovere uno slot

È possibile rimuovere uno slot tramite l' [interfaccia](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) della riga di comando o tramite il portale. La procedura seguente illustra come rimuovere uno slot nel portale:

1. Passare all'app per le funzioni panoramica

1. Fare clic sul pulsante **Elimina**

    ![Aggiungi slot di distribuzione di funzioni di Azure](./media/functions-deployment-slots/azure-functions-deployment-slots-delete.png)

## <a name="automate-slot-management"></a>Automatizzare la gestione degli slot

Usando l' [interfaccia](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest)della riga di comando di Azure, è possibile automatizzare le azioni seguenti per uno slot:

- [create](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [delete](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [list](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [swap](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [scambio automatico](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>Modificare il piano di servizio app

Con un'app per le funzioni in esecuzione in un piano di servizio app, è possibile modificare il piano di servizio app sottostante per uno slot.

> [!NOTE]
> Non è possibile modificare il piano di servizio app di uno slot nel piano a consumo.

Per modificare il piano di servizio app di uno slot, attenersi alla procedura seguente:

1. Passa a uno slot

1. In *funzionalità della piattaforma*fare clic su **tutte le impostazioni**

    ![Modificare il piano di servizio app](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-settings.png)

1. Fare clic su **piano di servizio app**

1. Selezionare un nuovo piano di servizio app o crearne uno nuovo

1. Fare clic su **OK**

    ![Modificare il piano di servizio app](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-select.png)


## <a name="limitations"></a>Limitazioni

Gli slot di distribuzione di funzioni di Azure presentano le limitazioni seguenti:

- Il numero di slot disponibili per un'app dipende dal piano. Il piano a consumo è consentito solo uno slot di distribuzione. Sono disponibili slot aggiuntivi per le app in esecuzione nel piano di servizio app.
- Lo scambio di uno slot Reimposta le chiavi per le app che `AzureWebJobsSecretStorageType` hanno un'impostazione dell' `files`app uguale a.
- Gli slot non sono disponibili per il piano a consumo Linux.

## <a name="support-levels"></a>Livelli di supporto

Per gli slot di distribuzione sono disponibili due livelli di supporto:

- **Disponibilità generale (GA)** : Completamente supportato e approvato per l'uso in produzione.
- **Anteprima**: Non ancora supportato, ma si prevede di raggiungere lo stato di GA in futuro.

| Sistema operativo/piano di hosting           | Livello di supporto     |
| ------------------------- | -------------------- |
| Utilizzo di Windows       | Disponibilità generale |
| Windows Premium (anteprima) | Anteprima              |
| Windows dedicato         | Disponibilità generale |
| Consumo Linux         | File modifiche disco non supportato          |
| Linux Premium (anteprima)   | Anteprima              |
| Linux dedicato           | Disponibilità generale |

## <a name="next-steps"></a>Passaggi successivi

- [Tecnologie di distribuzione in funzioni di Azure](./functions-deployment-technologies.md)
