---
title: Monitorare i servizi di archiviazione di Azure con monitoraggio di Azure per l'archiviazione (anteprima) | Microsoft Docs
description: Questo articolo descrive il monitoraggio di Azure per la funzionalità di archiviazione che offre agli amministratori dell'archiviazione una rapida comprensione dei problemi di prestazioni e utilizzo con gli account di archiviazione di Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 08/15/2019
ms.openlocfilehash: aaf7d1a38d4b809b904b6c607a4cfc23efd4dde5
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286314"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>Monitoraggio del servizio di archiviazione con monitoraggio di Azure per l'archiviazione (anteprima)

Monitoraggio di Azure per l'archiviazione (anteprima) offre un monitoraggio completo degli account di archiviazione di Azure, fornendo una visualizzazione unificata delle prestazioni, della capacità e della disponibilità dei servizi di archiviazione di Azure. È possibile osservare la capacità di archiviazione e le prestazioni in due modi, visualizzare direttamente da un account di archiviazione o da una vista di monitoraggio di Azure per visualizzare tutti i gruppi di account di archiviazione. 

Questo articolo illustra l'esperienza di monitoraggio di Azure per l'archiviazione (anteprima) per ottenere informazioni di utilità pratica sull'integrità e sulle prestazioni degli account di archiviazione su larga scala, con una capacità di concentrarsi sugli hotspot e diagnosticare la latenza, la limitazione delle richieste, e problemi di disponibilità.

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>Introduzione a monitoraggio di Azure per l'archiviazione (anteprima)

Prima di approfondire l'esperienza, è necessario comprenderne il modo in cui presenta e visualizza le informazioni. Se si seleziona la funzionalità di archiviazione direttamente da un account di archiviazione o da monitoraggio di Azure, monitoraggio di Azure per l'archiviazione offre un'esperienza coerente. 

In combinazione offre:

* **Nel punto di vista della scala** , che mostra una visualizzazione snapshot della loro disponibilità in base all'integrità del servizio di archiviazione o all'operazione API, l'utilizzo indica il numero totale di richieste ricevute dal servizio di archiviazione e la latenza che mostra il tempo medio necessario per l'elaborazione delle richieste da parte del servizio di archiviazione o del tipo di operazione API. È anche possibile visualizzare la capacità per BLOB, file, tabelle e code.

* Eseguire il **drill-down dell'analisi** di un determinato account di archiviazione per facilitare la diagnosi dei problemi o l'analisi dettagliata per disponibilità, prestazioni, errori e capacità di categoria. Selezionando una di queste opzioni viene fornita una visualizzazione dettagliata delle metriche.  

* **Personalizzabile** , in cui è possibile modificare la metrica che si desidera visualizzare, modificare o impostare le soglie che si allineano ai limiti e salvare come cartella di lavoro personalizzata. I grafici nella cartella di lavoro possono essere aggiunti al dashboard di Azure.  

Per questa funzionalità non è necessario abilitare o configurare elementi, per impostazione predefinita vengono raccolte le metriche di archiviazione degli account di archiviazione. Se non si ha familiarità con le metriche disponibili in archiviazione di Azure, vedere la descrizione e la definizione nelle metriche di archiviazione di Azure esaminando le [metriche di archiviazione di Azure](../../storage/common/storage-metrics-in-azure-monitor.md).

>[!NOTE]
>Non sono previsti addebiti per accedere a questa funzionalità e verranno addebitate solo le funzionalità essenziali di monitoraggio di Azure configurate o abilitate, come descritto nella pagina [Dettagli prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/) .

>[!NOTE]
>Il monitoraggio di Azure per l'archiviazione non supporta gli [account per utilizzo generico V1](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts).
>

## <a name="view-from-azure-monitor"></a>Visualizza da monitoraggio di Azure

Da monitoraggio di Azure è possibile visualizzare i dettagli relativi a transazioni, latenza e capacità da più account di archiviazione nella sottoscrizione e identificare le prestazioni, i problemi di capacità e gli errori.

Per visualizzare l'utilizzo e la disponibilità degli account di archiviazione in tutte le sottoscrizioni, seguire questa procedura.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **monitoraggio** nel riquadro a sinistra nel portale di Azure e nella sezione **Insights** selezionare **account di archiviazione (anteprima)** .

    ![Visualizzazione di più account di archiviazione](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>Cartella di lavoro Panoramica

Nella cartella di lavoro **Panoramica** per la sottoscrizione selezionata la tabella Visualizza le metriche di archiviazione interattiva e lo stato di disponibilità del servizio per un massimo di 10 account di archiviazione raggruppati nella sottoscrizione. È possibile filtrare i risultati in base alle opzioni selezionate dagli elenchi a discesa seguenti:

* **Sottoscrizioni** sono elencate solo le sottoscrizioni con account di archiviazione.  

* **Account di archiviazione** : per impostazione predefinita, sono già selezionati 10 account di archiviazione. Se si selezionano tutti o più account di archiviazione nel selettore di ambito, verranno restituiti fino a 200 account di archiviazione. Se, ad esempio, si dispone di un totale di 573 account di archiviazione tra tre sottoscrizioni selezionate, verranno visualizzati solo gli account 200. 

* **Intervallo di tempo** : per impostazione predefinita, Visualizza le ultime 4 ore di informazioni in base alle selezioni corrispondenti effettuate.

Il riquadro contatore sotto gli elenchi a discesa esegue il rollup del numero totale di account di archiviazione nella sottoscrizione e indica quanti del totale sono selezionati. Per le colonne nella cartella di lavoro è presente una codifica dei colori condizionale o Heatmaps che segnalano gli errori o le metriche delle transazioni. Il colore più profondo ha il valore più alto e un colore più chiaro è basato sui valori più bassi. Per le colonne basate sugli errori, il valore è in rosso e per le colonne basate su metriche, il valore è in blu.

Selezionare un valore in colonne **disponibilità**, **latenza E2E**, **latenza server**e **tipo di errore transazione/errori** indirizzare l'utente a un report adattato al tipo specifico di metriche di archiviazione che corrispondono alla colonna selezionata per l'account di archiviazione. Per ulteriori informazioni sulle cartelle di lavoro per ogni categoria, vedere la sezione [cartelle di lavoro di archiviazione dettagliata](#detailed-storage-workbooks) di seguito. 

>[!NOTE]
>Per informazioni dettagliate sugli errori che possono essere visualizzati nel report, vedere [schema del tipo di risposta](../../storage/common/storage-metrics-in-azure-monitor.md#metrics-dimensions) e cercare i tipi di risposta, ad esempio **ServerOtherError**, **ClientOtherError**, **ClientThrottlingError**. A seconda degli account di archiviazione selezionati, in presenza di più di tre tipi di errori segnalati, tutti gli altri errori sono rappresentati nella categoria **altro**.

La soglia di **disponibilità** predefinita è:

* Avviso-99%
* Critico-90%

Per impostare una soglia di disponibilità in base ai risultati dell'osservazione o dei requisiti, vedere [modificare la soglia di disponibilità](#modify-the-availability-threshold). 

### <a name="capacity-workbook"></a>Cartella di lavoro capacità

Selezionare **capacità** nella parte superiore della pagina e verrà aperta la cartella di lavoro **capacità** . Mostra la quantità di spazio di archiviazione totale usato nell'account e la capacità usata da ogni servizio dati nell'account per identificare le risorse di archiviazione utilizzate e sottoutilizzate.

![Cartella di lavoro della capacità di più account di archiviazione](./media/storage-insights-overview/storage-account-capacity-02.png) 

Per le colonne nella cartella di lavoro è presente una codifica dei colori condizionale o Heatmaps che segnala la metrica di capacità con un valore blu. Il colore più profondo ha il valore più alto e un colore più chiaro è basato sui valori più bassi.

Quando si seleziona un valore in una delle colonne della cartella di lavoro, si esegue il drill-down nella cartella di lavoro **capacità** per l'account di archiviazione. Ulteriori dettagli sul report drill-down sono descritti nella sezione [cartelle di lavoro di archiviazione dettagliata](#detailed-storage-workbooks) di seguito. 

## <a name="view-from-a-storage-account"></a>Visualizzare da un account di archiviazione

Per accedere Monitoraggio di Azure per le macchine virtuali direttamente da un account di archiviazione:

1. Nella portale di Azure selezionare account di archiviazione.

2. Dall'elenco scegliere un account di archiviazione. Nella sezione monitoraggio scegliere Insights (anteprima).

    ![Pagina di panoramica dell'account di archiviazione selezionato](./media/storage-insights-overview/storage-account-direct-overview-01.png)

Nella cartella di lavoro **Panoramica** per l'account di archiviazione vengono visualizzate diverse metriche delle prestazioni di archiviazione che consentono di valutare rapidamente:

* Integrità del servizio di archiviazione per verificare immediatamente se un problema al di fuori del controllo influisce sul servizio di archiviazione nell'area in cui è distribuito, come indicato nella colonna **Riepilogo** .

* Grafici delle prestazioni interattive con i dettagli più importanti relativi alla capacità di archiviazione, alla disponibilità, alle transazioni e alla latenza.  

* I riquadri metrica e stato evidenziano la disponibilità del servizio, il numero totale di transazioni per il servizio di archiviazione, la latenza E2E e la latenza del server.

La selezione di uno dei pulsanti per **errori**, **prestazioni**, **disponibilità**e **capacità** apre la rispettiva cartella di lavoro. 

![Pagina di panoramica dell'account di archiviazione selezionato](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>Cartelle di lavoro di archiviazione dettagliate

Se è stato selezionato un valore in colonne **disponibilità**, **latenza E2E**, **latenza server**e **tipo di errore transazione/errori** dalla cartella di lavoro **Panoramica** di più account di archiviazione o se si seleziona uno dei pulsanti per **errori**, **prestazioni**, **disponibilità**e **capacità** dalla cartella di lavoro **Panoramica** da un account di archiviazione specifico, ciascuno fornisce un set di informazioni interattive relative all'archiviazione personalizzate per tale categoria.  

* **Disponibilità** apre la cartella di lavoro **disponibilità** . Mostra lo stato di integrità corrente del servizio di archiviazione di Azure, una tabella che mostra lo stato di integrità disponibile di ogni oggetto categorizzato in base al servizio dati definito nell'account di archiviazione con una linea di tendenza che rappresenta l'intervallo di tempo selezionato e un grafico di tendenza della disponibilità per ogni servizio dati nell'account.  

    ![Esempio di report di disponibilità](./media/storage-insights-overview/storage-account-availability-01.png)

* **Latenza E2E** e **latenza server** consente di aprire la cartella di lavoro **prestazioni** . Include un riquadro Stato rollup che mostra la latenza di E2E e la latenza del server, un grafico delle prestazioni di E2E rispetto alla latenza del server e una tabella che suddivide la latenza delle chiamate effettuate dall'API categorizzate in base al servizio dati definito nell'account di archiviazione.

    ![Esempio di rapporto di prestazioni](./media/storage-insights-overview/storage-account-performance-01.png)

* La selezione di una delle categorie di errore elencate nella griglia apre la cartella di lavoro non **riuscita** . Il report Mostra i riquadri della metrica di tutti gli altri errori lato client tranne quelli descritti e le richieste riuscite, errori di limitazione client, un grafico delle prestazioni per la metrica della dimensione del **tipo di risposta** transazione specifica dell'attributo ClientOtherError e due tabelle- **transazioni per nome API** e **transazioni per tipo di risposta**.

   ![Esempio di report di errore](./media/storage-insights-overview/storage-account-failures-01.png)

* **Capacity** apre la cartella di lavoro **Capacity** . Mostra la quantità totale di spazio di archiviazione utilizzato per ogni oggetto dati di archiviazione nell'account nei riquadri e nel grafico e il numero di oggetti dati archiviati nell'account.  

    ![Pagina di capacità dell'account di archiviazione selezionato](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>Pin ed export

È possibile aggiungere una delle sezioni della metrica a un dashboard di Azure selezionando l'icona a puntina da disegno in alto a destra nella sezione.

![Esempio di aggiunta della sezione metrica al dashboard](./media/storage-insights-overview/workbook-pin-example.png)

Le cartelle di lavoro della **Panoramica** o della **capacità** di più sottoscrizioni e account di archiviazione supportano l'esportazione dei risultati in formato Excel selezionando l'icona a forma di freccia giù a destra dell'icona a forma di puntina da disegno.

![Esempio di risultati esportazione griglia cartella di lavoro](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>Personalizzare monitoraggio di Azure per l'archiviazione (anteprima)

Questa sezione illustra gli scenari comuni per la modifica della cartella di lavoro per personalizzare il supporto delle esigenze di analisi dei dati:

* Definire l'ambito della cartella di lavoro per selezionare sempre una sottoscrizione o uno o più account di archiviazione specifici
* Modificare le metriche nella griglia
* Modificare la soglia di disponibilità
* Modificare il rendering del colore

Le personalizzazioni vengono salvate in una cartella di lavoro personalizzata per impedire la sovrascrittura della configurazione predefinita nella cartella di lavoro pubblicata. Le cartelle di lavoro vengono salvate in un gruppo di risorse, nella sezione **report personali** riservata all'utente o nella sezione **report condivisi** accessibile a tutti gli utenti con accesso al gruppo di risorse. Dopo avere salvato la cartella di lavoro personalizzata, è necessario passare alla raccolta di cartelle di lavoro per avviarla.

![Avvia raccolta cartelle di lavoro dalla barra dei comandi](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>Specifica di una sottoscrizione o di un account di archiviazione

È possibile configurare le cartelle di lavoro della **capacità** e della **Panoramica** dell'account di archiviazione e della multisottoscrizione per definire l'ambito di una o più sottoscrizioni o account di archiviazione a ogni esecuzione, seguire questa procedura.

1. Selezionare **monitoraggio** dal portale e quindi selezionare **account di archiviazione (anteprima)** nel riquadro a sinistra.

2. Nella cartella di lavoro **Panoramica** , dalla barra dei comandi, selezionare **modifica**.

3. Selezionare nell'elenco a discesa **sottoscrizioni** una o più sottoscrizioni per le quali si desidera impostare il valore predefinito. Tenere presente che la cartella di lavoro supporta la selezione di un totale di 10 sottoscrizioni.  

4. Selezionare dall'elenco a discesa **account di archiviazione** uno o più account per i quali si vuole impostare come predefinito. Tenere presente che la cartella di lavoro supporta la selezione di un totale di 200 account di archiviazione. 

5. Selezionare **Salva con nome** dalla barra dei comandi per salvare una copia della cartella di lavoro con le personalizzazioni, quindi fare clic su **modifica eseguita** per tornare alla modalità di lettura.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>Modificare le metriche e i colori nella cartella di lavoro

Le cartelle di lavoro predefinite contengono dati di metrica e sono in grado di modificare o rimuovere una qualsiasi delle visualizzazioni e personalizzarle in base alle esigenze specifiche del team.

In questo esempio, si sta lavorando con la cartella di lavoro di capacità dell'account di archiviazione e multisottoscrizione per dimostrare come:

* Rimuovere una metrica
* Modificare il rendering del colore

È possibile eseguire le stesse modifiche in una qualsiasi delle cartelle di lavoro di **errori**, **prestazioni**, **disponibilità**e **capacità** predefinite.

1. Selezionare **monitoraggio** dal portale e quindi selezionare **account di archiviazione (anteprima)** nel riquadro a sinistra.

2. Selezionare **capacità** per passare alla cartella di lavoro capacità e dalla barra dei comandi, selezionare **modifica** dalla barra dei comandi.

    ![Selezionare modifica per modificare una cartella di lavoro](./media/storage-insights-overview/workbook-edit-workbook.png)

3. Accanto alla sezione metrica, selezionare **modifica**.

    ![Selezionare modifica per modificare la metrica della cartella di lavoro della capacità](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. Verrà rimossa la colonna della **sequenza temporale della capacità utilizzata** per l'account, quindi selezionare **Impostazioni colonna** nella griglia metriche.

    ![Modificare le impostazioni delle colonne](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. Nel riquadro **Modifica impostazioni colonna** selezionare sotto la sezione **colonne** **Microsoft. storage/Storageaccounts-Capacity-capacità utilizzata Timeline $ | L'account usa la sequenza temporale della capacità $** e, sotto il **renderer della colonna** dell'elenco a discesa, selezionare **Hidden**.

6. Selezionare **Salva e Chiudi** per eseguire il commit della modifica.

A questo punto è possibile modificare il tema colori per la metrica della capacità nel report per usare il verde anziché il blu.

1. Selezionare **le impostazioni delle colonne** nella griglia metrica.

2. Nel riquadro **Modifica impostazioni colonna** selezionare sotto la sezione **colonne** **Microsoft. storage/Storageaccounts-Capacity-capacità utilizzata $ | Microsoft. storage/storageaccounts/Blobservices-Capacity-BlobCapacity $ | Microsoft. storage/Storageaccounts/fileservices-Capacity-filecapacity $ | Microsoft. storage/storageaccounts/Queueservices-Capacity-QueueCapacity $ | Microsoft. storage/storageaccounts/Tableservices-Capacity-TableCapacity** Nella **tavolozza dei colori**dell'elenco a discesa selezionare **verde**.

3. Selezionare **Salva e Chiudi** per eseguire il commit della modifica.

4. Selezionare **Salva con nome** dalla barra dei comandi per salvare una copia della cartella di lavoro con le personalizzazioni, quindi fare clic su **modifica eseguita** per tornare alla modalità di lettura.  

### <a name="modify-the-availability-threshold"></a>Modificare la soglia di disponibilità

In questo esempio si usa la cartella di lavoro di capacità dell'account di archiviazione e si dimostra come modificare la soglia di disponibilità. Per impostazione predefinita, la percentuale di disponibilità del report della griglia e del riquadro è configurata con una soglia minima di 90 e la soglia massima di 99. Il valore soglia minimo della **percentuale di disponibilità** nella griglia **disponibilità per nome API** verrà modificato in 85%, il che significa che lo stato di integrità passa a critico se la soglia è inferiore al 85%. 

1. Selezionare **account di archiviazione** dal portale e quindi selezionare un account di archiviazione dall'elenco.

2. Selezionare **Insights (anteprima)** nel riquadro a sinistra.

3. Nella cartella di lavoro selezionare **disponibilità** per passare alla cartella di lavoro disponibilità, quindi scegliere **modifica** dalla barra dei comandi. 

4. Scorrere fino alla parte inferiore della pagina e sul lato sinistro accanto alla griglia **availability by API** selezionare **Edit (modifica**).

    ![Modificare la disponibilità in base alle impostazioni della griglia nome API](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. Selezionare **Impostazioni colonne** , quindi nel riquadro **Modifica impostazioni** colonne, nella sezione **colonne** selezionare **disponibilità (%) (Soglie + formattato)** .

6. Modificare il valore dello stato di integrità **critico** da **90** a **85** , quindi fare clic su **Salva e Chiudi**.

    ![Modificare il valore soglia di disponibilità per lo stato critico](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. Selezionare **Salva con nome** dalla barra dei comandi per salvare una copia della cartella di lavoro con le personalizzazioni, quindi fare clic su **modifica eseguita** per tornare alla modalità di lettura.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Questa sezione illustra la diagnosi e la risoluzione dei problemi comuni che possono verificarsi quando si usa monitoraggio di Azure per l'archiviazione (anteprima). Usare l'elenco che segue per individuare le informazioni pertinenti a un problema specifico.

### <a name="resolving-performance-capacity-or-availability-issues"></a>Risoluzione dei problemi di prestazioni, capacità o disponibilità

Per risolvere eventuali problemi correlati all'archiviazione identificati con monitoraggio di Azure per l'archiviazione (anteprima), vedere le indicazioni per la [risoluzione dei problemi](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)di archiviazione di Azure.  

### <a name="why-can-i-only-see-200-storage-accounts"></a>Perché è possibile visualizzare solo gli account di archiviazione 200?

Il numero di account di archiviazione selezionati ha un limite di 200, indipendentemente dal numero di sottoscrizioni selezionate.

### <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>Cosa accade quando si fa clic su un riquadro aggiunto di recente nel dashboard?

* Se si fa clic in un punto qualsiasi del riquadro, viene visualizzata la scheda dalla quale è stato aggiunto il riquadro. Se, ad esempio, si aggiunge un grafico nella scheda "Panoramica dell'account di archiviazione", quando si fa clic su tale riquadro nel dashboard verrà aperta la visualizzazione predefinita, tuttavia se si aggiunge un grafo dalla propria copia salvata, verrà aperta la visualizzazione della copia salvata.
* L'icona del filtro in alto a sinistra del titolo apre la scheda "Configura impostazioni riquadro".
* L'icona a ellisse in alto a destra fornirà le opzioni per "personalizzare i dati del titolo", "Customize", "Refresh" e "Remove from Dashboard".

### <a name="what-happens-when-i-save-a-workbook"></a>Cosa accade quando si salva una cartella di lavoro?

* Quando si salva una cartella di lavoro, è possibile creare una nuova copia della cartella di lavoro con le modifiche e modificare il titolo. Salvando non viene sovrascritta la cartella di lavoro, la cartella di lavoro corrente sarà sempre la visualizzazione predefinita.
* Una cartella di lavoro non **salvata** è solo la visualizzazione predefinita.


### <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Perché non vengono visualizzate tutte le sottoscrizioni nel portale?

Il portale visualizzerà solo i dati per le sottoscrizioni selezionate all'avvio del portale. Per modificare le sottoscrizioni selezionate, andare in alto a destra e fare clic sul notebook con un'icona di filtro. Verrà visualizzata la scheda Directory + sottoscrizioni.

![Directory + sottoscrizione](./media/storage-insights-overview/fqa3.png)

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>Come modificare la colorazione e la soglia per la disponibilità?

Per la procedura dettagliata su come modificare la colorazione e le soglie per la disponibilità, vedere la sezione [modificare la soglia di disponibilità](storage-insights-overview.md#modify-the-availability-threshold) .

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>Come analizzare e risolvere i problemi relativi ai dati visualizzati in monitoraggio di Azure per l'archiviazione

 Per informazioni dettagliate su come analizzare e risolvere i problemi relativi ai dati di archiviazione di Azure visualizzati in monitoraggio di Azure per l'archiviazione, vedere l'articolo [monitorare, diagnosticare e risolvere i problemi di archiviazione di Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-monitoring-diagnosing-troubleshooting) .

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>Perché non vengono visualizzati tutti i tipi di errori nelle metriche?

Attualmente vengono visualizzati fino a tre tipi diversi di errori e il resto degli errori viene raggruppato in un singolo bucket. Viene controllata tramite splitByLimit e può essere modificata. Per modificare questa proprietà:

1. Fare clic su Modifica cartella di lavoro.
2. Passare a metrica, fare clic su modifica e quindi selezionare **transazioni, somma** o qualsiasi metrica che si vuole modificare.

    ![Passare a metrica e fare clic su modifica e quindi su "transazioni, somme"](./media/storage-insights-overview/fqa7.png)

1. Modificare quindi il numero di divisioni.

    ![Selezionare i parametri della metrica "](./media/storage-insights-overview/fqa7-2.png)

Se si desidera visualizzare n tipi di errore diversi rispetto a specificare splitByLimit come n + 1, 1 in aggiunta per il resto degli errori.

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>La cartella di lavoro è stata salvata in un account di archiviazione. Perché non è possibile trovarlo adesso?

Ogni cartella di lavoro viene salvata nell'account di archiviazione in cui è stato salvato. Provare a trovare l'account di archiviazione specifico in cui l'utente ha salvato la cartella di lavoro. In caso contrario, non è possibile trovare una cartella di lavoro specifica senza conoscere la risorsa (account di archiviazione).

### <a name="what-is-time-range"></a>Che cos'è l'intervallo di tempo?

L'intervallo di tempo Mostra i dati di un determinato intervallo di tempo. Se, ad esempio, l'intervallo di tempo è di 24 ore, vengono visualizzati i dati delle ultime 24 ore.

### <a name="what-is-time-granularity-time-grain"></a>Che cos'è la granularità temporale (Time Grain)?

La granularità temporale è la differenza di tempo tra due punti dati. Se, ad esempio, l'intervallo di tempo è impostato su 1 secondo, significa che la metrica viene raccolta ogni secondo.

### <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Qual è la granularità temporale dopo che è stata aggiunta una parte delle cartelle di lavoro a un dashboard?

La granularità predefinita è impostata su automatico, attualmente non può essere modificata in questo momento.

### <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Ricerca per categorie modificare l'intervallo di tempo e TimeSpan del passaggio della cartella di lavoro nel dashboard?

Per impostazione predefinita, l'intervallo di tempo/intervallo di tempo nel riquadro del dashboard è impostato su 24 ore, per modificare questo clic sui puntini di sospensione in alto a destra, selezionare **Personalizza dati del riquadro**, selezionare "Sostituisci le impostazioni dell'ora del dashboard al livello del titolo" e quindi selezionare un intervallo di tempo usando il menu a discesa.  

![Selezionare i puntini di sospensione nell'angolo destro del riquadro e scegliere Personalizza questi dati.](./media/storage-insights-overview/fqa-data-settings.png)

![In Configura impostazioni riquadro selezionare l'elenco a discesa TimeSpan per modificare l'intervallo di tempo](./media/storage-insights-overview/fqa-timespan.png)

### <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Ricerca per categorie modificare il titolo della cartella di lavoro o un passaggio di cartella di lavoro aggiunto a un dashboard?

Il titolo della cartella di lavoro o del passaggio della cartella di lavoro aggiunto a un dashboard mantiene lo stesso nome disponibile nella cartella di lavoro. Per modificare il titolo, è necessario salvare la propria copia della cartella di lavoro. Sarà quindi possibile denominare la cartella di lavoro prima di fare clic su Salva.

![Selezionare Salva nella parte superiore per salvare una copia della cartella di lavoro e modificarne il nome](./media/storage-insights-overview/fqa-change-workbook-name.png)

Per modificare il nome di un passaggio nella cartella di lavoro salvata, selezionare modifica sotto il passaggio e quindi selezionare l'ingranaggio nella parte inferiore delle impostazioni.

![selezionare modifica nella parte inferiore di un passaggio della cartella di lavoro per aprire le impostazioni](./media/storage-insights-overview/fqa-edit.png)
![in impostazioni selezionare l'ingranaggio in basso per poter modificare il nome del passaggio](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Passaggi successivi

* Configurare gli [avvisi delle metriche](../platform/alerts-metric.md) e le notifiche sull'integrità del [servizio](../../service-health/alerts-activity-log-service-notifications.md) per impostare avvisi automatici per facilitare il rilevamento dei problemi.

* Informazioni sulle cartelle di lavoro degli scenari sono progettate per supportare, come creare nuovi report esistenti e personalizzarli e altro ancora esaminando [creare report interattivi con le cartelle di lavoro di monitoraggio di Azure](../app/usage-workbooks.md).

* Per una guida dettagliata sull'utilizzo di Analisi archiviazione e di altri strumenti per identificare, diagnosticare e risolvere i problemi relativi ad Archiviazione di Azure, vedere [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md).
