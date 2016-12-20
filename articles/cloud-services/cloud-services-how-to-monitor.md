---
title: Come monitorare un servizio cloud | Documentazione Microsoft
description: Informazioni su come monitorare i servizi cloud usando il portale di Azure classico.
services: cloud-services
documentationcenter: 
author: rboucher
manager: timlt
editor: 
ms.assetid: 5c48d2fb-b8ea-420f-80df-7aebe2b66b1b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2015
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a8d581d678d35045d7d27df55ee1e629f259f30c


---
# <a name="how-to-monitor-cloud-services"></a>Come monitorare i servizi cloud
[!INCLUDE [disclaimer](../../includes/disclaimer.md)]

È possibile monitorare le metriche delle prestazioni `key` per i servizi cloud nel portale di Azure classico. È possibile impostare il livello di monitoraggio da minimo a dettagliato per ogni ruolo del servizio e personalizzare le schermate di monitoraggio. I dati di monitoraggio dettagliati vengono memorizzati in un account di archiviazione, a cui è possibile accedere fuori dal portale. 

Le schermate di monitoraggio nel portale di Azure classico sono altamente configurabili. È possibile scegliere le metriche che si vuole monitorare dall'elenco delle metriche nella pagina **Monitoraggio** e le metriche da riportare nei relativi grafici nella pagina **Monitoraggio** e nel dashboard. 

## <a name="concepts"></a>Concetti
Per impostazione predefinita, per un nuovo servizio cloud è previsto un monitoraggio minimo, con contatori delle prestazioni raccolte dal sistema operativo per le istanze del ruolo (macchine virtuali). Le metriche minime si limitano a percentuale CPU, dati in entrata, dati in uscita, velocità effettiva di lettura dal disco e velocità effettiva di scrittura sul disco. Con la configurazione del monitoraggio dettagliato, è possibile ricevere altre metriche in base ai dati delle prestazioni all'interno delle macchine virtuali (istanze del ruolo). Le metriche dettagliate consentono un'analisi più accurata dei problemi che si verificano durante l'elaborazione dell'applicazione.

Per impostazione predefinita, i dati del contatore di prestazioni dalle istanze del ruolo sono campionati e trasferiti dall'istanza del ruolo a intervalli di 3 minuti. Quando viene abilitato il monitoraggio dettagliato, i dati non elaborati del contatore di prestazioni sono aggregati per ogni istanza del ruolo e per ogni ruolo tra istanze del ruolo a intervalli di 5 minuti, 1 ora e 12 ore. I dati aggregati vengono eliminati dopo 10 giorni.

Dopo aver abilitato il monitoraggio dettagliato, i dati aggregati di monitoraggio sono archiviati in tabelle nell'account di archiviazione. Per abilitare il monitoraggio dettagliato per un ruolo, è necessario configurare una stringa di connessione diagnostica collegata all'account di archiviazione. È possibile usare account di archiviazione diversi per i diversi ruoli.

Si noti che l'abilitazione del monitoraggio dettagliato comporta un aumento dei costi legati all'archiviazione e al trasferimento dei dati e alle transazioni di archiviazione. Il monitoraggio minimo non richiede un account di archiviazione. I dati per le metriche esposti al livello minimo di monitoraggio non sono archiviati nell'account di archiviazione, anche se si imposta il livello di monitoraggio dettagliato.

## <a name="how-to-configure-monitoring-for-cloud-services"></a>Procedura: Configurare il monitoraggio per i servizi cloud
Attenersi alle procedure seguenti per configurare il monitoraggio dettagliato o minimo nel portale di Azure classico. 

### <a name="before-you-begin"></a>Prima di iniziare
* Creare un account di archiviazione per archiviare i dati di monitoraggio. È possibile usare account di archiviazione diversi per i diversi ruoli. Per altre informazioni, vedere gli argomenti **Account di archiviazione**o [Come creare un account di archiviazione](/manage/services/storage/how-to-create-a-storage-account/)nella Guida.
* Abilitare Diagnostica Azure per i ruoli del servizio cloud. Vedere [Configurazione della diagnostica per i servizi cloud](https://msdn.microsoft.com/library/azure/dn186185.aspx#BK_EnableBefore).

Assicurarsi che la stringa di connessione della diagnostica sia presente nella configurazione del ruolo. È possibile attivare il monitoraggio dettagliato fino ad abilitare la diagnostica di Azure e includere una stringa di connessione della diagnostica nella configurazione del ruolo.   

> [!NOTE]
> I progetti destinati a Azure SDK 2.5 non includono automaticamente la stringa di connessione della diagnostica nel modello di progetto. Per questi progetti è necessario aggiungere manualmente la stringa di connessione della diagnostica alla configurazione del ruolo.
> 
> 

**Per aggiungere manualmente la stringa di connessione della diagnostica alla configurazione del ruolo**

1. Aprire il progetto servizio cloud in Visual Studio.
2. Fare doppio clic su **Ruolo** per aprire la progettazione Ruolo e selezionare la scheda **Impostazioni**.
3. Cercare un’impostazione denominata **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**. 
4. Se questa impostazione non è presente, fare clic sul pulsante **Aggiungi impostazione** per aggiungere l'impostazione alla configurazione e modificare il tipo per la nuova impostazione su **ConnectionString**.
5. Impostare il valore per la stringa di connessione facendo clic sul pulsante **...** . Si apre una finestra di dialogo che consente di selezionare un account di archiviazione.
   
    ![Impostazioni di Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioDiagnosticsConnectionString.png)

### <a name="to-change-the-monitoring-level-to-verbose-or-minimal"></a>Per cambiare il livello di monitoraggio a dettagliato o minimo
1. Nel [portale di Azure classico](https://manage.windowsazure.com/)aprire la pagina di **Configurazione** per la distribuzione del servizio cloud.
2. In **Livello** fare clic su **Dettagliato** o **Minimo**. 
3. Fare clic su **Salva**.

Dopo avere abilitato il monitoraggio dettagliato, è consigliabile visualizzare i primi dati di monitoraggio nel portale di Azure classico entro un'ora.

I dati non elaborati del contatore di prestazioni e i dati aggregati di monitoraggio sono archiviati nell'account di archiviazione in tabelle contraddistinte dall'ID di distribuzione per i ruoli. 

## <a name="how-to-receive-alerts-for-cloud-service-metrics"></a>Procedura: Ricevere avvisi per le metriche dei servizi cloud
È possibile ricevere avvisi basati sulle metriche di monitoraggio del servizio cloud. Nella pagina **Servizi di gestione** del portale di Azure classico è possibile creare una regola per attivare un avviso quando la metrica scelta raggiunge il valore specificato. È inoltre possibile impostare l'invio di un messaggio di posta elettronica all'attivazione dell'avviso. Per altre informazioni, vedere [Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

## <a name="how-to-add-metrics-to-the-metrics-table"></a>Procedura: Aggiungere metriche alla relativa tabella
1. Nel [portale di Azure classico](http://manage.windowsazure.com/)aprire la pagina **Monitoraggio** per il servizio cloud.
   
    Per impostazione predefinita, la tabella delle metriche riporta un subset delle metriche disponibili. Nell'illustrazione sono visualizzate le metriche dettagliate predefinite per un servizio cloud, limitate al contatore di prestazioni Memoria/MB disponibili, con dati aggregati a livello di ruolo. Utilizzare **Aggiungi Metriche** per selezionare altre metriche aggregate e a livello di ruolo da monitorare nel portale di Azure classico.
   
    ![Visualizzazione dettagliata](./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png)
2. Per aggiungere metriche alla relativa tabella:
   
   1. Fare clic su **Aggiungi metriche** per aprire **Scegli metriche**, come illustrato di seguito.
      
       Viene espansa la prima metrica disponibile per visualizzare le opzioni disponibili. Per ogni metrica, la prima opzione consente di visualizzare dati aggregati di monitoraggio per tutti i ruoli. È inoltre possibile scegliere i singoli ruoli di cui visualizzare i dati.
      
       ![Aggiungi Metriche](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png)
   2. Per selezionare la metrica da visualizzare
      
      * Fare clic sulla freccia giù accanto alla metrica per espandere le opzioni di monitoraggio.
      * Selezionare le caselle di controllo relative alle opzioni di monitoraggio da visualizzare.
        
        Nella tabella delle metriche è possibile visualizzare fino a 50 metriche.
        
        > [!TIP]
        > Nel monitoraggio dettagliato l'elenco delle metriche può contenere decine di metriche. Per visualizzare una barra di scorrimento, passare il puntatore del mouse sul lato destro della finestra di dialogo. Per filtrare l'elenco, fare clic sull'icona di ricerca e immettere il testo nella casella di ricerca, come illustrato di seguito.
        > 
        > 
        
        ![Ricerca Aggiungi metriche](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png)
3. Dopo aver completato la selezione delle metriche, fare clic su OK (segno di spunta).
   
    Le metriche selezionate vengono aggiunte alla tabella, come illustrato di seguito.
   
    ![monitor metriche](./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png)
4. Per eliminare una metrica dalla tabella, fare clic sulla metrica per selezionarla e quindi fare clic su **Delete Metric**. (Selezionando una metrica viene visualizzata solo l'opzione **Delete Metric** .)

### <a name="to-add-custom-metrics-to-the-metrics-table"></a>Per aggiungere metriche personalizzate alla relativa tabella
Il livello di monitoraggio **Dettagliato** fornisce un elenco delle metriche predefinite che è possibile monitorare nel portale. Oltre a queste metriche, è possibile monitorare le metriche personalizzate o i contatori delle prestazioni definiti dall'applicazione tramite il portale.

Nei passaggi seguenti si presuppone che sia stato attivato il livello di monitoraggio **Dettagliato** e che si stia configurando l’applicazione per raccogliere e trasferire i contatori delle prestazioni personalizzati. 

Per visualizzare i contatori delle prestazioni personalizzati nel portale è necessario aggiornare la configurazione in wad-control-container:

1. Aprire il BLOB wad-control-container nell'account di archiviazione della diagnostica. A tale scopo, è possibile usare Visual Studio o un altro Esplora archivi.
   
    ![Esplora server di Visual Studio.](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioBlobExplorer.png)
2. Passare al percorso del BLOB usando il modello **DeploymentId/RoleName/RoleInstance** per trovare la configurazione per l'istanza del ruolo. 
   
    ![Esplora archivi di Visual Studio.](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioStorage.png)
3. Scaricare il file di configurazione per l'istanza del ruolo e aggiornarlo in modo da includere eventuali contatori delle prestazioni personalizzati. Ad esempio, per monitorare *Byte scritti su disco/sec* per l'*unità C*, aggiungere il codice seguente nel nodo **PerformanceCounters\Subscriptions**.
   
    ```xml
    <PerformanceCounterConfiguration>
    <CounterSpecifier>\LogicalDisk(C:)\Disk Write Bytes/sec</CounterSpecifier>
    <SampleRateInSeconds>180</SampleRateInSeconds>
    </PerformanceCounterConfiguration>
    ```
4. Salvare le modifiche e caricare il file di configurazione nuovamente nello stesso percorso sovrascrivendo il file esistente nel BLOB.
5. Passare alla modalità dettagliata nella configurazione del portale di Azure classico. Se si è già in modalità dettagliata è necessario attivare la modalità minima e tornare alla modalità dettagliata.
6. Il contatore delle prestazioni personalizzato sarà ora disponibile nella finestra di dialogo **Aggiungi metriche** . 

## <a name="how-to-customize-the-metrics-chart"></a>Procedura: Personalizzare il grafico delle metriche
1. Nella tabella delle metriche selezionare fino a 6 metriche da tracciare nel relativo grafico. Per selezionare una metrica, fare clic sulla casella di controllo a sinistra. Per rimuovere una metrica dal grafico, deselezionare la casella di controllo nella tabella delle metriche.
   
    Le metriche selezionate nella tabella vengono aggiunte al grafico. Su uno schermo stretto, un elenco a discesa **n more** riporta le intestazioni delle metriche che non si adattano allo schermo.
2. Per passare dalla visualizzazione di valori relativi (solo il valore finale per ogni metrica) e valori assoluti (asse Y visualizzato), selezionare Relative o Absolute nella parte superiore del grafico.
   
    ![Relative o Absolute](./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png)
3. Per modificare l'intervallo di tempo visualizzato sul grafico delle metriche, selezionare 1 ora, 24 ore o 7 giorni nella parte superiore del grafico.
   
    ![Visualizzazione del periodo monitorato](./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png)
   
    Nel grafico delle metriche del dashboard le metriche vengono tracciate in modo diverso. È disponibile un set di metriche standard che vengono aggiunte o rimosse selezionandone l'intestazione.

### <a name="to-customize-the-metrics-chart-on-the-dashboard"></a>Per personalizzare il grafico delle metriche sul dashboard
1. Aprire il dashboard per il servizio cloud.
2. Aggiungere o rimuovere le metriche dal grafico:
   
   * Per riportare una nuova metrica, selezionare la casella di controllo corrispondente nelle intestazioni del grafico. Su uno schermo stretto fare clic sulla freccia verso il basso accanto a ***n*??metrics** per tracciare sul grafico una metrica non visualizzata nell'area delle intestazioni del grafico.
   * Per eliminare una metrica tracciata sul grafico, deselezionare la casella di controllo accanto all'intestazione.
3. Passare dalla visualizzazione **relativa** a quella **assoluta**.
4. Scegliere se visualizzare dati relativi a 1 ora, 24 ore o 7 giorni.

## <a name="how-to-access-verbose-monitoring-data-outside-the-azure-classic-portal"></a>Procedura: accedere ai dati di monitoraggio dettagliati all'esterno del portale di Azure classico
I dati di monitoraggio dettagliati sono archiviati in tabelle negli account di archiviazione specificati per ogni ruolo. Per ogni distribuzione del servizio cloud, vengono create sei tabelle per il ruolo. Vengono create due tabelle per ogni intervallo di tempo (5 minuti, 1 ora e 12 ore). Una di queste tabelle archivia aggregazioni a livello di ruolo, mentre l'altra archivia le aggregazioni per le istanze del ruolo. 

I nomi delle tabelle presentano il formato seguente:

```
WAD*deploymentID*PT*aggregation_interval*[R|RI]Table
```

dove:

* *deploymentID* è il GUID assegnato alla distribuzione del servizio cloud
* *aggregation_interval* = 5M, 1H o 12H
* aggregazioni a livello di ruolo = R
* aggregazioni per le istanze del ruolo = RI

Ad esempio, nelle seguenti tabelle sarebbero archiviati dati di monitoraggio dettagliato aggregati a intervalli di 1 ora:

```
WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (hourly aggregations for the role)

WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (hourly aggregations for role instances)
```



<!--HONumber=Nov16_HO3-->


