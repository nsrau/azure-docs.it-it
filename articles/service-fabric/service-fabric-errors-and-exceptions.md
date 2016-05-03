<properties
   pageTitle="Eccezioni FabricClient comuni | Microsoft Azure"
   description="Vengono descritti errori ed eccezioni comuni che possono essere generati dalle API FabricClient durante l'esecuzione di operazioni di gestione di applicazioni e cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2016"
   ms.author="ryanwi"/>

# Errori ed eccezioni comuni quando si usano le API FabricClient
Le API [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) consentono agli amministratori di cluster e applicazioni di eseguire attività amministrative su applicazioni, servizi o cluster Service Fabric. Ad esempio, la distribuzione, l'aggiornamento e la rimozione di applicazioni, il controllo dell'integrità di un cluster o il test di un servizio. Gli sviluppatori di applicazioni e gli amministratori di cluster possono usare le API FabricClient per sviluppare strumenti per la gestione del cluster e delle applicazioni Service Fabric.

Esistono molti tipi diversi di operazioni che possono essere eseguite tramite FabricClient. Ogni metodo può generare eccezioni per errori dovuti a input non corretti, errori di runtime o problemi temporanei dell'infrastruttura. Per individuare le eccezioni generate da un metodo specifico, vedere la documentazione di riferimento delle API . Esistono alcune eccezioni, tuttavia, che possono essere generate da diverse API [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). Nella tabella seguente vengono elencate le eccezioni comuni alle API FabricClient.

|Eccezione| Generata quando|
|---------|:-----------|
|[System.Fabric.FabricObjectClosedException](https://msdn.microsoft.com/library/system.fabric.fabricobjectclosedexception.aspx)|L'oggetto [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) si trova in uno stato chiuso. Eliminare l'oggetto [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) che si sta usando e creare un'istanza di un nuovo oggetto [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). |
|[System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)|L'operazione ha raggiunto il timeout. [OperationTimedOut](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) viene restituito quando per il completamento dell'operazione occorre più tempo di quello stabilito in MaxOperationTimeout.|
|[System.UnauthorizedAccessException](https://msdn.microsoft.com/it-IT/library/system.unauthorizedaccessexception.aspx)|Il controllo di accesso per l'operazione non è riuscito. Viene restituito E\_ACCESSDENIED.|
|[System.Fabric.FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx)|Si è verificato un errore di runtime durante l'operazione. Tutti i metodi FabricClient possono potenzialmente generare un'eccezione [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx); la proprietà [ErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricexception.errorcode.aspx) indica la causa esatta dell'eccezione. I codici di errore sono definiti nell'enumerazione [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx).|
|[System.Fabric.FabricTransientException](https://msdn.microsoft.com/library/system.fabric.fabrictransientexception.aspx)|L'operazione non è riuscita a causa di una condizione di errore temporanea di qualche tipo. Ad esempio, un'operazione potrebbe non riuscire perché un quorum di repliche non è temporaneamente raggiungibile. Le eccezioni temporanee corrispondono alle operazioni non riuscite che è possibile ritentare.|

Alcuni errori [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) comuni che possono essere restituiti in un'eccezione [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx):

|Errore| Condizione|
|---------|:-----------|
|CommunicationError|L'operazione non è riuscita a causa di un errore di comunicazione, ripetere l'operazione.|
|InvalidCredentialType|Il tipo di credenziale non è valido.|
|InvalidX509FindType|Il tipo X509FindType non è valido.|
|InvalidX509StoreLocation|Il percorso dell'archivio X509 non è valido.|
|InvalidX509StoreName|Il nome dell'archivio X509 non è valido.|
|InvalidX509Thumbprint|La stringa di identificazione personale del certificato X509 non è valida.|
|InvalidProtectionLevel|Il livello di protezione non è valido.|
|InvalidX509Store|Non è possibile aprire l'archivio certificati X509.|
|InvalidSubjectName|Il nome soggetto non è valido.|
|InvalidAllowedCommonNameList|Il formato della stringa dell'elenco nomi comuni non è valido. Deve essere un elenco delimitato da virgole.|

<!---HONumber=AcomDC_0420_2016-->