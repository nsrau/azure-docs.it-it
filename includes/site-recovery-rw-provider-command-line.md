UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Parametri

* /ServerMode: obbligatorio. Specifica se devono essere installati i server di configurazione e di elaborazione o solo il server di elaborazione. Valori di input: CS, PS.
* InstallLocation: obbligatorio. Cartella in cui sono installati i componenti.
* /MySQLCredsFilePath: Obbligatorio. Percorso file in cui sono archiviate le credenziali del server MySQL. Il file deve essere nel formato:
* [MySQLCredentials]
* MySQLRootPassword = "<Password>"
* MySQLUserPassword = "<Password>"
* /VaultCredsFilePath: obbligatorio. Percorso del file di credenziali dell'insieme di credenziali.
* /EnvType: obbligatorio. Tipo di installazione. Valori: VMware, NonVMware.
* /PSIP e /CSIP. obbligatorio. Indirizzo IP del server di elaborazione e del server di configurazione.
* /PassphraseFilePath: obbligatorio. Percorso del file della passphrase.
* /BypassProxy: facoltativo. Specifica che il server di configurazione si connette ad Azure senza un proxy.
* /ProxySettingsFilePath: facoltativo. Impostazioni proxy, il proxy predefinito richiede l'autenticazione o un proxy personalizzato. Il file deve essere nel formato:
* [ProxySettings]
* ProxyAuthentication = "Sì/No"
* Proxy IP = "Indirizzo IP>"
* ProxyPort = "<Port>"
* ProxyUserName="<User Name>"
* ProxyPassword="<Password>"
* DataTransferSecurePort: facoltativo. Numero di porta per i dati di replica.
* SkipSpaceCheck: facoltativo. Ignora la verifica dello spazio per la cache.
* AcceptThirdpartyEULA: Obbligatorio. Accetta il contratto di licenza di terze parti.
* ShowThirdpartyEULA: obbligatorio. Visualizza le condizioni di licenza di terze parti. Se specificato come input, tutti gli altri parametri vengono ignorati.


<!--HONumber=Feb17_HO2-->


