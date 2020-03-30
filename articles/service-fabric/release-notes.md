---
title: Versioni di Azure Service FabricAzure Service Fabric releases
description: Note sulla versione per Azure Service Fabric. Include informazioni sulle funzionalità e sui miglioramenti più recenti di Service Fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: cb4fdd56e9cf67c71ac690d423499929167f8977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064242"
---
# <a name="service-fabric-releases"></a>Versioni di Service Fabric

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Risoluzione dei problemi relativi alle guide alle opzioni</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Code Samples</a> di supporto per il<a href="https://github.com/Azure/service-fabric-issues" target="blank">rilevamento dei</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">problemi- nelle</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">versioni</a> 
| supportate

In questo articolo vengono fornite ulteriori informazioni sulle versioni e gli aggiornamenti più recenti per il runtime di Service Fabric e SDK.

## <a name="whats-new-in-service-fabric"></a>Novità di Service Fabric

### <a name="service-fabric-70"></a>Service Fabric 7.0

Azure Service Fabric 7.0 è ora disponibile. Sarà possibile eseguire l'aggiornamento alla versione 7.0 tramite il portale di Azure o tramite una distribuzione di Azure Resource Manager.You will be able to update to 7.0 through the Azure portal or via an Azure Resource Manager deployment. A causa del feedback dei clienti sulle versioni intorno al periodo di vacanza, non inizieremo ad aggiornare automaticamente i cluster impostati per ricevere aggiornamenti automatici fino a gennaio.
A gennaio, riprenderemo la procedura di implementazione standard e i cluster con gli aggiornamenti automatici abilitati inizieranno a ricevere automaticamente l'aggiornamento 7.0. Forniremo un altro annuncio prima dell'inizio del lancio.
Aggiorneremo anche le date di rilascio pianificate per indicare che prendiamo in considerazione questa politica. Cerca qui gli aggiornamenti sui nostri futuri programmi di [rilascio](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule).
 
Questa è l'ultima versione di Service Fabric e viene caricata con funzionalità e miglioramenti chiave.

### <a name="key-announcements"></a>Annunci chiave
 - [**Supporto KeyVaultReference per i segreti dell'applicazione (anteprima)**](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references): le applicazioni di Service Fabric che hanno abilitato [Managed Identities](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) possono ora fare riferimento direttamente a un URL segreto dell'insieme di credenziali delle chiavi come variabile di ambiente, parametro dell'applicazione o credenziale del repository del contenitore. Service Fabric risolverà automaticamente il segreto usando l'identità gestita dell'applicazione. 
     
- **Miglioramento della sicurezza dell'aggiornamento per**i servizi senza stato: per garantire la disponibilità durante l'aggiornamento di un'applicazione, sono state introdotte nuove configurazioni per definire il numero minimo di [istanze per](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) i servizi senza stato da considerare disponibili. In precedenza questo valore era 1 per tutti i servizi e non era modificabile. Con questo nuovo controllo di sicurezza per servizio, è possibile garantire che i servizi mantengano un numero minimo di istanze fino in esecuzione durante gli aggiornamenti delle applicazioni, gli aggiornamenti del cluster e altre operazioni di manutenzione che si basano sui controlli di integrità e sicurezza di Service Fabric.
  
- [**Limiti delle risorse per**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services)i servizi utente: gli utenti possono impostare limiti di risorse per i servizi utente in un nodo per impedire scenari quali l'esaurimento delle risorse dei servizi di sistema di Service Fabric. 
  
- [**Costo**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) di spostamento del servizio molto elevato per un tipo di replica. Le repliche con costo di spostamento molto elevato verranno spostate solo se nel cluster è presente una violazione del vincolo che non può essere corretta in altri modi. Consultare la documentazione per ulteriori informazioni su quando l'utilizzo di un costo di spostamento "molto alto" è ragionevole e per ulteriori considerazioni.
  
-  **Controlli di sicurezza del cluster aggiuntivi**: In questa versione è stato introdotto un controllo di sicurezza del quorum del nodo di seeding configurabile. In questo modo è possibile personalizzare il numero di nodi di seeding che devono essere disponibili durante il ciclo di vita del cluster e gli scenari di gestione. Le operazioni che porterebbero il cluster al di sotto del valore configurato vengono bloccate. Oggi il valore predefinito è sempre un quorum dei nodi di seeding, ad esempio, se si dispone di 7 nodi di seeding, un'operazione che porterebbe sotto 5 nodi di serie verrebbero bloccati per impostazione predefinita. Con questa modifica, è possibile impostare il valore minimo sicuro 6, che consentirebbe l'inattività di un solo nodo di serie alla volta.
   
- Aggiunto il supporto per [**la gestione del servizio di backup e ripristino in Service Fabric Explorer**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster). Ciò rende possibili le seguenti attività direttamente da SFX: individuazione del servizio di backup e ripristino, creazione di criteri di backup, abilitazione dei backup automatici, esecuzione di backup ad hoc, attivazione di operazioni di ripristino ed esplorazione dei backup esistenti.

- Annuncio della disponibilità di [**ReliableCollectionsMissingTypesTool:**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)questo strumento consente di verificare che i tipi utilizzati in raccolte reliable siano compatibili con l'avanti e le versioni precedenti durante un aggiornamento in sequenza dell'applicazione. In questo modo si evitano errori di aggiornamento o perdita di dati e danneggiamento dei dati a causa di tipi mancanti o incompatibili.

- [**Abilitare letture stabili nelle repliche secondarie:**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1)le letture stabili limiteranno le repliche secondarie alla restituzione dei valori che sono stati sottoposti a quorum.

Inoltre, questa versione contiene altre nuove funzionalità, correzioni di bug e miglioramenti della supportabilità, affidabilità e prestazioni. Per l'elenco completo delle modifiche, fare riferimento alle [note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

### <a name="service-fabric-70-releases"></a>Service Fabric 7.0 rilascia

| Data di rilascio | Versione | Altre informazioni |
|---|---|---|
| 18 novembre 2019 | [Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 gennaio 2020 | [Azure Service Fabric 7.0 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| giovedì 6 febbraio 2020 | [Azure Service Fabric 7.0 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Fabric 6.5

Questa versione include miglioramenti di supportabilità, affidabilità e prestazioni, nuove funzionalità, correzioni di bug e miglioramenti per semplificare la gestione del ciclo di vita di cluster e applicazioni.

> [!IMPORTANT]
> Service Fabric 6.5 è la versione finale con il supporto degli strumenti di Service Fabric in Visual Studio 2015.Service Fabric 6.5 is the final release with Service Fabric tools support in Visual Studio 2015. Si consiglia ai clienti di passare a [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) in futuro.

Ecco le novità di Service Fabric 6.5:Here's what's new in Service Fabric 6.5:

- Service Fabric Explorer include un [visualizzatore dell'archivio](service-fabric-visualizing-your-cluster.md#image-store-viewer) immagini per l'ispezione delle applicazioni caricate nell'archivio immagini.

- [Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) versione [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) include molti miglioramenti di autodiagnostica. I clienti di POA sono invitati a passare a questa versione.

- [Il servizio EventStore è abilitato per impostazione predefinita](service-fabric-visualizing-your-cluster.md#event-store) per i cluster di Service Fabric 6.5, a meno che non sia stato rifiutato esplicito.

- Aggiunti [eventi del ciclo di vita](service-fabric-diagnostics-event-generation-operational.md#replica-events) della replica per i servizi con stato.

- [Migliore visibilità dello stato del nodo di seeding](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), inclusi gli avvisi a livello di cluster se un nodo di seeding non è integro (*Giù*, *Rimosso* o *Sconosciuto*).

- Lo strumento di ripristino di [emergenza dell'applicazione Service Fabric](https://github.com/Microsoft/Service-Fabric-AppDRTool) consente ai servizi con stato di Service Fabric di eseguire rapidamente il ripristino rapidamente in caso di emergenza del cluster primario. I dati del cluster primario vengono continuamente sincronizzati nell'applicazione di standby secondaria utilizzando il backup e il ripristino periodici.

- Supporto di Visual Studio per la pubblicazione di app .NET Core in [cluster basati su Linux.](service-fabric-how-to-publish-linux-app-vs.md)

- L'interfaccia della riga di comando di Azure Service Fabric (SFCTL) verrà installata automaticamente per Service Fabric 6.5 (e versioni successive) quando si aggiorna o si crea un nuovo cluster Linux in Azure.Azure [Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) will be installed automatically for Service Fabric 6.5 (and later versions) when you upgrade or create a new Linux cluster on Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) viene installato per impostazione predefinita nei cluster OneBox MacOS/Linux.

Per ulteriori dettagli, vedere le note sulla versione di [Service Fabric 6.5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Service Fabric 6.5 rilascia

| Data di rilascio | Versione | Altre informazioni |
|---|---|---|
| 11 giugno 2019 | [Azure Service Fabric 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 luglio 2019 | [Azure Service Fabric 6.5 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 luglio 2019 | [Azure Service Fabric 6.5 Refresh Release](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Ago 23, 2019 | [Azure Service Fabric 6.5 Refresh Release](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| giovedì 14 ottobre 2019 | [Azure Service Fabric 6.5 Refresh Release](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>Versioni precedenti

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 rilascia

| Data di rilascio | Versione | Altre informazioni |
|---|---|---|
| giovedì 30 novembre 2018 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 dicembre 2018 | [Versione di aggiornamento di Azure Service Fabric 6.4 per cluster WindowsAzure Service Fabric 6.4 Refresh Release for Windows clusters](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| giovedì 4 febbraio 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| giovedì 4 marzo 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 aprile 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| giovedì 2 maggio 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| giovedì 28 maggio 2019 | [Azure Service Fabric 6.4 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
