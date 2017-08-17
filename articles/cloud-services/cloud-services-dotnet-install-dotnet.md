---
title: Installare .NET in un ruolo del servizio cloud | Documentazione Microsoft
description: Questo articolo descrive come installare manualmente .NET Framework in ruoli Web e di lavoro del servizio cloud
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: 
ms.assetid: 8d1243dc-879c-4d1f-9ed0-eecd1f6a6653
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: adegeo
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: e6154d990e10f67d4b30b889a62a99cedcbfccbe
ms.contentlocale: it-it
ms.lasthandoff: 07/25/2017

---

# <a name="install-net-on-a-cloud-service-role"></a>Installare .NET in un ruolo del servizio cloud
Questo articolo descrive come installare in ruoli Web e di lavoro del servizio cloud versioni di .NET Framework diverse da quelle fornite con il sistema operativo guest. È ad esempio possibile usare questa procedura per installare .NET 4.6.1 nella famiglia di sistemi operativi Azure Guest 4, che non include alcuna versione di .NET 4.6. Per le informazioni più recenti sulle versioni del sistema operativo guest, vedere [Novità sulle versioni del sistema operativo guest di Azure](cloud-services-guestos-update-matrix.md).

>[!NOTE]
>Il sistema operativo guest 5 include .NET 4.6

>[!IMPORTANT]
>Azure SDK 2.9 contiene una restrizione relativa alla distribuzione di .NET 4.6 nel sistema operativo guest 4 o una versione inferiore. Una correzione è disponibile [qui](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9).

Il processo di installazione di .NET in ruoli Web e di lavoro prevede l'inclusione del pacchetto del programma di installazione .NET come parte del progetto cloud e l'avvio del programma di installazione come parte delle attività di avvio del ruolo.  

## <a name="add-the-net-installer-to-your-project"></a>Aggiungere al progetto il programma di installazione .NET
* Scaricare il programma di installazione Web per installare il .NET framework necessario
  * [Programma di installazione Web di .NET 4.7](http://go.microsoft.com/fwlink/?LinkId=825298)
  * [Programma di installazione web di .NET 4.6.1](http://go.microsoft.com/fwlink/?LinkId=671729)

* Per un ruolo web
  1. In **Esplora soluzioni**, nel progetto di servizio cloud fare clic con il pulsante destro del mouse sul ruolo in **Ruoli** e quindi scegliere **Aggiungi > Nuova cartella**. Creare una cartella denominata *bin*
  2. Fare clic con il pulsante destro del mouse sulla cartella **bin**, quindi scegliere **Aggiungi > Elemento esistente**. Selezionare il programma di installazione .NET e aggiungerlo alla cartella bin.
  
* Creare un ruolo di lavoro
  1. Fare clic con il pulsante destro del mouse sul ruolo, quindi scegliere **Aggiungi > Elemento esistente**. Selezionare il programma di installazione .NET e aggiungerlo al ruolo. 

I file aggiunti in questo modo alla cartella Contenuto ruolo vengono automaticamente aggiunti al pacchetto del servizio cloud e distribuiti in una posizione coerente sulla macchina virtuale. Ripetere questo processo per tutti i ruoli Web e di lavoro nel servizio cloud in modo che per tutti i ruoli vi sia una copia del programma di installazione.

> [!NOTE]
> Anche se l'applicazione è destinata a .NET 4.6, è necessario installare .NET 4.6.1 nel ruolo del servizio cloud. Il sistema operativo guest di Azure include gli aggiornamenti [3098779](https://support.microsoft.com/kb/3098779) e [3097997](https://support.microsoft.com/kb/3097997). L'installazione di .NET 4.6 su questi aggiornamenti potrebbe causare problemi durante l'esecuzione di applicazioni .NET, quindi è necessario installare direttamente .NET 4.6.1 invece di .NET 4.6. Per altre informazioni, vedere l'articolo [KB 3118750](https://support.microsoft.com/kb/3118750).
> 
> 

![Contenuto ruolo con i file del programma di installazione][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definire le attività di avvio per i ruoli
Le attività di avvio consentono di eseguire operazioni prima dell'avvio di un ruolo. Per assicurarsi che sia installato il framework prima di eseguire qualsiasi codice dell'applicazione, installare .NET Framework come parte dell'attività di avvio. Per altre informazioni sulle attività di avvio, vedere: [Eseguire attività di avvio in Azure](cloud-services-startup-tasks.md). 

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
   
    La configurazione precedente esegue il comando *install.cmd* della console con privilegi di amministratore per installare .NET Framework. La configurazione crea anche un *LocalStorage* con nome *NETFXInstall*. Lo script di avvio imposta la cartella temporanea per l'uso di questa risorsa di archiviazione locale. È importante impostare le dimensioni della risorsa su almeno 1024 MB per garantire che il framework venga installato correttamente. Per altre informazioni sulle attività di avvio, vedere [Attività di avvio comuni del servizio cloud](cloud-services-startup-tasks-common.md) 

2. Creare un file **install.cmd** e aggiungerlo a tutti i ruoli facendo clic con il pulsante destro del mouse sul ruolo e scegliendo **Aggiungi > Elemento esistente...**. In questo modo tutti i ruoli avranno a disposizione il file del programma di installazione .NET, nonché il file install.cmd.
   
   ![Contenuto ruolo con tutti i file][2]
   
   > [!NOTE]
   > Usare un editor di testo di base, come il blocco note, per creare questo file. Se si usa Visual Studio per creare un file di testo e quindi lo si rinomina con estensione "cmd", il file potrebbe continuare a contenere un BOM UTF-8 e verrà generato un errore eseguendo la prima riga dello script. Assicurarsi che la prima riga del file sia un comando REM, in modo da saltare l'elaborazione del BOM UTF-8. 
   > 
   > 

3. Aggiungere il seguente script al file **Install.cmd** :
   
    ```cmd
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    REM ***** To install .NET 4.7 set the variable netfx to "NDP47" *****
    set netfx="NDP47"

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
    if %netfx%=="NDP47" goto NDP47
    if %netfx%=="NDP462" goto NDP462
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
        set "netfxinstallfile=NDP452-KB2901954-Web.exe"
        set netfxregkey="0x5cbf5"
        goto logtimestamp

    :NDP46
    set "netfxinstallfile=NDP46-KB3045560-Web.exe"
    set netfxregkey="0x6004f"
    goto logtimestamp

    :NDP461
    set "netfxinstallfile=NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6040e"
    goto logtimestamp

    :NDP462
    set "netfxinstallfile=NDP462-KB3151802-Web.exe"
    set netfxregkey="0x60632"

    :NDP47
    set "netfxinstallfile=NDP47-KB3186500-Web.exe"
    set netfxregkey="0x707FE"

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
    set /A netfxregkeydecimal=%netfxregkey%
    set foundkey=0
    FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
    echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
    if %foundkey% GEQ %netfxregkeydecimal% goto installed

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
    EXIT /B %ERRORLEVEL%

    :installed
    echo .NET (%netfx%) is installed >> %startuptasklog%

    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%

    :exit
    EXIT /B 0
    ```
   
    Lo script di installazione verifica se la versione .NET framework specificata è già installata nel computer eseguendo una query sul registro. Se la versione .NET non è installata, viene avviato il programma di installazione Web di .Net. Per la risoluzione di eventuali problemi, lo script registrerà tutte le attività in un file denominato *startuptasklog-(dataoracorrente).txt* archiviato nella risorsa di archiviazione locale *InstallLogs*.
   
   > [!NOTE]
   > Lo script mostra ancora come installare .NET 4.5.2 o .NET 4.6 per la continuità. Non è necessario installare manualmente .NET 4.5.2 perché è già disponibile nel sistema operativo guest di Azure. Invece di installare .NET 4.6, è consigliabile installare direttamente .NET 4.6.1 a causa di quanto specificato nell'articolo [KB 3118750](https://support.microsoft.com/kb/3118750).
   > 
   > 

## <a name="configure-diagnostics-to-transfer-the-startup-task-logs-to-blob-storage"></a>Configurare la diagnostica per trasferire i log delle attività di avvio all'archivio BLOB
Per semplificare la risoluzione di eventuali problemi di installazione, è possibile configurare Diagnostica di Azure per trasferire i file di log generati dallo script di avvio o dal programma di installazione .NET nell'archivio BLOB. Con questo approccio è possibile visualizzare i log semplicemente scaricando i file di log dall'archiviazione BLOB anziché collegarsi con il desktop remoto al ruolo.

Per configurare la diagnostica, aprire *diagnostics.wadcfgx* e aggiungere quanto segue sotto il nodo **Directory**: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Questo codice XML configura la diagnostica di Azure per trasferire tutti i file presenti nella directory *log* nella risorsa *NETFXInstall* nell'account di archiviazione di diagnostica nel contenitore BLOB *netfx-install*.

## <a name="deploying-your-service"></a>Distribuzione del servizio
Quando si distribuisce il servizio, le attività di avvio installano .NET Framework, se non è già installato. I ruoli hanno stato *occupato* durante l'installazione del framework e potrebbero persino essere riavviati se l'installazione di framework lo richiede. 

## <a name="additional-resources"></a>Risorse aggiuntive
* [Installazione di .NET Framework][Installing the .NET Framework]
* [Procedura: determinare le versioni di .NET Framework installate][How to: Determine Which .NET Framework Versions Are Installed]
* [Risoluzione dei problemi di installazione di .NET Framework][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: https://msdn.microsoft.com/library/hh925568.aspx
[Installing the .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Troubleshooting .NET Framework Installations]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



