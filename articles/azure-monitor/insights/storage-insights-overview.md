---
title: Monitorare i servizi di Archiviazione di Azure con Monitoraggio di Azure per l'archiviazione | Microsoft Docs
description: Questo articolo descrive la funzionalità Monitoraggio di Azure per l'archiviazione, che consente agli amministratori dell'archiviazione di comprendere rapidamente i problemi di prestazioni e utilizzo degli account di archiviazione di Azure.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/11/2020
ms.openlocfilehash: 7ab7071f504231290f72646e59a30fa855cff6cf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84944492"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage"></a>Monitoraggio del servizio di archiviazione con Monitoraggio di Azure per l'archiviazione

Monitoraggio di Azure per l'archiviazione offre un monitoraggio completo degli account di archiviazione di Azure grazie a una visualizzazione unificata delle prestazioni, della capacità e della disponibilità dei servizi di archiviazione di Azure. È possibile osservare la capacità di archiviazione e le prestazioni in due modi, direttamente da un account di archiviazione oppure da Monitoraggio di Azure, per vedere i diversi gruppi di account di archiviazione. 

Questo articolo aiuta a comprendere l'esperienza di Monitoraggio di Azure per l'archiviazione per ricavare informazioni di utilità pratica sull'integrità e sulle prestazioni degli account di archiviazione su larga scala, con la capacità di concentrare l'attenzione sulle aree sensibili e diagnosticare i problemi di latenza, limitazione della larghezza di banda della rete e disponibilità.

## <a name="introduction-to-azure-monitor-for-storage"></a>Introduzione a Monitoraggio di Azure per l'archiviazione

Prima di approfondire l'esperienza è necessario comprendere il modo in cui le informazioni vengono presentate e visualizzate. Che si selezioni la funzionalità di archiviazione direttamente da un account di archiviazione o da Monitoraggio di Azure, Monitoraggio di Azure per l'archiviazione offre un'esperienza coerente. 

In combinazione, offre:

* **Una prospettiva su larga scala**, che mostra una visualizzazione snapshot della disponibilità in base all'integrità del servizio di archiviazione o all'operazione API, dell'utilizzo, con il numero totale di richieste ricevute dal servizio di archiviazione, e della latenza, con il tempo medio impiegato dal servizio di archiviazione o dal tipo di operazione API per elaborare le richieste. È anche possibile visualizzare la capacità per BLOB, file, tabelle e code.

* **Analisi drill-down** di uno specifico account di archiviazione per facilitare la diagnosi dei problemi o eseguire l'analisi dettagliata per categoria: disponibilità, prestazioni, errori e capacità. Selezionando una di queste opzioni si ottiene una visualizzazione approfondita delle metriche.  

* **Possibilità di personalizzazione**, per modificare le metriche da visualizzare, modificare o impostare soglie allineate ai propri limiti e salvare come cartella di lavoro personalizzata. I grafici nelle cartelle di lavoro possono essere aggiunti ai dashboard di Azure.  

Per questa funzionalità non è necessario abilitare o configurare alcunché, le metriche di archiviazione degli account di archiviazione vengono raccolte per impostazione predefinita. Se non si ha familiarità con le metriche disponibili in Archiviazione di Azure, vedere la descrizione e la definizione delle metriche in [Metriche di archiviazione di Azure](../../storage/common/storage-metrics-in-azure-monitor.md).

>[!NOTE]
>Non sono previsti addebiti per accedere a questa funzionalità. Verranno addebitate solo le funzionalità essenziali di Monitoraggio di Azure configurate o abilitate, come descritto nella pagina relativa ai [dettagli sui prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

>[!NOTE]
>Monitoraggio di Azure per l'archiviazione non supporta gli [account per utilizzo generico v1](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts).
>

## <a name="view-from-azure-monitor"></a>Visualizzare da Monitoraggio di Azure

Da Monitoraggio di Azure è possibile esaminare i dettagli relativi a transazioni, latenza e capacità di più account di archiviazione nella sottoscrizione e identificare gli errori e i problemi di prestazioni e capacità.

Per visualizzare l'utilizzo e la disponibilità degli account di archiviazione in tutte le sottoscrizioni, seguire questa procedura.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **Monitoraggio** nel riquadro a sinistra del portale di Azure e nella sezione **Informazioni dettagliate** selezionare **Account di archiviazione**.

    ![Vista di più account di archiviazione](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>Cartella di lavoro Panoramica

Nella cartella di lavoro **Panoramica** per la sottoscrizione selezionata, la tabella mostra metriche di archiviazione interattive e lo stato di disponibilità del servizio per un massimo di 10 account di archiviazione raggruppati nella sottoscrizione. È possibile filtrare i risultati in base alle opzioni selezionate dagli elenchi a discesa seguenti:

* **Sottoscrizioni**: sono elencate solo le sottoscrizioni con account di archiviazione.  

* **Account di archiviazione**: per impostazione predefinita, sono preselezionati 10 account di archiviazione. Se si selezionano più account di archiviazione o tutti gli account nel selettore di ambito, verranno restituiti fino a 200 account di archiviazione. Ad esempio, se si ha un totale di 573 account di archiviazione in tre sottoscrizioni selezionate, verranno visualizzati solo 200 account. 

* **Intervallo di tempo**: per impostazione predefinita, visualizza le ultime 4 ore di informazioni in base alle selezioni corrispondenti effettuate.

Il riquadro contatore sotto gli elenchi a discesa riepiloga il numero totale di account di archiviazione nella sottoscrizione e indica quanti ne sono selezionati sul totale. Per le colonne nella cartella di lavoro che riportano metriche di transazioni errori sono previste una codifica con colori condizionale o le mappe termiche. Il colore più profondo presenta il valore più elevato, mentre un colore più chiaro deriva da valori più bassi. Per le colonne basate sugli errori il valore è in rosso, per le colonne basate su metriche il valore è in blu.

Selezionare un valore nelle colonne **Disponibilità**, **Latenza end-to-end**, **Latenza server** e **tipo di errore transazione/Errori** per accedere a un report basato sul tipo specifico di metrica di archiviazione corrispondente alla colonna selezionata per l'account di archiviazione. Per altre informazioni sulle cartelle di lavoro per ogni categoria, vedere la sezione [Cartelle di lavoro sull'archiviazione dettagliate](#detailed-storage-workbooks) più avanti. 

>[!NOTE]
>Per informazioni dettagliate sugli errori che possono essere visualizzati nel report, vedere lo [schema dei tipi di risposta](../../storage/common/monitor-storage-reference.md#metrics-dimensions) e cercare i tipi di risposta, ad esempio **ServerOtherError**, **ClientOtherError**, **ClientThrottlingError**. A seconda degli account di archiviazione selezionati, in presenza di più di tre tipi di errore, tutti gli altri errori sono rappresentati nella categoria **Altro**.

La soglia di **Disponibilità** predefinita è:

* Avviso - 99%
* Critico - 90%

Per impostare una soglia di disponibilità in base ai risultati dell'osservazione o ai propri requisiti, vedere [Modificare la soglia di disponibilità](#modify-the-availability-threshold). 

### <a name="capacity-workbook"></a>Cartella di lavoro Capacità

Selezionare **Capacità** nella parte superiore della pagina e verrà aperta la cartella di lavoro **Capacità**. Mostra la quantità di spazio di archiviazione totale usato nell'account e la capacità usata da ogni servizio dati nell'account, per identificare le risorse di archiviazione sovrautilizzate e sottoutilizzate.

![Cartella di lavoro della capacità per più account di archiviazione](./media/storage-insights-overview/storage-account-capacity-02.png) 

Nella cartella di lavoro vengono usate una codifica con colori condizionale o mappe termiche per indicare le metriche di capacità con un valore blu. Il colore più profondo presenta il valore più elevato, mentre un colore più chiaro deriva da valori più bassi.

Quando si seleziona un valore in una delle colonne della cartella di lavoro, si esegue il drill-down nella cartella di lavoro **Capacità** per l'account di archiviazione. Maggiori informazioni sul report drill-down sono disponibili nella sezione [Cartelle di lavoro sull'archiviazione dettagliate](#detailed-storage-workbooks) più avanti. 

## <a name="view-from-a-storage-account"></a>Visualizzare da un account di archiviazione

Per accedere a Monitoraggio di Azure per le macchine virtuali direttamente da un account di archiviazione:

1. Nel portale di Azure selezionare Account di archiviazione.

2. Scegliere un account di archiviazione nell'elenco. Nella sezione Monitoraggio scegliere Informazioni dettagliate.

    ![Pagina Panoramica dell'account di archiviazione selezionato](./media/storage-insights-overview/storage-account-direct-overview-01.png)

La cartella di lavoro **Panoramica** per l'account di archiviazione mostra diverse metriche relative alle prestazioni di archiviazione che consentono di valutare rapidamente:

* Integrità del servizio di archiviazione, per vedere immediatamente se un problema al di fuori del proprio controllo incide sul servizio di archiviazione nell'area in cui è distribuito, come indicato nella colonna **Riepilogo**.

* Grafici delle prestazioni interattivi con i dettagli più importanti relativi a capacità di archiviazione, disponibilità, transazioni e latenza.  

* Riquadri relativi a metriche e stato che evidenziano la disponibilità del servizio, il numero totale di transazioni per il servizio di archiviazione, la latenza end-to-end e la latenza del server.

Selezionando uno dei pulsanti per **Errori**, **Prestazioni**, **Disponibilità** e **Capacità** verrà aperta la cartella di lavoro corrispondente. 

![Pagina Panoramica dell'account di archiviazione selezionato](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>Cartelle di lavoro sull'archiviazione dettagliate

Selezionando un valore nelle colonne **Disponibilità**, **Latenza end-to-end**, **Latenza server** e **tipo di errore di transazione/Errori** dalla cartella di lavoro **Panoramica** di più account di archiviazione, oppure selezionando uno dei pulsanti per **Errori**, **Prestazioni**, **Disponibilità** e **Capacità** dalla cartella di lavoro **Panoramica** di uno specifico account di archiviazione, si otterrà un set di informazioni interattive correlate all'archiviazione, personalizzato in base a tale categoria.  

* **Disponibilità** apre la cartella di lavoro **Disponibilità**. Mostra lo stato di integrità corrente del servizio Archiviazione di Azure, una tabella con lo stato di integrità disponibile di ogni oggetto categorizzato in base al servizio dati definito nell'account di archiviazione, con una linea di tendenza che rappresenta l'intervallo di tempo selezionato e un grafico di tendenza della disponibilità per ogni servizio dati nell'account.  

    ![Esempio di report di disponibilità](./media/storage-insights-overview/storage-account-availability-01.png)

* **Latenza E2E** e **Latenza server** aprono la cartella di lavoro **Prestazioni**. Include un riquadro riepilogativo dello stato che mostra la latenza end-to-end e la latenza del server, un grafico delle prestazioni con i due tipi di latenza a confronto e una tabella con la suddivisione della latenza delle chiamate riuscita per API, categorizzate in base al servizio dati definito nell'account di archiviazione.

    ![Esempio di report sulle prestazioni](./media/storage-insights-overview/storage-account-performance-01.png)

* Selezionando una delle categorie di errore elencate nella griglia viene aperta la cartella di lavoro **Errori**. Il report mostra i riquadri delle metriche di tutti gli altri errori lato client tranne quelli descritti e le richieste riuscite, gli errori di limitazione del client, un grafico delle prestazioni per la metrica della dimensione **Tipo di risposta** della transazione specifica dell'attributo ClientOtherError e due tabelle: **Transactions by API name** (Transazioni per nome API) e **Transazioni in base al tipo di risposta**.

   ![Esempio di report degli errori](./media/storage-insights-overview/storage-account-failures-01.png)

* **Capacità** apre la cartella di lavoro **Capacità**. Mostra la quantità totale di spazio di archiviazione utilizzato per ogni oggetto dati di archiviazione nell'account nei riquadri e nel grafico e il numero di oggetti dati archiviati nell'account.  

    ![Pagina Capacità dell'account di archiviazione selezionato](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>Aggiunta ed esportazione

È possibile aggiungere una qualsiasi delle sezioni della metrica a un dashboard di Azure selezionando l'icona a forma di puntina da disegno in alto a destra nella sezione.

![Esempio di aggiunta della sezione metrica al dashboard](./media/storage-insights-overview/workbook-pin-example.png)

Le cartelle di lavoro **Panoramica** o **Capacità** relative a più sottoscrizioni e account di archiviazione supportano l'esportazione dei risultati in formato Excel, selezionando l'icona a forma di freccia in giù a destra della puntina da disegno.

![Esempio di risultati dell'esportazione della griglia della cartella di lavoro](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage"></a>Personalizzare Monitoraggio di Azure per l'archiviazione

Questa sezione illustra gli scenari comuni in cui è utile modificare la cartella di lavoro per personalizzarla a supporto delle proprie esigenze di analisi dei dati:

* Definire l'ambito della cartella di lavoro in modo da selezionare sempre una particolare sottoscrizione o uno o più account di archiviazione specifici
* Modificare le metriche nella griglia
* Modificare la soglia di disponibilità
* Modificare il rendering dei colori

Le personalizzazioni vengono salvate in una cartella di lavoro personalizzata per evitare la sovrascrittura della configurazione predefinita nella cartella di lavoro pubblicata. Le cartelle di lavoro vengono salvate all'interno di un gruppo di risorse, ovvero nella sezione **Report personali** privata o nella sezione **Report condivisi** accessibile a tutti gli utenti che hanno accesso al gruppo di risorse. Dopo aver salvato la cartella di lavoro personalizzata, per avviarla è necessario passare alla raccolta di cartelle di lavoro.

![Avviare la raccolta di cartelle di lavoro dalla barra dei comandi](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>Specifica di una sottoscrizione o di un account di archiviazione

È possibile configurare le cartelle di lavoro **Panoramica** o **Errori** relative a più sottoscrizioni e account di archiviazione in modo da includere nell'ambito una o più specifiche sottoscrizioni o uno o più specifici account di archiviazione per ogni esecuzione, seguendo questa procedura.

1. Selezionare **Monitoraggio** dal portale e quindi selezionare **Account di archiviazione** dal riquadro a sinistra.

2. Nella cartella di lavoro **Panoramica** selezionare **Modifica** nella barra dei comandi.

3. Nell'elenco a discesa **Sottoscrizioni** selezionare una o più sottoscrizioni da usare come predefinite. Tenere presente che la cartella di lavoro supporta la selezione di un totale di 10 sottoscrizioni.  

4. Nell'elenco a discesa **Account di archiviazione** selezionare uno o più account da usare come predefiniti. Tenere presente che la cartella di lavoro supporta la selezione di un totale di 200 account di archiviazione. 

5. Selezionare **Salva con nome** sulla barra dei comandi per salvare una copia della cartella di lavoro con le personalizzazioni, quindi fare clic su **Modifica completata** per tornare alla modalità di lettura.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>Modificare le metriche e i colori nella cartella di lavoro

Le cartelle di lavoro predefinite contengono dati di metrica ed è possibile modificare o rimuovere qualsiasi visualizzazione e personalizzarle in base alle esigenze specifiche del team.

In questo esempio si usa la cartella di lavoro Capacità relativa a più sottoscrizioni e account di archiviazione per dimostrare come:

* Rimuovere una metrica
* Modificare il rendering dei colori

È possibile eseguire le stesse modifiche su una qualsiasi delle cartelle di lavoro predefinite **Errori**, **Prestazioni**, **Disponibilità** e **Capacità**.

1. Selezionare **Monitoraggio** dal portale e quindi selezionare **Account di archiviazione** dal riquadro a sinistra.

2. Selezionare **Capacità** per passare alla cartella di lavoro della capacità e nella barra dei comandi selezionare **Modifica**.

    ![Selezionare Modifica per modificare una cartella di lavoro](./media/storage-insights-overview/workbook-edit-workbook.png)

3. Accanto alla sezione delle metriche selezionare **Modifica**.

    ![Selezionare Modifica per modificare le metriche della cartella di lavoro della capacità](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. Verrà rimossa la colonna **Sequenza temporale capacità usata account**, quindi selezionare **Impostazioni di colonna** nella griglia delle metriche.

    ![Modifica impostazioni di colonna](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. Nel riquadro **Modifica impostazioni di colonna**, sotto la sezione **Colonne** selezionare **microsoft.storage/storageaccounts-Capacity-UsedCapacity Timeline$|Account used capacity Timeline$** e nell'elenco a discesa **Renderer colonna** selezionare **Nascosto**.

6. Selezionare **Salva e chiudi** per eseguire il commit della modifica.

A questo punto è possibile modificare il tema colori per le metriche di capacità nel report in modo da usare il verde anziché il blu.

1. Selezionare **Impostazioni di colonna** nella griglia delle metriche.

2. Nel riquadro **Modifica impostazioni di colonna**, sotto la sezione **Colonne** selezionare **microsoft.storage/storageaccounts-Capacity-UsedCapacity$|microsoft.storage/storageaccounts/blobservices-Capacity-BlobCapacity$|microsoft.storage/storageaccounts/fileservices-Capacity-FileCapacity$|microsoft.storage/storageaccounts/queueservices-Capacity-QueueCapacity$|microsoft.storage/storageaccounts/tableservices-Capacity-TableCapacity$** . Nell'elenco a discesa **Tavolozza dei colori** selezionare **Verde**.

3. Selezionare **Salva e chiudi** per eseguire il commit della modifica.

4. Selezionare **Salva con nome** sulla barra dei comandi per salvare una copia della cartella di lavoro con le personalizzazioni, quindi fare clic su **Modifica completata** per tornare alla modalità di lettura.  

### <a name="modify-the-availability-threshold"></a>Modificare la soglia di disponibilità

In questo esempio si usa la cartella di lavoro sulla capacità dell'account di archiviazione e si dimostra come modificare la soglia di disponibilità. Per impostazione predefinita, il riquadro e la griglia che mostrano la percentuale di disponibilità sono configurati con una soglia minima di 90 e una soglia massima di 99. Si modificherà il valore di soglia minima della **percentuale di disponibilità** nella griglia **Disponibilità per nome API** all'85%, il che significa che lo stato di integrità passerà a critico se la soglia è inferiore all'85%. 

1. Selezionare **Account di archiviazione** dal portale e quindi selezionare un account di archiviazione dall'elenco.

2. Selezionare **Informazioni dettagliate** dal riquadro a sinistra.

3. Nella cartella di lavoro selezionare **Disponibilità** per passare alla cartella di lavoro corrispondente, quindi selezionare **Modifica** dalla barra dei comandi. 

4. Scorrere verso il basso fino alla parte inferiore della pagina e sul lato sinistro accanto alla griglia della **disponibilità per API** selezionare **Modifica**.

    ![Modificare le impostazioni della griglia di disponibilità in base al nome API](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. Selezionare **Impostazioni di colonna**, quindi nel riquadro **Modifica impostazioni di colonna** selezionare **Availability (%) (Thresholds + Formatted)** (Disponibilità % - Soglie + formattati) nella sezione **Colonne**.

6. Modificare il valore per lo stato di integrità **Critico** da **90** a **85**, quindi fare clic su **Salva e chiudi**.

    ![Modificare il valore della soglia di disponibilità per lo stato critico](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. Selezionare **Salva con nome** sulla barra dei comandi per salvare una copia della cartella di lavoro con le personalizzazioni, quindi fare clic su **Modifica completata** per tornare alla modalità di lettura.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per indicazioni generali sulla risoluzione dei problemi, vedere l'articolo dedicato alla [risoluzione dei](troubleshoot-workbooks.md)problemi di Insights basato sulla cartella di lavoro.

Questa sezione fornisce un supporto per la diagnosi e la risoluzione di alcuni dei problemi più comuni che possono verificarsi durante l'uso di Monitoraggio di Azure per l'archiviazione. Usare l'elenco che segue per individuare le informazioni pertinenti a un problema specifico.

### <a name="resolving-performance-capacity-or-availability-issues"></a>Risoluzione dei problemi di prestazioni, capacità o disponibilità

Per risolvere i problemi relativi all'archiviazione identificati con Monitoraggio di Azure per l'archiviazione, vedere la [guida alla risoluzione dei problemi](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance) di Archiviazione di Azure.  

### <a name="why-can-i-only-see-200-storage-accounts"></a>Perché è possibile vedere solo 200 account di archiviazione?

Per il numero di account di archiviazione selezionati esiste un limite di 200, indipendentemente dal numero di sottoscrizioni selezionate.

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>Come si fa a modificare la colorazione e la soglia per la disponibilità?

Per la procedura dettagliata su come modificare la colorazione e le soglie per la disponibilità, vedere la sezione [Modificare la soglia di disponibilità](storage-insights-overview.md#modify-the-availability-threshold).

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>Come si fa ad analizzare e risolvere i problemi relativi ai dati visualizzati in Monitoraggio di Azure per l'archiviazione?

 Per informazioni dettagliate su come analizzare e risolvere i problemi relativi ai dati di Archiviazione di Azure visualizzati in Monitoraggio di Azure per l'archiviazione, vedere l'articolo relativo a [Monitorare, diagnosticare e risolvere i problemi dell'Archiviazione di Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-monitoring-diagnosing-troubleshooting).

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>Perché nelle metriche non vengono visualizzati tutti i tipi di errori?

Attualmente vengono visualizzati fino a tre tipi diversi di errori, il resto viene raggruppato in un unico bucket. Il comportamento viene controllato tramite splitByLimit e può essere modificato. Per modificare questa proprietà:

1. Fare clic su Modifica cartella di lavoro.
2. Passare alle metriche, fare clic su Modifica e quindi selezionare **Transactions, Sum** (Transazioni, Somma) o qualunque metrica che si voglia modificare.

    ![Passare alle metriche e fare clic su Modifica e quindi su "Transactions, Sum" (Transazioni, Somma)](./media/storage-insights-overview/fqa7.png)

3. Modificare quindi il numero di divisioni.

    ![Selezionare i parametri della metrica"](./media/storage-insights-overview/fqa7-2.png)

Se si vogliono visualizzare n tipi di errore diversi, specificare splitByLimit come n+1. 1 indica il resto degli errori.

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>Si è salvata la cartella di lavoro in un account di archiviazione. Perché non si riesce più a trovarla?

Ogni cartella di lavoro viene salvata nell'account di archiviazione in cui è stata salvata. Provare a trovare lo specifico account di archiviazione in cui l'utente ha salvato la cartella di lavoro. Altrimenti, non è possibile trovare una specifica cartella di lavoro senza conoscere la risorsa (account di archiviazione).

## <a name="next-steps"></a>Passaggi successivi

* Configurare gli [avvisi sulle metriche](../platform/alerts-metric.md) e le [notifiche sull'integrità del servizio](../../service-health/alerts-activity-log-service-notifications.md) per impostare avvisi automatici che facilitano il rilevamento dei problemi.

* Informazioni sugli scenari che le cartelle di lavoro sono progettate per supportare, su come creare nuovi report e personalizzare report esistenti e altro ancora sono disponibili nell'articolo [Creare report interattivi con le cartelle di lavoro di Monitoraggio di Azure](../platform/workbooks-overview.md).

* Per una guida dettagliata sull'utilizzo di Analisi archiviazione e di altri strumenti per identificare, diagnosticare e risolvere i problemi relativi ad Archiviazione di Azure, vedere [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md).
