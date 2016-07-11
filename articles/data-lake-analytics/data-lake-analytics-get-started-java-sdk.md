<properties
   pageTitle="Usare l'SDK Java di Analisi Data Lake per lo sviluppo di applicazioni | Azure"
   description="Usare l'SDK Java di Analisi Azure Data Lake per lo sviluppo di applicazioni"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# Esercitazione: Introduzione ad Analisi Azure Data Lake con l'SDK Java

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Informazioni su come usare l'SDK Java di Analisi Azure Data Lake per creare un account Azure Data Lake ed eseguire operazioni di base, ad esempio creare cartelle, caricare e scaricare file di dati, eliminare l'account e lavorare con i processi. Per altre informazioni su Data Lake, vedere [Panoramica di Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md).

In questa esercitazione verrà sviluppata un'applicazione console Java che include esempi di attività amministrative comuni, inoltre saranno creati dati di test e sarà inviato un processo. Per eseguire la stessa esercitazione usando altri strumenti supportati, fare clic sulle schede nella parte superiore di questa sezione.

[AZURE.INCLUDE [basic-process-include](../../includes/data-lake-analytics-basic-process.md)]

## Prerequisiti

* Java Development Kit (JDK) 8 con Java versione 1.8.
* IntelliJ o un altro ambiente di sviluppo Java adatto. Questo prerequisito è facoltativo ma consigliato. Le istruzioni riportate di seguito usano IntelliJ.
* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Abilitare la sottoscrizione di Azure** per l'anteprima pubblica di Analisi Data Lake. Vedere le [istruzioni](data-lake-analytics-get-started-portal.md#signup).
* Creare un'applicazione Azure Active Directory (AAD) e recuperarne l'**ID client**, l'**ID tenant** e la **chiave**. Per altre informazioni sulle applicazioni AAD e le istruzioni su come ottenere un ID client, vedere [Creare un'applicazione e un'entità servizio di Active Directory tramite il portale](../resource-group-create-service-principal-portal.md). Dopo aver creato l'applicazione e generato la chiave, anche l'URI di risposta e la chiave saranno disponibili dal portale.

## Come si esegue l'autenticazione tramite Azure Active Directory?

Il frammento di codice riportato di seguito fornisce il codice per l'autenticazione **non interattiva**, in cui l'applicazione fornisce le proprie credenziali.

Perché questa esercitazione possa funzionare, è necessario concedere all'applicazione le autorizzazioni necessarie per creare risorse in Azure. Ai fini di questa esercitazione, è **consigliabile** concedere all'applicazione autorizzazioni di Collaboratore soltanto per un gruppo di risorse nuovo, vuoto e inutilizzato nella sottoscrizione di Azure.

## Creare un'applicazione Java

1. Aprire IntelliJ e creare un nuovo progetto Java usando il modello **Command Line App**.

2. Fare clic con il pulsante destro del mouse sul progetto sulla sinistra dello schermo e fare clic su **Add Framework Support** (Aggiungi supporto framework). Scegliere **Maven** e fare clic su **OK**.

3. Aprire il file **"pom.xml"** appena creato e aggiungere il frammento di testo seguente tra il tag **</version>** e il tag **</project>**:

    NOTA: questo passaggio è temporaneo e non sarà più necessario quando l'SDK di Analisi Azure Data Lake sarà disponibile in Maven. Quando l'SDK sarà disponibile in Maven l'articolo verrà aggiornato. Tutti gli aggiornamenti futuri di questo SDK saranno disponibili tramite Maven.

        <repositories>
        	<repository>
	            <id>adx-snapshots</id>
	            <name>Azure ADX Snapshots</name>
	            <url>http://adxsnapshots.azurewebsites.net/</url>
	            <layout>default</layout>
	            <snapshots>
                	<enabled>true</enabled>
            	</snapshots>
        	</repository>
        	<repository>
	            <id>oss-snapshots</id>
	            <name>Open Source Snapshots</name>
	            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
	            <layout>default</layout>
	            <snapshots>
	                <enabled>true</enabled>
	                <updatePolicy>always</updatePolicy>
	            </snapshots>
        	</repository>
    	</repositories>
    	<dependencies>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-client-authentication</artifactId>
	            <version>1.0.0-20160513.000802-24</version>
	        </dependency>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-client-runtime</artifactId>
	            <version>1.0.0-20160513.000812-28</version>
	        </dependency>
	        <dependency>
	            <groupId>com.microsoft.rest</groupId>
	            <artifactId>client-runtime</artifactId>
	            <version>1.0.0-20160513.000825-29</version>
	        </dependency>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-mgmt-datalake-store</artifactId>
	            <version>1.0.0-SNAPSHOT</version>
	        </dependency>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-mgmt-datalake-analytics</artifactId>
	            <version>1.0.0-SNAPSHOT</version>
	        </dependency>
    	</dependencies>


4. Passare a **File**, **Settings** (Impostazioni), **Build** (Compilazione), **Execution** (Esecuzione), **Deployment** (Distribuzione). Selezionare **Build Tools** (Strumenti di compilazione), **Maven** e quindi **Importing** (Importazione). Selezionare **Import Maven projects automatically** (Importa automaticamente progetti Maven).

5. Aprire **Main.java** e sostituire il blocco di codice esistente con il codice seguente. Immettere anche i valori per i parametri indicati nel frammento di codice, ad esempio **localFolderPath**, **\_adlaAccountName**, **\_adlsAccountName** e **\_resourceGroupName** e sostituire i segnaposto per **CLIENT-ID**, **CLIENT-SECRET**, **TENANT-ID** e **SUBSCRIPTION-ID**.

	Questo codice crea gli account Archivio Data Lake e Analisi Data Lake, crea i file nell'archivio, esegue un processo, recupera lo stato del processo, scarica l'output del processo e infine elimina l'account.

	>[AZURE.NOTE] Attualmente esiste un problema noto con il servizio di Azure Data Lake. Se l'applicazione di esempio viene interrotta o si verifica un errore, potrebbe essere necessario eliminare manualmente gli account Archivio Data Lake e Analisi Data Lake che lo script crea. Se non si ha familiarità con il portale, la guida [Gestire Analisi Azure Data Lake tramite il portale di Azure](data-lake-analytics-manage-use-portal.md) descrive come iniziare.


		package com.company;

		import com.microsoft.azure.CloudException;
		import com.microsoft.azure.credentials.ApplicationTokenCredentials;
		import com.microsoft.azure.management.datalake.store.*;
		import com.microsoft.azure.management.datalake.store.models.*;
		import com.microsoft.azure.management.datalake.analytics.*;
		import com.microsoft.azure.management.datalake.analytics.models.*;
		import com.microsoft.rest.credentials.ServiceClientCredentials;
		import java.io.*;
		import java.nio.charset.Charset;
		import java.nio.file.Files;
		import java.nio.file.Paths;
		import java.util.ArrayList;
		import java.util.UUID;
		import java.util.List;
		
		public class Main {
		    private static String _adlsAccountName;
		    private static String _adlaAccountName;
		    private static String _resourceGroupName;
		    private static String _location;
		
		    private static String _tenantId;
		    private static String _subId;
		    private static String _clientId;
		    private static String _clientSecret;
		
		    private static DataLakeStoreAccountManagementClient _adlsClient;
		    private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
		    private static DataLakeAnalyticsAccountManagementClient _adlaClient;
		    private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
		    private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
		
		    public static void main(String[] args) throws Exception {
		        _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
		        _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>";
		        _resourceGroupName = "<RESOURCE-GROUP-NAME>";
		        _location = "East US 2";
		
		        _tenantId = "<TENANT-ID>";
		        _subId =  "<SUBSCRIPTION-ID>";
		        _clientId = "<CLIENT-ID>";
		
		        _clientSecret = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.
		
		        String localFolderPath = "C:\\local_path\"; // TODO: Change this to any unused, new, empty folder on your local machine.
		
		        // Authenticate
		        ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
		        SetupClients(creds);
		
		        // Create Data Lake Store and Analytics accounts
		        WaitForNewline("Authenticated.", "Creating NEW accounts.");
		        CreateAccounts();
		        WaitForNewline("Accounts created.", "Displaying accounts.");
		
		        // List Data Lake Store and Analytics accounts that this app can access
		        System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
		        List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
		        for (DataLakeStoreAccount acct : adlsListResult) {
		            System.out.println(acct.getName());
		        }
		        System.out.println(String.format("All ADL Analytics accounts that this app can access in subscription %s:", _subId));
		        List<DataLakeAnalyticsAccount> adlaListResult = _adlaClient.getAccountOperations().list().getBody();
		        for (DataLakeAnalyticsAccount acct : adlaListResult) {
		            System.out.println(acct.getName());
		        }
		        WaitForNewline("Accounts displayed.", "Creating files.");
		
		        // Create a file in Data Lake Store: input1.csv
		        // TODO: these change order in the next patch
		        byte[] bytesContents = "123,abc".getBytes();
		        _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, "/input1.csv", bytesContents, true);
		
		        WaitForNewline("File created.", "Submitting a job.");
		
		        // Submit a job to Data Lake Analytics
		        UUID jobId = SubmitJobByScript("@input =  EXTRACT Data string FROM "/input1.csv" USING Extractors.Csv(); OUTPUT @input TO @"/output1.csv" USING Outputters.Csv();", "testJob");
		        WaitForNewline("Job submitted.", "Getting job status.");
		
		        // Wait for job completion and output job status
		        System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
		        System.out.println("Waiting for job completion.");
		        WaitForJob(jobId);
		        System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
		        WaitForNewline("Job completed.", "Downloading job output.");
		
		        // Download job output from Data Lake Store
		        DownloadFile("/output1.csv", localFolderPath + "output1.csv");
		        WaitForNewline("Job output downloaded.", "Deleting file.");
		
		        // Delete file from Data Lake Store
		        DeleteFile("/output1.csv");
		        WaitForNewline("File deleted.", "Deleting account.");
		
		        // Delete account
		        _adlsClient.getAccountOperations().delete(_resourceGroupName, _adlsAccountName);
		        _adlaClient.getAccountOperations().delete(_resourceGroupName, _adlaAccountName);
		        WaitForNewline("Account deleted.", "DONE.");
		    }
		
		    //Set up clients
		    public static void SetupClients(ServiceClientCredentials creds)
		    {
		        _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
		        _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
		        _adlaClient = new DataLakeAnalyticsAccountManagementClientImpl(creds);
		        _adlaJobClient = new DataLakeAnalyticsJobManagementClientImpl(creds);
		        _adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClientImpl(creds);
		        _adlsClient.setSubscriptionId(_subId);
		        _adlaClient.setSubscriptionId(_subId);
		    }
		
		    // Helper function to show status and wait for user input
		    public static void WaitForNewline(String reason, String nextAction)
		    {
		        if (nextAction == null)
		            nextAction = "";
		
		        System.out.println(reason + "\r\nPress ENTER to continue...");
		        try{System.in.read();}
		        catch(Exception e){}
		
		        if (!nextAction.isEmpty())
		        {
		            System.out.println(nextAction);
		        }
		    }
		
		    // Create Data Lake Store and Analytics accounts
		    public static void CreateAccounts() throws InterruptedException, CloudException, IOException {
		        // Create ADLS account
		        DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
		        adlsParameters.setLocation(_location);
		
		        _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);
		
		        // Create ADLA account
		        DataLakeStoreAccountInfo adlsInfo = new DataLakeStoreAccountInfo();
		        adlsInfo.setName(_adlsAccountName);
		
		        DataLakeStoreAccountInfoProperties adlsInfoProperties = new DataLakeStoreAccountInfoProperties();
		        adlsInfo.setProperties(adlsInfoProperties);
		
		        List<DataLakeStoreAccountInfo> adlsInfoList = new ArrayList<DataLakeStoreAccountInfo>();
		        adlsInfoList.add(adlsInfo);
		
		        DataLakeAnalyticsAccountProperties adlaProperties = new DataLakeAnalyticsAccountProperties();
		        adlaProperties.setDataLakeStoreAccounts(adlsInfoList);
		        adlaProperties.setDefaultDataLakeStoreAccount(_adlsAccountName);
		
		        DataLakeAnalyticsAccount adlaParameters = new DataLakeAnalyticsAccount();
		        adlaParameters.setLocation(_location);
		        adlaParameters.setName(_adlaAccountName);
		        adlaParameters.setProperties(adlaProperties);
		
		            /* If this line generates an error message like "The deep update for property 'DataLakeStoreAccounts' is not supported", please delete the ADLS and ADLA accounts via the portal and re-run your script. */
		
		        _adlaClient.getAccountOperations().create(_resourceGroupName, _adlaAccountName, adlaParameters);
		    }
		
		    //todo: this changes in the next version of the API
		    public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
		        byte[] bytesContents = contents.getBytes();
		
		        _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path, bytesContents, force);
		    }
		
		    public static void DeleteFile(String filePath) throws IOException, CloudException {
		        _adlsFileSystemClient.getFileSystemOperations().delete(filePath, _adlsAccountName);
		    }
		
		    // Download file
		    public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
		        InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(srcPath, _adlsAccountName).getBody();
		
		        PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());
		
		        String fileContents = "";
		        if (stream != null) {
		            Writer writer = new StringWriter();
		
		            char[] buffer = new char[1024];
		            try {
		                Reader reader = new BufferedReader(
		                        new InputStreamReader(stream, "UTF-8"));
		                int n;
		                while ((n = reader.read(buffer)) != -1) {
		                    writer.write(buffer, 0, n);
		                }
		            } finally {
		                stream.close();
		            }
		            fileContents =  writer.toString();
		        }
		
		        pWriter.println(fileContents);
		        pWriter.close();
		    }
		
		    // Submit a U-SQL job by providing script contents.
		    // Returns the job ID
		    public static UUID SubmitJobByScript(String script, String jobName) throws IOException, CloudException {
		        UUID jobId = java.util.UUID.randomUUID();
		        USqlJobProperties properties = new USqlJobProperties();
		        properties.setScript(script);
		        JobInformation parameters = new JobInformation();
		        parameters.setName(jobName);
		        parameters.setJobId(jobId);
		        parameters.setType(JobType.USQL);
		        parameters.setProperties(properties);
		
		        JobInformation jobInfo = _adlaJobClient.getJobOperations().create(_adlaAccountName, jobId, parameters).getBody();
		
		        return jobId;
		    }
		
		    // Wait for job completion
		    public static JobResult WaitForJob(UUID jobId) throws IOException, CloudException {
		        JobInformation jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName, jobId).getBody();
		        while (jobInfo.getState() != JobState.ENDED)
		        {
		            jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName,jobId).getBody();
		        }
		        return jobInfo.getResult();
		    }
		
		    // Get job status
		    public static String GetJobStatus(UUID jobId) throws IOException, CloudException {
		        JobInformation jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName, jobId).getBody();
		        return jobInfo.getState().toValue();
		    }
		}

6. Seguire le istruzioni per eseguire e completare l'applicazione.


## Vedere anche

- Per visualizzare la stessa esercitazione usando altri strumenti, scegliere i selettori di scheda nella parte superiore della pagina.
- Per visualizzare una query più complessa, vedere [Analizzare i log del sito Web mediante Analisi Data Lake di Azure](data-lake-analytics-analyze-weblogs.md).
- Per iniziare a sviluppare applicazioni U-SQL, vedere [Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Per altre informazioni su U-SQL, vedere l'[Esercitazione: Introduzione al linguaggio U-SQL di Analisi Azure Data Lake](data-lake-analytics-u-sql-get-started.md) e il [riferimento al linguaggio U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).
- Per informazioni sulle attività di gestione, vedere [Gestire Analisi di Azure Data Lake tramite il portale di Azure](data-lake-analytics-manage-use-portal.md).
- Per una panoramica su Analisi Data Lake, vedere [Panoramica di Analisi Data Lake di Azure](data-lake-analytics-overview.md).

<!---HONumber=AcomDC_0629_2016-->