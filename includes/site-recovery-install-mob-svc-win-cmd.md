1. Copiare il programma di installazione in una cartella locale (ad esempio, C:\Temp) nel server da proteggere ed eseguire questi comandi da un prompt dei comandi con privilegi elevati:

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. È ora possibile installare il servizio Mobility con la riga di comando seguente:

  ```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>``
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Argomenti della riga di comando del programma di installazione del servizio Mobility

```
Usage :
UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>
```

  | Parametro|Tipo|Descrizione|Valori possibili|
  |-|-|-|-|
  |/Role|Mandatory|Specifica se deve essere installato il servizio Mobility|Agente </br> MasterTarget|
  |/InstallLocation|Mandatory|Percorso in cui verrà installato il servizio Mobility|Qualsiasi cartella del computer|
  |/CSIP|Mandatory|Indirizzo IP del server di configurazione| Qualsiasi indirizzo IP valido|
  |/PassphraseFilePath|Mandatory|Percorso in cui viene archiviata la passphrase |Qualsiasi percorso file locale o UNC valido|
  |/LogGilePath|Facoltativo|Percorso del log di installazione|Qualsiasi cartella valida del computer|

#### <a name="sample-usage"></a>Esempio di utilizzo

```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "I192.168.2.35" /PassphraseFilePath "C:\Temp\MobSvc.passphrase"
```


<!--HONumber=Jan17_HO3-->


