---
title: Slot di distribuzione di Funzioni di AzureAzure Functions deployment slots
description: Informazioni su come creare e usare slot di distribuzione con Funzioni di AzureLearn to create and use deployment slots with Azure Functions
author: craigshoemaker
ms.topic: reference
ms.date: 08/12/2019
ms.author: cshoe
ms.openlocfilehash: 0e8c93ea6d5c2b525ccbea2af900f100afcc3d93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769218"
---
# <a name="azure-functions-deployment-slots"></a>Slot di distribuzione di Funzioni di AzureAzure Functions deployment slots

Gli slot di distribuzione di Funzioni di Azure consentono all'app per le funzioni di eseguire istanze diverse denominate "slot". Gli slot sono ambienti diversi esposti tramite un endpoint disponibile pubblicamente. Un'istanza dell'app viene sempre mappata allo slot di produzione ed è possibile scambiare le istanze assegnate a uno slot su richiesta. Le app per le funzioni in esecuzione nel piano di servizio App possono avere più slot, mentre nel piano di consumo è consentito un solo slot.

Di seguito viene riportato il modo in cui le funzioni sono influenzate dallo scambio di slot:

- Il reindirizzamento del traffico è senza soluzione di continuità; nessuna richiesta viene eliminata a causa di uno scambio.
- Se una funzione è in esecuzione durante uno scambio, l'esecuzione continua e i trigger successivi vengono instradati all'istanza dell'app scambiata.

> [!NOTE]
> Gli slot non sono attualmente disponibili per il piano di consumo Linux.

## <a name="why-use-slots"></a>Perché usare gli slot?

L'utilizzo degli slot di distribuzione offre numerosi vantaggi. Gli scenari seguenti descrivono gli usi comuni per gli slot:The following scenarios describe common uses for slots:

- **Ambienti diversi per scopi diversi:** l'uso di slot diversi offre l'opportunità di differenziare le istanze dell'app prima di passare a uno slot di produzione o di gestione temporanea.
- **Prewarming**: La distribuzione in uno slot invece di direttamente in produzione consente all'app di riscaldarsi prima di andare in diretta. Inoltre, l'utilizzo degli slot riduce la latenza per i carichi di lavoro attivati tramite HTTP. Le istanze vengono riscaldate prima della distribuzione, riducendo così l'avvio a freddo per le funzioni appena distribuite.
- **Fallback facili**: Dopo uno scambio con la produzione, lo slot con un'app gestita in precedenza ora ha l'app di produzione precedente. Se le modifiche scambiate nello slot di produzione non sono quelle previste, è possibile invertire immediatamente lo scambio per ottenere l'ultima istanza valida nota.

## <a name="swap-operations"></a>Operazioni di scambio

Durante uno scambio, uno slot viene considerato l'origine e l'altro la destinazione. Lo slot di origine ha l'istanza dell'applicazione applicata allo slot di destinazione. I passaggi seguenti assicurano che lo slot di destinazione non si verifichi tempi di inattività durante uno scambio:

1. **Applicare le impostazioni:** Le impostazioni dello slot di destinazione vengono applicate a tutte le istanze dello slot di origine. Ad esempio, le impostazioni di produzione vengono applicate all'istanza di gestione temporanea. Le impostazioni applicate includono le seguenti categorie:
    - [Impostazioni](#manage-settings) dell'app specifiche dello slot e stringhe di connessione (se applicabile)
    - [Impostazioni di distribuzione continua](../app-service/deploy-continuous-deployment.md) (se abilitate)
    - Impostazioni di [autenticazione](../app-service/overview-authentication-authorization.md) del servizio app (se abilitate)App Service authentication settings (if enabled)

1. **Attendere i riavvii e la disponibilità:** Lo swap attende che ogni istanza nello slot di origine completi il riavvio e sia disponibile per le richieste. Se un'istanza non viene riavviata, l'operazione di scambio ripristina tutte le modifiche allo slot di origine e interrompe l'operazione.

1. **Aggiorna routing:** Se tutte le istanze nello slot di origine vengono riscaldate correttamente, i due slot completano lo scambio cambiando regola di routing. Dopo questo passaggio, lo slot di destinazione (ad esempio, lo slot di produzione) ha l'app che in precedenza è riscaldata nello slot di origine.

1. **Ripeti operazione:** Ora che lo slot di origine ha l'app di pre-scambio precedentemente nello slot di destinazione, eseguire la stessa operazione applicando tutte le impostazioni e riavviando le istanze per lo slot di origine.

Tenere presente quanto segue:

- In qualsiasi punto dell'operazione di scambio, l'inizializzazione delle app scambiate avviene nello slot di origine. Lo slot di destinazione rimane online durante la preparazione dello slot di origine, indipendentemente dal fatto che lo scambio abbia esito positivo o negativo.

- Per scambiare uno slot di staging con lo slot di produzione, assicurarsi che lo slot di produzione sia *sempre* lo slot di destinazione. In questo modo, l'operazione di scambio non influisce sull'app di produzione.

- Le impostazioni relative alle origini eventi e ai binding devono essere configurate come [impostazioni dello slot](#manage-settings) di distribuzione prima di *avviare uno scambio.* Contrassegnarli come "appiccicosi" in anticipo assicura che gli eventi e gli output vengano indirizzati all'istanza corretta.

## <a name="manage-settings"></a>Gestire le impostazioni

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>Creare un'impostazione di distribuzioneCreate a deployment setting

È possibile contrassegnare le impostazioni come impostazioni di distribuzione che la rendono "appiccicosa". Un'impostazione perstiva tempo non viene scambiata con l'istanza dell'app.

Se si crea un'impostazione di distribuzione in uno slot, assicurarsi di creare la stessa impostazione con un valore univoco in qualsiasi altro slot coinvolto in uno scambio. In questo modo, anche se il valore di un'impostazione non cambia, i nomi delle impostazioni rimangono coerenti tra gli slot. Questa coerenza dei nomi garantisce che il codice non tenti di accedere a un'impostazione definita in uno slot ma non in un altro.

Per creare un'impostazione di distribuzione, eseguire la procedura seguente:Use the following steps to create a deployment setting:

- Passare a Slot nell'app per le *funzioni*
- Fare clic sul nome dello slot
- In *Funzionalità della piattaforma > Impostazioni generali*, fare clic su **Configurazione**
- Fare clic sul nome dell'impostazione che si desidera attaccare con lo slot corrente
- Fare clic sulla casella di **controllo Impostazione slot di distribuzione**
- Fare clic su **OK**.
- Una volta che il pannello di impostazione scompare, fare clic su **Salva** per mantenere le modifiche

![Impostazione dello slot di distribuzione](./media/functions-deployment-slots/azure-functions-deployment-slots-deployment-setting.png)

## <a name="deployment"></a>Distribuzione

Gli slot sono vuoti quando si crea uno slot. È possibile utilizzare una qualsiasi delle tecnologie di [distribuzione supportate](./functions-deployment-technologies.md) per distribuire l'applicazione in uno slot.

## <a name="scaling"></a>Scalabilità

Tutti gli slot vengono scalati allo stesso numero di lavoratori dello slot di produzione.

- Per i piani di consumo, lo slot viene ridimensionato in base alla scalabilità dell'app per le funzioni.
- Per i piani di servizio app, l'app viene ridimensionata in base a un numero fisso di ruoli di lavoro. Gli slot vengono eseguiti sullo stesso numero di lavoratori del piano dell'app.

## <a name="add-a-slot"></a>Aggiungi uno slot

È possibile aggiungere uno slot tramite [l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) o tramite il portale. I passaggi seguenti illustrano come creare un nuovo slot nel portale:The following steps demonstrate how to create a new slot in the portal:

1. Passare all'app per le funzioni e fare clic sul **segno più** accanto a *Slot*.

    ![Aggiungere lo slot di distribuzione di Funzioni di AzureAdd Azure Functions deployment slot](./media/functions-deployment-slots/azure-functions-deployment-slots-add.png)

1. Immettere un nome nella casella di testo e premere il pulsante **Crea.**

    ![Nome Slot di distribuzione di Funzioni di AzureName Azure Functions deployment slot](./media/functions-deployment-slots/azure-functions-deployment-slots-add-name.png)

## <a name="swap-slots"></a>Scambiare gli slot

È possibile scambiare le slot tramite [l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) o tramite il portale. I passaggi seguenti illustrano come scambiare i slot nel portale:

1. Passare all'app per le funzioni
1. Fare clic sul nome dello slot di origine che si desidera scambiare
1. Nella scheda *Panoramica* fare clic ![sul pulsante **Scambia** slot di distribuzione Funzioni di Azure](./media/functions-deployment-slots/azure-functions-deployment-slots-swap.png)
1. Verificare le impostazioni di configurazione per lo scambio e fare clic su Slot di distribuzione **Scambia** ![scambio funzioni di AzureVerify the configuration settings for your swap and click Swap Swap Azure Functions deployment slot](./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png)

L'operazione potrebbe richiedere un po' di tempo durante l'esecuzione dell'operazione di scambio.

## <a name="roll-back-a-swap"></a>Eseguire il rollback di uno scambio

Se uno scambio genera un errore o si desidera semplicemente "annullare" uno scambio, è possibile ripristinare lo stato iniziale. Per tornare allo stato pre-scambiato, eseguire un altro scambio per invertire lo scambio.

## <a name="remove-a-slot"></a>Rimuovere uno slot

È possibile rimuovere uno slot tramite [l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) o tramite il portale. I passaggi seguenti illustrano come rimuovere uno slot nel portale:

1. Passare all'app per le funzioni Panoramica

1. Fare clic sul pulsante **Elimina**

    ![Aggiungere lo slot di distribuzione di Funzioni di AzureAdd Azure Functions deployment slot](./media/functions-deployment-slots/azure-functions-deployment-slots-delete.png)

## <a name="automate-slot-management"></a>Automatizzare la gestione degli slot

Usando [l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest)di Azure, è possibile automatizzare le azioni seguenti per uno slot:Using the Azure CLI , you can automate the following actions for a slot:

- [Creare](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [Elimina](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [Elenco](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [Swap](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [scambio automatico](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>Modificare il piano di servizio dell'appChange App Service plan

Con un'app per le funzioni in esecuzione in un piano di servizio app, è possibile modificare il piano di servizio app sottostante per uno slot.

> [!NOTE]
> Non è possibile modificare il piano di servizio app di uno slot nel piano di consumo.

Utilizzare la procedura seguente per modificare il piano di servizio app di uno slot:Use the following steps to change a slot's App Service plan:

1. Passare a uno slot

1. In *Funzionalità della piattaforma*fare clic su Tutte le **impostazioni**

    ![Modificare il piano di servizio dell'appChange app service plan](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-settings.png)

1. Fare clic su **App Service plan**

1. Selezionare un nuovo piano di servizio app o creare un nuovo piano

1. Fare clic su **OK**.

    ![Modificare il piano di servizio dell'appChange app service plan](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-select.png)


## <a name="limitations"></a>Limitazioni

Gli slot di distribuzione di Funzioni di Azure presentano le limitazioni seguenti:Azure Functions deployment slots have the following limitations:

- Il numero di slot disponibili per un'app dipende dal piano. Il piano di consumo è consentito un solo slot di distribuzione. Sono disponibili slot aggiuntivi per le app in esecuzione nel piano di servizio app.
- Lo scambio di uno slot reimposta i tasti per le app con un'impostazione `AzureWebJobsSecretStorageType` dell'app uguale a `files`.
- Gli slot non sono disponibili per il piano di consumo Linux.

## <a name="support-levels"></a>Livelli di supporto

Esistono due livelli di supporto per gli slot di distribuzione:There are two levels of support for deployment slots:

- **Disponibilità generale (GA):** completamente supportato e approvato per l'utilizzo in produzione.
- **Anteprima**: Non ancora supportato, ma si prevede che raggiunga lo stato GA in futuro.

| Sistema operativo/Piano di hosting           | Livello di supporto     |
| ------------------------- | -------------------- |
| Consumo di Windows       | Disponibilità generale |
| Windows Premium           | Disponibilità generale  |
| Windows dedicato         | Disponibilità generale |
| Consumo Di Linux         | Non supportato          |
| Linux Premium             | Disponibilità generale  |
| Linux dedicato           | Disponibilità generale |

## <a name="next-steps"></a>Passaggi successivi

- [Tecnologie di distribuzione in Funzioni di AzureDeployment technologies in Azure Functions](./functions-deployment-technologies.md)
