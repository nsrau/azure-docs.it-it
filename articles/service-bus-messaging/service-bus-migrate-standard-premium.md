---
title: 'Eseguire la migrazione degli spazi dei nomi del bus di servizio di Azure: da standard a premiumMigrate Azure Service Bus namespaces - standard to premium'
description: Guida per consentire la migrazione degli spazi dei nomi standard del bus di servizio di Azure esistenti a premiumGuide to allow migration of existing Azure Service Bus standard namespaces to premium
services: service-bus-messaging
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 27e3260b91bebee14ff12188a7dbd6c7cf76355c
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385028"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Eseguire la migrazione degli spazi dei nomi standard del bus di servizio di Azure esistenti al livello PremiumMigrate existing Azure Service Bus standard namespaces to the premium tier

In precedenza, il bus di servizio di Azure offriva spazi dei nomi solo nel livello standard. Gli spazi dei nomi sono configurazioni multi-tenant ottimizzate per ambienti con bassa velocità effettiva e sviluppatori. Il livello Premium offre risorse dedicate per ogni spazio dei nomi per una latenza prevedibile e una maggiore velocità effettiva a un prezzo fisso. Il livello Premium è ottimizzato per ambienti di velocità effettiva e produzione elevati che richiedono funzionalità aziendali aggiuntive.

In questo articolo viene descritto come eseguire la migrazione degli spazi dei nomi del livello standard esistenti al livello Premium.This article describes how to migrate existing standard tier namespaces to the premium tier.  

>[!WARNING]
> La migrazione è destinata agli spazi dei nomi standard del bus di servizio da aggiornare al livello Premium.Migration is intended for Service Bus standard namespaces to be upgraded to the premium tier. Lo strumento di migrazione non supporta il downgrade.

Alcuni dei punti da notare:

- Questa migrazione ha lo scopo di avvenire sul posto, il che significa che le applicazioni mittente e destinatario esistenti **non richiedono alcuna modifica al codice o alla configurazione**. La stringa di connessione esistente punterà automaticamente al nuovo spazio dei nomi Premium.The existing connection string will automatically point to the new premium namespace.
- Lo spazio dei nomi **premium** non deve avere **entità** in esso per la migrazione abbia esito positivo.
- Tutte le **entità** nello spazio dei nomi standard vengono **copiate** nello spazio dei nomi Premium durante il processo di migrazione.
- La migrazione supporta **1.000 entità per unità** di messaggistica nel livello Premium. Per identificare il numero di unità di messaggistica necessarie, iniziare con il numero di entità nello spazio dei nomi standard corrente.
- Non è possibile eseguire direttamente la migrazione dal **livello di base** al livello **Premium,** ma è possibile eseguire indirettamente la migrazione da Basic a Standard prima e quindi dallo standard al premium nel passaggio successivo.

## <a name="migration-steps"></a>Passaggi della migrazione

Alcune condizioni sono associate al processo di migrazione. Acquisire familiarità con i seguenti passaggi per ridurre la possibilità di errori. Questi passaggi illustrano il processo di migrazione e i dettagli dettagliati sono elencati nelle sezioni seguenti.

1. Creare un nuovo spazio dei nomi Premium.Create a new premium namespace.
1. Associare gli spazi dei nomi standard e premium tra loro.
1. Sincronizzare le entità (copy-over) dallo spazio dei nomi standard allo spazio dei nomi Premium.Sync (copy-over) entities from the standard to the premium namespace.
1. Eseguire il commit della migrazione.
1. Svuotare le entità nello spazio dei nomi standard usando il nome post-migrazione dello spazio dei nomi.
1. Eliminare lo spazio dei nomi standard.

>[!IMPORTANT]
> Dopo il commit della migrazione, accedere allo spazio dei nomi standard precedente ed esaurire le code e le sottoscrizioni. Dopo che i messaggi sono stati scaricati, possono essere inviati al nuovo spazio dei nomi premium per essere elaborati dalle applicazioni del destinatario. Dopo che le code e le sottoscrizioni sono state svuotate, è consigliabile eliminare lo spazio dei nomi standard precedente.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Eseguire la migrazione tramite l'interfaccia della riga di comando di Azure o PowerShellMigrate by using the Azure CLI or PowerShell

Per eseguire la migrazione dello spazio dei nomi standard del bus di servizio a premium usando l'interfaccia della riga di comando di Azure o lo strumento PowerShell, eseguire la procedura seguente.

1. Creare un nuovo spazio dei nomi Premium del bus di servizio. È possibile fare riferimento [ai modelli](service-bus-resource-manager-namespace.md) di Azure Resource Manager o usare il portale di [Azure.](service-bus-create-namespace-portal.md) Assicurarsi di selezionare **premium** per il parametro **serviceBusSku.**

1. Impostare le seguenti variabili di ambiente per semplificare i comandi di migrazione.

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > L'alias/nome (post_migration_dns_name) post-migrazione verrà utilizzato per accedere al vecchio spazio dei nomi standard dopo la migrazione. Utilizzare questa opzione per svuotare le code e le sottoscrizioni e quindi eliminare lo spazio dei nomi.

1. Associare gli spazi dei nomi standard e premium e avviare la sincronizzazione utilizzando il comando seguente:

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. Controllare lo stato della migrazione utilizzando il comando seguente:

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    La migrazione viene considerata completa quando vengono visualizzati i valori seguenti:The migration is considered complete when you see the following values:

    * MigrationState - "Attivo"
    * pendingReplicationsOperationsCount : 0
    * provisioningState - "Operazione completata"

    Questo comando visualizza anche la configurazione della migrazione. Verificare che i valori siano impostati correttamente. Controllare inoltre lo spazio dei nomi Premium nel portale per verificare che tutte le code e gli argomenti siano stati creati e che corrispondano a quelli esistenti nello spazio dei nomi standard.

1. Eseguire il commit della migrazione eseguendo il comando completo seguente:

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Eseguire la migrazione tramite il portale di AzureMigrate by using the Azure portal

La migrazione tramite il portale di Azure ha lo stesso flusso logico della migrazione tramite i comandi. Seguire questi passaggi per eseguire la migrazione tramite il portale di Azure.Follow these steps to migrate by using the Azure portal.

1. Nel menu **Navigazione** nel riquadro sinistro selezionare **Migra a premium**. Fare clic sul pulsante **Inizia** per passare alla pagina successiva.
    ![Pagina di destinazione della migrazione][]

1. Completare **l'installazione**.
   ![Spazio dei nomi di installazione][]
   1. Creare e assegnare lo spazio dei nomi Premium in cui eseguire la migrazione dello spazio dei nomi standard esistente.
        ![Spazio dei nomi di installazione - creare lo spazio dei nomi PremiumSetup namespace - create premium namespace][]
   1. Scegliere un **nome post-migrazione**. Questo nome verrà usato per accedere allo spazio dei nomi standard al termine della migrazione.
        ![Spazio dei nomi di installazione - selezionare il nome post-migrazione][]
   1. Selezionare **'Avanti'** per continuare.
1. Sincronizzare le entità tra gli spazi dei nomi standard e premium.
    ![Spazio dei nomi di installazione - entità di sincronizzazione - avvio][]

   1. Selezionare **Avvia sincronizzazione** per avviare la sincronizzazione delle entità.
   1. Selezionare **Sì** nella finestra di dialogo per confermare e avviare la sincronizzazione.
   1. Attendere il completamento della sincronizzazione. Lo stato è disponibile sulla barra di stato.
        ![Spazio dei nomi di installazione - entità di sincronizzazione - avanzamento][]
        >[!IMPORTANT]
        > Se è necessario interrompere la migrazione per qualsiasi motivo, esaminare il flusso di interruzione nella sezione Domande frequenti di questo documento.
   1. Al termine della sincronizzazione, selezionare **Avanti** nella parte inferiore della pagina.

1. Esaminare le modifiche nella pagina di riepilogo. Selezionare **Completa migrazione** per passare da uno spazio dei nomi all'altro e per completare la migrazione.
    ![Cambia spazio dei nomi - menu Switch][]  
    Al termine della migrazione viene visualizzata la pagina di conferma.
    ![Cambia spazio dei nomi - esito positivoSwitch namespace - success][]

## <a name="caveats"></a>Precisazioni

Alcune delle funzionalità fornite dal livello Standard del bus di servizio di Azure non sono supportate dal livello Premium del bus di servizio di Azure.Some of the features provided by Azure Service Bus Standard tier are not supported by Azure Service Bus Premium tier. Questi sono in base alla progettazione poiché il livello premium offre risorse dedicate per velocità effettiva e latenza prevedibili.

Ecco un elenco delle funzioni non supportate da Premium e la loro mitigazione -

### <a name="express-entities"></a>Entità Express

   Express entities that don't commit any message data to storage are not supported in Premium. Le risorse dedicate hanno fornito un miglioramento significativo della velocità effettiva garantendo al contempo la persistenza dei dati, come previsto da qualsiasi sistema di messaggistica aziendale.

   Durante la migrazione, tutte le entità express nello spazio dei nomi Standard verranno create nello spazio dei nomi Premium come entità non express.

   Se si usano modelli di Azure Resource Manager (ARM), assicurarsi di rimuovere il flag 'enableExpress' dalla configurazione di distribuzione in modo che i flussi di lavoro automatizzati vengano eseguiti senza errori.

### <a name="partitioned-entities"></a>Entità partizionate

   Le entità partizionate sono state supportate nel livello Standard per garantire una migliore disponibilità in una configurazione multi-tenant. Con la fornitura di risorse dedicate disponibili per ogni spazio dei nomi nel livello Premium, questa operazione non è più necessaria.

   Durante la migrazione, qualsiasi entità partizionata nello spazio dei nomi Standard viene creata nello spazio dei nomi Premium come entità non partizionata.

   Se il modello ARM imposta 'enablePartitioning' su 'true' per una coda o un argomento specifico, verrà ignorato dal broker.

## <a name="faqs"></a>Domande frequenti

### <a name="what-happens-when-the-migration-is-committed"></a>Cosa succede quando viene eseguito il commit della migrazione?

Dopo il commit della migrazione, la stringa di connessione che punta allo spazio dei nomi standard punterà allo spazio dei nomi Premium.After the migration is committed, the connection string that pointed to the standard namespace will point to the premium namespace.

Le applicazioni mittente e destinatario si disconnetteranno dallo spazio dei nomi standard e si riconnetteranno automaticamente allo spazio dei nomi Premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Cosa devo fare dopo che lo standard per la migrazione premium è stato completato?

La migrazione standard a premium garantisce che i metadati dell'entità, ad esempio argomenti, sottoscrizioni e filtri, vengano copiati dallo spazio dei nomi standard allo spazio dei nomi Premium.The standard to premium migration ensures that the entity metadata such as topics, subscriptions, and filters are copied from the standard namespace to the premium namespace. I dati del messaggio di cui è stato eseguito il commit nello spazio dei nomi standard non vengono copiati dallo spazio dei nomi standard allo spazio dei nomi Premium.The message data that was committed to the standard namespace't copied from the standard namespace to the premium namespace.

Lo spazio dei nomi standard può avere alcuni messaggi che sono stati inviati e sottoposti a commit mentre la migrazione era in corso. Svuotare manualmente questi messaggi dallo spazio dei nomi standard e inviarli manualmente allo spazio dei nomi premium. Per svuotare manualmente i messaggi, usare un'app console o uno script che scarichi le entità dello spazio dei nomi standard usando il nome DNS post-migrazione specificato nei comandi di migrazione. Inviare questi messaggi allo spazio dei nomi Premium in modo che possano essere elaborati dai destinatari.

Dopo che i messaggi sono stati scaricati, eliminare lo spazio dei nomi standard.

>[!IMPORTANT]
> Dopo che i messaggi dallo spazio dei nomi standard sono stati scaricati, eliminare lo spazio dei nomi standard. Questo è importante perché la stringa di connessione che inizialmente faceva riferimento allo spazio dei nomi standard ora fa riferimento allo spazio dei nomi premium. Non avrai più bisogno dello spazio dei nomi standard. L'eliminazione dello spazio dei nomi standard di cui è stata eseguita la migrazione consente di ridurre la confusione successiva.

### <a name="how-much-downtime-do-i-expect"></a>Quanto tempo di inattività mi aspetto?

Il processo di migrazione ha lo scopo di ridurre i tempi di inattività previsti per le applicazioni. Il tempo di inattività viene ridotto utilizzando la stringa di connessione utilizzata dalle applicazioni mittente e destinatario per puntare al nuovo spazio dei nomi Premium.Downtime is reduced by using the connection string that the sender and receiver applications use to point to the new premium namespace.

Il tempo di inattività che si verifica dall'applicazione è limitato al tempo necessario per aggiornare la voce DNS in modo che punti allo spazio dei nomi Premium. Il tempo di inattività è di circa 5 minuti.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>È necessario apportare modifiche alla configurazione durante la migrazione?

No, non sono necessarie modifiche al codice o alla configurazione per eseguire la migrazione. La stringa di connessione utilizzata dalle applicazioni mittente e destinatario per accedere allo spazio dei nomi standard viene mappata automaticamente in modo da fungere da alias per lo spazio dei nomi Premium.The connection string that sender and receiver applications use to access the standard Namespace is automatically mapped to act as an alias for the premium namespace.

### <a name="what-happens-when-i-abort-the-migration"></a>Cosa succede quando si interrompe la migrazione?

La migrazione può essere interrotta `Abort` tramite il comando o il portale di Azure.The migration can be aborted either by using the command or by using the Azure portal.

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Portale di Azure

![Interrompi flusso -][]
![Interruzione sincronizzazione Interruzione flusso - Interruzione completata][]

Quando il processo di migrazione viene interrotto, interrompe il processo di copia delle entità (argomenti, sottoscrizioni e filtri) dallo standard allo spazio dei nomi Premium e interrompe l'associazione.

La stringa di connessione non viene aggiornata in modo da puntare allo spazio dei nomi Premium.The connection string isn't updated to point to the premium namespace. Le applicazioni esistenti continuano a funzionare come prima di iniziare la migrazione.

Tuttavia, non elimina le entità nello spazio dei nomi premium né elimina lo spazio dei nomi premium. Eliminare le entità manualmente se si è deciso di non procedere con la migrazione.

>[!IMPORTANT]
> Se si decide di interrompere la migrazione, eliminare lo spazio dei nomi premium di cui è stato eseguito il provisioning per la migrazione in modo da non addebitare alcun costo per le risorse.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Non voglio dover svuotare i messaggi. Cosa devo fare?

Potrebbero essere presenti messaggi inviati dalle applicazioni mittente e di cui viene eseguito il commit nell'archiviazione nello spazio dei nomi standard durante la migrazione e appena prima del commit della migrazione.

Durante la migrazione, i dati/payload effettivi del messaggio non vengono copiati dallo spazio dei nomi Premium standard. I messaggi devono essere scaricati manualmente e quindi inviati allo spazio dei nomi Premium.

Tuttavia, se è possibile eseguire la migrazione durante una finestra di manutenzione/pulizia pianificata e non si desidera svuotare manualmente e inviare i messaggi, attenersi alla seguente procedura:

1. Arrestare le applicazioni del mittente. Le applicazioni ricevente elaboreranno i messaggi che si trovano attualmente nello spazio dei nomi standard e svuotano la coda.
1. Dopo che le code e le sottoscrizioni nello spazio dei nomi standard sono vuote, seguire la procedura descritta in precedenza per eseguire la migrazione dallo standard allo spazio dei nomi Premium.
1. Al termine della migrazione, è possibile riavviare le applicazioni mittente.
1. I mittenti e i ricevitori si connetteranno automaticamente con lo spazio dei nomi premium.

    >[!NOTE]
    > Non è necessario arrestare le applicazioni ricevente per la migrazione.
    >
    > Al termine della migrazione, le applicazioni ricevente si disconnetteranno dallo spazio dei nomi standard e si connetteranno automaticamente allo spazio dei nomi Premium.After the migration is complete, the receiver applications will disconnect from the standard namespace and automatically connect to the premium namespace.

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni sulle [differenze tra messaggistica standard e premium.](./service-bus-premium-messaging.md)
* Informazioni sugli [aspetti High-Availability e Geo-Disaster recovery per Service Bus premium](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

[Pagina di destinazione della migrazione]: ./media/service-bus-standard-premium-migration/1.png
[Spazio dei nomi di installazione]: ./media/service-bus-standard-premium-migration/2.png
[Spazio dei nomi di installazione - creare lo spazio dei nomi PremiumSetup namespace - create premium namespace]: ./media/service-bus-standard-premium-migration/3.png
[Spazio dei nomi di installazione - selezionare il nome post-migrazione]: ./media/service-bus-standard-premium-migration/4.png
[Spazio dei nomi di installazione - entità di sincronizzazione - avvio]: ./media/service-bus-standard-premium-migration/5.png
[Spazio dei nomi di installazione - entità di sincronizzazione - avanzamento]: ./media/service-bus-standard-premium-migration/8.png
[Cambia spazio dei nomi - menu Switch]: ./media/service-bus-standard-premium-migration/9.png
[Cambia spazio dei nomi - esito positivoSwitch namespace - success]: ./media/service-bus-standard-premium-migration/12.png

[Interrompere il flusso - interrompere la sincronizzazione]: ./media/service-bus-standard-premium-migration/abort1.png
[Interruzione flusso - Interruzione completata]: ./media/service-bus-standard-premium-migration/abort3.png
