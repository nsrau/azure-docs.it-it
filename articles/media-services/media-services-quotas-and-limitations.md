<properties pageTitle="Quote e limitazioni di Servizi multimediali" description="Questo argomento descrive le quote e le limitazioni associate a Servizi multimediali di Microsoft Azure.." services="media-services" documentationCenter="" authors="Juliako" manager="dwrede" />

<tags ms.service="media-services" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="media" ms.date="01/23/2015" ms.author="juliako" />

# Quote e limitazioni

Questo argomento descrive le quote e le limitazioni associate a Servizi multimediali di Microsoft Azure.

## Quote e limitazioni

- Numero massimo di asset consentiti nell'account di Servizi multimediali: 1.000.000. 

- Numero massimo di attività concatenate per processo: 30.

- Numero massimo di asset consentiti in un'attività: 50 asset per attività.
 
- Numero massimo di asset per processo: 100.
 
- Questo numero include i processi in coda, terminati, attivi e annullati. Non include invece i processi eliminati.
 
- Il numero massimo di processi nell'account non deve essere superiore a 50.000.
 
- È possibile eliminare i processi completati da molto tempo usando **IJob.Delete** o la richiesta HTTP **DELETE**. Per altre informazioni, vedere le pagine relative al [limite di record dei processi per Azure Media Encoder](http://blogs.msdn.com/b/randomnumber/archive/2014/05/05/job-record-limit-for-windows-azure-media-encoder.aspx) e alla [gestione di asset](https://msdn.microsoft.com/library/azure/dn642436.aspx). 
 
- L'elenco di entità Job restituite può includere al massimo 1.000 voci per richiesta. Per tenere traccia di tutte le entità Job inviate, si possono usare le opzioni top/skip descritte nell'argomento relativo alle [opzioni query di sistema OData](http://msdn.microsoft.com/library/gg309461.aspx).  


- Non è possibile avere più di cinque localizzatori univoci associati contemporaneamente a un determinato asset. 
	
	**Nota**
	I localizzatori non sono progettati per gestire il controllo dell'accesso per utente. Per assegnare a singoli utenti diritti di accesso diversi, è possibile usare soluzioni DRM (Digital Rights Management).

- Non è possibile includere più di 25 account di Servizi multimediali in una singola sottoscrizione.

- Il meccanismo di limitazione delle richieste di Servizi multimediali limita l'uso delle risorse per le applicazioni che inviano un numero elevato di richieste al servizio. Il servizio può restituire il codice di stato HTTP di servizio non disponibile (503). Per altre informazioni, vedere la descrizione dell'errore 503 nell'argomento [Codici di errore di Servizi multimediali di Azure](http://msdn.microsoft.com/library/azure/dn168949.aspx).

- Per impostazione predefinita, è possibile aggiungere fino a cinque canali live all'account di Servizi multimediali. 

	È anche possibile avviare un massimo di cinque canali contemporaneamente. Per richiedere un limite maggiore, vedere la seguente sezione *How to request a higher limit for updatable quotas*.

- Per impostazione predefinita, è possibile aggiungere fino a 50 programmi (con stato arrestato) a un singolo canale. 

	Possono essere contemporaneamente presenti fino a tre programmi con stato in esecuzione. Per richiedere un limite maggiore, vedere la seguente sezione *How to request a higher limit for updatable quotas*.

- Per impostazione predefinita, ogni account di Servizi multimediali può includere fino a due endpoint di streaming (origini). 

	Possono essere contemporaneamente presenti fino a due endpoint di streaming con stato in esecuzione.

	È possibile scalare ogni endpoint di streaming fino a 10 unità di streaming. Per richiedere un limite maggiore, vedere la seguente sezione *How to request a higher limit for updatable quotas*.


- Per impostazione predefinita, ogni account di Servizi multimediali può essere scalato verticalmente fino a 25 unità di codifica. Per altre informazioni, vedere Come scalare un servizio multimediale. Per richiedere un limite maggiore, vedere Come richiedere un limite maggiore per le quote aggiornabili.
	
	**Importante**
	Non creare altri account di Servizi multimediali per aumentare il limite, ma inviare invece un ticket di supporto.

## Come richiedere un limite maggiore per le quote aggiornabili

### Quote aggiornabili

È possibile richiedere l'aggiornamento dei limiti per le seguenti quote aprendo un ticket di supporto.
- Unità di codifica

- Canali live (con stato arrestato e in esecuzione)
 
- Endpoint di streaming (con stato arrestato e in esecuzione)
 
- Unità di streaming

### Aprire un ticket di supporto

Per aprire un ticket di supporto, seguire questa procedura:

1. Fare clic su [Ottieni supporto](https://manage.windowsazure.com/?getsupport=true). Se non si è ancora connessi, verrà richiesto di immettere le credenziali.

1. Selezionare la propria sottoscrizione.
 
1. Come tipo di supporto, selezionare "Tecnico".
 
1. Fare clic su "Crea ticket". 
 
1. Selezionare "Servizi multimediali di Azure" dall'elenco dei prodotti visualizzato nella pagina successiva.
 
1. Selezionare un tipo di problema appropriato per la situazione specifica.
 
1. Fare clic su Continua.
 
1. Attenersi alle istruzioni visualizzate nella pagina successiva, quindi specificare il numero di unità riservate di codifica o di streaming necessarie. 
 
1. Fare clic su Invia per aprire il ticket.
 


<!--HONumber=52--> 