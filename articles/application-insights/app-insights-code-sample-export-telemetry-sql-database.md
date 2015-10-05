<properties 
	pageTitle="Esempio di codice: esportare in SQL da Application Insights usando un ruolo di lavoro" 
	description="Codificare la propria analisi dei dati di telemetria in Application Insights usando la funzione di esportazione continua." 
	services="application-insights" 
    documentationCenter=""
	authors="mazharmicrosoft" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/23/2015" 
	ms.author="awills"/>
 
# Esempio di codice: esportare in SQL da Application Insights usando un ruolo di lavoro

Questo articolo illustra come spostare i dati di telemetria da [Application Insights di Visual Studio][start] in un database SQL di Azure usando [Esportazione continua][export] e poche righe di codice.

L'esportazione continua sposta i dati di telemetria nell'archiviazione di Azure nel formato JSON, quindi verrà scritto del codice per analizzare gli oggetti JSON e creare righe in una tabella di database.

Più in generale, Esportazione continua è il modo per eseguire la propria analisi dei dati di telemetria che le app inviano ad Application Insights. È possibile adattare questo esempio di codice per eseguire altre operazioni con i dati di telemetria esportati.

Si inizierà dal presupposto che si abbia già l'app che si vuole monitorare.

## Aggiungere Application Insights SDK

Per monitorare l'applicazione, [aggiungere un Application Insights SDK][start] all'applicazione. Esistono diversi SDK e strumenti di supporto per diverse piattaforme, linguaggi e IDE. È possibile monitorare le pagine Web, i server Web ASP.NET o Java e i dispositivi mobili di diversi tipi. Tutti gli SDK inviano dati di telemetria al [portale di Application Insights][portal], dove è possibile usare potenti strumenti di analisi e diagnostica ed esportare i dati nell'archivio.

Attività iniziali

1. Ottenere un [account in Microsoft Azure](http://azure.microsoft.com/pricing/).
2. Nel [portale di Azure][portal] aggiungere una nuova risorsa di Application Insights per la propria app:

    ![Scegliere Nuovo, quindi Servizi per gli sviluppatori, Application Insights e scegliere il tipo di applicazione](./media/app-insights-code-sample-export-telemetry-sql-database/010-new-asp.png)


    Il tipo di app e la sottoscrizione potrebbero essere diversi.
3. Aprire Avvio rapido per scoprire come configurare l'SDK per il proprio tipo di app.

    ![Scegliere Avvio rapido e seguire le istruzioni](./media/app-insights-code-sample-export-telemetry-sql-database/020-quick.png)

    Se il tipo di app non è elencato, dare uno sguardo alla pagina [Introduzione][start].

4. In questo esempio viene eseguito il monitoraggio di un'app Web, quindi è possibile usare gli strumenti di Azure in Visual Studio per installare l'SDK. A questo punto, viene specificato il nome della risorsa di Application Insights:

    ![In Visual Studio, nella finestra di dialogo Nuovo progetto, selezionare Aggiungi Application Insights e, sotto Invia telemetria a, scegliere di creare una nuova applicazione o di usarne una esistente.](./media/app-insights-code-sample-export-telemetry-sql-database/030-new-project.png)


## Creare l'archiviazione in Azure

1. Creare un account di archiviazione "classico" per la sottoscrizione nel [portale di Azure][portal].

    ![Nel portale di Azure scegliere Nuovo, Dati, Archiviazione](./media/app-insights-code-sample-export-telemetry-sql-database/040-store.png)

2. Creare un contenitore

    ![Nel nuovo archivio selezionare Contenitori, fare clic sul riquadro Contenitori e quindi su Aggiungi](./media/app-insights-code-sample-export-telemetry-sql-database/050-container.png)


## Avviare l'esportazione continua nell'archiviazione di Azure

1. Nel portale di Azure passare alla risorsa di Application Insights creata per la propria applicazione.

    ![Scegliere Sfoglia, Application Insights e quindi l'applicazione](./media/app-insights-code-sample-export-telemetry-sql-database/060-browse.png)

2. Creare un'esportazione continua.

    ![Scegliere Impostazioni, Esportazione continua, Aggiungi](./media/app-insights-code-sample-export-telemetry-sql-database/070-export.png)


    Selezionare l'account di archiviazione creato in precedenza:

    ![Impostare la destinazione di esportazione](./media/app-insights-code-sample-export-telemetry-sql-database/080-add.png)
    
    Impostare i tipi di eventi da visualizzare:

    ![Scegliere i tipi di eventi](./media/app-insights-code-sample-export-telemetry-sql-database/085-types.png)

3. Lasciare che alcuni dati si accumulino. Attendere che gli utenti usino l'applicazione per qualche tempo. Quando verranno restituiti i dati di telemetria, sarà possibile esaminare i grafici statistici in [Esplora metriche](app-insights-metrics-explorer.md) e i singoli eventi in [Ricerca diagnostica](app-insights-diagnostic-search.md).

    I dati verranno inoltre esportati nell'archivio.

4. Esaminare i dati esportati. In Visual Studio, scegliere **Visualizza/Cloud Explorer** e aprire Azure/Archiviazione. (Se non si dispone di tale opzione del menu, è necessario installare l’SDK di Azure: aprire la finestra di dialogo Nuovo progetto, aprire Visual C#/Cloud/Ottieni Microsoft Azure SDK per .NET).

    ![In Visual Studio aprire Esplora server, Azure, Archiviazione](./media/app-insights-code-sample-export-telemetry-sql-database/087-explorer.png)

    Prendere nota della parte comune del nome del percorso, derivata dal nome dell’applicazione e dalla chiave di strumentazione.

Gli eventi vengono scritti nei file BLOB in formato JSON. Ogni file può contenere uno o più eventi. A questo punto sarà possibile leggere i dati degli eventi e filtrare i campi preferiti. È possibile eseguire una serie di operazioni sui dati, ma lo scopo di questo articolo è la scrittura di codice per spostare i dati in un database SQL. Sarà quindi più semplice eseguire molte query interessanti.

## Creare un database SQL di Azure

Dalla sottoscrizione nel [portale di Azure][portal] creare il database (e un nuovo server, a meno che non sia già disponibile) in cui verranno scritti i dati.

![Nuovo, Dati, SQL](./media/app-insights-code-sample-export-telemetry-sql-database/090-sql.png)


Assicurarsi che il server di database consenta di accedere ai servizi di Azure:


![Sfoglia, Server, il proprio server, Impostazioni, Firewall, Consenti l'accesso a Servizi di Azure](./media/app-insights-code-sample-export-telemetry-sql-database/100-sqlaccess.png)


## Creare un ruolo di lavoro 

Ora è possibile scrivere del [codice](https://sesitai.codeplex.com/) per analizzare il documento JSON nei BLOB esportati e creare record nei database. Dal momento che sia l'archivio di esportazione sia il database si trovano in Azure, il codice verrà eseguito in un ruolo di lavoro di Azure.

Questo codice estrae automaticamente tutte le proprietà presenti nel documento JSON. Per le descrizioni delle proprietà, vedere il [modello di dati di esportazione](app-insights-export-data-model.md).


#### Creare un progetto di ruolo di lavoro

In Visual Studio creare un nuovo progetto per il ruolo di lavoro:

![Nuovo progetto, Visual C#, Cloud, Servizio cloud di Azure](./media/app-insights-code-sample-export-telemetry-sql-database/110-cloud.png)

![Nella finestra di dialogo del servizio cloud, scegliere Visual C#, Ruolo di lavoro](./media/app-insights-code-sample-export-telemetry-sql-database/120-worker.png)


#### Effettuare la connessione all'account di archiviazione

In Azure, ottenere la stringa di connessione dall'account di archiviazione:

![In Account di archiviazione, selezionare Chiavi e copiare Stringa di connessione primaria](./media/app-insights-code-sample-export-telemetry-sql-database/055-get-connection.png)

In Visual Studio, configurare le impostazioni del ruolo di lavoro con la stringa di connessione dell'account di archiviazione:


![In Esplora soluzioni, sotto il progetto di servizio cloud, espandere Ruoli e aprire il proprio ruolo di lavoro. Aprire la scheda Impostazioni, scegliere Aggiungi impostazione e impostare name=StorageConnectionString, type=connection string, fare clic per impostare il valore. Impostarlo manualmente e incollare la stringa di connessione.](./media/app-insights-code-sample-export-telemetry-sql-database/130-connection-string.png)


#### Pacchetti

In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto Ruolo di lavoro e scegliere Gestisci pacchetti NuGet. Cercare e installare i pacchetti seguenti:

 * EntityFramework 6.1.2 o versioni successive: verrà usato per generare uno schema di tabella del database al volo, basandosi sul contenuto del documento JSON nel BLOB.
 * JsonFx: verrà usato per rendere flat il documento JSON alle proprietà della classe C#.

Usare questo strumento per generare la classe C# dal singolo documento JSON. È necessario apportare alcune modifiche secondarie come il rendering bidimensionale delle matrici JSON in una singola proprietà C# e la trasformazione di una singola colonna nella tabella di database (ad esempio: urlData\_port)

 * [Generatore di classi C# JSON](http://jsonclassgenerator.codeplex.com/)

## Codice 

È possibile inserire il codice in `WorkerRole.cs`.

#### Importazioni

    using Microsoft.WindowsAzure.Storage;

    using Microsoft.WindowsAzure.Storage.Blob;

#### Recuperare la stringa di connessione di archiviazione

    private static string GetConnectionString()
    {
      return Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    }

#### Eseguire il ruolo di lavoro a intervalli regolari

Sostituire il metodo di esecuzione esistente e scegliere l'intervallo preferito. Deve essere di almeno un'ora, perché la funzionalità di esportazione completa un oggetto JSON in un'ora.

    public override void Run()
    {
      Trace.TraceInformation("WorkerRole1 is running");

      while (true)
      {
        Trace.WriteLine("Sleeping", "Information");

        Thread.Sleep(86400000); //86400000=24 hours //1 hour=3600000
                
        Trace.WriteLine("Awake", "Information");

        ImportBlobtoDB();
      }
    }

#### Inserire ogni oggetto JSON come una riga della tabella


    public void ImportBlobtoDB()
    {
      try
      {
        CloudStorageAccount account = CloudStorageAccount.Parse(GetConnectionString());

        var blobClient = account.CreateCloudBlobClient();
        var container = blobClient.GetContainerReference(FilterContainer);

        foreach (CloudBlobDirectory directory in container.ListBlobs())//Parent directory
        {
          foreach (CloudBlobDirectory subDirectory in directory.ListBlobs())//PageViewPerformance
       	  {
            foreach (CloudBlobDirectory dir in subDirectory.ListBlobs())//2015-01-31
            {
              foreach (CloudBlobDirectory subdir in dir.ListBlobs())//22
              {
                foreach (IListBlobItem item in subdir.ListBlobs())//3IAwm6u3-0.blob
                {
                  itemname = item.Uri.ToString();
                  ParseEachBlob(container, item);
                  AuditBlob(container, directory, subDirectory, dir, subdir, item);
                } //item loop
              } //subdir loop
            } //dir loop
          } //subDirectory loop
        } //directory loop
      }
      catch (Exception ex)
      {
		//handle exception
      }
    }

#### Analizzare ogni BLOB

    private void ParseEachBlob(CloudBlobContainer container, IListBlobItem item)
    {
      try
      {
        var blob = container.GetBlockBlobReference(item.Parent.Prefix + item.Uri.Segments.Last());
    
        string json;
    
        using (var memoryStream = new MemoryStream())
        {
          blob.DownloadToStream(memoryStream);
          json = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    
          IEnumerable<string> entities = json.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
    
          recCount = entities.Count();
          failureCount = 0; //resetting failure count
    
          foreach (var entity in entities)
          {
            var reader = new JsonFx.Json.JsonReader();
            dynamic output = reader.Read(entity);
    
            Dictionary<string, object> dict = new Dictionary<string, object>();
    
            GenerateDictionary((System.Dynamic.ExpandoObject)output, dict, "");
    
            switch (FilterType)
            {
              case "PageViewPerformance":
    
              if (dict.ContainsKey("clientPerformance"))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["clientPerformance"])[0], dict, "");
    	        }
    
              if (dict.ContainsKey("context_custom_dimensions"))
              {
                if (dict["context_custom_dimensions"].GetType() == typeof(System.Dynamic.ExpandoObject[]))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["context_custom_dimensions"])[0], dict, "");
                }
              }
    
            PageViewPerformance objPageViewPerformance = (PageViewPerformance)GetObject(dict);
    
            try
            {
              using (var db = new TelemetryContext())
              {
                db.PageViewPerformanceContext.Add(objPageViewPerformance);
                db.SaveChanges();
              }
            }
            catch (Exception ex)
            {
              failureCount++;
            }
            break;
    
            default:
            break;
          }
        }
      }
    }
    catch (Exception ex)
    {
      //handle exception 
    }
    }

#### Preparare un dizionario per ciascun documento JSON


    private void GenerateDictionary(System.Dynamic.ExpandoObject output, Dictionary<string, object> dict, string parent)
        {
            try
            {
                foreach (var v in output)
                {
                    string key = parent + v.Key;
                    object o = v.Value;

                    if (o.GetType() == typeof(System.Dynamic.ExpandoObject))
                    {
                        GenerateDictionary((System.Dynamic.ExpandoObject)o, dict, key + "_");
                    }
                    else
                    {
                        if (!dict.ContainsKey(key))
                        {
                            dict.Add(key, o);
                        }
                    }
                }
            }
            catch (Exception ex)
            {
      		//handle exception 
    	    }
        }

#### Eseguire il cast del documento JSON in proprietà dell'oggetto telemetria della classe C#

     public object GetObject(IDictionary<string, object> d)
        {
            PropertyInfo[] props = null;
            object res = null;

            try
            {
                switch (FilterType)
                {
                    case "PageViewPerformance":

                        props = typeof(PageViewPerformance).GetProperties();
                        res = Activator.CreateInstance<PageViewPerformance>();
                        break;

                    default:
                        break;
                }

                for (int i = 0; i < props.Length; i++)
                {
                    if (props[i].CanWrite && d.ContainsKey(props[i].Name))
                    {
                        props[i].SetValue(res, d[props[i].Name], null);
                    }
                }
            }
            catch (Exception ex)
            {
      		//handle exception 
    	    }

            return res;
        }

#### File di classe PageViewPerformance generato dal documento JSON



    public class PageViewPerformance
    {
    	[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        public Guid Id { get; set; }

        public string url { get; set; }

        public int urlData_port { get; set; }

        public string urlData_protocol { get; set; }

        public string urlData_host { get; set; }

        public string urlData_base { get; set; }

        public string urlData_hashTag { get; set; }

        public double total_value { get; set; }

        public double networkConnection_value { get; set; }

        public double sendRequest_value { get; set; }

        public double receiveRequest_value { get; set; }

        public double clientProcess_value { get; set; }

        public string name { get; set; }

        public string internal_data_id { get; set; }

        public string internal_data_documentVersion { get; set; }

        public DateTime? context_data_eventTime { get; set; }

        public string context_device_id { get; set; }

        public string context_device_type { get; set; }

        public string context_device_os { get; set; }

        public string context_device_osVersion { get; set; }

        public string context_device_locale { get; set; }

        public string context_device_userAgent { get; set; }

        public string context_device_browser { get; set; }

        public string context_device_browserVersion { get; set; }

        public string context_device_screenResolution_value { get; set; }

        public string context_user_anonId { get; set; }

        public string context_user_anonAcquisitionDate { get; set; }

        public string context_user_authAcquisitionDate { get; set; }

        public string context_user_accountAcquisitionDate { get; set; }

        public string context_session_id { get; set; }

        public bool context_session_isFirst { get; set; }

        public string context_operation_id { get; set; }

        public double context_location_point_lat { get; set; }

        public double context_location_point_lon { get; set; }

        public string context_location_clientip { get; set; }

        public string context_location_continent { get; set; }

        public string context_location_country { get; set; }

        public string context_location_province { get; set; }

        public string context_location_city { get; set; }
    }


#### DBcontext per l'interazione con SQL da Entity Framework

	public class TelemetryContext : DbContext
    {
        public DbSet<PageViewPerformance> PageViewPerformanceContext { get; set; }
        public TelemetryContext()
            : base("name=TelemetryContext")
        {
        }
    }

Aggiungere la stringa di connessione di database con nome `TelemetryContext` in `app.config`.

## Schema (solo informazioni)

Questo è lo schema per la tabella che verrà generata per PageView.

> [AZURE.NOTE]Non è necessario eseguire questo script. Gli attributi nel documento JSON determinano le colonne nella tabella.

    CREATE TABLE [dbo].[PageViewPerformances](
	[Id] [uniqueidentifier] NOT NULL,
	[url] [nvarchar](max) NULL,
	[urlData_port] [int] NOT NULL,
	[urlData_protocol] [nvarchar](max) NULL,
	[urlData_host] [nvarchar](max) NULL,
	[urlData_base] [nvarchar](max) NULL,
	[urlData_hashTag] [nvarchar](max) NULL,
	[total_value] [float] NOT NULL,
	[networkConnection_value] [float] NOT NULL,
	[sendRequest_value] [float] NOT NULL,
	[receiveRequest_value] [float] NOT NULL,
	[clientProcess_value] [float] NOT NULL,
	[name] [nvarchar](max) NULL,
	[User] [nvarchar](max) NULL,
	[internal_data_id] [nvarchar](max) NULL,
	[internal_data_documentVersion] [nvarchar](max) NULL,
	[context_data_eventTime] [datetime] NULL,
	[context_device_id] [nvarchar](max) NULL,
	[context_device_type] [nvarchar](max) NULL,
	[context_device_os] [nvarchar](max) NULL,
	[context_device_osVersion] [nvarchar](max) NULL,
	[context_device_locale] [nvarchar](max) NULL,
	[context_device_userAgent] [nvarchar](max) NULL,
	[context_device_browser] [nvarchar](max) NULL,
	[context_device_browserVersion] [nvarchar](max) NULL,
	[context_device_screenResolution_value] [nvarchar](max) NULL,
	[context_user_anonId] [nvarchar](max) NULL,
	[context_user_anonAcquisitionDate] [nvarchar](max) NULL,
	[context_user_authAcquisitionDate] [nvarchar](max) NULL,
	[context_user_accountAcquisitionDate] [nvarchar](max) NULL,
	[context_session_id] [nvarchar](max) NULL,
	[context_session_isFirst] [bit] NOT NULL,
	[context_operation_id] [nvarchar](max) NULL,
	[context_location_point_lat] [float] NOT NULL,
	[context_location_point_lon] [float] NOT NULL,
	[context_location_clientip] [nvarchar](max) NULL,
	[context_location_continent] [nvarchar](max) NULL,
	[context_location_country] [nvarchar](max) NULL,
	[context_location_province] [nvarchar](max) NULL,
	[context_location_city] [nvarchar](max) NULL,
    CONSTRAINT [PK_dbo.PageViewPerformances] PRIMARY KEY CLUSTERED 
    (
     [Id] ASC
    )WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]

    GO

    ALTER TABLE [dbo].[PageViewPerformances] ADD  DEFAULT (newsequentialid()) FOR [Id]
    GO


Per vedere questo esempio in azione, [scaricare](https://sesitai.codeplex.com/) il codice di lavoro completo, modificare le impostazioni di `app.config` e pubblicare il ruolo di lavoro in Azure.


## Articoli correlati

* [Esportare in SQL usando un ruolo di lavoro](app-insights-code-sample-export-telemetry-sql-database.md)
* [Esportazione continua in Application Insights](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)
* [Modello di dati di esportazione](app-insights-export-data-model.md)
* [Altri esempi e procedure dettagliate](app-insights-code-samples.md)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-get-started.md

 

<!---HONumber=Sept15_HO4-->