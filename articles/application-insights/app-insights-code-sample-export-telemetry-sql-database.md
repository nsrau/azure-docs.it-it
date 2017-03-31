---
title: 'Esempio: Analizzare i dati esportati da Azure Application Insights |Documentazione Microsoft'
description: Codificare la propria analisi dei dati di telemetria in Application Insights usando la funzione di esportazione continua. Salvare i dati in SQL.
services: application-insights
documentationcenter: 
author: mazharmicrosoft
manager: douge
ms.assetid: 3ffb62b6-3fe9-455d-a260-b2a0201b5ecd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: fedd078402bbd220bce9b71cd035508d46f92f82


---
# <a name="code-sample-parse-data-exported-from-application-insights"></a>Esempio di codice: analizzare i dati esportati da Application Insights
Questo articolo illustra come scrivere il codice per elaborare i dati esportati da [Azure Application Insights][start] usando l'[esportazione continua][export]. L'esportazione continua sposta i dati di telemetria nell'archiviazione di Azure nel formato JSON, quindi verrà scritto del codice per analizzare gli oggetti JSON e creare righe in una tabella di database.

Come esempio, si scriverà un codice per spostare i dati di telemetria da Application Insights in un database SQL.

Prima di iniziare, tenere presente quanto segue:

* Per trasferire in un database i dati esportati, è più efficiente [usare l'analisi di flusso](app-insights-code-sample-export-sql-stream-analytics.md), ma lo scopo qui è illustrare un codice per elaborare i dati esportati. È possibile adattare questo esempio di codice per eseguire altre operazioni con i dati di telemetria esportati.
* In questo esempio i dati vengono esportati in un database di Azure eseguendo il codice in un ruolo di lavoro di Azure, ma è possibile adattare questo codice per eseguirlo in un server locale per poter effettuare il pull dei dati in un'istanza di SQL Server locale.
* È possibile [scrivere il codice per accedere ai dati di telemetria direttamente](http://dev.applicationinsights.io/) in Application Insights, senza esportarli.

Se non si è ancora iniziato a monitorare l'applicazione Web con Application Insights, [farlo ora][start].



## <a name="create-storage-in-azure"></a>Creare l'archivio in Azure
I dati da Application Insights vengono sempre esportati in un account di archiviazione di Azure in formato JSON. È da questa archiviazione che il codice leggerà i dati.

1. Creare un account di archiviazione "classico" per la sottoscrizione nel [portale di Azure][portal].
   
    ![Nel portale di Azure scegliere Nuovo, Dati, Archiviazione](./media/app-insights-code-sample-export-telemetry-sql-database/040-store.png)
2. Creare un contenitore
   
    ![Nel nuovo archivio selezionare Contenitori, fare clic sul riquadro Contenitori e quindi su Aggiungi](./media/app-insights-code-sample-export-telemetry-sql-database/050-container.png)

## <a name="start-continuous-export-to-azure-storage"></a>Avviare l'esportazione continua nell'archiviazione di Azure
1. Nel portale di Azure passare alla risorsa di Application Insights creata per la propria applicazione.
   
    ![Scegliere Sfoglia, Application Insights e quindi l'applicazione](./media/app-insights-code-sample-export-telemetry-sql-database/060-browse.png)
2. Creare un'esportazione continua.
   
    ![Scegliere Impostazioni, Esportazione continua, Aggiungi](./media/app-insights-code-sample-export-telemetry-sql-database/070-export.png)

    Selezionare l'account di archiviazione creato in precedenza:

    ![Impostare la destinazione di esportazione](./media/app-insights-code-sample-export-telemetry-sql-database/080-add.png)

    Impostare i tipi di eventi da visualizzare:

    ![Scegliere i tipi di eventi](./media/app-insights-code-sample-export-telemetry-sql-database/085-types.png)

1. Lasciare che alcuni dati si accumulino. Attendere che gli utenti usino l'applicazione per qualche tempo. Verranno restituiti i dati di telemetria e sarà possibile esaminare i grafici statistici in [Esplora metriche](app-insights-metrics-explorer.md) e i singoli eventi in [Ricerca diagnostica](app-insights-diagnostic-search.md). 
   
    I dati verranno inoltre esportati nell'archivio. 
2. Esaminare i dati esportati. In Visual Studio, scegliere **Visualizza/Cloud Explorer**e aprire Azure/Archiviazione. (Se non si dispone di tale opzione del menu, è necessario installare l’SDK di Azure: aprire la finestra di dialogo Nuovo progetto, aprire Visual C#/Cloud/Ottieni Microsoft Azure SDK per .NET).
   
    ![In Visual Studio aprire Esplora server, Azure, Archiviazione](./media/app-insights-code-sample-export-telemetry-sql-database/087-explorer.png)
   
    Prendere nota della parte comune del nome del percorso, derivata dal nome dell’applicazione e dalla chiave di strumentazione. 

Gli eventi vengono scritti nei file BLOB in formato JSON. Ogni file può contenere uno o più eventi. A questo punto sarà possibile leggere i dati degli eventi e filtrare i campi preferiti. È possibile eseguire una serie di operazioni sui dati, ma lo scopo di questo articolo è la scrittura di codice per spostare i dati in un database SQL. Sarà quindi più semplice eseguire molte query interessanti.

## <a name="create-an-azure-sql-database"></a>Creare un database SQL di Azure
Per questo esempio, verrà scritto il codice per inserire i dati in un database.

Iniziando di nuovo dalla sottoscrizione nel [portale di Azure][portal], creare il database (e un nuovo server, se necessario) in cui si scriveranno i dati.

![Nuovo, Dati, SQL](./media/app-insights-code-sample-export-telemetry-sql-database/090-sql.png)

Assicurarsi che il server di database consenta di accedere ai servizi di Azure:

![Sfoglia, Server, il proprio server, Impostazioni, Firewall, Consenti l'accesso a Servizi di Azure](./media/app-insights-code-sample-export-telemetry-sql-database/100-sqlaccess.png)

## <a name="create-a-worker-role"></a>Creare un ruolo di lavoro
Ora è infine possibile scrivere del [codice](https://sesitai.codeplex.com/) per analizzare il documento JSON nei BLOB esportati e creare record nei database. Dal momento che sia l'archivio di esportazione sia il database si trovano in Azure, il codice verrà eseguito in un ruolo di lavoro di Azure.

Questo codice estrae automaticamente tutte le proprietà presenti nel documento JSON. Per le descrizioni delle proprietà, vedere il [modello di dati di esportazione](app-insights-export-data-model.md).

#### <a name="create-worker-role-project"></a>Creare un progetto di ruolo di lavoro
In Visual Studio creare un nuovo progetto per il ruolo di lavoro:

![Nuovo progetto, Visual C#, Cloud, Servizio cloud di Azure](./media/app-insights-code-sample-export-telemetry-sql-database/110-cloud.png)

![Nella finestra di dialogo del servizio cloud, scegliere Visual C#, Ruolo di lavoro](./media/app-insights-code-sample-export-telemetry-sql-database/120-worker.png)

#### <a name="connect-to-the-storage-account"></a>Effettuare la connessione all'account di archiviazione
In Azure, ottenere la stringa di connessione dall'account di archiviazione:

![In Account di archiviazione, selezionare Chiavi e copiare Stringa di connessione primaria](./media/app-insights-code-sample-export-telemetry-sql-database/055-get-connection.png)

In Visual Studio, configurare le impostazioni del ruolo di lavoro con la stringa di connessione dell'account di archiviazione:

![In Esplora soluzioni, sotto il progetto di servizio cloud, espandere Ruoli e aprire il proprio ruolo di lavoro. Aprire la scheda Impostazioni, scegliere Aggiungi impostazione e impostare name=StorageConnectionString, type=connection string, fare clic per impostare il valore. Impostarlo manualmente e incollare la stringa di connessione.](./media/app-insights-code-sample-export-telemetry-sql-database/130-connection-string.png)

#### <a name="packages"></a>Pacchetti
In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto Ruolo di lavoro e scegliere Gestisci pacchetti NuGet.
Cercare e installare i pacchetti seguenti: 

* EntityFramework 6.1.2 o versioni successive: verrà usato per generare uno schema di tabella del database al volo, basandosi sul contenuto del documento JSON nel BLOB.
* JsonFx: verrà usato per rendere flat il documento JSON con le proprietà della classe C#.

Usare questo strumento per generare la classe C# dal singolo documento JSON. È necessario apportare alcune modifiche secondarie come il rendering bidimensionale delle matrici JSON in una singola proprietà C# e la trasformazione di una singola colonna nella tabella di database (ad esempio: urlData_port) 

* [Generatore di classi C# JSON](http://jsonclassgenerator.codeplex.com/)

## <a name="code"></a>Codice
È possibile inserire il codice in `WorkerRole.cs`.

#### <a name="imports"></a>Importazioni
    using Microsoft.WindowsAzure.Storage;

    using Microsoft.WindowsAzure.Storage.Blob;

#### <a name="retrieve-the-storage-connection-string"></a>Recuperare la stringa di connessione di archiviazione
    private static string GetConnectionString()
    {
      return Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    }

#### <a name="run-the-worker-at-regular-intervals"></a>Eseguire il ruolo di lavoro a intervalli regolari
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

#### <a name="insert-each-json-object-as-a-table-row"></a>Inserire ogni oggetto JSON come una riga della tabella
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

#### <a name="parse-each-blob"></a>Analizzare ogni BLOB
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

#### <a name="prepare-a-dictionary-for-each-json-document"></a>Preparare un dizionario per ciascun documento JSON
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

#### <a name="cast-the-json-document-into-c-class-telemetry-object-properties"></a>Eseguire il cast del documento JSON in proprietà dell'oggetto telemetria della classe C#
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

#### <a name="pageviewperformance-class-file-generated-out-of-json-document"></a>File di classe PageViewPerformance generato dal documento JSON
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


#### <a name="dbcontext-for-sql-interaction-by-entity-framework"></a>DBcontext per l'interazione con SQL da Entity Framework
    public class TelemetryContext : DbContext
    {
        public DbSet<PageViewPerformance> PageViewPerformanceContext { get; set; }
        public TelemetryContext()
            : base("name=TelemetryContext")
        {
        }
    }

Aggiungere la stringa di connessione di database con nome `TelemetryContext` in `app.config`.

## <a name="schema-information-only"></a>Schema (solo informazioni)
Questo è lo schema per la tabella che verrà generata per PageView.

> [!NOTE]
> Non è necessario eseguire questo script. Gli attributi nel documento JSON determinano le colonne nella tabella.
> 
> 

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

## <a name="next-steps"></a>Passaggi successivi

* [Write code to access your telemetry directly](http://dev.applicationinsights.io/) (Scrivere il codice per accedere direttamente ai dati di telemetria)
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
[start]: app-insights-overview.md





<!--HONumber=Jan17_HO4-->


