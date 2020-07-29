---
title: Gruppi di cartelle di lavoro di monitoraggio di Azure
description: Come usare i gruppi nelle cartelle di lavoro di monitoraggio di Azure.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: b6377cdcdb5816426eba62fdbef79eeb42659dcc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82892082"
---
# <a name="how-to-use-groups-in-workbooks"></a>Come usare i gruppi nelle cartelle di lavoro

Un elemento di gruppo in una cartella di lavoro consente di raggruppare logicamente un set di passaggi in una cartella di lavoro.

I gruppi nelle cartelle di lavoro sono utili per diversi aspetti:

- Layout
  - Negli scenari in cui si vuole che gli elementi siano organizzati verticalmente, è possibile creare un gruppo di elementi che verranno tutti sovrapposti e impostare lo stile del gruppo in modo che sia una percentuale di larghezza anziché impostare la larghezza della percentuale su tutti i singoli elementi.
- Visibilità
  - Negli scenari in cui si desidera che molti elementi vengano nascosti o visualizzati insieme, è possibile impostare la visibilità dell'intero gruppo di elementi, anziché impostare le impostazioni di visibilità per ogni singolo elemento. Questo può essere utile nei modelli che usano le schede, in quanto è possibile usare un gruppo come contenuto della scheda e l'intero gruppo può essere nascosto/visualizzato in base a un parametro impostato dalla scheda selezionata.
- Prestazioni
  - Nei casi in cui si dispone di un modello di grandi dimensioni con molte sezioni o schede, è possibile convertire ogni sezione nel relativo modello secondario e utilizzare i gruppi per caricare tutti i sottomodelli all'interno del modello di primo livello. Il contenuto dei modelli secondari non verrà caricato o eseguito finché un utente non renderà visibili tali gruppi. Altre informazioni su [come suddividere un modello di grandi dimensioni in molti modelli](#how-to-split-a-large-template-into-many-templates).

## <a name="using-groups"></a>Uso di gruppi

Per aggiungere un gruppo alla cartella di lavoro, selezionare *Aggiungi* e quindi *Aggiungi gruppo*.

![Selezionare Aggiungi quindi gruppo.](./media/workbooks-groups/add-group.png)

Di seguito è riportata una schermata di un gruppo in modalità di lettura con due elementi all'interno di: un elemento di testo e un elemento di query.  

![Gruppo in modalità lettura.](./media/workbooks-groups/groups-view.png)

Quando si modifica la cartella di lavoro, è possibile visualizzare i due elementi in realtà all'interno di un elemento di gruppo:

![Gruppo in modalità di modifica. ](./media/workbooks-groups/groups-edit.png)

Nella schermata precedente il gruppo è in modalità di modifica, mostrando che contiene due elementi (all'interno dell'area tratteggiata). Ogni passaggio può essere in modalità di modifica o lettura, indipendentemente l'uno dall'altro. Ad esempio, il passaggio di testo è in modalità di modifica mentre il passaggio della query è in modalità lettura.

## <a name="scoping"></a>Scoping

Attualmente, un gruppo viene considerato come un nuovo ambito nella cartella di lavoro. Tutti i parametri creati nel gruppo sono visibili solo all'interno del gruppo. Questo vale anche per l'Unione, è possibile visualizzare solo i dati all'interno del gruppo o a livello padre.

## <a name="group-types"></a>Tipi di gruppi

L'elemento del gruppo di cartelle di lavoro consente di aggiungere un gruppo di elementi a una cartella di lavoro. In qualità di autore di una cartella di lavoro, è possibile specificare il tipo di gruppo che sarà. Esistono due tipi di gruppi:

- Editable
  - Il gruppo nella cartella di lavoro consente di aggiungere, rimuovere o modificare il contenuto degli elementi del gruppo. Questa operazione viene usata in genere per scopi di layout e visibilità.
- Da modello
  - Il gruppo nella cartella di lavoro viene caricato dal contenuto di un altro modello in base al relativo ID. Il contenuto del modello viene caricato e Unito nella cartella di lavoro in fase di esecuzione. In modalità di modifica non è possibile modificare i contenuti del gruppo, perché verranno caricati nuovamente dal modello al successivo caricamento dell'elemento.  

## <a name="load-types"></a>Tipi di carico

È possibile caricare il contenuto di un gruppo in diversi modi. Come autore di una cartella di lavoro, è possibile specificare quando e come viene caricato il contenuto di un gruppo.

### <a name="lazy-the-default"></a>Lazy (impostazione predefinita)

Il gruppo verrà caricato solo quando l'elemento è visibile. In questo modo, un gruppo può essere utilizzato dagli elementi di tabulazione. Se la scheda non è mai selezionata, il gruppo non diventa mai visibile, quindi il contenuto non viene caricato.

Per i gruppi creati da un modello, il contenuto del modello non viene recuperato e gli elementi del gruppo non vengono creati finché il gruppo non diventa visibile. Quando il contenuto viene recuperato, l'utente visualizzerà gli spinner di avanzamento per l'intero gruppo.

### <a name="explicit"></a>Esplicito

In questa modalità viene visualizzato un pulsante in cui si trova il gruppo e non viene recuperato o creato alcun contenuto finché l'utente non fa clic esplicitamente sul pulsante per caricare il contenuto. Questa operazione è utile negli scenari in cui il contenuto potrebbe essere costoso per il calcolo o per uso raro. L'autore può specificare il testo da visualizzare sul pulsante.

Di seguito è riportata una schermata delle impostazioni di caricamento esplicito che mostrano un pulsante "carica altro" configurato.

![Impostazioni di caricamento esplicite](./media/workbooks-groups/groups-explicitly-loaded.png)

Gruppo prima del caricamento nella cartella di lavoro:

![Gruppo esplicito prima del caricamento nella cartella di lavoro](./media/workbooks-groups/groups-explicitly-loaded-before.png)

Gruppo dopo il caricamento nella cartella di lavoro:

![Gruppo esplicito dopo essere stato caricato in una cartella di lavoro](./media/workbooks-groups/groups-explicitly-loaded-after.png)

### <a name="always"></a>Sempre

In questa modalità, il contenuto del gruppo viene sempre caricato e creato subito dopo il caricamento della cartella di lavoro. Questa operazione viene usata con maggiore frequenza quando si usa un gruppo solo a scopo di layout, in cui il contenuto sarà sempre visibile.

## <a name="using-templates-inside-a-group"></a>Utilizzo di modelli all'interno di un gruppo

Quando un gruppo è configurato per il caricamento da un modello, il contenuto verrà caricato Lazy per impostazione predefinita e verrà caricato solo quando il gruppo è visibile.

Quando un modello viene caricato in un gruppo, la cartella di lavoro tenta di unire tutti i parametri dichiarati nel modello da caricare con i parametri già esistenti nel gruppo. Tutti i parametri già presenti nella cartella di lavoro con nomi identici verranno uniti dal modello caricato. Se tutti i parametri in un passaggio del parametro vengono Uniti, il passaggio dell'intero parametro scomparirà.

### <a name="example-1-all-parameters-have-identical-names"></a>Esempio 1: tutti i parametri hanno nomi identici

Si consideri un modello con due parametri nella parte superiore.

- `TimeRange`: parametro di intervallo di tempo.
- `Filter`: parametro di testo.

![Modifica dell'elemento Parameters: "parametri di primo livello"](./media/workbooks-groups/groups-top-level-params.png)

Quindi, un elemento del gruppo carica un secondo modello con due parametri e un passaggio di testo, in cui i parametri sono denominati gli stessi:

![Modifica di un elemento parametro per il secondo modello](./media/workbooks-groups/groups-merged-away.png)

Quando il secondo modello viene caricato nel gruppo, i parametri duplicati verranno uniti in uscita. Dal momento che tutti i parametri vengono Uniti, anche il passaggio dei parametri interni viene Unito, ottenendo così il gruppo che contiene solo il passaggio di testo.

### <a name="example-2-one-parameter-has-an-identical-name"></a>Esempio 2: un parametro ha un nome identico

Si consideri il modello di un gruppo che dispone di due parametri nella parte superiore.

- `TimeRange`: parametro di intervallo di tempo.
- `FilterB`-un parametro di testo. si noti che non è `Filter` simile al modello principale.

![Modifica di un elemento di gruppo con il risultato dei parametri Uniti](./media/workbooks-groups/groups-wont-merge-away.png)

Quando il modello item's del gruppo viene caricato, il `TimeRange` parametro viene unito al di fuori del gruppo. La cartella di lavoro avrà quindi il passaggio parametri iniziali con `TimeRange` e e `Filter` il passaggio del parametro del gruppo includerà solo`FilterB`

![risultato dei parametri che non si unirà](./media/workbooks-groups/groups-wont-merge-away-result.png)

Se il modello caricato contiene `TimeRange` e `Filter` (anziché `FilterB` ), la cartella di lavoro risultante avrà un passaggio Parameters e un gruppo con solo il passaggio del testo rimanente.

## <a name="how-to-split-a-large-template-into-many-templates"></a>Come suddividere un modello di grandi dimensioni in molti modelli

Per migliorare le prestazioni, è opportuno suddividere un modello di grandi dimensioni in più modelli più piccoli che caricano un contenuto Lazy o on demand da parte dell'utente. In questo modo il caricamento iniziale risulta più veloce perché il modello di livello superiore può essere molto più piccolo.

Quando si suddivide un modello in parti, sarà necessario suddividere il modello in molti modelli (modelli secondari) che funzionano singolarmente. Quindi, se il modello di livello superiore ha un `TimeRange` parametro usato da altri passaggi, anche il sottomodello dovrà avere un passaggio Parameters che definisce un parametro con il nome esatto. Questo consente ai sottomodelli di funzionare in modo indipendente e di caricarli all'interno di modelli più grandi nei gruppi.

Per trasformare un modello più grande in più modelli secondari:

1.  Creare un nuovo gruppo vuoto nella parte superiore della cartella di lavoro, dopo i parametri condivisi. Questo nuovo gruppo diventerà infine un sottomodello.
2. Creare una copia del passaggio dei parametri condivisi e quindi usare *Sposta nel gruppo* per spostare la copia nel gruppo creato nel passaggio 1. Questo passaggio dei parametri consentirà al sottomodello di funzionare indipendentemente dal modello esterno e verrà unito quando viene caricato nel modello esterno.
    > [!NOTE]
    > Sottomodello non è tecnicamente necessario che questi parametri vengano Uniti se non si intende mai che i modelli secondari siano visibili da soli. Tuttavia, sarà molto difficile da modificare o eseguire il debug se è necessario eseguire questa operazione in un secondo momento.

3. Spostare ogni elemento nella cartella di lavoro che si desidera includere nel modello secondario nel gruppo creato nel passaggio 1.
4. Se i singoli passaggi spostati nel passaggio 3 avevano visibilità condizionali, che diventeranno la visibilità del gruppo esterno (come usato nelle schede). Rimuoverli dagli elementi all'interno del gruppo e aggiungere l'impostazione di visibilità al gruppo stesso. Salva qui per evitare di perdere le modifiche e/o esportare e salvare una copia del contenuto JSON.
5. Se si vuole che il gruppo venga caricato da un modello, è possibile usare il pulsante di *modifica* della barra degli strumenti nel gruppo. In questo modo si aprirà solo il contenuto del gruppo come cartella di lavoro in una nuova finestra. È quindi possibile salvarlo nel modo appropriato e chiudere la visualizzazione della cartella di lavoro (non chiudere il browser, ma solo questa visualizzazione per tornare alla cartella di lavoro precedente che si stava modificando).
6. È quindi possibile modificare il passaggio del gruppo per caricare il modello e impostare il campo ID modello sulla cartella di lavoro o sul modello creato nel passaggio 5. Per lavorare con gli ID delle cartelle di lavoro, l'origine deve essere un ID di risorsa cartella di lavoro condivisa. Premere *carica* e il contenuto del gruppo verrà caricato da tale sottomodello anziché salvato nella cartella di lavoro esterna.

## <a name="next-steps"></a>Passaggi successivi
- [Cenni preliminari sulle cartelle di lavoro](workbooks-overview.md)
- [Utilizzo di JSONPath con cartelle di lavoro](workbooks-jsonpath.md)