1. Copiare il programma di installazione in una cartella locale (ad esempio, /tmp) nel server da proteggere ed eseguire questi comandi da una finestra del terminale:
  ```
  cd /tmp
  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. È ora possibile installare il servizio Mobility con la riga di comando seguente:

  ```
  sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P MobSvc.passphrase
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Argomenti della riga di comando del programma di installazione del servizio Mobility

|Parametro|Tipo|Descrizione|Valori possibili|
|-|-|-|-|
|-t |Mandatory|Tipo di agente<br>(sarà deprecato nella prossima versione)|*both*|
|-a |Mandatory|Configurazione dell'agente<br>(sarà deprecato nella prossima versione) |*host*|
|-R |Facoltativo|Ruolo dell'agente|Agente<br>MasterTarget|
|-d |Facoltativo|Percorso in cui verrà installato il servizio Mobility|/usr/local/ASR|
|-i |Mandatory|Indirizzo IP del server di configurazione|Qualsiasi indirizzo IP valido|
|-p |Mandatory|Porta su cui il server di configurazione è in ascolto per le connessioni in ingresso|443|
|-s |Mandatory|Avvio del servizio al termine dell'installazione<br>(sarà deprecato nella prossima versione)|*y*|
|-c |Mandatory|Modalità di comunicazione tra agente e server di elaborazione<br>(sarà deprecato nella prossima versione) |*https*|
|-P |Mandatory|Passphrase del server di configurazione|Qualsiasi percorso file locale o UNC valido|


#### <a name="sample-usage"></a>Esempio di utilizzo
```
sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i 192.168.2.53 -p 443 -s y -c https -P /tmp/MobSvc.passphrase
```


<!--HONumber=Jan17_HO3-->


