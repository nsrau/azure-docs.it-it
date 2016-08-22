<properties
   pageTitle="Installare .NET in un ruolo del servizio cloud | Microsoft Azure"
   description="Questo articolo descrive come installare manualmente .NET Framework in ruoli Web e di lavoro del servizio cloud"
   services="cloud-services"
   documentationCenter=".net"
   authors="thraka"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/10/2016"
   ms.author="adegeo"/>  

# Installare .NET in un ruolo del servizio cloud 

Questo articolo descrive come installare .NET Framework in ruoli Web e di lavoro del servizio cloud. È possibile usare questa procedura per installare .NET 4.6.1 sulla famiglia di sistemi operativi guest Azure 4. Per le informazioni più recenti sulle versioni del sistema operativo guest vedere [Rilasci del sistema operativo guest Azure e matrice di compatibilità SDK](cloud-services-guestos-update-matrix.md).

Il processo di installazione di .NET in ruoli Web e di lavoro prevede l'inclusione del pacchetto del programma di installazione .NET come parte del progetto cloud e l'avvio del programma di installazione come parte delle attività di avvio del ruolo.

## Aggiungere al progetto il programma di installazione .NET
- Scaricare il programma di installazione web per installare il .NET framework necessario
	- [Programma di installazione web di .NET 4.6.1](http://go.microsoft.com/fwlink/?LinkId=671729)
- Per un ruolo web
  1. In **Esplora soluzioni** in **Ruoli** nel progetto del servizio cloud fare clic con il pulsante destro del mouse sul ruolo e quindi selezionare **Aggiungi>Nuova cartella**. Creare una cartella denominata *bin*
  2. Fare clic sulla cartella **bin** e selezionare **Aggiungi>Elemento esistente**. Selezionare il programma di installazione .NET e aggiungerlo alla cartella bin.
- Creare un ruolo di lavoro
  1. Fare clic sul ruolo e selezionare **Aggiungi>Elemento esistente**. Selezionare il programma di installazione .NET e aggiungerlo al ruolo.

I file aggiunti in questo modo alla cartella Contenuto ruolo verranno automaticamente aggiunti al pacchetto del servizio cloud e distribuiti in una posizione coerente sulla macchina virtuale. Ripetere questo processo per tutti i ruoli Web e di lavoro nel servizio cloud in modo che per tutti i ruoli vi sia una copia del programma di installazione.

> [AZURE.NOTE] Anche se l'applicazione è destinata a .NET 4.6, è necessario installare .NET 4.6.1 nel ruolo del servizio cloud. Il sistema operativo guest di Azure include gli aggiornamenti [3098779](https://support.microsoft.com/kb/3098779) e [3097997](https://support.microsoft.com/kb/3097997). L'installazione di .NET 4.6 su questi aggiornamenti potrebbe causare problemi durante l'esecuzione di applicazioni .NET, quindi è necessario installare direttamente .NET 4.6.1 invece di .NET 4.6. Per altre informazioni, vedere l'articolo [KB 3118750](https://support.microsoft.com/kb/3118750).

![Contenuto ruolo con i file del programma di installazione][1]

## Definire le attività di avvio per i ruoli
Le attività di avvio consentono di eseguire operazioni prima dell'avvio di un ruolo. Per assicurarsi che sia installato il framework prima di eseguire qualsiasi codice dell'applicazione, installare .NET Framework come parte dell'attività di avvio. Per altre informazioni sull'avvio di attività, vedere: [Eseguire attività di avvio in Azure](cloud-services-startup-tasks.md).

1. Aggiungere quanto segue al file *ServiceDefinition.csdef* nel nodo **WebRole** o **WorkerRole** per tutti i ruoli:
	
	```xml
	<LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
	<Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
	```

	La configurazione precedente eseguirà il comando della console *install.cmd* con privilegi di amministratore in modo da poter installare .NET Framework. La configurazione creerà anche un LocalStorage con il nome *NETFXInstall*. Lo script di avvio imposterà la cartella temporanea per utilizzare questa risorsa di archiviazione locale in modo che il programma di installazione di .NET framework verrà scaricato e installato da questa risorsa. È importante impostare le dimensioni della risorsa ad almeno 1024 MB per garantire che il framework venga installato correttamente. Per altre informazioni sulle attività di avvio, vedere: [Attività di avvio comuni del servizio cloud](cloud-services-startup-tasks-common.md)

2. Creare un file**Install.cmd**e aggiungerlo a tutti i ruoli tramite clic sul tasto destro del mouse sul ruolo e selezionando**Aggiungi > elemento esistente...**. In questo modo tutti i ruoli avranno a disposizione il file del programma di installazione .NET, nonché il file install.cmd.
	
	![Contenuto ruolo con tutti i file][2]

	> [AZURE.NOTE] Usare un semplice editor di testo, come il blocco note, per creare questo file. Se si usa Visual Studio per creare un file di testo e quindi lo si rinomina con estensione "cmd", il file potrebbe continuare a contenere un BOM UTF-8 e verrà generato un errore eseguendo la prima riga dello script. Se si prevede di usare Visual Studio per creare il file, aggiungere un REM (commento) nella prima riga del file in modo che venga ignorato durante l'esecuzione.

3. Aggiungere il seguente script al file**Install.cmd**:

	```
	REM Set the value of netfx to install appropriate .NET Framework. 
	REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
	REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
	REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
	set netfx="NDP461"
	
	
	REM ***** Set script start timestamp *****
	set timehour=%time:~0,2%
	set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
	set "log=install.cmd started %timestamp%."
	
	REM ***** Exit script if running in Emulator *****
	if %ComputeEmulatorRunning%=="true" goto exit
	
	REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
	set TMP=%PathToNETFXInstall%
	set TEMP=%PathToNETFXInstall%
	
	REM ***** Setup .NET filenames and registry keys *****
	if %netfx%=="NDP461" goto NDP461
	if %netfx%=="NDP46" goto NDP46
	    set "netfxinstallfile=NDP452-KB2901954-Web.exe"
	    set netfxregkey="0x5cbf5"
	    goto logtimestamp
	
	:NDP46
	set "netfxinstallfile=NDP46-KB3045560-Web.exe"
	set netfxregkey="0x60051"
	goto logtimestamp
	
	:NDP461
	set "netfxinstallfile=NDP461-KB3102438-Web.exe"
	set netfxregkey="0x6041f"
	
	:logtimestamp
	REM ***** Setup LogFile with timestamp *****
	md "%PathToNETFXInstall%\log"
	set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
	set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
	echo %log% >> %startuptasklog%
	echo Logfile generated at: %startuptasklog% >> %startuptasklog%
	echo TMP set to: %TMP% >> %startuptasklog%
	echo TEMP set to: %TEMP% >> %startuptasklog%
	
	REM ***** Check if .NET is installed *****
	echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
	reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release | Find %netfxregkey%
	if %ERRORLEVEL%== 0 goto installed
	
	REM ***** Installing .NET *****
	echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
	start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
	if %ERRORLEVEL%== 0 goto installed
		echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%	
		if %ERRORLEVEL%== 3010 goto restart
		if %ERRORLEVEL%== 1641 goto restart
		echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
	
	:restart
	echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
	goto end
	
	:installed
	echo .NET (%netfx%) is installed >> %startuptasklog%
	
	:end
	echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
	
	:exit
	EXIT /B 0
	```
		
	Lo script di installazione verifica se la versione .NET framework specificata è già installata nel computer eseguendo una query sul registro. Se la versione .NET non è installata, viene avviato il programma di installazione Web di .Net. Per la risoluzione di eventuali problemi, lo script registrerà tutte le attività in un file denominato *startuptasklog-(currentdatetime).txt* archiviato nella risorsa di archiviazione locale *InstallLogs*.

	> [AZURE.NOTE] Lo script mostra ancora come installare .NET 4.5.2 o .NET 4.6 per la continuità. Non è necessario installare manualmente .NET 4.5.2 perché è già disponibile nel sistema operativo guest di Azure. Invece di installare .NET 4.6, è consigliabile installare direttamente .NET 4.6.1 a causa di quanto specificato nell'articolo [KB 3118750](https://support.microsoft.com/kb/3118750).
      

## Configurare la diagnostica per trasferire i log delle attività di avvio all'archivio BLOB 
Per semplificare la risoluzione di eventuali problemi di installazione, è possibile configurare Diagnostica di Azure per trasferire i file di log generati dallo script di avvio o dal programma di installazione .NET nell'archivio BLOB. Con questo approccio è possibile visualizzare i log semplicemente scaricando i file di log dall'archiviazione BLOB anziché collegarsi con il desktop remoto al ruolo.

Per configurare la diagnostica aprire *diagnostics.wadcfgx* e aggiungere quanto segue sotto il nodo **Directory**:

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Verrà configurata la diagnostica Azure per trasferire tutti i file nella directory *log* nella risorsa*NETFXInstall* nell’account di archiviazione di diagnostica nel contenitore BLOB *netfx-install*.

## Distribuzione del servizio 
Quando si distribuisce il servizio, le attività di avvio eseguiranno e installeranno .NET Framework, se non è già installato. I ruoli saranno nello stato occupato durante l'installazione del framework e potrebbero persino essere riavviati se l'installazione di framework lo richiede.

## Risorse aggiuntive

- [Installazione di .NET Framework][]
- [Procedura: determinare le versioni di .NET Framework installate][]
- [Risoluzione dei problemi di installazione di .NET Framework][]

[Procedura: determinare le versioni di .NET Framework installate]: https://msdn.microsoft.com/library/hh925568.aspx
[Installazione di .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Risoluzione dei problemi di installazione di .NET Framework]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png

 

<!---HONumber=AcomDC_0810_2016-->