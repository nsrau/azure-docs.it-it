<properties
	pageTitle="Uso della rete CDN di Azure con CORS | Microsoft Azure"
	description="Informazioni su come usare la rete per la distribuzione di contenuti (rete CDN) di Azure con CORS (Cross-Origin Resource Sharing)."
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="casoper"/>
    
# Uso della rete CDN di Azure con CORS     

## Informazioni su CORS

CORS (Cross Origin Resource Sharing) è una funzionalità HTTP che consente a un'applicazione Web in esecuzione in un dominio di accedere alle risorse in un altro dominio. Per ridurre il rischio di attacchi tramite script da altri siti, tutti i Web browser moderni implementano una restrizione di sicurezza nota come [regola della stessa origine](http://www.w3.org/Security/wiki/Same_Origin_Policy). Questo impedisce a una pagina Web di chiamare le API in un dominio diverso. CORS offre una modalità sicura per consentire a un dominio (quello di origine) di chiamare le API in un altro dominio.
 
## Funzionamento
1.	Il browser invia la richiesta OPTIONS con un'intestazione HTTP **Origin**. Il valore di questa intestazione è il dominio che ha gestito la pagina padre. Quando una pagina tenta di accedere da https://www.contoso.com ai dati di un utente nel dominio fabrikam.com, è necessario inviare a tale sito l'intestazione di richiesta seguente:
    
    `Origin: https://www.contoso.com`
 
2.	Il server può rispondere con gli elementi seguenti:
    - Un'intestazione **Access-Control-Allow-Origin** presente nella risposta, per indicare i siti di origine consentiti. ad esempio:
        
        `Access-Control-Allow-Origin: https://www.contoso.com`
        
    - Una pagina di errore se il server non consente la richiesta multiorigine
    - Un'intestazione **Access-Control-Allow-Origin** con un carattere jolly che consente tutti i domini:
        
        `Access-Control-Allow-Origin: *`
 
Per quanto riguarda le richieste HTTP complesse, esiste una richiesta "preliminare" da eseguire in precedenza per verificare la disponibilità dell'autorizzazione necessaria prima dell'invio dell'intera richiesta.
 
## Scenari con caratteri jolly o singola origine

La condivisione CORS sulla rete CDN di Azure funzionerà automaticamente senza operazioni di configurazione aggiuntive quando l'intestazione **Access-Control-Allow-Origin** è impostata sul carattere jolly asterisco (*) o su una singola origine. La rete CDN memorizzerà nella cache la prima risposta e le richieste successive useranno la stessa intestazione.
 
Se sono state inviate richieste alla rete CDN prima che la condivisione CORS venisse impostata nell'origine, sarà necessario eliminare il contenuto sull'endpoint e ricaricarlo con l'intestazione **Access-Control-Allow-Origin**.
 
## Scenari con più origini

Se si desidera autorizzare per CORS uno specifico elenco di origini, le operazioni da eseguire sono più complesse. Il problema si verifica quando la rete CDN memorizza nella cache l'intestazione **Access-Control-Allow-Origin** per la prima origine CORS. Quando un'origine CORS differente effettua una richiesta successiva, la rete CDN gestisce l'intestazione **Access-Control-Allow-Origin**, che però non corrisponde. Esistono diversi modi per risolvere il problema.
 
### Rete CDN Premium di Azure fornita da Verizon

Il modo migliore per abilitare questa rete consiste nell'usare la **rete CDN Premium di Azure fornita da Verizon**, in cui sono disponibili alcune funzionalità avanzate.
 
È necessario [creare una regola](cdn-rules-engine.md) per verificare l'intestazione **Origin** nella richiesta. Se l'origine è valida, la regola imposterà l'intestazione **Access-Control-Allow-Origin** sull'origine indicata nella richiesta. Se l'origine specificata nell'intestazione **Origin** non è consentita, la regola deve omettere l'intestazione **Access-Control-Allow-Origin**, azione che determinerà il rifiuto della richiesta da parte del browser.
 
Per eseguire questa operazione è possibile procedere in due modi usando il motore regole: In entrambi i casi l'intestazione **Access-Control-Allow-Origin** proveniente dal server di origine del file viene completamente ignorata e il motore regole della rete CDN gestisce per intero le origini CORS consentite.

#### Un'espressione regolare con tutte le origini valide
 
In questo caso verrà creata un'espressione regolare che include tutte le origini che si desidera consentire:

	https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
 
> [AZURE.TIP] La **rete CDN Premium di Azure fornita da Verizon** usa la libreria [PCRE (Perl Compatible Regular Expressions)](http://pcre.org/) come motore per le espressioni regolari. Per convalidare le espressioni regolari, è possibile usare uno strumento come [Regular Expressions 101](https://regex101.com/). Si noti che il carattere "/" è valido nelle espressioni regolari e non deve essere preceduto da un carattere di escape. Tuttavia, l'inserimento di un carattere di escape prima di "/" è considerato una procedura consigliata ed è previsto da alcuni strumenti di convalida delle espressioni regolari.

Se l'espressione regolare corrisponde, la regola specificata sostituirà l'intestazione **Access-Control-Allow-Origin** (se presente) proveniente dall'origine con l'origine che ha inviato la richiesta. È inoltre possibile aggiungere altre intestazioni CORS, ad esempio **Access-Control-Allow-Methods**.

![Esempio di regole con espressione regolare](./media/cdn-cors/cdn-cors-regex.png)
 
#### Regola intestazione richiesta per ciascuna origine.

Anziché usare espressioni regolari, è possibile creare una regola separata per ciascuna origine che si desidera consentire usando la [condizione di corrispondenza](cdn-rules-engine-details.md#match-conditions) **Request Header Wildcard** (Carattere jolly intestazione richiesta). Come per il metodo delle espressioni regolari, il motore regole imposta le intestazioni CORS.
  
![Esempio di regole senza espressione regolare](./media/cdn-cors/cdn-cors-no-regex.png)

> [AZURE.TIP] Nell'esempio precedente l'uso del carattere jolly asterisco (*) indica al motore regole di mettere in corrispondenza sia HTTP che HTTPS.
 
### Rete CDN Standard di Azure

Nei profili della rete CDN Standard di Azure, l'unico meccanismo per consentire più origini senza l'uso dell'origine con caratteri jolly consiste nella [memorizzazione della stringa di query nella cache](cdn-query-string.md). È necessario abilitare l'impostazione della stringa di query per l'endpoint della rete CDN e usare quindi una stringa di query univoca per le richieste provenienti da ciascun dominio consentito. Con questa operazione la rete CDN memorizzerà nella cache un oggetto separato per ciascuna stringa di query univoca. Questo approccio tuttavia non rappresenta la soluzione ideale, poiché avrà come risultato la memorizzazione nella cache di più copie dello stesso file nella rete CDN.

<!---HONumber=AcomDC_0803_2016-->