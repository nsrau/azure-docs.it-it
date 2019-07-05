---
title: Pianificare le attività di registro contenitori di Azure
description: Impostare dei timer per eseguire un'attività di registro contenitori di Azure in una pianificazione definita.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/27/2019
ms.author: danlep
ms.openlocfilehash: a1123a30025f9be6e994e69703f5ee1aa05d1b49
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509703"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>Eseguire un'attività di registro contenitori di AZURE in una pianificazione definita

Questo articolo illustra come eseguire un' [attività ACR](container-registry-tasks-overview.md) in una pianificazione. Pianificare un'attività di configurazione di uno o più *trigger timer*. 

Pianificazione di un'attività è utile per scenari come i seguenti:

* Eseguire un carico di lavoro contenitore per le operazioni di manutenzione pianificata. Ad esempio, eseguire un'app in contenitori per rimuovere le immagini non necessarie dal Registro di sistema.
* Eseguire un set di test su un'immagine di produzione durante la giornata lavorativa come parte del monitoraggio di siti live.

È possibile usare Azure Cloud Shell o un'installazione locale della riga di comando di Azure per eseguire gli esempi in questo articolo. Se si vuole usarlo in locale, versione 2.0.68 o versione successiva è richiesto. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].


## <a name="about-scheduling-a-task"></a>Sulla pianificazione di un'attività

* **Trigger con espressione cron** -il trigger timer per un'attività Usa una *espressione cron*. L'espressione è una stringa con cinque campi che specifica il minuto, ora, giorno, mese e giorno della settimana per attivare l'attività. Sono supportate le frequenze di massimo una volta al minuto. 

  Ad esempio, l'espressione `"0 12 * * Mon-Fri"` attiva un'attività a mezzogiorno UTC ogni giorno feriale. Visualizzare [dettagli](#cron-expressions) più avanti in questo articolo.
* **Più trigger di timer** -aggiunta di più timer a un'attività è consentito, purché le pianificazioni sono diversi. 
    * Quando si crea l'attività o aggiungerli in un secondo momento, specificare più trigger di timer.
    * Assegnare un nome, facoltativamente, i trigger per semplificare la gestione o le attività di registro contenitori di AZURE fornirà i nomi dei trigger predefinito.
    * Se le pianificazioni di timer si sovrappongono alla volta, le attività di registro contenitori di AZURE attiva l'attività all'ora pianificata per ogni timer. 
* **Altri trigger di attività** -In un'attività attivata tramite timer, è anche possibile abilitare trigger basati sul [commit del codice sorgente](container-registry-tutorial-build-task.md) oppure [gli aggiornamenti di immagine di base](container-registry-tutorial-base-image-update.md). Analogamente ad altre attività di registro contenitori di AZURE, è anche possibile [attivare manualmente][az-acr-task-run] un'attività pianificata.

## <a name="create-a-task-with-a-timer-trigger"></a>Creare un'attività con un trigger timer

Quando si crea un'attività con il [az acr attività creare][az-acr-task-create] comando, è possibile aggiungere facoltativamente un trigger del timer. Aggiungere il `--schedule` parametro e passare un'espressione cron per il timer. 

Un esempio semplice, il seguente comando trigger in esecuzione il `hello-world` immagine dall'Hub Docker giorno alle ore 21:00 UTC. L'attività viene eseguita senza un contesto del codice sorgente.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --context /dev/null \
  --cmd hello-world \
  --schedule "0 21 * * *"
```

Eseguire la [show attività di az acr][az-acr-task-show] comando per verificare che il trigger timer è configurato. Per impostazione predefinita, il trigger di aggiornamento immagine di base è abilitato.

```console
$ az acr task show --name mytask --registry registry --output table
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

Attivare le attività manualmente con il [dell'esecuzione dell'attività az acr][az-acr-task-run] per assicurarsi che sia impostata correttamente:

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

Dopo l'orario pianificato, eseguire la [elenco di attività az acr-esecuzioni][az-acr-task-list-runs] comando per verificare che il timer attivato l'attività come previsto:

```azurecli
az acr task list runs --name mytask --registry myregistry --output table
``` 

Quando il timer ha esito positivo, l'output è simile al seguente:

```console
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```
            
## <a name="manage-timer-triggers"></a>Gestire i trigger timer

Usare la [timer di attività di az acr][az-acr-task-timer] comandi per gestire i trigger timer per un'attività di registro contenitori di AZURE.

### <a name="add-or-update-a-timer-trigger"></a>Aggiungere o aggiornare un trigger timer

Dopo la creazione di un'attività, se lo si desidera aggiungere un trigger timer con il [timer di attività di az acr aggiungere][az-acr-task-timer-add] comando. L'esempio seguente aggiunge un nome di trigger timer *timer2* al *mytask* creato in precedenza. Questo timer attiva l'attività ogni giorno alle 10:30 UTC.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Aggiornare la pianificazione di un trigger esistente o modificare lo stato, tramite il [aggiornamento di az acr attività timer][az-acr-task-timer-update] comando. Ad esempio, aggiornare il trigger denominato *timer2* per attivare l'attività alle 11:30 UTC:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Elencare i trigger timer

Il [elenco di az acr attività timer][az-acr-task-timer-list] comando Mostra i trigger di timer impostato per un'attività:

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

Usare la [remove di az acr attività timer][az-acr-task-timer-remove] comando per rimuovere un trigger timer in un'attività. L'esempio seguente rimuove il *timer2* attivazione da *mytask*:

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Espressioni cron

Le attività di registro contenitori di AZURE Usa il [NCronTab](https://github.com/atifaziz/NCrontab) libreria per interpretare le espressioni cron. Espressioni supportate nelle attività di registro contenitori di AZURE hanno cinque campi obbligatori separati da spazi vuoti:

`{minute} {hour} {day} {month} {day-of-week}`

Il fuso orario usato con le espressioni cron è Coordinated Universal Time (UTC). Le ore sono in formato 24 ore.

> [!NOTE]
> Le attività di registro contenitori di AZURE non supporta il `{second}` o `{year}` campo nelle espressioni cron. Se si copia un'espressione cron utilizzata in un altro sistema, assicurarsi di rimuovere tali campi, se vengono usati.

Ogni campo può avere uno dei tipi di valori seguenti:

|Type  |Esempio  |Quando viene attivato  |
|---------|---------|---------|
|Valore specifico |<nobr>"5 * * * *"</nobr>|ogni ora in 5 minuti dopo l'ora|
|Tutti i valori (`*`)|<nobr>"* 5 * * *"</nobr>|ogni minuto dell'ora inizio 5.00 UTC (60 volte al giorno)|
|Intervallo (operatore `-`)|<nobr>"0 1-3 * * *"</nobr>|3 volte al giorno, alle 13:00, dalle 2.00 e l'ora UTC 3:00|  
|Set di valori (operatore `,`)|<nobr>"20,30,40 * * * *"</nobr>|3 volte all'ora, a 20 minuti, 30 minuti e 40 minuti passati dall'ora|
|Valore di intervallo (operatore `/`)|<nobr>"*/10 * * * *"</nobr>|6 volte all'ora, in 10 minuti, 20 minuti e così via, l'ora precedente

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Esempi cron

|Esempio|Quando viene attivato  |
|---------|---------|
|`"*/5 * * * *"`|Una volta ogni cinque minuti|
|`"0 * * * *"`|Una volta all'inizio di ogni ora|
|`"0 */2 * * *"`|Una volta ogni due ore|
|`"0 9-17 * * *"`|una volta ogni ora dalle 9.00 alle 17.00 UTC|
|`"30 9 * * *"`|Nella finestra 9.30 ogni giorno UTC|
|`"30 9 * * 1-5"`|Nella finestra 9.30 UTC ogni giorno feriale|
|`"30 9 * Jan Mon"`|Nella finestra 9.30 UTC ogni lunedì nel mese di gennaio|


## <a name="next-steps"></a>Passaggi successivi

Per esempi di attività attivata dal commit del codice sorgente o immagine di base degli aggiornamenti, consultare il [serie di esercitazioni in Registro contenitori di AZURE attività](container-registry-tutorial-quick-task.md).



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