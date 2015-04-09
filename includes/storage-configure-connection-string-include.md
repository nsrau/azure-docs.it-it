## Configurare una stringa di connessione di archiviazione

Nella libreria del client di archiviazione di Azure per .NET è possibile usare una stringa di connessione di archiviazione per configurare gli endpoint e le credenziali per l'accesso ai servizi di archiviazione. Si consiglia di gestire la stringa di connessione di archiviazione in un file di configurazione, invece di impostarla come hardcoded nell'applicazione. Sono disponibili due opzioni per salvare la stringa di connessione:

- Se l'applicazione viene eseguita in un servizio cloud di Azure, salvare la stringa di connessione con il sistema di configurazione dei servizi di Azure (file `*.csdef` e `*.cscfg`).
- Se l'applicazione viene eseguita in macchine virtuali di Azure o si compilano applicazioni .NET che verranno eseguite all'esterno di Azure, salvare la stringa di connessione con il sistema di configurazione .NET, ad esempio il file `web.config` o `app.config`.

Più avanti in questa guida verrà spiegato come recuperare la stringa di connessione dal codice.

### Configurazione della stringa di connessione da un servizio cloud di Azure

In Servizi cloud di Azure è presente uno speciale meccanismo di configurazione dei servizi che consente di modificare dinamicamente le impostazioni di configurazione dal portale di gestione di Azure senza ridistribuire l'applicazione.

Per configurare la stringa di connessione nella configurazione dei servizi di Azure:

1.  In Esplora soluzioni di Visual Studio, nella cartella **Ruoli** del progetto di distribuzione di Azure, fare clic con il pulsante destro del mouse sul ruolo Web o sul ruolo di lavoro e selezionare **Proprietà**.  
    ![Selezionare le proprietà in un ruolo di servizio Cloud in Visual Studio][connection-string1]

2.  Fare clic sulla scheda **Impostazioni** e quindi sul pulsante **Aggiungi impostazioni**.  
    ![Aggiungere un'impostazione di servizio cloud in Visual Studio][connection-string2]

    Nella griglia delle impostazioni verrà visualizzata una nuova voce **Impostazione1**.

3.  Nell'elenco a discesa **Tipo** della nuova voce **Impostazione1**, scegliere **Stringa di connessione**.  
    ![Impostare il tipo stringa di connessione][connection-string3]

4.  Fare clic sul pulsante **...** a destra della voce **Impostazione1**. Verrà visualizzata la finestra di dialogo **Stringa di connessione all'account di archiviazione**.

5.  Scegliere se impostare come destinazione l'emulatore di archiviazione (archiviazione Windows Azure simulata sulla macchina locale) o un account di archiviazione nel cloud. Il codice riportato in questa guida funziona con entrambe le opzioni. 

	> [AZURE.NOTE] È possibile impostare come destinazione l'emulatore di archiviazione per evitare di incorrere negli eventuali costi associati al servizio Archiviazione di Azure. Se però si sceglie di impostare come destinazione un account di archiviazione di Azure nel cloud, i costi per eseguire questa esercitazione saranno minimi.

	Se si imposta come destinazione un account di archiviazione nel cloud, immettere la chiave di accesso primaria per tale account di archiviazione. Per scoprire come copiare la chiave di accesso primaria tramite il Portale di gestione di Azure, vedere  	
	[Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys).
	
    ![Selezionare l'ambiente di destinazione][connection-string4]

6.  Modificare la voce **Nome** da **Impostazione1** a un nome più descrittivo come **StringaConnessioneArchiviazione**. Si farà riferimento a questa stringa di connessione successivamente nel codice in questa guida.  
    ![Cambiare il nome della stringa di connessione][connection-string5]
	
### Impostazione della stringa di connessione con la configurazione .NET

Se si intende scrivere un'applicazione diversa da un servizio cloud di Azure (vedere la sezione precedente), è consigliabile usare il sistema di configurazione .NET, ad esempio `web.config` o `app.config`. Oltre alle applicazioni progettate per l'esecuzione all'esterno di Azure, questo sistema include Siti Web di Azure o Macchine virtuali di Azure. Per archiviare la stringa di connessione, utilizzare l'elemento `<appSettings>` come segue. Sostituire `account-name` con il nome dell'account di archiviazione e `account-key` con la chiave di accesso dell'account:

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
  		</appSettings>
	</configuration>

Ad esempio, l'impostazione configuration nel file di configurazione sarà simile a:

	<configuration>
    	<appSettings>
      		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=nYV0gln9fT7bvY+rxu2iWAEyzPNITGkhM88J8HUoyofpK7C8fHcZc2kIZp6cKgYRUM74lHI84L50Iau1+9hPjB==" />
    	</appSettings>
	</configuration>

È ora possibile eseguire le procedure di questa guida.

[connection-string1]: ./media/storage-configure-connection-string-include/connection-string1.png
[connection-string2]: ./media/storage-configure-connection-string-include/connection-string2.png
[connection-string3]: ./media/storage-configure-connection-string-include/connection-string3.png
[connection-string4]: ./media/storage-configure-connection-string-include/connection-string4.png
[connection-string5]: ./media/storage-configure-connection-string-include/connection-string5.png

[Configurazione delle stringhe di connessione]: http://msdn.microsoft.com/library/azure/ee758697.aspx

<!--HONumber=49-->