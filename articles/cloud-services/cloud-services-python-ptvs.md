---
title: Ruoli Web e ruoli di lavoro Python con Visual Studio | Microsoft Docs
description: Panoramica dell'uso di Python Tools per Visual Studio per creare servizi cloud di Azure, inclusi ruoli Web e ruoli di lavoro.
services: cloud-services
documentationcenter: python
author: thraka
manager: timlt
editor: ''

ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: hero-article
ms.date: 08/03/2016
ms.author: adegeo

---
# <a name="python-web-and-worker-roles-with-python-tools-for-visual-studio"></a>Ruoli Web e ruoli di lavoro Python con Python Tools for Visual Studio
Questo articolo offre una panoramica dell'uso dei ruoli Web e di lavoro con [Python Tools for Visual Studio][]. Si apprenderà come usare Visual Studio per creare e distribuire un servizio cloud di base che usa Python.

## <a name="prerequisites"></a>Prerequisiti
* Visual Studio 2013 o 2015
* [Python Tools for Visual Studio][](PTVS.md)
* [Strumenti di Azure SDK per VS 2013][] o [Strumenti di Azure SDK per VS 2015][]
* [Python 2.7 a 32 bit][Python 2.7 a 32 bit] o [Python 3.5 a 32 bit][Python 3.5 a 32 bit]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles?"></a>Cosa sono i ruoli Web e di lavoro Python?
Azure offre tre modelli di calcolo per l'esecuzione di applicazioni: [funzionalità App Web in Servizio app di Azure][execution model-web sites], [Macchine virtuali di Azure][execution model-vms] e [Servizi cloud di Azure][execution model-cloud services]. Tutti e tre i modelli supportano Python. Servizi cloud, che include ruoli Web e di lavoro, fornisce la tecnologia di *piattaforma distribuita come servizio (PaaS)*. Nell'ambito di un servizio cloud, un ruolo Web fornisce un server Web IIS (Internet Information Services) dedicato su cui ospitare applicazioni Web front-end, mentre un ruolo di lavoro consente di eseguire attività asincrone, a esecuzione prolungata o perpetue, indipendenti dall'interazione o dall'input degli utenti.

Per altre informazioni, vedere [Informazioni sul servizio cloud].

> [!NOTE]
> *Come creare un semplice sito Web*
> Se si intende creare un semplice sito Web front-end, è possibile usare la funzionalità App Web in Azure App Service. È possibile procedere all'aggiornamento a un servizio cloud con facilità, in base alla crescita del sito Web e all'insorgere di nuove esigenze. Per articoli che trattano lo sviluppo della funzionalità App Web in Servizio app di Azure, vedere il <a href="/develop/python/">Centro per sviluppatori Python</a>.
> <br />
> 
> 

## <a name="project-creation"></a>Creazione del progetto
In Visual Studio è possibile selezionare **Servizio Cloud Azure** nella finestra di dialogo **Nuovo progetto** sotto **Python**.

![Finestra di dialogo Nuovo progetto](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

Nella procedura guidata del servizio cloud di Azure è possibile creare nuovi ruoli Web e di lavoro.

![Finestra di dialogo del servizio cloud di Azure](./media/cloud-services-python-ptvs/new-service-wizard.png)

Nel modello di ruolo di lavoro è disponibile il codice boilerplate per connettersi a un bus di servizio o a un account di archiviazione di Azure.

![Soluzione del servizio cloud](./media/cloud-services-python-ptvs/worker.png)

È possibile aggiungere ruoli di lavoro o Web a un servizio cloud esistente in qualsiasi momento.  È possibile scegliere di aggiungere alla soluzione progetti esistenti oppure crearne uno nuovo.

![Comando per l'aggiunta di un ruolo](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Il servizio cloud può contenere ruoli implementati in linguaggi diversi.  Ad esempio, è possibile avere un ruolo Web Python implementato usando Django, con ruoli di lavoro Python o C#.  È possibile mettere facilmente in comunicazione i ruoli con code di bus di servizio o code di archiviazione.

## <a name="install-python-on-the-cloud-service"></a>Installare Python nel servizio cloud
> [!WARNING]
> Gli script di installazione installati con Visual Studio al momento dell'ultimo aggiornamento di questo articolo non funzionano. Questa sezione illustra una soluzione alternativa.
> 
> 

Il problema principale con gli script di installazione è che non installano Python. Definire prima due [attività di avvio](cloud-services-startup-tasks.md) nel file [ServiceDefinition.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef). La prima attività, **PrepPython.ps1**, scarica e installa il runtime di Python. La seconda attività, **PipInstaller.ps1**, esegue pip per installare le eventuali dipendenze.

Gli script seguenti sono stati scritti per Python 3.5. Per usare la versione 2.x di Python, impostare il file di variabile **PYTHON2** su **on** per le due attività di avvio e l'attività di runtime: `<Variable name="PYTHON2" value="<mark>on</mark>" />`.

```xml
<Startup>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
  </Task>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>

  </Task>

</Startup>
```

È necessario aggiungere le variabili **PYTHON2** e **PYPATH** all'attività di avvio del ruolo di lavoro. La variabile **PYPATH** viene usata solo se la variabile **PYTHON2** è impostata su **on**.

```xml
<Runtime>
  <Environment>
    <Variable name="EMULATED">
      <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
    </Variable>
    <Variable name="PYTHON2" value="off" />
    <Variable name="PYPATH" value="%SystemDrive%\Python27" />
  </Environment>
  <EntryPoint>
    <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
  </EntryPoint>
</Runtime>
```

#### <a name="sample-servicedefinition.csdef"></a>File ServiceDefinition.csdef di esempio
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicePython" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
      <Setting name="Python2" />
    </ConfigurationSettings>
    <Startup>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="EMULATED">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
        </Variable>
        <Variable name="PYTHON2" value="off" />
        <Variable name="PYPATH" value="%SystemDrive%\Python27" />
      </Environment>
      <EntryPoint>
        <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
      </EntryPoint>
    </Runtime>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
  </WorkerRole>
</ServiceDefinition>
```



Creare ora i file **PrepPython.ps1** e **PipInstaller.ps1** nella cartella **./bin** del ruolo.

#### <a name="preppython.ps1"></a>PrepPython.ps1
Questo script installa Python. Se la variabile di ambiente **PYTHON2** viene impostata su **on** verrà installato Python 2.7, in caso contrario verrà installato Python 3.5.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if python is installed...$nl"
    if ($is_python2) {
        & "${env:SystemDrive}\Python27\python.exe"  -V | Out-Null
    }
    else {
        py -V | Out-Null
    }

    if (-not $?) {

        $url = "https://www.python.org/ftp/python/3.5.2/python-3.5.2-amd64.exe"
        $outFile = "${env:TEMP}\python-3.5.2-amd64.exe"

        if ($is_python2) {
            $url = "https://www.python.org/ftp/python/2.7.12/python-2.7.12.amd64.msi"
            $outFile = "${env:TEMP}\python-2.7.12.amd64.msi"
        }

        Write-Output "Not found, downloading $url to $outFile$nl"
        Invoke-WebRequest $url -OutFile $outFile
        Write-Output "Installing$nl"

        if ($is_python2) {
            Start-Process msiexec.exe -ArgumentList "/q", "/i", "$outFile", "ALLUSERS=1" -Wait
        }
        else {
            Start-Process "$outFile" -ArgumentList "/quiet", "InstallAllUsers=1" -Wait
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Already installed"
    }
}
```

#### <a name="pipinstaller.ps1"></a>PipInstaller.ps1
Questo script chiama pip e installa tutte le dipendenze presenti nel file **requirements.txt**. Se la variabile di ambiente **PYTHON2** viene impostata su **on** verrà usato Python 2.7, in caso contrario verrà usato Python 3.5.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if requirements.txt exists$nl"
    if (Test-Path ..\requirements.txt) {
        Write-Output "Found. Processing pip$nl"

        if ($is_python2) {
            & "${env:SystemDrive}\Python27\python.exe" -m pip install -r ..\requirements.txt
        }
        else {
            py -m pip install -r ..\requirements.txt
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Not found$nl"
    }
}
```

#### <a name="modify-launchworker.ps1"></a>Modificare LaunchWorker.ps1
> [!NOTE]
> Nel caso di un progetto **ruolo di lavoro**, è necessario il file **LauncherWorker.ps1** per eseguire il file di avvio. In un progetto **ruolo Web** il file di avvio viene invece definito nelle proprietà del progetto.
> 
> 

Il file **bin\LaunchWorker.ps1** è stato originariamente creato per eseguire molte attività preliminari, ma non funziona. Sostituire il contenuto del file con lo script seguente.

Questo script chiama il file **worker.py** dal progetto Python. Se la variabile di ambiente **PYTHON2** viene impostata su **on** verrà usato Python 2.7, in caso contrario verrà usato Python 3.5.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated)
{
    Write-Output "Running worker.py$nl"

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
else
{
    Write-Output "Running (EMULATED) worker.py$nl"

    # Customize to your local dev environment

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
```

#### <a name="ps.cmd"></a>ps.cmd
I modelli di Visual Studio dovrebbero aver creato un file **ps.cmd** nella cartella **./bin**. Questo script della shell chiama gli script del wrapper di PowerShell precedenti e fornisce la registrazione in base al nome del wrapper di PowerShell chiamato. Se questo file non è stato creato, di seguito è indicato il contenuto necessario. 

```bat
@echo off

cd /D %~dp0

if not exist "%DiagnosticStore%\LogFiles" mkdir "%DiagnosticStore%\LogFiles"
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Unrestricted -File %* >> "%DiagnosticStore%\LogFiles\%~n1.txt" 2>> "%DiagnosticStore%\LogFiles\%~n1.err.txt"
```



## <a name="run-locally"></a>Esecuzione locale
Se si imposta il progetto servizio cloud come progetto di avvio e si preme F5, il servizio cloud verrà eseguito nell'emulatore locale di Azure.

Anche se PTVS supporta l'avvio nell'emulatore, il debug (ad esempio, punti di interruzione) non funzionerà.

Per eseguire il debug dei ruoli di lavoro e Web, è possibile impostare il progetto di ruolo come progetto di avvio e ed eseguirne il debug.  È anche possibile impostare più progetti di avvio.  Fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Imposta progetti di avvio**.

![Proprietà del progetto di avvio della soluzione](./media/cloud-services-python-ptvs/startup.png)

## <a name="publish-to-azure"></a>Pubblicazione in Azure
Per pubblicare, fare clic con il pulsante destro del mouse sul progetto servizio cloud nella soluzione e quindi scegliere **Pubblica**.

![Accesso per la pubblicazione di Microsoft Azure](./media/cloud-services-python-ptvs/publish-sign-in.png)

Seguire la procedura guidata. Se necessario, abilitare Desktop remoto. Desktop remoto è utile quando è necessario eseguire il debug di un elemento.

Dopo aver finito di configurare le impostazioni, fare clic su **Pubblica**.

Nella finestra di output verranno visualizzati alcuni stati, quindi apparirà la finestra Log attività di Microsoft Azure.

![Finestra Log attività di Microsoft Azure](./media/cloud-services-python-ptvs/publish-activity-log.png)

Saranno necessari alcuni minuti per completare la distribuzione, quindi i ruoli di lavoro e/o Web verranno eseguiti in Azure.

### <a name="investigate-logs"></a>Esaminare i log
Dopo che la macchina virtuale del servizio cloud è stata avviata e ha installato Python, è possibile esaminare i log per trovare eventuali messaggi di errore. Questi log si trovano nella cartella **C:\Resources\Directory\{role}\LogFiles**. **PrepPython.err.txt** conterrà almeno un errore relativo al tentativo dello script di verificare se Python è installato e **PipInstaller.err.txt** può segnalare una versione obsoleta di pip.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni più dettagliate sull'uso di ruoli di lavoro e Web in Python Tools per Visual Studio, vedere la documentazione PTVS:

* [Cloud Service Projects][](Progetti di servizio cloud.md)

Per altre informazioni dettagliate sull'uso di servizi di Azure dai ruoli di lavoro e Web, ad esempio sull'uso dell'archiviazione o del bus di servizio di Azure, vedere gli articoli seguenti.

* [Servizio BLOB][Servizio BLOB]
* [Servizio tabelle][Servizio tabelle]
* [Servizio di accodamento][Servizio di accodamento]
* [Code del bus di servizio][Code del bus di servizio]
* [Argomenti del bus di servizio][Argomenti del bus di servizio]

<!--Link references-->

[Informazioni sul servizio cloud]: cloud-services-choose-me.md
[execution model-web sites]: ../app-service-web/app-service-web-overview.md
[execution model-vms]: ../virtual-machines/virtual-machines-windows-about.md
[execution model-cloud services]: cloud-services-choose-me.md
[Centro per sviluppatori Python]: /develop/python/

[Servizio BLOB]: ../storage/storage-python-how-to-use-blob-storage.md
[Servizio di accodamento]: ../storage/storage-python-how-to-use-queue-storage.md
[Servizio tabelle]: ../storage/storage-python-how-to-use-table-storage.md
[Code del bus di servizio]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md
[Argomenti del bus di servizio]: ../service-bus-messaging/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python Tools per Visual Studio]: http://aka.ms/ptvs
[Documentazione di Python Tools per Visual Studio]: http://aka.ms/ptvsdocs
[Progetti servizio cloud]: http://go.microsoft.com/fwlink/?LinkId=624028
[Strumenti di Azure SDK per Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Strumenti di Azure SDK per Visual Studio 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 a 32 bit]: https://www.python.org/downloads/
[Python 3.5 a 32 bit]: https://www.python.org/downloads/



<!--HONumber=Oct16_HO2-->


