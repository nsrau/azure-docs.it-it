---
title: Log di diagnostica di Azure | Microsoft Docs
description: Il cliente può abilitare l'analisi dei log per la rete CDN di Azure.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: v-deasim
ms.openlocfilehash: 98a7fc5c4607115811e17a7cf6acd4e867663833
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261305"
---
# <a name="azure-diagnostic-logs"></a>Log di diagnostica di Azure

Con i log di diagnostica di Azure è possibile visualizzare le principali analisi e salvarle in una o più destinazioni, tra cui:

 - Account di archiviazione di Azure
 - Hub eventi di Azure
 - [Area di lavoro di Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Questa funzionalità è disponibile negli endpoint di rete CDN per tutti i piani tariffari. 

I log di diagnostica di Azure consentono di esportare le metriche di utilizzo di base dall'endpoint CDN in diverse origini, per poterle usare in modo personalizzato. Ad esempio, è possibile eseguire i tipi di esportazione di dati seguenti:

- Esportare i dati in archivi BLOB, esportare in file CSV e generare grafici in Excel.
- Esportare i dati in Hub eventi e correlarli con dati provenienti da altri servizi di Azure.
- Esportare i dati in Log Analytics e visualizzarli nella propria area di lavoro di Log Analytics

La figura seguente mostra una tipica visualizzazione dei dati di analisi principale della rete CDN.

![portale - Log di diagnostica](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Figura 1 - Visualizzazione di analisi principale della rete CDN*

Per altre informazioni sui log di diagnostica, vedere [Log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-logging-with-the-azure-portal"></a>Abilitare la registrazione con il portale di Azure

Per abilitare la registrazione con l'analisi principale della rete CDN, seguire questi passaggi:

Accedere al [portale di Azure](http://portal.azure.com). Se la rete CDN non è già stata abilitata per il flusso di lavoro [creare un profilo e un endpoint per la rete CDN di Azure](cdn-create-new-endpoint.md) prima di continuare.

1. Nel portale di Azure passare al **profilo della rete CDN**.

2. Nel portale di Azure cercare un profilo di rete CDN o selezionarne uno dal dashboard. Selezionare quindi un endpoint della rete CDN per cui si vogliono abilitare i log di diagnostica.

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Selezionare **Log di diagnostica** nella sezione Monitoraggio.

   Verrà visualizzata la pagina **Log di diagnostica**.

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Abilitare la registrazione con Archiviazione di Azure

Per usare un account di archiviazione per archiviare i log, seguire questa procedura:
    
1. In **Nome** immettere un nome per le impostazioni dei log di diagnostica.
 
2. Selezionare **Archivia in un account di archiviazione** e quindi selezionare **CoreAnalytics**. 

2. In **Conservazione (giorni)** scegliere il numero di giorni di conservazione. Se il valore di conservazione è zero giorni, i log vengono conservati all'infinito. 

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Selezionare **Account di archiviazione**.

    Verrà visualizzata la pagina **Selezionare un account di archiviazione**.

4. Selezionare un account di archiviazione nell'elenco a discesa e quindi selezionare **OK**.

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Dopo aver completato le impostazioni dei log di diagnostica, selezionare **Salva**.

### <a name="logging-with-log-analytics"></a>Registrazione con Log Analytics

Per usare Log Analytics per archiviare i log, seguire questa procedura:

1. Nella pagina **Log di diagnostica** selezionare **Invia a Log Analytics**. 

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Fare clic su **Configura** per configurare la registrazione di Log Analytics. 

   Verrà visualizzata la pagina **Aree di lavoro OMS**.

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Selezionare **Crea una nuova area di lavoro**.

    Verrà visualizzata la pagina **Area di lavoro OMS**.

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/07_Create-new.png)

4. In **Area di lavoro OMS** immettere un nome per l'area di lavoro OMS. Il nome dell'area di lavoro OMS deve essere univoco e contenere solo lettere, numeri e segni meno. Non sono consentiti spazi e caratteri di sottolineatura. 

5. In **Sottoscrizione** selezionare una sottoscrizione esistente nell'elenco a discesa. 

6. In **Gruppo di risorse** creare un nuovo gruppo di risorse o selezionarne uno esistente.

7. In **Percorso** selezionare un percorso nell'elenco.

8. Selezionare **Aggiungi al dashboard** se si vuole salvare la configurazione del log nel dashboard. 

9. Selezionare **OK** per completare la configurazione.

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/08_Workspace-resource.png)

10. Dopo aver creato l'area di lavoro si ritorna alla pagina **Log di diagnostica**. Confermare il nome della nuova area di lavoro di Log Analytics.

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Selezionare **CoreAnalytics** e quindi selezionare **Salva**.

12. Per visualizzare la nuova area di lavoro di Log Analytics, selezionare **Analisi principale** dalla pagina dell'endpoint della rete CDN.

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    L'area di lavoro di Log Analytics è ora pronta per la registrazione dei dati. Per utilizzare i dati, è necessario usare una [soluzione Log Analytics](#consuming-diagnostics-logs-from-a-log-analytics-workspace), descritta più avanti in questo articolo.

Per altre informazioni sui ritardi dei dati di log, vedere [Ritardi dei dati di log](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Abilitare la registrazione con PowerShell

L'esempio seguente mostra come abilitare i log di diagnostica tramite i cmdlet di Azure PowerShell.

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Abilitazione dei log di diagnostica in un account di archiviazione

1. Eseguire l'accesso e selezionare una sottoscrizione:

    Connect-AzureRmAccount 

    Selezionare -AzureSubscription -SubscriptionId 

2. Per abilitare i log di diagnostica in un account di archiviazione, immettere questo comando:

    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Per abilitare i log di diagnostica in un'area di lavoro di Log Analytics, immettere questo comando:

    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Utilizzo di log di diagnostica da Archiviazione di Azure
Questa sezione descrive lo schema dell'analisi principale della rete CDN e il modo in cui questa è organizzata all'interno di un account di archiviazione di Azure e contiene il codice di esempio per scaricare i log in un file CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Uso di Microsoft Azure Storage Explorer
Prima di poter accedere ai dati dell'analisi principale dell'account di archiviazione di Azure, è necessario uno strumento per accedere al contenuto in un account di archiviazione. Sebbene siano disponibili sul mercato diversi strumenti, si consiglia l'uso di Microsoft Azure Storage Explorer. Per scaricare lo strumento, vedere [Azure Storage Explorer](http://storageexplorer.com/). Dopo aver scaricato e installato il software, configurarlo per usare lo stesso account di archiviazione di Azure impostato come destinazione dei log di diagnostica della rete CDN.

1.  Aprire **Microsoft Azure Storage Explorer**
2.  Individuare l'account di archiviazione
3.  Espandere il nodo **Contenitori BLOB** in questo account di archiviazione.
4.  Selezionare il contenitore denominato *insights-logs-coreanalytics*.
5.  I risultati vengono visualizzati nel riquadro di destra, a partire dal primo livello, come *"resourceId="*. Continuare selezionando ogni livello fino a quando non si trova il file *PT1H.json*. Vedere la nota seguente relativa al *formato del percorso BLOB* per una spiegazione del percorso.
6.  Il file *PT1H.json* di ogni BLOB rappresenta i log di analisi per un'ora per un endpoint della rete CDN specifico o per il relativo dominio personalizzato.
7.  Lo schema del contenuto di questo file JSON è descritto nella sezione dello schema dei log dell'analisi principale.


> [!NOTE]
> **Formato del percorso BLOB**
> 
> I log dell'analisi principale vengono generati ogni ora e i dati vengono raccolti e archiviati all'interno di un singolo BLOB di Azure come payload JSON. Dato che lo strumento Storage Explorer interpreta '/' come separatore di directory e mostra la gerarchia, il percorso del BLOB di Azure viene visualizzato come se esistesse una struttura gerarchica e rappresenta il nome del BLOB. Il nome del BLOB segue la convenzione di denominazione seguente: 
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**Descrizione dei campi:**

|Valore|DESCRIZIONE|
|-------|---------|
|ID sottoscrizione    |ID della sottoscrizione di Azure nel formato GUID.|
|Nome gruppo di risorse |Nome del gruppo di risorse cui appartengono le risorse della rete CDN.|
|Nome profilo |Nome del profilo CDN|
|Nome endpoint |Nome dell'endpoint della rete CDN|
|Year|  Rappresentazione a quattro cifre dell'anno, ad esempio 2017|
|Mese| Rappresentazione a due cifre del numero del mese. 01=Gennaio... 12=Dicembre|
|Giorno|   Rappresentazione a due cifre del giorno del mese|
|PT1H.json| File JSON effettivo in cui vengono archiviati i dati di analisi|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Esportazione dei dati dell'analisi principale in un file CSV

Per rendere più semplice l'accesso all'analisi principale, viene fornito il codice di esempio per uno strumento. Questo strumento consente di scaricare i file JSON in un formato di file normale con valori delimitati da virgole, che può essere usato per creare grafici o altre aggregazioni.

Di seguito è illustrato come è possibile usare lo strumento:

1.  Visitare il collegamento di github: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Scaricare il codice.
3.  Seguire le istruzioni per la compilazione e la configurazione.
4.  Eseguire lo strumento.
5.  Il file CSV risultate mostra i dati di analisi in una semplice gerarchia.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Utilizzo dei log di diagnostica da un'area di lavoro di Log Analytics
Log Analytics è un servizio di Azure che monitora gli ambienti cloud e locali per mantenerne la disponibilità e le prestazioni. Raccoglie i dati generati dalle risorse negli ambienti cloud e locali e da altri strumenti di monitoraggio per analizzare più origini. 

Per usare Log Analytics, è necessario [abilitare la registrazione](#enable-logging-with-azure-storage) nell'area di lavoro di Azure Log Analytics, descritta in precedenza in questo articolo.

### <a name="using-the-log-analytics-workspace"></a>Uso dell'area di lavoro di Log Analytics

 Il diagramma seguente mostra l'architettura degli input e output del repository:

![Area di lavoro di Log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Figura 3 - Repository di Log Analytics*

È possibile visualizzare i dati in svariati modi tramite soluzioni di gestione. È possibile ottenere soluzioni di gestione da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

È possibile installare le soluzioni di gestione da Azure Marketplace selezionando il collegamento **Get it now** (Scarica adesso) nella parte inferiore di ogni soluzione.

### <a name="add-a-log-analytics-cdn-management-solution"></a>Aggiungere una soluzione di gestione della rete CDN di Log Analytics

Seguire questa procedura per aggiungere una soluzione di gestione di Log Analytics:

1.   Accedere al portale di Azure tramite la sottoscrizione di Azure e passare al dashboard.
    ![Dashboard di Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Nella pagina **Nuovo**, in **Marketplace**, selezionare **Monitoraggio e gestione**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. Nella pagina **Monitoraggio e gestione** selezionare **Visualizza tutto**.

    ![Visualizzare tutto](./media/cdn-diagnostics-log/15_See-all.png)

4. Cercare la rete CDN nella casella di ricerca.

    ![Visualizzare tutto](./media/cdn-diagnostics-log/16_Search-for.png)

5. Selezionare **Azure CDN Core Analytics** (Analisi principale rete CDN di Azure). 

    ![Visualizzare tutto](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Dopo aver selezionato **Crea**, verrà richiesto se creare una nuova area di lavoro di Log Analytics o usarne una esistente. 

    ![Visualizzare tutto](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Selezionare l'area di lavoro creata prima. Sarà quindi necessario aggiungere un account di automazione.

    ![Visualizzare tutto](./media/cdn-diagnostics-log/19_Add-automation.png)

8. La schermata seguente mostra il modulo dell'account di automazione che è necessario compilare. 

    ![Visualizzare tutto](./media/cdn-diagnostics-log/20_Automation.png)

9. Dopo aver creato l'account di automazione, è possibile aggiungere la soluzione. Selezionare il pulsante **Crea**.

    ![Visualizzare tutto](./media/cdn-diagnostics-log/21_Ready.png)

10. La soluzione è stata ora aggiunta all'area di lavoro. Tornare al dashboard del portale di Azure.

    ![Visualizzare tutto](./media/cdn-diagnostics-log/22_Dashboard.png)

    Fare clic sull'area di lavoro di Log Analytics creata per passare all'area di lavoro. 

11. Selezionare il riquadro **Portale di OMS** per visualizzare la nuova soluzione.

    ![Visualizzare tutto](./media/cdn-diagnostics-log/23_workspace.png)

12. Il portale dovrebbe avere un aspetto simile a quello mostrato nella schermata seguente:

    ![Visualizzare tutto](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Selezionare uno dei riquadri per esaminare diverse visualizzazioni dei dati.

    ![Visualizzare tutto](./media/cdn-diagnostics-log/25_Interior-view.png)

    È possibile scorrere verso destra o sinistra per visualizzare altri riquadri che rappresentano singole visualizzazioni dei dati. 

    Selezionare uno dei riquadri per visualizzare altri dettagli sui dati.

     ![Visualizzare tutto](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Offerte e piani tariffari

Le offerte e i piani tariffari per le soluzioni di gestione sono disponibili [qui](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers).

### <a name="customizing-views"></a>Personalizzazione delle visualizzazioni

È possibile personalizzare la visualizzazione dei dati usando **Progettazione viste**. Per iniziare con la progettazione, passare all'area di lavoro di Log Analytics e fare clic sul riquadro **Progettazione viste**.

![Progettazione viste](./media/cdn-diagnostics-log/27_Designer.png)

Trascinare e rilasciare i tipi di grafico e immettere i dettagli dei dati da analizzare.

![Progettazione viste](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Ritardi dei dati di log

La tabella seguente mostra i ritardi dei dati di log per la **rete CDN Standard di Azure con tecnologia Microsoft**, la **rete CDN Standard di Azure con tecnologia Akamai** e la **rete CDN Standard/Premium di Azure con tecnologia Verizon**.

Ritardi dei dati di log di Microsoft | Ritardi dei dati di log di Verizon | Ritardi dei dati di log di Akamai
--- | --- | ---
Ritardato di 1 ora. | Ritardato di 1 ora e può richiedere fino a 2 ore prima di essere visualizzati dopo il completamento della propagazione dell'endpoint. | Ritardati di 24 ore e richiedono fino a 2 ore prima di venire visualizzati se sono stati creati più di 24 ore prima. Se i log sono stati creati di recente, possono essere necessarie fino a 25 ore prima di poterli visualizzare.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Tipi di log di diagnostica per l'analisi principale della rete CDN

Microsoft offre attualmente solo i log di analisi principale, contenenti metriche che mostrano le statistiche sulle risposte HTTP e le statistiche relative all'uscita rilevate dai server POP/perimetrali della rete CDN.

### <a name="core-analytics-metrics-details"></a>Dettagli delle metriche dell'analisi principale
La tabella seguente mostra un elenco di metriche disponibili nei log di analisi principale per la **rete CDN Standard di Azure con tecnologia Microsoft**, la **rete CDN Standard di Azure con tecnologia Akamai** e la **rete CDN Standard/Premium di Azure con tecnologia Verizon**. Non tutte le metriche sono disponibili da tutti i provider, sebbene le differenze siano minime. La tabella mostra anche se una determinata metrica può essere indicata da un provider. Le metriche sono disponibili solo per gli endpoint della rete CDN in cui vi è traffico.


|Metrica                     | DESCRIZIONE | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Numero totale di riscontri della richiesta durante questo periodo. | Sì | Sì |Sì |
| RequestCountHttpStatus2xx | Numero di tutte le richieste che hanno generato un codice HTTP 2xx (ad esempio 200, 202). | Sì | Sì |Sì |
| RequestCountHttpStatus3xx | Numero di tutte le richieste che hanno generato un codice HTTP 3xx (ad esempio 300, 302). | Sì | Sì |Sì |
| RequestCountHttpStatus4xx | Numero di tutte le richieste che hanno generato un codice HTTP 4xx (ad esempio 400, 404). | Sì | Sì |Sì |
| RequestCountHttpStatus5xx | Numero di tutte le richieste che hanno generato un codice HTTP 5xx (ad esempio 500, 504). | Sì | Sì |Sì |
| RequestCountHttpStatusOthers | Numero di tutti gli altri codici HTTP (non inclusi nell'intervallo 2xx-5xx). | Sì | Sì |Sì |
| RequestCountHttpStatus200 | Numero di tutte le richieste che hanno generato una risposta di codice HTTP 200. | Sì | No   |Sì |
| RequestCountHttpStatus206 | Numero di tutte le richieste che hanno generato una risposta di codice HTTP 206. | Sì | No   |Sì |
| RequestCountHttpStatus302 | Numero di tutte le richieste che hanno generato una risposta di codice HTTP 302. | Sì | No   |Sì |
| RequestCountHttpStatus304 | Numero di tutte le richieste che hanno generato una risposta di codice HTTP 304. | Sì | No   |Sì |
| RequestCountHttpStatus404 | Numero di tutte le richieste che hanno generato una risposta di codice HTTP 404. | Sì | No   |Sì |
| RequestCountCacheHit | Conteggio di tutte le richieste che hanno generato un riscontro nella cache. L'asset è stato servito direttamente dal POP al client. | Sì | Sì | No   |
| RequestCountCacheMiss | Conteggio di tutte le richieste che hanno generato un mancato riscontro nella cache. Mancato riscontro nella cache significa che l'asset non è stato trovato nel POP più vicino al client e pertanto è stato recuperato dall'origine. | Sì | Sì | No  |
| RequestCountCacheNoCache | Conteggio di tutte le richieste a un asset a cui è stata impedita la memorizzazione nella cache a causa di una configurazione dell'utente sull'edge. | Sì | Sì | No  |
| RequestCountCacheUncacheable | Numero di tute le richieste su asset a cui è stata impedita la memorizzazione nella cache dalle intestazioni Cache-Control ed Expires dell'asset che indicano che non deve essere eseguita la memorizzazione nella cache in un POP o da un client HTTP. | Sì | Sì | No  |
| RequestCountCacheOthers | Conteggio di tutte le richieste con stato della cache non coperto dalle metriche precedenti. | No  | Sì | No   |
| EgressTotal | Trasferimento di dati in uscita in GB | Sì |Sì |Sì |
| EgressHttpStatus2xx | Trasferimento di dati in uscita* per risposte con codici di stato HTTP 2xx in GB. | Sì | Sì | No   |
| EgressHttpStatus3xx | Trasferimento di dati in uscita per risposte con codici di stato HTTP 3xx in GB. | Sì | Sì | No   |
| EgressHttpStatus4xx | Trasferimento di dati in uscita per risposte con codici di stato HTTP 4xx in GB. | Sì | Sì | No   |
| EgressHttpStatus5xx | Trasferimento di dati in uscita per risposte con codici di stato HTTP 5xx in GB. | Sì | Sì | No  |
| EgressHttpStatusOthers | Trasferimento di dati in uscita per risposte con altri codici di stato HTTP in GB. | Sì | Sì | No   |
| EgressCacheHit | Trasferimento di dati in uscita per risposte recapitate direttamente dalla cache CDN su POP/Edge della rete CDN. | Sì | Sì | No  |
| EgressCacheMiss. | Trasferimento di dati in uscita per risposte non trovate nel server POP più vicino e recuperate dal server di origine. | Sì | Sì | No  |
| EgressCacheNoCache | Trasferimento di dati in uscita per asset a cui è stata impedita la memorizzazione nella cache a causa di una configurazione dell'utente sull'edge. | Sì | Sì | No  |
| EgressCacheUncacheable | Trasferimento di dati in uscita per asset a cui è stata impedita la memorizzazione nella cache dalle intestazioni Cache-Control ed Expires dell'asset. Indica che non deve essere memorizzato nella cache in un POP o da un client HTTP. | Sì | Sì | No  |
| EgressCacheOthers | Trasferimento di dati in uscita per altri scenari di cache. | No  | Sì | No  |

*Il trasferimento di dati in uscita si riferisce al traffico recapitato da server POP della rete CDN al client.


### <a name="schema-of-the-core-analytics-logs"></a>Schema dei log dell'analisi principale 

Tutti i log vengono archiviati in formato JSON e per ogni voce sono presenti campi stringa in base allo schema seguente:

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

Dove *time* rappresenta l'ora di inizio del limite di ore per cui vengono restituite le statistiche. Quando una metrica non è supportata da un provider di rete CDN, anziché un valore double o integer, è presente un valore null. Questo valore null indica l'assenza di una metrica ed è diverso da un valore 0. Esiste un solo set di queste metriche per ogni dominio configurato nell'endpoint.

Proprietà di esempio:

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
* [Log Analytics di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [API REST di Azure Log Analytics](https://docs.microsoft.com/rest/api/loganalytics)







