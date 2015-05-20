<properties 
	pageTitle="Esportazione continua dei dati di telemetria da Application Insights" 
	description="Esportare i dati di diagnostica e di uso nella risorsa di archiviazione in Microsoft Azure e scaricarli da lì." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="awills"/>
 
# Esportare i dati di telemetria da Application Insights

Si vogliono effettuare alcune analisi personalizzate sui dati di telemetria? Oppure forse si vuole un messaggio di avviso di posta elettronica relativo a eventi con proprietà specifiche? A tale scopo, l'esportazione continua è ideale. Gli eventi visualizzati nel portale di Application Insights possono essere esportati nella risorsa di archiviazione di Microsoft Azure in formato JSON. Da qui è possibile scaricare i dati e scrivere qualsiasi tipo di codice necessario per elaborarli.


## <a name="setup"></a> Configurare l'esportazione continua

Nel pannello Panoramica dell'applicazione nel portale di Application Insights, aprire Esportazione continua:

![Scorrere verso il basso e fare clic su Esportazione continua](./media/app-insights-export-telemetry/01-export.png)

Aggiungere un'esportazione e scegliere un [account di archiviazione Azure](storage-introduction.md) in cui inserire i dati:

![Fare clic su Aggiungi, Destinazione di esportazione, Account di archiviazione e quindi creare un nuovo archivio o scegliere un archivio esistente](./media/app-insights-export-telemetry/02-add.png)

Scegliere i tipi di evento che si vuole esportare:

![Fare clic su Scegli tipi di eventi](./media/app-insights-export-telemetry/03-types.png)


Dopo averla creata, l'esportazione viene avviata. Si ottengono solo i dati che arrivano dopo aver creato l'esportazione.


Se si vogliono modificare i tipi di eventi in un secondo momento, è sufficiente modificare l'esportazione:

![Fare clic su Scegli tipi di eventi](./media/app-insights-export-telemetry/05-edit.png)

Per interrompere il flusso, fare clic su Disabilita. Quando si fa nuovamente clic su Abilita, il flusso verrà riavviato con nuovi dati. Non si otterranno i dati che arrivano nel portale mentre l'esportazione è stata disabilitata.

Per interrompere il flusso in modo permanente, eliminare l'esportazione. Questa operazione non elimina i dati dalla risorsa di archiviazione.
#### Non si riesce ad aggiungere o modificare un'esportazione?

* Per aggiungere o modificare le esportazioni, è necessario avere i diritti di accesso proprietario, collaboratore o collaboratore di Application Insights. [Informazioni sui ruoli][roles].

## <a name="analyze"></a> Quali eventi si ottengono?

I dati esportati sono dati di telemetria non elaborati ricevuti dall'applicazione, ad eccezione di quanto segue:

* I risultati dei test Web non sono attualmente inclusi. 
* È possibile aggiungere dati del percorso che vengono calcolati dall'indirizzo IP del client.  

Le metriche calcolate non sono incluse. Ad esempio, non si procederà all'esportazione dell'uso medio della CPU, ma si procederà all'esportazione dei dati di telemetria non elaborati da cui viene calcolata la media.

## <a name="get"></a> Come si ottengono?

Quando si apre l'archivio BLOB con uno strumento come [Esplora Server](http://msdn.microsoft.com/library/azure/ff683677.aspx), si noterà un contenitore con un set di file BLOB. L'URI di ogni file è application-id/telemetry-type/date/time.

![Controllare l'archivio BLOB con uno strumento adatto](./media/app-insights-export-telemetry/04-data.png)

La data e ora sono UTC e lo sono quando i dati di telemetria sono stati depositati nell'archivio, non l'ora in cui sono stati generati. Di conseguenza, se si scrive codice per scaricare i dati, è possibile spostarlo in modo lineare attraverso i dati.

Per scaricare questi dati in modo programmatico, usare l'[API REST dell'archivio BLOB](storage-dotnet-how-to-use-blobs.md#configure-access) o i [cmdlet di Azure PowerShell](http://msdn.microsoft.com/library/azure/dn806401.aspx).

In alternativa, considerare [DataFactory](http://azure.microsoft.com/services/data-factory/), in cui è possibile impostare pipeline per la gestione di dati su larga scala.

La scrittura di un nuovo BLOB viene avviata a tutte le ore \(se vengono ricevuti gli eventi\). Di conseguenza, è necessario sempre eseguire elaborazioni fino all'ora precedente, ma attendere l'ora corrente per il completamento.

[Esempio di codice][exportcode]


## <a name="format"></a> Che aspetto hanno i dati?

* Ogni BLOB è un file di testo che contiene più righe separate da '\\n'.
* Ogni riga è un documento JSON non formattato. Se si vuole stare seduti e esaminali, usare un visualizzatore, ad esempio Blocco note++ con il plug-in JSON:

![Visualizzare i dati di telemetria con uno strumento adatto](./media/app-insights-export-telemetry/06-json.png)

Gli intervalli di tempo sono espressi in tick, dove 10 000 tick = 1 ms. Questi valori, ad esempio, mostrano un tempo di 10 ms per inviare una richiesta dal browser, 30 ms per riceverlo e 1,8 s per elaborare la pagina nel browser:

	"sendRequest": {"value": 10000.0},
	"receiveRequest": {"value": 30000.0},
	"clientProcess": {"value": 17970000.0}



## Come eseguire l'elaborazione?

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


In alternativa, è possibile spostarli in un database SQL; vedere l'[esempio di codice][exportcode].

Su scala più grande valutare i cluster [HDInsight](http://azure.microsoft.com/services/hdinsight/) - Hadoop nel cloud. HDInsight offre un'ampia gamma di tecnologie per la gestione e l'analisi di big data.

## <a name="delete"></a>Eliminare i vecchi dati
Si noti che si è responsabili della gestione della capacità di archiviazione ed eliminazione di vecchi dati, se necessario.

## Se si rigenera la chiave di archiviazione...

Se si modifica la chiave per l'archiviazione, l'esportazione continua non funzionerà più. Verrà visualizzata una notifica nell'account Azure.

Aprire il pannello Esportazione continua e modificare l'esportazione. Modificare la destinazione di esportazione, ma lasciare selezionata la stessa risorsa di archiviazione. Fare clic su OK per confermare.

![Modificare l'esportazione continua, aprire e chiudere tre destinazioni di esportazione.](./media/app-insights-export-telemetry/07-resetstore.png)

L'esportazione continua verrà riavviata.


## Esempio di codice

[Spostare i dati esportati in un database SQL][exportcode]

## Domande e risposte

* *Si intende scaricare un grafico in una sola volta.*  
 
    Su questo aspetto si sta lavorando separatamente.

* *È stata impostata un'esportazione, ma non sono presenti dati nell'archivio personale.*

    Application Insights ha ricevuto eventuali dati di telemetria dall'app dal momento in cui si è impostata l'esportazione? Si riceveranno solo nuovi dati.

* *Si è tentato di impostare un'esportazione, ma è stato negato l'accesso*

    Se l'account è di proprietà dell'organizzazione, è necessario essere un membro del gruppo di proprietari o di collaboratori.

    <!-- Your account has to be either a paid-for account, or in the free trial period. -->

* *È possibile eseguire un'esportazione direttamente al negozio locale?*

    No. Il motore di esportazione deve basarsi su un canale aperto in cui inserire i dati.

* *Esiste un limite alla quantità di dati da inserire nell'archivio personale?*

    No. L'inserimento dei dati continuerà fino a quando non si elimina l'esportazione. Occorrerà fermarsi se i limiti esterni per l'archiviazione BLOB sono stati raggiunti, ma ciò è abbastanza difficile. Spetta all'utente controllare quante risorse di archiviazione usare.

* *La chiave per la risorsa di archiviazione è stata rigenerata o il nome del contenitore è stato modificato, ma l'esportazione non funziona.*

    Modificare l'esportazione e aprire il pannello di destinazione dell'esportazione. Lasciare la stessa risorsa di archiviazione selezionata come in precedenza e fare clic su OK per confermare. L'esportazione verrà riavviata. Se la modifica è stata eseguita negli ultimi giorni, non si perderanno i dati.

* *È possibile sospendere l'esportazione?*

    Sì. Fare clic su Disabilita.


<!--Link references-->

[exportcode]: app-insights-code-sample-export-telemetry-sql-database.md
[roles]: app-insights-resources-roles-access-control.md


<!--HONumber=54-->