---
title: "Esercitazione: pianificare un'attività ACR"
description: In questa esercitazione si apprenderà come eseguire un'attività di Container Registry di Azure in base a una pianificazione definita impostando uno o più trigger timer
ms.topic: article
ms.date: 06/27/2019
ms.openlocfilehash: 37247289ef11873ac37dc78ad56548994220f894
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454667"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>Eseguire un'attività ACR in base a una pianificazione definita

Questa esercitazione illustra come eseguire un' [attività ACR](container-registry-tasks-overview.md) in base a una pianificazione. Pianificare un'attività impostando uno o più *trigger timer*. I trigger timer possono essere usati singolarmente o in combinazione con altri trigger di attività.

In questa esercitazione si apprenderà come pianificare le attività e:

> [!div class="checklist"]
> * Creare un'attività con un trigger timer
> * Gestisci trigger timer

La pianificazione di un'attività è utile per scenari simili ai seguenti:

* Eseguire un carico di lavoro del contenitore per le operazioni di manutenzione pianificata. Eseguire ad esempio un'app in contenitori per rimuovere le immagini non necessarie dal registro di sistema.
* Eseguire un set di test in un'immagine di produzione durante la giornata lavorativa come parte del monitoraggio del sito Live.

Per eseguire gli esempi in questo articolo, è possibile usare l'Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Se si vuole usarlo localmente, è richiesta la versione 2.0.68 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].


## <a name="about-scheduling-a-task"></a>Informazioni sulla pianificazione di un'attività

* **Trigger con espressione cron** : il trigger timer per un'attività usa un' *espressione cron*. L'espressione è una stringa con cinque campi che specificano il minuto, l'ora, il giorno, il mese e il giorno della settimana per l'attivazione dell'attività. Sono supportate frequenze fino a una volta al minuto.

  Ad esempio, l'espressione `"0 12 * * Mon-Fri"` attiva un'attività a mezzogiorno UTC in ogni giorno della settimana. Vedere [i dettagli](#cron-expressions) più avanti in questo articolo.
* **Più trigger timer** : è possibile aggiungere più timer a un'attività, purché le pianificazioni differiscano.
    * Specificare più trigger timer quando si crea l'attività o aggiungerli in un secondo momento.
    * Facoltativamente, denominare i trigger per semplificare la gestione oppure le attività ACR forniranno i nomi predefiniti dei trigger.
    * Se le pianificazioni del timer si sovrappongono alla volta, le attività ACR attivano l'attività all'orario pianificato per ogni timer.
* **Altri trigger di attività** : in un'attività attivata dal timer, è anche possibile abilitare trigger basati sul [commit del codice sorgente](container-registry-tutorial-build-task.md) o sugli [aggiornamenti dell'immagine di base](container-registry-tutorial-base-image-update.md). Analogamente ad altre attività ACR, è anche possibile [attivare manualmente][az-acr-task-run] un'attività pianificata.

## <a name="create-a-task-with-a-timer-trigger"></a>Creare un'attività con un trigger timer

Quando si crea un'attività con il comando [AZ ACR task create][az-acr-task-create] , è possibile aggiungere facoltativamente un trigger timer. Aggiungere il parametro `--schedule` e passare un'espressione cron per il timer.

Come semplice esempio, il comando seguente attiva l'esecuzione dell'immagine `hello-world` dall'hub Docker ogni giorno alle 21:00 UTC. L'attività viene eseguita senza un contesto del codice sorgente.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --cmd hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Eseguire il comando [AZ ACR task show][az-acr-task-show] per verificare che il trigger del timer sia configurato. Per impostazione predefinita, è abilitato anche il trigger di aggiornamento dell'immagine di base.

```console
$ az acr task show --name mytask --registry registry --output table
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

Attivare manualmente l'attività con [AZ ACR task run][az-acr-task-run] per assicurarsi che sia configurata correttamente:

```azurecli
az acr task run --name mytask --registry myregistry
```

Se il contenitore viene eseguito correttamente, l'output sarà simile al seguente:

```console
Queued a run with ID: cf2a
Waiting for an agent...
2019/06/28 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2019/06/28 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2019/06/28 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Al termine dell'ora pianificata, eseguire il comando [AZ ACR Task List-runs][az-acr-task-list-runs] per verificare che il timer abbia attivato l'attività come previsto:

```azurecli
az acr task list-runs --name mytask --registry myregistry --output table
```

Quando il timer ha esito positivo, l'output è simile al seguente:

```console
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Gestisci trigger timer

Usare i comandi [AZ ACR Task Timer][az-acr-task-timer] per gestire i trigger timer per un'attività ACR.

### <a name="add-or-update-a-timer-trigger"></a>Aggiungere o aggiornare un trigger timer

Dopo aver creato un'attività, è possibile aggiungere facoltativamente un trigger timer usando il comando [AZ ACR Task Timer Add][az-acr-task-timer-add] . Nell'esempio seguente viene aggiunto un nome di trigger timer *Timer2* a *attività* creata in precedenza. Questo timer attiva l'attività ogni giorno alle 10:30 UTC.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Aggiornare la pianificazione di un trigger esistente o modificarne lo stato usando il comando [AZ ACR Task Timer Update][az-acr-task-timer-update] . Ad esempio, aggiornare il trigger denominato *Timer2* per attivare l'attività alle 11:30 UTC:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Elenca trigger timer

Il comando [AZ ACR Task Timer list][az-acr-task-timer-list] Mostra i trigger timer impostati per un'attività:

```azurecli
az acr task timer list --name mytask --registry myregistry
```

Output di esempio:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>Rimuovere un trigger timer

Usare il comando [AZ ACR Task Timer Remove][az-acr-task-timer-remove] per rimuovere un trigger timer da un'attività. Nell'esempio seguente viene rimosso il trigger *Timer2* da *attività*:

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Espressioni cron

Le attività ACR usano la libreria [NCronTab](https://github.com/atifaziz/NCrontab) per interpretare le espressioni cron. Le espressioni supportate nelle attività ACR hanno cinque campi obbligatori separati da spazi vuoti:

`{minute} {hour} {day} {month} {day-of-week}`

Il fuso orario utilizzato con le espressioni cron è Coordinated Universal Time (UTC). Le ore sono in formato 24 ore.

> [!NOTE]
> Le attività ACR non supportano il campo `{second}` o `{year}` nelle espressioni cron. Se si copia un'espressione cron usata in un altro sistema, assicurarsi di rimuovere i campi, se usati.

Ogni campo può avere uno dei tipi di valori seguenti:

|digitare  |Esempio  |Quando viene attivato  |
|---------|---------|---------|
|Valore specifico |<nobr>`"5 * * * *"`</nobr>|ogni ora a 5 minuti dopo l'ora|
|Tutti i valori (`*`)|<nobr>`"* 5 * * *"`</nobr>|ogni minuto dell'ora inizia 5:00 UTC (60 volte al giorno)|
|Intervallo (operatore `-`)|<nobr>`"0 1-3 * * *"`</nobr>|3 volte al giorno, alle 1:00, 2:00 e 3:00 UTC|
|Set di valori (operatore `,`)|<nobr>`"20,30,40 * * * *"`</nobr>|3 volte all'ora, 20 minuti, 30 minuti e 40 minuti dopo l'ora|
|Valore di intervallo (operatore `/`)|<nobr>`"*/10 * * * *"`</nobr>|6 volte all'ora, a 10 minuti, 20 minuti e così via, oltre l'ora

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Esempi Cron

|Esempio|Quando viene attivato  |
|---------|---------|
|`"*/5 * * * *"`|Una volta ogni cinque minuti|
|`"0 * * * *"`|Una volta all'inizio di ogni ora|
|`"0 */2 * * *"`|Una volta ogni due ore|
|`"0 9-17 * * *"`|una volta ogni ora da 9:00 a 17:00 UTC|
|`"30 9 * * *"`|alle 9:30 UTC ogni giorno|
|`"30 9 * * 1-5"`|alle 9:30 UTC di ogni giorno feriale|
|`"30 9 * Jan Mon"`|alle 9:30 UTC ogni lunedì di gennaio|


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come creare attività di Container Registry di Azure che vengono attivate automaticamente da un timer. 

Per un esempio di come usare un'attività pianificata per pulire i repository in un registro, vedere [eliminare automaticamente le immagini da un registro contenitori di Azure](container-registry-auto-purge.md).

Per esempi di attività avviate da commit del codice sorgente o aggiornamenti di immagini di base, vedere gli altri articoli della [serie di esercitazioni sulle attività di ACR](container-registry-tutorial-quick-task.md).



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
