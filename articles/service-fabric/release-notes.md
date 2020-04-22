---
title: Versioni di Azure Service FabricAzure Service Fabric releases
description: Note sulla versione per Azure Service Fabric. Include informazioni sulle funzionalità e sui miglioramenti più recenti di Service Fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 3e0f6c78b6e5dd066cbfbac6805bb3c42068e66a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729589"
---
# <a name="service-fabric-releases"></a>Versioni di Service Fabric

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Risoluzione dei problemi relativi alle guide alle opzioni</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Code Samples</a> di supporto per il<a href="https://github.com/Azure/service-fabric-issues" target="blank">rilevamento dei</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">problemi- nelle</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">versioni</a> 
| supportate

In questo articolo vengono fornite ulteriori informazioni sulle versioni e gli aggiornamenti più recenti per il runtime di Service Fabric e SDK.

## <a name="whats-new-in-service-fabric"></a>Novità di Service Fabric

### <a name="service-fabric-71"></a>Service Fabric 7.1
A causa dell'attuale crisi del COVID-19 e tenendo conto delle sfide affrontate dai nostri clienti, stiamo rendendo disponibili 7.1, ma non aggiorneremo automaticamente i cluster impostati per ricevere aggiornamenti automatici. Stiamo mettendo in pausa gli aggiornamenti automatici fino a nuovo avviso per garantire che i clienti possano applicare gli aggiornamenti quando sono più appropriati per loro, per evitare interruzioni impreviste.

Sarà possibile eseguire l'aggiornamento alla versione 7.1 tramite il portale di [Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal) o tramite una distribuzione di [Azure Resource Manager.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#set-the-upgrade-mode-using-a-resource-manager-template)

I cluster di Service Fabric con gli aggiornamenti automatici abilitati inizieranno a ricevere automaticamente l'aggiornamento 7.1 una volta ripresa la procedura di implementazione standard. Verrà fornito un altro annuncio prima dell'inizio dell'implementazione standard nel [sito della community di Service Fabric Tech](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).
Abbiamo anche pubblicato gli aggiornamenti alla data di fine del supporto per le versioni principali a partire dalla 6.5 alla 7.1 [qui](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-versions). 

## <a name="what-is-new-in-service-fabric-71"></a>Che cosa è nuovo in-Service Fabric 7.1?
Siamo lieti di annunciare la prossima versione di Service Fabric. Questa versione viene caricata con funzionalità e miglioramenti principali. Alcune delle caratteristiche principali sono evidenziate di seguito:
## <a name="key-announcements"></a>Annunci chiave
- **Disponibilità generale** delle [ **identità gestite** di Service Fabric per le applicazioni di Service Fabric](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity)
- [**Supporto per Ubuntu 1804**](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-create-vnet-and-linux-cluster)
 - Anteprima: supporto per i dischi del sistema [**operativo effimero VMSS**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-azure-deployment-preparation#use-ephemeral-os-disks-for-virtual-machine-scale-sets)- : i dischi del sistema operativo effimero sono l'archiviazione creata nella macchina virtuale locale e non salvata in Archiviazione di Azure remota. Sono consigliati per tutti i tipi di nodo Service Fabric (primario e secondario), perché rispetto ai dischi del sistema operativo persistente tradizionali, i dischi del sistema operativo effimeri:
      -  Ridurre la latenza di lettura/scrittura sul disco del sistema operativo
      -  Abilitare operazioni di ripristino/gestione dei nodi di nuova immagine più velociEnable faster reset/re-image node management operations
      -  Riduci i costi complessivi (i dischi sono gratuiti e non comportano costi di archiviazione aggiuntivi)
- Supporto per la dichiarazione dei [**certificati dell'endpoint del servizio delle applicazioni dell'infrastruttura di servizio in base**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)al nome comune del soggetto .
- [**Supporto per Health Probe sonderati per servizi containerizzati:**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage)supporto per il meccanismo Liveness Probe per applicazioni containerizzate. Liveness Probe aiuta ad annunciare la vivacità dell'applicazione containerizzata e quando non rispondono in modo tempestivo, si tradurrà in un riavvio. 
- [**Supporto per pacchetti di codice dell'inizializzatore**](https://docs.microsoft.com/azure/service-fabric/initializer-codepackages) per [contenitori](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-containers-overview) e applicazioni [eseguibili guest.](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-guest-executables-introduction) Ciò consente l'esecuzione di pacchetti di codice (ad esempio contenitori), in un ordine specificato, per eseguire l'inizializzazione del pacchetto di servizio.
- **FabricObserver e ClusterObserver** sono applicazioni senza stato che acquisiscono la telemetria dell'infrastruttura del servizio correlata a diversi aspetti di un cluster SF. Entrambe queste applicazioni sono pronte per la distribuzione nei cluster di produzione Windows per acquisire dati di telemetria avanzati con il supporto implementato per ApplicationInsights, EventSource e LogAnalytics.Both applications are ready for deployment to Windows production clusters to capture rich telemetry with implemented support for ApplicationInsights, EventSource and LogAnalytics.
    - [**FabricObserver (FO) 2.0**](https://github.com/microsoft/service-fabric-observer)- viene eseguito su tutti i nodi, genera eventi di integrità, genera dati di telemetria quando vengono raggiunte le soglie di utilizzo delle risorse configurate dall'utente. Questa versione contiene diversi miglioramenti tra il monitoraggio, la gestione dei dati, i dettagli degli eventi di integrità, i dati di telemetria strutturati.
     - [**ClusterObserver (CO) 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) - viene eseguito su un nodo e acquisisce i dati di telemetria di integrità a livello di cluster. In questa versione, ClusterObserver monitora anche lo stato del nodo e genera dati di telemetria quando il nodo è inattivo/disabilitato/disabilitato per un periodo di tempo più lungo rispetto al periodo di tempo specificato dall'utente.

### <a name="improve-application-life-cycle-experience"></a>Migliorare l'esperienza del ciclo di vita dell'applicazione

- **[Anteprima:Richiesta di svuotamento](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-advanced#avoid-connection-drops-during-planned-downtime-of-stateless-services)**: Durante la manutenzione del servizio pianificata, ad esempio gli aggiornamenti del servizio o la disattivazione del nodo, si desidera consentire ai servizi di eseguire normalmente le connessioni. Questa funzionalità aggiunge una durata del ritardo di chiusura dell'istanza nella configurazione del servizio. Durante le operazioni pianificate, SF rimuoverà l'indirizzo del servizio dall'individuazione e quindi attenderà questa durata prima di arrestare il servizio.
- **[Rilevamento e bilanciamento automatico dei sottocluster:](https://docs.microsoft.com/azure/service-fabric/cluster-resource-manager-subclustering )** il sottocluster si verifica quando i servizi con vincoli di posizionamento diversi hanno una [metrica](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-metrics)di carico comune. Se il carico sui diversi set di nodi differisce in modo significativo, Gestione risorse cluster di Service Fabric ritiene che il cluster sia sbilanciato, anche quando ha il miglior equilibrio possibile a causa dei vincoli di posizionamento. Di conseguenza, tenta di riequilibrare il cluster, causando potenzialmente movimenti di servizio non necessari (poiché lo "squilibrio" non può essere notevolmente migliorato). A partire da questa versione, Gestione risorse cluster tenterà ora di rilevare automaticamente questi tipi di configurazioni e capire quando lo squilibrio può essere risolto attraverso lo spostamento e quando invece dovrebbe lasciare le cose da sole poiché non è possibile fare alcun miglioramento sostanziale.  
- [**Costo di spostamento diverso per le repliche secondarie:**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost)è stato introdotto un nuovo valore di costo di spostamento VeryHigh che offre maggiore flessibilità in alcuni scenari per definire se un costo di spostamento separato deve essere utilizzato per le repliche secondarie.
- Meccanismo [**Liveness Probe**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage ) abilitato per le applicazioni containerizzate. Liveness Probe aiuta ad annunciare la vivacità dell'applicazione containerizzata e quando non rispondono in modo tempestivo, si tradurrà in un riavvio.
- [**Esegui fino al completamento/una volta per i servizi**](https://docs.microsoft.com/azure/service-fabric/run-to-completion)**

### <a name="image-store-improvements"></a>Miglioramenti all'archivio immagini
 - Service Fabric 7.1 utilizza il trasporto personalizzato per proteggere il **trasferimento di file tra i nodi per impostazione predefinita.** La dipendenza dalla condivisione file SMB viene rimossa dalla versione 7.1. Le condivisioni file SMB protette sono ancora esistenti nei nodi che contengono la replica del servizio Archivio immagini per la scelta del cliente di rifiutare esplicitamente l'impostazione predefinita e per l'aggiornamento e il downgrade alla versione precedente.
       
 ### <a name="reliable-collections-improvements"></a>Miglioramenti alle raccolte affidabili

- [**In memoria solo il supporto dell'archivio per**](https://docs.microsoft.com/azure/service-fabric/service-fabric-work-with-reliable-collections#volatile-reliable-collections)i servizi con stato tramite Reliable Collections : Volatile Reliable Collections consente di rendere persistenti i dati su disco per garantire la durabilità in caso di interruzioni su larga scala, può essere utilizzato per carichi di lavoro come la cache replicata, ad esempio in cui è possibile tollerare la perdita di dati occasionale. In base alle [limitazioni e](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections-guidelines#volatile-reliable-collections)alle restrizioni delle raccolte affidabili volatili , è consigliabile eseguire questa operazione per i carichi di lavoro che non richiedono persistenza, per i servizi che gestiscono le rare occasioni di perdita del quorum.
- [**Anteprima: Service Fabric Backup Explorer**](https://github.com/microsoft/service-fabric-backup-explorer): Per semplificare la gestione dei backup Reliable Collections per le applicazioni con stato di Service Fabric, Service Fabric Backup Explorer consente agli utenti di
    - Controllare ed esaminare i contenuti delle Reliable Collections,
    - Aggiornare lo stato corrente a una visualizzazione coerenteUpdate current state to a consistent view
    - Creazione di un backup dello snapshot corrente delle raccolte Reliable Collections
    - Correggere il danneggiamento dei dati
                 
### <a name="service-fabric-71-releases"></a>Service Fabric 7.1 rilascia
| Data di rilascio | Versione | Altre informazioni |
|---|---|---|
| 20 aprile 2020 | [Azure Service Fabric 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Note sulla versione](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|


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
| 18 novembre 2019 | [Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 gennaio 2020 | [Azure Service Fabric 7.0 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| giovedì 6 febbraio 2020 | [Azure Service Fabric 7.0 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2 marzo 2020 | [Azure Service Fabric 7.0 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)

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
| 11 giugno 2019 | [Azure Service Fabric 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 luglio 2019 | [Azure Service Fabric 6.5 Refresh Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 luglio 2019 | [Azure Service Fabric 6.5 Refresh Release](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Ago 23, 2019 | [Azure Service Fabric 6.5 Refresh Release](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| giovedì 14 ottobre 2019 | [Azure Service Fabric 6.5 Refresh Release](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Note sulla versione] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


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
