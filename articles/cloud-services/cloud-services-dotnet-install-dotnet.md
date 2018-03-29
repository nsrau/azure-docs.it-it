---
title: Installare .NET nei ruoli di Servizi cloud di Azure | Microsoft Docs
description: Questo articolo illustra come installare manualmente .NET Framework nei ruoli Web e di lavoro del servizio cloud
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: ''
ms.assetid: 8d1243dc-879c-4d1f-9ed0-eecd1f6a6653
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2017
ms.author: adegeo
ms.openlocfilehash: fa41a18b31a255fa7cda90e33a948f5c6c65434f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2018
---
# <a name="install-net-on-azure-cloud-services-roles"></a>Installare .NET nei ruoli di Servizi cloud di Azure
Questo articolo illustra come installare versioni di .NET Framework non incluse nel sistema operativo guest di Azure. È possibile usare .NET nel sistema operativo guest per configurare i ruoli Web e di lavoro del servizio cloud.

Ad esempio, è possibile installare .NET 4.6.1 nella famiglia di sistemi operativi guest 4, non disponibile in nessuna versione di .NET 4.6. La famiglia di sistemi operativi guest 5 è inclusa in .NET 4.6. Per le informazioni più recenti sulle versioni del sistema operativo guest di Azure, vedere [Novità sulle versioni del sistema operativo guest di Azure](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>Azure SDK 2.9 contiene una restrizione relativa alla distribuzione di .NET 4.6 nella famiglia di sistemi operativi guest 4 o versioni precedenti. Nel sito di [Microsoft Docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) è disponibile una correzione alla restrizione.

Per installare .NET nei ruoli Web e di lavoro, includere il programma di installazione Web di .NET come parte del progetto di servizio cloud. Avviare il programma di installazione come parte delle attività di avvio del ruolo. 

## <a name="add-the-net-installer-to-your-project"></a>Aggiungere al progetto il programma di installazione .NET
Per scaricare il programma di installazione Web per .NET Framework, scegliere la versione da installare:

* [Programma di installazione Web di .NET 4.7.1](http://go.microsoft.com/fwlink/?LinkId=852095)
* [Programma di installazione Web di .NET 4.6.1](http://go.microsoft.com/fwlink/?LinkId=671729)

Per aggiungere il programma di installazione per un ruolo *Web*:
  1. In **Ruoli** del progetto di servizio cloud in **Esplora soluzioni** fare clic con il pulsante destro del mouse sul ruolo *Web* e scegliere **Aggiungi** > **Nuova cartella**. Creare una cartella denominata **bin**.
  2. Fare clic con il pulsante destro del mouse sulla cartella bin e scegliere **Aggiungi** > **Elemento esistente**. Selezionare il programma di installazione .NET e aggiungerlo alla cartella bin.
  
Per aggiungere il programma di installazione per un ruolo *di lavoro*:
* Fare clic con il pulsante destro del mouse sul ruolo *di lavoro* e scegliere **Aggiungi** > **Elemento esistente**. Selezionare il programma di installazione .NET e aggiungerlo al ruolo. 

I file aggiunti in questo modo alla cartella di contenuto del ruolo vengono aggiunti automaticamente al pacchetto del servizio cloud. I file vengono quindi distribuiti in una posizione analoga nella macchina virtuale. Ripetere questo processo per tutti i ruoli Web e di lavoro nel servizio cloud, in modo che tutti i ruoli abbiano una copia del programma di installazione.

> [!NOTE]
> Anche se l'applicazione è destinata a .NET 4.6, è necessario installare .NET 4.6.1 nel ruolo del servizio cloud. Il sistema operativo guest include l'[aggiornamento 3098779](https://support.microsoft.com/kb/3098779) e l'[aggiornamento 3097997](https://support.microsoft.com/kb/3097997) della Knowledge Base. Se .NET 4.6 viene installato sugli aggiornamenti della Knowledge Base, possono verificarsi problemi durante l'esecuzione delle applicazioni .NET. Per evitare tali problemi, installare .NET 4.6.1 anziché la versione 4.6. Per altre informazioni, vedere l'[articolo 3118750 della Knowledge Base](https://support.microsoft.com/kb/3118750).
> 
> 

![Contenuto del ruolo con i file del programma di installazione][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definire le attività di avvio per i ruoli
È possibile usare le attività di avvio per eseguire operazioni prima dell'avvio di un ruolo. Per assicurarsi che il framework venga installato prima di eseguire qualsiasi codice dell'applicazione, installare .NET Framework come parte dell'attività di avvio. Per altre informazioni sulle attività di avvio, vedere [Eseguire attività di avvio in Azure](cloud-services-startup-tasks.md). 

1. Aggiungere il contenuto seguente al file ServiceDefinition.csdef nel nodo **WebRole** o **WorkerRole** per tutti i ruoli:
   
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
   
    La configurazione precedente esegue il comando `install.cmd` della console con privilegi di amministratore per installare .NET Framework. La configurazione crea anche un elemento **LocalStorage** con nome **NETFXInstall**. Lo script di avvio imposta la cartella temporanea per l'uso di questa risorsa di archiviazione locale. 
    
    > [!IMPORTANT]
    > Perché il framework venga installato correttamente, impostare le dimensioni della risorsa su almeno 1.024 MB.
    
    Per altre informazioni sulle attività di avvio, vedere [Attività di avvio comuni del servizio cloud](cloud-services-startup-tasks-common.md).

2. Creare un file denominato **install.cmd** e aggiungere al file lo script di installazione seguente.

    Lo script verifica se la versione di .NET Framework specificata è già installata nel computer eseguendo una query sul registro. Se la versione di .NET non è installata, viene avviato il programma di installazione Web di .NET. Per consentire la risoluzione di eventuali problemi, lo script registra tutte le attività in un file denominato startuptasklog-(data e ora corrente).txt memorizzato nell'archiviazione locale **InstallLogs**.
  
    > [!IMPORTANT]
    > Usare un editor di testo di base, come Blocco note di Windows, per creare il file install.cmd. Se si usa Visual Studio per creare un file di testo e si modifica l'estensione in cmd, il file potrebbe ancora contenere un byte order mark UTF-8. Il byte order mark può provocare un errore quando viene eseguita la prima riga dello script. Per evitare questo errore, creare la prima riga dello script come istruzione REM che possa essere ignorata dall'elaborazione dell'ordine dei byte. 
    > 
    >
   
    ```cmd
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    REM ***** To install .NET 4.7 set the variable netfx to "NDP47" *****
    REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" *****
    set netfx="NDP471"
    
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
    if %netfx%=="NDP471" goto NDP471
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
    goto logtimestamp
    
    :NPD47
    set "netfxinstallfile=NDP47-KB3186500-Web.exe"
    set netfxregkey="0x707FE"
    goto logtimestamp
    
    :NDP471
    set "netfxinstallfile=NDP471-KB4033344-Web.exe"
    set netfxregkey="0x709fc"
    goto logtimestamp
    
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

3. Aggiungere il file install.cmd a ogni ruolo tramite **Aggiungi** > **Elemento esistente** in **Esplora soluzioni**, come descritto in precedenza in questo argomento. 

    Al termine, tutti i ruoli avranno a disposizione il file del programma di installazione di .NET e il file install.cmd.

   ![Contenuto del ruolo con tutti i file][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Configurare la diagnostica per trasferire i log di avvio nell'archivio BLOB
Per semplificare la risoluzione dei problemi di installazione, è possibile configurare Diagnostica di Azure per trasferire i file di log generati dallo script di avvio o dal programma di installazione di .NET nell'archivio BLOB di Azure. Con questo approccio è possibile visualizzare i log scaricando i file di log dall'archivio BLOB anziché collegandosi al ruolo tramite desktop remoto.

Per configurare la diagnostica, aprire il diagnostics.wadcfgx e aggiungere il contenuto seguente al nodo **Directory**: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Questo codice XML permette di configurare Diagnostica di Azure per il trasferimento di tutti i file presenti nella directory dei log della risorsa **NETFXInstall** nell'account di archiviazione di Diagnostica nel contenitore BLOB **netfx-install**.

## <a name="deploy-your-cloud-service"></a>Distribuire il servizio cloud
Quando si distribuisce il servizio cloud, le attività di avvio installano .NET Framework, se non è già installato. Durante l'installazione del framework, i ruoli dei servizi cloud sono in stato *occupato*. Se l'installazione del framework richiede un riavvio, potrebbero essere riavviati anche i ruoli del servizio. 

## <a name="additional-resources"></a>Risorse aggiuntive
* [Installazione di .NET Framework][Installing the .NET Framework]
* [Determinare quali sono le versioni di .NET Framework installate][How to: Determine Which .NET Framework Versions Are Installed]
* [Risoluzione dei problemi di installazione di .NET Framework][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png
