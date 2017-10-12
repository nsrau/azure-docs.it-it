---
title: Esportazione continua dei dati di telemetria da Application Insights | Microsoft Docs
description: "Esportare i dati di diagnostica e di uso nella risorsa di archiviazione in Microsoft Azure e scaricarli da lì."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 5b859200-b484-4c98-9d9f-929713f1030c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: bwren
ms.openlocfilehash: 6ac3bda5101593b5ca66b4c9035e2fdac9d1e833
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="export-telemetry-from-application-insights"></a>Esportare i dati di telemetria da Application Insights
Si vogliono mantenere i dati di telemetria per un periodo più lungo del periodo di mantenimento standard o elaborarli in un modo particolare? A tale scopo, l'esportazione continua è ideale. Gli eventi visualizzati nel portale di Application Insights possono essere esportati nella risorsa di archiviazione di Microsoft Azure in formato JSON. Da qui è possibile scaricare i dati e scrivere qualsiasi tipo di codice necessario per elaborarli.  

L'uso dell'esportazione continua può comportare un costo aggiuntivo. Controllare il [modello di prezzi](http://azure.microsoft.com/pricing/details/application-insights/).

Prima di configurare l'esportazione continua, è necessario prendere in considerazione alcune alternative:

* Il pulsante Esporta nella parte superiore del pannello delle metriche o di ricerca consente di esportare tabelle e grafici in un foglio di calcolo di Excel.

* [Dati di analisi](app-insights-analytics.md) offre un linguaggio avanzato di query per la telemetria che consente anche di esportare i risultati.
* Se si vogliono [esplorare i dati in Power BI](app-insights-export-power-bi.md), non è necessario usare l'esportazione continua.
* L'[API REST di accesso ai dati](https://dev.applicationinsights.io/) consente di accedere ai dati di telemetria a livello di codice.

Con l'esportazione continua i dati vengono copiati nella risorsa di archiviazione, in cui possono rimanere fino a quando si desidera, ma sono ancora disponibili in Application Insights per il [periodo di conservazione](app-insights-data-retention-privacy.md) usuale.

## <a name="setup"></a> Creare un'esportazione continua
1. Nella risorsa di Application Insights per l'app aprire Esportazione continua e scegliere **Aggiungi**:

    ![Scorrere verso il basso e fare clic su Esportazione continua](./media/app-insights-export-telemetry/01-export.png)

2. Scegliere i tipi di dati di telemetria da esportare.

3. Creare o selezionare un [account di archiviazione di Azure](../storage/common/storage-introduction.md) in cui memorizzare i dati.

    > [!Warning]
    > Per impostazione predefinita, il percorso di archiviazione verrà impostato sulla stessa area geografica della risorsa di Application Insights. Se si esegue l'archiviazione in un'area differente, è possibile che vengano applicati addebiti per il trasferimento.

    ![Fare clic su Aggiungi, Destinazione di esportazione, Account di archiviazione e quindi creare un nuovo archivio o scegliere un archivio esistente](./media/app-insights-export-telemetry/02-add.png)

4. Creare o selezionare un contenitore nella risorsa di archiviazione:

    ![Fare clic su Scegli tipi di eventi](./media/app-insights-export-telemetry/create-container.png)

Dopo averla creata, l'esportazione viene avviata. Si ottengono solo i dati ricevuti dopo avere creato l'esportazione.

Può verificarsi un ritardo di circa un'ora prima che i dati vengano visualizzati nella risorsa di archiviazione.

### <a name="to-edit-continuous-export"></a>Per modificare l'esportazione continua

Se si vogliono modificare i tipi di eventi in un secondo momento, è sufficiente modificare l'esportazione:

![Fare clic su Scegli tipi di eventi](./media/app-insights-export-telemetry/05-edit.png)

### <a name="to-stop-continuous-export"></a>Per interrompere l'esportazione continua

Per interrompere l'esportazione, fare clic su Disabilita. Quando si fa clic di nuovo su Abilita, l'esportazione verrà riavviata con nuovi dati. Non si otterranno i dati che arrivano nel portale mentre l'esportazione è stata disabilitata.

Per interrompere l'esportazione in modo permanente, eliminare l'esportazione. Questa operazione non elimina i dati dalla risorsa di archiviazione.

### <a name="cant-add-or-change-an-export"></a>Non si riesce ad aggiungere o modificare un'esportazione?
* Per aggiungere o modificare le esportazioni, è necessario avere i diritti di accesso proprietario, collaboratore o collaboratore di Application Insights. [Informazioni sui ruoli][roles].

## <a name="analyze"></a> Quali eventi si ottengono?
I dati esportati sono dati di telemetria non elaborati ricevuti dall'applicazione, tranne che per l'aggiunta di dati del percorso calcolati dall'indirizzo IP del client.

I dati che il [campionamento](app-insights-sampling.md) ha rimosso non sono inclusi nei dati esportati.

Le altre metriche calcolate non sono incluse. Ad esempio, non si procederà all'esportazione dell'uso medio della CPU, ma si procederà all'esportazione dei dati di telemetria non elaborati a partire dai quali viene calcolata la media.

I dati includono anche i risultati di ogni [test Web di disponibilità](app-insights-monitor-web-app-availability.md) impostato.

> [!NOTE]
> **Campionamento.** Se l'applicazione invia una grande quantità di dati, la funzionalità di campionamento può intervenire e inviare solo una percentuale della telemetria generata. [Altre informazioni sul campionamento.](app-insights-sampling.md)
>
>

## <a name="get"></a> Esaminare i dati
È possibile esaminare lo spazio di archiviazione direttamente nel portale. Fare clic su **Sfoglia**, selezionare l'account di archiviazione e quindi aprire i **Contenitori**.

Per esaminare l'archiviazione di Azure in Visual Studio, aprire **Visualizza**, **Cloud Explorer**. (Se non si dispone di tale comando del menu, è necessario installare l’SDK di Azure: aprire la finestra di dialogo **Nuovo progetto**, espandere Visual C#/Cloud e scegliere **Ottieni Microsoft Azure SDK per .NET**).

Quando si apre l'archivio BLOB, si noterà un contenitore con un set di file BLOB. L'URI di ogni file è derivato dal nome della risorsa di Application Insights, la relativa chiave di strumentazione e tipo/data/ora della telemetria. (Il nome della risorsa viene scritto in minuscolo e la chiave di strumentazione omette i trattini).

![Controllare l'archivio BLOB con uno strumento adatto](./media/app-insights-export-telemetry/04-data.png)

La data e ora sono UTC e lo sono quando i dati di telemetria sono stati depositati nell'archivio, non l'ora in cui sono stati generati. Di conseguenza, se si scrive codice per scaricare i dati, può spostarsi in modo lineare attraverso i dati.

Di seguito è riportato il formato del percorso:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Where

* `blobCreationTimeUtc` è l'ora di creazione del BLOB nell'archivio di gestione temporanea interno
* `blobDeliveryTimeUtc` è l'ora in cui il BLOB viene copiato nell'archivio di destinazione dell'esportazione

## <a name="format"></a> Formato dati
* Ogni BLOB è un file di testo che contiene più righe separate da '\n'. Contiene i dati di telemetria elaborati in un periodo di tempo di circa mezzo minuto.
* Ogni riga rappresenta un punto dati di telemetria, ad esempio una richiesta o una visualizzazione di pagina.
* Ogni riga è un documento JSON non formattato. Se si desidera sedersi a osservare, aprirlo in Visual Studio e scegliere Modifica, Avanzate, File di formato:

![Visualizzare i dati di telemetria con uno strumento adatto](./media/app-insights-export-telemetry/06-json.png)

Gli intervalli di tempo sono espressi in tick, dove 10 000 tick = 1 ms. Questi valori, ad esempio, indicano un tempo di 1 ms per inviare una richiesta dal browser, 3 ms per riceverla e 1,8 s per elaborare la pagina nel browser:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Riferimento dettagliato al modello di dati per i valori e i tipi di proprietà.](app-insights-export-data-model.md)

## <a name="processing-the-data"></a>Elaborazione dei dati
Su scala ridotta è possibile scrivere codice per separare i dati, leggerli in un foglio di calcolo e così via. ad esempio:

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }

Per un esempio di codice più esaustivo, vedere l'articolo relativo all'[uso di un ruolo di lavoro][exportasa].

## <a name="delete"></a>Eliminare i vecchi dati
Si noti che si è responsabili della gestione della capacità di archiviazione ed eliminazione di vecchi dati, se necessario.

## <a name="if-you-regenerate-your-storage-key"></a>Se si rigenera la chiave di archiviazione...
Se si modifica la chiave per l'archiviazione, l'esportazione continua non funzionerà più. Verrà visualizzata una notifica nell'account Azure.

Aprire il pannello Esportazione continua e modificare l'esportazione. Modificare la destinazione di esportazione, ma lasciare selezionata la stessa risorsa di archiviazione. Fare clic su OK per confermare.

![Modificare l'esportazione continua, aprire e chiudere tre destinazioni di esportazione.](./media/app-insights-export-telemetry/07-resetstore.png)

L'esportazione continua verrà riavviata.

## <a name="export-samples"></a>Esempi di esportazione

* [Eseguire l'esportazione in SQL usando l'analisi di flusso][exportasa]
* [Esempio di analisi di flusso 2](app-insights-export-stream-analytics.md)

Su scala più estesa considerare la possibilità di usare cluster [HDInsight](https://azure.microsoft.com/services/hdinsight/) - Hadoop nel cloud. HDInsight offre un'ampia gamma di tecnologie per la gestione e analisi dei Big Data e può essere usato per elaborare i dati esportati da Application Insights.

## <a name="q--a"></a>Domande e risposte
* *Si intende scaricare semplicemente un grafico.*  

    Questa operazione è consentita. Nella parte superiore del pannello fare clic sul **pulsante di esportazione dati**.
* *È stata impostata un'esportazione, ma non sono presenti dati nell'archivio personale.*

    Application Insights ha ricevuto eventuali dati di telemetria dall'app dal momento in cui si è impostata l'esportazione? Si riceveranno solo nuovi dati.
* *Si è tentato di impostare un'esportazione, ma è stato negato l'accesso*

    Se l'account è di proprietà dell'organizzazione, è necessario essere un membro del gruppo di proprietari o di collaboratori.
* *È possibile eseguire un'esportazione direttamente al negozio locale?*

    No. Il motore di esportazione attualmente funziona solo con Archiviazione di Azure.  
* *Esiste un limite alla quantità di dati da inserire nell'archivio personale?*

    No. L'inserimento dei dati continuerà fino a quando non si elimina l'esportazione. Occorrerà fermarsi se i limiti esterni per l'archiviazione BLOB sono stati raggiunti, ma ciò è abbastanza difficile. Spetta all'utente controllare quante risorse di archiviazione usare.  
* *Quanti BLOB dovrebbero essere visualizzati nella risorsa di archiviazione?*

  * Per ogni tipi di dati selezionato per l'esportazione, viene creato un nuovo BLOB ogni minuto, se sono disponibili dati.
  * Per le applicazioni con traffico elevato, inoltre, vengono allocate unità di partizione aggiuntive. In questo caso ogni unità crea un BLOB ogni minuto.
* *La chiave per la risorsa di archiviazione è stata rigenerata o il nome del contenitore è stato modificato, ma l'esportazione non funziona.*

    Modificare l'esportazione e aprire il pannello di destinazione dell'esportazione. Lasciare la stessa risorsa di archiviazione selezionata come in precedenza e fare clic su OK per confermare. L'esportazione verrà riavviata. Se la modifica è stata eseguita negli ultimi giorni, non si perderanno i dati.
* *È possibile sospendere l'esportazione?*

    Sì. Fare clic su Disabilita.

## <a name="code-samples"></a>Esempi di codice

* [Esempio di analisi di flusso](app-insights-export-stream-analytics.md)
* [Eseguire l'esportazione in SQL usando l'analisi di flusso][exportasa]
* [Riferimento dettagliato al modello di dati per i valori e i tipi di proprietà.](app-insights-export-data-model.md)

<!--Link references-->

[exportasa]: app-insights-code-sample-export-sql-stream-analytics.md
[roles]: app-insights-resources-roles-access-control.md
