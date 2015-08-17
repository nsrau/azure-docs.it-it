Lo script di distribuzione ignorerà la creazione dell'ambiente virtuale in Azure se rileva che esiste già un ambiente virtuale compatibile. Questo comportamento può accelerare notevolmente la distribuzione. I pacchetti già installati verranno ignorati da pip.

In determinate situazioni, può essere utile forzare l'eliminazione dell'ambiente virtuale, ad esempio quando si decide di includere un ambiente virtuale come parte del repository oppure quando è necessario eliminare alcuni pacchetti o testare le modifiche apportate a requirements.txt.

Sono disponibili alcune opzioni per gestire l'ambiente virtuale in Azure.

### Opzione 1: Utilizzare il FTP

Con un client FTP connettersi al server per eliminare la cartella env. Tenere presente che alcuni client FTP, come i Web browser, possono essere di sola lettura e non consentire l'eliminazione di cartelle. Per questo motivo, è bene assicurarsi di usare un client FTP con questa funzionalità. Il nome host FTP e l'utente vengono visualizzati nel blade dell'applicazione web nel [portale Azure](https://portal.azure.com).

### Opzione 2: Attiva/Disattiva runtime

Ecco un'alternativa che sfrutta il fatto che lo script di distribuzione eliminerà la cartella env quando non corrisponde alla versione desiderata di Python. In questo modo viene effettivamente eliminato l'ambiente esistente e ne viene creato uno nuovo.

1. Passare a una versione diversa di Python (tramite runtime.txt o **le impostazioni dell'applicazione** blade nel portale di Azure)
1. Eseguire il push GIT di alcune modifiche (ignorare eventuali errori di installazione di pip)
1. Tornare alla versione iniziale di Python
1. Eseguire di nuovo il push GIT di alcune modifiche

### Opzione 3: personalizzare lo script di distribuzione

Se lo script di distribuzione è stato personalizzato, è possibile modificare il codice in deploy.cmd per forzare l'eliminazione della cartella env.

<!---HONumber=August15_HO6-->