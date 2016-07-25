<properties
   pageTitle="Risoluzione dei problemi di installazione locale del cluster di infrastruttura dei servizi | Microsoft Azure"
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
   ms.date="07/08/2016"
   ms.author="seanmck"/>

# Risolvere i problemi di configurazione del cluster di sviluppo locale

Se si verifica un problema durante l'interazione con il cluster di sviluppo di Service Fabric di Azure locale, esaminare i suggerimenti seguenti per possibili soluzioni.

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

Chiudere la finestra di PowerShell corrente e aprire una nuova finestra di PowerShell come amministratore. Ora dovrebbe essere possibile eseguire correttamente lo script.

## Errori di connessione del cluster

### Cmdlet di PowerShell di Service Fabric non sono riconosciute in Azure PowerShell

#### Problema

Se si tenta di eseguire cmdlet di PowerShell di Service Fabric, ad esempio `Connect-ServiceFabricCluster` in una finestra di Azure PowerShell, l'operazione non va a buon fine e si viene informati del mancato riconoscimento del cmdlet. Il motivo è che Azure PowerShell utilizza la versione a 32 bit di Windows PowerShell (anche le versioni del sistema operativo a 64 bit), mentre i cmdlet di Service Fabric funzionano solo in ambienti a 64 bit.

#### Soluzione

Eseguire sempre i cmdlet di Service Fabric direttamente da Windows PowerShell.

>[AZURE.NOTE] La versione più recente di Azure PowerShell non crea un collegamento speciale, pertanto questa situazione non dovrebbe più verificarsi.

### Eccezione di inizializzazione del tipo

#### Problema

Quando ci si connette al cluster in PowerShell, viene visualizzato l'errore TypeInitializationException per System.Fabric.Common.AppTrace.

#### Soluzione

La variabile di percorso non è stata impostata correttamente durante l'installazione. Disconnettersi da Windows e accedere nuovamente. In tal modo il percorso verrà aggiornato.

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

Chiudere la finestra di PowerShell corrente e aprire una nuova finestra di PowerShell come amministratore. Ora dovrebbe essere possibile effettuare la connessione.

### Eccezione di connessione a Fabric negata

#### Problema

Durante il debug da Visual Studio, si verifica un errore FabricConnectionDeniedException.

#### Soluzione

Questo errore si verifica in genere quando si tenta di avviare manualmente un processo host del servizio invece di consentirne l’avvio automatico dal runtime di Infrastruttura di servizi.

Assicurarsi di non disporre di progetti di servizio impostati come progetti di avvio nella soluzione. Solo i progetti di applicazione di Infrastruttura di servizi devono essere impostati come progetti di avvio.

>[AZURE.TIP] Se, in seguito alla configurazione, il cluster locale inizia a presentare un comportamento anomalo, può essere ripristinato con l'applicazione dell'area di notifica Local Cluster Manager. In questo modo verrà rimosso il cluster esistente e ne verrà configurato uno nuovo. Si noti che tutte le applicazioni distribuite e i dati associati verranno rimossi.

## Passaggi successivi

- [Comprendere e risolvere i problemi del cluster con report di integrità del sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Visualizzare il cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

<!---HONumber=AcomDC_0713_2016-->