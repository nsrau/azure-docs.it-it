---
title: Guida alla risoluzione dei problemi e problemi noti | Microsoft Docs
description: Elenco di problemi noti e guida alla risoluzione
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 54038785f513e56b07f5f3fafa3dbd6d4b6e7400
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2017
---
# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench - Guida alla risoluzione dei problemi e problemi noti 
Questo articolo consente di trovare e correggere errori o guasti riscontrati durante l'uso dell'applicazione Azure Machine Learning Workbench. 

## <a name="find-the-workbench-build-number"></a>Trovare il numero di build di Workbench
Quando si comunica con il team di supporto, è importante inserire il numero di build dell'app Workbench. In Windows, è possibile trovare il numero di build facendo clic sul menu **Guida** e scegliere **About Azure ML Workbench** (Informazioni su Azure ML Workbench). In MacOS, è possibile fare clic sul menu **Azure ML Workbench** e scegliere **About Azure ML Workbench** (Informazioni su Azure ML Workbench).

## <a name="machine-learning-msdn-forum"></a>Forum MSDN di Machine Learning
È disponibile un forum MSDN in cui è possibile pubblicare domande. Il forum viene monitorato attivamente dal team del prodotto. L'URL del forum è [https://aka.ms/azureml-forum](https://aka.ms/azureml-forum). 

## <a name="gather-diagnostics-information"></a>Raccogliere informazioni di diagnostica
In alcuni casi può essere utile fornire le informazioni di diagnostica quando si richiede supporto. Qui si trovano i file di log:

### <a name="installer"></a>Programma di installazione
Se si verificano errori durante l'installazione, i file di log del programma di installazione si trovano qui:

```
# Windows:
%TEMP%\amlinstaller\logs\*

# macOS:
/tmp/amlinstaller/logs/*
```
È possibile comprimere i contenuti delle directory e inviarli per la diagnostica.

### <a name="app-update"></a>Aggiornamento dell'app 
#### <a name="no-update-notification-on-windows-desktop"></a>Nessuna notifica di aggiornamento sul desktop di Windows 
Questo problema verrà risolto in uno dei prossimi aggiornamenti. Nel frattempo, la soluzione alternativa consiste nell'evitare di avviare l'app dal collegamento aggiunto alla barra delle applicazioni. Avviare invece l'app usando il menu Start o la barra di ricerca in Start oppure il collegamento sul desktop (se presente). 

#### <a name="no-update-notification-on-an-ubuntu-data-sciece-virtual-machine-dsvm"></a>Nessuna notifica di aggiornamento in un sistema Ubuntu Data Science Virtual Machine (DSVM)
Eseguire i passaggi seguenti per scaricare l'applicazione più recente:   
   - Rimuovere la cartella \Utenti\AppData\Local\amlworkbench
   - Rimuovere lo script `c:\dsvm\tools\setup\InstallAMLFromLocal.ps1`
   - Rimuovere il collegamento sul desktop che avvia lo script precedente
   - Eseguire un'installazione pulita tramite [https://aka.ms/azureml-wb-msi](https://aka.ms/azureml-wb-msi)

### <a name="workbench-desktop-app"></a>App desktop Workbench
Se si riscontrano problemi di accesso, o se desktop Workbench si arresta in modo anomalo, è possibile trovare i file di log qui:
```
# Windows
%APPDATA%\AmlWorkbench

# macOS
~/Library/Application Support/AmlWorkbench
``` 
È possibile comprimere i contenuti delle directory e inviarli per la diagnostica.

### <a name="experiment-execution"></a>Esecuzione dell'esperimento
Se un determinato script ha esito negativo durante l'invio dall'app desktop, provare a inviarlo di nuovo tramite l'interfaccia della riga di comando usando il comando `az ml experiment submit`. Questo dovrebbe restituire un messaggio di errore completo in formato JSON e, ancora più importante, contiene un valore **ID operazione**. Inviare il file JSON includendo l'**ID operazione** per facilitare la diagnosi del problema. 

Se un determinato script viene inviato ma non può essere eseguito dovrebbe visualizzare l'**ID di esecuzione** per identificare quella specifica esecuzione. È possibile creare un pacchetto dei file di log pertinenti con il comando seguente:

```azurecli
# Create a ZIP file that contains all the diagnostics information
$ az ml experiment diagnostics -r <run_id> -t <target_name>
```

Il comando `az ml experiment diagnostics` genera un file `diagnostics.zip` nella cartella radice del progetto. Il pacchetto ZIP contiene l'intera cartella di progetto nello stato in cui si trovava al momento dell'esecuzione e le informazioni di log. Assicurarsi di rimuovere tutte le informazioni riservate che non si vogliono includere prima di inviare il file di diagnostica.

## <a name="send-us-a-frown-or-a-smile"></a>Inviare una faccia imbronciata o uno smile

Quando si usa Azure ML Workbench, è anche possibile inviare una faccia imbronciata o uno smile facendo clic sull'icona a forma di smile nell'angolo inferiore sinistro della shell dell'applicazione. Si può scegliere facoltativamente di includere il proprio indirizzo di posta elettronica (per poter essere contattati) e/o uno screenshot dello stato corrente. 

## <a name="known-service-limits"></a>Limiti noti del servizio
- Dimensioni massime consentite per la cartella di progetto: 25 MB
    >[!NOTE]
    >Questo limite non si applica alle cartelle `.git`, `docs` e `outputs`. Questi nomi di cartella distinguono tra maiuscole e minuscole. Se si usano file di grandi dimensioni, vedere [Persisting Changes and Deal with Large Files](how-to-read-write-files.md) (Salvataggio permanente delle modifiche e uso di file di grandi dimensioni).

- Tempo di esecuzione massimo consentito per gli esperimenti: sette giorni

- Dimensioni massime di un file tracciato nella cartella `outputs` dopo un'esecuzione: 512 MB
  - Se un file generato dallo script nella cartella outputs è di dimensioni superiori a 512 MB, non viene raccolto in tale posizione. Se si usano file di grandi dimensioni, vedere [Persisting Changes and Deal with Large Files](how-to-read-write-files.md) (Salvataggio permanente delle modifiche e uso di file di grandi dimensioni).

- Le chiavi SSH non sono supportate quando ci si connette a un computer remoto o a un cluster Spark tramite SSH. È attualmente supportata solo la modalità nome utente/password.

- Quando si usa il cluster HDInsight come destinazione di calcolo, è necessario usare BLOB di Azure come archiviazione primaria. L'archiviazione in Azure Data Lake non è supportata.

- Le trasformazioni clustering del testo non sono supportate in Mac.

- La libreria RevoScalePy è supportata solo in Windows o in Linux (in contenitori Docker). Non è supportata in macOS.

## <a name="cant-update-workbench"></a>Non è possibile aggiornare Workbench
Quando è disponibile un nuovo aggiornamento, nella home page dell'app Workbench viene visualizzato un messaggio con informazioni sul nuovo aggiornamento. Dovrebbe essere visualizzata una notifica di aggiornamento nell'angolo in basso a sinistra dell'app sull'icona a forma di campanella. Fare clic sulla notifica ed eseguire l'installazione guidata per installare l'aggiornamento. 

![Immagine dell'aggiornamento](./media/known-issues-and-troubleshooting-guide/update.png)

Se non viene visualizzata la notifica, provare a riavviare l'app. Se la notifica di aggiornamento non viene ancora visualizzata dopo il riavvio, le cause potrebbero essere le seguenti.

### <a name="you-are-launching-workbench-from-a-pinned-shortcut-on-the-task-bar"></a>Workbench viene avviato da un collegamento bloccato sulla barra delle applicazioni
L'aggiornamento potrebbe essere già stato installato, ma il collegamento bloccato fa ancora riferimento a elementi precedenti nel disco. Per verificarlo, passare alla cartella `%localappdata%/AmlWorkbench` e controllare se è installata la versione più recente, quindi esaminare le proprietà del collegamento bloccato per vedere a quale percorso fa riferimento. Se questa causa risulta confermata, è sufficiente rimuovere il collegamento precedente, avviare Workbench dal menu Start e, facoltativamente, creare un nuovo collegamento bloccato sulla barra delle applicazioni.

### <a name="you-installed-workbench-using-the-install-azure-ml-workbench-link-on-a-windows-dsvm"></a>Workbench è stato installato tramite il collegamento per l'installazione di Azure Machine Learning Workbench in una macchina virtuale di Windows Data Science
Sfortunatamente non è disponibile alcuna correzione in questo caso. È necessario eseguire la procedura seguente per rimuovere gli elementi installati e scaricare il programma di installazione più recente per installare di nuovo Workbench: 
   - Rimuovere la cartella `C:\Users\<Username>\AppData\Local\amlworkbench`
   - Rimuovere lo script `C:\dsvm\tools\setup\InstallAMLFromLocal.ps1`
   - Rimuovere il collegamento sul desktop che avvia lo script precedente
   - Scaricare il programma di installazione https://aka.ms/azureml-wb-msi e ripetere l'installazione.

## <a name="get-stuck-at-checking-experimentation-account-screen-after-logging-in"></a>Dopo l'accesso si rimane bloccati nella schermata "Verifica account sperimentazione in corso"
Dopo l'accesso, l'app Workbench potrebbe bloccarsi su una schermata vuota con un messaggio che mostra "Verifica account sperimentazione in corso" con una ruota che gira. Per risolvere il problema, eseguire la procedura seguente:
1. Arrestare l'app
2. Eliminare il file seguente:
  ```
  # on Windows
  %appdata%\AmlWorkbench\AmlWb.settings

  # on macOS
  ~/Library/Application Support/AmlWorkbench/AmlWb.settings
  ```
3. Riavviare l'app.

## <a name="cant-delete-experimentation-account"></a>Non è possibile eliminare l'account di Sperimentazione
È possibile usare l'interfaccia della riga di comando per eliminare un account di Sperimentazione, ma è necessario eliminare innanzitutto le aree di lavoro figlio e i progetti figlio al loro interno. In caso contrario, viene visualizzato un errore.

```azure-cli
# delete a project
$ az ml project delete -g <resource group name> -a <experimentation account name> -w <worksapce name> -n <project name>

# delete a workspace 
$ az ml workspace delete -g <resource group name> -a <experimentation account name> -n <worksapce name>

# delete an experimentation account
$ az ml account experimentation delete -g <resource group name> -n <experimentation account name>
```

È anche possibile eliminare i progetti e le aree di lavoro nell'app di Workbench.

## <a name="cant-open-file-if-project-is-in-onedrive"></a>Non è possibile aprire il file se il progetto si trova in OneDrive
Se si dispone di Windows 10 Fall Creators Update e il progetto viene creato in una cartella locale associata a OneDrive, si potrebbe non riuscire ad aprire alcun file in Workbench. Ciò è dovuto a un bug introdotto con Fall Creators Update che causa un errore del codice di node.js in una cartella di OneDrive. Il bug verrà presto corretto con un aggiornamento di Windows, ma fino ad allora, non creare progetti in una cartella di OneDrive.

## <a name="file-name-too-long-on-windows"></a>Nome file troppo a lungo in Windows
Se si usa Workbench in Windows, si può incorrere nel limite predefinito di 260 caratteri per la lunghezza dei nomi di file, che potrebbe essere segnalato con un errore di tipo "Impossibile trovare il percorso specificato". È possibile modificare l'impostazione di una chiave del Registro di sistema per consentire nomi di percorso per i file molto più lunghi. Per altre informazioni su come configurare la chiave del Registro di sistema _MAX_PATH_, vedere [questo articolo](https://msdn.microsoft.com/en-us/library/windows/desktop/aa365247%28v=vs.85%29.aspx?#maxpath).

## <a name="docker-error-read-connection-refused"></a>Errore di Docker "read: connection refused"
In caso di esecuzione su un contenitore Docker locale, può essere talvolta visualizzato l'errore seguente: 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```

È possibile risolvere l'errore modificando il server DNS di Docker da `automatic` al valore fisso `8.8.8.8`.

## <a name="remove-vm-execution-error-no-tty-present"></a>Risolvere l'errore di esecuzione in VM "no tty present"
In caso di esecuzione su un contenitore Docker in un computer Linux remoto, potrebbe essere visualizzato il messaggio di errore seguente:
```
sudo: no tty present and no askpass program specified.
``` 
Questo errore può verificarsi se si usa il portale di Azure per modificare la password radice di una VM Ubuntu Linux. 

Azure Machine Learning Workbench richiede l'accesso a sudoers senza password per l'esecuzione in host remoti. A tale scopo, il modo più semplice è usare _visudo_ per modificare il file seguente (se non esiste, è possibile crearlo):

```
$ sudo visudo -f /etc/sudoers
```

>[!IMPORTANT]
>È importante modificare il file con _visudo_ e non con un altro comando. _visudo_ esegue automaticamente il controllo della sintassi su tutti i file di configurazione di sudo e l'impossibilità di generare un file sudoers sintatticamente corretto può impedire l'accesso a sudo.

Inserire la riga seguente alla fine del file:

```
username ALL=(ALL) NOPASSWD:ALL
```

_username_ è il nome usato da Azure Machine Learning Workbench per accedere all'host remoto.

La riga deve essere inserita dopo #includedir "/etc/sudoers.d". In caso contrario, un'altra regola potrebbe eseguirne l'override.

Se si ha una configurazione di sudo più complessa, vedere la documentazione relativa a sudo per Ubuntu disponibile all'indirizzo https://help.ubuntu.com/community/Sudoers

L'errore riportato sopra può verificarsi anche se non si usa una VM Linux basata su Ubuntu in Azure come destinazione dell'esecuzione. Per l'esecuzione remota è supportata solo una VM Linux basata su Ubuntu. 

## <a name="vm-disk-is-full"></a>Disco della VM pieno
Quando si crea una nuova VM Linux in Azure, per impostazione predefinita si ottiene un disco da 30 GB per il sistema operativo. Per impostazione predefinita, il motore Docker usa lo stesso disco per scaricare le immagini ed eseguire i contenitori. È così possibile che il disco del sistema operativo si riempia e che, in tal caso, venga visualizzato un errore che segnala che il disco della VM è pieno.

Una risoluzione rapida consiste nel rimuovere tutte le immagini Docker che non si usano più. Questo comando di Docker esegue tale operazione. Per eseguire il comando di Docker da una shell Bash, è necessario connettersi tramite SSH alla VM.

```
$ docker system prune -a
```

È anche possibile aggiungere un disco dati e configurare il motore Docker per l'uso del disco dati per l'archiviazione delle immagini. Vedere l'articolo su [come aggiungere un disco dati](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/add-disk). È quindi possibile [modificare la posizione in cui Docker archivia le immagini](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169).

In alternativa è possibile espandere il disco del sistema operativo, senza dover modificare la configurazione del motore Docker. Vedere l'articolo su [come espandere il disco del sistema operativo](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/add-disk).

## <a name="sharing-c-drive-on-windows"></a>Condivisione dell'unità C in Windows
In caso di esecuzione in un contenitore Docker locale in Windows, impostando `sharedVolumes` su `true` nel file `docker.compute` in `aml_config` è possibile migliorare le prestazioni di esecuzione. A tale scopo è tuttavia necessario condividere l'unità C nello _strumento Docker per Windows_. Se non si riesce a condividere l'unità C, provare i suggerimenti seguenti:

* Controllare la condivisione nell'unità C usando Esplora file
* Aprire le impostazioni della scheda di rete e disinstallare/reinstallare "Condivisione file e stampanti per reti Microsoft" per vEthernet
* Aprire le impostazioni di Docker e condividere l'unità C dalle impostazioni di Docker
* Le modifiche alla password di Windows influiscono sulla condivisione. Aprire Esplora file, condividere di nuovo l'unità C e immettere la nuova password.
* Potrebbe anche verificarsi un problema di firewall quando si prova a condividere l'unità C con Docker. Questo [post di Stack Overflow](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) può essere utile.
* Quando si condivide l'unità C usando le credenziali di dominio, la condivisione potrebbe smettere di funzionare nelle reti in cui il controller di dominio non è raggiungibile (ad esempio, rete domestica, Wi-Fi pubblico e così via). Per altre informazioni, vedere [questo post](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).

È anche possibile evitare il problema della condivisione, con un costo ridotto in termini di prestazioni, impostando `sharedVolumne` su `false` nel file `docker.compute`.

## <a name="some-useful-docker-commands"></a>Alcuni comandi utili di Docker

Di seguito sono riportati alcuni comandi utili di Docker:

```sh
# display all running containers
$ docker ps

# dislplay all containers (running or stopped)
$ docke ps -a

# display all images
$ docker images

# show Docker logs of a container
$ docker logs <container_id>

# create a new container and launch into a bash shell
$ docker run <image_id> /bin/bash

# launch into a bash shell on a running container
$ docker exec -it <container_id> /bin/bash

# stop an running container
$ docker stop <container_id>

# delete a container
$ docker rm <container_id>

# delete an image
$ docker rmi <image_id>

# delete all unussed Docker images 
$ docker system prune -a

```
