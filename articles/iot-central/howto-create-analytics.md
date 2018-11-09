---
title: Analizzare i dati dei dispositivi nell'applicazione Azure IoT Central | Microsoft Docs
description: Analizzare i dati dei dispositivi nell'applicazione Azure IoT Central.
author: lmasieri
ms.author: lmasieri
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c170f573a473d3fdfca5c916b4be594dc93d6b27
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50156324"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Come usare le analisi per analizzare i dati dei dispositivi


*Questo articolo è rivolto a operatori, autori e amministratori.*


Azure IoT Central offre funzionalità avanzate di analisi che consentono di comprendere le grandi quantità di dati provenienti dai dispositivi. Per iniziare, visitare **Analytics** nel menu di navigazione a sinistra. 

  ![Navigazione IoT Central per l'analitica](media\howto-create-analytics\analytics-navigation.png)

## <a name="querying-your-data"></a>Query dei dati

È necessario scegliere un **set di dispositivi**, aggiungere un **filtro** (facoltativo) e selezionare un **periodo di tempo** per iniziare. Al termine, fare clic su *Mostra i risultati* per iniziare a visualizzare i dati.


* **Set di dispositivi:** un [set di dispositivi](howto-use-device-sets.md) è un gruppo di dispositivi definito dall'utente. Ad esempio, tutti frigoriferi in Oakland o tutte le turbine eoliche rev 2.0.

<!---
to-do: confirm if 10 is the max number of filters
to-do: do we need to explain how fiters work?
--->

* **Filtri:** è facoltativo aggiungere dei filtri per la ricerca per individuare i dati. È possibile aggiungere fino a 10 filtri alla volta. Ad esempio, all'interno di tutti i frigoriferi in Oakland, trovare quelli che hanno avuto temperatura al di sopra dei 60 gradi. 
* **Periodo di tempo:** per impostazione predefinita verranno recuperati i dati dagli ultimi 10 minuti. È possibile modificare questo valore su uno degli intervalli di tempo predefiniti o selezionare un periodo di tempo personalizzato. 

 ![Query Analytics](media\howto-create-analytics\analytics-query.png)

## <a name="visualizing-your-data"></a>Visualizzazione dei dati

Una volta che è stata eseguita una query dei dati, sarà possibile avviare la visualizzazione. È possibile mostrare/nascondere le misure, modificare il modo in cui i dati sono aggregati e suddividere ulteriormente i dati per le proprietà diverse del dispositivo.  

* **Divisione:** prevede la suddivisione dei dati in base alle proprietà del dispositivo, consentendo di indagare ulteriormente i dati. Ad esempio, è possibile suddividere i risultati in base all'ID dispositivo o alla posizione.
<!---
to-do: confirm if 10 is the max number of measurements
--->
* **Misure:** è possibile scegliere di mostrare/nascondere fino a 10 elementi di telemetria diversi allo stesso tempo, segnalati dai dispositivi. Le misure sono ad esempio, temperatura e umidità. 
* **Aggregazione:** per impostazione predefinita i dati sono aggregati in base alla media, ma è possibile scegliere di modificare l'aggregazione sulla base delle esigenze. 

   ![Visualizzazione di Analytics](media\howto-create-analytics\analytics-visualize.png) <br/><br/>
   ![Visualizzazione di Analytics suddivisa in](media\howto-create-analytics\analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interazione con i dati

Sono disponibili varie modalità attraverso cui è possibile modificare ulteriormente i risultati delle query per soddisfare le esigenze di visualizzazione. È possibile alternare tra una visualizzazione a grafico e una visualizzazione a griglia, eseguire lo zoom avanti o indietro, aggiornare il set di dati e modificare la modalità in cui vengono visualizzate le righe.

* **Mostra griglia:** i risultati saranno disponibili in formato di tabella per consentire di visualizzare il valore specifico per ogni punto dati. Questa visualizzazione soddisfa anche gli standard di accessibilità. 
* **Mostra il grafico:** i risultati verranno visualizzati in un formato di riga per consentire di individuare facilmente i trend verso l'alto o verso il basso e le anomalie. 

 ![Passare alla visualizzazione griglia per l'analitica](media\howto-create-analytics\analytics-showgrid.png)

Zoom consente di focalizzarsi sui dati. Se si trova un periodo di tempo nel quale si desidera concentrarsi all'interno del set di risultati, usare il cursore per acquisire l'area che si intende ingrandire e usare i controlli disponibili per eseguire una delle seguenti azioni:
* **Eseguire lo zoom avanti:** dopo aver selezionato un periodo di tempo, lo zoom avanti sarà abilitato, consentendo di ingrandire i dati.
* **Eseguire lo zoom indietro:** questo controllo consente di eseguire lo zoom di un livello indietro all'ultimo zoom. Ad esempio, se i dati sono stati ingranditi tre volte, zoom indietro riporterà indietro di un livello alla volta.
* **Reimpostare zoom:** dopo aver eseguito diversi livelli di zoom, è possibile usare il controllo di reimpostazione dello zoom per ritornare al set di risultati originale. 

 ![Eseguire lo zoom avanti sui dati](media\howto-create-analytics\analytics-zoom.png)


È possibile modificare lo stile della riga per soddisfare diverse esigenze. È possibile scegliere tra quattro opzioni:
* **Linea:** verrà configurata una linea retta tra ciascuno dei punti dei dati. 
* **Smooth:** verrà configurata una linea curva tra ogni punto
* **Passaggio:** una linea tra ogni punto nel grafico creerà un grafico di passaggio
* **A dispersione:** tutti i punti saranno tracciati sul grafico senza linee che li connettono. 

 ![Tipi di linea diversi disponibili in Analytics](media\howto-create-analytics\analytics-linetypes.png)

Infine, è possibile disporre i dati sull'asse y, scegliendo una delle tre modalità:

* **In pila:** i grafici per ogni misura sono impilati e ciascuno dei grafici ha il proprio asse y. I grafici in pila sono utili quando si hanno più misure selezionate e si vuole avere una singola visualizzazione delle misure.
* **Istogrammi:** viene tracciato un grafico per ogni misura rispetto a un solo asse y, ma i valori per l'asse y cambiano in base alla misura evidenziata. Gli istogrammi sono utili quando si vuole sovrapporre più misure e si vuole vedere gli schemi fra le misure per lo stesso intervallo di tempo.
* **Asse y condiviso:** tutti i grafici condividono lo stesso asse y e i valori per l'asse non cambiano. I grafici con l'asse y condiviso sono utili quando si vuole esaminare una sola misura suddividendo i dati in base a qualche criterio.

 ![Disporre i dati tra l'asse y con diverse modalità di visualizzazione](media\howto-create-analytics\analytics-yaxis.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per creare analisi personalizzate per l'applicazione Azure IoT Central, il prossimo passo suggerito è:

> [!div class="nextstepaction"]
> [Preparare e connettere un'applicazione Node.js](howto-connect-nodejs.md)