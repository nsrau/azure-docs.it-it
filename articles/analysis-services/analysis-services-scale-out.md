---
title: Ridimensionamento orizzontale di Azure Analysis Services | Microsoft Docs
description: Eseguire la replica di Azure Analysis Services server con scalabilità orizzontale. Le query client possono quindi essere distribuite tra più repliche di query in un pool di query con scalabilità orizzontale.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 1b40238dfc579e42d0389ae14fdea4b5692ede06
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572632"
---
# <a name="azure-analysis-services-scale-out"></a>Ridimensionamento orizzontale di Azure Analysis Services

Con la scalabilità orizzontale, le query client possono essere distribuite tra più *repliche di query* in un *pool di query*, riducendo i tempi di risposta durante i carichi di lavoro di query elevati. È anche possibile separare l'elaborazione dal pool di query, assicurando così che le prestazioni delle query dei client non vengano influenzate negativamente dalle operazioni di elaborazione. Il ridimensionamento orizzontale può essere configurato nel portale di Azure o tramite l'API REST di Analysis Services.

Il ridimensionamento orizzontale è disponibile per i server del piano tariffario Standard. Ogni replica di query viene fatturata alla stessa tariffa del server. Tutte le repliche di query vengono create nella stessa area del server. Il numero di repliche di query che è possibile configurare dipende dall'area in cui risiede il server. Per altre informazioni, vedere [Disponibilità per area geografica](analysis-services-overview.md#availability-by-region). Il ridimensionamento orizzontale non aumenta la quantità di memoria disponibile per il server. Per aumentare la memoria, è necessario aggiornare il piano. 

## <a name="why-scale-out"></a>Vantaggi della scalabilità orizzontale

In una distribuzione di server tipica, un server funge sia da server di elaborazione che da server di query. Se il numero di query client verso i modelli presenti nel server supera il numero di unità di elaborazione query (QPU, Query Processing Unit) del piano del server o se l'elaborazione dei modelli avviene contemporaneamente a carichi di lavoro di query elevati, le prestazioni possono peggiorare. 

Con la scalabilità orizzontale è possibile creare un pool di query con fino a sette risorse di replica query aggiuntive (otto in totale, incluso il server *primario* ). È possibile ridimensionare il numero di repliche nel pool di query per soddisfare le richieste QPU in momenti critici ed è possibile separare un server di elaborazione dal pool di query in qualsiasi momento. 

Indipendentemente dal numero di repliche di query presenti in un pool di query, i carichi di lavoro di elaborazione non sono distribuiti tra le repliche di query. Il server primario funge da server di elaborazione. Le repliche di query servono solo query sui database modello sincronizzati tra il server primario e ogni replica nel pool di query. 

Quando si aumenta la scalabilità orizzontale, possono essere necessari fino a cinque minuti per aggiungere le nuove repliche di query al pool di query. Quando tutte le nuove repliche di query sono in esecuzione, le nuove connessioni client vengono sottoposte a bilanciamento del carico tra le risorse nel pool di query. Le connessioni client esistenti non vengono modificate dalla risorsa alla quale sono attualmente connesse. Durante il ridimensionamento verticale, tutte le connessioni client esistenti a una risorsa del pool di query che viene rimossa dal pool di query vengono terminate. I client possono riconnettersi a una risorsa del pool di query rimanente.

## <a name="how-it-works"></a>Funzionamento

Quando si configura la scalabilità orizzontale per la prima volta, i database modello nel server primario vengono sincronizzati *automaticamente* con le nuove repliche in un nuovo pool di query. La sincronizzazione automatica viene eseguita una sola volta. Durante la sincronizzazione automatica, i file di dati del server primario (crittografati inattivi nell'archivio BLOB) vengono copiati in una seconda posizione, crittografati anche inattivi nell'archivio BLOB. Le repliche nel pool di query vengono quindi *idratate* con i dati del secondo set di file. 

Mentre una sincronizzazione automatica viene eseguita solo quando si esegue la scalabilità orizzontale di un server per la prima volta, è anche possibile eseguire una sincronizzazione manuale. La sincronizzazione garantisce che i dati sulle repliche nel pool di query corrispondano a quelli del server primario. Quando si elaborano (aggiornano) i modelli nel server primario, una sincronizzazione deve essere eseguita *dopo* il completamento delle operazioni di elaborazione. Questa sincronizzazione copia i dati aggiornati dai file del server primario nell'archivio BLOB al secondo set di file. Le repliche nel pool di query vengono quindi idratate con i dati aggiornati dal secondo set di file nell'archivio BLOB. 

Quando si esegue un'operazione di scalabilità orizzontale successiva, ad esempio aumentando il numero di repliche nel pool di query da due a cinque, le nuove repliche vengono idratate con i dati del secondo set di file nell'archivio BLOB. Nessuna sincronizzazione. Se quindi si esegue una sincronizzazione dopo la scalabilità orizzontale, le nuove repliche nel pool di query verrebbero idratate due volte, un'idratazione ridondante. Quando si esegue un'operazione di scalabilità orizzontale successiva, è importante tenere presente quanto segue:

* Eseguire una sincronizzazione *prima dell'operazione di scalabilità orizzontale* per evitare l'idratazione ridondante delle repliche aggiunte. Non sono consentite operazioni simultanee di sincronizzazione e scalabilità orizzontale in esecuzione nello stesso momento.

* Quando si automatizzano le operazioni di elaborazione *e* scalabilità orizzontale, è importante innanzitutto elaborare i dati nel server primario, quindi eseguire una sincronizzazione e quindi eseguire l'operazione di scalabilità orizzontale. Questa sequenza garantisce un effetto minimo sulle risorse di memoria e QPU.

* La sincronizzazione è consentita anche quando non sono presenti repliche nel pool di query. Se si esegue la scalabilità orizzontale da zero a una o più repliche con nuovi dati da un'operazione di elaborazione sul server primario, eseguire prima la sincronizzazione senza repliche nel pool di query e quindi eseguire la scalabilità orizzontale. La sincronizzazione prima della scalabilità orizzontale evita l'idratazione ridondante delle repliche appena aggiunte.

* Quando si elimina un database modello dal server primario, quest'ultima non viene automaticamente eliminata dalle repliche nel pool di query. È necessario eseguire un'operazione di sincronizzazione usando il comando [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) di PowerShell che rimuove il file o i file per quel database dal percorso di archiviazione BLOB condiviso della replica e quindi Elimina il database modello nelle repliche nel pool di query. Per determinare se un database modello esiste nelle repliche nel pool di query, ma non nel server primario, assicurarsi che l'impostazione **separa il server di elaborazione dal pool di query** sia impostata su **Sì**. Utilizzare quindi SSMS per connettersi al server primario utilizzando il qualificatore `:rw` per verificare se il database esiste. Connettersi quindi alle repliche nel pool di query connettendosi senza il qualificatore `:rw` per verificare se esiste anche lo stesso database. Se il database è presente nelle repliche nel pool di query ma non nel server primario, eseguire un'operazione di sincronizzazione.   

* Quando si rinomina un database nel server primario, è necessario eseguire un passaggio aggiuntivo per verificare che il database sia correttamente sincronizzato con tutte le repliche. Dopo la ridenominazione, eseguire una sincronizzazione usando il comando [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) specificando il parametro `-Database` con il nome del database precedente. Questa sincronizzazione rimuove il database e i file con il nome precedente dalle repliche. Quindi eseguire un'altra sincronizzazione specificando il parametro `-Database` con il nuovo nome del database. La seconda sincronizzazione copia il database appena denominato nel secondo set di file e idrata tutte le repliche. Queste sincronizzazioni non possono essere eseguite usando il comando Sincronizza modello nel portale.

### <a name="separate-processing-from-query-pool"></a>Separare l'elaborazione dal pool di query

Per ottenere prestazioni ottimali sia delle operazioni di elaborazione che delle operazioni di query, è possibile scegliere di separare il server di elaborazione dal pool di query. Una volta separate, le nuove connessioni client vengono assegnate alle repliche di query solo nel pool di query. Se le operazioni di elaborazione richiedono pochi minuti, è possibile scegliere di separare il server di elaborazione dal pool di query solo per il tempo necessario per eseguire le operazioni di elaborazione e sincronizzazione e quindi includerlo nuovamente nel pool di query. Quando si separa il server di elaborazione dal pool di query o se lo si aggiunge nuovamente nel pool di query, possono essere necessari fino a cinque minuti per il completamento dell'operazione.

## <a name="monitor-qpu-usage"></a>Monitorare l'utilizzo di QPU

Per determinare se per il server è necessario un ridimensionamento orizzontale, monitorare il server nel portale di Azure tramite le metriche. Se le QPU si esauriscono regolarmente, significa che il numero di query verso i modelli supera il limite di QPU per il piano. La metrica relativa alla lunghezza della coda dei processi del pool di query aumenta anche quando il numero di query nella coda del pool di thread di query supera le QPU disponibili. 

Un'altra metrica efficace da controllare è la media di QPU per ServerResourceType. Questa metrica confronta il numero medio di QPU per il server primario con quello del pool di query. 

![Metriche di scalabilità orizzontale delle query](media/analysis-services-scale-out/aas-scale-out-monitor.png)

### <a name="to-configure-qpu-by-serverresourcetype"></a>Per configurare QPU by ServerResourceType
1. In un grafico a linee metrica fare clic su **Aggiungi metrica**. 
2. In **risorsa**selezionare il server, in **spazio dei nomi metrica**, selezionare **Analysis Services metrica standard**, quindi in **metrica**, selezionare **QPU**, quindi in **aggregazione**selezionare **AVG**. 
3. Fare clic su **applica suddivisione**. 
4. In **valori**selezionare **ServerResourceType**.  

Per altre informazioni, vedere [Monitorare le metriche dei server](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Configurare il ridimensionamento orizzontale

### <a name="in-azure-portal"></a>Nel portale di Azure

1. Nel portale fare clic su **scale-out**. Usare il dispositivo di scorrimento per selezionare il numero di server di replica di query. Il numero di repliche scelto viene aggiunto al server esistente.  

2. In **Separare il server di elaborazione dal pool di query** selezionare Sì per escludere il server di elaborazione dal server di query. Le [connessioni](#connections) client che utilizzano la stringa di connessione predefinita (senza `:rw`) vengono reindirizzate alle repliche nel pool di query. 

   ![Dispositivo di scorrimento di ridimensionamento orizzontale](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Fare clic su **Salva** per effettuare il provisioning dei nuovi server di replica di query. 

Quando si configura per la prima volta la scalabilità orizzontale per un server, i modelli nel server primario vengono sincronizzati automaticamente con le repliche nel pool di query. La sincronizzazione automatica si verifica solo una volta, quando si configura per la prima volta la scalabilità orizzontale in una o più repliche. Le successive modifiche al numero di repliche sullo stesso server *non attiverà un'altra sincronizzazione automatica*. La sincronizzazione automatica non verrà rieseguita anche se si imposta il server su zero repliche e quindi si aumenta di nuovo la scalabilità orizzontale a un numero qualsiasi di repliche. 

## <a name="synchronize"></a>Sincronizzare 

Le operazioni di sincronizzazione devono essere eseguite manualmente o tramite l'API REST.

### <a name="in-azure-portal"></a>Nel portale di Azure

In **Panoramica** > modello > **Sincronizza modello**.

![Dispositivo di scorrimento di ridimensionamento orizzontale](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>API REST

Usare l'operazione **sync**.

#### <a name="synchronize-a-model"></a>Sincronizzare un modello   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Ottenere lo stato di sincronizzazione  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Codici di stato restituiti:


|Codice  |Descrizione  |
|---------|---------|
|-1     |  Non valido       |
|0     | La replica        |
|1     |  Reidratanti       |
|2     |   Completed       |
|3     |   Non riuscito      |
|4     |    Finalizzazione     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Prima di usare PowerShell, [installare o aggiornare il modulo Azure PowerShell più recente](/powershell/azure/install-az-ps). 

Per eseguire la sincronizzazione, usare [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Per impostare il numero di repliche di query, usare [set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Specificare il parametro facoltativo `-ReadonlyReplicaCount`.

Per separare il server di elaborazione dal pool di query, usare [set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Consente di specificare il parametro `-DefaultConnectionMode` facoltativo da utilizzare `Readonly`.

Per altre informazioni, vedere [uso di un'entità servizio con il modulo AZ. AnalysisServices](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>Connessioni

Nella pagina Panoramica del server sono presenti due nomi di server. Se il ridimensionamento orizzontale non è stato ancora configurato per un server, entrambi i nomi di server funzionano allo stesso modo. Dopo che per un server è stato configurato il ridimensionamento orizzontale, è necessario specificare il nome del server appropriato a seconda del tipo di connessione. 

Per le connessioni client destinate agli utenti finali, ad esempio Power BI Desktop, Excel e le applicazioni personalizzate, usare il **nome del server**. 

Per SSMS, Visual Studio e le stringhe di connessione in PowerShell, app per le funzioni di Azure e AMO, usare il **nome del server di gestione**. Il nome del server di gestione include un qualificatore (lettura e scrittura) `:rw` speciale. Tutte le operazioni di elaborazione vengono eseguite nel server di gestione (primario).

![Nomi dei server](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Scalabilità verticale, riduzione e scalabilità orizzontale

È possibile modificare il piano tariffario in un server con più repliche. Lo stesso piano tariffario si applica a tutte le repliche. Un'operazione di ridimensionamento arresterà prima tutte le repliche in una sola volta e quindi mostrerà tutte le repliche nel nuovo piano tariffario.

## <a name="troubleshoot"></a>Risolvere problemi

**Problema:** viene restituito un errore per segnalare che **non è possibile trovare l'istanza del server '\<nome del server>' in modalità di connessione 'ReadOnly'.**

**Soluzione:** Quando si seleziona l'opzione **separa il server di elaborazione dal pool di query** , le connessioni client che usano la stringa di connessione predefinita (senza `:rw`) vengono reindirizzate alle repliche del pool di query. Se le repliche nel pool di query non sono ancora online perché la sincronizzazione non è stata ancora completata, le connessioni client reindirizzate possono avere esito negativo. Quando si esegue una sincronizzazione, per evitare errori di connessione, nel pool di query devono essere presenti almeno due server. Ogni server viene sincronizzato singolarmente, mentre gli altri rimangono online. Se si sceglie di non tenere il server di elaborazione all'interno del pool di query durante l'elaborazione, è possibile rimuoverlo dal pool per l'elaborazione e quindi riaggiungerlo al termine di questa, ma prima della sincronizzazione. Usare le metriche di memoria e di QPU per monitorare lo stato della sincronizzazione.



## <a name="related-information"></a>Informazioni correlate

[Monitorare le metriche dei server](analysis-services-monitor.md)   
[Gestire Azure Analysis Services](analysis-services-manage.md) 
