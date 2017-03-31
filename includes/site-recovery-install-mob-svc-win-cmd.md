1. Copiare il programma di installazione in una cartella locale (ad esempio C:\Temp) sul server che si vuole proteggere. Eseguire i comandi seguenti come amministratore in un prompt dei comandi:

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Per installare il servizio Mobility, eseguire il comando seguente:

  ```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP address of the configuration server" /PassphraseFilePath <Full path to the passphrase file>``
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Argomenti della riga di comando del programma di installazione del servizio Mobility

```
Usage :
UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation directory>] [/CSIP <IP address>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log file path>]<br/>
```

  | Parametro|Tipo|Descrizione|Valori possibili|
  |-|-|-|-|
  |/Role|Mandatory|Specifica se deve essere installato il servizio Mobility|Agente </br> MasterTarget|
  |/InstallLocation|Mandatory|Percorso in cui viene installato il servizio Mobility|Qualsiasi cartella del computer|
  |/CSIP|Mandatory|Indirizzo IP del server di configurazione| Qualsiasi indirizzo IP valido|
  |/PassphraseFilePath|Mandatory|Percorso della passphrase |Qualsiasi percorso file locale o UNC valido|
  |/LogFilePath|Facoltativo|Percorso del log di installazione|Qualsiasi cartella valida del computer|

#### <a name="example"></a>Esempio

```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "I192.168.2.35" /PassphraseFilePath "C:\Temp\MobSvc.passphrase"
```
