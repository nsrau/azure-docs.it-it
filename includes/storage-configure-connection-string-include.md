## Configurare una stringa di connessione di archiviazione

Nella libreria del client di archiviazione di Azure per .NET è possibile usare una stringa di connessione di archiviazione per configurare gli endpoint e le credenziali per l'accesso ai servizi di archiviazione. Si consiglia di gestire la stringa di connessione di archiviazione in un file di configurazione, invece di impostarla come hardcoded nell'applicazione. Sono disponibili due opzioni per salvare la stringa di connessione:

- Se l'applicazione viene eseguita in un servizio cloud di Azure, salvare la stringa di connessione con il sistema di configurazione dei servizi di Azure (file `*.csdef` e `*.cscfg`). Per informazioni dettagliate sulla configurazione del servizio cloud di Azure, vedere [Come creare e distribuire un servizio Cloud](../articles/cloud-services/cloud-services-how-to-create-deploy.md).
- Se l'applicazione viene eseguita in macchine virtuali di Azure o si compilano applicazioni .NET che verranno eseguite all'esterno di Azure, salvare la stringa di connessione con il sistema di configurazione .NET, ad esempio il file `web.config` o `app.config`.

Più avanti in questa guida verrà spiegato come recuperare la stringa di connessione dal codice.

### Configurazione della stringa di connessione da un servizio cloud di Azure

In Servizi cloud di Azure è presente uno speciale meccanismo di configurazione dei servizi che consente di modificare dinamicamente le impostazioni di configurazione dal portale di gestione di Azure senza ridistribuire l'applicazione.

Per configurare la stringa di connessione nella configurazione dei servizi di Azure:

1.  All'interno di Esplora soluzioni di Visual Studio, fare clic con il pulsante destro del mouse sul ruolo Web o sul ruolo di lavoro nella cartella **Ruoli** del progetto di distribuzione di Azure, quindi scegliere **Proprietà**. ![Selezione delle proprietà per un ruolo servizio cloud in Visual Studio][connection-string1].

2.  Fare clic sulla scheda **Impostazioni** e quindi sul pulsante **Aggiungi impostazione**. ![Aggiunta di un'impostazione del servizio cloud in Visual Studio][connection-string2]

    Nella griglia delle impostazioni verrà visualizzata una nuova voce **Impostazione1**.

3.  Nell'elenco a discesa **Tipo** della nuova voce **Impostazione1** selezionare **Stringa di connessione**. ![Impostare il tipo di stringa di connessione][connection-string3]

4.  Fare clic sul pulsante **...** a destra della voce **Impostazione1**. Verrà visualizzata la finestra di dialogo **Stringa di connessione all'account di archiviazione**.

5.  Scegliere se si desidera utilizzare come destinazione l'emulatore di archiviazione (servizio Archiviazione di Microsoft Azure simulato nel computer locale) oppure un account di archiviazione nel cloud. Il codice riportato in questa guida funziona con entrambe le opzioni.

	> [AZURE.NOTE] È possibile impostare come destinazione l'emulatore di archiviazione per evitare di incorrere negli eventuali costi associati al servizio Archiviazione di Azure. Se però si sceglie di impostare come destinazione un account di archiviazione di Azure nel cloud, i costi per eseguire questa esercitazione saranno minimi.

	Se si imposta come destinazione un account di archiviazione nel cloud, immettere la chiave di accesso primaria per tale account di archiviazione. Per scoprire come copiare la chiave di accesso primaria tramite il portale di gestione di Azure, vedere [Visualizzare e copiare le chiavi di accesso nelle risorse di archiviazione](../articles/storage/storage-create-storage-account.md#view-and-copy-storage-access-keys).

	> [AZURE.NOTE] La chiave dell’account di archiviazione è simile alla password radice per l'account di archiviazione. Assicurarsi di proteggere la chiave. Evitare di distribuirla agli altri utenti o salvarla in un file di testo normale accessibile ad altri utenti. Rigenerare la chiave tramite il portale di gestione se si ritiene che potrebbe essere stata violata.
	
    ![Select target environment][connection-string4]

6.  Modificare il valore **Impostazione1** della voce **Nome** specificando un nome più descrittivo, ad esempio **StorageConnectionString**. A questa stringa di connessione verrà fatto riferimento più avanti nel codice in questa guida. ![Modificare il nome della stringa di connessione][connection-string5]
	
### Impostazione della stringa di connessione con la configurazione .NET

Se si intende scrivere un'applicazione diversa da un servizio cloud di Azure (vedere la sezione precedente), è consigliabile usare il sistema di configurazione .NET (ad esempio `web.config` o `app.config`). Oltre alle applicazioni progettate per l'esecuzione all'esterno di Azure, questo sistema include Siti Web di Azure o Macchine virtuali di Azure. Per archiviare la stringa di connessione, utilizzare l'elemento `<appSettings>` come segue. Sostituire `account-name` con il nome dell'account di archiviazione e `account-key` con la chiave di accesso dell'account:

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

[Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx

<!---HONumber=AcomDC_0218_2016-->