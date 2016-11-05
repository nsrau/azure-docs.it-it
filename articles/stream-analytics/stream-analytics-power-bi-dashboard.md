---
title: Dashboard di Power BI su Analisi di flusso| Microsoft Docs
description: Utilizzare un dashboard di Power BI streaming in tempo reale per raccogliere intelligence aziendali e analizzare i dati di volumi elevati di un processo di Analisi di flusso.
keywords: dashboard di analisi, dashboard in tempo reale
services: stream-analytics
documentationcenter: ''
author: jeffstokes72
manager: jhubbard
editor: cgronlun

ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 09/26/2016
ms.author: jeffstok

---
# Analisi di flusso e Power BI: un dashboard di analisi in tempo reale per il flusso di dati
Analisi di flusso di Azure consente l’uso di uno dei principali strumenti di business intelligence, Microsoft Power BI. Imparare a usare Analisi di flusso di Azure per l'analisi di volumi elevati di flussi di dati e per ottenere informazioni in un dashboard di analisi di Power BI in tempo reale.

Utilizzare [Microsoft Power BI](https://powerbi.com/) per creare rapidamente un dashboard in tempo reale. [Guardare un video che illustra lo scenario](https://www.youtube.com/watch?v=SGUpT-a99MA).

Questo articolo descrive come creare strumenti di business intelligence personalizzati usando Power BI come output per i processi di Analisi di flusso di Azure e usando un dashboard in tempo reale.

## Prerequisiti
* Account di Microsoft Azure
* Un input per il processo di Analisi di flusso da cui utilizzare il flusso di dati. Analisi di flusso accetta l'input dall'hub eventi di Azure o dell’archiviazione BLOB di Azure.
* Account aziendale o dell'istituto di istruzione per Power BI

## Creare processi di analisi di flusso di Azure
Dal [portale di Azure classico](https://manage.windowsazure.com), fare clic su **Nuovo, Servizi dati, Analisi di flusso, Creazione rapida**.

Specificare i valori seguenti, quindi fare clic su **Crea processo di analisi dei flussi**:

* **Nome processo**: immettere un nome per il processo. Ad esempio, **DeviceTemperatures**.
* **Area**: selezionare l’area in cui collocare il processo. Considerare la possibilità di collocare il processo e l’hub eventi nella stessa area per garantire prestazioni ottimali ed evitare di incorrere in costi di trasferimento dei dati tra le aree.
* **Account di archiviazione**: scegliere l'account di archiviazione da usare per archiviare i dati di monitoraggio per tutti i processi di Analisi dei flussi in esecuzione all'interno dell'area scelta. È possibile scegliere un account di archiviazione esistente o crearne uno nuovo.

Fare clic su **Analisi dei flussi** nel riquadro sinistro per visualizzare un elenco dei processi di Analisi dei flussi.

![graphic1][graphic1]

> [!TIP]
> Il nuovo processo verrà visualizzato nell'elenco con lo stato **Non avviato**. Si noti che il pulsante **Avvia** nella parte inferiore della pagina è disabilitato. Questo è un comportamento previsto, poiché è necessario configurare input, output, query del processo e così via, prima di avviare il processo.
> 
> 

## Specificare l'input del processo
Per questa esercitazione, si presuppone che l’utente utilizzi l’hub di eventi come input con serializzazione JSON e codifica UTF-8.

* Fare clic sul nome del processo.
* Fare clic su **Input** nella parte superiore della pagina, quindi scegliere **Aggiungi input**. La finestra di dialogo visualizzata presenterà un numero di passaggi per l'impostazione dell'input.
* Selezionare **Flusso dati**, quindi fare clic sul pulsante a destra.
* Selezionare **Hub eventi**, quindi fare clic sul pulsante a destra.
* Digitare o selezionare i valori seguenti nella terza pagina:
  * **Alias dell’input** - Immettere un nome descrittivo per l'input del processo. Si noti che il nome verrà usato nella query in un secondo momento.
  * **Hub eventi**: se l'hub eventi creato si trova nella stessa sottoscrizione del processo di Analisi di flusso, selezionare lo spazio dei nomi in cui si trova l'hub eventi.
* Se l'hub eventi si trova in un'altra sottoscrizione, selezionare **Usa hub eventi da un'altra sottoscrizione**, quindi immettere manualmente i valori per **Spazio dei nomi del bus di servizio**, **Nome hub eventi**, **Nome criterio hub eventi**, **Chiave criterio hub eventi** e **Conteggio partizioni hub eventi**.

> [!NOTE]
> Questo esempio usa il numero predefinito di partizioni, che è pari a 16.
> 
> 

* **Nome dell’hub eventi** - selezionare il nome dell’hub eventi di Azure disponibile.
* **Nome criterio dell’hub eventi** - selezionare il criterio dell’hub eventi per l’hub eventi utilizzato. Assicurarsi che il criterio disponga di autorizzazioni di gestione.
* **Gruppo utenti dell’hub eventi** - è possibile lasciare il campo vuoto o specificare un gruppo di utenti disponibile sull’hub eventi. Tenere presente che ogni gruppo di utenti di un hub eventi può avere solo 5 lettori alla volta. Pertanto, è necessario scegliere il gruppo di utenti appropriato al processo. Se si lascia il campo vuoto, verrà utilizzato il gruppo di utenti predefinito.
* Fare clic sul pulsante a destra.
* Specificare i valori seguenti:
  * **Formato serializzatore eventi** - JSON
  * **Codifica** - UTF8
* Fare clic sul pulsante con il segno di spunta per aggiungere l'origine e per verificare che Analisi dei flussi possa connettersi all'hub eventi.

## Aggiungere l’output Power BI
1. Fare clic su **Output** nella parte superiore della pagina, quindi scegliere **Aggiungi output**. Verrà visualizzato Power BI come opzione di output nell’elenco.
   
   ![graphic2][graphic2]
2. Selezionare **Power BI**, quindi fare clic sul pulsante destro.
3. Verrà visualizzata una schermata analoga a quella seguente:
   
   ![graphic3][graphic3]
4. In questo passaggio, fornire un account aziendale o dell’istituto di istruzione per l'output del processo di analisi di flusso. Se si dispone già di un account Power BI, selezionare **Autorizza ora**. In caso contrario, scegliere **Effettua l’iscrizione ora**. [In questo blog sono riportati passo passo i dettagli di registrazione a Power BI](http://blogs.technet.com/b/powerbisupport/archive/2015/02/06/power-bi-sign-up-walkthrough.aspx).
   
   ![graphic11][graphic11]
5. Successivamente verrà visualizzata una schermata analoga a quella seguente:
   
   ![graphic4][graphic4]

Fornire i valori come mostrato di seguito:

* **Alias dell’output** – È possibile usare qualsiasi alias dell’output purché sia di facile riferimento. L’alias dell’output risulta essere particolarmente utile se si decide di avere più output per il processo. In tal caso, è necessario fare riferimento a questo output nella query. Ad esempio, utilizziamo il valore dell’alias di output = “OutPbi”.
* **Nome set di dati** - Fornire un nome set di dati all’output Power BI. Ad esempio, “pbidemo”.
* **Nome tabella** - Fornire un nome tabella nel set di dati dell’output Power BI. Ad esempio, “pbidemo”. Attualmente, l’output di Power BI da processi di Analisi di flusso può avere solo una tabella in un set di dati.
* **Area di lavoro** – Selezionare nel tenant Power BI un'area di lavoro in cui verrà creato il set di dati.

> [!NOTE]
> Non è necessario creare il set di dati e la tabella nell'account Power BI. Verranno creati automaticamente quando si avvia il processo di analisi di flusso e gli output vengono trasmessi a Power BI. Se la query del processo non restituisce risultati, il set di dati e la tabella non verranno creati.
> 
> 

* Fare clic su **OK**, **Connessione di test**. A questo punto la configurazione dell’output è completa.

> [!WARNING]
> Inoltre, tenere presente che se Power BI dispone già di un seti di dati e di una tabella caratterizzati dal nome fornito dall'utente nel processo di analisi di flusso, i dati esistenti verranno sovrascritti.
> 
> 

## Scrivere query
Andare nella scheda **Query** del processo. Scrivere la query, l’output che si desidera avere in Power BI. Ad esempio, potrebbe essere simile alla query SQL seguente:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,1),
        dspl



Avviare il processo. Confermare che l’hub eventi riceve eventi e che la query genera i risultati previsti. Se la query produce 0 righe, il set di dati e le tabelle Power BI non verranno creati automaticamente.

## Creare il dashboard in Power BI
Andare al sito [Powerbi.com](https://powerbi.com) e accedere con l’account aziendale o dell’istituto di istruzione. Se la query del processo di Analisi di flusso produce risultati, verrà visualizzato il set di dati già creato:

![graphic5][graphic5]

Per creare il dashboard, andare all’opzione Dashboard e creare un nuovo dashboard.

![graphic6][graphic6]

In questo esempio, verrà nominato "Demo Dashboard".

A questo punto, fare clic sul set di dati creato dal processo di Analisi di flusso (pbidemo nell’esempio corrente). Si verrà indirizzati a una pagina per creare un grafico su questo set di dati. Il seguente è solo un esempio dei report che è possibile creare:

Selezionare i campi e Σ temp e ora. Questi saranno automaticamente utilizzati in Valore e Asse del grafico:

![graphic7][graphic7]

In questo modo, si otterrà automaticamente un grafico come riportato:

![graphic8][graphic8]

Nella sezione relativa, fare clic sull’elenco a discesa per il valore temp e scegliere **media** per la temperatura e sul grafico, fare clic su **visualizzazione** e scegliere **grafico a linee**:

![graphic9][graphic9]

A questo punto si otterrà un grafico a linee della media nel tempo. Usando l’opzione di aggiunta come riportato, è possibile aggiungere l’elemento al dashboard creato in precedenza:

![graphic10][graphic10]

A questo punto, quando si visualizza il dashboard con il report aggiunto, si vedrà l’aggiornamento dei report in tempo reale. Provare a modificare i dati negli eventi – aumentare il valore temp o analogo e si visualizzerà in tempo reale l’effetto riportato sul dashboard.

Tenere presente che in questa esercitazione è stato mostrato come creare solo uno dei tipi di grafici possibili per un set di dati. Power BI consente di creare altri strumenti di business intelligence per clienti per l'organizzazione. Per visualizzare un altro esempio di dashboard Power BI, guardare il video [Introduzione a Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s).

Per altre informazioni sulla configurazione di un output di Power BI e per usare i gruppi di Power BI, vedere la [sezione Power BI](stream-analytics-define-outputs.md#power-bi) di [Informazioni sugli output di analisi di flusso](stream-analytics-define-outputs.md "Informazioni sugli output di analisi di flusso"). Un'altra risorsa utile per maggiori informazioni sulla creazione di dashboard con Power BI è [Dashboard in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## Limitazioni e procedure consigliate
Power BI impiega vincoli di concorrenza e velocità effettiva come descritto qui: [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Prezzi di Power BI")

A causa di tali vincoli Power BI soddisfa maggiormente quei casi in cui Analisi di flusso di Azure realizza una riduzione significativa del carico dei dati. 
Si consiglia l'uso di TumblingWindow o di HoppingWindow per garantire che il push di dati sia di massimo 1 push/secondo e che la query soddisfi i requisiti di velocità effettiva. È possibile usare l'equazione seguente per calcolare il valore da indicare nella finestra in pochi secondi:

![equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)

Ad esempio, se si dispone di 1.000 dispositivi che inviano dati ogni secondo, si utilizza la SKU di Power BI Pro che supporta 1.000.000 righe/ora e si desidera ottenere i dati medi per dispositivo su Power BI, è possibile effettuare massimo un push ogni 4 secondi per dispositivo (come mostrato di seguito):

![equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)

Pertanto sarà necessario modificare la query originale in:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl

### Aggiornamento di visualizzazione di Power BI
Una domanda comune è: "Perché il dashboard non si aggiorna automaticamente in Power BI?".

Per farlo, utilizzare in Power BI domande e risposte, porre una domanda come "Valore massimo per temp dove timestamp è oggi" e aggiungere il riquadro sul dashboard.

### Rinnovare l'autorizzazione
Se la password dell'account Power BI è stata modificata dopo la creazione o l'ultima autenticazione del processo, sarà necessario autenticare nuovamente l'account. Se Multi-Factor Authentication (MFA) è configurata nel tenant Azure Active Directory (AAD), sarà necessario rinnovare anche l'autorizzazione Power BI ogni due settimane. Un sintomo di questo problema è che non ci sono output del processo e un "Errore nell’autenticazione dell’utente" nei log delle operazioni:

![graphic12][graphic12]

Analogamente, se un processo tenta di avviarsi mentre il token è scaduto, si verificherà un errore e l'avvio del processo avrà esito negativo. L'errore avrà un aspetto simile al seguente:

![Errore di convalida di PowerBI](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-expire.png)

Per risolvere questo problema, arrestare il processo in esecuzione e passare all'output di Power BI. Fare clic sul collegamento "Rinnovare autorizzazione" e riavviare il processo dall'ultima volta che è stato arrestato per evitare la perdita di dati.

![Rinnovo convalida di PowerBI](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renew.png)

Dopo l'aggiornamento dell'autorizzazione con Power BI, verrà visualizzato un avviso verde nell'area di autorizzazione:

![Rinnovo convalida di PowerBI](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renewed.png)

## Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureStreamAnalytics)

## Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[graphic1]: ./media/stream-analytics-power-bi-dashboard/1-stream-analytics-power-bi-dashboard.png
[graphic2]: ./media/stream-analytics-power-bi-dashboard/2-stream-analytics-power-bi-dashboard.png
[graphic3]: ./media/stream-analytics-power-bi-dashboard/3-stream-analytics-power-bi-dashboard.png
[graphic4]: ./media/stream-analytics-power-bi-dashboard/4-stream-analytics-power-bi-dashboard.png
[graphic5]: ./media/stream-analytics-power-bi-dashboard/5-stream-analytics-power-bi-dashboard.png
[graphic6]: ./media/stream-analytics-power-bi-dashboard/6-stream-analytics-power-bi-dashboard.png
[graphic7]: ./media/stream-analytics-power-bi-dashboard/7-stream-analytics-power-bi-dashboard.png
[graphic8]: ./media/stream-analytics-power-bi-dashboard/8-stream-analytics-power-bi-dashboard.png
[graphic9]: ./media/stream-analytics-power-bi-dashboard/9-stream-analytics-power-bi-dashboard.png
[graphic10]: ./media/stream-analytics-power-bi-dashboard/10-stream-analytics-power-bi-dashboard.png
[graphic11]: ./media/stream-analytics-power-bi-dashboard/11-stream-analytics-power-bi-dashboard.png
[graphic12]: ./media/stream-analytics-power-bi-dashboard/12-stream-analytics-power-bi-dashboard.png
[graphic13]: ./media/stream-analytics-power-bi-dashboard/13-stream-analytics-power-bi-dashboard.png

<!---HONumber=AcomDC_0928_2016-->