<properties 
	pageTitle="Come implementare il ripristino di emergenza usando il backup e il ripristino dei servizi in Gestione API di Azure" 
	description="Informazioni su come usare il backup e il ripristino per eseguire il ripristino di emergenza in Gestione API di Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Come implementare il ripristino di emergenza utilizzando il backup e il ripristino dei servizi in Gestione API di Azure

Scegliendo di pubblicare e gestire le API tramite Gestione API di Azure è possibile sfruttare molte funzionalità di tolleranza di errore e di infrastruttura che sarebbe altrimenti necessario progettare, implementare e gestire. La piattaforma di Azure permette di mitigare una vasta gamma di potenziali errori a un costo nettamente inferiore.

Per risolvere i problemi di disponibilità che colpiscono l'area in cui è ospitato il servizio di Gestione API, è necessario essere pronti a ripristinare il servizio in un'area diversa in qualsiasi momento. In base ai propri obiettivi in termini di disponibilità e tempi di ripristino, è consigliabile riservare un servizio di backup in una o più aree e provare a mantenere sincronizzati la configurazione e il contenuto con il servizio attivo. La funzionalità di backup e ripristino dei servizi fornisce il blocco predefinito necessario per implementare la propria strategia di ripristino di emergenza.

La funzionalità di backup e ripristino dei servizi è disponibile tramite l'API REST di Gestione servizi. Per le istruzioni su come ottenere l'accesso all'API, vedere [Autenticazione delle richieste di Gestione risorse di Azure][Autenticazione delle richieste di Gestione risorse di Azure].

## Contenuto dell'argomento

-   [Backup di un servizio di Gestione API][Backup di un servizio di Gestione API]
-   [Ripristino di un servizio di Gestione API][Ripristino di un servizio di Gestione API]

## <a name="step1"> </a>Backup di un servizio di Gestione API

Per eseguire il backup di un servizio di gestione API, emettere la seguente richiesta HTTP:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

dove:

-   `subscriptionId` - ID della sottoscrizione contenente il servizio di Gestione API di cui si sta tentando di eseguire il backup
-   `resourceGroupName` - Una stringa nel formato "Api-Default-{service-region}", dove `service-region` identifica l'area di Azure in cui è ospitato il servizio di Gestione API di cui si sta tentando di eseguire il backup, ad esempio `North-Central-US`
-   `serviceName` - Il nome del servizio di Gestione API di cui sta eseguendo il backup specificato quando è stato creato
-   `api-version` - Sostituire con `2014-02-14`

Nel corpo della richiesta, specificare l'account di archiviazione, la chiave di accesso, il nome del contenitore BLOB e il nome del backup di destinazione di Azure:

    '{  
        storageAccount : "{storage account for the backup}",  
        accessKey : "{access key for the account}",  
        containerName : "{backup container name}",  
        backupName : "{backup blob name}"  
    }'

Impostare il valore dell'intestazione della richiesta `Content-Type` su `application\json`.

Il backup è un'operazione a lunga esecuzione che potrebbe richiedere diversi minuti per essere completata. Se la richiesta viene eseguita correttamente e il processo di backup viene avviato, si riceverà un codice di stato risposta `202 Accepted` con un'intestazione `Location`. Effettuare richieste "GET" all'URL nell'intestazione `Location` per conoscere lo stato dell'operazione. Durante l'esecuzione del backup si continuerà a ricevere il codice di stato "202 - Accettato". Il codice risposta `200 OK` indicherà il completamento dell'operazione di backup.

**Nota**:

-   Il **contenitore** specificato nel corpo della richiesta **deve esistere**.
-   Mentre il backup è in corso, **non tentare di eseguire alcuna operazione di gestione dei servizi**, ad esempio l'aggiornamento o il downgrade di livelli, la modifica di nomi di dominio e così via.
-   Il ripristino di un **backup è garantito solo per 7 giorni** dal momento della sua creazione.
-   I **dati di utilizzo** usati per creare report analitici **non sono inclusi** nel backup. Utilizzare l'[API REST di Gestione API di Azure][API REST di Gestione API di Azure] per recuperare periodicamente i report analitici e custodirli al sicuro.
-   La frequenza con cui si eseguono i backup dei servizi influenzerà i propri obiettivi relativi ai punti di ripristino. Per ridurla al minimo, si consiglia di implementare backup regolari e di eseguire backup su richiesta dopo aver apportato modifiche importanti al servizio di Gestione API.
-   Le **modifiche** apportate alla configurazione del servizio (ad esempio alle API, ai criteri, all'aspetto del portale per sviluppatori) durante l'esecuzione del processo di backup **potrebbero non essere incluse nel backup e potrebbero quindi andare perse**.

## <a name="step2"> </a>Ripristino di un servizio di Gestione API

Per ripristinare un servizio di Gestione API da un backup creato in precedenza, creare la seguente richiesta HTTP:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

dove:

-   `subscriptionId` - ID della sottoscrizione contenente il servizio di Gestione API in cui si sta ripristinando un backup
-   `resourceGroupName` - Una stringa nel formato "Api-Default-{service-region}", dove `service-region` identifica l'area di Azure in cui è ospitato il servizio di Gestione API in cui si ripristinando un backup, ad esempio `North-Central-US`
-   `serviceName` - Il nome del servizio di Gestione API in cui si sta effettuando il ripristino specificato quando è stato creato
-   `api-version` - Sostituire con `2014-02-14`

Nel corpo della richiesta, specificare la posizione del file di backup, ad esempio l'account di accesso, la chiave di accesso, il nome del contenitore BLOB e il nome del backup di Azure:

    '{  
        storageAccount : "{storage account for the backup}",  
        accessKey : "{access key for the account}",  
        containerName : "{backup container name}",  
        backupName : "{backup blob name}"  
    }'

Impostare il valore dell'intestazione della richiesta `Content-Type` su `application\json`.

Il ripristino è un'operazione a lunga esecuzione che potrebbe richiedere 30 minuti o più per essere completata. Se la richiesta viene eseguita correttamente e il processo di ripristino viene avviato, si riceverà un codice di stato risposta `202 Accepted` con un'intestazione `Location`. Effettuare richieste "GET" all'URL nell'intestazione `Location` per conoscere lo stato dell'operazione. Durante l'esecuzione del ripristino si continuerà a ricevere il codice di stato "202 - Accettato". Il codice risposta `200 OK` indicherà il completamento dell'operazione di ripristino.

**Nota**:

-   Il **livello** del servizio in cui si effettua il ripristino **deve corrispondere** al livello del servizio sottoposto a backup da ripristinare.
-   Le **modifiche** apportate alla configurazione del servizio (ad esempio alle API, ai criteri, all'aspetto del portale per sviluppatori) durante l'operazione di ripristino **potrebbero essere sovrascritte**.

  [Autenticazione delle richieste di Gestione risorse di Azure]: http://msdn.microsoft.com/library/dn790557.aspx
  [Backup di un servizio di Gestione API]: #step1
  [Ripristino di un servizio di Gestione API]: #step2
  [API REST di Gestione API di Azure]: http://msdn.microsoft.com/library/azure/dn781421.aspx

<!--HONumber=46--> 

<!--HONumber=46--> 
