---
title: Log non elaborati HTTP della rete CDN di Azure
description: Questo articolo descrive i log non elaborati HTTP della rete CDN di Azure.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/22/2020
ms.author: allensu
ms.openlocfilehash: 3b36e528a013403a2ed664d3011338d92f37a3db
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87040169"
---
# <a name="azure-cdn-http-raw-logs"></a>Log non elaborati HTTP della rete CDN di Azure
I log non elaborati offrono informazioni dettagliate sulle operazioni e sugli errori importanti per il controllo e la risoluzione dei problemi. I log non elaborati differiscono dai log attività. I log attività offrono visibilità sulle operazioni eseguite sulle risorse di Azure. I log non elaborati forniscono un record delle operazioni della risorsa. Il log non elaborato fornisce informazioni dettagliate su ogni richiesta ricevuta dalla rete CDN. 

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

    ![Impostazione di diagnostica della rete CDN](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > I log non elaborati sono disponibili solo a livello di profilo mentre i log del codice di stato HTTP aggregati sono disponibili a livello di endpoint.

4. In **Impostazioni di diagnostica** immettere un nome per l'impostazione di diagnostica in **Diagnostic settings name** (Nome impostazioni di diagnostica).

5. Selezionare il **log** e impostare i giorni di conservazione.

6. Selezionare i **Dettagli destinazione**. Le opzioni di destinazione sono:
    * **Invia a Log Analytics**
        * Selezionare la **sottoscrizione** e l'**area di lavoro Log Analytics**.
    * **Archivia in un account di archiviazione**
        * Selezionare la **sottoscrizione** e l'**account di archiviazione**.
    * **Streaming in un hub eventi**
        * Selezionare la **sottoscrizione**, lo **spazio dei nomi dell'hub eventi**, il **nome dell'hub eventi (facoltativo)** e il **nome criterio hub eventi**.

    ![Impostazione di diagnostica della rete CDN](./media/cdn-raw-logs/raw-logs-02.png)

7. Selezionare **Salva**.

## <a name="configuration---azure-powershell"></a>Configurazione-Azure PowerShell

Usare [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest) per configurare l'impostazione di diagnostica per i log non elaborati.

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
> I log possono essere visualizzati nel profilo di Log Analytics tramite una query. Una query di esempio può essere              AzureDiagnostics | where Category == "AzureCdnAccessLog"


### <a name="sent-to-origin-shield-deprecation"></a>Deprecazione inviata a schermata di origine
La proprietà del log non elaborato **isSentToOriginShield** è stata deprecata e sostituita da un nuovo campo **isReceivedFromClient**. Utilizzare il nuovo campo se si sta già utilizzando il campo deprecato. 

I log non elaborati includono i log generati dal perimetro della rete CDN (POP figlio) e dallo scudo di origine. Shield di origine si riferisce ai nodi padre posizionati in modo strategico in tutto il mondo. Questi nodi comunicano con i server di origine e riducono il carico di traffico all'origine. 

Per ogni richiesta che passa a Shield di origine, sono presenti due voci di log:

* Uno per i nodi perimetrali
* Uno per Shield di origine. 

Per distinguere l'uscita o le risposte dai nodi perimetrali rispetto a Shield di origine, è possibile usare il campo isReceivedFromClient per ottenere i dati corretti. 

Se il valore è false, significa che la richiesta è stata risposta dalla schermatura di origine ai nodi perimetrali. Questo approccio è efficace per confrontare i log non elaborati con i dati di fatturazione. Gli addebiti non vengono addebitati per l'uscita dalla schermatura di origine ai nodi perimetrali. Vengono addebitati i costi per l'uscita dai nodi perimetrali ai client. 

**Esempio di query kusto per escludere i log generati sullo scudo di origine in Log Analytics.**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> La funzionalità dei log non elaborati HTTP è disponibile automaticamente per tutti i profili creati o aggiornati dopo il **25 febbraio 2020**. Per i profili della rete CDN creati in precedenza, è necessario aggiornare l'endpoint della rete CDN dopo aver configurato la registrazione. È ad esempio possibile passare al filtro geografico negli endpoint della rete CDN e bloccare i paesi/le aree geografiche non pertinenti per il carico di lavoro e quindi fare clic su Salva. 

## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati abilitati i log non elaborati HTTP per il servizio rete CDN Microsoft.

Per altre informazioni sulla rete CDN di Azure e sugli altri servizi di Azure menzionati in questo articolo, vedere:

* [Analizzare](cdn-log-analysis.md) i modelli di utilizzo della rete CDN di Azure.

* Altre informazioni su [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview).

* Configurare [Log Analytics in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
