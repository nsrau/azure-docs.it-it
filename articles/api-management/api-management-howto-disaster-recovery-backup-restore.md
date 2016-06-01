<properties 
	pageTitle="Come implementare il ripristino di emergenza usando il backup e il ripristino dei servizi in Gestione API di Azure | Microsoft Azure" 
	description="Informazioni su come usare il backup e il ripristino per eseguire il ripristino di emergenza in Gestione API di Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/16/2016" 
	ms.author="sdanie"/>

# Come implementare il ripristino di emergenza usando il backup e il ripristino dei servizi in Gestione API di Azure

Scegliendo di pubblicare e gestire le API tramite Gestione API di Azure è possibile sfruttare molte funzionalità di tolleranza di errore e di infrastruttura che sarebbe altrimenti necessario progettare, implementare e gestire. La piattaforma di Azure permette di mitigare una vasta gamma di potenziali errori a un costo nettamente inferiore.

Per risolvere i problemi di disponibilità che colpiscono l'area in cui è ospitato il servizio di Gestione API, è necessario essere pronti a ripristinare il servizio in un'area diversa in qualsiasi momento. In base ai propri obiettivi in termini di disponibilità e tempi di ripristino, è consigliabile riservare un servizio di backup in una o più aree e provare a mantenere sincronizzati la configurazione e il contenuto con il servizio attivo. La funzionalità di backup e ripristino dei servizi fornisce il blocco predefinito necessario per implementare la propria strategia di ripristino di emergenza.

Questa guida descrive come autenticare le richieste di Gestione risorse di Azure e come eseguire il backup e ripristinare le istanze del servizio Gestione API.

>[AZURE.NOTE] Il processo di backup e ripristino di un'istanza del servizio Gestione API per il ripristino di emergenza può essere usato anche per la replica delle istanze del servizio Gestione API per scenari quali la gestione temporanea.
>
>Si noti che ogni backup scade dopo 7 giorni. Se si tenta di ripristinare un backup dopo la scadenza del periodo di 7 giorni, il ripristino avrà esito negativo e verrà visualizzato il messaggio `Cannot restore: backup expired`.

## Autenticazione delle richieste di Gestione risorse di Azure

>[AZURE.IMPORTANT] L'API REST per il backup e ripristino usa Gestione risorse di Azure e include un meccanismo di autenticazione diverso rispetto alle API REST per la gestione delle entità di Gestione API. I passaggi descritti in questa sezione descrivono come autenticare le richieste di Gestione risorse di Azure. Per altre informazioni, vedere [Autenticazione delle richieste di Gestione risorse di Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx).

Tutte le attività che è possibile eseguire sulle risorse tramite Gestione risorse di Azure devono essere autenticate con Azure Active Directory usando la procedura seguente.

-	Aggiungere un'applicazione al tenant di Azure Active Directory.
-	Impostare le autorizzazioni per l'applicazione aggiunta.
-	Ottenere il token per autenticare le richieste a Gestione risorse di Azure.

Il primo passaggio consiste nel creare un'applicazione Azure Active Directory. Accedere al [portale di Azure classico](http://manage.windowsazure.com/) usando la sottoscrizione che include l'istanza del servizio Gestione API e passare alla scheda **Applicazioni** per la directory predefinita di Azure Active Directory.

>[AZURE.NOTE] Se la directory predefinita di Azure Active Directory non è visibile nel proprio account, contattare l'amministratore della sottoscrizione di Azure perché conceda le autorizzazioni necessarie per l'account. Per informazioni su come trovare la directory predefinita, vedere l'articolo sulle modalità per [trovare la directory predefinita](../virtual-machines/resource-group-create-work-id-from-persona.md/#locate-your-default-directory-in-the-azure-portal).

![Creare un'applicazione Azure Active Directory][api-management-add-aad-application]

Fare clic su **Aggiungi**, **Aggiungi un'applicazione che l'organizzazione sta sviluppando**, quindi scegliere **Applicazione client nativa**. Immettere un nome descrittivo e fare clic sulla freccia Avanti. Immettere un URL di segnaposto, ad esempio `http://resources` per **URI di reindirizzamento**, che è un campo obbligatorio, ma il valore non viene usato in seguito. Selezionare la casella di controllo per salvare l'applicazione.

Dopo il salvataggio dell'applicazione, fare clic su **Configura**, scorrere verso il basso fino alla sezione **Autorizzazioni per altre applicazioni** e fare clic su **Aggiungi applicazione**.

![Aggiungere autorizzazioni][api-management-aad-permissions-add]

Selezionare **API** **di gestione del servizio Microsoft Azure** e selezionare la casella di controllo per aggiungere l'applicazione.

![Aggiungere autorizzazioni][api-management-aad-permissions]

Fare clic su **Autorizzazioni delegate** accanto all'applicazione di **API** **di gestione del servizio Microsoft Azure** appena aggiunta, selezionare la casella per **Accesso a Gestione del servizio Azure (anteprima)**, e fare clic su **Salva**.

![Aggiungere autorizzazioni][api-management-aad-delegated-permissions]

Prima di richiamare le API che generano il backup e ripristino, è necessario ottenere un token. L'esempio seguente usa il pacchetto NuGet [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) per recuperare il token.

	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System;

	namespace GetTokenResourceManagerRequests
	{
        class Program
	    {
	        static void Main(string[] args)
	        {
	            var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
	            var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

	            if (result == null) {
	                throw new InvalidOperationException("Failed to obtain the JWT token");
	            }

	            Console.WriteLine(result.AccessToken);

	            Console.ReadLine();
	        }
    	}
	}

Sostituire `{tentand id}`, `{application id}` e `{redirect uri}` usando le istruzioni seguenti.

Sostituire `{tenant id}` con l'ID tenant dell'applicazione Azure Active Directory appena creata. È possibile accedere all'ID facendo clic su **Visualizza endpoint**.

![Endpoint][api-management-aad-default-directory]

![Endpoint][api-management-endpoint]

Sostituire `{application id}` e `{redirect uri}` usando l'**ID client** e l'URL dalla sezione **URI di reindirizzamento** dalla scheda **Configura** dell'applicazione Azure Active Directory.

![Risorse][api-management-aad-resources]

Dopo avere specificato i valori, l'esempio di codice dovrebbe restituire un token simile all'esempio seguente.

![Token][api-management-arm-token]

Prima di chiamare le operazioni di backup e ripristino descritte nelle sezioni seguenti, impostare l'intestazione della richiesta di autorizzazione per la chiamata REST.

	request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);

## <a name="step1"> </a>Backup di un servizio di Gestione API
Per eseguire il backup di un servizio di gestione API, emettere la seguente richiesta HTTP:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

dove:

* `subscriptionId`: ID della sottoscrizione contenente il servizio di Gestione API di cui si sta tentando di eseguire il backup.
* `resourceGroupName`: una stringa nel formato "Api-Default-{service-region}", dove `service-region` identifica l'area di Azure in cui è ospitato il servizio di Gestione API di cui si sta tentando di eseguire il backup, ad esempio `North-Central-US`.
* `serviceName`: il nome del servizio di Gestione API di cui sta eseguendo il backup specificato quando è stato creato.
* `api-version`: sostituire con `2014-02-14`.

Nel corpo della richiesta, specificare il nome dell'account di archiviazione, la chiave di accesso, il nome del contenitore BLOB e il nome del backup di destinazione di Azure:

	'{  
	    storageAccount : {storage account name for the backup},  
	    accessKey : {access key for the account},  
	    containerName : {backup container name},  
	    backupName : {backup blob name}  
	}'

Impostare il valore dell'intestazione della richiesta `Content-Type` su `application/json`.

Il backup è un'operazione a lunga esecuzione che potrebbe richiedere diversi minuti per essere completata. Se la richiesta viene eseguita correttamente e il processo di backup viene avviato, si riceverà un codice di stato risposta `202 Accepted` con un'intestazione `Location`. Effettuare richieste "GET" all'URL nell'intestazione `Location` per conoscere lo stato dell'operazione. Durante l'esecuzione del backup si continuerà a ricevere il codice di stato "202 - Accettato". Il codice risposta `200 OK` indicherà il completamento dell'operazione di backup.

**Nota**:

- Il **contenitore** specificato nel corpo della richiesta **deve esistere**.
* Mentre il backup è in corso, **non tentare di eseguire alcuna operazione di gestione dei servizi**, ad esempio l'aggiornamento o il downgrade di SKU, la modifica di nomi di dominio e così via. 
* Il ripristino di un **backup è garantito solo per 7 giorni** dal momento della sua creazione. 
* I **dati di utilizzo** usati per creare report analitici **non sono inclusi** nel backup. Usare l'[API REST di Gestione API di Azure][] per recuperare periodicamente i report analitici e custodirli al sicuro.
* La frequenza con cui si eseguono i backup dei servizi influenzerà i propri obiettivi relativi ai punti di ripristino. Per ridurla al minimo, si consiglia di implementare backup regolari e di eseguire backup su richiesta dopo aver apportato modifiche importanti al servizio di Gestione API.
* Le **modifiche** apportate alla configurazione del servizio (ad esempio alle API, ai criteri, all'aspetto del portale per sviluppatori) durante l'esecuzione del processo di backup **potrebbero non essere incluse nel backup e potrebbero quindi andare perse**.

## <a name="step2"> </a>Ripristino di un servizio di Gestione API
Per ripristinare un servizio di Gestione API da un backup creato in precedenza, creare la seguente richiesta HTTP:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

dove:

* `subscriptionId`: ID della sottoscrizione contenente il servizio di Gestione API in cui si sta ripristinando un backup.
* `resourceGroupName`: una stringa nel formato "Api-Default-{service-region}", dove `service-region` identifica l'area di Azure in cui è ospitato il servizio di Gestione API in cui si ripristinando un backup, ad esempio `North-Central-US`.
* `serviceName`: il nome del servizio di Gestione API in cui si sta effettuando il ripristino specificato quando è stato creato.
* `api-version`: sostituire con `2014-02-14`.

Nel corpo della richiesta, specificare la posizione del file di backup, ad esempio il nome dell'account di archiviazione, la chiave di accesso, il nome del contenitore BLOB e il nome del backup di Azure:

	'{  
	    storageAccount : {storage account name for the backup},  
	    accessKey : {access key for the account},  
	    containerName : {backup container name},  
	    backupName : {backup blob name}  
	}'

Impostare il valore dell'intestazione della richiesta `Content-Type` su `application/json`.

Il ripristino è un'operazione a lunga esecuzione che potrebbe richiedere 30 minuti o più per essere completata. Se la richiesta viene eseguita correttamente e il processo di ripristino viene avviato, si riceverà un codice di stato risposta `202 Accepted` con un'intestazione `Location`. Effettuare richieste "GET" all'URL nell'intestazione `Location` per conoscere lo stato dell'operazione. Durante l'esecuzione del ripristino si continuerà a ricevere il codice di stato "202 - Accettato". Il codice risposta `200 OK` indicherà il completamento dell'operazione di ripristino.

>[AZURE.IMPORTANT] Lo **SKU** del servizio in cui si effettua il ripristino **deve corrispondere** allo SKU del servizio sottoposto a backup da ripristinare.
>
>Le **modifiche** apportate alla configurazione del servizio (ad esempio alle API, ai criteri, all'aspetto del portale per sviluppatori) durante l'operazione di ripristino **potrebbero essere sovrascritte**.

## Passaggi successivi
Consultare i blog Microsoft seguenti per due diverse procedure dettagliate del processo di backup e ripristino.

-	[Replicare account di Gestione API di Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/) 
	-	Grazie a Gisela per il contributo fornito per questo articolo.
-	[Gestione API di Azure: Backup e ripristino della configurazione](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
	-	L'approccio descritto da Stuart corrisponde alle linee guida ufficiali, ma è molto interessante.

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[API REST di Gestione API di Azure]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
 

<!---HONumber=AcomDC_0518_2016-->