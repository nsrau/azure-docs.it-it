---
title: Analisi dei log per la rete CDN di Azure | Microsoft Docs
description: "Il cliente può abilitare l'analisi dei log per la rete CDN di Azure."
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: v-semcev
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 03ff74ae4e40d3f2279caaf4f73e9b4aac6a2ebb
ms.contentlocale: it-it
ms.lasthandoff: 08/08/2017

---

# <a name="diagnostics-logs-for-azure-cdn"></a>Log di diagnostica per la rete CDN di Azure

Dopo aver abilitato la rete CDN per l'applicazione, è probabile che si voglia monitorare l'uso della rete CDN, controllare l'integrità della distribuzione e risolvere i potenziali problemi. La rete CDN di Azure offre queste funzionalità tramite l'[analisi principale della rete CDN](cdn-analyze-usage-patterns.md) e i [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)

## <a name="cdn-core-analytics"></a>Analisi principale della rete CDN
Gli utenti correnti della rete CDN di Azure con profilo Verizon standard o premium sono già in grado di visualizzare l'analisi principale nel portale supplementare accessibile tramite l'opzione "Gestisci" del portale Azure. 

## <a name="azure-diagnostic-logs"></a>Log di diagnostica di Azure

Con questa nuova funzionalità di Azure è ora possibile visualizzare analisi principali e salvarle in una o più destinazioni, tra cui:

 - Account di archiviazione di Azure
 - Hub eventi di Azure
 - [Repository di OMS Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
 Questa funzionalità è disponibile per tutti gli endpoint della rete CDN che appartengono a profili CDN Verizon (Standard e Premium) e Akamai (Standard).

I log di diagnostica consentono di esportare le metriche di utilizzo di base dall'endpoint di rete CDN in diverse origini, per poterle usare in modo personalizzato. Ad esempio, è possibile eseguire i tipi di esportazione di dati seguenti:

- Esportare i dati nell'archiviazione BLOB, esportare in CSV e generare grafici in excel.
- Esportare i dati in hub eventi e correlarli con dati provenienti da altri servizi di Azure.
- Esportare i dati nell'analisi dei log e visualizzarli nella propria area di lavoro OMS

La figura seguente mostra una tipica visualizzazione di analisi principale della rete CDN.

![portale - Log di diagnostica](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Figura 1 - Visualizzazione di analisi principale della rete CDN*

La procedura dettagliata seguente descrive in modo dettagliato lo schema dei dati dell'analisi principale, i passaggi necessari per abilitare la funzionalità e distribuire i dati in diverse destinazioni e l'uso da tali destinazioni.

## <a name="enable-logging-with-azure-portal"></a>Abilitare la registrazione con il portale di Azure

> [!NOTE]
> I log di diagnostica sono **disattivati** per impostazione predefinita. 

Per abilitare la registrazione con l'analisi principale della rete CDN, eseguire i passaggi indicati di seguito:

Accedere al [portale di Azure](http://portal.azure.com). Se non si è già abilitata per il proprio flusso di lavoro, [abilitare la rete CDN di Azure](cdn-create-new-endpoint.md) prima di continuare.

1. Nel portale spostarsi sul **profilo CDN**.
2. Selezionare un profilo CDN, quindi selezionare l'endpoint della rete CDN per cui si desidera abilitare **Log di diagnostica**.

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Andare al pannello **Log di diagnostica** nella sezione **Monitoraggio**, quindi modificare lo stato su **Attivato**.

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Abilitare la registrazione con Archiviazione di Azure
    
Per usare Archiviazione di Azure per archiviare i log, selezionare **Archivia in un account di archiviazione**, selezionare i giorni di conservazione e fare clic su **CoreAnalytics** in **Log**.

![portale - Log di diagnostica](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png)

*Figura 2 - Registrazione con Archiviazione di Azure*

### <a name="logging-with-oms-log-analytics"></a>Registrazione con OMS Log Analytics

Per usare OMS Log Analytics per archiviare i log, eseguire questi passaggi:

1. Nel pannello **Log di diagnostica** in **Monitoraggio** selezionare **Invia a Log Analytics** nel 

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Configurare la registrazione di Log Analytics facendo clic su Configura. Si verrà reindirizzati a una finestra di dialogo in cui è possibile selezionare un'area di lavoro precedente o crearne una nuova.

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Fare clic su **Crea una nuova area di lavoro**.

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/07_Create-new.png)

4. È quindi necessario selezionare il nome della nuova area di lavoro, la sottoscrizione esistente, un gruppo di risorse (nuovo o esistente), la posizione e il piano tariffario. È possibile aggiungere questa configurazione al dashboard. Fare clic su OK per completare la configurazione.

    È quindi necessario visualizzare l'area di lavoro con i nomi dell'area di lavoro e del gruppo di risorse di OMS. I nomi devono essere univoci e possono contenere solo lettere, numeri e trattini. Gli spazi e i caratteri di sottolineatura non sono supportati. 

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/08_Workspace-resource.png)

5. Viene visualizzato un breve messaggio che indica che l'area di lavoro è stata creata e quindi viene visualizzata di nuovo la schermata di configurazione della registrazione. È possibile confermare il nome dell'area di lavoro di Log Analytics.

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/09_Return-to-logging.png)

    Dopo aver completato la configurazione di Log Analytics, assicurarsi di selezionare anche la casella CoreAnalytics per la registrazione della rete CDN.

6. Se è tutto corretto, fare clic sul pulsante **Salva** nella parte superiore della finestra di dialogo di configurazione.

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/10_Save-me.png)

    Il pulsante **Salva** non è più attivo e il pulsante Sì/No è ora impostato su Sì, ma è blu anziché viola.

7. Se si vuole visualizzare la nuova area di lavoro OMS, passare al dashboard del portale di Azure e fare clic sul nome dell'area di lavoro di Log Analytics. Verrà visualizzata l'area di lavoro. Assicurarsi che l'area di lavoro OMS sia evidenziata a sinistra. Fare clic sul riquadro Portale di OMS per visualizzare l'area di lavoro nel repository di OMS. 

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    Il repository di OMS è ora pronto per registrare dati. Per utilizzare i dati, è necessario usare una [soluzione OMS](#consuming-oms-log-analytics-data), descritta più avanti in questo articolo.

Per altre informazioni sui ritardi dei dati di log, vedere [qui](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Abilitare la registrazione con PowerShell

Di seguito viene riportato un esempio di come abilitare e ottenere log di diagnostica tramite i cmdlet di Azure PowerShell.

###<a name="enabling-diagnostic-logs-in-a-storage-account"></a>Abilitare log di diagnostica in un account di archiviazione

Prima di tutto, accedere e selezionare una sottoscrizione:

    Login-AzureRmAccount 

    Select-AzureSubscription -SubscriptionId 


Per abilitare i log di diagnostica in un account di archiviazione, usare questo comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```
Per abilitare i log di diagnostica in un'area di lavoro OMS, usare questo comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
```



## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Utilizzo di log di diagnostica da Archiviazione di Azure
Questa sezione descrive lo schema dell'analisi principale della rete CDN e il modo in cui questa è organizzata all'interno di un account di archiviazione di Azure e contiene il codice di esempio per scaricare i log in un file CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Uso di Microsoft Azure Storage Explorer
Per poter accedere ai dati di analisi principali dell'account di archiviazione di Azure, è necessario uno strumento per accedere al contenuto di un account di archiviazione. Sebbene siano disponibili sul mercato diversi strumenti, si consiglia l'uso di Microsoft Azure Storage Explorer. È possibile scaricare lo strumento [qui](http://storageexplorer.com/). Dopo aver scaricato e installato il software, configurarlo per usare lo stesso account di archiviazione di Azure impostato come destinazione dei log di diagnostica della rete CDN.

1.  Aprire **Microsoft Azure Storage Explorer**
2.  Individuare l'account di archiviazione
3.  Passare al nodo **"Blob Containers"** in questo account di archiviazione ed espanderlo
4.  Selezionare il contenitore denominato **"insights-logs-coreanalytics"** e fare doppio clic su di esso
5.  Risultati visualizzati nel riquadro di destra a partire dal primo livello, simili a **"resourceId="**. Continuare a fare clic finché non si visualizza il file **PT1H.json**. Per una spiegazione del percorso, vedere la nota di seguito.
6.  Ogni BLOB **PT1H.json** rappresenta i log di analisi per un'ora per un endpoint della rete CDN specifico o per il relativo dominio personalizzato.
7.  Lo schema del contenuto di questo file JSON è descritto nella sezione Schema dei log dell'analisi principale


> [!NOTE]
> **Formato del percorso BLOB**
> 
> I log dell'analisi principale vengono generati ogni ora. Tutti i dati per un'ora sono raccolti e archiviati all'interno di un unico BLOB di Azure come payload JSON. Il percorso di questo BLOB di Azure appare come se fosse presente una struttura gerarchica. Questo è dovuto al fatto che lo strumento Storage Explorer interpreta "/" come un separatore di directory e mostra la gerarchia per comodità. In realtà, l'intero percorso è rappresentato solo dal nome del BLOB. Questo nome del BLOB segue la convenzione di denominazione seguente   
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**Descrizione dei campi:**

|value|description|
|-------|---------|
|ID sottoscrizione    |ID della sottoscrizione di Azure. Questo è in formato Guid.|
|Risorsa |Nome gruppo    Nome del gruppo di risorse cui appartengono le risorse della rete CDN.|
|Nome profilo |Nome del profilo CDN|
|Nome endpoint |Nome dell'endpoint della rete CDN|
|Year|  Rappresentazione a 4 cifre dell'anno, ad esempio 2017|
|Mese| Rappresentazione a 2 cifre del numero del mese. 01=Gennaio... 12=Dicembre|
|Giorno|   Rappresentazione a 2 cifre del giorno del mese|
|PT1H.json| File JSON effettivo in cui vengono archiviati i dati di analisi|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Esportazione dei dati dell'analisi principale in un file CSV

Per semplificare l'accesso all'analisi principale, è disponibile il codice di esempio per uno strumento che consente di scaricare i file JSON in un formato di file normale con valori delimitati da virgole, che può essere usato per creare grafici o altre aggregazioni in tutta semplicità.

Di seguito è illustrato come è possibile usare lo strumento:

1.  Visitare il collegamento github: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.  Scaricare il codice
3.  Seguire le istruzioni per la compilazione e la configurazione
4.  Eseguire lo strumento
5.  Il file CSV risultate mostra i dati di analisi in una semplice gerarchia.

## <a name="consuming-diagnostics-logs-from-an-oms-log-analytics-repository"></a>Utilizzo di log di diagnostica da un repository di OMS Log Analytics
Log Analytics è un servizio in Operations Management Suite (OMS) che monitora gli ambienti cloud e locali per mantenerne la disponibilità e le prestazioni. Raccoglie i dati generati dalle risorse negli ambienti cloud e locali e da altri strumenti di monitoraggio per analizzare più origini. 

Per usare Log Analytics, è necessario [abilitare la registrazione](#enable-logging-with-azure-storage) nel repository di Azure OMS Log Analytics, descritto in precedenza in questo articolo.

### <a name="using-the-oms-repository"></a>Uso del repository di OMS

 Il diagramma seguente mostra l'architettura degli input e output del repository:

![Repository di OMS Log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Figura 3 - Repository di Log Analytics*

È possibile visualizzare i dati in svariati modi tramite soluzioni di gestione. È possibile ottenere soluzioni di gestione da [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

È possibile installare le soluzioni di gestione da Azure Marketplace facendo clic sul collegamento **Get it now** (Scarica adesso) nella parte inferiore di ogni soluzione.

### <a name="adding-an-oms-cdn-management-solution"></a>Aggiunta di una soluzione di gestione della rete CDN di OMS

Seguire questi passaggi per aggiungere una soluzione di gestione:

1.   Se non è già stato fatto, accedere al portale di Azure tramite la sottoscrizione di Azure e passare al dashboard.
    ![Dashboard di Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Nel pannello **Nuovo** in **Marketplace** selezionare **Monitoraggio e gestione**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. Nel pannello **Monitoraggio e gestione** fare clic su **Visualizza tutto**.

    ![Visualizzare tutto](./media/cdn-diagnostics-log/15_See-all.png)

4.  Cercare la rete CDN nella casella di ricerca.

    ![Visualizzare tutto](./media/cdn-diagnostics-log/16_Search-for.png)

5.  Selezionare **Azure CDN Core Analytics** (Analisi principale rete CDN di Azure). 

    ![Visualizzare tutto](./media/cdn-diagnostics-log/17_Core-analytics.png)

6.  Dopo aver fatto clic su **Crea**, verrà chiesto se creare una nuova area di lavoro OMS o se usarne una esistente. 

    ![Visualizzare tutto](./media/cdn-diagnostics-log/18_Adding-solution.png)

7.  Selezionare l'area di lavoro creata prima. Sarà quindi necessario aggiungere un account di automazione.

    ![Visualizzare tutto](./media/cdn-diagnostics-log/19_Add-automation.png)

8. La schermata seguente mostra il modulo dell'account di automazione che è necessario compilare. 

    ![Visualizzare tutto](./media/cdn-diagnostics-log/20_Automation.png)

9. Dopo aver creato l'account di automazione, è possibile aggiungere la soluzione. Selezionare il pulsante **Create** .

    ![Visualizzare tutto](./media/cdn-diagnostics-log/21_Ready.png)

10. La soluzione è stata ora aggiunta all'area di lavoro. Tornare al dashboard del portale di Azure.

    ![Visualizzare tutto](./media/cdn-diagnostics-log/22_Dashboard.png)

    Fare clic sull'area di lavoro di Log Analytics creata per passare all'area di lavoro. 

11. Fare clic sul riquadro **Portale di OMS** per visualizzare la nuova soluzione nel portale di OMS.

    ![Visualizzare tutto](./media/cdn-diagnostics-log/23_workspace.png)

12. Il portale di OMS dovrebbe avere un aspetto simile a quello mostrato nella schermata seguente:

    ![Visualizzare tutto](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Fare clic su uno dei riquadri per esaminare diverse visualizzazioni dei dati.

    ![Visualizzare tutto](./media/cdn-diagnostics-log/25_Interior-view.png)

    È possibile scorrere verso destra o sinistra per visualizzare altri riquadri che rappresentano singole visualizzazioni dei dati. 

    Facendo clic su uno dei riquadri, è possibile visualizzare altri dettagli sui dati.

     ![Visualizzare tutto](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Offerte e piani tariffari

Le offerte e i piani tariffari per le soluzioni di gestione di OMS sono disponibili [qui](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers).

### <a name="customizing-views"></a>Personalizzazione delle visualizzazioni

È possibile personalizzare la visualizzazione dei dati usando **Progettazione viste**. Passare all'area di lavoro di OMS e avviare la progettazione facendo clic sul riquadro **Progettazione viste**.

![Progettazione viste](./media/cdn-diagnostics-log/27_Designer.png)

È possibile trascinare e rilasciare i tipi di grafico dal lato sinistro e immettere i dettagli dei dati da analizzare a destra.

![Progettazione viste](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Ritardi dei dati di log

Ritardi dei dati di log di Verizon | Ritardi dei dati di log di Akamai
--- | ---
I dati dei log di Verizon hanno un ritardo di un'ora e richiedono fino a due ore prima di venire visualizzati dopo il completamento della propagazione degli endpoint. | I dati dei log di Akamai hanno un ritardo di 24 ore e richiedono fino a 2 ore prima di venire visualizzati se sono stati creati più di 24 ore prima. Se i log sono stati creati di recente, possono essere necessarie fino a 25 ore prima di poterli visualizzare.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Tipi di log di diagnostica per l'analisi principale della rete CDN

Attualmente sono disponibili solo i log di analisi principale, contenenti metriche che mostrano le statistiche sulle risposte HTTP e le statistiche relative all'uscita rilevate dai server POP/perimetrali della rete CDN.

### <a name="core-analytics-metrics-details"></a>Dettagli delle metriche dell'analisi principale
La tabella seguente mostra un elenco di metriche disponibili nei log di analisi principale. Non tutte le metriche sono disponibili da tutti i provider, sebbene le differenze siano minime. La tabella seguente indica anche se una metrica specifica è disponibile presso un provider. Si noti che le metriche sono disponibili solo per gli endpoint della rete CDN in cui vi è traffico.


|Metrica                     | Descrizione   | Verizon  | Akamai 
|---------------------------|---------------|---|---|
| RequestCountTotal         |Numero totale di riscontri della richiesta durante questo periodo| Sì  |Sì   |
| RequestCountHttpStatus2xx |Conteggio di tutte le richieste che hanno generato un codice HTTP 2xx (ad esempio 200, 202)              | Sì  |Sì   |
| RequestCountHttpStatus3xx | Conteggio di tutte le richieste che hanno generato un codice HTTP 3xx (ad esempio 300, 302)              | Sì  |Sì   |
| RequestCountHttpStatus4xx |Conteggio di tutte le richieste che hanno generato un codice HTTP 4xx (ad esempio 400, 404)               | Sì   |Sì   |
| RequestCountHttpStatus5xx | Conteggio di tutte le richieste che hanno generato un codice HTTP 5xx (ad esempio 500, 504)              | Sì  |Sì   |
| RequestCountHttpStatusOthers |  Conteggio di tutti gli altri codici HTTP (non inclusi nell'intervallo 2xx-5xx) | Sì  |Sì   |
| RequestCountHttpStatus200 | Conteggio di tutte le richieste che hanno generato una risposta di codice HTTP 200              |No   |Sì   |
| RequestCountHttpStatus206 | Conteggio di tutte le richieste che hanno generato una risposta di codice HTTP 206              |No   |Sì   |
| RequestCountHttpStatus302 | Conteggio di tutte le richieste che hanno generato una risposta di codice HTTP 302              |No   |Sì   |
| RequestCountHttpStatus304 |  Conteggio di tutte le richieste che hanno generato una risposta di codice HTTP 304             |No   |Sì   |
| RequestCountHttpStatus404 | Conteggio di tutte le richieste che hanno generato una risposta di codice HTTP 404              |No   |Sì   |
| RequestCountCacheHit |Conteggio di tutte le richieste che hanno generato un riscontro nella cache. Questo significa che l'asset è stato servito direttamente dal POP al client.               | Sì  |No   |
| RequestCountCacheMiss | Conteggio di tutte le richieste che hanno generato un mancato riscontro nella cache. Questo significa che l'asset non è stato trovato nel POP più vicino al client e pertanto è stato recuperato dall'origine.              |Sì   | No  |
| RequestCountCacheNoCache | Conteggio di tutte le richieste a un asset a cui è stata impedita la memorizzazione nella cache a causa di una configurazione dell'utente sull'edge.              |Sì   | No  |
| RequestCountCacheUncacheable | Conteggio di tute le richieste su asset a cui è stata impedita la memorizzazione nella cache dalle intestazioni Cache-Control ed Expires dell'asset che indicano che non deve essere memorizzato nella cache in un POP o da un client HTTP                |Sì   |No   |
| RequestCountCacheOthers | Conteggio di tutte le richieste con stato della cache non coperto dalle metriche precedenti.              |Sì   | No  |
| EgressTotal | Trasferimento di dati in uscita in GB              |Sì   |Sì   |
| EgressHttpStatus2xx | Trasferimento di dati in uscita* per risposte con codici di stato HTTP 2xx in GB            |Sì   |No   |
| EgressHttpStatus3xx | Trasferimento di dati in uscita per risposte con codici di stato HTTP 3xx in GB              |Sì   |No   |
| EgressHttpStatus4xx | Trasferimento di dati in uscita per risposte con codici di stato HTTP 4xx in GB               |Sì   | No  |
| EgressHttpStatus5xx | Trasferimento di dati in uscita per risposte con codici di stato HTTP 5xx in GB               |Sì   |  No |
| EgressHttpStatusOthers | Trasferimento di dati in uscita per risposte con altri codici di stato HTTP in GB                |Sì   |No   |
| EgressCacheHit |  Trasferimento di dati in uscita per risposte recapitate direttamente dalla cache CDN su POP/Edge della rete CDN  |Sì   |  No |
| EgressCacheMiss | Trasferimento di dati in uscita per risposte non trovate nel server POP più vicino e recuperate dal server di origine              |Sì   |  No |
| EgressCacheNoCache | Trasferimento di dati in uscita per asset a cui è stata impedita la memorizzazione nella cache a causa di una configurazione dell'utente sull'edge.                |Sì   |No   |
| EgressCacheUncacheable | Trasferimento di dati in uscita per asset a cui è stata impedita la memorizzazione nella cache dalle intestazioni Cache-Control ed Expires dell'asset che indicano che non deve essere memorizzato nella cache in un POP o da un client HTTP                    |Sì   | No  |
| EgressCacheOthers |  Trasferimento di dati in uscita per altri scenari di cache.             |Sì   | No  |

*Il trasferimento di dati in uscita si riferisce al traffico recapitato da server POP della rete CDN al client.


### <a name="schema-of-the-core-analytics-logs"></a>Schema dei log dell'analisi principale 

Tutti i log vengono archiviati in formato JSON e per ogni voce sono presenti campi stringa che seguono lo schema seguente:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Dove 'time' rappresenta l'ora di inizio del limite di ore per cui vengono restituite le statistiche. Quando una metrica non è supportata da un provider di rete CDN, anziché un valore double o integer, è specificato un valore null. Questo valore null indica l'assenza di una metrica e questo comportamento è diverso da un valore 0. Notare inoltre che è presente un insieme di metriche per ogni dominio configurato sull'endpoint.

Di seguito sono riportate proprietà di esempio:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Risorse aggiuntive

* [Log di diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Analisi principale tramite il portale supplementare della rete CDN di Azure](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure OMS Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-overview)
* [API REST di Azure Log Analytics](https://docs.microsoft.com/en-us/rest/api/loganalytics)








