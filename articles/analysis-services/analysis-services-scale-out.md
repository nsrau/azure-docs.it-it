---
title: Ridimensionamento orizzontale di Azure Analysis Services | Microsoft Docs
description: Replicare server di Azure Analysis Services con ridimensionamento orizzontale
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5524645153db0468076cc9b567965bff79d915cb
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192295"
---
# <a name="azure-analysis-services-scale-out"></a>Ridimensionamento orizzontale di Azure Analysis Services

Con scalabilità orizzontale, le query client possono essere distribuite tra più *repliche delle query* in un *pool di query*, riducendo i tempi di risposta durante i carichi di lavoro di query elevati. È anche possibile separare l'elaborazione dal pool di query, assicurando così che le prestazioni delle query dei client non vengano influenzate negativamente dalle operazioni di elaborazione. Il ridimensionamento orizzontale può essere configurato nel portale di Azure o tramite l'API REST di Analysis Services.

Il ridimensionamento orizzontale è disponibile per i server del piano tariffario Standard. Ogni replica di query viene fatturata alla stessa tariffa del server. Tutte le repliche di query vengono create nella stessa area del server. Il numero di repliche di query che è possibile configurare dipende dall'area in cui risiede il server. Per altre informazioni, vedere [Disponibilità per area geografica](analysis-services-overview.md#availability-by-region). Il ridimensionamento orizzontale non aumenta la quantità di memoria disponibile per il server. Per aumentare la memoria, è necessario aggiornare il piano. 

## <a name="why-scale-out"></a>Il motivo per cui scalabilità?

In una distribuzione di server tipica, un server funge sia da server di elaborazione che da server di query. Se il numero di query client verso i modelli presenti nel server supera il numero di unità di elaborazione query (QPU, Query Processing Unit) del piano del server o se l'elaborazione dei modelli avviene contemporaneamente a carichi di lavoro di query elevati, le prestazioni possono peggiorare. 

Con scalabilità orizzontale, è possibile creare un pool di query con le risorse di replica fino a sette query aggiuntive (otto totale, incluso il *primaria* server). È possibile aumentare il numero di repliche nel pool di query per soddisfare le richieste di QPU nei momenti critici, ed è possibile separare un server di elaborazione dal pool di query in qualsiasi momento. 

Indipendentemente dal numero di repliche di query presenti in un pool di query, i carichi di lavoro di elaborazione non sono distribuiti tra le repliche di query. Il server primario funge da server di elaborazione. Le repliche di query servono solo le query verso i database del modello di sincronizzazione tra il server primario e ogni replica nel pool di query. 

Quando la scalabilità orizzontale, può richiedere fino a cinque minuti per le nuove repliche di query in modo incrementale da aggiungere al pool di query. Quando tutte le nuove repliche di query sono attivi e in esecuzione, nuove connessioni client sono con carico bilanciato tra le risorse nel pool di query. Le connessioni client esistenti non vengono modificate dalla risorsa alla quale sono attualmente connesse. Durante il ridimensionamento verticale, tutte le connessioni client esistenti a una risorsa del pool di query che viene rimossa dal pool di query vengono terminate. I client possono riconnettersi a una risorsa di pool di query rimanenti.

## <a name="how-it-works"></a>Funzionamento

Durante la configurazione di scalabilità orizzontale la prima volta, i database modello sul server primario vengono *automaticamente* sincronizzato con le nuove repliche in un nuovo pool di query. Sincronizzazione automatica si verifica una sola volta. Durante la sincronizzazione automatica, i file di dati del server primario (crittografati quando inattivi in archiviazione blob) vengono copiati in un secondo percorso, anche crittografato quando inattivi in archiviazione blob. Le repliche nel pool di query sono quindi *idratato* con dati del secondo set di file. 

Mentre una sincronizzazione automatica viene eseguita solo quando è scalare orizzontalmente un server per la prima volta, è anche possibile eseguire una sincronizzazione manuale. La sincronizzazione assicura i dati nelle repliche nel pool di query corrisponde a quello del server primario. Durante l'elaborazione di modelli (Aggiorna) nel server primario, è necessario eseguire una sincronizzazione *dopo* vengono completate le operazioni di elaborazione. Questa sincronizzazione copia i dati aggiornati dai file del server primario nell'archiviazione blob per il secondo set di file. Le repliche nel pool di query vengono quindi idratate con i dati aggiornati il secondo set di file nell'archivio blob. 

Quando si esegue una successiva operazione di scalabilità orizzontale, ad esempio, l'aumento del numero di repliche nel pool di query da due a cinque, le nuove repliche sono idratate con i dati del secondo set di file nell'archivio blob. Non vi è alcuna sincronizzazione. Se si intende eseguire una sincronizzazione dopo la scalabilità orizzontale, le nuove repliche nel pool di query saranno idratati due volte: un'idratazione ridondanti. Quando si esegue una successiva operazione di scalabilità orizzontale, è importante da tenere presenti:

* Eseguire una sincronizzazione *prima dell'operazione di scalabilità orizzontale* per evitare l'attivazione con ridondanza di repliche aggiunte. Sincronizzazione simultanea e le operazioni di scalabilità orizzontale in esecuzione nello stesso momento non sono consentite.

* Quando si automatizza entrambi elaborazione *e* operazioni di scalabilità orizzontale, è importante prima di elaborare i dati nel server primario, quindi eseguire una sincronizzazione e quindi eseguire l'operazione di scalabilità orizzontale. Questa sequenza assicura un impatto minimo sulle risorse di memoria e QPU.

* Anche se non sono presenti repliche nel pool di query, è consentita la sincronizzazione. Se sono scalabilità orizzontale da zero a una o più repliche con i nuovi dati da un'operazione di elaborazione nel server primario, eseguire prima di tutto la sincronizzazione senza repliche nel pool di query e quindi scalabilità orizzontale. La sincronizzazione prima di scalabilità orizzontale consente di evitare un'attivazione ridondante delle repliche appena aggiunte.

* Quando si elimina un database modello dal server primario, questo non ottenere eliminato automaticamente da repliche nel pool di query. È necessario eseguire un'operazione di sincronizzazione tramite il [sincronizzazione AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) comandi di PowerShell che rimuove i file/sec per un database dalla posizione di archiviazione blob di condiviso della replica e quindi Elimina il modello database nelle repliche nel pool di query. Per determinare se un database modello presente nelle repliche nel pool di query, ma non nel server primario, assicurarsi che il **separare il server di elaborazione dal pool di query** impostazione prevede **Yes**. Quindi utilizzare SSMS per connettersi al server primario usando la `:rw` qualificatore per vedere se il database esista. Quindi connettersi alle repliche nel pool di query tramite la connessione senza le `:rw` qualificatore per vedere se esiste anche nello stesso database. Se il database è presente nelle repliche nel pool di query ma non nel server primario, eseguire un'operazione di sincronizzazione.   

* Quando si rinomina un database nel server primario, è un passaggio aggiuntivo necessario per assicurare che il database è sincronizzato correttamente a tutte le repliche. Dopo la ridenominazione, eseguire una sincronizzazione tramite il [sincronizzazione AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) comando specificando il `-Database` parametro con il vecchio nome del database. Questa sincronizzazione rimuove il database e i file con il nome precedente da tutte le repliche. Eseguire quindi un'altra sincronizzazione specifica il `-Database` parametro con il nuovo nome del database. La sincronizzazione secondo il database copiato nel nuovo nome per il secondo set di file e generati idratano tutte le repliche. Impossibile eseguire il comando Sincronizza modello nel portale le sincronizzazioni.

### <a name="separate-processing-from-query-pool"></a>Separare l'elaborazione dal pool di query

Per ottenere prestazioni ottimali sia delle operazioni di elaborazione che delle operazioni di query, è possibile scegliere di separare il server di elaborazione dal pool di query. Quando separati, le nuove connessioni client vengono assegnate alle repliche di query nel pool di query solo. Se le operazioni di elaborazione richiedono pochi minuti, è possibile scegliere di separare il server di elaborazione dal pool di query solo per il tempo necessario per eseguire le operazioni di elaborazione e sincronizzazione e quindi includerlo nuovamente nel pool di query. Durante la separazione di server di elaborazione dal pool di query o l'aggiunta di nuovo nel pool di query può richiedere fino a cinque minuti per completare l'operazione.

## <a name="monitor-qpu-usage"></a>Monitorare l'utilizzo di QPU

Per determinare se per il server è necessario un ridimensionamento orizzontale, monitorare il server nel portale di Azure tramite le metriche. Se le QPU si esauriscono regolarmente, significa che il numero di query verso i modelli supera il limite di QPU per il piano. La metrica relativa alla lunghezza della coda dei processi del pool di query aumenta anche quando il numero di query nella coda del pool di thread di query supera le QPU disponibili. 

Un'altra buona metrica per il controllo è medio QPU da ServerResourceType. Questa metrica Confronta QPU medio per il server primario con quella del pool di query. 

![Scalabilità orizzontale delle metriche delle query](media/analysis-services-scale-out/aas-scale-out-monitor.png)

### <a name="to-configure-qpu-by-serverresourcetype"></a>Per configurare QPU da ServerResourceType
1. In un grafico a linee le metriche, fare clic su **Aggiungi metrica**. 
2. In **RESOURCE**, selezionare il server, quindi **dello spazio dei nomi di METRICA**, selezionare **metriche standard di Analysis Services**, quindi nella **METRICA**, Selezionare **QPU**, quindi nel **aggregazione**, selezionare **Avg**. 
3. Fare clic su **applica la suddivisione**. 
4. Nelle **i valori**, selezionare **ServerResourceType**.  

Per altre informazioni, vedere [Monitorare le metriche dei server](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Configurare il ridimensionamento orizzontale

### <a name="in-azure-portal"></a>Nel portale di Azure

1. Nel portale di Azure fare clic su **Aumenta**. Usare il dispositivo di scorrimento per selezionare il numero di server di replica di query. Il numero di repliche scelto viene aggiunto al server esistente.

2. In **Separare il server di elaborazione dal pool di query** selezionare Sì per escludere il server di elaborazione dal server di query. Client [connessioni](#connections) usando la stringa di connessione predefinito (senza `:rw`) vengono reindirizzate alle repliche nel pool di query. 

   ![Dispositivo di scorrimento di ridimensionamento orizzontale](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Fare clic su **Salva** per effettuare il provisioning dei nuovi server di replica di query. 

Durante la configurazione di scalabilità orizzontale per un server la prima volta, i modelli nel server primario vengono sincronizzati automaticamente con le repliche nel pool di query. Sincronizzazione automatica si verifica solo una volta, quando si configura prima scalabilità orizzontale fino a una o più repliche. Le successive modifiche al numero di repliche nello stesso server *non attiva un'altra sincronizzazione automatica*. Sincronizzazione automatica non verrà eseguita nuovamente, anche se si imposta il server a zero le repliche e quindi nuovamente scalabilità orizzontale a qualsiasi numero di repliche. 

## <a name="synchronize"></a>Sincronizza 

Operazioni di sincronizzazione devono essere eseguite manualmente o tramite l'API REST.

### <a name="in-azure-portal"></a>Nel portale di Azure

In **Panoramica** > modello > **Sincronizza modello**.

![Dispositivo di scorrimento di ridimensionamento orizzontale](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>API REST

Usare l'operazione **sync**.

#### <a name="synchronize-a-model"></a>Sincronizzare un modello   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Ottenere lo stato di sincronizzazione  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Codici di stato:


|Codice  |DESCRIZIONE  |
|---------|---------|
|-1     |  Non valido       |
|0     | Replica in corso        |
|1     |  La riattivazione       |
|2     |   Completi       |
|3     |   Failed      |
|4     |    Finalizzazione     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Prima di PowerShell, usare [installare o aggiornare il modulo Azure PowerShell più recente](/powershell/azure/install-az-ps). 

Per eseguire la sincronizzazione, usare [sincronizzazione AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Per impostare il numero di repliche di query, utilizzare [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Specificare il parametro facoltativo `-ReadonlyReplicaCount`.

Per separare il server di elaborazione dal pool di query, usare [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Specificare l'opzione facoltativa `-DefaultConnectionMode` parametro per l'utilizzo `Readonly`.

Per altre informazioni, vedere [usando un'entità servizio con il modulo Az.AnalysisServices](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>connessioni

Nella pagina Panoramica del server sono presenti due nomi di server. Se il ridimensionamento orizzontale non è stato ancora configurato per un server, entrambi i nomi di server funzionano allo stesso modo. Dopo che per un server è stato configurato il ridimensionamento orizzontale, è necessario specificare il nome del server appropriato a seconda del tipo di connessione. 

Per le connessioni client destinate agli utenti finali, ad esempio Power BI Desktop, Excel e le applicazioni personalizzate, usare il **nome del server**. 

Per SSMS ed SSDT, nonché per le stringhe di connessione in PowerShell, per le app di Funzioni di Azure e AMO, usare il **nome del server di gestione**. Il nome del server di gestione include un qualificatore (lettura e scrittura) `:rw` speciale. Tutte le operazioni di elaborazione si verificano nel server di gestione (primario).

![Nomi dei server](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>Risolvere problemi

**Problema:** viene restituito un errore per segnalare che **non è possibile trovare l'istanza del server '\<nome del server>' in modalità di connessione 'ReadOnly'.**

**Soluzione:** Quando si seleziona il **separare il server di elaborazione dal pool di query** opzione, le connessioni client usando la stringa di connessione predefinito (senza `:rw`) vengono reindirizzate a repliche di pool di query. Se le repliche nel pool di query non sono ancora online perché la sincronizzazione non è stata ancora completata, le connessioni client reindirizzate possono avere esito negativo. Quando si esegue una sincronizzazione, per evitare errori di connessione, nel pool di query devono essere presenti almeno due server. Ogni server viene sincronizzato singolarmente, mentre gli altri rimangono online. Se si sceglie di non tenere il server di elaborazione all'interno del pool di query durante l'elaborazione, è possibile rimuoverlo dal pool per l'elaborazione e quindi riaggiungerlo al termine di questa, ma prima della sincronizzazione. Usare le metriche di memoria e di QPU per monitorare lo stato della sincronizzazione.



## <a name="related-information"></a>Informazioni correlate

[Monitorare le metriche dei server](analysis-services-monitor.md)   
[Gestire Azure Analysis Services](analysis-services-manage.md) 
