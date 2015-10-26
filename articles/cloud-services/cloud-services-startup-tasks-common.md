<properties 
pageTitle="Attività di avvio comuni per Servizi cloud | Microsoft Azure" 
description="Questo articolo fornisce alcuni esempi delle attività di avvio comuni che è possibile eseguire nel ruolo Web o di lavoro dei servizi cloud." 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="08/24/2015" 
ms.author="adegeo"/>

# Attività di avvio comuni del servizio cloud

Questo articolo fornisce alcuni esempi relativi alle attività di avvio comuni che è possibile eseguire nel servizio cloud. È possibile usare le attività di avvio per eseguire operazioni prima dell'avvio di un ruolo. Le operazioni che si possono eseguire sono l'installazione di un componente, la registrazione dei componenti COM, l'impostazione delle chiavi del Registro di sistema o l'avvio di un processo a esecuzione prolungata.

Per comprendere il funzionamento delle attività di avvio e in particolare la modalità di creazione delle voci che definiscono un'attività di avvio, vedere [questo articolo](cloud-services-startup-tasks.md).

Molte di queste attività usano

>[AZURE.NOTE]Le attività di avvio non sono applicabili ai ruoli VM, ma solo ai ruoli Web e di lavoro del servizio cloud.


## Definire le variabili di ambiente prima dell'avvio di un ruolo

È possibile definire le variabili di ambiente per un intero ruolo aggiungendo l'elemento [Runtime] alla definizione del ruolo nel file csdef.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Runtime>
            <Environment>
                <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
            </Environment>
        </Runtime>
    </WebRole>
</ServiceDefinition>
```

Se è necessario le definire variabili di ambiente per un'attività specifica non condivisa da altre attività, è possibile usare l'elemento [Environment] all'interno dell'elemento [Task].

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Le variabili possono inoltre usare un [valore XPath di Azure valido](https://msdn.microsoft.com/library/azure/hh404006.aspx) per fare riferimento a un elemento della distribuzione. Anziché usare l'attributo `value`, definire un elemento figlio [RoleInstanceValue].

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## Configurare l'avvio IIS con AppCmd.exe

Lo strumento da riga di comando [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) può essere usato per gestire le impostazioni IIS all'avvio in Azure. *AppCmd.exe* offre un comodo accesso da riga di comando alle impostazioni di configurazione da usare nelle attività di avvio in Azure. Tramite *AppCmd.exe* è possibile aggiungere, modificare o rimuovere impostazioni per applicazioni e siti Web.

È necessario tuttavia tenere conto di alcuni aspetti se si usa *AppCmd.exe* come attività di avvio:

- Le attività di avvio possono essere eseguite più di una volta tra un riavvio e l'altro. Questo può accadere, ad esempio, se il ruolo viene riciclato.
- Alcune azioni di *AppCmd.exe* possono generare errori se vengono eseguite più di una volta. Il tentativo di aggiungere due volte una sezione a *Web.config* potrebbe generare un errore.
- Le attività di avvio danno esito negativo se restituiscono un codice di uscita o un valore di **errorlevel** diverso da zero. Questa situazione può verificarsi quando *AppCmd.exe* genera un errore.

Per i motivi elencati, è spesso consigliabile controllare il valore di **errorlevel** dopo la chiamata di *AppCmd.exe*, operazione semplice se si esegue il wrapping della chiamata a *AppCmd.exe* con un file con estensione *cmd*. Se si rileva un valore di **errorlevel** noto, è possibile ignorarlo oppure restituirlo. Questa situazione è illustrata nell'esempio riportato di seguito.

I valori di errorlevel restituiti da *AppCmd.exe* sono elencati nel file winerror.h e possono essere visualizzati anche in [MSDN](https://msdn.microsoft.com/library/windows/desktop/ms681382.aspx).

### Esempio

In questo esempio vengono aggiunte una sezione di compressione e una voce di compressione per JSON al file *Web.config*, con gestione e registrazione degli errori.

Le sezioni pertinenti del file [ServiceDefinition.csdef] sono riportate di seguito, evidenziando l'impostazione dell'attributo [executionContext](https://msdn.microsoft.com/library/azure/gg557552.aspx#Task) su `elevated` per fornire ad *AppCmd.exe* le autorizzazioni sufficienti per modificare le impostazioni nel file *Web.config*:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Nel file batch Startup.cmd viene usato AppCmd.exe per aggiungere una sezione di compressione e una voce di compressione per JSON al file *Web.config*. Il valore di **errorlevel** previsto di 183 viene impostato su zero tramite il programma da riga di comando VERIFY.EXE. I valori di errorlevel imprevisti vengono registrati in StartupErrorLog.txt.

    REM   *** Add a compression section to the Web.config file. ***
    %windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
    REM   batch file were executed twice. This can occur and must be accounted for in a Azure startup
    REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
    REM   command will safely set the ERRORLEVEL to zero.
    IF %ERRORLEVEL% EQU 183 DO VERIFY > NUL
    
    REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
    IF %ERRORLEVEL% NEQ 0 (
        ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
        GOTO ErrorExit
    )
    
    REM   *** Add compression for json. ***
    %windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
    IF %ERRORLEVEL% EQU 183 VERIFY > NUL
    IF %ERRORLEVEL% NEQ 0 (
        ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
        GOTO ErrorExit
    )
    
    REM   *** Exit batch file. ***
    EXIT /b 0
    
    REM   *** Log error and exit ***
    :ErrorExit
    REM   Report the date, time, and ERRORLEVEL of the error.
    DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
    TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
    EXIT %ERRORLEVEL%


## Aggiungere regole di firewall

In Azure sono disponibili due firewall. Il primo controlla le connessioni tra la macchina virtuale e il mondo esterno. Questa funzionalità è controllata dall'elemento [EndPoints] nel file [ServiceDefinition.csdef].

Il secondo controlla le connessioni tra la macchina virtuale e i processi al suo interno. Questa funzionalità è controllata dallo strumento da riga di comando `netsh advfirewall firewall` ed è l'oggetto di questo articolo.

In Azure vengono create regole di firewall per i processi avviati nei ruoli. Quando si avvia un servizio o un programma, ad esempio, in Azure vengono create automaticamente le regole di firewall necessarie per consentire la comunicazione del servizio con Internet. Tuttavia, se si crea un servizio che viene avviato da un processo esterno al ruolo (ad esempio un servizio COM+ o un programma avviato con l'utilità di pianificazione di Windows), è necessario creare manualmente una regola di firewall per consentire l'accesso al servizio. Queste regole di firewall possono essere create usando un'attività di avvio.

In un'attività di avvio che crea una regola di firewall l'attributo [executionContext][Task] deve essere impostato su **elevato**. Aggiungere l'attività di avvio seguente per il file [ServiceDefinition.csdef].

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Per aggiungere la regola di firewall, è necessario usare i comandi `netsh advfirewall firewall` appropriati nel file batch di avvio. In questo esempio l'attività di avvio richiede la sicurezza e la crittografia per la porta TCP 80.

    REM   Add a firewall rule in a startup task.
    
    REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
    netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%


## Bloccare un indirizzo IP specifico

È possibile limitare l'accesso a un ruolo Web Azure a un set di indirizzi IP specificati modificando il file **web.config** di IIS e creando un file di comando con il quale sbloccare la sezione **ipSecurity** del file **ApplicationHost.config**.

Creare innanzitutto un file di comando da eseguire all'avvio del ruolo per lo sblocco della sezione **ipSecurity** del file **ApplicationHost.config**. Creare una nuova cartella al livello radice del ruolo Web denominata **startup** e crearvi un file batch denominato **startup.cmd**. Impostare le proprietà del file su **Copia sempre** per assicurarsi che venga distribuito.

Aggiungere l'attività di avvio seguente al file [ServiceDefinition.csdef].

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Aggiungere questo comando al file **startup.cmd**:

    %windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity

In questo modo il file batch **startup.cmd** viene eseguito ogni volta che il ruolo Web viene inizializzato, assicurando lo sblocco della sezione **ipSecurity**.

Modificare infine la sezione [system.webServer section](http://www.iis.net/configreference/system.webserver/security/ipsecurity#005) del file **web.config** del ruolo Web in modo da aggiungere un elenco di indirizzi IP a cui viene concesso l'accesso, come illustrato nell'esempio seguente:

Questa configurazione di esempio **consente** l'accesso al server a tutti gli indirizzi IP, a eccezione dei due IP definiti

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity>
        <!--The following IP addresses are granted access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Questa configurazione di esempio **nega** l'accesso al server a tutti gli indirizzi IP, a eccezione dei due IP definiti.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## Creare un'attività di avvio di PowerShell

Gli script di Windows PowerShell non possono essere chiamati direttamente dal file [ServiceDefinition.csdef], ma possono essere richiamati da un file batch di avvio.

Per impostazione predefinita, in PowerShell non vengono eseguiti script non firmati. Se non si firmano gli script, Windows PowerShell deve essere configurato per eseguire script non firmati. Per eseguire script non firmati, **ExecutionPolicy** deve essere impostato su **Unrestricted**. L'impostazione di **ExecutionPolicy** da usare dipende dalla versione di Windows PowerShell.

    REM   Run an unsigned PowerShell script and log the output
    PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
        
    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%


Se si usa un sistema operativo Guest che esegue PowerShell 2.0 o 1.0, è possibile forzare l'esecuzione della versione 2 e, se non è disponibile, usare la versione 1.

    REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
    PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

    REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
    IF %ERRORLEVEL% EQU -393216 (

       PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
       PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
    )

    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%

## Creare file nell'archiviazione locale da un'attività di avvio

È possibile usare una risorsa di archiviazione locale per archiviare i file creati dall'attività di avvio a cui l'applicazione accederà in seguito.

Per creare la risorsa di archiviazione locale, aggiungere una sezione [LocalResources] al file [ServiceDefinition.csdef] e quindi aggiungere l'elemento figlio [LocalStorage]. Assegnare alla risorsa di archiviazione locale un nome univoco e una dimensione appropriata per l'attività di avvio.

Per usare una risorsa di archiviazione locale nell'attività di avvio, è necessario creare una variabile di ambiente che faccia riferimento al percorso della risorsa di archiviazione locale. In questo modo l'attività di avvio e l'applicazione saranno in grado di leggere e scrivere i file nella risorsa di archiviazione locale.

Le sezioni pertinenti del file **ServiceDefinition.csdef** sono riportate di seguito:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        
        <LocalResources>
          <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
        </LocalResources>
        
        ...
        
        <Runtime>
            <Environment>
                <Variable name="PathToStartupStorage">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
                </Variable>
            </Environment>
        </Runtime>
        
        ...
        
        <Startup>
          <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Nel file batch **Startup.cmd** file batch viene usata la variabile di ambiente **PathToStartupStorage** per creare il file **MyTest.txt** nel percorso di archiviazione locale.

    REM   Create a simple text file.

    ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
    ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
    ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
    ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

    REM   Exit the batch file with ERRORLEVEL 0.

    EXIT /b 0

È possibile accedere all'archiviazione locale da Azure SDK usando il metodo [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx). Tramite operazioni standard di lettura e scrittura dei file il contenuto della risorsa di archiviazione locale verrà quindi letto e scritto.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTest.txt"));
```


## Distinzione tra l'esecuzione nell'emulatore e nel cloud

È possibile impostare l'attività di avvio in modo che esegua passaggi diversi a seconda che venga usata nel cloud o nell'emulatore di calcolo. È ad esempio possibile decidere di usare una copia aggiornata dei dati SQL solo quando l'esecuzione avviene nell'emulatore. In alternativa, è possibile eseguire dei passaggi di ottimizzazione delle prestazioni nel cloud che non sono necessari quando l'esecuzione avviene nell'emulatore.

Per eseguire nell'emulatore di calcolo azioni diverse da quelle eseguite nel cloud, è necessario creare una variabile di ambiente nel file [ServiceDefinition.csdef] e testare la variabile nell'attività di avvio.

Per creare la variabile di ambiente, aggiungere l'elemento [Variable]/[RoleInstanceValue] e creare un valore XPath di `/RoleEnvironment/Deployment/@emulated`. Il valore della variabile di ambiente **% ComputeEmulatorRunning %** sarà `"true"` durante l'esecuzione nell'emulatore di calcolo e `"false"` durante l'esecuzione nel cloud.


```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">

        ...
        
        <Runtime>
            <Environment>
                <Variable name="ComputeEmulatorRunning">
                    <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
                </Variable>
            </Environment>
        </Runtime>

    </WebRole>
</ServiceDefinition>
```

È ora possibile usare la variabile di ambiente **ComputeEmulatorRunning %** in qualsiasi esecuzione di attività per eseguire azioni diverse a seconda che il ruolo sia in esecuzione nel cloud o nell'emulatore. Di seguito è riportato uno script shell con estensione cmd che controlla la variabile di ambiente.

    REM   Check if this task is running on the compute emulator.

    IF "%ComputeEmulatorRunning%" == "true" (
        REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
        
    ) ELSE (
        REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
        
    )


## Rilevare se l'attività è già stata eseguita

Il ruolo può essere riciclato senza riavvio, causando una nuova esecuzione dell'attività di avvio. Il flag indica che un'attività è già stata eseguita nella macchina virtuale di hosting. Per alcune attività non è importante se l'esecuzione avviene più volte. Potrebbe tuttavia verificarsi una situazione in cui è necessario impedire l'esecuzione ripetuta di un'attività.

Il modo più semplice per rilevare se un'attività è già stata eseguita consiste nel creare un file nella cartella **%TEMP%** quando l'attività ha esito positivo e cercarlo all'inizio dell'attività. Di seguito è riportato uno script shell con estensione cmd di esempio che esegue automaticamente questa operazione.

    REM   If Task1_Success.txt exists, then Application 1 is already installed.
    IF EXIST "%RoleRoot%\Task1_Success.txt" (
      ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
      GOTO Finish
    )

    REM   Run your real exe task
    ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

    IF %ERRORLEVEL% EQU 0 (
      REM   The application installed without error. Create a file to indicate that the task
      REM   does not need to be run again.

      ECHO This line will create a file to indicate that Application 1 installed correctly. > "%RoleRoot%\Task1_Success.txt"
      
    ) ELSE (
      REM   An error occurred. Log the error and exit with the error code.

      DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
      TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
      ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

      EXIT %ERRORLEVEL%
    )

    :Finish

    REM   Exit normally.
    EXIT /B 0


## Procedure consigliate per l'attività
Di seguito sono riportate alcune procedure consigliate da seguire durante la configurazione dell'attività per il ruolo Web o di lavoro.

### Registrare sempre le attività di avvio

In Visual Studio non è previsto un debugger per analizzare i file batch, pertanto è buona pratica ottenere quanti più dati possibile sul funzionamento di tali file. La registrazione dell'output dei file batch, sia **stdout** sia **stderr**, può fornire informazioni importanti quando si tenta di eseguire il debug e correggere i file batch. Per registrare sia **stdout** sia **stderr** nel file StartupLog.txt nella directory a cui fa riferimento la variabile di ambiente **%TEMP%**, aggiungere il testo `>>  "%TEMP%\\StartupLog.txt" 2>&1` alla fine delle righe specifiche che si desidera registrare. Ad esempio, per eseguire setup.exe nella directory **%PathToApp1Install%**:

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Se si desidera registrare tutto l'output dell'attività di avvio senza aggiungere `>> "%TEMP%\StartupLog.txt" 2>&1` alla fine di ogni riga, sono necessari due file batch di avvio. Il primo file batch chiama il secondo con reindirizzamento per registrare tutte le attività del secondo file batch. Tale operazione è necessaria affinché il reindirizzamento sia corretto.

Di seguito viene illustrato come reindirizzare tutto l'output di un file batch di avvio. In questo esempio il file ServerDefinition.csdef crea un'attività di avvio che chiama Startup1.cmd. Startup1.cmd chiama Startup2.cmd, reindirizzando tutto l'output a %TEMP%\\StartupLog.txt.

ServiceDefinition.cmd:

```xml
<Startup>
    <Task commandLine="Startup1.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

Startup1.cmd:

    REM   Startup1.cmd calls the main startup batch file, Startup2.cmd, redirecting all output
    REM   to the StartupLog.txt log file.

    REM   Log the startup date and time.
    ECHO Startup1.cmd: >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO Current date and time: >> "%TEMP%\StartupLog.txt" 2>&1
    DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
    TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO Starting up Startup2.cmd. >> "%TEMP%\StartupLog.txt" 2>&1

    REM   Call the Startup2.cmd batch file, redirecting all output to the StartupLog.txt log file.
    START /B /WAIT Startup2.cmd >> "%TEMP%\StartupLog.txt" 2>&1

    REM   Log the completion of Startup1.cmd.
    ECHO Returned to Startup1.cmd. >> "%TEMP%\StartupLog.txt" 2>&1

    IF ERRORLEVEL EQU 0 (
       REM   No errors occurred. Exit Startup1.cmd normally.
       EXIT /B 0
    ) ELSE (
       REM   Log the error.
       ECHO An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
       EXIT %ERRORLEVEL%
    )

Startup2.cmd:

    REM   This is the batch file where the startup steps should be performed. Because of the
    REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
    REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

    REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
    REM   calling batch file.
    EXIT /B %ERRORLEVEL%

### Impostare l'attributo executionContext in modo appropriato per le attività di avvio

Impostare i privilegi in modo appropriato per l'attività di avvio. In alcuni casi le attività di avvio devono essere eseguite con privilegi elevati anche se il ruolo viene eseguito con privilegi normali.

L'attributo [executionContext][Task] imposta il livello di privilegio dell'attività di avvio. L'uso di `executionContext="limited"` indica che l'attività di avvio disporrà dello stesso livello di privilegio del ruolo. L'uso di `executionContext="elevated"` indica che l'attività di avvio disporrà di privilegi di amministratore, cioè potrà eseguire attività amministrative, senza fornire privilegi di amministratore al ruolo.

Un esempio di attività di avvio che richiede privilegi elevati è un'attività di avvio che usa **AppCmd.exe** per configurare IIS. **AppCmd.exe** richiede `executionContext="elevated"`.

### Usare l'attributo taskType appropriato

L'attributo [taskType][Task] determina la modalità di esecuzione dell'attività di avvio. Sono disponibili tre valori: **simple**, **background** e **foreground**. Le attività in background e in primo piano (foreground) vengono avviate in modo asincrono e le attività semplici (simple) vengono eseguite in modo sincrono una alla volta.

Con le attività di avvio **simple** è possibile impostare l'ordine in cui le attività si verificano in base all'ordine in cui le attività sono elencate nel file ServiceDefinition.csdef. Se un'attività **simple** termina con un codice di uscita diverso da zero, la procedura di avvio verrà arrestata e il ruolo non verrà avviato.

La differenza tra le attività di avvio **background** e **foreground** è che le attività **foreground** mantengono il ruolo in esecuzione fino alla fine dell'attività stessa. Questo significa anche che se l'attività **foreground** si blocca o viene arrestata in modo anomalo, il ruolo non verrà riciclato fino alla chiusura forzata dell'attività **foreground**. Per questo motivo, il tipo **background** è consigliato per le attività di avvio asincrono, a meno che non siano necessarie le funzionalità dell'attività di tipo **foreground**.

### Terminare i file batch con EXIT /B 0

Il ruolo verrà avviato solo se il valore di **errorlevel** di ognuna delle attività di avvio semplici è uguale a zero. Non in tutti i programmi il valore di **errorlevel** (codice di uscita) viene impostato correttamente, per cui se l'esecuzione è avvenuta correttamente il file batch deve terminare con un comando `EXIT /B 0`.

L'assenza di `EXIT /B 0` alla fine di un file batch di avvio è una causa comune del mancato avvio dei ruoli.

### Prevedere la ripetuta esecuzione delle attività di avvio

Non tutti i ricicli dei ruoli includono un riavvio, ma tutti includono l'esecuzione di tutte le attività di avvio. Ciò significa che deve essere possibile eseguire le attività di avvio più volte tra un riavvio e l'altro senza problemi. Questo aspetto è stato illustrato [in precedenza](#detect-that-your-task-has-already-run).

### Usare le risorse di archiviazione locale per archiviare i file che dovranno essere accessibili nel ruolo

Se si desidera copiare o creare un file durante l'attività di avvio che sia quindi accessibile al ruolo, tale file deve essere posizionato nella risorsa di archiviazione locale. Vedere la [sezione](#create-files-in-local-storage-from-a-startup-task) precedente.

## Passaggi successivi

Verificare il [pacchetto e il modello del servizio](cloud-services-model-and-package.md) cloud

Altre informazioni sul funzionamento delle [attività](cloud-services-startup-tasks.md).

[Creare e distribuire](cloud-services-how-to-create-deploy-portal.md) il pacchetto del servizio cloud.


[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Task]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Runtime]: https://msdn.microsoft.com/it-IT/library/azure/gg557552.aspx#Runtime
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Environment]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Endpoints]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue

<!---HONumber=Oct15_HO3-->