<properties
	pageTitle="Procedure consigliate per OAuth 2.0 in Azure AD | Microsoft Azure"
	description="Questo articolo descrive le procedure consigliate per lo sviluppo di applicazioni che usano OAuth 2.0 in Azure Active Directory."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>


# Procedure consigliate per OAuth 2.0 in Azure Active Directory

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

## Usare il parametro di stato

Il parametro `state` è un valore non riutilizzabile generato in modo casuale (in genere un GUID) che il client invia nella richiesta. Nonostante sia facoltativo, questo parametro è altamente consigliato nelle richieste del codice di autorizzazione. La risposta contiene lo stesso valore `state` e prima di usare la risposta l'applicazione deve verificare che i valori dello stato siano identici.

Il parametro `state` consente di rilevare gli attacchi di richiesta intersito falsa contro il client. Per altre informazioni sugli attacchi di questo tipo, vedere la sezione relativa alla [richiesta intersito falsa](https://tools.ietf.org/html/rfc6749#section-10.12) nel framework di autorizzazione di OAuth 2.0.

## Memorizzare nella cache i token di accesso

Per ridurre al minimo le chiamate di rete dall'applicazione client e la latenza associata, l'applicazione client deve memorizzare i token di accesso nella cache per la durata del token specificata nella risposta OAuth 2.0. Per determinare la durata del token, usare i valori dei parametri `expires_in` o `expires_on`.

Se una risorsa API Web restituisce un codice errore `invalid_token`, ciò può indicare che la risorsa ha determinato che il token è scaduto. Se l'ora del client e l'ora della risorsa sono diverse ("sfasamento dell'ora"), la risorsa potrebbe considerare il token scaduto prima che venga cancellato dalla cache del client. In questo caso, cancellare il token dalla cache anche se si trova ancora nella durata calcolata.

## Gestione dei token di aggiornamento

I token di aggiornamento non hanno una durata specificata. La durata dei token di aggiornamento è in genere relativamente lunga. In alcuni casi, tuttavia, i token di aggiornamento scadono, vengono revocati o non hanno privilegi sufficienti per l'azione desiderata. L'applicazione client deve prevedere e gestire correttamente gli errori restituiti dall'endpoint di rilascio del token.

Quando si riceve una risposta con un errore relativo al token di aggiornamento, rimuovere il token di aggiornamento corrente e richiedere un nuovo codice di autorizzazione o un nuovo token di accesso. In particolare, se si usa un token di aggiornamento nel flusso di concessione del codice di autorizzazione e si riceve una risposta con i codici errore `interaction_required` o `invalid_grant`, rimuovere il token di aggiornamento e richiedere un nuovo codice di autorizzazione.

## Verificare il parametro dell'ID risorsa

L'applicazione client deve verificare il valore del parametro `resource_id` nella risposta. In caso contrario, un servizio dannoso potrebbe riuscire a indurre un attacco di tipo elevazione dei privilegi.

 La strategia consigliata per prevenire un attacco consiste nel verificare che resource\_id corrisponda alla base dell'URL dell'API Web a cui si accede. Se si accede a https://service.contoso.com/data, ad esempio, `resource_id` può essere https://service.contoso.com/. L'applicazione client deve rifiutare un `resource_id` che non inizia con l'URL di base a meno che non esista un modo alternativo affidabile per verificare l'ID.

## Linee guida per il rilascio di token e la ripetizione dei tentativi

Azure AD supporta diversi endpoint di rilascio dei token su cui i client possono eseguire query. Per implementare una logica di ripetizione dei tentativi che consenta a tali endpoint di gestire errori di rete o del server imprevisti, rispettare le linee guida riportate di seguito.

Per una richiesta a un endpoint di Azure AD, gli errori in risposta alla ripetizione dei tentativi restituiscono in genere codici errore della serie HTTP 500. In alcuni scenari, il client è un'applicazione o un servizio che invia richieste automatizzate ad Azure AD. In altri scenari, ad esempio con la federazione basata sul Web che usa il protocollo WS-Federation, il client è un Web browser e l'utente finale deve eseguire la ripetizione dei tentativi manualmente.

### Implementare la logica di ripetizione dei tentativi in base alle risposte degli errori della serie HTTP 500

È consigliabile usare la logica di ripetizione dei tentativi quando il Servizio di controllo di accesso di Active Directory (ACS) restituisce errori della serie HTTP 500. Sono inclusi:

- Errore HTTP 500 - Errore interno del server
- Errore HTTP 502 - Gateway non valido
- Errore HTTP 503 - Servizio non disponibile
- Errore HTTP 504 - Timeout del gateway

Nonostante sia possibile elencare in modo esplicito i singoli codici HTTP nella logica di ripetizione dei tentativi, è sufficiente richiamare la logica di ripetizione dei tentativi in caso di restituzione di qualsiasi errore della serie HTTP 500.

In genere, non è consigliabile usare la logica di ripetizione dei tentativi quando vengono restituiti errori della serie HTTP 400. Un codice di risposta di errore HTTP della serie 400 restituito da ACS indica che la richiesta non è valida e deve essere modificata.

La logica di ripetizione dei tentativi dovrebbe essere attivata dai codici errore HTTP, come HTTP 504 (timeout del server esterno) o i codici errore della serie HTTP 500, e non dai codici errore ACS, come ACS90005. I codici errore ACS sono informativi e soggetti a modifiche.

### Usare un timer di backoff per un controllo ottimale del flusso

Quando un client riceve un errore della serie HTTP 500, deve attendere prima di ripetere la richiesta. Per ottenere risultati ottimali, è consigliabile che questo intervallo di tempo aumenti per ogni tentativo successivo. Questo approccio consente di risolvere rapidamente gli errori temporanei ottimizzando al tempo stesso la frequenza delle richieste per gli errori di rete o del server temporanei con tempi di risoluzione più lunghi.

Usare ad esempio un timer di backoff esponenziale in cui il ritardo che precede il tentativo aumenta in modo esponenziale con ogni istanza, ad esempio 1 secondo per il tentativo 1, 2 secondi per il tentativo 2, 4 secondi per il tentativo 3 e così via.

Regolare il numero di tentativi e l'intervallo di tempo tra i singoli tentativi in base ai requisiti dell'esperienza utente. È tuttavia consigliabile eseguire fino a cinque tentativi in un periodo di cinque minuti. Gli errori causati da un timeout presentano tempi di risoluzione più lunghi.

## Passaggi successivi

[Active Directory Authentication Library (ADAL)](active-directory-authentication-libraries.md)

<!---HONumber=AcomDC_0601_2016-->