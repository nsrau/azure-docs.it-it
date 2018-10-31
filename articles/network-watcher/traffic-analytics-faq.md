---
title: Domande frequenti su Analisi del traffico di Azure | Microsoft Docs
description: Ottenere le risposte ad alcune delle domande più frequenti su Analisi del traffico.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: 69d2d80e40400cc7fa40aeb5a163dce5036905ab
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "49402761"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Domande frequenti su Analisi del traffico

Questo articolo raccoglie molte delle domande più frequenti su Analisi del traffico in Azure Network Watcher.

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Quali sono i prerequisiti per usare Analisi del traffico?

Analisi del traffico prevede i prerequisiti seguenti:

- Una sottoscrizione abilitata per Network Watcher.
- Log dei flussi dei gruppi di sicurezza di rete (NSG) abilitati per i gruppi di sicurezza di rete da monitorare.
- Un account di archiviazione di Azure per archiviare i log dei flussi non elaborati.
- Un'area di lavoro Azure Log Analytics con accesso in lettura e scrittura.

Per abilitare Analisi del traffico, l'account deve soddisfare una delle seguenti condizioni:

- L'account deve essere assegnato a uno dei seguenti ruoli a livello di abbonamento: amministratore account, amministratore del servizio o coamministratore.
- L'account deve avere uno dei seguenti ruoli di controllo degli accessi in base al ruolo (RBAC) nell'ambito dell'abbonamento: proprietario, collaboratore, lettore o collaboratore di rete.
- Se l'account non è assegnato a uno dei ruoli appena elencati, deve essere assegnato a un ruolo personalizzato al quale vengono assegnate le seguenti azioni, a livello di sottoscrizione.
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft.Network/routeTables/read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
Per verificare i ruoli assegnati a un utente per una sottoscrizione:

1. Accedere ad Azure usando **Login-AzureRmAccount**. 

2. Selezionare la sottoscrizione richiesta usando **Select-AzureRmSubscription**. 

3. Per elencare tutti i ruoli assegnati a un utente specifico, usare **Get-AzureRmRoleAssignment -SignInName [e-mail utente] -IncludeClassicAdministrators**. 

Se non viene visualizzato alcun risultato, contattare l'amministratore dell'abbonamento per ottenere l'accesso ed eseguire i comandi. Per altre informazioni, consultare [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).


## <a name="in-which-azure-regions-are-traffic-analytics-available"></a>In quali aree è disponibile Analisi del traffico?

È possibile usare Analisi del traffico per i gruppi di sicurezza di rete in tutte le seguenti aree supportate: Stati Uniti centro-occidentali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Stati Uniti centrali, Stati Uniti occidentali, Stati Uniti occidentali 2, Europa occidentale, Europa settentrionale, Regno Unito occidentale, Regno Unito meridionale, Australia orientale, Australia sud-orientale e Asia sud-orientale. L'area di lavoro di Log Analytics deve esistere nell'area Stati Uniti centro-occidentali, Stati Uniti orientali, Europa occidentale, Regno Unito meridionale, Australia sud-orientale o Asia sud-orientale.

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>I gruppi di sicurezza di rete per cui vengono abilitati i log dei flussi possono trovarsi in aree differenti dalla propria area di lavoro?

Sì, questi NSG possono trovarsi in regioni diverse dall'area di lavoro di Log Analytics.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>È possibile configurare più gruppi di sicurezza di rete in una singola area di lavoro?

Sì.

## <a name="can-i-use-an-existing-workspace"></a>È possibile usare un'area di lavoro esistente?

Sì. Se si seleziona un'area di lavoro esistente, assicurarsi che sia stata eseguita la migrazione al nuovo linguaggio di query. Se non si vuole aggiornare l'area di lavoro, è necessario crearne una nuova. Per altre informazioni sul nuovo linguaggio di query, vedere [Aggiornamento di Azure Log Analytics alla nuova ricerca log](../log-analytics/log-analytics-log-search-upgrade.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>È possibile usare sottoscrizioni diverse per l'account di archiviazione di Azure e l'area di lavoro di Log Analytics?

Sì, è possibile usare sottoscrizioni diverse per l'account di archiviazione di Azure e l'area di lavoro di Log Analytics.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Posso archiviare i log non elaborati in un altro abbonamento?

No. È possibile memorizzare i log non elaborati in qualsiasi account di archiviazione in cui è abilitato un NSG per i log dei flussi. Tuttavia, sia l'account di archiviazione che i log non elaborati devono trovarsi nella stessa area e nella stessa sottoscrizione.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Cosa succede se non riesco a configurare un NSG per Analisi del traffico a causa di un errore "Non trovato"?

Selezionare un'area supportata. Se si seleziona un'area non supportata, viene visualizzato un errore "Non trovato". Le regioni supportate sono state elencate in precedenza in questo articolo.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Cosa accade se si ottiene lo stato "Impossibile caricare" nella pagina dei registri di flusso NSG?

Per il corretto funzionamento della registrazione dei flussi, è necessario registrare il provider Microsoft.Insights. Se non si è sicuri che il provider Microsoft.Insights sia registrato o meno per la sottoscrizione, sostituire *xxxxx-xxxxx-xxxxxx-xxxx* nel comando seguente ed eseguire questi comandi di PowerShell:

```powershell-interactive
**Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Ho configurato la soluzione. Perché non viene visualizzato nessun elemento nel dashboard?

La prima visualizzazione del dashboard può richiedere fino a 30 minuti. La soluzione deve aggregare dati sufficienti per poter ottenere informazioni significative, prima di generare i report. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Cosa succede se viene visualizzato il messaggio seguente: "Non sono stati trovati dati in questa area di lavoro per l'intervallo di tempo selezionato. Provare a modificare l'intervallo di tempo o a selezionare un'altra area di lavoro."?

Provare le opzioni seguenti:
- Modificare l'intervallo di tempo nella barra superiore.
- Selezionare una diversa area di lavoro di Log Analytics nella barra superiore.
- Provare ad accedere ad Analisi del traffico dopo 30 minuti, se è stata abilitata di recente.
    
Se i problemi persistono, chiedere assistenza nel [forum di suggerimenti per gli utenti](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Cosa succede se viene visualizzato il seguente messaggio: "L’analisi dei registri di flusso NSG viene eseguito per la prima volta. L'operazione potrebbe richiedere circa 20-30 minuti. Verificare più tardi". 2) Controllare qui l'utilizzo dell'area di lavoro per convalidare la quota in eccedenza altrimenti fare riferimento alle FAQ per ulteriori informazioni, se l'area di lavoro si trova nello SKU gratuito e il passaggio precedente non funziona"?

È possibile che venga visualizzato questo messaggio perché:
- Analisi del traffico è stato abilitato di recente ed è possibile che non abbia già aggregato dati sufficienti per ricavare informazioni dettagliate significative.
- Si sta usando la versione gratuita dell'area di lavoro di Log Analytics, che ha superato i limiti di quota. Potrebbe essere necessario utilizzare un'area di lavoro con una capacità maggiore.
    
Se i problemi persistono, chiedere assistenza nel [forum di suggerimenti per gli utenti](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Cosa succede se viene visualizzato il seguente È stato visualizzato il messaggio seguente: "Sono disponibili solo i dati delle risorse (topologia) e nessuna informazione sui flussi. Nel frattempo, fare clic per visualizzare solo i dati delle risorse e fare riferimento alle FAQ per ulteriori informazioni"?

Le informazioni sulle risorse sono visualizzate nel dashboard, tuttavia non sono presenti le statistiche relative ai flussi. I dati potrebbero non essere presenti a causa della mancanza di flussi di comunicazione tra le risorse. Attendere 60 minuti e ricontrollare lo stato. Se il problema non viene risolto e si è certi dell'esistenza dei flussi di comunicazione tra le risorse, chiedere assistenza nel [forum di suggerimenti per gli utenti](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>È possibile configurare la funzionalità Analisi del traffico tramite PowerShell o un client o modello di Azure Resource Manager?

È possibile configurare Analisi del traffico utilizzando Windows PowerShell a partire dalla versione 6.2.1. Per configurare la registrazione dei flussi e Analisi del traffico per un NSG specifico utilizzando il comando Set cmdlet, vedere [Set-AzureRmNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermnetworkwatcherconfigflowlog?view=azurermps-6.3.0). Per ottenere lo stato della registrazione dei flussi e di Analisi del traffico per un determinato NSG, vedere [Get-AzureRmNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkwatcherflowlogstatus?view=azurermps-6.3.0).

Attualmente, non è possibile utilizzare un modello di Azure Resource Manager per configurare Analisi del traffico.

Per configurare Analisi del traffico utilizzando un client Azure Resource Manager, vedere gli esempi seguenti.

**Esempio Set cmdlet:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<name of NSG>"
$TAstorageId = "/subscriptions/<storage subscription id>/resourcegroups/<storage resource group name> /providers/microsoft.storage/storageaccounts/<storage account name>"
$networkWatcherResourceGroupName = "<network watcher resource group name>"
$networkWatcherName = "<network watcher name>"

$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}',
    'properties': 
    {
        'storageId': '${TAstorageId}',
        'enabled': '<true to enable flow log or false to disable flow log>',
        'retentionPolicy' : 
        {
            days: <enter number of days like to retail flow logs in storage account>,
            enabled: <true to enable retention or false to disable retention>
        }
    },
    'flowAnalyticsConfiguration':
    {
                'networkWatcherFlowAnalyticsConfiguration':
      {
        'enabled':,<true to enable traffic analytics or false to disable traffic analytics>
        'workspaceId':'bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb',
        'workspaceRegion':'<workspace region>',
        'workspaceResourceId':'/subscriptions/<workspace subscription id>/resourcegroups/<workspace resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
        
      }

    }
}
"@
$apiversion = "2016-09-01"

armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/configureFlowlog?api-version=${apiversion}" $requestBody
```
**Esempio Get cmdlet:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<NSG name>"


$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}'
}
“@


armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/queryFlowLogStatus?api-version=${apiversion}" $requestBody
```



## <a name="how-is-traffic-analytics-priced"></a>Come viene determinato il prezzo di Analisi del traffico?

L'analisi del traffico viene misurata. La misurazione si basa sull'elaborazione dei dati del registro di flusso da parte del servizio e sulla memorizzazione dei registri avanzati risultanti in un'area di lavoro Log Analytics. 

Ad esempio, in base il [piano tariffario](https://azure.microsoft.com/pricing/details/network-watcher/), prendere in considerazione l'area degli Stati Uniti centrali. Se i dati archiviati per i log dei flussi in un account di archiviazione elaborato da Analisi del traffico è 10 GB e i log avanzati inseriti nell'area di lavoro di Log Analytics sono pari a 1 GB, gli addebiti applicabili saranno: 10 x 2.3$ + 1 x 2.76$ = 25.76$

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Come è possibile spostarsi con la tastiera nella visualizzazione mappa geografica?

La pagina della mappa geografica contiene due sezioni principali:
    
- **Banner**: il banner nella parte superiore della mappa geografica fornisce i pulsanti per selezionare i filtri di distribuzione del traffico (ad esempio, Distribuzione, Traffico da paesi e Dannoso). Quando si seleziona un pulsante, il filtro corrispondente viene applicato sulla mappa. Ad esempio, se si seleziona il pulsante Attivo, la mappa evidenzia i centri dati attivi nella distribuzione remota.
- **Mappa**: sotto al banner, la sezione Mappa mostra la distribuzione del traffico tra i data center di Azure e i paesi.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navigazione da tastiera sul banner
    
- Per impostazione predefinita, la selezione nella pagina mappa geografica per il banner è il filtro "Data center di Azure".
- Per spostarsi su un altro filtro, utilizzare il pulsante `Tab` o `Right arrow`. Per spostarsi indietro, utilizzare il pulsante `Shift+Tab` o `Left arrow`. La direzione di navigazione in avanti è da sinistra a destra, seguita dalla direzione dall'alto verso il basso.
- Premere il tasto di direzione `Enter` o `Down` per applicare il filtro selezionato. In base alla selezione e alla distribuzione del filtro, vengono evidenziati uno o più nodi nella sezione Mappa.
- Per alternare tra Banner e Mappa, premere `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-map"></a>Navigazione da tastiera sulla mappa
    
- Dopo aver selezionato un filtro nel banner e premuto `Ctrl+F6`, lo stato attivo si sposta su uno dei nodi evidenziati (**Data Center di Azure** o **Paese/area geografica**) nella visualizzazione della mappa.
- Per spostarsi su altri nodi evidenziati nella mappa, utilizzare `Tab` o il tasto `Right arrow` per spostarsi in avanti. Utilizzare `Shift+Tab` o il tasto `Left arrow` per spostarsi all'indietro.
- Per selezionare qualsiasi nodo evidenziato nella mappa, usare il tasto `Enter` o `Down arrow`.
- Selezionando uno di questi nodi, lo stato attivo si sposta sulla **casella degli strumenti Informazioni** relativa al nodo. Per impostazione predefinita, lo stato attivo passerà al pulsante di chiusura nella **casella degli strumenti Informazioni**. Per spostarsi ulteriormente all'interno della visualizzazione della **casella**, usare i tasti `Right arrow` e `Left arrow` per andare in avanti o indietro. Premere `Enter` equivale a selezionare il pulsante con stato attivo nella **casella degli strumenti Informazioni**.
- Premendo `Tab` mentre è attiva la **casella degli strumenti Informazioni**, lo stato attivo passa agli endpoint dello stesso continente del nodo selezionato. Usare i tasti `Right arrow` e `Left arrow` per spostarsi tra questi endpoint.
- Per passare ad altri endpoint di flusso o cluster del continente, usare `Tab` per spostarsi in avanti e `Shift+Tab` per spostarsi all'indietro.
- Quando lo stato attivo si trova su **Cluster del continente**, usare i tasti di direzione `Enter` o `Down` per evidenziare gli endpoint all'interno del cluster del continente. Per spostarsi tra gli endpoint e il pulsante di chiusura nella casella Informazioni del cluster del continente, usare il tasto `Right arrow` o `Left arrow` rispettivamente per il movimento in avanti e all'indietro. In qualsiasi endpoint è possibile usare `Shift+L` per passare alla linea di connessione dal nodo selezionato all'endpoint. È possibile premere nuovamente `Shift+L` per spostarsi all'endpoint selezionato.
        
### <a name="keyboard-navigation-at-any-stage"></a>Navigazione da tastiera in qualsiasi momento
    
- `Esc` comprime la selezione espansa.
- Il tasto `Up arrow` esegue la stessa azione di `Esc`. Il tasto `Down arrow` esegue la stessa azione di `Enter`.
- Usare `Shift+Plus` per fare zoom avanti e `Shift+Minus` per fare zoom indietro.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Come è possibile spostarsi con la tastiera nella visualizzazione topologia di rete virtuale?

La pagina della topologia di rete virtuale contiene due sezioni principali:
    
- **Banner**: il banner nella parte superiore della topologia di rete virtuale fornisce i pulsanti per selezionare i filtri di distribuzione del traffico (ad esempio, Reti virtuali connesse, Reti virtuali disconnesse e IP pubblici). Quando si seleziona un pulsante, il filtro corrispondente viene applicato sulla topologia. Ad esempio, se si seleziona il pulsante Attivo, la topologia evidenzia le reti virtuali attive nella distribuzione remota.
- **Topologia**: sotto al banner, la sezione topologia mostra la distribuzione del traffico tra le reti virtuali.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navigazione da tastiera sul banner
    
- Per impostazione predefinita, la selezione nella pagina della topologia di rete virtuale per il banner è il filtro "VNet connesse".
- Per spostarsi su un altro filtro, utilizzare il pulsante `Tab` per spostarsi in avanti. Per tornare indietro, utilizzare il tasto `Shift+Tab`. La direzione di navigazione in avanti è da sinistra a destra, seguita dalla direzione dall'alto verso il basso.
- Premere `Enter` per applicare il filtro selezionato. In base alla selezione e all'implementazione del filtro, vengono evidenziati uno o più nodi (rete virtuale) nella sezione topologia.
- Per passare dal banner alla topologia, premere `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Navigazione da tastiera sulla topologia
    
- Dopo aver selezionato un filtro nel banner e premuto `Ctrl+F6`, lo stato attivo si sposta su uno dei nodi evidenziati (**VNet**) nella visualizzazione della topologia.
- Per spostarsi su altri nodi evidenziati nella visualizzazione della topologia, utilizzare il tasto `Shift+Right arrow` per spostarsi in avanti. 
- Sui nodi evidenziati, lo stato attivo si sposta sulla **casella degli strumenti Informazioni** relativa al nodo. Per impostazione predefinita, la messa a fuoco viene spostata sul pulsante **Altri dettagli** nella **casella degli strumenti Informazioni**. Per spostarsi ulteriormente all'interno della visualizzazione della **casella**, usare i tasti `Right arrow` e `Left arrow` per andare in avanti o indietro. Premere `Enter` equivale a selezionare il pulsante con stato attivo nella **casella degli strumenti Informazioni**.
- Una volta selezionati tali nodi, è possibile consultarne tutte le connessioni, una per una, premendo il tasto `Shift+Left arrow`. L’attenzione si sposta sulla **casella degli strumenti Informazioni** di tale connessione. In qualsiasi momento è possibile spostare nuovamente l’attenzione sul nodo premendo nuovamente `Shift+Right arrow`.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Come è possibile spostarsi con la tastiera nella visualizzazione topologia di subnet?

La pagina della topologia di subnet virtuale contiene due sezioni principali:
    
- **Banner**: il banner nella parte superiore della topologia delle subnet virtuali fornisce i pulsanti per selezionare i filtri di distribuzione del traffico (ad esempio, le subnet Attivo, Medio e Gateway). Quando si seleziona un pulsante, il filtro corrispondente viene applicato sulla topologia. Ad esempio, se si seleziona il pulsante Attivo, la topologia evidenzia le subnet virtuali attive nella distribuzione remota.
- **Topologia**: sotto al banner, la sezione topologia mostra la distribuzione del traffico tra le subnet virtuali.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navigazione da tastiera sul banner
    
- Per impostazione predefinita, la selezione nella pagina della topologia di subnet virtuale per il banner è il filtro "Subnet".
- Per spostarsi su un altro filtro, utilizzare il pulsante `Tab` per spostarsi in avanti. Per tornare indietro, utilizzare il tasto `Shift+Tab`. La direzione di navigazione in avanti è da sinistra a destra, seguita dalla direzione dall'alto verso il basso.
- Premere `Enter` per applicare il filtro selezionato. In base alla selezione e alla distribuzione del filtro, vengono evidenziati uno o più nodi (subnet) nella sezione Topologia.
- Per passare dal banner alla topologia, premere `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Navigazione da tastiera sulla topologia
    
- Dopo aver selezionato un filtro nel banner e premuto `Ctrl+F6`, lo stato attivo si sposta su uno dei nodi evidenziati (**Subnet**) nella visualizzazione della topologia.
- Per spostarsi su altri nodi evidenziati nella visualizzazione della topologia, utilizzare il tasto `Shift+Right arrow` per spostarsi in avanti. 
- Sui nodi evidenziati, lo stato attivo si sposta sulla **casella degli strumenti Informazioni** relativa al nodo. Per impostazione predefinita, la messa a fuoco viene spostata sul pulsante **Altri dettagli** nella **casella degli strumenti Informazioni**. Per spostarsi ulteriormente all'interno della visualizzazione della **casella**, usare i tasti `Right arrow` e `Left arrow` per andare in avanti o indietro. Premere `Enter` equivale a selezionare il pulsante con stato attivo nella **casella degli strumenti Informazioni**.
- Una volta selezionati tali nodi, è possibile consultarne tutte le connessioni, una per una, premendo il tasto `Shift+Left arrow`. L’attenzione si sposta sulla **casella degli strumenti Informazioni** di tale connessione. In qualsiasi momento è possibile spostare nuovamente l’attenzione sul nodo premendo nuovamente `Shift+Right arrow`.    

