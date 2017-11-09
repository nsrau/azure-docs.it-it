---
title: Comprendere i clienti in Azure Application Insights | Microsoft Docs
description: Esercitazione sull'uso di Azure Application Insights per comprendere in che modo i clienti usano l'applicazione.
keywords: 
services: application-insights
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: c0175d4fb98614d41423b7204fe7eb2aa50d23dc
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Usare Azure Application Insights per comprendere in che modo i clienti usano l'applicazione

Azure Application Insights raccoglie informazioni sull'utilizzo che consentono di capire in che modo gli utenti interagiscono con l'applicazione.  Questa esercitazione illustra le diverse risorse disponibili per analizzare queste informazioni.  Si apprenderà come:

> [!div class="checklist"]
> * Analizzare i dettagli relativi agli utenti che accedono all'applicazione
> * Usare le informazioni sulla sessione per analizzare in che modo i clienti usano l'applicazione
> * Definire grafici a imbuto che consentono di confrontare l'attività degli utenti desiderata rispetto a quella effettiva 
> * Creare una cartella di lavoro per consolidare visualizzazioni e query in un unico documento
> * Raggruppare gli utenti con caratteristiche simili per analizzarli insieme
> * Sapere quali utenti continuano a usare l'applicazione
> * Controllare la modalità di navigazione degli utenti all'interno dell'applicazione


## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

- Installare [Visual Studio 2017](https://www.visualstudio.com/downloads/) con i carichi di lavoro seguenti:
    - Sviluppo Web e ASP.NET
    - Sviluppo di Azure
- Scaricare e installare il [debugger di snapshot di Visual Studio](http://aka.ms/snapshotdebugger).
- Distribuire un'applicazione .NET in Azure e [abilitare Application Insights SDK](app-insights-asp-net.md). 
- [Inviare dati di telemetria dall'applicazione](app-insights-usage-overview.md#send-telemetry-from-your-app) per aggiungere visualizzazioni pagina/eventi personalizzati.
- Inviare il [contesto utente](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-usage-send-user-context) per tenere traccia delle operazioni eseguite da un utente nel corso del tempo e sfruttare al meglio le funzionalità di utilizzo.

## <a name="log-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="get-information-about-your-users"></a>Ottenere informazioni sugli utenti
Il pannello **Utenti** consente di ottenere informazioni importanti relative agli utenti in diversi modi. È possibile usare questo pannello per sapere da dove si connettono gli utenti, conoscere i dettagli del client che usano e sapere a quali aree dell'applicazione accedono. 

1. Selezionare **Application Insights** e quindi selezionare la sottoscrizione in uso.
2. Scegliere **Utenti** dal menu.
3. La visualizzazione predefinita mostra il numero di utenti univoci connessi all'applicazione nelle ultime 24 ore.  È possibile modificare l'intervallo di tempo e impostare diversi altri criteri per filtrare queste informazioni.

    ![Generatore di query](media\app-insights-tutorial-users\QueryBuilder.png)

6. Fare clic sull'elenco a discesa **Durante** e modificare l'intervallo di tempo impostandolo su 7 giorni.  In questo modo si aumentano i dati inclusi nei diversi grafici del pannello.

    ![Modificare l'intervallo di tempo](media\app-insights-tutorial-users\TimeRange.png)

4. Fare clic sull'elenco a discesa **Split by** (Dividi per) per aggiungere al grafico una scomposizione in base a una proprietà utente.  Selezionare **Paese o area geografica**.  Il grafico include gli stessi dati, ma consente di visualizzare una scomposizione del numero di utenti in base ai diversi paesi.

    ![Grafico Paese o area geografica](media\app-insights-tutorial-users\CountryorRegion.png)

5. Posizionando il cursore sulle diverse barre del grafico, si nota che il conteggio per ogni paese riflette solo l'intervallo di tempo rappresentato da quella barra.
6. La colonna **Informazioni dettagliate** a destra riporta le analisi effettuate sui dati utente.  Sono disponibili informazioni quali il numero di sessioni univoche per il periodo di tempo considerato e i record con proprietà comuni estrapolati dai dati utente più significativi. 

    ![Colonna Informazioni dettagliate](media\app-insights-tutorial-users\insights.png)


## <a name="analyze-user-sessions"></a>Analizzare le sessioni utente
Il pannello **Sessioni** è simile al pannello **Utenti**.  Se da una parte il pannello **Utenti** consente di ottenere informazioni dettagliate sugli utenti che hanno avuto accesso all'applicazione, il pannello **Sessioni** permette di comprendere in che modo tali utenti hanno usato l'applicazione.  

1. Scegliere **Sessioni** dal menu.
2. Osservare il grafico: sono disponibili le stesse opzioni del pannello **Utenti** per filtrare e suddividere i dati.

    ![Sessioni - Generatore di query](media\app-insights-tutorial-users\SessionsBuilder.png)

3. Il riquadro **Esempio di queste sessioni** sulla destra elenca le sessioni che includono un numero elevato di eventi.  Si tratta di sessioni interessanti da analizzare.

    ![Esempio di queste sessioni](media\app-insights-tutorial-users\SessionsSample.png)

4. Fare clic su una delle sessioni per visualizzare la relativa **Sequenza temporale sessione**, che mostra tutte le operazioni nelle sessioni.  Ciò consente di identificare informazioni quali le sessioni con un numero elevato di eccezioni.

    ![Sequenza temporale sessione](media\app-insights-tutorial-users\SessionsTimeline.png)

## <a name="group-together-similar-users"></a>Raggruppare gli utenti con caratteristiche simili
Una **coorte** è un set di utenti raggruppati in base a caratteristiche simili.  È possibile usare le coorti per filtrare i dati in altri pannelli in modo da analizzare specifici gruppi di utenti,  ad esempio per analizzare solo gli utenti che hanno completato un acquisto.

1.  Scegliere **Coorti** dal menu.
2.  Fare clic su **Nuova** per creare una nuova coorte.
3.  Selezionare l'elenco a discesa **Who used** (Usato da) e selezionare un'azione.  Verranno inclusi solo gli utenti che hanno eseguito questa azione nell'intervallo di tempo del report.

    ![Coorte degli utenti che hanno eseguito le azioni specificate](media\app-insights-tutorial-users\CohortsDropdown.png)

4.  Scegliere **Utenti** dal menu.
5.  Nell'elenco a discesa **Mostra** selezionare la coorte appena creata.  I dati per il grafico sono limitati a tali utenti.

    ![Coorte nello strumento Utenti](media\app-insights-tutorial-users\UsersCohort.png)


## <a name="compare-desired-activity-to-reality"></a>Confrontare l'attività desiderata con quella effettiva
Mentre i pannelli precedenti sono incentrati sulle attività eseguite dagli utenti dell'applicazione, il pannello **Imbuti** prende in esame le attività che si vuole che gli utenti eseguano.  Un grafico a imbuto rappresenta un set di passaggi all'interno dell'applicazione e la percentuale di utenti che ha eseguito i diversi passaggi.  Ad esempio, è possibile creare un grafico a imbuto per misurare la percentuale di utenti che si connette all'applicazione e che cerca un prodotto.  È quindi possibile visualizzare la percentuale di utenti che aggiunge tale prodotto al carrello e quindi la percentuale di utenti che completa un acquisto.

1. Scegliere **Imbuti** dal menu e quindi fare clic su **Nuovo**. 

    ![](media\app-insights-tutorial-users\funnelsnew.png)

2. Specificare un nome in **Nome imbuto**.
3. Creare un grafico a imbuto con almeno due passaggi selezionando un'azione per ogni passaggio.  L'elenco di azioni viene creato da dati sull'utilizzo raccolti da Application Insights.

    ![](media\app-insights-tutorial-users\funnelsedit.png)

4. Fare clic su **Salva** per salvare il grafico a imbuto e quindi visualizzare i risultati.  La finestra a destra del grafico a imbuto mostra gli eventi più comuni prima della prima attività e dopo l'ultima attività per comprendere le tendenze degli utenti per la specifica sequenza.

    ![](media\app-insights-tutorial-users\funnelsright.png)


## <a name="learn-which-customers-return"></a>Comprendere quali clienti tornano a usare l'applicazione
Il pannello **Conservazione** aiuta a identificare quali utenti tornano a usare l'applicazione.  

1. Scegliere **Conservazione** dal menu.
2. Per impostazione predefinita, le informazioni analizzate includono gli utenti che hanno eseguito un'azione e quindi sono tornati a usare l'applicazione per eseguirne altre.  È possibile modificare questo filtro in modo da includere qualsiasi categoria, ad esempio solo gli utenti che sono tornati a usare l'applicazione dopo aver completato un acquisto.

    ![](media\app-insights-tutorial-users\retentionquery.png)

3. Gli utenti che tornano a usare l'applicazione e che soddisfano i criteri specificati vengono visualizzati sotto forma di grafico e tabella per intervalli di tempo diversi.  Il modello tipico prevede un calo graduale nel corso del tempo degli utenti che tornano a usare l'applicazione.  Un calo improvviso da un momento all'altro potrebbe essere motivo di preoccupazione. 

    ![](media\app-insights-tutorial-users\retentiongraph.png)

## <a name="analyze-user-navigation"></a>Analizzare la navigazione degli utenti
Un **flusso utente** permette di visualizzare il modo in cui gli utenti si spostano tra le pagine e le funzionalità dell'applicazione.  Ciò consente di identificare, ad esempio, dove si spostano in genere gli utenti che accedono a una determinata pagina, come escono di norma dall'applicazione e se ci sono azioni che vengono ripetute regolarmente.

1.  Scegliere **Flussi utente** dal menu.
2.  Fare clic su **Nuovo** per creare un nuovo flusso utente e quindi fare clic su **Modifica** per modificarne i dettagli.
3.  Aumentare l'**Intervallo di tempo** impostandolo su 7 giorni e quindi selezionare un evento iniziale.  Il flusso terrà traccia delle sessioni utente che iniziano con tale evento.

    ![](media\app-insights-tutorial-users\flowsedit.png)

4.  Viene visualizzato il flusso utente ed è possibile visualizzare i diversi percorsi degli utenti e i relativi conteggi delle sessioni.  Le linee blu indicano un'azione che l'utente ha eseguito dopo l'azione corrente.  Una linea rossa indica la fine della sessione utente.

    ![](media\app-insights-tutorial-users\flows.png)

5.  Per rimuovere un evento dal flusso, fare clic sulla **x** nell'angolo dell'azione e quindi fare clic su **Crea grafico**.  Il grafico viene ridisegnato con tutte le istanze di tale evento rimosse.  Fare clic su **Modifica** per verificare che l'evento venga aggiunto in **Eventi esclusi**.

    ![](media\app-insights-tutorial-users\flowsexclude.png)

## <a name="consolidate-usage-data"></a>Consolidare i dati sull'utilizzo
Le **cartelle di lavoro** combinano le visualizzazioni dei dati, le query di Analytics e il testo in documenti interattivi.  È possibile usare le cartelle di lavoro per raggruppare le informazioni sull'utilizzo comuni, consolidare le informazioni di uno specifico evento imprevisto o creare report per il team sull'uso dell'applicazione.

1.  Scegliere **Cartelle di lavoro** dal menu.
2.  Fare clic su **Nuova** per creare una nuova cartella di lavoro.
3.  È già disponibile una query che include tutti i dati sull'utilizzo nell'ultimo giorno, visualizzati sotto forma di grafico a barre.  È possibile usare questa query, modificarla manualmente o fare clic su **Query di esempio** per sceglierne una da altre query utili.

    ![](media\app-insights-tutorial-users\samplequeries.png)

4.  Fare clic su **Modifica completata**.
5.  Fare clic su **Modifica** nel riquadro superiore per modificare il testo nella parte superiore della cartella di lavoro,  che viene formattato tramite markdown.

    ![](media\app-insights-tutorial-users\markdown.png)

6.  Fare clic su **Aggiungi utenti** per aggiungere un grafico con le informazioni utente.  Modificare i dettagli del grafico, se necessario, e quindi fare clic su **Modifica completata** per salvarlo.


## <a name="next-steps"></a>Passaggi successivi
Dopo avere appreso come analizzare gli utenti, passare all'esercitazione successiva per imparare a creare dashboard personalizzati che consentono di combinare queste informazioni con altri dati utili sull'applicazione.

> [!div class="nextstepaction"]
> [Creare dashboard personalizzati](app-insights-tutorial-dashboards.md)