---
title: 'Avvio rapido: Portale Web di Advisor metriche'
titleSuffix: Azure Cognitive Services
description: Informazioni su come iniziare a usare il portale Web di Advisor metriche.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/30/2020
ms.author: aahi
ms.openlocfilehash: 004685a50e2413c29528ad3aca08a0150843a8aa
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91631370"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>Avvio rapido: Monitorare la prima metrica con il portale Web

Quando si effettua il provisioning di un'istanza di Advisor metriche, è possibile usare le API e l'area di lavoro basata sul Web per lavorare con il servizio. L'area di lavoro basata sul Web è un modo semplice per imparare rapidamente a usare il servizio. Offre anche un'interfaccia visiva per configurare le impostazioni, personalizzare il modello ed eseguire l'analisi della causa radice. 

* Eseguire l'onboarding dei dati delle metriche
* Visualizzare le metriche e le visualizzazioni
* Ottimizzare le configurazioni del rilevamento
* Esplorare le informazioni di diagnostica
* Creare e sottoscrivere avvisi relativi alle anomalie

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* Una volta che si dispone di una sottoscrizione di Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Creare una risorsa di Advisor metriche"  target="_blank">creare una risorsa di Advisor metriche <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per distribuire l'istanza di Advisor metriche.  

    
> [!TIP]
> * La distribuzione della risorsa di Advisor metriche può richiedere dai 10 ai 30 minuti. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
> * Se si vuole usare l'API REST per interagire con il servizio, sono necessari la chiave e l'endpoint della risorsa creata. È possibile trovarli nella scheda **Chiavi ed endpoint** della risorsa creata.

In questo documento si usa un database SQL come esempio per la creazione del primo monitoraggio.

## <a name="sign-in-to-your-workspace"></a>Accedere all'area di lavoro

Dopo aver distribuito la risorsa, accedere al [portale Advisor metriche](https://go.microsoft.com/fwlink/?linkid=2143774). Selezionare l'area di lavoro per avviare il monitoraggio delle metriche. 
 
Attualmente è possibile creare una risorsa di Advisor metriche in ogni area disponibile. È possibile spostare le aree di lavoro nel portale Advisor metriche in qualsiasi momento.


## <a name="onboard-time-series-data"></a>Eseguire l'onboarding dei dati delle serie temporali

Advisor metriche fornisce connettori per diverse origini dati, ad esempio database SQL, Esplora dati di Azure e archiviazione tabelle di Azure. La procedura per la connessione dei dati è simile per i diversi connettori, sebbene alcuni parametri di configurazione possano variare. Per conoscere i parametri necessari per le origini dati specifiche, vedere [Connettere i dati da origini diverse](../data-feeds-from-different-sources.md).

Questa guida di avvio rapido usa un database SQL come esempio. È anche possibile inserire i propri dati seguendo la stessa procedura.

Per iniziare, accedere all'area di lavoro di Advisor metriche con il proprio account Active Directory. Nella pagina di destinazione selezionare la **directory**, la **sottoscrizione** e l'**area di lavoro** appena create, quindi fare clic su **Inizia**. Una volta caricata la pagina principale dell'area di lavoro, selezionare **Add data feed** (Aggiungi feed di dati) dal menu a sinistra.

### <a name="data-schema-requirements-and-configuration"></a>Requisiti e configurazione dello schema di dati

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings"></a>Configurare le impostazioni di connessione

> [!TIP]
> Per informazioni dettagliate sui parametri disponibili, vedere [Come aggiungere feed di dati](../how-tos/onboard-your-data.md).

Aggiungere il feed di dati connettendosi all'origine dati della serie temporale. Selezionare prima di tutto i parametri seguenti:

* **Tipo di origine**: il tipo di origine dati in cui sono archiviati i dati della serie temporale.
* **Granularità**: l'intervallo tra i punti dati consecutivi nei dati della serie temporale, ad esempio Annuale, Mensile, Giornaliero. L'intervallo più piccolo supportato dalla personalizzazione è di 60 secondi.
* **Ingest data since (UTC)** (Inserisci dati dalle ore - UTC): l'ora di inizio del primo timestamp da inserire. 


Specificare quindi la **stringa di connessione** con le credenziali per l'origine dati e una **query** personalizzata. La query viene usata per specificare i dati da inserire e convertire nello schema richiesto.

[!INCLUDE [query requirements](../includes/query-requirements.md)]

:::image type="content" source="../media/connection-settings.png" alt-text="Impostazioni di connessione" lightbox="../media/connection-settings.png":::


### <a name="verify-the-connection-and-load-the-data-schema"></a>Verificare la connessione e caricare lo schema di dati

Dopo aver creato la stringa di connessione e la stringa di query, selezionare **Verify and get schema** (Verifica e ottieni lo schema) per verificare la connessione ed eseguire la query per ottenere lo schema di dati dall'origine dati. Questa operazione normalmente richiede alcuni secondi, a seconda della connessione all'origine dati. Se si verifica un errore in questa fase, verificare che:

1. La stringa di connessione e la query siano corrette.
2. L'istanza di Advisor metriche sia in grado di connettersi all'origine dati in presenza di impostazioni del firewall.

### <a name="schema-configuration"></a>Configurazione dello schema

Dopo che lo schema di dati è stato caricato, con un aspetto simile al seguente, selezionare i campi appropriati.


|Selezione  |Descrizione  |Note  |
|---------|---------|---------|
|**Timestamp**     | Timestamp di un punto dati. Se omesso, Advisor metriche userà il timestamp quando il punto dati viene inserito. Per ogni feed di dati è possibile specificare al massimo una colonna come timestamp.        | facoltativo. Deve essere specificato con al massimo una colonna.       |
|**Measure**     |  Valori numerici nel feed di dati. Per ogni feed di dati è possibile specificare più misure, ma almeno una colonna deve essere selezionata come misura.        | Deve essere specificato con almeno una colonna.        |
|**Dimensione**     | Valori di categoria. Una combinazione di valori diversi identifica una particolare serie temporale unidimensionale, ad esempio paese, lingua, tenant. È possibile selezionare da zero a un numero arbitrario di colonne come dimensioni. Nota: se si seleziona una colonna non di tipo stringa come dimensione, prestare attenzione all'esplosione della dimensione. | facoltativo.        |
|**Ignora**     | Ignora la colonna selezionata.        |         |


:::image type="content" source="../media/schema-configuration.png" alt-text="Impostazioni di connessione" lightbox="../media/schema-configuration.png":::

### <a name="automatic-roll-up-settings"></a>Impostazioni di rollup automatico

> [!IMPORTANT]
> Se si vuole abilitare l'**analisi della causa radice** e altre funzionalità di diagnostica, è necessario configurare le impostazioni di rollup automatico. Una volta abilitate, le impostazioni di rollup automatico non possono essere modificate.

Advisor metriche può eseguire automaticamente l'aggregazione (SUM/MAX/MIN...) su ogni dimensione durante l'inserimento, quindi creare una gerarchia che verrà usata nell'analisi della causa radice e in altre funzionalità di diagnostica. Per altre informazioni, vedere [Impostazioni di rollup automatico](../how-tos/onboard-your-data.md#automatic-roll-up-settings).

Assegnare al feed di dati un nome personalizzato, che verrà visualizzato nell'area di lavoro. Fare clic su **Invia**. 

## <a name="tune-detection-configuration"></a>Ottimizzare la configurazione del rilevamento

Dopo l'aggiunta del feed di dati, Advisor metriche cercherà di inserire i dati delle metriche dalla data di inizio specificata. Il completamento dell'inserimento dei dati richiederà del tempo. È possibile controllarne lo stato facendo clic su **Ingestion progress** (Stato dell'inserimento) nella parte superiore della pagina del feed di dati. Se vengono inseriti dei dati, Advisor metriche applica il rilevamento e continua a monitorare l'origine per rilevare nuovi dati.

Una volta applicato il rilevamento, fare clic su una delle metriche elencate nel feed di dati per trovare la **pagina dei dettagli della metrica** in cui è possibile: 
- Controllare le visualizzazioni di tutte le sezioni della serie temporale in questa metrica
- Aggiornare la configurazione del rilevamento per soddisfare i risultati previsti
- Configurare la notifica per le anomalie rilevate

:::image type="content" source="../media/metric-details.png" alt-text="Impostazioni di connessione" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>Visualizzare le informazioni di diagnostica

Dopo l'ottimizzazione della configurazione del rilevamento, le anomalie rilevate dovrebbero riflettere le anomalie effettive nei dati. Advisor metriche esegue l'analisi sulla base di metriche multidimensionali, come il clustering delle anomalie, la correlazione degli eventi imprevisti e l'analisi della causa radice. Usare queste funzionalità per analizzare e diagnosticare gli eventi imprevisti nei dati.

Per visualizzare le informazioni di diagnostica, fare clic sui puntini rossi nelle visualizzazioni delle serie temporali, che rappresentano le anomalie rilevate. Verrà visualizzata una finestra che contiene un collegamento alla pagina di analisi degli eventi imprevisti. 

:::image type="content" source="../media/incident-link.png" alt-text="Impostazioni di connessione" lightbox="../media/incident-link.png":::

Facendo clic sul collegamento si passerà alla pagina di analisi degli eventi imprevisti, che analizza le anomalie corrispondenti con una serie di informazioni dettagliate di diagnostica. Nella parte superiore sono presenti statistiche sull'evento imprevisto, ad esempio la **gravità**, le **anomalie interessate**, l'**ora di inizio** e l'**ora di fine**. 

Si vedranno quindi l'anomalia predecessore dell'evento imprevisto e un suggerimento automatico sulla causa radice. Questo suggerimento viene generato analizzando l'albero degli eventi imprevisti di tutte le anomalie correlate, tra cui deviazione, distribuzione e contributo alle anomalie padre. 

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Impostazioni di connessione" lightbox="../media/incident-diagnostic.png":::

In base a queste informazioni è già possibile ottenere una visione d'insieme di ciò che accade e l'impatto dell'evento imprevisto, oltre alla causa radice più probabile, in modo da poter agire immediatamente per risolvere l'incidente il più presto possibile. 

Ma è anche possibile esplorare più informazioni dettagliate di diagnostica sfruttando funzionalità aggiuntive per eseguire il drill-down delle anomalie per dimensione, visualizzare anomalie simili e confrontare metriche diverse. Per altre informazioni, vedere [Diagnosticare un evento imprevisto](../how-tos/diagnose-incident.md). 

## <a name="get-notified-when-new-anomalies-are-found"></a>Ricevere una notifica quando vengono rilevate nuove anomalie

Se si vuole ricevere un avviso quando viene rilevata un'anomalia nei dati, si può creare una sottoscrizione per una o più metriche. Advisor metriche usa gli hook per inviare avvisi. Sono supportati tre tipi di hook: hook di posta elettronica, webhook e Azure DevOps. Come esempio verrà usato un webhook. 

### <a name="create-a-web-hook"></a>Creare un webhook

Un webhook è il punto di ingresso da cui rilevare un'anomalia in modo programmatico tramite il servizio Advisor metriche, che chiama un'API fornita dall'utente quando viene attivato un avviso. Per informazioni dettagliate su come creare un hook, vedere la sezione **Creare un hook** in [Procedura dettagliata: Configurare avvisi e ottenere notifiche usando un hook](../how-tos/alerts.md#create-a-hook). 

### <a name="configure-alert-settings"></a>Configurare le impostazioni degli avvisi

Dopo la creazione di un hook, un'impostazione degli avvisi determina quali notifiche devono essere inviate e in che modo. È possibile configurare più impostazioni degli avvisi per ogni metrica. Due impostazioni importanti sono **Alert for** (Avvisa per), che specifica le anomalie da includere, e **Filter anomaly options** (Filtra opzioni anomalie), che definisce quali anomalie includere nell'avviso. Per altre informazioni, vedere la sezione **Aggiungere o modificare le impostazioni degli avvisi** in [Procedura dettagliata: Configurare avvisi e ottenere notifiche usando un hook](../how-tos/alerts.md#add-or-edit-alert-settings).


## <a name="next-steps"></a>Passaggi successivi

- [Eseguire l'onboarding dei feed di dati](../how-tos/onboard-your-data.md)
    - [Gestire feed di dati](../how-tos/manage-data-feeds.md)
    - [Configurazioni per origini dati diverse](../data-feeds-from-different-sources.md)
- [Usare l'API REST o le librerie client](rest-api.md)
- [Configurare le metriche e ottimizzare la configurazione del rilevamento](../how-tos/configure-metrics.md)
