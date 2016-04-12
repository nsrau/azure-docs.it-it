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
 ms.date="03/09/2016"
 ms.author="krisragh"/>

# Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione

## Quote, limiti, impostazioni predefinite e limiti dell'utilità di pianificazione

[AZURE.INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## L'intestazione x-ms-request-id

Ogni richiesta effettuata per il servizio dell’Utilità di pianificazione restituisce un'intestazione di risposta denominata **x-ms-request-id**. Questa intestazione contiene un valore opaco che identifica in modo univoco la richiesta.

Se una richiesta fallisce sistematicamente e si è verificato che la richiesta è formulata in modo appropriato, si può usare questo valore per riportare l'errore a Microsoft. Nel report includere il valore di x-ms-request-id, l'ora approssimativa in cui è stata effettuata la richiesta, l'identificativo della sottoscrizione, la raccolta processi e/o il processo e il tipo di operazione tentata con la richiesta.

## Stato dell'Utilità di pianificazione e codici di errore:

Oltre ai codici di stato HTTP standard, l'API REST dell'Utilità di pianificazione di Azure restituisce codici di errore estesi e messaggi di errore. I codici estesi non sostituiscono i codici di stato HTTP standard, ma forniscono informazioni aggiuntive e utili a decidere ulteriori operazioni, che possono essere utilizzate in combinazione con i codici di stato HTTP standard.

Ad esempio, un errore HTTP 404 può verificarsi per diversi motivi, per cui che le informazioni aggiuntive nel messaggio esteso possono contribuire alla risoluzione del problema. Per altre informazioni sui codici HTTP standard restituiti dall'API REST, vedere [Stato di gestione del servizio e codici di errore](https://msdn.microsoft.com/library/windowsazure/ee460801.aspx). Le operazioni con l'API REST per l'API di gestione dei servizi restituiscono codici di stato HTTP standard, come specificato nelle [Definizioni dei codici di stato HTTP/1.1](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Nella tabella seguente vengono descritti gli errori più comuni che possono essere restituiti dal servizio.

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

Le operazioni API possono inoltre restituire informazioni aggiuntive sugli errori definite dal servizio di gestione. Queste informazioni aggiuntive sugli errori vengono restituite nel corpo della risposta.

## Vedere anche


 [Che cos'è l'Utilità di pianificazione?](scheduler-intro.md)

 [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione di Azure](scheduler-concepts-terms.md)

 [Introduzione all'uso dell'Utilità di pianificazione di Azure nel portale di Azure](scheduler-get-started-portal.md)

 [Piani e fatturazione nell'utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione di Azure](https://msdn.microsoft.com/library/mt629143)

 [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione di Azure](scheduler-powershell-reference.md)

 [Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione di Azure](scheduler-high-availability-reliability.md)

 [Autenticazione in uscita dell'Utilità di pianificazione di Azure](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0323_2016-->