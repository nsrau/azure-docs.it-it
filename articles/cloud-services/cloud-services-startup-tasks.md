<properties 
pageTitle="Eseguire attività di avvio in Servizi cloud di Azure | Microsoft Azure" 
description="Le attività di avvio consentono di preparare l'ambiente del servizio cloud per l'app. Questo argomento illustra il funzionamento e la modalità di esecuzione delle attività di avvio" 
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
ms.date="09/06/2016" 
ms.author="adegeo"/>  



# Come configurare ed eseguire attività di avvio per un servizio cloud

È possibile usare le attività di avvio per eseguire operazioni prima dell'avvio di un ruolo. Le operazioni che si possono eseguire sono l'installazione di un componente, la registrazione dei componenti COM, l'impostazione delle chiavi del Registro di sistema o l'avvio di un processo a esecuzione prolungata.

>[AZURE.NOTE] Le attività di avvio non sono applicabili ai ruoli VM, ma solo ai ruoli Web e di lavoro del servizio cloud.

## Funzionamento delle attività di avvio

Le attività di avvio sono azioni effettuate prima dell'inizio dei ruoli e vengono definite nel file [ServiceDefinition.csdef] tramite l'elemento [Task] all'interno dell'elemento [Startup]. Spesso le attività di avvio sono file batch, ma possono essere anche applicazioni console o file batch tramite i quali si avviano script di PowerShell.

Le variabili di ambiente passano informazioni all'interno di un'attività di avvio e le risorse di archiviazione locale possono essere usate per il passaggio all'esterno di un'attività di avvio. In una variabile di ambiente può essere ad esempio specificato il percorso di un programma che si desidera installare e i file possono essere scritti nelle risorse di archiviazione locale e letti successivamente dai ruoli.

Con l'attività di avvio è possibile registrare informazioni ed errori nella directory specificata dalla variabile di ambiente **TEMP**. Durante l'attività di avvio, la variabile di ambiente **TEMP** viene risolta nella directory *C:\\Resources\\temp\\[guid].[nomeruolo]\\RoleTemp* in esecuzione nel cloud.

Le attività di avvio possono inoltre essere eseguite più volte tra un riavvio e l'altro. Ad esempio, l'attività di avvio viene eseguita a ogni riciclo del ruolo e tali ricicli non includono necessariamente un riavvio. Le attività di avvio devono essere scritte in modo da poter essere eseguite più volte senza problemi.

Le attività di avvio devono terminare con un valore di **errorlevel** (o codice di uscita) uguale a zero perché il processo di avvio possa essere completato. Se l'attività di avvio termina con un valore di **errorlevel** diverso da zero, il ruolo non verrà avviato.


## Sequenza di avvio di un ruolo

Di seguito è riportata la procedura di avvio di un ruolo in Azure:

1. L'istanza viene contrassegnata con lo stato **Avvio in corso** e non riceve traffico.

2. Tutte le attività di avvio vengono eseguite in base al relativo attributo **taskType**.
    - Le attività di tipo **simple** vengono eseguite in modo sincrono, una alla volta.
    - Le attività di tipo **background** e **foreground** vengono avviate in modo asincrono e parallelo all'attività di avvio.
       
    > [AZURE.WARNING] È possibile che IIS non sia stato configurato completamente nella fase delle attività di avvio del processo di avvio, pertanto potrebbero non essere disponibili dati specifici per il ruolo. Per le attività di avvio che richiedono dati specifici per il ruolo è necessario usare [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).

3. Viene avviato il processo host del ruolo e il sito viene creato in IIS.

4. Viene chiamato il metodo [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).

5. L'istanza viene contrassegnata con lo stato **Pronto** e il traffico viene indirizzato all'istanza.

6. Viene chiamato il metodo [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx).


## Esempio di attività di avvio

Le attività di avvio vengono definite nel file [ServiceDefinition.csdef] nell'elemento **Task**. L'attributo **commandLine** specifica il nome e i parametri del file batch o del comando di console di avvio, l'attributo **executionContext** specifica il livello di privilegio dell'attività di avvio e l'attributo **taskType** specifica la modalità di esecuzione dell'attività.

In questo esempio viene creata la variabile di ambiente **MyVersionNumber** per l'attività di avvio e il relativo valore viene impostato su "**1.0.0.0**".

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

Nell'esempio seguente il file batch **Startup.cmd** scrive la riga "The current version is 1.0.0.0" nel file StartupLog.txt nella directory specificata dalla variabile di ambiente TEMP. La riga `EXIT /B 0` assicura che l'attività di avvio termini con un argomento **errorlevel** uguale a zero.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [AZURE.NOTE] In Visual Studio la proprietà **Copia in directory di output** per il file batch di avvio deve essere impostata su **Copia sempre** per assicurarsi che il file batch di avvio venga correttamente distribuito al progetto in Azure (**approot\\bin** per i ruoli Web e **approot** per i ruoli di lavoro).

## Descrizione degli attributi di Task

Di seguito vengono descritti gli attributi dell'elemento **Task** nel file [ServiceDefinition.csdef]\:

**commandLine**: specifica la riga di comando per l'attività di avvio, come indicato di seguito.

- Il comando, con i parametri della riga di comando facoltativi, che inizia l'attività di avvio.
- Questo è spesso il nome del file batch con estensione cmd o bat.
- L'attività è relativa alla cartella AppRoot\\Bin per la distribuzione. Le variabili di ambiente non vengono espanse per determinare il percorso e il file dell'attività. Se è necessaria l'espansione dell'ambiente, è possibile creare un piccolo script con estensione cmd con il quale richiamare l'attività di avvio.
- Può essere un'applicazione console o un file batch che avvia uno [script di PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext**: specifica il livello di privilegio per l'attività di avvio. Il livello di privilegio può essere limitato o elevato:

- **limitato**: l'attività di avvio viene eseguita con gli stessi privilegi del ruolo. Quando anche l'attributo **executionContext** dell'elemento [Runtime] è **limitato**, vengono usati i privilegi utente.

- **elevato**: l'attività di avvio viene eseguita con privilegi di amministratore. In questo modo attraverso le attività di avvio è possibile installare programmi, apportare modifiche alla configurazione di IIS, effettuare modifiche al Registro di sistema e altre attività a livello di amministratore senza aumentare il livello di privilegio del ruolo.

> [AZURE.NOTE] Il livello di privilegio dell'attività di avvio non deve essere necessariamente uguale al livello di privilegio del ruolo.

**taskType**: specifica la modalità di esecuzione di un'attività di avvio.

- Le attività di tipo **simple** vengono eseguite in modo sincrono, una alla volta, nell'ordine specificato nel file [ServiceDefinition.csdef]. Quando un'attività di avvio **simple** termina con un valore di **errorlevel** uguale a zero, viene eseguita l'attività di avvio **simple** successiva. Se non sono presenti altre attività di avvio **simple** da eseguire, viene avviato il ruolo.

    > [AZURE.NOTE] Se l'attività **simple** termina con un valore di **errorlevel** diverso da zero, l'istanza viene bloccata. Le successive attività di avvio **simple** e il ruolo non vengono avviati.

    Per assicurarsi che il file batch termini con un valore di **errorlevel** uguale a zero, eseguire il comando `EXIT /B 0` al termine del processo del file batch.

- Le attività di tipo **background** vengono eseguite in modo asincrono, in parallelo con l'avvio del ruolo.

- Le attività di tipo **foreground** vengono eseguite in modo asincrono, in parallelo con l'avvio del ruolo. La differenza principale tra un'attività **foreground** e un'attività **background** è che l'attività **foreground** impedisce il riciclo o l'arresto del ruolo fino al termine dell'attività. Le attività **background** non prevedono questa restrizione.

## Variabili di ambiente

Le variabili di ambiente costituiscono un modo per passare le informazioni a un'attività di avvio. È ad esempio possibile inserire il percorso di un BLOB contenente un programma da installare, i numeri di porta che verranno usati dal ruolo o impostazioni per controllare le funzionalità dell'attività di avvio.

Esistono due tipi di variabili di ambiente per le attività di avvio: le variabili di ambiente statiche e le variabili di ambiente basate sui membri della classe [RoleEnvironment]. Entrambi si trovano nella sezione [Environment] del file [ServiceDefinition.csdef] e per entrambi vengono usati l'elemento [Variable] e l'attributo **name**.

Per le variabili di ambiente statiche viene usato l'attributo **value** dell'elemento [Variable]. Nell'esempio precedente viene creata la variabile di ambiente **MyVersionNumber** con un valore statico di "**1.0.0.0**". Un altro esempio potrebbe essere la creazione di una variabile di ambiente **StagingOrProduction** per la quale impostare manualmente i valori "**staging**" o "**production**" per eseguire azioni di avvio diverse in base al valore della variabile di ambiente **StagingOrProduction**.

Per le variabili di ambiente basate sui membri della classe RoleEnvironment non viene usato l'attributo **value** dell'elemento [Variable]. Viene invece usato l'elemento figlio [RoleInstanceValue], con l'appropriato valore per l'attributo **xPath**, per creare una variabile di ambiente basata su un membro specifico della classe [RoleEnvironment]. I valori per l'attributo **xPath** per accedere ai diversi valori di [RoleEnvironment] sono disponibili [qui](cloud-services-role-config-xpath.md).



Ad esempio, per creare una variabile di ambiente che sia "**true**" se l'istanza è in esecuzione nell'emulatore di calcolo e "**false**" se è in esecuzione nel cloud, usare gli elementi [Variable] e [RoleInstanceValue] seguenti:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
    
            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->
    
            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>
    
        </Environment>
    </Task>
</Startup>
```

## Passaggi successivi
Informazioni su come eseguire alcune [attività di avvio comuni](cloud-services-startup-tasks-common.md) con il servizio cloud.

[Creare il pacchetto](cloud-services-model-and-package.md) del servizio cloud.


[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Task]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Environment]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx

<!---HONumber=AcomDC_0914_2016-->