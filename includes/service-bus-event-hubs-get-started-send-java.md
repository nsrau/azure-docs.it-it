## Inviare messaggi all'hub eventi

La libreria client Java per hub eventi è disponibile per l'utilizzo in progetti Maven dal [Repository centrale Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) ed è possibile farvi riferimento utilizzando la seguente dichiarazione di dipendenza all'interno del file di progetto Maven:

``` XML
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>azure-eventhubs-clients</artifactId> 
    <version>0.6.0</version> 
</dependency>   
 ```
 
Per diversi tipi di ambienti di compilazione, è possibile ottenere in modo esplicito i più recenti file JAR rilasciati dal [Repository centrale Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) o dal [punto di distribuzione rilascio su GitHub](https://github.com/Azure/azure-event-hubs/releases).

Per un autore di eventi semplice, importare il pacchetto *com.microsoft.azure.eventhubs* per le classi di client di hub eventi e il pacchetto *com.microsoft.azure.servicebus* per le classi di utilità, ad esempio eccezioni comuni condivise con il client di messaggistica del bus di servizio di Azure.

Per l'esempio seguente, creare innanzitutto un nuovo progetto Maven per un'applicazione console/shell nell'ambiente di sviluppo Java preferito. La classe verrà chiamata ```Send```.

``` Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
	public static void main(String[] args) 
			throws ServiceBusException, ExecutionException, InterruptedException, IOException
	{
```

Sostituire lo spazio dei nomi e i nomi di Hub eventi con i valori utilizzati durante la creazione dell'Hub eventi. `sasKeyName` e `sasKey` corrispondono al nome e alla chiave della regola di trasmissione creata in precedenza. Con queste informazioni creare una stringa di connessione.

``` Java
	final String namespaceName = "----ServiceBusNamespaceName-----";
	final String eventHubName = "----EventHubName-----";
	final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
	final String sasKey = "---SharedAccessSignatureKey----";
	ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

Creare quindi un evento singolare trasformando una stringa nella rispettiva codifica UTF-8 byte. Viene creata quindi una nuova istanza del client di hub eventi dalla stringa di connessione e il messaggio viene inviato.

``` Java 
				
	byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
	EventData sendEvent = new EventData(payloadBytes);
	
	EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
	ehClient.sendSync(sendEvent);
	}
}

``` 

<!---HONumber=AcomDC_0323_2016-->