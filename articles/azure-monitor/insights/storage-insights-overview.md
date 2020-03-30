---
title: Monitorare i servizi di Archiviazione di Azure con Monitoraggio di Azure per archiviazione (anteprima)Monitor Azure Storage services with Azure Monitor for Storage (preview) Documenti Microsoft
description: Questo articolo descrive la funzionalità Monitoraggio di Azure per l'archiviazione che offre agli amministratori di archiviazione una rapida comprensione dei problemi di prestazioni e utilizzo con gli account di archiviazione di Azure.This article describes the Azure Monitor for Storage feature that provides storage admins with a quick understanding of performance and utilization issues with their Azure Storage accounts.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/15/2019
ms.openlocfilehash: f23be7e764ad180a23c76abb7f9bb2218fd61e4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662520"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>Monitoraggio del servizio di archiviazione con Monitoraggio di Azure per l'archiviazione (anteprima)Monitoring your storage service with Azure Monitor for Storage (preview)

Monitoraggio di Azure per l'archiviazione (anteprima) offre un monitoraggio completo degli account di Archiviazione di Azure offrendo una visualizzazione unificata delle prestazioni, della capacità e della disponibilità dei servizi di archiviazione di Azure.Azure Monitor for Storage (preview) provides comprehensive monitoring of your Azure Storage accounts by delivering a unified view of your Azure Storage services performance, capacity, and availability. È possibile osservare la capacità di archiviazione e le prestazioni in due modi, visualizzare direttamente da un account di archiviazione o visualizzare da Monitoraggio di Azure per visualizzare tra gruppi di account di archiviazione. 

Questo articolo illustra l'esperienza fornita da Monitoraggio di Archiviazione per archiviazione (anteprima) per ricavare informazioni utili sull'integrità e le prestazioni degli account di archiviazione su larga scala, con la capacità di concentrarsi sulle aree sensibili e diagnosticare la limitazione della latenza, problemi di disponibilità.

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>Introduzione a Monitoraggio di Azure per Archiviazione (anteprima)Introduction to Azure Monitor for Storage (preview)

Prima di immergersi nell'esperienza, dovresti capire come presenta e visualizza le informazioni. Se si seleziona la funzionalità di archiviazione direttamente da un account di archiviazione o da Monitoraggio di Azure, Monitoraggio di Azure per l'archiviazione offre un'esperienza coerente. 

Combinato offre:

* **In prospettiva della scalabilità** che mostra una visualizzazione snapshot della disponibilità in base all'integrità del servizio di archiviazione o dell'operazione API, l'utilizzo che mostra il numero totale di richieste ricevute dal servizio di archiviazione e la latenza che indica il tempo medio che il servizio di archiviazione o il tipo di operazione API sta impiegando per elaborare le richieste. È anche possibile visualizzare la capacità in base a BLOB, file, tabella e coda.

* **Eseguire il drill-down dell'analisi** di un account di archiviazione specifico per diagnosticare i problemi o eseguire un'analisi dettagliata per categoria: disponibilità, prestazioni, errori e capacità. La selezione di una di queste opzioni offre una visualizzazione approfondita delle metriche.  

* **Personalizzabile** in cui è possibile modificare le metriche che si desidera visualizzare, modificare o impostare soglie in linea con i limiti e salvarle come cartella di lavoro personalizzata. I grafici nella cartella di lavoro possono essere aggiunti al dashboard di Azure.Charts in the workbook can be pinned to Azure dashboard.  

Questa funzionalità non richiede l'abilitazione o la configurazione di elementi, per impostazione predefinita vengono raccolte le metriche di archiviazione degli account di archiviazione. Se non si ha familiarità con le metriche disponibili in Archiviazione di Azure, visualizzare la descrizione e la definizione nelle metriche di Archiviazione di Azure esaminando le [metriche](../../storage/common/storage-metrics-in-azure-monitor.md)di archiviazione di Azure.If you are unfamiliar with metrics available on Azure Storage, view the description and definition in Azure Storage metrics by reviewing Azure storage metrics .

>[!NOTE]
>Non è previsto alcun costo per accedere a questa funzionalità e verranno addebitati solo le funzionalità essenziali di Monitoraggio di Azure configurate o abilitate, come descritto nella pagina dei dettagli sui prezzi di Monitoraggio di Azure.There is no charge to access this feature and you will be only charged for the Azure Monitor essential features you configure or enable, as described on the [Azure Monitor pricing details](https://azure.microsoft.com/pricing/details/monitor/) page.

>[!NOTE]
>Monitoraggio di Azure per l'archiviazione non supporta [gli account v1](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts)generici.
>

## <a name="view-from-azure-monitor"></a>Visualizza da Monitoraggio di AzureView from Azure Monitor

Da Monitoraggio di Azure è possibile visualizzare i dettagli relativi a transazioni, latenza e capacità da più account di archiviazione nella sottoscrizione e identificare prestazioni, problemi di capacità ed errori.

Per visualizzare l'utilizzo e la disponibilità degli account di archiviazione in tutte le sottoscrizioni, eseguire la procedura seguente.

1. Accedere al [portale](https://portal.azure.com)di Azure .

2. Selezionare **Monitoraggio** nel riquadro sinistro del portale di Azure e nella sezione **Informazioni dettagliate** selezionare **Account di archiviazione (anteprima).**

    ![Visualizzazione di più account di archiviazioneMultiple storage accounts view](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>Cartella di lavoro panoramica

Nella cartella di lavoro **Panoramica** per la sottoscrizione selezionata, nella tabella vengono visualizzate le metriche di archiviazione interattive e lo stato di disponibilità del servizio per un massimo di 10 account di archiviazione raggruppati all'interno della sottoscrizione. È possibile filtrare i risultati in base alle opzioni selezionate dai seguenti elenchi a discesa:

* **Sottoscrizioni:** vengono elencate solo le sottoscrizioni con account di archiviazione.  

* **Account di archiviazione:** per impostazione predefinita, 10 account di archiviazione sono preselezionati. Se si selezionano tutti o più account di archiviazione nel selettore dell'ambito, verranno restituiti fino a 200 account di archiviazione. Ad esempio, se si dispone di un totale di 573 account di archiviazione in tre sottoscrizioni selezionate, verranno visualizzati solo 200 account. 

* **Intervallo** di tempo - per impostazione predefinita, visualizza le ultime 4 ore di informazioni in base alle selezioni corrispondenti effettuate.

Il riquadro del contatore negli elenchi a discesa esegue il rollup del numero totale di account di archiviazione nella sottoscrizione e riflette il numero di elementi totali selezionati. Esistono mappe colore o heatmap condizionali per le colonne della cartella di lavoro che segnalano metriche o errori delle transazioni. Il colore più profondo ha il valore più alto e un colore più chiaro si basa sui valori più bassi. Per le colonne basate sugli errori, il valore è in rosso e per le colonne basate su metriche, il valore è in blu.

Selezionare un valore nelle colonne **Disponibilità**, **Latenza E2E**, **Latenza server**e **Tipo/Errori** di errore transazione consente di indirizzare l'utente a un report personalizzato per il tipo specifico di metriche di archiviazione che corrispondono alla colonna selezionata per l'account di archiviazione. Per ulteriori informazioni sulle cartelle di lavoro per ogni categoria, vedere la sezione Cartelle di lavoro di [archiviazione dettagliate](#detailed-storage-workbooks) di seguito. 

>[!NOTE]
>Per informazioni dettagliate sugli errori che possono essere visualizzati nel report, vedere [Schema del tipo](../../storage/common/storage-metrics-in-azure-monitor.md#metrics-dimensions) di risposta e cercare i tipi di risposta quali **ServerOtherError**, **ClientOtherError**, **ClientThrottlingError**. A seconda degli account di archiviazione selezionati, se sono segnalati più di tre tipi di errori, tutti gli altri errori vengono rappresentati nella categoria **Altro**.

La soglia di disponibilità predefinita è:The default **Availability** threshold is:

* Avvertenza - 99%
* Critico - 90%

Per impostare una soglia di disponibilità in base ai risultati dell'osservazione o dei requisiti, vedere [modificare la soglia](#modify-the-availability-threshold)di disponibilità . 

### <a name="capacity-workbook"></a>Cartella di lavoro Capacità

Selezionare **Capacità** nella parte superiore della pagina per aprire la cartella di lavoro **Capacità.** Mostra la quantità di spazio di archiviazione totale utilizzato nell'account e nella capacità utilizzati da ogni servizio dati nell'account per identificare lo spazio di archiviazione utilizzato e sopra e sotto.

![Più account di archiviazione Cartella di lavoro CapacitàMultiple storage accounts Capacity workbook](./media/storage-insights-overview/storage-account-capacity-02.png) 

Esistono mappe colore o heatmap condizionali per le colonne nella cartella di lavoro che segnalano metriche di capacità con un valore blu. Il colore più profondo ha il valore più alto e un colore più chiaro si basa sui valori più bassi.

Quando si seleziona un valore in una delle colonne della cartella di lavoro, si esegue il drill-down alla cartella di lavoro **Capacity** per l'account di archiviazione. Ulteriori dettagli sul report drill-down sono descritti nella sezione [Cartelle di lavoro](#detailed-storage-workbooks) di archiviazione dettagliata di seguito. 

## <a name="view-from-a-storage-account"></a>Visualizzare da un account di archiviazioneView from a storage account

Per accedere a Monitoraggio di Azure per le macchine virtuali direttamente da un account di archiviazione:To access Azure Monitor for VMs directly from a storage account:

1. Nel portale di Azure selezionare Account di archiviazione.

2. Nell'elenco scegliere un account di archiviazione. Nella sezione Monitoraggio scegliere Insights (anteprima).

    ![Pagina Panoramica dell'account di archiviazione selezionato](./media/storage-insights-overview/storage-account-direct-overview-01.png)

Nella cartella di lavoro **Panoramica** per l'account di archiviazione vengono illustrate diverse metriche sulle prestazioni di archiviazione che consentono di valutare rapidamente:On the Overview workbook for the storage account, it shows several storage performance metrics that help you quickly assess:

* Integrità del servizio di archiviazione per verificare immediatamente se un problema al di fuori del controllo interessa il servizio di archiviazione nell'area in cui è distribuito, come indicato nella colonna **Riepilogo.**

* Grafici interattivi sulle prestazioni che mostrano i dettagli più essenziali relativi a capacità di archiviazione, disponibilità, transazioni e latenza.  

* Riquadri di metrica e stato che evidenziano la disponibilità del servizio, il numero totale di transazioni per il servizio di archiviazione, la latenza E2E e la latenza del server.

Se si seleziona uno dei **pulsanti Errori**, **Prestazioni**, **Disponibilità**e **Capacità** verrà aperta la rispettiva cartella di lavoro. 

![Pagina Panoramica dell'account di archiviazione selezionato](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>Cartelle di lavoro di archiviazione dettagliateDetailed storage workbooks

Se è stato selezionato un valore nelle colonne **Disponibilità**, **Latenza E2E**, **Latenza server**e **Tipo/Errori** di transazione dalla cartella di lavoro **Panoramica** di più account di archiviazione oppure se è stato selezionato uno dei pulsanti per **Errori**, **Prestazioni**, **Disponibilità**e **Capacità** dalla cartella di lavoro **Panoramica** da un account di archiviazione specifico, ognuno dei quali fornisce un set di informazioni interattive relative all'archiviazione su misura per tale categoria.  

* **Disponibilità** apre la cartella di lavoro **Disponibilità.Availability** opens the Availability workbook. Mostra lo stato di integrità corrente del servizio di archiviazione di Azure, una tabella che mostra lo stato di integrità disponibile di ogni oggetto categorizzato dal servizio dati definito nell'account di archiviazione con una linea di tendenza che rappresenta l'intervallo di tempo selezionato e un grafico delle tendenze di disponibilità per ogni servizio dati nell'account.  

    ![Esempio di report sulla disponibilitàAvailability report example](./media/storage-insights-overview/storage-account-availability-01.png)

* **E2E Latenza** e **latenza server** apre la cartella di lavoro **Prestazioni.** Include un riquadro dello stato di rollup che mostra la latenza E2E e la latenza del server, un grafico delle prestazioni di E2E rispetto alla latenza del server e una tabella che interrompe la latenza delle chiamate riuscite dall'API categorizzata dal servizio dati definito nell'account di archiviazione.

    ![Esempio di rapporto sul rendimento](./media/storage-insights-overview/storage-account-performance-01.png)

* Selezionando una delle categorie di errore elencate nella griglia aprire la cartella di lavoro **Errore.** Il report mostra i riquadri delle metriche di tutti gli altri errori sul lato client, ad eccezione di quelli descritti e le richieste riuscite, gli errori di limitazione dei client, un grafico delle prestazioni per la metrica della dimensione Tipo di **risposta** della transazione specifica per l'attributo ClientOtherError e due tabelle: **Transazioni per nome API** e **Transazioni per tipo di risposta**.

   ![Esempio di rapporto di errore](./media/storage-insights-overview/storage-account-failures-01.png)

* **Capacità** apre la cartella di lavoro **Capacità.** Mostra la quantità totale di spazio di archiviazione usato per ogni oggetto dati di archiviazione nell'account nei riquadri e nel grafico e il numero di oggetti dati archiviati nell'account.  

    ![Pagina Capacità dell'account di archiviazione selezionato](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>Pin ed esportazione

È possibile aggiungere una qualsiasi delle sezioni metriche a un dashboard di Azure selezionando l'icona a forma di puntina da aggiungere nella parte superiore destra della sezione.

![Esempio di pin della sezione metrica all'esempio di dashboard](./media/storage-insights-overview/workbook-pin-example.png)

Le cartelle di lavoro **Panoramica** o **Capacità** dell'account multi-sottoscrizione e archiviazione supportano l'esportazione dei risultati in formato Excel selezionando l'icona della freccia rivolta verso il basso a destra dell'icona a forma di puntina da aggiungere.

![Esempio di risultati della griglia della cartella di lavoro Per l'esportazione](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>Personalizzare Monitoraggio di Azure per l'archiviazione (anteprima)Customize Azure Monitor for Storage (preview)

In questa sezione vengono evidenziati gli scenari comuni per la modifica della cartella di lavoro per la personalizzazione a supporto delle esigenze di analisi dei dati:This section highlights common scenarios for editing the workbook to customize in support of your data analytics needs:

* Definire sempre l'ambito della cartella di lavoro per selezionare una sottoscrizione o una o più account di archiviazione specificiScope the workbook to always select a particular subscription or storage account(s)
* Modificare le metriche nella griglia
* Modificare la soglia di disponibilità
* Modificare il rendering dei colori

Le personalizzazioni vengono salvate in una cartella di lavoro personalizzata per evitare di sovrascrivere la configurazione predefinita nella cartella di lavoro pubblicata. Le cartelle di lavoro vengono salvate all'interno di un gruppo di risorse, nella sezione **Report** personali privata o nella sezione **Report condivisi** accessibile a tutti gli utenti con accesso al gruppo di risorse. Dopo aver salvato la cartella di lavoro personalizzata, è necessario passare alla raccolta di cartelle di lavoro per avviarla.

![Avviare la raccolta di cartelle di lavoro dalla barra dei comandi](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>Specifica di una sottoscrizione o di un account di archiviazioneSpecifying a subscription or storage account

È possibile configurare le **cartelle** di lavoro **Panoramica** per più sottoscrizioni e account di archiviazione per l'ambito a una o più sottoscrizioni o account di archiviazione specifici a ogni esecuzione, eseguire i passaggi seguenti.

1. Selezionare **Monitoraggio** dal portale e quindi Account di archiviazione **(anteprima)** nel riquadro sinistro.

2. Nella cartella di lavoro **Panoramica** selezionare **Modifica**dalla barra dei comandi.

3. Selezionare dall'elenco a discesa **Sottoscrizioni** una o più sottoscrizioni per le impostazioni predefinite. Tenere presente che la cartella di lavoro supporta la selezione di un totale di 10 sottoscrizioni.  

4. Selezionare dall'elenco a discesa **Account di archiviazione** uno o più account per i nomi predefiniti. Tenere presente che la cartella di lavoro supporta la selezione di un totale di 200 account di archiviazione. 

5. Selezionare **Salva con** nome dalla barra dei comandi per salvare una copia della cartella di lavoro con le personalizzazioni e quindi fare clic su **Fine modifica** per tornare alla modalità di lettura.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>Modificare metriche e colori nella cartella di lavoro

Le cartelle di lavoro predefinite contengono dati sulle metriche e si ha la possibilità di modificare o rimuovere una qualsiasi delle visualizzazioni e personalizzare in base alle esigenze specifiche del team.

In questo esempio viene utilizzata la cartella di lavoro per la capacità dell'account di archiviazione e di più sottoscrizioni, per illustrare come:In our example, we are working with the multi-subscription and storage account capacity workbook, to demonstrate how to:

* Rimuovere una metrica
* Modificare il rendering dei colori

È possibile eseguire le stesse modifiche in base a una qualsiasi delle cartelle di lavoro **Errori**, **Prestazioni**, **Disponibilità**e **Capacità** predefinite.

1. Selezionare **Monitoraggio** dal portale e quindi Account di archiviazione **(anteprima)** nel riquadro sinistro.

2. Selezionare **Capacità** per passare alla cartella di lavoro della capacità e nella barra dei comandi selezionare **Modifica** dalla barra dei comandi.

    ![Selezionare Modifica per modificare una cartella di lavoro](./media/storage-insights-overview/workbook-edit-workbook.png)

3. Accanto alla sezione delle metriche selezionare **Modifica**.

    ![Selezionare Modifica per modificare le metriche della cartella di lavoro capacità](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. Rimuoveremo la colonna **Sequenza temporale della capacità utilizzata dall'account,** quindi seleziona **Impostazioni colonna** nella griglia delle metriche.

    ![Modificare le impostazioni delle colonne](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. Nel riquadro **Modifica impostazioni colonna** selezionare la sezione **Colonne** **microsoft.storage/storageaccounts-Capacity-UsedCapacity Timeline. Account utilizzato capacità Timeline**, e sotto l'elenco a discesa **Colonna renderer** selezionare **Nascosto**.

6. Selezionare **Salva e chiudi** per eseguire il commit della modifica.

Ora cambiamo il tema del colore per le metriche di capacità nel rapporto in modo da usare il verde anziché il blu.

1. Selezionare **Impostazioni colonna** nella griglia delle metriche.

2. Nel riquadro **Modifica impostazioni colonna** selezionare la sezione **Colonne** **microsoft.storage/storageaccounts-Capacity-UsedCapacity,microsoft.storage/storageaccounts/blobservices-Capacity-BlobCapacity,microsoft.storage/storageaccounts/fileservices-Capacity-FileCapacity, microsoft.storage/storageaccounts/queueservices-Capacity-QueueCapacity,microsoft.storage/storageaccounts/tableservices-Capacity.** Nell'elenco a discesa **Tavolozza colori**selezionare **Verde**.

3. Selezionare **Salva e chiudi** per eseguire il commit della modifica.

4. Selezionare **Salva con** nome dalla barra dei comandi per salvare una copia della cartella di lavoro con le personalizzazioni e quindi fare clic su **Fine modifica** per tornare alla modalità di lettura.  

### <a name="modify-the-availability-threshold"></a>Modificare la soglia di disponibilità

In questo esempio viene utilizzata la cartella di lavoro capacità dell'account di archiviazione e viene illustrato come modificare la soglia di disponibilità. Per impostazione predefinita, la disponibilità percentuale di report di riquadri e griglia è configurata con una soglia minima di 90 e una soglia massima di 99. Si modificherà il valore di soglia minima della **percentuale** di disponibilità nella griglia **Disponibilità per nome API** a 85%, il che significa che lo stato di integrità diventa critico se la soglia è inferiore all'85%. 

1. Selezionare **Account di archiviazione** dal portale e quindi selezionare un account di archiviazione dall'elenco.

2. Selezionare **Insights (anteprima)** nel riquadro sinistro.

3. Nella cartella di lavoro selezionare **Disponibilità** per passare alla cartella di lavoro per la disponibilità e quindi selezionare **Modifica** dalla barra dei comandi. 

4. Scorrere verso il basso fino alla fine della pagina e sul lato sinistro accanto alla griglia **Disponibilità per API** selezionare **Modifica**.

    ![Modificare la disponibilità in base alle impostazioni della griglia Nome API](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. Selezionare **Impostazioni colonna** e quindi nel riquadro **Modifica impostazioni colonna** selezionare Disponibilità **Columns** **(%) (Soglie e Formattato)**.

6. Modificare il valore dello stato di integrità **Critico** da **90** a **85,** quindi fare clic su **Salva e chiudi**.

    ![Modificare il valore della soglia di disponibilità per lo stato criticoModify the availability threshold value for critical state](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. Selezionare **Salva con** nome dalla barra dei comandi per salvare una copia della cartella di lavoro con le personalizzazioni e quindi fare clic su **Fine modifica** per tornare alla modalità di lettura.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Questa sezione consente di diagnosticare e risolvere i problemi di alcuni dei problemi comuni che possono verificarsi quando si usa Monitoraggio di Azure per l'archiviazione (anteprima). Usare l'elenco che segue per individuare le informazioni pertinenti a un problema specifico.

### <a name="resolving-performance-capacity-or-availability-issues"></a>Risoluzione dei problemi di prestazioni, capacità o disponibilità

Per risolvere eventuali problemi relativi all'archiviazione identificati con Monitoraggio di Azure per l'archiviazione (anteprima), vedere le linee guida per la [risoluzione dei problemi](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)di Archiviazione di Azure.To help troubleshoot any storage-related issues you identify with Azure Monitor for Storage (preview), see the Azure Storage troubleshooting guidance .  

### <a name="why-can-i-only-see-200-storage-accounts"></a>Perché è possibile visualizzare solo 200 account di archiviazione?

Il numero di account di archiviazione selezionati ha un limite di 200, indipendentemente dal numero di sottoscrizioni selezionate.

### <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>Cosa succede quando si fa clic su un riquadro aggiunto di recente nel dashboard?

* Se si fa clic in un punto qualsiasi del riquadro, verrà visualizzato il percorso da cui è stato aggiunto il riquadro. Ad esempio, se si aggiunge un grafico nella scheda "Panoramica account di archiviazione", quando si fa clic su tale riquadro nel dashboard verrà aperta la visualizzazione predefinita, tuttavia se si aggiunge un grafico dalla copia salvata, verrà aperta la visualizzazione della copia salvata.
* L'icona del filtro in alto a sinistra del titolo apre la scheda "Configura impostazioni riquadro".
* L'icona dell'ellisse in alto a destra ti darà le opzioni per "Personalizzare i dati del titolo", "personalizzare", "aggiornare" e "rimuovere dal cruscotto".

### <a name="what-happens-when-i-save-a-workbook"></a>Cosa succede quando si salva una cartella di lavoro?

* Quando si salva una cartella di lavoro, consente di creare una nuova copia della cartella di lavoro con le modifiche e modificare il titolo. Il salvataggio non sovrascrive la cartella di lavoro, la cartella di lavoro corrente sarà sempre la visualizzazione predefinita.
* Una cartella di lavoro **non salvata** è solo la visualizzazione predefinita.


### <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Perché non vedo tutte le mie sottoscrizioni nel portale?

Il portale mostrerà i dati solo per le sottoscrizioni selezionate all'avvio del portale. Per modificare le sottoscrizioni selezionate, passare in alto a destra e fare clic sul blocco appunti con un'icona di filtro. Verrà visualizzata la scheda Directory e sottoscrizioni.

![Directory e sottoscrizione](./media/storage-insights-overview/fqa3.png)

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>Come modificare la colorazione e la soglia per la disponibilità?

Fare riferimento alla sezione [Modificare la soglia](storage-insights-overview.md#modify-the-availability-threshold) di disponibilità per la procedura dettagliata su come modificare la colorazione e le soglie per la disponibilità.

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>Come analizzare e risolvere i problemi relativi ai dati visualizzati in Monitoraggio di Azure per l'archiviazione?

 Per informazioni dettagliate su come analizzare e risolvere i problemi relativi ai dati di Archiviazione di Azure illustrati in Monitoraggio di Azure per archiviazione, vedere l'articolo [Monitorare, diagnosticare e risolvere i problemi](https://docs.microsoft.com/azure/storage/common/storage-monitoring-diagnosing-troubleshooting) di Archiviazione di Microsoft Azure.Refer to the Monitor, diagnose, and troubleshoot Microsoft Azure Storage article for details on how to analyze and troubleshoot the Azure Storage data shown in Azure Monitor for Storage.

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>Perché non vedo tutti i tipi di errori nelle metriche?

Attualmente, vengono visualizzati fino a tre diversi tipi di errori e il resto degli errori vengono raggruppati in un unico bucket. È controllato tramite splitByLimit e può essere modificato. Per modificare questa proprietà:

1. Fare clic su Modifica cartella di lavoro.
2. Vai alle metriche, fai clic su Modifica e quindi seleziona **Transazioni, Somma** o qualsiasi metrica tu voglia modificare.

    ![Vai alle metriche e fare clic su modifica poi su "Transazioni, somme"](./media/storage-insights-overview/fqa7.png)

1. Quindi modificare il numero di divisioni.

    ![Seleziona parametri metrici"](./media/storage-insights-overview/fqa7-2.png)

Se si desidera visualizzare n diversi tipi di errore rispetto a specificare splitByLimit come n , 1 in più per il resto degli errori.

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>La cartella di lavoro è stata salvata durante un account di archiviazione. Perché non riesco a trovarlo ora?

Ogni cartella di lavoro viene salvata nell'account di archiviazione in cui è stata salvata. Provare a trovare l'account di archiviazione specifico in cui l'utente ha salvato la cartella di lavoro. In caso contrario, non è possibile trovare una cartella di lavoro specifica senza conoscere la risorsa (account di archiviazione).

### <a name="what-is-time-range"></a>Che cos'è l'intervallo di tempo?

L'intervallo di tempo mostra i dati di un determinato intervallo di tempo. Ad esempio, se l'intervallo di tempo è di 24 ore, vengono visualizzati i dati delle ultime 24 ore.

### <a name="what-is-time-granularity-time-grain"></a>Che cos'è la granularità temporale (granatura temporale)?

La granularità temporale è la differenza di tempo tra due punti dati. Ad esempio, se il valore granello temporale è impostato su 1 secondo, significa che le metriche vengono raccolte ogni secondo.

### <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Qual è la granularità temporale dopo aver appuntare qualsiasi parte delle cartelle di lavoro a un dashboard?

La granularità temporale predefinita è impostata su automatica, attualmente non può essere modificata in questo momento.

### <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Come si modifica l'intervallo di tempo/intervallo di tempo del passaggio della cartella di lavoro nel dashboard?

Per impostazione predefinita, l'intervallo di tempo/intervallo di tempo nel riquadro del dashboard è impostato su 24 ore, per modificare questo clic sui puntini di sospensione in alto a destra, selezionare La casella "Personalizza i dati del **riquadro,** selezionare "ignora le impostazioni del tempo del dashboard a livello di titolo" e quindi selezionare un intervallo di tempo utilizzando il menu a discesa.  

![Selezionare i puntini di sospensione nell'angolo destro del riquadro e scegliere Personalizza questi dati](./media/storage-insights-overview/fqa-data-settings.png)

![In Configura impostazioni riquadro selezionare l'elenco a discesa dell'intervallo di tempo per modificare l'intervallo di tempo/intervallo di tempo](./media/storage-insights-overview/fqa-timespan.png)

### <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Come si modifica il titolo della cartella di lavoro o di un passaggio della cartella di lavoro aggiunto a un dashboard?

Il titolo del passaggio della cartella di lavoro o della cartella di lavoro aggiunto a un dashboard mantiene lo stesso nome che aveva nella cartella di lavoro. Per modificare il titolo, è necessario salvare la propria copia della cartella di lavoro. Quindi sarai in grado di assegnare un nome alla cartella di lavoro prima di premere Salva.

![Selezionare Salva nella parte superiore per salvare una copia della cartella di lavoro e modificarne il nome](./media/storage-insights-overview/fqa-change-workbook-name.png)

Per modificare il nome di un passaggio nella cartella di lavoro salvata, selezionare Modifica sotto il passaggio e quindi selezionare l'ingranaggio nella parte inferiore delle impostazioni.

![Selezionare Modifica nella parte inferiore del passaggio di una cartella di lavoro per aprire le impostazioni](./media/storage-insights-overview/fqa-edit.png)
![Nelle impostazioni selezionare l'ingranaggio nella parte inferiore, per poter modificare il nome del passaggio](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Passaggi successivi

* Configurare [gli avvisi di metrica](../platform/alerts-metric.md) e [le notifiche](../../service-health/alerts-activity-log-service-notifications.md) di integrità dei servizi per configurare gli avvisi automatici per facilitare il rilevamento dei problemi.

* Informazioni sugli scenari che le cartelle di lavoro sono progettate per supportare, su come creare report nuovi e personalizzarle esistenti e altro ancora esaminando la revisione di [Creare report interattivi con cartelle di lavoro](../app/usage-workbooks.md)di Monitoraggio di Azure.

* Per una guida dettagliata sull'utilizzo di Analisi archiviazione e di altri strumenti per identificare, diagnosticare e risolvere i problemi relativi ad Archiviazione di Azure, vedere [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md).
