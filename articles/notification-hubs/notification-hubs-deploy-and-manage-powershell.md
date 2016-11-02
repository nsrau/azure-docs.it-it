<properties 
    pageTitle="Distribuire e gestire hub di notifica tramite PowerShell" 
    description="Come creare e gestire Hub di notifica utilizzando PowerShell per l'automazione" 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="powershell" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>


# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Distribuire e gestire hub di notifica tramite PowerShell

##<a name="overview"></a>Panoramica

In questo articolo viene illustrato come utilizzare Creazione e gestione di hub di notifica di Azure tramite PowerShell. Le seguenti attività comuni di automazione sono illustrate in questo argomento.

+ Creare un hub di notifica
+ Impostare le credenziali

Se si desidera anche creare un nuovo spazio dei nomi per l'hub di notifica, vedere [Gestire il bus di servizio con PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

La gestione degli hub di notifica non è supportata direttamente tramite i cmdlet inclusi con Azure PowerShell. L'approccio migliore da PowerShell consiste nel fare riferimento all'assembly Microsoft.Azure.NotificationHubs.dll. L'assembly viene distribuito con il [pacchetto NuGet degli hub di notifica di Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).


## <a name="prerequisites"></a>Prerequisiti

Per eseguire le procedure descritte nell'articolo è necessario:

- Una sottoscrizione di Azure. Azure è una piattaforma basata su sottoscrizione. Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto], [Offerte per i membri] oppure [Versione di valutazione gratuita].

- Un computer con Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell].

- Conoscenza generale degli script di PowerShell, dei pacchetti NuGet e di .NET Framework.


## <a name="including-a-reference-to-the-.net-assembly-for-service-bus"></a>Inclusione di un riferimento all'assembly .NET per il bus di servizio

La gestione degli hub di notifica di Azure non è ancora inclusa con i cmdlet PowerShell di Azure PowerShell. Per eseguire il provisioning degli hub di notifica, è possibile utilizzare il client .NET nel [pacchetto NuGet degli hub di notifica di Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Innanzitutto, assicurarsi che lo script sia in grado di individuare l'assembly **Microsoft.Azure.NotificationHubs.dll** , che viene installato con il pacchetto NuGet in un progetto Visual Studio. Per essere flessibile, lo script esegue i passaggi seguenti:

1. Determina il percorso in cui è stato richiamato.
2. Scorre il percorso finché trova una cartella denominata `packages`. Questa cartella viene creata durante l'installazione dei pacchetti NuGet per Visual Studio.
3. Cerca in modo ricorsivo nella cartella `packages` , per trovare un assembly denominato **Microsoft.Azure.NotificationHubs.dll**.
4. Fa quindi riferimento all'assembly in modo che i tipi siano disponibili per un uso successivo.

Di seguito viene illustrato in che modo questi passaggi vengono implementati in uno script di PowerShell:

``` powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>Creare la classe NamespaceManager

Per eseguire il provisioning degli hub di notifica creare un'istanza della classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) dall’SDK. 

È possibile usare il cmdlet [Get-AzureSBAuthorizationRule] incluso con Azure PowerShell per recuperare una regola di autorizzazione usata per fornire una stringa di connessione. Verrà archiviato un riferimento all'istanza di `NamespaceManager` nella variabile `$NamespaceManager`. Si utilizzerà `$NamespaceManager` per eseguire il provisioning di un hub di notifica.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## <a name="provisioning-a-new-notification-hub"></a>Provisioning di un nuovo hub di notifica 

Per eseguire il provisioning di un nuovo hub di notifica, utilizzare l’ [API .NET per Hub di notifica].

In questa parte dello script verranno impostate quattro variabili locali. 

1. `$Namespace` : impostare sul nome dello spazio dei nomi in cui si desidera creare un hub di notifica.
2. `$Path` : impostare il percorso sul nome del nuovo hub di notifica.  Ad esempio, "MyHub".    
3. `$WnsPackageSid` : impostare sul SID di pacchetto per l’applicazione Windows dal [Windows Dev Center](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).
4. `$WnsSecretkey`: impostare sulla chiave privata per l’applicazione Windows dal [Windows Dev Center](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).

Queste variabili vengono utilizzate per connettersi allo spazio dei nomi e creare un nuovo Hub di notifica configurato per gestire le notifiche Windows Notification Services (WNS) con credenziali WNS per un’applicazione Windows. Per informazioni su come ottenere il SID di pacchetto e la chiave privata, vedere l’esercitazione [Introduzione agli Hub di notifica](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) . 

+ Il frammento di script utilizza l’oggetto `NamespaceManager` da controllare per verificare se l'Hub di notifica è identificato da `$Path` esiste.

+ Se non esiste, lo script creerà un `NotificationHubDescription` con credenziali WNS e lo passerà alla classe `NamespaceManager`, metodo `CreateNotificationHub`.

``` powershell

$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```




## <a name="additional-resources"></a>Risorse aggiuntive

- [Gestire il bus di servizio con PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [Come creare code, argomenti e sottoscrizioni del bus di servizio tramite uno script di PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Come creare uno spazio dei nomi del bus di servizio e un hub eventi tramite uno script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Sono disponibili per il download anche alcuni script predefiniti:
- [Script PowerShell del bus di servizio](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)
 

[Opzioni di acquisto]: http://azure.microsoft.com/pricing/purchase-options/
[Offerte per i membri]: http://azure.microsoft.com/pricing/member-offers/
[versione di prova gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Come installare e configurare Azure PowerShell]: ../powershell-install-configure.md
[API .NET per Hub di notifica]: https://msdn.microsoft.com/library/azure/mt414893.aspx
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
 



<!--HONumber=Oct16_HO2-->


