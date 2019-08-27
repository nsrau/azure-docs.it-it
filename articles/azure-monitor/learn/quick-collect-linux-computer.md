---
title: Configurare l'agente di Azure Log Analytics per computer Linux in ambienti ibridi | Microsoft Docs
description: Informazioni su come distribuire l'agente di Log Analytics per Linux in esecuzione in computer all'esterno di Azure e abilitare la raccolta di dati con Log Analytics.
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
ms.date: 06/14/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 535b96a2bb86f1996e6ff0aba19339b55a18ee50
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515863"
---
# <a name="configure-log-analytics-agent-for-linux-computers-in-a-hybrid-environment"></a>Configurare l'agente di Log Analytics per computer Linux in un ambiente ibrido
[Azure Log Analytics](../platform/agent-windows.md) può raccogliere dati direttamente dai computer Linux fisici o virtuali nel data center o in altri ambienti cloud in un unico repository per l'esecuzione di analisi dettagliate e della correlazione. Questo avvio rapido illustra come configurare e raccogliere dati dal computer Linux in pochi semplici passaggi.  Per le macchine virtuali Linux di Azure vedere l'argomento seguente [Raccogliere dati sulle macchine virtuali di Azure](quick-collect-azurevm.md).  

Prima di procedere, è necessario rivedere l'articolo con la [panoramica](../platform/log-analytics-agent.md) dell'agente di analisi dei log per comprendere quali configurazioni sono supportate, quali [sistemi operativi Linux sono supportati](../platform/log-analytics-agent.md#supported-linux-operating-systems) e la [ configurazione del firewall di rete](../platform/log-analytics-agent.md#network-firewall-requirements). 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Creare un'area di lavoro
1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse immettere **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.

    ![Portale di Azure](media/quick-collect-linux-computer/azure-portal-01.png) 

2. Selezionare **Crea** e quindi scegliere le opzioni per gli elementi seguenti:

   * Specificare un nome per la nuova **area di lavoro Log Analytics**, ad esempio *DefaultLAWorkspace*. Le aree di lavoro OMS sono ora denominate aree di lavoro di Log Analytics.   
   * Selezionare una **sottoscrizione** a cui collegarsi. Se la sottoscrizione selezionata per impostazione predefinita non è appropriata, è possibile sceglierne una dall'elenco a discesa.
   * Per **Gruppo di risorse**, selezionare un gruppo di risorse esistente contenente una o più macchine virtuali di Azure.  
   * Selezionare la **località** in cui sono distribuite le VM.  Per altre informazioni, vedere le [are in cui è disponibile Log Analytics](https://azure.microsoft.com/regions/services/).  
   * Se si sta creando un'area di lavoro in una nuova sottoscrizione creata dopo il 2 aprile 2018, verrà automaticamente usato il piano di determinazione dei prezzi *Per GB* e non sarà disponibile l'opzione che consente di selezionare un piano tariffario.  Se si sta creando un'area di lavoro per una sottoscrizione esistente creata prima del 2 aprile o per una sottoscrizione collegata a un Contratto Enterprise esistente, selezionare il piano tariffario preferito.  Per altre informazioni sui piani specifici, vedere [Dettagli prezzi di Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Creare il pannello della risorsa Log Analytics](media/quick-collect-linux-computer/create-loganalytics-workspace-02.png)<br>  

3. Dopo aver specificato le informazioni necessarie nel riquadro **Area di lavoro di Log Analytics**, selezionare **OK**.  

Per tenere traccia dello stato di avanzamento della verifica delle informazioni e della creazione dell'area di lavoro, è possibile usare la voce **Notifiche** nel menu. 

## <a name="obtain-workspace-id-and-key"></a>Ottenere l'ID e la chiave dell'area di lavoro
Prima di installare l'agente di Log Analytics per Linux, sono necessari l'ID e la chiave dell'area di lavoro per l'area di lavoro Log Analytics.  Queste informazioni sono richieste dallo script del wrapper agente per configurare correttamente l'agente e verificare che possa comunicare correttamente con Log Analytics.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. Nel portale di Azure selezionare **Tutti i servizi** nell'angolo superiore sinistro. Nell'elenco delle risorse immettere **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.
2. Nell'elenco di aree di lavoro di Log Analytics selezionare *DefaultLAWorkspace* creata prima.
3. Selezionare **Impostazioni avanzate**.

    ![Impostazioni avanzate di Log Analytics](media/quick-collect-linux-computer/log-analytics-advanced-settings-01.png) 
 
4. Selezionare **Origini connesse**, quindi **Server Linux**.   
5. Il valore a destra di **ID area di lavoro** e **Chiave primaria**. Copiare e incollare entrambi i valori nell'editor predefinito.   

## <a name="install-the-agent-for-linux"></a>Installare l'agente per Linux
I passaggi seguenti configurano l'installazione dell'agente per Log Analytics in Azure e il cloud di Microsoft Azure per enti pubblici.  

>[!NOTE]
>L'agente di Log Analytics per Linux non può essere configurato per inviare report a più di un'area di lavoro Log Analytics.  

Se il computer Linux deve comunicare tramite un server proxy con Log Analytics, è possibile configurare la configurazione del proxy dalla riga di comando includendo `-p [protocol://][user:password@]proxyhost[:port]`.  La proprietà *proxyhost* accetta un nome di dominio completo o l'indirizzo IP del server proxy. 

Ad esempio: `https://user01:password@proxy01.contoso.com:30443`

1. Per configurare il computer Linux per connettersi a Log Analytics, eseguire il comando seguente fornendo l'ID e la chiave primaria dell'area di lavoro copiati in precedenza. Il comando seguente scarica l'agente, convalida il relativo checksum e ne esegue l'installazione. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Il comando seguente include il parametro `-p` del proxy e la sintassi di esempio.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Per configurare il computer Linux per connettersi a Log Analytics nel cloud di Azure per enti pubblici, eseguire il comando seguente fornendo l'ID e la chiave primaria dell'area di lavoro copiati in precedenza. Il comando seguente scarica l'agente, convalida il relativo checksum e ne esegue l'installazione. 

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
2. Si aggiunge un registro eventi digitandone il nome. Immettere **Syslog** e quindi selezionare il segno più **+** .  
3. Nella tabella deselezionare i livelli di gravità **Informativo**, **Avviso** e **Debug**. 
4. Selezionare **Salva** nella parte superiore della pagina per salvare la configurazione.
5. Selezionare **Linux Performance Data** (Dati di prestazione di Linux) per abilitare la raccolta di contatori delle prestazioni in un computer Linux. 
6. Quando si configurano i contatori delle prestazioni di Linux per la prima volta per una nuova area di lavoro Log Analytics, è possibile creare rapidamente numerosi contatori comuni. Verranno visualizzati in un elenco con una casella di controllo accanto a ognuno. 

    ![Contatori delle prestazioni di Windows predefiniti selezionati](media/quick-collect-linux-computer/linux-perfcounters-default.png)
    
    Fare clic su **Aggiungi i contatori delle prestazioni selezionati**. Vengono aggiunti e preimpostati con un intervallo di esempio tra le raccolte di dieci secondi.

7. Selezionare **Salva** nella parte superiore della pagina per salvare la configurazione.

## <a name="view-data-collected"></a>Visualizzare i dati raccolti
Ora che la raccolta di dati è stata abilitata, verrà eseguito un semplice esempio di ricerca nel log per visualizzare alcuni dati dal computer di destinazione.  

1. Nel portale di Azure passare a Log Analytics e selezionare l'area di lavoro creata prima.
2. Selezionare il riquadro **Ricerca log**, nel riquadro Ricerca log immettere `Perf` nel campo della query e quindi premere INVIO o selezionare il pulsante di ricerca a destra del campo della query.

    ![Esempio di query di ricerca log di Log Analytics](media/quick-collect-linux-computer/log-analytics-portal-queryexample.png)

    La query nella figura seguente, ad esempio, ha restituito 735 record relativi alle prestazioni.

    ![Risultato della ricerca log di Log Analytics](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessario, è possibile rimuovere l'agente dal computer Linux ed eliminare l'area di lavoro Log Analytics.  

Per rimuovere l'agente, eseguire il comando seguente nel computer Linux. L'argomento *--purge* rimuove completamente l'agente e la rispettiva configurazione.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Per eliminare l'area di lavoro, selezionare l'area di lavoro Log Analytics creata prima e quindi **Elimina** nella pagina delle risorse.

![Eliminare la risorsa Log Analytics](media/quick-collect-linux-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Passaggi successivi
Ora che è in corso la raccolta di dati operativi e sulle prestazioni dal computer Linux locale, è possibile iniziare facilmente a esplorare, analizzare e modificare i dati raccolti *gratuitamente*.  

Per informazioni su come visualizzare e analizzare i dati, passare all'esercitazione.   

> [!div class="nextstepaction"]
> [View or analyze data in Log Analytics (Visualizzare o analizzare i dati in Log Analytics)](../../azure-monitor/learn/tutorial-viewdata.md)
