1. Copiare il programma di installazione in una cartella locale (ad esempio /tmp) sul server che si vuole proteggere. Eseguire i comandi seguenti in un terminale:
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Per installare il servizio Mobility, eseguire il comando seguente:

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. Al termine dell'installazione è necessario registrare il servizio Mobility nel server di configurazione. Eseguire questo comando per registrare il servizio Mobility nel server di configurazione.

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```

#### <a name="mobility-service-installer-command-line"></a>Riga di comando del programma di installazione del servizio Mobility

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|Parametro|type|DESCRIZIONE|Valori possibili|
|-|-|-|-|
|-r |Mandatory|Specifica se installare il servizio Mobility (MS) o MasterTarget (MT)|MS </br> MT|
|-d |Facoltativo|Percorso in cui verrà installato il servizio Mobility|/usr/local/ASR|
|-v|Mandatory|Specifica la piattaforma in cui viene installato il servizio Mobility </br> </br>- **VMware: usare questo valore se si sta installando il servizio Mobility in una macchina virtuale in esecuzione in *host VMware vSphere ESXi*, *host Hyper-V o *server fisici* </br> -**Azure: usare questo valore se si sta installando un agente in una macchina virtuale IaaS di Azure| VMware </br> Azure|
|-q|Facoltativo|Specifica l'esecuzione del programma di installazione in modalità non interattiva| N/D|


#### <a name="mobility-service-configuration-command-line"></a>Riga di comando di configurazione del servizio Mobility

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Parametro|type|DESCRIZIONE|Valori possibili|
|-|-|-|-|
|-i |Mandatory|Indirizzo IP del server di configurazione|Qualsiasi indirizzo IP valido|
|-P |Mandatory|Percorso completo del file in cui viene salvata la passphrase di connessione|Qualsiasi cartella valida|
