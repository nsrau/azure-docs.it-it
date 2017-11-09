---
title: Risoluzione dei problemi di installazione locale del cluster di Service Fabric | Documentazione Microsoft
description: In questo articolo viene illustrata una serie di suggerimenti per la risoluzione dei problemi del cluster di sviluppo locale
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: mikkelhegn
ms.openlocfilehash: aa393f884b564cee81fcf75cc2eff895efea9471
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Risolvere i problemi di configurazione del cluster di sviluppo locale
Se si verifica un problema durante l'interazione con il cluster di sviluppo di Service Fabric di Azure locale, esaminare i suggerimenti seguenti per possibili soluzioni.

## <a name="cluster-setup-failures"></a>Errori di configurazione del cluster
### <a name="cannot-clean-up-service-fabric-logs"></a>Impossibile pulire i log di Infrastruttura di servizi
#### <a name="problem"></a>Problema
Quando si esegue lo script DevClusterSetup, viene visualizzato un errore simile al seguente:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Soluzione
Chiudere la finestra di PowerShell corrente e aprire una nuova finestra di PowerShell come amministratore. Ora dovrebbe essere possibile eseguire correttamente lo script.

## <a name="cluster-connection-failures"></a>Errori di connessione del cluster
### <a name="service-fabric-powershell-cmdlets-are-not-recognized-in-azure-powershell"></a>Cmdlet di PowerShell di Service Fabric non sono riconosciute in Azure PowerShell
#### <a name="problem"></a>Problema
Se si tenta di eseguire cmdlet di PowerShell di Service Fabric, ad esempio `Connect-ServiceFabricCluster` in una finestra di Azure PowerShell, l'operazione non va a buon fine e si viene informati del mancato riconoscimento del cmdlet. Il motivo è che Azure PowerShell utilizza la versione a 32 bit di Windows PowerShell (anche le versioni del sistema operativo a 64 bit), mentre i cmdlet di Service Fabric funzionano solo in ambienti a 64 bit.

#### <a name="solution"></a>Soluzione
Eseguire sempre i cmdlet di Service Fabric direttamente da Windows PowerShell.

> [!NOTE]
> La versione più recente di Azure PowerShell non crea un collegamento speciale, pertanto questa situazione non dovrebbe più verificarsi.
> 
> 

### <a name="type-initialization-exception"></a>Eccezione di inizializzazione del tipo
#### <a name="problem"></a>Problema
Quando ci si connette al cluster in PowerShell, viene visualizzato l'errore TypeInitializationException per System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Soluzione
La variabile di percorso non è stata impostata correttamente durante l'installazione. Disconnettersi da Windows e accedere nuovamente. Il percorso risulterà aggiornato.

### <a name="cluster-connection-fails-with-object-is-closed"></a>La connessione del cluster ha esito negativo con il messaggio "L’oggetto è chiuso"
#### <a name="problem"></a>Problema
Una chiamata a Connect-ServiceFabricCluster ha esito negativo con un errore simile a quello indicato di seguito:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Soluzione
Chiudere la finestra di PowerShell corrente e aprire una nuova finestra di PowerShell come amministratore. Ora dovrebbe essere possibile effettuare la connessione.

### <a name="fabric-connection-denied-exception"></a>Eccezione di connessione a Fabric negata
#### <a name="problem"></a>Problema
Durante il debug da Visual Studio, si verifica un errore FabricConnectionDeniedException.

#### <a name="solution"></a>Soluzione
Questo errore si verifica in genere quando si tenta di avviare manualmente un processo host del servizio, anziché consentirne l'avvio automatico dal runtime di Service Fabric.

Assicurarsi di non disporre di progetti di servizio impostati come progetti di avvio nella soluzione. Solo i progetti di applicazione di Infrastruttura di servizi devono essere impostati come progetti di avvio.

> [!TIP]
> Se, in seguito alla configurazione, il cluster locale inizia a presentare un comportamento anomalo, può essere ripristinato con l'applicazione dell'area di notifica Local Cluster Manager. In questo modo viene rimosso il cluster esistente e ne viene configurato uno nuovo. Tenere presente che verranno rimosse anche tutte le applicazioni distribuite e i dati associati.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* [Comprendere e risolvere i problemi del cluster con report di integrità del sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualizzare il cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

