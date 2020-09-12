---
title: Renderer della barra composita della cartella di lavoro Monitoraggio di Azure
description: Informazioni su tutte le visualizzazioni di renderer della barra composita della cartella di lavoro di monitoraggio di Azure.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 9/04/2020
ms.author: lagayhar
ms.openlocfilehash: 76c52d47c7fd7e271f3e74439ee8502117eb83a7
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664306"
---
# <a name="composite-bar-renderer"></a>Renderer della barra composita

La cartella di lavoro consente il rendering dei dati tramite la barra composita, una barra composta da più barre.

L'immagine seguente mostra la barra composita per lo stato del database che rappresenta il numero di server online, offline e in stato di recupero.

![Screenshot della barra composita per lo stato del database.](./media/workbooks-composite-bar/database-status.png)

Il renderer della barra composita è supportato per le visualizzazioni Grid, Tiles e graphs.

## <a name="adding-composite-bar-renderer"></a>Aggiunta del renderer della barra composita

1. Passare alla modalità di modifica della cartella di lavoro selezionando *modifica* elemento della barra degli strumenti.
2. Selezionare *Aggiungi* e quindi *Aggiungi query*.
3. Impostare l' *origine dati* su "JSON" e la *visualizzazione* su "Grid".
4. Aggiungere i dati JSON seguenti.

```json
[
    {"sub":"X", "server": "A", "online": 20, "recovering": 3, "offline": 4, "total": 27},
    {"sub":"X", "server": "B", "online": 15, "recovering": 8, "offline": 5, "total": 28},
    {"sub":"Y", "server": "C", "online": 25, "recovering": 4, "offline": 5, "total": 34},
    {"sub":"Y", "server": "D", "online": 18, "recovering": 6, "offline": 9, "total": 33}
]
```

5. Eseguire la query.
6. Selezionare le **impostazioni delle colonne** per aprire le impostazioni.
7. Selezionare "totale" dalle *colonne* e scegliere "barra composita" per il *renderer della colonna*.
8. Impostare le impostazioni seguenti in *impostazioni della barra composita*.

| Nome colonna | Color        |
|-------------|--------------|
| online      | Green        |
| ripristino  | Giallo       |
| Offline     | Rosso (chiaro) |

9. Aggiungi etichetta:`["online"] of ["total"] are healthy`
10. Nelle impostazioni delle colonne per online, offline e ripristino è possibile impostare il renderer della colonna su "hidden" (facoltativo).
11. Selezionare *etichette* nella parte superiore e aggiornare l'etichetta per la colonna Total come "stato del database" (facoltativo).
12. Seleziona su **applica**

Le impostazioni della barra composita hanno un aspetto simile alla schermata seguente:

![Screenshot delle impostazioni della colonna della barra composita con le impostazioni descritte in precedenza.](./media/workbooks-composite-bar/composite-bar-settings.png)

La barra composita con le impostazioni precedenti:

![Screenshot della barra composita.](./media/workbooks-composite-bar/composite-bar.png)

## <a name="composite-bar-settings"></a>Impostazioni della barra composita

Selezionare il nome della colonna e il colore corrispondente per eseguire il rendering di tale colonna in tale colore come parte della barra composita. È possibile inserire, eliminare e spostare righe verso l'alto e verso il basso.

### <a name="label"></a>Etichetta

L'etichetta della barra composita viene visualizzata nella parte superiore della barra composita. È possibile utilizzare una combinazione di testo statico, colonne e parametro.  Se l'etichetta è vuota, il valore delle colonne correnti viene visualizzato come etichetta. Nell'esempio precedente, se si lascia il campo etichetta nero, viene visualizzato il valore delle colonne totali.

Vedere le colonne con `["columnName"]` .

Fare riferimento ai parametri con `{paramName}` .

Il nome della colonna e del parametro fanno distinzione tra maiuscole e minuscole. È anche possibile rendere le etichette un collegamento selezionando "crea questo elemento come collegamento" e quindi Aggiungi impostazioni di collegamento.

### <a name="aggregation"></a>Aggregazione

Le aggregazioni sono utili per le visualizzazioni Tree/Group by. I dati di una colonna per la riga di gruppo vengono decisi dal set di aggregazioni per la colonna. Per le barre composite sono applicabili tre tipi di aggregazioni: None, Sum ed inherit.

Per aggiungere le impostazioni di raggruppamento:

1. In Impostazioni colonna passare alla colonna a cui si desidera aggiungere le impostazioni.
2. In *Tree/Group by Settings* in *Tree Type*Selezionare **Group by**
3. Selezionare il campo in base al quale eseguire il raggruppamento.

![Screenshot delle impostazioni di raggruppamento.](./media/workbooks-composite-bar/group-by-settings.png)

#### <a name="none"></a>nessuno

Nessuna aggregazione significa che non viene visualizzato alcun risultato per la colonna per le righe di gruppo.

![Screenshot della barra composita con nessuna aggregazione.](./media/workbooks-composite-bar/none.png)

#### <a name="sum"></a>SUM

Se l'aggregazione è impostata come Sum, nella colonna della riga di gruppo verrà visualizzata la barra composita utilizzando la somma delle colonne utilizzate per eseguirne il rendering. L'etichetta utilizzerà anche la somma delle colonne a cui si fa riferimento.

Nell'esempio seguente online, offline e il ripristino di tutte le funzioni di aggregazione max sono impostate su di essi e l'aggregazione per la colonna Total è Sum.

![Screenshot della barra composita con aggregazione Sum.](./media/workbooks-composite-bar/sum.png)

#### <a name="inherit"></a>Eredita

Se l'aggregazione è impostata come inherit, nella colonna della riga di gruppo verrà visualizzata la barra composita utilizzando l'aggregazione impostata dagli utenti per le colonne utilizzate per eseguirne il rendering. Anche le colonne utilizzate nell'etichetta utilizzano l'aggregazione impostata dall'utente. Se il renderer della colonna corrente è una barra composta ed è indicato nell'etichetta (ad esempio "Total" nell'esempio precedente), Sum viene usato come aggregazione per la colonna.

Nell'esempio riportato di seguito, le aggregazioni in linea, non in linea e di recupero sono impostate su Max e l'aggregazione per la colonna Total è Inherit.

![Screenshot della barra composita con ereditarietà dell'aggregazione.](./media/workbooks-composite-bar/inherit.png)

## <a name="sorting"></a>Ordinamento

Per le visualizzazioni griglia, l'ordinamento delle righe per la colonna con il renderer della barra composita funziona in base al valore che rappresenta la somma delle colonne utilizzate per il rendering dinamico del computer della barra composita. Negli esempi precedenti, il valore utilizzato per l'ordinamento è la somma delle colonne online, di recupero e offline per la riga specifica.

## <a name="tiles-visualization"></a>Visualizzazione riquadri

1. Selezionare **Aggiungi** e *Aggiungi query*.
2. Modificare l'origine dati in JSON immettere i dati dell' [esempio precedente](#adding-composite-bar-renderer).
3. Modificare la visualizzazione in *riquadri*.
4. Eseguire la query.
5. Selezionare **le impostazioni del riquadro**.
6. Selezionare *Left* nei campi title.
7. Immettere le impostazioni seguenti in *Impostazioni campo*.
    1. Usare la colonna: "Server".
    2. Renderer di colonna: "testo".
8. Selezionare *inferiore* in campi titolo.
9. Immettere le impostazioni seguenti in *Impostazioni campo*.
    1. USA colonna: "totale".
    2. Renderer della colonna: "barra composita".
    3. Immettere imposta le impostazioni seguenti in "impostazioni della barra composita".

    | Nome colonna | Color        |
    |-------------|--------------|
    | online      | Green        |
    | ripristino  | Giallo       |
    | Offline     | Rosso (chiaro) |

    4. Aggiungi etichetta: `["online"] of ["total"] are healthy` .
10. Selezionare **Applica**.

Impostazioni della barra composita per i titoli:

![Screenshot delle impostazioni del titolo della barra composita con le impostazioni descritte in precedenza.](./media/workbooks-composite-bar/tiles-settings.png)

La visualizzazione della barra composita per i riquadri con le impostazioni precedenti sarà simile alla seguente:

![Screenshot dei riquadri della barra composita.](./media/workbooks-composite-bar/composite-bar-tiles.png)

## <a name="graphs-visualization"></a>Visualizzazione grafici

Per creare un renderer della barra composita per la visualizzazione dei grafici (digitare cluster hive), seguire le istruzioni riportate di seguito.

1. Selezionare **Aggiungi** e *Aggiungi query*.
2. Modificare l'origine dati in JSON immettere i dati dell' [esempio precedente](#adding-composite-bar-renderer).
3. Modificare la visualizzazione in *riquadri*.
4. Eseguire la query.
5. Selezionare **Impostazioni grafico**.
6. Selezionare *centro contenuto* in Impostazioni formato nodo.
7. Immettere le impostazioni seguenti in *Impostazioni campo*.
    1. USA colonna: "totale".
    2. Renderer della colonna: "barra composita".
    3. Immettere le impostazioni seguenti in *Impostazioni barra composita*.

    |Nome colonna  |     Color    |
    |-------------|--------------|
    | online      | Green        |
    | ripristino  | Giallo       |
    | Offline     | Rosso (chiaro) |

   4. Aggiungi etichetta: `["online"] of ["total"] are healthy` .
9. Immettere le impostazioni seguenti in *Impostazioni layout*.
    1. Tipo di grafico: **cluster hive**.
    2. ID nodo selezionare: "Server".
    3. Raggruppa per campo: "None".
    4. Dimensioni nodo: 100.
    5. Margine tra gli esagoni: 5.
    6. Tipo di colorazione: **None**.
1. Selezionare **Applica**.
    
Impostazioni della barra composita per i grafici:

![Screenshot delle impostazioni del grafico a barre composite con le impostazioni descritte in precedenza.](./media/workbooks-composite-bar/graphs-settings.png)

La visualizzazione a barre composite per Graph con le impostazioni precedenti sarà simile alla seguente:

![Screenshot dei grafici a barre composite con cluster hive.](./media/workbooks-composite-bar/composite-bar-graphs.png)

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire](workbooks-automate.md) cartelle di lavoro con Azure Resource Manager.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.