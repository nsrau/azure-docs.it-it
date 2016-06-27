<properties
	pageTitle="API di Azure Mobile Engagement SDK per Web | Microsoft Azure"
	description="Ultimi aggiornamenti e procedure relativi all'SDK per Web per Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="web"
	ms.devlang="js"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="piyushjo" />

# Come usare l'API di Engagement in un'applicazione Web

Questo documento costituisce un'integrazione al documento [Come integrare Engagement in un'applicazione Web](mobile-engagement-web-integrate-engagement.md). Fornisce informazioni approfondite su come usare l'API di Engagement per segnalare le statistiche dell'applicazione.

L'API di Engagement viene fornita dall'oggetto `engagement.agent`. `engagement` è l'alias predefinito di Engagement SDK che può essere ridefinito dalla configurazione dell'SDK.

## Concetti relativi a Mobile Engagement

Le parti seguenti approfondiscono le informazioni contenute nell'articolo [Concetti relativi ad Azure Mobile Engagement](mobile-engagement-concepts.md) per la piattaforma Web.

### `Session` e `Activity`

Se l'utente resta inattivo per più di due secondi tra due *attività*, la sequenza di *attività* viene divisa in due *sessioni* distinte. Questi pochi secondi vengono chiamati *timeout della sessione*.

Se l'applicazione Web non dichiara la fine delle attività utente da sola (chiamando la funzione `engagement.agent.endActivity`), il server di Engagement terminerà automaticamente la sessione utente entro 3 minuti dalla chiusura della pagina dell'applicazione. Questo comportamento viene definito *timeout della sessione* del server.

### `Crash`

Non esiste alcun report automatizzato di eccezioni JavaScript non rilevate. Tuttavia, è possibile segnalare manualmente arresti anomali del sistema tramite la funzione `sendCrash` (vedere di seguito).

## Segnalazione di attività

### L'utente inizia una nuova attività

	engagement.agent.startActivity("MyUserActivity");

È necessario chiamare `startActivity()` ogni volta che l'attività dell'utente cambia. La prima chiamata a questa funzione avvia una nuova sessione utente.

### L'utente termina l'attività corrente

	engagement.agent.endActivity();

È necessario chiamare `endActivity()` almeno una volta quando l'utente termina la sua ultima attività. In questo modo, si indica all'SDK di Engagement che l'utente è attualmente inattivo e che la sessione utente deve essere chiusa allo scadere del timeout. Se si chiama `startActivity()` prima dello scadere del timeout, la sessione viene semplicemente ripresa.

Spesso è difficile o non è possibile rilevare la fine delle attività utente all'interno di ambienti Web (chiamata non affidabile quando la finestra di navigazione è chiusa). Per questo motivo il server di Engagement termina automaticamente una sessione utente entro 3 minuti dalla chiusura della pagina dell'applicazione.

## Segnalazione di eventi

### Eventi di sessione

Gli eventi di sessione vengono in genere usati per segnalare le azioni eseguite da un utente durante la sua sessione.

**Esempio senza dati aggiuntivi:**

	loginButton.onclick = function() {
	  engagement.agent.sendSessionEvent('login');
	  // [...]
	}

**Esempio con dati aggiuntivi:**

	loginButton.onclick = function() {
	  engagement.agent.sendSessionEvent('login', {user: 'alice'});
	  // [...]
	}

### Eventi autonomi

Diversamente dagli eventi di sessione, gli eventi autonomi possono verificarsi all'esterno del contesto di una sessione.

A tale scopo, usare ``engagement.agent.sendEvent`` invece di ``engagement.agent.sendSessionEvent``.

## Segnalazione di errori

### Errori di sessione

Gli errori di sessione vengono in genere usati per segnalare gli errori che hanno impatto sull'utente durante la sua sessione.

**Esempio senza dati aggiuntivi:**

	var validateForm = function() {
	  // [...]
	  if (password.length < 6) {
	    engagement.agent.sendSessionError('password_too_short');
	  }
	  // [...]
	}

**Esempio con dati aggiuntivi:**

	var validateForm = function() {
	  // [...]
	  if (password.length < 6) {
	    engagement.agent.sendSessionError('password_too_short', {length: 4});
	  }
	  // [...]
	}

### Errori autonomi

Diversamente dagli errori di sessione, gli errori autonomi possono verificarsi all'esterno del contesto di una sessione.

A tale scopo, usare `engagement.agent.sendError` invece di `engagement.agent.sendSessionError`.

## Segnalazione di processi

### Esempio

Si supponga di voler monitorare una richiesta Ajax:
			
	// [...]
	xhr.onreadystatechange = function() {
	  if (xhr.readyState == 4) {
	  // [...]
	    engagement.agent.endJob('publish');
	  }
	}
	engagement.agent.startJob('publish');
	xhr.send();
	// [...]

### Segnalazione di errori durante un processo

Gli errori possono essere correlati a un processo in esecuzione invece che alla sessione utente corrente.

**Esempio:**

Si supponga di voler segnalare un errore in caso di esito negativo di una richiesta Ajax:

	// [...]
	xhr.onreadystatechange = function() {
	  if (xhr.readyState == 4) {
	    // [...]
	    if (xhr.status == 0 || xhr.status >= 400) {
	      engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
	    }
	    engagement.agent.endJob('publish');
	  }
	}
	engagement.agent.startJob('publish');
	xhr.send();
	// [...]

### Segnalazione di eventi durante un processo

Gli eventi possono essere correlati a un processo in esecuzione invece che alla sessione utente corrente grazie alla funzione `engagement.agent.sendJobEvent`.

Questa funzione opera esattamente come la funzione `engagement.agent.sendJobError`.

### Segnalazione di arresti anomali

la funzione `sendCrash` è usata per segnalare manualmente arresti anomali del sistema.

l'argomento `crashid` è una stringa usata per identificare il tipo di arresto anomalo del sistema. L'argomento `crash` è in genere l'analisi dello stack dell'arresto anomalo sotto forma di stringa.

	engagement.agent.sendCrash(crashid, crash);

## Parametri aggiuntivi

Dati arbitrari possono essere collegati a un evento, un errore, un'attività o un processo.

Questi dati possono essere qualsiasi oggetto JSON (non una matrice o tipi primitivi).

**Esempio**

	var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
	engagement.agent.sendEvent("video_clicked", extras);

### Limiti

#### Chiavi

Ogni chiave dell'oggetto deve corrispondere all'espressione regolare seguente:

	^[a-zA-Z][a-zA-Z_0-9]*

Questo significa che le chiavi devono iniziare con almeno una lettera, seguita da lettere, cifre o caratteri di sottolineatura (\\_).

#### Valori

I valori sono limitati a tipi string, number e boolean.

#### Dimensione

I dati aggiuntivi sono limitati a **1024** caratteri per chiamata, dopo essere stati codificati in JSON dall'SDK.

## Segnalazione di informazioni sull'applicazione

È possibile segnalare manualmente le informazioni di traccia o qualsiasi altra informazione specifica dell'applicazione mediante la funzione `sendAppInfo()`.

Queste informazioni possono essere inviate in modo incrementale: viene mantenuto solo l'ultimo valore per una determinata chiave per ogni dispositivo specifico.

Come per i dati aggiuntivi degli eventi, l'oggetto JSON può essere usato per astrarre le informazioni sull'applicazione. Tenere presente che le matrici o gli oggetti secondari vengono trattati come stringhe flat (usando la serializzazione JSON).

### Esempio

Ecco un esempio di codice per inviare il sesso e la data di nascita dell'utente:

	var appInfos = {"birthdate":"1983-12-07","gender":"female"};
	engagement.agent.sendAppInfo(appInfos);

### Limiti

#### Chiavi

Ogni chiave dell'oggetto deve corrispondere all'espressione regolare seguente:

	^[a-zA-Z][a-zA-Z_0-9]*

Questo significa che le chiavi devono iniziare con almeno una lettera, seguita da lettere, cifre o caratteri di sottolineatura (\\_).

#### Dimensione

Le informazioni sull'applicazione sono limitate a **1024** caratteri per chiamata, dopo essere state codificate in JSON dall'SDK.

Nell'esempio precedente il codice JSON inviato al server è lungo 44 caratteri:

	{"birthdate":"1983-12-07","gender":"female"}
 

<!---HONumber=AcomDC_0615_2016-->