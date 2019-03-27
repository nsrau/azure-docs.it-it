---
title: Configurare l'agente di Azure Log Analytics per computer Windows in ambienti ibridi | Microsoft Docs
description: Informazioni su come distribuire l'agente di Log Analytics per Windows in esecuzione in computer all'esterno di Azure e abilitare la raccolta di dati con Log Analytics.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 02/05/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 44e386b7da6fde2b24f02381760fcd96118f7e51
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58092957"
---
# <a name="configure-log-analytics-agent-for-windows-computers-in-a-hybrid-environment"></a>Configurare l'agente di Log Analytics per computer Windows in un ambiente ibrido
[Azure Log Analytics](../../azure-monitor/platform/agent-windows.md) può raccogliere dati direttamente dai computer Windows fisici o virtuali nel data center o in altri ambienti cloud in un unico repository per l'esecuzione di analisi dettagliate e della correlazione.  Questa guida introduttiva mostra come configurare e raccogliere dati dal computer Windows in pochi semplici passaggi.  Per le macchine virtuali Windows di Azure, vedere l'argomento seguente [Raccogliere dati sulle macchine virtuali di Azure](../../azure-monitor/learn/quick-collect-azurevm.md).  

Per comprendere la configurazione supportata, vedere i [sistemi operativi Windows supportati](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) e la [configurazione del firewall di rete](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).
 
Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="log-in-to-azure-portal"></a>Accedere al portale di Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Creare un'area di lavoro
1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.<br><br> ![Portale di Azure](media/quick-collect-windows-computer/azure-portal-01.png)<br><br>  
2. Fare clic su **Crea** e quindi selezionare le opzioni per gli elementi seguenti:

   * Specificare un nome per la nuova **area di lavoro di Log Analytics Workspace**, ad esempio *DefaultLAWorkspace*. 
   * Selezionare una **sottoscrizione** a cui collegarsi. Se la sottoscrizione selezionata per impostazione predefinita non è appropriata, è possibile sceglierne una dall'elenco a discesa.
   * Per **Gruppo di risorse**, selezionare un gruppo di risorse esistente contenente una o più macchine virtuali di Azure.  
   * Selezionare la **località** in cui sono distribuite le VM.  Per altre informazioni, vedere le [are in cui è disponibile Log Analytics](https://azure.microsoft.com/regions/services/).  
   * Se si sta creando un'area di lavoro in una nuova sottoscrizione creata dopo il 2 aprile 2018, verrà automaticamente usato il piano di determinazione dei prezzi *Per GB* e non sarà disponibile l'opzione che consente di selezionare un piano tariffario.  Se si sta creando un'area di lavoro per una sottoscrizione esistente creata prima del 2 aprile o per una sottoscrizione collegata a un Contratto Enterprise esistente, selezionare il piano tariffario preferito.  Per altre informazioni sui piani specifici, vedere [Dettagli prezzi di Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Creare il pannello della risorsa Log Analytics](media/quick-collect-windows-computer/create-loganalytics-workspace-02.png)<br>  

3. Dopo aver specificato le informazioni necessarie nel riquadro **Area di lavoro di Log Analytics**, fare clic su **OK**.  

Per tenere traccia dello stato di avanzamento della verifica delle informazioni e della creazione dell'area di lavoro, è possibile usare la voce **Notifiche** nel menu. 

## <a name="obtain-workspace-id-and-key"></a>Ottenere l'ID e la chiave dell'area di lavoro
Prima di installare Microsoft Monitoring Agent per Windows, sono necessari l'ID e la chiave dell'area di lavoro per l'area di lavoro di Log Analytics.  Queste informazioni sono richieste dall'installazione guidata per configurare correttamente l'agente e verificare che possa comunicare correttamente con Log Analytics.  

1. Nel portale di Azure fare clic su **Tutti i servizi** nell'angolo superiore sinistro. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.
2. Nell'elenco di aree di lavoro di Log Analytics selezionare *DefaultLAWorkspace* creata prima.
3. Selezionare **Impostazioni avanzate**.<br><br> ![Impostazioni avanzate di Log Analytics](media/quick-collect-windows-computer/log-analytics-advanced-settings-01.png)<br><br>  
4. Selezionare **Origini connesse** e quindi **Server Windows**.   
5. Il valore a destra di **ID area di lavoro** e **Chiave primaria**. Copiare e incollare entrambi i valori nell'editor predefinito.   

## <a name="install-the-agent-for-windows"></a>Installare l'agente per Windows
Le istruzioni seguenti consentono di installare e configurare l'agente per Log Analytics nel cloud di Azure e di Azure per enti pubblici, installando Microsoft Monitoring Agent nel computer.  

1. Nella pagina **Server Windows** selezionare la versione appropriata da scaricare in **Scarica agente Windows** a seconda dell'architettura del processore del sistema operativo Windows.
2. Eseguire il programma di installazione per installare l'agente nel computer in uso.
2. Nella pagina di **benvenuto** fare clic su **Avanti**.
3. Nella pagina **Condizioni di licenza** leggere la licenza e quindi fare clic su **Accetto**.
4. Nella pagina **Cartella di destinazione** modificare o mantenere la cartella di installazione predefinita e quindi fare clic su **Avanti**.
5. Nella pagina **Opzioni di installazione dell'agente** scegliere di connettere l'agente ad Azure Log Analytics e quindi fare clic su **Avanti**.   
6. Nella pagina **Azure Log Analytics** eseguire le operazioni seguenti:
   1. Incollare **ID area di lavoro** e **Chiave dell'area di lavoro (Chiave primaria)** copiati in precedenza.  Se il computer deve fare riferimento a un'area di lavoro di Log Analytics nel cloud di Azure per enti pubblici, selezionare **Azure Governo degli Stati Uniti** nell'elenco a discesa **Cloud di Azure**.  
   2. Se il computer deve comunicare tramite un server proxy con il servizio Log Analytics, fare clic su **Avanzate** e specificare l'URL e il numero di porta del server proxy.  Se il server proxy richiede l'autenticazione, digitare il nome utente e la password per l'autenticazione nel server proxy, quindi fare clic su **Avanti**.  
7. Fare clic su **Avanti** dopo aver specificato le impostazioni di configurazione necessarie.<br><br> ![incollare ID area di lavoro e chiave primaria](media/quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Nella pagina **Pronto per l'installazione** rivedere le scelte effettuate e quindi fare clic su **Installa**.
9. Nella pagina **Configurazione completata** fare clic su **Fine**.

Al termine, verrà visualizzato **Microsoft Monitoring Agent** nel **Pannello di controllo**. È possibile rivedere la configurazione e verificare che l'agente sia connesso a Log Analytics. Una volta stabilita la connessione, nella scheda **Azure Log Analytics**, l'agente visualizza un messaggio per indicare che: **Microsoft Monitoring Agent ha eseguito la connessione al servizio Microsoft Log Analytics**.<br><br> ![Stato della connessione di MMA a Log Analytics](media/quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Raccogliere dati su eventi e prestazioni
Log Analytics può raccogliere gli eventi dai registri eventi e dai contatori delle prestazioni di Windows specificati per l'analisi a lungo termine e la creazione di report e intervenire quando viene rilevata una particolare condizione.  Seguire questi passaggi per configurare la raccolta di eventi dal registro eventi di Windows e di diversi contatori delle prestazioni comuni con cui iniziare.  

1. Nel portale di Azure fare clic su **Altri servizi** nell'angolo in basso a sinistra. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.
2. Selezionare **Impostazioni avanzate**.<br><br> ![Impostazioni avanzate di Log Analytics](media/quick-collect-windows-computer/log-analytics-advanced-settings-01.png)<br><br> 
3. Selezionare **Dati** e quindi selezionare **Registri eventi di Windows**.  
4. Si aggiunge un registro eventi digitandone il nome.  Digitare **Sistema** e quindi fare clic sul segno **+**.  
5. Nella tabella selezionare i livelli di gravità **Errore** e **Avviso**.   
6. Fare clic su **Salva** nella parte superiore della pagina per salvare la configurazione.
7. Selezionare **Contatori delle prestazioni di Windows** per abilitare la raccolta di contatori delle prestazioni in un computer Windows. 
8. Quando si configurano i contatori delle prestazioni di Windows per la prima volta per una nuova area di lavoro di Log Analytics, è possibile creare rapidamente numerosi contatori comuni. Viene visualizzato l'elenco dei contatori con le caselle di controllo corrispondenti.<br> ![Contatori delle prestazioni di Windows predefiniti selezionati](media/quick-collect-windows-computer/windows-perfcounters-default.png).<br> Fare clic su **Aggiungi i contatori delle prestazioni selezionati**.  Vengono aggiunti e preimpostati con un intervallo di esempio tra le raccolte di dieci secondi.  
9. Fare clic su **Salva** nella parte superiore della pagina per salvare la configurazione.

## <a name="view-data-collected"></a>Visualizzare i dati raccolti
Ora che la raccolta di dati è stata abilitata, verrà eseguito un semplice esempio di ricerca nel log per visualizzare alcuni dati dal computer di destinazione.  

1. Nel portale di Azure, nell'area di lavoro selezionata, fare clic sul riquadro **Ricerca log**.  
2. Nel riquadro Ricerca log digitare `Perf` nel campo della query e quindi premere INVIO o fare clic sul pulsante di ricerca a destra del campo della query.<br><br> ![Esempio di query di ricerca log di Log Analytics](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)<br><br> La query nella figura seguente, ad esempio, ha restituito 735 record relativi alle prestazioni.<br><br> ![Risultato della ricerca log di Log Analytics](media/quick-collect-windows-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessario, è possibile rimuovere l'agente dal computer Windows ed eliminare l'area di lavoro di Log Analytics.  

Per rimuovere l'agente, eseguire la procedura seguente.

1. Aprire il **Pannello di controllo**.
2. Aprire **Programmi e funzionalità**.
3. In **Programmi e funzionalità** selezionare **Microsoft Monitoring Agent** e fare clic su **Disinstalla**.

Per eliminare l'area di lavoro, selezionare l'area di lavoro di Log Analytics creata in precedenza e nella pagina delle risorse fare clic su **Elimina**.<br><br> ![Eliminare la risorsa Log Analytics](media/quick-collect-windows-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Passaggi successivi
Ora che è in corso la raccolta di dati operativi e sulle prestazioni dal computer Linux locale, è possibile iniziare facilmente a esplorare, analizzare e modificare i dati raccolti *gratuitamente*.  

Per informazioni su come visualizzare e analizzare i dati, passare all'esercitazione.   

> [!div class="nextstepaction"]
> [View or analyze data in Log Analytics (Visualizzare o analizzare i dati in Log Analytics)](tutorial-viewdata.md)
