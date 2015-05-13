<properties
   pageTitle="Installare .NET in un ruolo del servizio cloud"
   description="Questo articolo descrive come installare manualmente .NET Framework in ruoli Web e di lavoro del servizio cloud"
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/23/2015"
   ms.author="saurabh"/>

# Installare .NET in un ruolo del servizio cloud 

Questo articolo descrive come installare .NET Framework in ruoli Web e di lavoro del servizio cloud. È possibile usare questi passaggi per installare una versione di .NET Framework che non è presente nel sistema operativo guest Azure per impostazione predefinita. Per le informazioni più recenti sulle versioni del sistema operativo guest vedere [Rilasci del sistema operativo guest Azure e matrice di compatibilità SDK](cloud-services-guestos-update-matrix.md).

Il processo di installazione di .NET in ruoli Web e di lavoro prevede l'inclusione del pacchetto del programma di installazione .NET come parte del progetto cloud e l'avvio del programma di installazione come parte delle attività di avvio del ruolo.

## Aggiungere al progetto il programma di installazione .NET
1. Scaricare il [programma di installazione Web .NET 4.5.2](http://www.microsoft.com/it-it/download/details.aspx?id=42643)
2. Per un ruolo web
  1. In **Esplora soluzioni** in **Ruoli** nel progetto del servizio cloud fare clic con il pulsante destro del mouse sul ruolo e quindi selezionare **Aggiungi\>Nuova cartella**. Creare una cartella denominata *bin*
  2. Fare clic sulla cartella **bin** e selezionare **Aggiungi\>Elemento esistente**. Selezionare il programma di installazione .NET e aggiungerlo alla cartella bin.
3. Creare un ruolo di lavoro
  1. Fare clic sul ruolo e selezionare **Aggiungi\>Elemento esistente**. Selezionare il programma di installazione .NET e aggiungerlo al ruolo. 

I file aggiunti in questo modo alla cartella Contenuto ruolo verranno automaticamente aggiunti al pacchetto del servizio cloud e distribuiti in una posizione coerente sulla macchina virtuale. Ripetere questo processo per tutti i ruoli Web e di lavoro nel servizio cloud in modo che per tutti i ruoli vi sia una copia del programma di installazione.

![Contenuto ruolo con i file del programma di installazione][1]

## Definire le attività di avvio per i ruoli
Le attività di avvio consentono di eseguire operazioni prima dell'avvio di un ruolo. Per assicurarsi che sia installato il framework prima di eseguire qualsiasi codice dell'applicazione, installare .NET Framework come parte dell'attività di avvio. Per altre informazioni sull'avvio di attività, vedere: [Eseguire attività di avvio in Azure](https://msdn.microsoft.com/library/azure/hh180155.aspx).

1. Aggiungere quanto segue al file *ServiceDefinition.csdef* nel nodo *WebRole* o *WorkerRole* per tutti i ruoli:
	
	```xml
	 <LocalResources>
	    <LocalStorage name="InstallLogs" sizeInMB="5" cleanOnRoleRecycle="false" />
	 </LocalResources>
	 <Startup>
	    <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
	        <Environment>
	        <Variable name="PathToInstallLogs">
	        <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='InstallLogs']/@path" />
	        </Variable>
	        </Environment>
	    </Task>
	 </Startup>
	```

	La configurazione precedente eseguirà il comando della console *install.cmd* con privilegi di amministratore in modo da poter installare .NET Framework. La configurazione creerà anche un LocalStorage con il nome *InstallLogs* per archiviare tutte le informazioni di log create dallo script di installazione. Per altre informazioni, vedere: [Usare le risorse di archiviazione locale per archiviare i file durante l'avvio](https://msdn.microsoft.com/library/azure/hh974419.aspx)

2. Creare il file install.cmd con il contenuto seguente:

	```
	REM install.cmd to install .NET Framework
	set timehour=%time:~0,2%
	set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
	set startuptasklog=%PathToInstallLogs%\startuptasklog-%timestamp%.txt
	set netfxinstallerlog = %PathToInstallLogs%\NetFXInstallerLog-%timestamp%
	echo Logfile generated at: %startuptasklog% >> %startuptasklog%
	echo Checking if .NET 4.5.2 is installed >> %startuptasklog%
	reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release | Find "0x5cbf5"
	if %ERRORLEVEL%== 0 goto end
	echo Installing .NET 4.5.2. Log: %netfxinstallerlog% >> %startuptasklog%
	start /wait %~dp0NDP452-KB2901954-Web.exe /q /serialdownload /log %netfxinstallerlog%
	:end
	echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
	```

	Lo script di installazione verifica se .NET 4.5.2 è già installato nel computer eseguendo una query sul registro. Se .NET 4.5.2 non è installato, viene avviato il programma di installazione Web .Net. Per la risoluzione di eventuali problemi, lo script registrerà tutte le attività in un file denominato *startuptasklog-\(currentdatetime\).txt* archiviato nella risorsa di archiviazione locale *InstallLogs*.

	> [AZURE.NOTE]Usare un semplice editor di testo, come il blocco note, per creare questo file. Se si usa Visual Studio per creare un file di testo e quindi lo si rinomina con estensione "cmd", il file potrebbe continuare a contenere un BOM UTF-8 e verrà generato un errore eseguendo la prima riga dello script. Se si prevede di usare Visual Studio per creare il file, aggiungere un REM \(commento\) nella prima riga del file in modo che venga ignorato durante l'esecuzione.
      
3. Aggiungere il file install.cmd a tutti i ruoli facendo clic con il pulsante destro del mouse sul{b\> \<b}ruolo e selezionando **Aggiungi\>Elemento esistente...**. In questo modo tutti i ruoli avranno a disposizione il file del programma di installazione .NET, nonché il file install.cmd.
	
	![Contenuto ruolo con tutti i file][2]

## Configurare la diagnostica per trasferire i log delle attività di avvio all'archiviazione BLOB \(facoltativo\)
È anche possibile configurare la diagnostica Azure per trasferire eventuali file di log generati tramite lo script di avvio o il programma di installazione .NET nell'archiviazione BLOB. Con questo approccio è possibile visualizzare i log semplicemente scaricando i file di log dall'archiviazione BLOB anziché collegarsi con il desktop remoto al ruolo.

Per configurare la diagnostica aprire *diagnostics.wadcfgx* e aggiungere quanto segue sotto il nodo *<Directories>*:

```xml 
<DataSources>
    <DirectoryConfiguration containerName="netfx-install">
    <LocalResource name="InstallLogs" relativePath="."/>
    </DirectoryConfiguration>
</DataSources>
```

Verrà configurata la diagnostica Azure per trasferire tutti i file della risorsa *InstallLogs* nell'account di archiviazione di diagnostica nel contenitore BLOB *netfx-install*.

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


<!--HONumber=52-->
