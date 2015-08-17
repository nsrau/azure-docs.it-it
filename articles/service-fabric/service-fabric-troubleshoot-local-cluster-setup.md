<properties
   pageTitle="Risolvere i problemi di configurazione del cluster locale"
   description="In questo articolo viene illustrata una serie di suggerimenti per la risoluzione dei problemi del cluster di sviluppo locale"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/09/2015"
   ms.author="seanmck"/>

# Risolvere i problemi di configurazione del cluster di sviluppo locale

Se si verifica un problema durante l'interazione con il cluster di sviluppo locale, esaminare i suggerimenti seguenti per possibili soluzioni.

## Errori di configurazione del cluster

### Impossibile pulire i log di Infrastruttura di servizi

#### Problema

Quando si esegue lo script DevClusterSetup, viene visualizzato un errore simile al seguente:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### Soluzione

Chiudere la finestra di Powershell corrente e avviare una nuova finestra di Powershell come amministratore. Ora dovrebbe essere possibile eseguire correttamente lo script.

## Errori di connessione del cluster

### Eccezione di inizializzazione del tipo

#### Problema

Quando ci si connette al cluster in PowerShell o in Esplora infrastruttura di servizi, viene visualizzata un’eccezione TypeInitializationException per System.Fabric.Common.AppTrace.

#### Soluzione

La variabile di percorso non è stata impostata correttamente durante l'installazione. Effettuare la disconnessione da Windows e accedere di nuovo. In tal modo il percorso verrà aggiornato.

### La connessione del cluster ha esito negativo con il messaggio "L’oggetto è chiuso"

#### Problema

Una chiamata a Connect-ServiceFabricCluster ha esito negativo con un errore simile a quello indicato di seguito:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### Soluzione

Chiudere la finestra di Powershell corrente e avviare una nuova finestra di Powershell come amministratore. Ora dovrebbe essere possibile effettuare la connessione.

### FabricConnectionDeniedException

#### Problema

Durante il debug da Visual Studio, è possibile che si verifichi un’eccezione FabricConnectionDeniedException.

#### Soluzione

Questo errore si verifica in genere quando si tenta di avviare manualmente un processo host del servizio invece di consentirne l’avvio automatico dal runtime di Infrastruttura di servizi.

Assicurarsi di non disporre di progetti di servizio impostati come progetti di avvio nella soluzione. Solo i progetti di applicazione di Infrastruttura di servizi devono essere impostati come progetti di avvio.


## Passaggi successivi

- [Comprendere e risolvere i problemi del cluster con report di integrità del sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Visualizzazione del cluster con Esplora Infrastruttura di servizi](service-fabric-visualizing-your-cluster.md)

<!---HONumber=August15_HO6-->