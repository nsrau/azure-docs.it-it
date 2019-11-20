---
title: Versioni di Azure Service Fabric
description: Note sulla versione per le funzionalità e i miglioramenti più recenti in Service Fabric.
author: athinanthny
manager: gamonroy
ms.author: atsenthi
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 496a5babe58be4354ffb10a331d35abc8a51b04d
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186515"
---
# <a name="service-fabric-releases"></a>Versioni Service Fabric

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Guida</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">alla risoluzione dei problemi</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">delleOpzioni</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">supporto</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank"> per la gestione delle versioni supportate esempi di codice</a>

Questo articolo fornisce altre informazioni sulle versioni più recenti e sugli aggiornamenti per il runtime di Service Fabric e gli SDK.

## <a name="whats-new-in-service-fabric"></a>Novità di Service Fabric

### <a name="service-fabric-70"></a>Service Fabric 7,0

Azure Service Fabric 7,0 è ora disponibile. Sarà possibile eseguire l'aggiornamento a 7,0 tramite la portale di Azure o tramite una distribuzione di Azure Resource Manager. A causa dei commenti e suggerimenti dei clienti sulle versioni per il periodo festivo, non inizierà ad aggiornare automaticamente i cluster impostati per ricevere aggiornamenti automatici fino a gennaio.
A gennaio, verrà ripresa la procedura di implementazione standard e i cluster con aggiornamenti automatici abilitati inizieranno a ricevere automaticamente l'aggiornamento 7,0. Prima dell'inizio dell'implementazione, si fornirà un altro annuncio.
Verranno aggiornate anche le date di rilascio pianificate per indicare che questi criteri verranno presi in considerazione. Per gli aggiornamenti, vedere le [pianificazioni delle versioni](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)future.
 
Questa è la versione più recente di Service Fabric e viene caricata con le funzionalità e i miglioramenti principali.

### <a name="key-announcements"></a>Annunci chiave
 - [**Supporto di KeyVaultReference per i segreti dell'applicazione (anteprima)** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references): Service Fabric le applicazioni che hanno abilitato le [identità gestite](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) possono ora fare riferimento direttamente a un URL di Key Vault Secret come variabile di ambiente, parametro dell'applicazione o credenziale del repository del contenitore. Service Fabric risolverà automaticamente il segreto usando l'identità gestita dell'applicazione. 
     
- **Miglioramento della sicurezza dell'aggiornamento per i servizi**senza stato: per garantire la disponibilità durante l'aggiornamento di un'applicazione, sono state introdotte nuove configurazioni per definire il [numero minimo di istanze per i servizi](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) senza stato da considerare disponibili. In precedenza questo valore era 1 per tutti i servizi e non era modificabile. Con questo nuovo controllo di sicurezza per servizio, è possibile assicurarsi che i servizi mantengano un numero minimo di istanze durante gli aggiornamenti dell'applicazione, gli aggiornamenti del cluster e altre operazioni di manutenzione che si basano sui controlli di integrità e sicurezza del Service Fabric.
  
- [**Limiti delle risorse per i servizi utente**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services): gli utenti possono impostare i limiti delle risorse per i servizi utente in un nodo per evitare scenari come l'esaurimento delle risorse dei servizi di Service Fabric sistema. 
  
- [**Costo di spostamento del servizio molto elevato**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) per un tipo di replica. Le repliche con un costo di spostamento molto elevato verranno spostate solo se si verifica una violazione del vincolo nel cluster che non può essere risolto in altro modo. Per ulteriori informazioni sui casi in cui l'utilizzo di un costo di spostamento molto elevato è ragionevole e per ulteriori considerazioni, consultare la documentazione.
  
-  **Controlli di sicurezza aggiuntivi**per i cluster: in questa versione è stato introdotto un controllo di sicurezza quorum del nodo di inizializzazione configurabile. In questo modo è possibile personalizzare il numero di nodi di inizializzazione che devono essere disponibili durante gli scenari di gestione e ciclo di vita del cluster. Le operazioni che comportano il blocco del cluster al di sotto del valore configurato sono bloccate. Attualmente, il valore predefinito è sempre un quorum dei nodi di inizializzazione, ad esempio, se si dispone di 7 nodi di inizializzazione, un'operazione che richiederebbe meno di 5 nodi di inizializzazione verrebbe bloccata per impostazione predefinita. Con questa modifica, è possibile impostare il valore minimo di Safe 6, in modo da consentire un solo nodo di inizializzazione alla volta.
   
- Aggiunta del supporto per [**la gestione del servizio di backup e ripristino in Service Fabric Explorer**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster). In questo modo è possibile eseguire le attività seguenti direttamente da SFX: individuazione del servizio di backup e ripristino, creazione di criteri di backup, abilitazione di backup automatici, esecuzione di backup ad hoc, attivazione di operazioni di ripristino ed esplorazione dei backup esistenti.

- Annuncio della disponibilità del [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool): questo strumento consente di verificare che i tipi usati nelle raccolte Reliable Collections siano compatibili con le versioni precedenti e successive durante l'aggiornamento di un'applicazione in sequenza. Ciò consente di evitare errori di aggiornamento, perdita di dati e danneggiamento dei dati a causa di tipi mancanti o incompatibili.

- [**Abilitare le letture stabili nelle repliche secondarie**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1): le letture stabili limiteranno le repliche secondarie alla restituzione di valori che sono stati quorum-confermati.

Inoltre, questa versione contiene altre nuove funzionalità, correzioni di bug e miglioramenti del supporto, dell'affidabilità e delle prestazioni. Per l'elenco completo delle modifiche, consultare le note sulla [versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

| Data di rilascio | Versione | Altre informazioni |
|---|---|---|
| 18 novembre 2019 | [Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|


### <a name="service-fabric-65"></a>Service Fabric 6,5

Questa versione include miglioramenti a supporto, affidabilità e prestazioni, nuove funzionalità, correzioni di bug e miglioramenti per semplificare la gestione del ciclo di vita delle applicazioni e del cluster.

> [!IMPORTANT]
> Service Fabric 6,5 è la versione finale con supporto degli strumenti Service Fabric in Visual Studio 2015. Si consiglia ai clienti di passare a [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) in futuro.

Ecco le novità di Service Fabric 6,5:

- Service Fabric Explorer include un [visualizzatore archivio immagini](service-fabric-visualizing-your-cluster.md#image-store-viewer) per esaminare le applicazioni caricate nell'archivio immagini.

- [Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) versione [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) include numerosi miglioramenti di diagnostica automatica. I clienti di POA sono consigliati per passare a questa versione.

- Il [servizio EventStore è abilitato per impostazione predefinita](service-fabric-visualizing-your-cluster.md#event-store) per Service Fabric cluster 6,5, a meno che non sia stato rifiutato.

- Sono stati aggiunti [eventi del ciclo](service-fabric-diagnostics-event-generation-operational.md#replica-events) di vita della replica per i servizi con stato.

- [Migliore visibilità dello stato del nodo di inizializzazione](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), inclusi gli avvisi a livello di cluster se un nodo di inizializzazione non è integro (*inattivo*, *rimosso* o *sconosciuto*).

- [Service Fabric strumento di ripristino di emergenza dell'applicazione](https://github.com/Microsoft/Service-Fabric-AppDRTool) consente Service Fabric servizi con stato di eseguire rapidamente il ripristino quando il cluster primario rileva un'emergenza. I dati del cluster primario vengono continuamente sincronizzati nell'applicazione di standby secondaria mediante backup e ripristino periodici.

- Supporto di Visual Studio per la [pubblicazione di app .NET Core in cluster basati su Linux](service-fabric-how-to-publish-linux-app-vs.md).

- L'interfaccia della riga di comando di [Azure Service Fabric (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) verrà installata automaticamente per Service Fabric 6,5 (e versioni successive) quando si aggiorna o si crea un nuovo cluster Linux in Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) viene installato per impostazione predefinita nei cluster OneBox MacOS/Linux.

Per ulteriori informazioni, vedere le [Note sulla versione di Service Fabric 6,5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Versioni di Service Fabric 6,5

| Data di rilascio | Versione | Altre informazioni |
|---|---|---|
| 11 giugno 2019 | [Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 luglio 2019 | [Azure Service Fabric 6,5 aggiornamento versione](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 luglio 2019 | [Azure Service Fabric 6,5 aggiornamento versione](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23 agosto 2019 | [Azure Service Fabric 6,5 aggiornamento versione](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 ottobre 2019 | [Azure Service Fabric 6,5 aggiornamento versione](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>Versioni precedenti

### <a name="service-fabric-64-releases"></a>Versioni di Service Fabric 6,4

| Data di rilascio | Versione | Altre informazioni |
|---|---|---|
| 30 novembre, 2018 | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 dicembre 2018 | [Versione di aggiornamento di Azure Service Fabric 6,4 per i cluster Windows](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 febbraio 2019 | [Azure Service Fabric 6,4 aggiornamento versione](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 marzo 2019 | [Azure Service Fabric 6,4 aggiornamento versione](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 aprile 2019 | [Azure Service Fabric 6,4 aggiornamento versione](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 maggio 2019 | [Azure Service Fabric 6,4 aggiornamento versione](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 maggio 2019 | [Azure Service Fabric 6,4 aggiornamento versione](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
