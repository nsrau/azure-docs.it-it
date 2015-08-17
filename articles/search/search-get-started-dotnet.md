<properties
	pageTitle="Introduzione alla prima applicazione Ricerca di Azure in .NET | Microsoft Azure"
	description="Esercitazione sulla compilazione di una soluzione Visual Studio usando la libreria client .NET da .NET SDK di Ricerca di Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="hero-article" 
	ms.tgt_pltfrm="na"
	ms.date="07/08/2015"
	ms.author="heidist"/>

#Introduzione alla prima applicazione Ricerca di Azure in .NET#

Informazioni su come compilare un'applicazione di ricerca .NET personalizzata in Visual Studio 2013 o versione successiva che usa Ricerca di Azure per l'esperienza di ricerca. Nell'esercitazione viene usato [.NET SDK di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx) per compilare le classi per gli oggetti e le operazioni usate nell'esempio, nonché l'API REST del servizio Ricerca di Azure.

Per eseguire questo esempio, è necessario disporre di un servizio Ricerca di Azure, a cui è possibile iscriversi nel [portale di Azure](https://portal.azure.com).

> [AZURE.TIP]Scaricare il codice sorgente per questa esercitazione dalla pagina [Azure Search .NET Samples](http://go.microsoft.com/fwlink/p/?LinkId=530196).

##Informazioni sui dati##

L'applicazione di esempio usa i dati dei [servizi geologici degli Stati Uniti (USGS)](http://geonames.usgs.gov/domestic/download_data.htm) con il filtro dello stato del Rhode Island per ridurre la dimensione del set di dati. Tali dati saranno usati per compilare un'applicazione di ricerca che restituisce gli edifici di riferimento quali ospedali e scuole nonché caratteristiche geologiche come fiumi, laghi e vette.

In questa applicazione, il programma **DataIndexer** compila e carica l'indice usando un costrutto [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx), recuperando il set di dati filtrato dei servizi geologici degli Stati Uniti da un database SQL di Azure pubblico. Nel codice del programma vengono fornite credenziali e connessioni all'origine dati online. Non è necessaria ulteriore configurazione.

> [AZURE.NOTE]A questo set di dati è stato applicato un filtro per restare sotto il limite di 10.000 documenti del livello di prezzo gratuito. Se si usa il livello standard, questo limite non si applica. Per ulteriori informazioni sulla capacità per ogni livello di prezzo, vedere [Limiti e vincoli](https://msdn.microsoft.com/library/azure/dn798934.aspx).

##Creare il servizio##

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nell'indice, fare clic su **Nuovo** | **Dati + archiviazione** | **Ricerca**.

     ![][1]

3. Configurare il nome del servizio, il livello di prezzo, il gruppo di risorse, la sottoscrizione e la posizione. Queste impostazioni sono necessarie e non possono essere modificate dopo il provisioning del servizio.

     ![][2]

	- Il **nome del servizio** deve essere univoco, in lettere minuscole, lungo massimo 15 caratteri e senza spazi. Questo nome diventa parte dell'endpoint del servizio Ricerca di Azure. Vedere [Regole di denominazione](https://msdn.microsoft.com/library/azure/dn857353.aspx) per ulteriori informazioni sulle convenzioni di denominazione.

	- Il **livello di prezzo** determina capacità e fatturazione. Entrambi i livelli offrono le stesse funzionalità, ma a livelli diversi di risorse.

		- Il livello **gratuito** viene eseguito in cluster condivisi con altri sottoscrittori. Offre capacità sufficienti per provare le esercitazioni e scrivere codice per i modelli di prova, ma non è consigliato per le applicazioni in ambienti di produzione. La distribuzione di un servizio gratuito in genere richiede solo pochi minuti.
		- Il livello **standard** viene eseguito su risorse dedicate ed è altamente scalabile. Inizialmente viene eseguito il provisioning di un servizio standard con una replica e una partizione, ma è possibile regolare la capacità una volta creato il servizio. La distribuzione di un servizio standard richiede più tempo, in genere circa 15 minuti.

	- I **gruppi di risorse** sono contenitori per i servizi e le risorse usati per uno scopo comune. Ad esempio, se si intende creare un'applicazione di ricerca personalizzata basata su Ricerca di Azure, Siti Web di Azure, il servizio di archiviazione BLOB di Azure, è possibile creare un gruppo di risorse per raggruppare questi servizi nelle pagine di gestione del portale.

	- L'opzione **Sottoscrizione** consente di scegliere tra più sottoscrizioni, se si dispone di più di una sottoscrizione.

	- La **posizione** è l'area geografica del data center. Attualmente, tutte le risorse devono essere eseguite nello stesso data center. La distribuzione di risorse tra più data center non è supportata.

4. Fare clic su **Crea** per eseguire il provisioning del servizio.

Controllare le notifiche nell'indice. Quando il servizio è pronto per l'uso, viene visualizzato un avviso.

<a id="sub-2"></a>
##Individuare il nome del servizio e le chiavi API del servizio Ricerca di Azure

Dopo aver creato il servizio, è possibile tornare al portale per ottenere l'URL o `api-key`. Per le connessioni al servizio di ricerca è necessario disporre sia dell'URL che di una `api-key` per l'autenticazione della chiamata.

1. Nell'indice, fare clic su **Home**, quindi fare clic sul servizio Ricerca per aprire il dashboard del servizio.

2. Nel dashboard del servizio, saranno riportate sezioni per informazioni essenziali, nonché l'icona della chiave per l'accesso alle chiavi di amministrazione.

  	![][3]

3. Copiare l'URL del servizio e una chiave di amministrazione. Serviranno in seguito, quando dovranno essere aggiunti ai file app.config e Web.config nei progetti di Visual Studio.

##Avviare un nuovo progetto e soluzione##

Questa soluzione includerà due progetti:

- **DataIndexer**, un'applicazione di console Visual C#, usata per caricare i dati.
- **SimpleSearchMVCApp**, un'applicazione Web MVC ASP.NET Visual C#, usata per eseguire query e restituire i risultati della ricerca.

In questo passaggio saranno creati entrambi i progetti.

1. Avviare **Visual Studio** | **Nuovo progetto** | **Visual C#** | **Applicazione console**.
2. Denominare il progetto **DataIndexer** e la soluzione **AzureSearchDotNetDemo**.
3. In Esplora soluzioni, nella soluzione, fare clic con il pulsante destro del mouse su **Aggiungi** | **Nuovo progetto** | **Visual C#** | **Applicazione Web ASP.NET**.
4. Denominare il progetto **SimpleSearchMVCApp**.
5. Nel nuovo progetto ASP.NET, scegliere il modello MVC e deselezionare le opzioni per evitare di creare elementi di programma che non si desidera usare in questa esercitazione.

   Deselezionare le caselle di controllo per Hosting di Azure e Unit test e non impostare alcuna autenticazione.

   ![][10]

Al termine della creazione dei progetti, la soluzione dovrebbe apparire simile all'esempio riportato di seguito.

   ![][4]

##Installare la libreria client .NET e altri pacchetti di aggiornamento

1. Fare clic con il pulsante destro del mouse su **Gestisci pacchetti NuGet** nella soluzione in Esplora soluzioni.
2. Specificare **Aggiornamenti** | **Solo stabile** | **Aggiorna tutto**.

   ![][11]

3. Accettare le installazioni di ulteriori pacchetti in modo che vengano installate anche tutte le dipendenze.

4. Successivamente, installare la libreria client .NET di Ricerca di Azure. Assicurarsi di specificare correttamente la ricerca o non sarà possibile trovare facilmente il pacchetto. Fare di nuovo clic con il pulsante destro del mouse sull'opzione **Gestisci pacchetti NuGet**.

5. Specificare **Online** | **nuget.org** | **Includi versione provvisoria**, quindi cercare *azure.search* e installare la libreria.

   ![][12]

Di seguito viene fornito un elenco parziale degli assembly usati in questo esempio.

   ![][5]

##Aggiungere un riferimento all'assembly per System.Configuration.

**DataIndexer** usa **Sistem.Configuration** per leggere le impostazioni di configurazione in app.config.

1. Fare clic con il pulsante destro del mouse su **DataIndexer** | **Aggiungi** | **Riferimento** | **Framework** | **System.Configuration**. Selezionare la casella di controllo.
2. Fare clic su **OK**.

##Aggiornare i file di configurazione

Ogni progetto include file di configurazione che specificano il nome del servizio e la chiave api.

1. In **DataIndexer**, sostituire App.config con il seguente esempio, aggiornando ancora una volta [SERVICE NAME] e [SERVICE KEY] con i valori validi per il servizio.

   Il nome del servizio non è l'URL completo. Ad esempio, se l'endpoint del servizio di ricerca è **https://mysearchsrv.search.microsoft.net*, il nome del servizio da immettere in App.config è *mysearchsrv*.

	    <?xml version="1.0" encoding="utf-8"?>
	    <configuration>
	      <startup>
	         <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	      </startup>
	      <appSettings>
	        <add key="SearchServiceName" value="[SERVICE NAME]" />
	        <add key="SearchServiceApiKey" value="[SERVICE KEY]" />
	      </appSettings>
	    </configuration>

2. In **SimpleSearchMVCApp** sostituire Web.config con il seguente esempio, aggiornando ancora una volta [SERVICE NAME] e [SERVICE KEY] con i valori validi per il servizio.

		<?xml version="1.0" encoding="utf-8"?>
		<!--
		  For more information on how to configure your ASP.NET application, please visit
		  http://go.microsoft.com/fwlink/?LinkId=152368
		  -->
		<configuration>
		  <configSections>
		    <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
		    <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=5.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
		  </configSections>
		  <connectionStrings>
		    <add name="DefaultConnection" providerName="System.Data.SqlClient" connectionString="Data Source=(LocalDb)\v11.0;Initial Catalog=aspnet-SimpleMVCApp-20150303114355;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|\aspnet-SimpleMVCApp-20150303114355.mdf" />
		  </connectionStrings>
		  <appSettings>
		    <add key="SearchServiceName" value="[SEARCH SERVICE NAME]" />
		    <add key="SearchServiceApiKey" value="[API KEY]" />

		    <add key="webpages:Version" value="2.0.0.0" />
		    <add key="webpages:Enabled" value="false" />
		    <add key="PreserveLoginUrl" value="true" />
		    <add key="ClientValidationEnabled" value="true" />
		    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
		  </appSettings>
		  <system.web>
		    <httpRuntime targetFramework="4.5" />
		    <compilation debug="true" targetFramework="4.5" />
		    <authentication mode="Forms">
		      <forms loginUrl="~/Account/Login" timeout="2880" />
		    </authentication>
		    <pages>
		      <namespaces>
		        <add namespace="System.Web.Helpers" />
		        <add namespace="System.Web.Mvc" />
		        <add namespace="System.Web.Mvc.Ajax" />
		        <add namespace="System.Web.Mvc.Html" />
		        <add namespace="System.Web.Optimization" />
		        <add namespace="System.Web.Routing" />
		        <add namespace="System.Web.WebPages" />
		      </namespaces>
		    </pages>
		    <profile defaultProvider="DefaultProfileProvider">
		      <providers>
		        <add name="DefaultProfileProvider" type="System.Web.Providers.DefaultProfileProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />
		      </providers>
		    </profile>
		    <membership defaultProvider="DefaultMembershipProvider">
		      <providers>
		        <add name="DefaultMembershipProvider" type="System.Web.Providers.DefaultMembershipProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" enablePasswordRetrieval="false" enablePasswordReset="true" requiresQuestionAndAnswer="false" requiresUniqueEmail="false" maxInvalidPasswordAttempts="5" minRequiredPasswordLength="6" minRequiredNonalphanumericCharacters="0" passwordAttemptWindow="10" applicationName="/" />
		      </providers>
		    </membership>
		    <roleManager defaultProvider="DefaultRoleProvider">
		      <providers>
		        <add name="DefaultRoleProvider" type="System.Web.Providers.DefaultRoleProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />
		      </providers>
		    </roleManager>
		    <!--
		            If you are deploying to a cloud environment that has multiple web server instances,
		            you should change session state mode from "InProc" to "Custom". In addition,
		            change the connection string named "DefaultConnection" to connect to an instance
		            of SQL Server (including SQL Azure and SQL  Compact) instead of to SQL Server Express.
		      -->
		    <sessionState mode="InProc" customProvider="DefaultSessionProvider">
		      <providers>
		        <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
		      </providers>
		    </sessionState>
		  </system.web>
		  <system.webServer>
		    <validation validateIntegratedModeConfiguration="false" />
		    <handlers>
		      <remove name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" />
		      <remove name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" />
		      <remove name="ExtensionlessUrlHandler-Integrated-4.0" />
		      <add name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness32" responseBufferLimit="0" />
		      <add name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness64" responseBufferLimit="0" />
		      <add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />
		      <remove name="OPTIONSVerbHandler" />
		      <remove name="TRACEVerbHandler" />
		    </handlers>
		  </system.webServer>
		  <entityFramework>
		    <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
		      <parameters>
		        <parameter value="v12.0" />
		      </parameters>
		    </defaultConnectionFactory>
		  </entityFramework>
		  <runtime>
		    <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
		      <dependentAssembly>
		        <assemblyIdentity name="Newtonsoft.Json" publicKeyToken="30ad4fe6b2a6aeed" culture="neutral" />
		        <bindingRedirect oldVersion="0.0.0.0-6.0.0.0" newVersion="6.0.0.0" />
		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="WebGrease" publicKeyToken="31bf3856ad364e35" culture="neutral" />
		        <bindingRedirect oldVersion="0.0.0.0-1.6.5135.21930" newVersion="1.6.5135.21930" />
		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="Antlr3.Runtime" publicKeyToken="eb42632606e9261f" culture="neutral" />
		        <bindingRedirect oldVersion="0.0.0.0-3.5.0.2" newVersion="3.5.0.2" />
		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="System.Web.Helpers" publicKeyToken="31bf3856ad364e35" />
		        <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="System.Web.WebPages" publicKeyToken="31bf3856ad364e35" />
		        <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="System.Web.Mvc" publicKeyToken="31bf3856ad364e35" />
		        <bindingRedirect oldVersion="1.0.0.0-5.2.3.0" newVersion="5.2.3.0" />
		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="System.Web.WebPages.Razor" publicKeyToken="31bf3856ad364e35" />
		        <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
		      </dependentAssembly>
		    </assemblyBinding>
		  </runtime>
		</configuration>


##Modificare DataIndexer

Questo programma è un'applicazione console che si connette al servizio di ricerca, come specificato nel file app.config, crea l'indice e lo carica con il set di dati dei servizi geologici degli Stati Uniti in un database SQL di Azure.

Attualmente, la versione provvisoria della libreria client non supporta gli indicizzatori, quindi per questa parte dell'esercitazione verrà usata l'API REST per creare e usare un indicizzatore.

Prima di poter eseguire questo programma, eseguire due modifiche:

- Aggiungere **AzureSearchhelper.cs**, usato quando si chiama l'API REST per gestire le connessioni e serializzare e deserializzare richieste e risposte JSON.

- Sostituire **Program.cs**, usato per creare l'indice e l'indicizzatore, caricare i dati e scrivere i messaggi.

###Creare AzureSearchHelper.cs

Il codice che chiama l'API REST deve includere una classe che gestisce le connessioni e la serializzazione e deserializzazione di richieste e risposte JSON. Negli esempi forniti con Ricerca di Azure, questa classe viene in genere chiamata **AzureSearchHelper.cs**. È possibile creare questa classe e aggiungerla a **DataIndexer**, usando il codice seguente.

1. In Esplora soluzioni, fare clic con il pulsante destro del mouse su **DataIndexer** | **Aggiungi** | **Nuovo elemento** | **Codice** | **Classe**.
2. Denominare la classe **AzureSearchHelper**.
3. Sostituire il codice predefinito con il codice seguente.

		//Copyright 2015 Microsoft

		//Licensed under the Apache License, Version 2.0 (the "License");
		//you may not use this file except in compliance with the License.
		//You may obtain a copy of the License at

		//       http://www.apache.org/licenses/LICENSE-2.0

		//Unless required by applicable law or agreed to in writing, software
		//distributed under the License is distributed on an "AS IS" BASIS,
		//WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
		//See the License for the specific language governing permissions and
		//limitations under the License.

		using System;
		using System.Net.Http;
		using System.Text;
		using Newtonsoft.Json;
		using Newtonsoft.Json.Converters;
		using Newtonsoft.Json.Serialization;

		namespace DataIndexer
		{
		    public class AzureSearchHelper
		    {
		        public const string ApiVersionString = "api-version=2015-02-28";

		        private static readonly JsonSerializerSettings _jsonSettings;

		        static AzureSearchHelper()
		        {
		            _jsonSettings = new JsonSerializerSettings
		            {
		                Formatting = Formatting.Indented, // for readability, change to None for compactness
		                ContractResolver = new CamelCasePropertyNamesContractResolver(),
		                DateTimeZoneHandling = DateTimeZoneHandling.Utc
		            };

		            _jsonSettings.Converters.Add(new StringEnumConverter());
		        }

		        public static string SerializeJson(object value)
		        {
		            return JsonConvert.SerializeObject(value, _jsonSettings);
		        }

		        public static T DeserializeJson<T>(string json)
		        {
		            return JsonConvert.DeserializeObject<T>(json, _jsonSettings);
		        }

		        public static HttpResponseMessage SendSearchRequest(HttpClient client, HttpMethod method, Uri uri, string json = null)
		        {
		            UriBuilder builder = new UriBuilder(uri);
		            string separator = string.IsNullOrWhiteSpace(builder.Query) ? string.Empty : "&";
		            builder.Query = builder.Query.TrimStart('?') + separator + ApiVersionString;

		            var request = new HttpRequestMessage(method, builder.Uri);

		            if (json != null)
		            {
		                request.Content = new StringContent(json, Encoding.UTF8, "application/json");
		            }

		            return client.SendAsync(request).Result;
		        }

		        public static void EnsureSuccessfulSearchResponse(HttpResponseMessage response)
		        {
		            if (!response.IsSuccessStatusCode)
		            {
		                string error = response.Content == null ? null : response.Content.ReadAsStringAsync().Result;
		                throw new Exception("Search request failed: " + error);
		            }
		        }
		    }
		}



###Aggiornare Program.cs.

1. In Esplora soluzioni aprire **DataIndexer** | **Program.cs**
2. Sostituire il contenuto del file Program.cs con il codice seguente.

		using Microsoft.Azure;
		using Microsoft.Azure.Search;
		using Microsoft.Azure.Search.Models;
		using Microsoft.Spatial;
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Timers;

		namespace DataIndexer
		{
		    class Program
		    {
		        private static SearchServiceClient _searchClient;
		        private static SearchIndexClient _indexClient;

		        // This Sample shows how to delete, create, upload documents and query an index
		        static void Main(string[] args)
		        {
		            string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
		            string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

		            // Create an HTTP reference to the catalog index
		            _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
		            _indexClient = _searchClient.Indexes.GetClient("geonames");

		            Console.WriteLine("{0}", "Deleting index...\n");
		            if (DeleteIndex())
		            {
		                Console.WriteLine("{0}", "Creating index...\n");
		                CreateIndex();
		                Console.WriteLine("{0}", "Sync documents from Azure SQL...\n");
		                SyncDataFromAzureSQL();
		            }
		            Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
		            Console.ReadKey();
		        }

		        private static bool DeleteIndex()
		        {
		            // Delete the index if it exists
		            try
		            {
		                _searchClient.Indexes.Delete("geonames");
		            }
		            catch (Exception ex)
		            {
		                Console.WriteLine("Error deleting index: {0}\r\n", ex.Message.ToString());
		                Console.WriteLine("Did you remember to add your SearchServiceName and SearchServiceApiKey to the app.config?\r\n");
		                return false;
		            }

		            return true;
		        }

		        private static void CreateIndex()
		        {
		            // Create the Azure Search index based on the included schema
		            try
		            {
		                var definition = new Index()
		                {
		                    Name = "geonames",
		                    Fields = new[]
		                    {
		                        new Field("FEATURE_ID",     DataType.String)         { IsKey = true,  IsSearchable = false, IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
		                        new Field("FEATURE_NAME",   DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("FEATURE_CLASS",  DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("STATE_ALPHA",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("STATE_NUMERIC",  DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("COUNTY_NAME",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("COUNTY_NUMERIC", DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("ELEV_IN_M",      DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("ELEV_IN_FT",     DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("MAP_NAME",       DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("DESCRIPTION",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
		                        new Field("HISTORY",        DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
		                        new Field("DATE_CREATED",   DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("DATE_EDITED",    DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true}
		                    }
		                };

		                _searchClient.Indexes.Create(definition);
		            }
		            catch (Exception ex)
		            {
		                Console.WriteLine("Error creating index: {0}\r\n", ex.Message.ToString());
		            }

		        }

		        private static void SyncDataFromAzureSQL()
		        {
		            // This will use the Azure Search Indexer to synchronize data from Azure SQL to Azure Search
		            Uri _serviceUri = new Uri("https://" + ConfigurationManager.AppSettings["SearchServiceName"] + ".search.windows.net");
		            HttpClient _httpClient = new HttpClient();
		            _httpClient.DefaultRequestHeaders.Add("api-key", ConfigurationManager.AppSettings["SearchServiceApiKey"]);

		            Console.WriteLine("{0}", "Creating Data Source...\n");
		            Uri uri = new Uri(_serviceUri, "datasources/usgs-datasource");
		            string json = "{ 'name' : 'usgs-datasource','description' : 'USGS Dataset','type' : 'azuresql','credentials' : { 'connectionString' : 'Server=tcp:azs-playground.database.windows.net,1433;Database=usgs;User ID=reader;Password=EdrERBt3j6mZDP;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;' },'container' : { 'name' : 'GeoNamesRI' }} ";
		            HttpResponseMessage response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Put, uri, json);
		            if (response.StatusCode != HttpStatusCode.Created && response.StatusCode != HttpStatusCode.NoContent)
		            {
		                Console.WriteLine("Error creating data source: {0}", response.Content.ReadAsStringAsync().Result);
		                return;
		            }

		            Console.WriteLine("{0}", "Creating Indexer...\n");
		            uri = new Uri(_serviceUri, "indexers/usgs-indexer");
		            json = "{ 'name' : 'usgs-indexer','description' : 'USGS data indexer','dataSourceName' : 'usgs-datasource','targetIndexName' : 'geonames','parameters' : { 'maxFailedItems' : 10, 'maxFailedItemsPerBatch' : 5, 'base64EncodeKeys': false }}";
		            response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Put, uri, json);
		            if (response.StatusCode != HttpStatusCode.Created && response.StatusCode != HttpStatusCode.NoContent)
		            {
		                Console.WriteLine("Error creating indexer: {0}", response.Content.ReadAsStringAsync().Result);
		                return;
		            }

		            Console.WriteLine("{0}", "Syncing data...\n");
		            uri = new Uri(_serviceUri, "indexers/usgs-indexer/run");
		            response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Post, uri);
		            if (response.StatusCode != HttpStatusCode.Accepted)
		            {
		                Console.WriteLine("Error running indexer: {0}", response.Content.ReadAsStringAsync().Result);
		                return;
		            }

		            bool running = true;
		            Console.WriteLine("{0}", "Synchronization running...\n");
		            while (running)
		            {
		                uri = new Uri(_serviceUri, "indexers/usgs-indexer/status");
		                response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Get, uri);
		                if (response.StatusCode != HttpStatusCode.OK)
		                {
		                    Console.WriteLine("Error polling for indexer status: {0}", response.Content.ReadAsStringAsync().Result);
		                    return;
		                }

		                var result = AzureSearchHelper.DeserializeJson<dynamic>(response.Content.ReadAsStringAsync().Result);
		                if (result.lastResult != null)
		                {
		                    switch ((string)result.lastResult.status)
		                    {
		                        case "inProgress":
		                            Console.WriteLine("{0}", "Synchronization running...\n");
		                            Thread.Sleep(1000);
		                            break;

		                        case "success":
		                            running = false;
		                            Console.WriteLine("Synchronized {0} rows...\n", result.lastResult.itemsProcessed.Value);
		                            break;

		                        default:
		                            running = false;
		                            Console.WriteLine("Synchronization failed: {0}\n", result.lastResult.errorMessage);
		                            break;
		                    }
		                }
		            }
		        }
		    }
		}



##Compilare ed eseguire DataIndexer

1. Fare clic con il pulsante destro del mouse sul progetto **DataIndexer**.
2. Compilare il progetto.
3. Premere **F5** per eseguirlo.

Si noterà una finestra della console con questi messaggi.

![][6]

Nel portale, verrà visualizzato un nuovo indice di **nomi di aree geografiche**. L'aggiornamento del portale può richiedere diversi minuti, pertanto si consiglia di attendere un po' prima di fare clic sui risultati.

![][7]

##Modificare SimpleSearchMVCApp

**SimpleSearchMVC** è l'app Web che viene eseguita localmente in IIS Express. Fornisce una casella di ricerca e visualizza i risultati della ricerca in una tabella.

Prima di poter eseguire questo programma, eseguire tre modifiche:

- Sostituire **HomeController.cs**, usato per accettare l'input dell'utente. In questo esempio, il termine di ricerca viene archiviato in una variabile denominata *q*.

- Sostituire **index.cshtml**, una pagina Web che fornisce gli input dei termini di ricerca e visualizza i risultati della ricerca.

- Aggiungere **FeatureSearch.cs**, una classe che crea il client di ricerca ed esegue la ricerca.

###Aggiornare HomeController.cs

Sostituire il codice predefinito con il codice seguente.

	using Microsoft.Azure.Search.Models;
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.Web.Mvc;

	namespace SimpleSearchMVCApp.Controllers
	{
	    public class HomeController : Controller
	    {
	        //
	        // GET: /Home/
	        private FeaturesSearch _featuresSearch = new FeaturesSearch();

	        public ActionResult Index()
	        {
	            return View();
	        }

	        public ActionResult Search(string q = "")
	        {
	            // If blank search, assume they want to search everything
	            if (string.IsNullOrWhiteSpace(q))
	                q = "*";

	            return new JsonResult
	            {
	                JsonRequestBehavior = JsonRequestBehavior.AllowGet,
	                Data = _featuresSearch.Search(q)
	            };
	        }


	    }
	}


###Aggiornare index.cshtml

Sostituire il codice predefinito con il codice seguente.

	@{
	    ViewBag.Title = "Azure Search - Feature Search";
	}

	<script src="http://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.2.min.js"></script>
	<script type="text/javascript">

	    $(function () {
	        // Execute search if user clicks enter
	        $("#q").keyup(function (event) {
	            if (event.keyCode == 13) {
	                Search();
	            }
	        });
	    });

	    function Search() {
	        // We will post to the MVC controller and parse the full results on the client side
	        // You may wish to do additional pre-processing on the data before sending it back to the client
	        var q = $("#q").val();

	        $.post('/home/search',
	        {
	            q: q
	        },
	        function (data) {
	            var searchResultsHTML = "<tr><td>FEATURE NAME</td><td>FEATURE CLASS</td>";
	            searchResultsHTML += "<td>STATE ALPHA</td><td>COUNTY_NAME</td>";
	            searchResultsHTML += "<td>Elevation (m)</td><td>Elevation (ft)</td><td>MAP NAME</td>";
	            searchResultsHTML += "<td>DESCRIPTION</td><td>HISTORY</td><td>DATE CREATED</td>";
	            searchResultsHTML += "<td>DATE EDITED</td></tr>";
	            for (var i = 0; i < data.length; i++) {
	                searchResultsHTML += "<td>" + data[i].Document.FEATURE_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.FEATURE_CLASS + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.STATE_ALPHA + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.COUNTY_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_M + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_FT + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.MAP_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.DESCRIPTION + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.HISTORY + "</td>";
	                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_CREATED) + "</td>";
	                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_EDITED) + "</td></tr>";
	            }

	            $("#searchResults").html(searchResultsHTML);

	        });

	        function parseJsonDate(jsonDateString) {
	            if (jsonDateString != null)
	                return new Date(parseInt(jsonDateString.replace('/Date(', '')));
	            else
	                return "";
	        }
	    };

	</script>
	<h2>USGS Search for Rhode Island</h2>

	<div class="container">
	    <input type="search" name="q" id="q" autocomplete="off" size="100" /> <button onclick="Search();">Search</button>
	</div>
	<br />
	<div class="container">
	    <div class="row">
	        <table id="searchResults" border="1"></table>
	    </div>
	</div>


###Aggiungere FeaturesSearch.cs

Aggiungere una classe che fornisce funzionalità di ricerca all'applicazione.

1. In Esplora soluzioni, fare clic con il pulsante destro del mouse su **SimpleSearchMVCApp** | **Aggiungi** | **Nuovo elemento** | **Codice** | **Classe**.
2. Denominare la classe **FeaturesSearch**.
3. Sostituire il codice predefinito con il codice seguente.

		using Microsoft.Azure.Search;
		using Microsoft.Azure.Search.Models;
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.Linq;
		using System.Web;

		namespace SimpleSearchMVCApp
		{
		    public class FeaturesSearch
		    {
		        private static SearchServiceClient _searchClient;
		        private static SearchIndexClient _indexClient;

		        public static string errorMessage;

		        static FeaturesSearch()
		        {
		            try
		            {
		                string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
		                string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

		                // Create an HTTP reference to the catalog index
		                _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
		                _indexClient = _searchClient.Indexes.GetClient("geonames");
		            }
		            catch (Exception e)
		            {
		                errorMessage = e.Message.ToString();
		            }
		        }

		        public DocumentSearchResponse Search(string searchText)
		        {
		            // Execute search based on query string
		            try
		            {
		                SearchParameters sp = new SearchParameters() { SearchMode = SearchMode.All };
		                return _indexClient.Documents.Search(searchText, sp);
		            }
		            catch (Exception ex)
		            {
		                Console.WriteLine("Error querying index: {0}\r\n", ex.Message.ToString());
		            }
		            return null;
		        }

		    }
		}

###Impostare SimpleSearchMVCApp come progetto di avvio

Fare clic con il pulsante destro del mouse sul progetto **SimpleSearchMVCApp** per impostarlo come progetto di avvio.

##Compilare ed eseguire SimpleSearchMVCApp

Quando si compila e si esegue questo programma, nel browser predefinito è presente una pagina Web con una casella di ricerca per l'accesso ai dati dei servizi geologici degli Stati Uniti archiviati nell'indice nel servizio Ricerca di Azure.

![][8]

###Eseguire ricerche sui dati dei servizi geologici degli Stati Uniti

Il set di dati dei servizi geologici degli Stati Uniti include i dati relativi allo stato del Rhode Island. Se si fa clic su **Ricerca** su una casella di ricerca vuota, si otterranno le prime 50 voci, ossia l'impostazione predefinita.

Immettendo un termine di ricerca si fornisce al motore di ricerca i criteri per restringere la ricerca. Provare a immettere un nome locale. *Roger Williams* è stato il primo governatore del Rhode Island. Numerosi parchi, edifici e scuole prendono il suo nome.

![][9]

È inoltre possibile tentare queste query, aggiungendo o rimuovendo frasi o operatori per ridefinire l'ambito della ricerca:

- Pawtucket OR Pembroke
- goose +cape -neck


##Passaggi successivi##

Questa è la prima esercitazione di Ricerca di Azure basata su .NET e sul set di dati dei servizi geologici degli Stati Uniti. Nel corso del tempo, l'esercitazione sarà ampliata per illustrare le funzionalità di ricerca aggiuntive che potrebbero essere utili nelle soluzioni personalizzate.

Se si dispone già delle nozioni di base di Ricerca di Azure, è possibile usare questo esempio come base di prova per i suggerimenti di alternative (query di suggerimento per la digitazione e completamento automatico), filtri ed esplorazione basata su facet. È inoltre possibile migliorare la pagina dei risultati della ricerca aggiungendo conteggi e raggruppando i documenti in modo che gli utenti possano sfogliare i risultati.

Novità in Ricerca di Azure È consigliabile provare altre esercitazioni per acquisire consapevolezza di ciò che è possibile creare. Visitare la [pagina della documentazione](http://azure.microsoft.com/documentation/services/search/) per trovare ulteriori risorse. È inoltre possibile visualizzare i collegamenti nell'[elenco di video ed esercitazioni](https://msdn.microsoft.com/library/azure/dn798933.aspx) per accedere a ulteriori informazioni.

<!--Image references-->
[1]: ./media/search-get-started-dotnet/create-search-portal-1.PNG
[2]: ./media/search-get-started-dotnet/create-search-portal-2.PNG
[3]: ./media/search-get-started-dotnet/create-search-portal-3.PNG

[4]: ./media/search-get-started-dotnet/AzSearch-DotNet-VSSolutionExplorer.png
[5]: ./media/search-get-started-dotnet/AzSearch-DotNet-Assembly.png

[6]: ./media/search-get-started-dotnet/consolemessages.png
[7]: ./media/search-get-started-dotnet/portalindexstatus.png
[8]: ./media/search-get-started-dotnet/usgssearchbox.png
[9]: ./media/search-get-started-dotnet/rogerwilliamsschool.png

[10]: ./media/search-get-started-dotnet/AzSearch-DotNet-MVCOptions.PNG
[11]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-1.PNG
[12]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-2.PNG
 

<!---HONumber=August15_HO6-->