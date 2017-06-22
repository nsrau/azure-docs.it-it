1. Copiare il programma di installazione in una cartella locale (ad esempio C:\Temp) sul server che si vuole proteggere. Eseguire i comandi seguenti come amministratore in un prompt dei comandi:

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Per installare il servizio Mobility, eseguire il comando seguente:

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```
3. A questo punto è necessario registrare l'agente nel server di configurazione.

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe”  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Argomenti della riga di comando del programma di installazione del servizio Mobility

```
Usage :
UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
```

| Parametro|Tipo|Descrizione|Valori possibili|
|-|-|-|-|
|/Role|Mandatory|Specifica se installare il servizio Mobility (MS) o MasterTarget (MT)|MS </br> MT|
|/InstallLocation|Facoltativo|Percorso in cui viene installato il servizio Mobility|Qualsiasi cartella del computer|
|/Platform|Mandatory|Specifica la piattaforma in cui viene installato il servizio Mobility </br> </br>- **VMware**: usare questo valore se si installa il servizio Mobility in una macchina virtuale in esecuzione in *host VMware vSphere ESXi*, *host Hyper-V* o *server fisici* </br> - **Azure**: usare questo valore se si installa un agente in una macchina virtuale IaaS di Azure| VMware </br> Azure|
|/Silent|Facoltativo|Specifica l'esecuzione del programma di installazione in modalità non interattiva| ND|

>[!TIP]
> I log di installazione sono disponibili in %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log

#### <a name="mobility-service-registration-command-line-arguments"></a>Argomenti della riga di comando per la registrazione del servizio Mobility

```
Usage :
UnifiedAgentConfigurator.exe”  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
```

  | Parametro|Tipo|Descrizione|Valori possibili|
  |-|-|-|-|
  |/CSEndPoint |Mandatory|Indirizzo IP del server di configurazione| Qualsiasi indirizzo IP valido|
  |/PassphraseFilePath|Mandatory|Percorso della passphrase |Qualsiasi percorso file locale o UNC valido|


>[!TIP]
> I log di AgentConfiguration sono disponibili in %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log
