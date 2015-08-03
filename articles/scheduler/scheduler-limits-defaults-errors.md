<properties 
 pageTitle="Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione" 
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
 ms.date="04/22/2015" 
 ms.author="krisragh"/>
 
# Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione

## Quote, limiti, impostazioni predefinite e limiti dell'utilità di pianificazione

Nella tabella seguente viene descritto ciascuno dei principali quote, limiti, impostazioni predefinite e limitazioni nell'utilità di pianificazione di Azure.

|Risorsa|Descrizione del limite|
|---|---|
|**Dimensioni del processo**|La dimensione massima del processo è di 16K. Se un'operazione PUT o PATCH determina un processo di dimensioni maggiori rispetto a questi limiti, viene restituito un codice di stato 400 di richiesta non valida.|
|**Dimensioni dell’URL della richiesta**|La dimensione massima dell’URL della richiesta è 2.048 caratteri.|
|**Dimensioni aggregate dell'intestazione**|Le dimensioni aggregate massime dell'intestazione sono di 4.096 caratteri.|
|**Numero di intestazioni**|Il numero massimo di intestazioni è di 50 intestazioni.|
|**Dimensioni del corpo**|La dimensione massima del corpo è di 8.192 caratteri.|
|**Intervallo di ricorrenza**|L’intervallo massimo delle ricorrenze è di 18 mesi.|
|**Tempo dall’ora di inizio**|Il massimo "tempo dall'ora di inizio" è di 18 mesi.|
|**Cronologia processi**|Le dimensioni massime dei corpi delle risposte archiviati nella cronologia processi è di 2.048 byte.|
|**Frequenza**|Il valore predefinito per la frequenza massima è di 1 ora per le raccolte di processi gratuite e di 1 minuto nelle raccolte di processi standard. La frequenza massima è configurabile in una raccolta di processi per essere inferiore a quella massima. Tutti i processi nella raccolta sono limitati al valore impostato nella raccolta stessa. Se si tenta di creare un processo con una frequenza maggiore rispetto alla frequenza massima nella raccolta la richiesta avrà esito negativo con codice di stato 409 Conflitto.|
|**Processi**|Il valore predefinito per la quota massima di processi è di 5 processi per le raccolte di processi gratuite e di 50 processi nelle raccolte di processi standard. Il numero massimo di processi è configurabile nelle raccolte di processi. Tutti i processi nella raccolta sono limitati al valore impostato nella raccolta stessa. Se si tenta di creare più processi rispetto alla quota massima, la richiesta ha esito negativo e restituisce un codice di stato 409 conflitto.|
|**Periodo di memorizzazione della cronologia dei processi**|La cronologia dei processi viene mantenuta fino a 2 mesi.|
|**Memorizzazione di processi completati e con errori**|I processi completati e con errori vengono riportati per 60 giorni.|
|**Timeout**|Esiste un timeout delle richieste statico (non configurabile) di 30 secondi per le azioni HTTP. Per le operazioni dai tempi di esecuzione più lunghi, seguire i protocolli HTTP asincroni, ad esempio, restituendo immediatamente un 202, ma continuando a lavorare in background.|

## L'intestazione x-ms-request-id

Ogni richiesta effettuata per il servizio dell’Utilità di pianificazione restituisce un'intestazione di risposta denominata **x-ms-request-id**. Questa intestazione contiene un valore opaco che identifica in modo univoco la richiesta.

Se una richiesta fallisce sistematicamente e si è verificato che la richiesta è formulata in modo appropriato, si può usare questo valore per riportare l'errore a Microsoft. Nel report, includere il valore di x-ms-request-id, l'ora approssimativa in cui è stata effettuata la richiesta, l'identificativo della sottoscrizione, il servizio cloud, la raccolta di processi e/o il processo e il tipo di operazione tentata con la richiesta.

## Stato dell'Utilità di pianificazione e codici di errore:

Oltre ai codici di stato HTTP standard, l'API REST dell'Utilità di pianificazione di Azure restituisce codici di errore estesi e messaggi di errore. I codici estesi non sostituiscono i codici di stato HTTP standard, ma forniscono informazioni aggiuntive e utili a decidere ulteriori operazioni, che possono essere utilizzate in combinazione con i codici di stato HTTP standard.

Ad esempio, un errore HTTP 404 può verificarsi per diversi motivi, per cui che le informazioni aggiuntive nel messaggio esteso possono contribuire alla risoluzione del problema. Per ulteriori informazioni sui codici HTTP standard restituiti dall'API REST, vedere [Status di gestione del servizio e codici di errore](https://msdn.microsoft.com/library/windowsazure/ee460801.aspx). Le operazioni con l’API REST per l'API di gestione dei servizi restituiscono codici di stato HTTP standard, come definiti nelle [Definizioni dei codici di stato HTTP/1.1](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Nella tabella seguente vengono descritti gli errori più comuni che possono essere restituiti dal servizio.

|Codice di errore|Stato codice HTTP|Messaggio utente|
|----|----|----|
|HeaderVersioneMancanteOErrato|Richiesta non valida (400)|L'intestazione del controllo delle versioni non è specificato o non è stato specificato correttamente.|
|RichiestaXmlNonValida|Richiesta non valida (400)|L’XML del corpo della richiesta non era valido o non è stato specificato correttamente.|
|ParametroQueryMancanteONonValido|Richiesta non valida (400)|Non è stato specificato un parametro di query obbligatorio per questa richiesta o è stato specificato in maniera non corretta.|
|VerboHttpNonValido|Richiesta non valida (400)|Il verbo HTTP specificato non è stato riconosciuto dal server o non è valido per questa risorsa.|
|AutenticazioneFallita|Accesso negato (403)|Il server non è riuscito ad autenticare la richiesta. Verificare che il certificato sia valido e che sia associato a questa sottoscrizione.|
|RisorsaNonTrovata|Non trovato (404)|La risorsa specificata non esiste.|
|ErroreInterno|Errore interno del server (500)|si è verificato un errore interno del server. Si prega di ripetere la richiesta.|
|TimeOutDellOperazione|Errore interno del server (500)|L'operazione non è stata completata nel tempo consentito.|
|ServerOccupato|Servizio non disponibile (503)|Il server (o un componente interno) non è attualmente disponibile per ricevere le richieste. Si prega di ripetere la richiesta.|
|SottoscrizioneDisabilitata|Accesso negato (403)|La sottoscrizione è in stato disabilitato.|
|RichiestaNonValida|Richiesta non valida (400)|Un parametro non era corretto.|
|ErroreDiConflitto|Conflitto (409)|Si è verificato un conflitto che ha impedito il completamento dell'operazione.|
|ReindirizzamentoTemporaneo|Reindirizzamento temporaneo (307)|L'oggetto richiesto non è disponibile. Un URI temporaneo per la nuova posizione dell'oggetto può essere ottenuto dal campo Percorso nella risposta. È possibile ripetere la richiesta originale con il nuovo URI.|

Le operazioni API possono inoltre restituire informazioni aggiuntive sugli errori definite dal servizio di gestione. Queste informazioni aggiuntive sugli errori vengono restituite nel corpo della risposta. Il corpo della risposta di errore segue il formato di base riportato di seguito.

		<?xml version="1.0" encoding="utf-8"?>  
		<Error>  
			<Code>string-code</Code>  
			<Message>detailed-error-message</Message>  
		</Error>  

## Vedere anche

 [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione](scheduler-concepts-terms.md)
 
 [Introduzione all'uso dell'Utilità di pianificazione nel portale di gestione](scheduler-get-started-portal.md)
 
 [Piani e fatturazione nell'Utilità di pianificazione di Azure](scheduler-plans-billing.md)
 
 [Come creare pianificazioni complesse e operazioni ricorrenti avanzate con l'Utilità di pianificazione di Azure](scheduler-advanced-complexity.md)
 
 [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione](https://msdn.microsoft.com/library/dn528946)
 
 [Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione](scheduler-high-availability-reliability.md)
 
 [Autenticazione in uscita dell'Utilità di pianificazione](scheduler-outbound-authentication.md)

  

<!---HONumber=July15_HO4-->