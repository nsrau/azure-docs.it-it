---
title: Monitoraggio delle metriche e dei log non elaborati per la rete CDN di Azure da Microsoft
description: Questo articolo descrive la rete CDN di Azure di Microsoft Monitoring Metrics e i log non elaborati.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/25/2020
ms.author: allensu
ms.openlocfilehash: c41bf8bc6e5aa3749786bc1189343dfdebdc1508
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321150"
---
# <a name="monitoring-metrics-and-raw-logs-for-azure-cdn-from-microsoft"></a>Monitoraggio delle metriche e dei log non elaborati per la rete CDN di Azure da Microsoft
Con la rete CDN di Azure di Microsoft, è possibile monitorare le risorse nei modi seguenti per risolvere i problemi, rilevare ed eseguire il debug dei problemi. 

* I log non elaborati forniscono informazioni dettagliate su ogni richiesta ricevuta dalla rete CDN. I log non elaborati differiscono dai log attività. I log attività offrono visibilità sulle operazioni eseguite sulle risorse di Azure.
* Metriche, che visualizzano quattro metriche chiave sulla rete CDN, ad esempio percentuale di riscontri di byte, numero di richieste, dimensioni della risposta e latenza totale. Fornisce anche dimensioni diverse per suddividere le metriche.
* Avviso, che consente al cliente di configurare l'avviso per le metriche chiave
* Metriche aggiuntive, che consentono ai clienti di usare Azure Log Analytics per abilitare metriche aggiuntive di valore. Vengono inoltre forniti esempi di query per alcune altre metriche in Azure Log Analytics.

> [!IMPORTANT]
> La funzionalità log non elaborati HTTP è disponibile per la rete CDN di Azure da Microsoft.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration---azure-portal"></a>Configurazione-portale di Azure

Per configurare i log non elaborati per la rete CDN di Azure dal Microsoft: 

1. Scegliere **tutte le risorse**dal menu portale di Azure  >>  **\<your-CDN-profile>** .

2. In **Monitoraggio** selezionare **Impostazioni di diagnostica**.

3. Fare clic su **+ Aggiungi impostazione di diagnostica**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-01.png" alt-text="Aggiungere l'impostazione di diagnostica per il profilo di rete CDN." border="true":::
    
    > [!IMPORTANT]
    > I log non elaborati sono disponibili solo a livello di profilo mentre i log del codice di stato HTTP aggregati sono disponibili a livello di endpoint.

4. In **Impostazioni di diagnostica** immettere un nome per l'impostazione di diagnostica in **Diagnostic settings name** (Nome impostazioni di diagnostica).

5. Selezionare il **AzureCdnAccessLog** e impostare la conservazione in giorni.

6. Selezionare i **Dettagli destinazione**. Le opzioni di destinazione sono:
    * **Invia a Log Analytics**
        * Selezionare la **sottoscrizione** e l'**area di lavoro Log Analytics**.
    * **Archivia in un account di archiviazione**
        * Selezionare la **sottoscrizione** e l'**account di archiviazione**.
    * **Streaming in un hub eventi**
        * Selezionare la **sottoscrizione**, lo **spazio dei nomi dell'hub eventi**, il **nome dell'hub eventi (facoltativo)** e il **nome criterio hub eventi**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-02.png" alt-text="Configurare la destinazione per le impostazioni del log." border="true":::

7. Selezionare **Salva**.

## <a name="configuration---azure-powershell"></a>Configurazione-Azure PowerShell

Usare [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) per configurare l'impostazione di diagnostica per i log non elaborati.

I dati di conservazione vengono definiti dall'opzione **-RetentionInDays** nel comando.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Abilitare i log di diagnostica in un account di archiviazione

1. Accedere a Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Per abilitare i log di diagnostica in un account di archiviazione, immettere questi comandi. Sostituire le variabili con i valori seguenti:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Abilitare i log di diagnostica per l'area di lavoro Log Analytics

1. Accedere a Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Per abilitare i log di diagnostica per un'area di lavoro Log Analytics, immettere questi comandi. Sostituire le variabili con i valori seguenti:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Abilita log di diagnostica per lo spazio dei nomi dell'hub eventi

1. Accedere a Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Per abilitare i log di diagnostica per uno spazio dei nomi di hub eventi, immettere questi comandi. Sostituire le variabili con i valori seguenti:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>Proprietà dei log non elaborati

La rete CDN di Azure di Servizi Microsoft attualmente fornisce log non elaborati. I log non elaborati forniscono richieste API singole le cui voci hanno lo schema seguente: 

| Proprietà              | Descrizione                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | Stringa di riferimento univoca che identifica una richiesta fornita da Frontdoor, inviata anche come intestazione X-Azure-Ref al client. Obbligatoria per la ricerca di dettagli nei log di accesso per una richiesta specifica. |
| HttpMethod            | Metodo HTTP usato dalla richiesta.                                                                                                                                                                     |
| HttpVersion           | Tipo della richiesta o della connessione.                                                                                                                                                                   |
| RequestUri            | URI della richiesta ricevuta.                                                                                                                                                                         |
| RequestBytes          | Dimensioni del messaggio di richiesta HTTP in byte, inclusi intestazioni e corpo della richiesta.                                                                                                   |
| ResponseBytes         | Byte inviati dal server back-end come risposta.                                                                                                                                                    |
| UserAgent             | Tipo di browser usato dal client.                                                                                                                                                               |
| ClientIp              | Indirizzo IP del client che ha eseguito la richiesta.                                                                                                                                                  |
| TimeTaken             | Durata dell'azione, in millisecondi.                                                                                                                                            |
| SecurityProtocol      | Versione del protocollo TLS/SSL usata dalla richiesta o Null se non è stato usato alcun tipo di crittografia.                                                                                                                           |
| Endpoint              | Endpoint della rete CDN configurato dall'host nel profilo della rete CDN padre.                                                                                                                                   |
| Nome host back-end     | Nome dell'host o dell'origine back-end in cui vengono inviate le richieste.                                                                                                                                |
| Sent to origin shield (Inviato a shield di origine) </br> (obsoleto) * **vedere la nota sulla deprecazione riportata di seguito.** | Se true, la risposta alla richiesta proviene dalla cache dello shield di origine anziché dal POP perimetrale. Lo shield di origine è una cache padre usata per migliorare la percentuale di riscontri nella cache.                                       |
| isReceivedFromClient | Se true, significa che la richiesta proviene dal client. Se false, la richiesta non è presente nel perimetro (POP figlio) e viene risposta da Shield di origine (POP padre). 
| HttpStatusCode        | Codice di stato HTTP restituito dal proxy.                                                                                                                                                        |
| HttpStatusDetails     | Stato risultante nella richiesta. Il significato di questo valore stringa è disponibile in una tabella di riferimento sullo stato.                                                                                              |
| POP                   | POP perimetrale che ha risposto alla richiesta utente. Le abbreviazioni dei POP sono codici aeroportuali delle rispettive Metro.                                                                                   |
| Stato della cache          | Indica se l'oggetto è stato restituito dalla cache o proviene dall'origine.                                                                                                             |
> [!NOTE]
> I log possono essere visualizzati nel profilo di Log Analytics tramite una query. Una query di esempio avrà un aspetto simile al seguente:
    ```
    AzureDiagnostics | where Category == "AzureCdnAccessLog"
    ```

### <a name="sent-to-origin-shield-deprecation"></a>Deprecazione inviata a schermata di origine
La proprietà del log non elaborato **isSentToOriginShield** è stata deprecata e sostituita da un nuovo campo **isReceivedFromClient**. Utilizzare il nuovo campo se si sta già utilizzando il campo deprecato. 

I log non elaborati includono i log generati dal perimetro della rete CDN (POP figlio) e dallo scudo di origine. Shield di origine si riferisce ai nodi padre posizionati in modo strategico in tutto il mondo. Questi nodi comunicano con i server di origine e riducono il carico di traffico all'origine. 

Per ogni richiesta che passa a Shield di origine, sono presenti due voci di log:

* Uno per i nodi perimetrali
* Uno per Shield di origine. 

Per distinguere l'uscita o le risposte dai nodi perimetrali rispetto a Shield di origine, è possibile usare il campo **isReceivedFromClient** per ottenere i dati corretti. 

Se il valore è false, significa che la richiesta è stata risposta dalla schermatura di origine ai nodi perimetrali. Questo approccio è efficace per confrontare i log non elaborati con i dati di fatturazione. Gli addebiti non vengono addebitati per l'uscita dalla schermatura di origine ai nodi perimetrali. Vengono addebitati i costi per l'uscita dai nodi perimetrali ai client. 

**Esempio di query kusto per escludere i log generati sullo scudo di origine in Log Analytics.**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> La funzionalità dei log non elaborati HTTP è disponibile automaticamente per tutti i profili creati o aggiornati dopo il **25 febbraio 2020**. Per i profili della rete CDN creati in precedenza, è necessario aggiornare l'endpoint della rete CDN dopo aver configurato la registrazione. È ad esempio possibile passare al filtro geografico negli endpoint della rete CDN e bloccare i paesi/le aree geografiche non pertinenti per il carico di lavoro e quindi fare clic su Salva.


## <a name="metrics"></a>Metriche
La rete CDN di Azure di Microsoft è integrata con monitoraggio di Azure e pubblica quattro metriche della rete CDN per tenere traccia, risolvere i problemi ed eseguire il debug dei problemi. 

Le metriche vengono visualizzate in grafici e accessibili tramite PowerShell, l'interfaccia della riga di comando e l'API. Le metriche della rete CDN sono gratuite.

La rete CDN di Azure di Microsoft misura e invia le metriche in intervalli di 60 secondi. Le metriche possono richiedere fino a 3 minuti per essere visualizzate nel portale. 

Per altre informazioni, vedere [metriche di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

**Metriche supportate dalla rete CDN di Azure di Microsoft**

| Metriche         | Descrizione                                                                                                      | Dimensione                                                                                   |
|-----------------|------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| Percentuale riscontri byte * | Percentuale di uscita dalla cache della rete CDN, calcolata rispetto al totale in uscita.                                      | Endpoint                                                                                    |
| RequestCount    | Il numero di richieste client gestite dalla rete CDN.                                                                     | Endpoint </br> Paese del client. </br> Area client. </br> Stato HTTP. </br> Gruppo di stato HTTP. |
| ResponseSize    | Numero di byte inviati come risposte dal perimetro della rete CDN ai client.                                                  |Endpoint </br> Paese del client. </br> Area client. </br> Stato HTTP. </br> Gruppo di stato HTTP.                                                                                          |
| TotalLatency    | Tempo totale dalla richiesta client ricevuta dalla rete CDN **fino all'invio dell'ultimo byte di risposta dalla rete CDN al client**. |Endpoint </br> Paese del client. </br> Area client. </br> Stato HTTP. </br> Gruppo di stato HTTP.                                                                                             |

***Byte raggiunti razione = (in uscita da Edge-uscita dall'origine)/egress da Edge**

Scenari esclusi dal calcolo della percentuale di riscontri in byte:

* Non è possibile configurare in modo esplicito nessuna cache tramite il motore regole o il comportamento di memorizzazione nella cache della stringa di query.
* La direttiva di controllo della cache viene configurata in modo esplicito con nessun archivio o con la cache privata.

### <a name="metrics-configuration"></a>Configurazione metrica

1. Scegliere **tutte le risorse**dal menu portale di Azure  >>  **\<your-CDN-profile>** .

2. In **monitoraggio**selezionare **metriche**:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-03.png" alt-text="Metriche per il profilo di rete CDN." border="true":::

3. Selezionare **Aggiungi metrica**e selezionare la metrica da aggiungere:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-04.png" alt-text="Aggiungere e selezionare metrica per profilo di rete CDN." border="true":::

4. Selezionare **Aggiungi filtro** per aggiungere un filtro:
    
    :::image type="content" source="./media/cdn-raw-logs/raw-logs-05.png" alt-text="Applicare il filtro alla metrica." border="true":::

5. Selezionare **applica** suddivisione per visualizzare la tendenza in base a dimensioni diverse:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-06.png" alt-text="Applicare la suddivisione alla metrica." border="true":::

6. Selezionare **nuovo grafico** per aggiungere un nuovo grafico:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-07.png" alt-text="Aggiungere un nuovo grafico alla visualizzazione metrica." border="true":::

### <a name="alerts"></a>Avvisi

È possibile configurare gli avvisi sulla rete CDN Microsoft selezionando avvisi di **monitoraggio**  >>  **Alerts**.

Selezionare **nuova regola di avviso** per le metriche elencate nella sezione metrica:

:::image type="content" source="./media/cdn-raw-logs/raw-logs-08.png" alt-text="Configurare gli avvisi per l'endpoint della rete CDN." border="true":::

L'avviso verrà addebitato in base al monitoraggio di Azure. Per altre informazioni sugli avvisi, vedere [avvisi di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

### <a name="additional-metrics"></a>Metriche aggiuntive
È possibile abilitare metriche aggiuntive usando Log Analytics di Azure e i log non elaborati per un costo aggiuntivo.

1. Seguire i passaggi precedenti in Abilitazione della diagnostica per inviare il log non elaborato a log Analytics.

2. Selezionare l'area di lavoro di Log Analytics creata:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-09.png" alt-text="Selezionare l'area di lavoro di log Analytics" border="true":::   

3. Selezionare **log** in **generale** nell'area di lavoro di log Analytics.  Quindi selezionare **inizia**:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-10.png" alt-text="Area di lavoro risorse di log Analytics." border="true":::   
 
4. Selezionare **profili**di rete CDN.  Selezionare una query di esempio per eseguire o chiudere la schermata di esempio per immettere una query personalizzata:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-11.png" alt-text="Schermata di query di esempio." border="true":::   

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-12.png" alt-text="Esecuzione della query." border="true":::   

4. Per visualizzare i dati in base al grafico, selezionare **grafico**.  Selezionare **Aggiungi al dashboard** per aggiungere il grafico al dashboard di Azure:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-13.png" alt-text="Aggiungere il grafico al dashboard." border="true"::: 

## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati abilitati i log non elaborati HTTP per il servizio rete CDN Microsoft.

Per altre informazioni sulla rete CDN di Azure e sugli altri servizi di Azure menzionati in questo articolo, vedere:

* [Analizzare](cdn-log-analysis.md) i modelli di utilizzo della rete CDN di Azure.

* Altre informazioni su [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview).

* Configurare [Log Analytics in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
