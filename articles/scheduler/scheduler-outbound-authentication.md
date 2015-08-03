<properties 
 pageTitle="Autenticazione in uscita dell'Utilità di pianificazione" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="05/12/2015" 
 ms.author="krisragh"/>
 
# Autenticazione in uscita dell'Utilità di pianificazione

I processi dell'Utilità di pianificazione potrebbero richiedere servizi che necessitano di autenticazione. In questo modo, un servizio richiamato può determinare se l'utilità di pianificazione può accedere alle sue risorse. Alcuni di questi servizi includono altri servizi di Azure, Salesforce.com, Facebook e siti Web custom protetti.

## Aggiunta e rimozione di autenticazione

Aggiungere un'autenticazione a un'utilità di pianificazione è semplice – è sufficiente aggiungere un elemento figlio JSON `authentication` all'`request` elemento durante la creazione o l’aggiornamento di un processo. I segreti passati al servizio dell’Utilità di pianificazione in una richiesta PUT, PATCH o POST – come parte dell’oggetto `authentication` – non vengono mai restituiti nelle risposte. Nelle risposte, le informazioni segrete sono impostati su null o potrebbero avere un token pubblico che rappresenti l'entità autenticata.

Per rimuovere l'autenticazione, effettuare un’azione PUT o PATCH in modo esplicito, impostando l’oggetto `authentication` su null. Nessuna proprietà di autenticazione verrà visualizzata nella risposta.

Attualmente, gli unici tipi di autenticazione supportati sono il modello `ClientCertificate` (per l'utilizzo di certificati client SSL/TLS), il modello `Basic` (per l'autenticazione di Base) e il modello `ActiveDirectoryOAuth` (per l'autenticazione OAuth di Active Directory).

## Corpo della richiesta per l'autenticazione ClientCertificate

Quando si aggiunge un'autenticazione utilizzando il modello `ClientCertificate`, specificare i seguenti elementi aggiuntivi nel corpo della richiesta.

|Elemento|Descrizione|
|:---|:---|
|_authentication (parent element)_|Oggetto di autenticazione per l'utilizzo di un certificato client SSL.|
|_type_|Obbligatorio. Tipo di autenticazione. Per i certificati client SSL, il valore deve essere `ClientCertificate`.|
|_pfx_|Obbligatorio. I contenuti del file PFX in codifica Base64.|
|_password_|Obbligatorio. La password per accedere al file PFX.|


## Corpo della risposta per l'autenticazione ClientCertificate

Quando viene inviata una richiesta con le informazioni di autenticazione, la risposta contiene i seguenti elementi relativi all'autenticazione.

|Elemento |Descrizione |
|:--|:--|
|_authentication (parent element)_ |Oggetto di autenticazione per l'utilizzo di un certificato client SSL.|
|_type_ |Tipo di autenticazione. Per i certificati client SSL, il valore è `ClientCertificate`.|
|_certificateThumbprint_ |L'identificazione personale del certificato.|
|_certificateSubjectName_ |Il nome distintivo del soggetto del certificato.|
|_certificateExpiration_ |La data di scadenza del certificato.|

## Esempio di richiesta e risposta per l'autenticazione ClientCertificate

La richiesta di esempio riportata di seguito effettua una richiesta PUT che incorpora l’autenticazione `ClientCertificate`. La richiesta è la seguente:


	PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
	x-ms-version: 2013-03-01
	User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
	Content-Type: application/json; charset=utf-8
	Host: management.core.windows.net
	Content-Length: 4013
	Expect: 100-continue

	{
	  "action": {
		"type": "http",
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication": {
			"type": "clientcertificate",
			"password": "test",
			"pfx": "long-pfx-key”
		  }
		}
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  }
	}

Una volta che la richiesta viene inviata, la risposta è la seguente:

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 721
	Content-Type: application/json; charset=utf-8
	Expires: -1
	Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
	x-ms-servedbyregion: ussouth2
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	 

	{
	  "id": "testScheduler",
	  "action": {
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication": {
			"type": "ClientCertificate",
			"certificateThumbprint": "C1645E2AF6317D9FCF9C78FE23F9DE0DAFAD2AB5",
			"certificateExpiration": "2021-01-01T08:00:00Z",
			"certificateSubjectName": "CN=Scheduler Management"
		  }
		},
		"type": "http"
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  },
	  "state": "enabled",
	  "status": {
		"nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
		"executionCount": 0,
		"failureCount": 0,
		"faultedCount": 0
	  }
	}
## Corpo della richiesta per l'autenticazione di Base

Quando si aggiunge un'autenticazione utilizzando il modello `Basic`, specificare i seguenti elementi aggiuntivi nel corpo della richiesta.

|Elemento|Descrizione|
|:--|:--|
|_authentication (parent element)_ |Oggetto di autenticazione per l’utilizzo dell'autenticazione di Base.|
|_type_ |Obbligatorio. Tipo di autenticazione. Per l'autenticazione di Base, il valore deve essere `Basic`.|
|_username_ |Obbligatorio. Nome utente da autenticare.|
|_password_ |Obbligatorio. Password da autenticare.|

## Corpo della risposta per l'autenticazione di Base

Quando viene inviata una richiesta con le informazioni di autenticazione, la risposta contiene i seguenti elementi relativi all'autenticazione.

|Elemento|Descrizione|
|:--|:--|
|_authentication (parent element)_ |Oggetto di autenticazione per l’utilizzo dell'autenticazione di Base.|
|_type_ |Tipo di autenticazione. Per l'autenticazione di Base, il valore è `Basic`.|
|_username_ |Il nome utente autenticato.|

## Esempio di richiesta e risposta per l'autenticazione di Base

La richiesta di esempio riportata di seguito effettua una richiesta PUT che incorpora l’autenticazione `Basic`. La richiesta è la seguente:

	PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
	x-ms-version: 2013-03-01
	User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
	Content-Type: application/json; charset=utf-8
	Host: management.core.windows.net
	Expect: 100-continue

	{
	  "action": {
		"type": "http",
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		"authentication":{  
		  "username":"user1",
		  "password":"password",
		  "type":"basic"
		  }           
		}
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  }
	}

Una volta che la richiesta viene inviata, la risposta è la seguente:

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 721
	Content-Type: application/json; charset=utf-8
	Expires: -1
	Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
	x-ms-servedbyregion: ussouth2
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET

	{
	  "id": "testScheduler",
	  "action": {
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication":{  
			"username":"user1",
			"type":"Basic"
		  }
		},
		"type": "http"
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  },
	  "state": "enabled",
	  "status": {
		"nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
		"executionCount": 0,
		"failureCount": 0,
		"faultedCount": 0
	  }
	}

## Corpo della richiesta per l'autenticazione ActiveDirectoryOAuth

Quando si aggiunge un'autenticazione utilizzando il modello `ActiveDirectoryOAuth`, specificare i seguenti elementi aggiuntivi nel corpo della richiesta.

|Elemento |Descrizione |
|:--|:--|
|_authentication (parent element)_ |Oggetto di autenticazione per l'autenticazione basata su ActiveDirectoryOAuth.|
|_type_ |Obbligatorio. Tipo di autenticazione. Per l'autenticazione ActiveDirectoryOAuth, il valore deve essere `ActiveDirectoryOAuth`.|
|_tenant_ |Obbligatorio. L'identificatore del tenant è un ID che viene utilizzato per identificare il tenant dell’Active Directory.|
|_audience_ |Obbligatorio. Viene impostato su https://management.core.windows.net/.|
|_clientId_ |Obbligatorio. Fornisce l'identificativo del client per l'applicazione Active Directory di Azure.|
|_secret_ |Obbligatorio. Segreto del client che richiede il token.|

## Corpo della risposta per l'autenticazione ActiveDirectoryOAuth

Quando viene inviata una richiesta con le informazioni di autenticazione, la risposta contiene i seguenti elementi relativi all'autenticazione.

|Elemento |Descrizione |
|:--|:--|
|_authentication (parent element)_ |Oggetto di autenticazione per l'autenticazione basata su ActiveDirectoryOAuth.|
|_type_ |Tipo di autenticazione. Per l'autenticazione ActiveDirectoryOAuth, il valore è `ActiveDirectoryOAuth`.|
|_tenant_ |L'identificativo del tenant utilizzato per identificare il tenant dell’Active Directory.|
|_audience_ |Viene impostato su https://management.core.windows.net/.|
|_clientId_ |L'identificativo del client per l'applicazione Active Directory di Azure.|

## Esempio di richiesta e risposta per l'autenticazione ActiveDirectoryOAuth

La richiesta di esempio riportata di seguito effettua una richiesta PUT che incorpora l’autenticazione `ActiveDirectoryOAuth`. La richiesta è la seguente:

	PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
	x-ms-version: 2013-03-01
	User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
	Content-Type: application/json; charset=utf-8
	Host: management.core.windows.net
	Expect: 100-continue

	{
	  "action": {
		"type": "http",
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication":{  
			"tenant":"contoso.com",
			"audience":"https://management.core.windows.net/",
			"clientId":"8a14db88-4d1a-46c7-8429-20323727dfab",
			"secret": "&lt;secret-key&gt;",
			"type":"ActiveDirectoryOAuth"
		  }                      
		}
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  }
	}

Una volta che la richiesta viene inviata, la risposta è la seguente:

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 721
	Content-Type: application/json; charset=utf-8
	Expires: -1
	Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
	x-ms-servedbyregion: ussouth2
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET


	{
	  "id": "testScheduler",
	  "action": {
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication":{  
			"tenant":"contoso.com",
			"audience":"https://management.core.windows.net/",
			"clientId":"8a14db88-4d1a-46c7-8429-20323727dfab",
			"type":"ActiveDirectoryOAuth"
		  }
		},
		"type": "http"
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  },
	  "state": "enabled",
	  "status": {
		"nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
		"executionCount": 0,
		"failureCount": 0,
		"faultedCount": 0
	  }
	}

## Vedere anche
 
 [Che cos'è l'Utilità di pianificazione?](scheduler-intro.md)
 
 [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione](scheduler-concepts-terms.md)
 
 [Introduzione all'uso dell'Utilità di pianificazione nel portale di gestione](scheduler-get-started-portal.md)
 
 [Piani e fatturazione nell'Utilità di pianificazione di Azure](scheduler-plans-billing.md)
 
 [Come creare pianificazioni complesse e operazioni ricorrenti avanzate con l'Utilità di pianificazione di Azure](scheduler-advanced-complexity.md)
 
 [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione](https://msdn.microsoft.com/library/dn528946)
 
 [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione](scheduler-powershell-reference.md)
 
 [Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione](scheduler-high-availability-reliability.md)
 
 [Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione](scheduler-limits-defaults-errors.md)
 
  

<!---HONumber=July15_HO4-->