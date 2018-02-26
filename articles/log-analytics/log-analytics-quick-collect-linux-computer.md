---
title: Raccogliere dati dai computer Linux locali con Azure Log Analytics | Microsoft Docs
description: Informazioni su come distribuire l'agente Log Analytics per Linux e abilitare la raccolta di dati da tale sistema operativo con Log Analytics.
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/11/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: bc7c7ea1a01ad784ae53090f1ae0edb042b4f07f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="collect-data-from-linux-computer-hosted-in-your-environment"></a>Raccogliere dati dal computer Linux ospitato nell'ambiente in uso
[Azure Log Analytics](log-analytics-overview.md) può raccogliere i dati direttamente dal computer fisico o macchina virtuale Linux e da altre risorse nell'ambiente in uso in un unico repository per procedere a analisi dettagliate e alla correlazione.  Questo avvio rapido illustra come configurare e raccogliere dati dal computer Linux in pochi semplici passaggi.  Per le macchine virtuali Linux di Azure vedere l'argomento seguente [Raccogliere dati sulle macchine virtuali di Azure](log-analytics-quick-collect-azurevm.md).  

Per informazioni sui requisiti di rete e di sistema per la distribuzione dell'agente Linux, vedere i [prerequisiti per il sistema operativo Linux](log-analytics-concept-hybrid.md#prerequisites).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="log-in-to-azure-portal"></a>Accedere al portale di Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Creare un'area di lavoro
1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.<br><br> ![Azure portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. Fare clic su **Crea** e quindi selezionare le opzioni per gli elementi seguenti:

  * Specificare un nome per la nuova **area di lavoro di OMS**, ad esempio *DefaultLAWorkspace*. 
  * Selezionare una **sottoscrizione** a cui collegarsi. Se la sottoscrizione selezionata per impostazione predefinita non è appropriata, è possibile sceglierne una dall'elenco a discesa.
  * Per **Gruppo di risorse**, selezionare un gruppo di risorse esistente contenente una o più macchine virtuali di Azure.  
  * Selezionare la **località** in cui sono distribuite le VM.  Per altre informazioni, vedere le [are in cui è disponibile Log Analytics](https://azure.microsoft.com/regions/services/).
  * È possibile scegliere tra tre diversi **piani tariffari** in Log Analytics, ma per questa guida introduttiva si selezionerà il piano **Gratuito**.  Per altre informazioni sui piani specifici, vedere [Dettagli prezzi di Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](./media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. Dopo aver specificato le informazioni necessarie nel riquadro **Area di lavoro di OMS**, fare clic su **OK**.  

Per tenere traccia dello stato di avanzamento della verifica delle informazioni e della creazione dell'area di lavoro, è possibile usare la voce **Notifiche** nel menu. 

## <a name="obtain-workspace-id-and-key"></a>Ottenere l'ID e la chiave dell'area di lavoro
Prima di installare l'agente OMS per Linux, sono necessari l'ID e la chiave dell'area di lavoro per l'area di lavoro di Log Analytics.  Queste informazioni sono richieste dallo script del wrapper agente per configurare correttamente l'agente e verificare che possa comunicare correttamente con Log Analytics.  

1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.
2. Nell'elenco di aree di lavoro di Log Analytics selezionare *DefaultLAWorkspace* creata prima.
3. Selezionare **Impostazioni avanzate**.<br><br> ![Impostazioni avanzate di Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Selezionare **Origini connesse**, quindi **Server Linux**.   
5. Il valore a destra di **ID area di lavoro** e **Chiave primaria**. Copiare e incollare entrambi i valori nell'editor predefinito.   

## <a name="install-the-agent-for-linux"></a>Installare l'agente per Linux
I passaggi seguenti configurano l'installazione dell'agente per Log Analytics in Azure e il cloud di Microsoft Azure per enti pubblici.  

>[!NOTE]
>L'agente OMS per Linux non può essere configurato per inviare report a più di un'area di lavoro di Log Analytics.  

Se il computer Linux deve comunicare tramite un server proxy con Log Analytics, è possibile configurare la configurazione del proxy dalla riga di comando includendo `-p [protocol://][user:password@]proxyhost[:port]`.  La proprietà *proxyhost* accetta un nome di dominio completo o l'indirizzo IP del server proxy. 

Ad esempio: `https://user01:password@proxy01.contoso.com:30443`

1. Per configurare il computer Linux per connettersi a Log Analytics, eseguire il comando seguente fornendo l'ID e la chiave primaria dell'area di lavoro copiati in precedenza.  Il comando seguente scarica l'agente, convalida il relativo checksum e ne esegue l'installazione. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Il comando seguente include il parametro `-p` del proxy e la sintassi di esempio.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Per configurare il computer Linux per connettersi a Log Analytics nel cloud di Azure per enti pubblici, eseguire il comando seguente fornendo l'ID e la chiave primaria dell'area di lavoro copiati in precedenza.  Il comando seguente scarica l'agente, convalida il relativo checksum e ne esegue l'installazione. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Il comando seguente include il parametro `-p` del proxy e la sintassi di esempio.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Riavviare l'agente eseguendo il comando seguente: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Raccogliere dati su eventi e prestazioni
Log Analytics può raccogliere gli eventi da Syslog Linux e dai contatori delle prestazioni specificati per l'analisi a lungo termine e la creazione di report e intervenire quando viene rilevata una particolare condizione.  Seguire questi passaggi per configurare la raccolta di eventi da Syslog Linux e di diversi comuni contatori delle prestazioni con cui iniziare.  

1. Selezionare **Syslog**.  
2. Si aggiunge un registro eventi digitandone il nome.  Digitare **Syslog** e quindi fare clic sul segno **+**.  
3. Nella tabella deselezionare i livelli di gravità **Informativo**, **Avviso** e **Debug**. 
4. Fare clic su **Salva** nella parte superiore della pagina per salvare la configurazione.
5. Selezionare **Linux Performance Data** (Dati di prestazione di Linux) per abilitare la raccolta di contatori delle prestazioni in un computer Windows. 
6. Quando si configurano i contatori delle prestazioni di Linux per la prima volta per una nuova area di lavoro di Log Analytics, è possibile creare rapidamente numerosi contatori comuni. Viene visualizzato l'elenco dei contatori con le caselle di controllo corrispondenti.<br><br> ![Contatori delle prestazioni di Windows predefiniti selezionati](media/log-analytics-quick-collect-azurevm/linux-perfcounters-default.png)<br> Fare clic su **Aggiungi i contatori delle prestazioni selezionati**.  Vengono aggiunti e preimpostati con un intervallo di esempio tra le raccolte di dieci secondi.  
7. Fare clic su **Salva** nella parte superiore della pagina per salvare la configurazione.

## <a name="view-data-collected"></a>Visualizzare i dati raccolti
Ora che la raccolta di dati è stata abilitata, verrà eseguito un semplice esempio di ricerca nel log per visualizzare alcuni dati dal computer di destinazione.  

1. Nel portale di Azure passare a Log Analytics e selezionare l'area di lavoro creata prima.
2. Fare clic sul riquadro **Ricerca log** e nel riquadro Ricerca log digitare `Perf` nel campo della query e quindi premere INVIO o fare clic sul pulsante della ricerca a destra del campo della query.<br><br> ![Esempio di query di ricerca log di Log Analytics](media/log-analytics-quick-collect-linux-computer/log-analytics-portal-queryexample.png)<br><br> La query nella figura seguente, ad esempio, ha restituito 735 record relativi alle prestazioni.<br><br> ![Risultato della ricerca log di Log Analytics](media/log-analytics-quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessario, è possibile rimuovere l'agente dal computer Linux ed eliminare l'area di lavoro di Log Analytics.  

Per rimuovere l'agente, eseguire il comando seguente nel computer Linux.  L'argomento *--purge* rimuove completamente l'agente e la rispettiva configurazione.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Per eliminare l'area di lavoro, selezionare l'area di lavoro di Log Analytics creata in precedenza e nella pagina delle risorse fare clic su **Elimina**.<br><br> ![Eliminare la risorsa Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Passaggi successivi
Ora che è in corso la raccolta di dati operativi e sulle prestazioni dal computer Linux locale, è possibile iniziare facilmente a esplorare, analizzare e modificare i dati raccolti *gratuitamente*.  

Per informazioni su come visualizzare e analizzare i dati, passare all'esercitazione.   

> [!div class="nextstepaction"]
> [View or analyze data in Log Analytics (Visualizzare o analizzare i dati in Log Analytics)](log-analytics-tutorial-viewdata.md)
