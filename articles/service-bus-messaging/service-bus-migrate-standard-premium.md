---
title: Eseguire la migrazione degli spazi dei nomi del bus di servizio di Azure-standard a Premium
description: Guida per consentire la migrazione degli spazi dei nomi standard del bus di servizio di Azure esistenti a Premium
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
ms.openlocfilehash: 610c3aa486b48b2d29df48d98e93b37cfec4854c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790372"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Eseguire la migrazione degli spazi dei nomi standard del bus di servizio di Azure esistenti al livello Premium
In precedenza, il bus di servizio di Azure offriva gli spazi dei nomi solo nel livello standard. Gli spazi dei nomi sono configurazioni multi-tenant ottimizzate per la velocità effettiva bassa e gli ambienti di sviluppo. Il livello Premium offre risorse dedicate per spazio dei nomi per la latenza prevedibile e una maggiore velocità effettiva a un prezzo fisso. Il livello Premium è ottimizzato per ambienti di produzione e velocità effettiva elevata che richiedono funzionalità aziendali aggiuntive.

Questo articolo descrive come eseguire la migrazione di spazi dei nomi di livello standard esistenti al livello Premium.  

>[!WARNING]
> La migrazione è destinata agli spazi dei nomi standard del bus di servizio da aggiornare al livello Premium. Lo strumento di migrazione non supporta il downgrade.

Alcuni punti da considerare: 
- Questa migrazione è concepita per essere eseguita, pertanto le applicazioni mittente e ricevitore esistenti **non richiedono alcuna modifica al codice o alla configurazione**. La stringa di connessione esistente punterà automaticamente al nuovo spazio dei nomi Premium.
- Lo spazio dei nomi Premium **non** deve contenere entità perché la migrazione abbia esito positivo. 
- Tutte le **entità** nello spazio dei nomi standard vengono **copiate** nello spazio dei nomi Premium durante il processo di migrazione. 
- La migrazione supporta **1.000 entità per ogni unità di messaggistica** nel livello Premium. Per identificare il numero di unità di messaggistica necessarie, iniziare con il numero di entità disponibili nello spazio dei nomi standard corrente. 
- Non è possibile eseguire direttamente la migrazione dal livello **Basic** al **livello Premier**, ma è possibile eseguire questa operazione indirettamente eseguendo la migrazione da Basic a standard e quindi da standard a Premium nel passaggio successivo.

## <a name="migration-steps"></a>Passaggi della migrazione
Alcune condizioni sono associate al processo di migrazione. Acquisire familiarità con i passaggi seguenti per ridurre la possibilità di errori. Questi passaggi illustrano il processo di migrazione e i dettagli sono elencati nelle sezioni seguenti.

1. Creare un nuovo spazio dei nomi Premium.
1. Associare gli spazi dei nomi standard e Premium tra loro.
1. Le entità di sincronizzazione (copia) dallo standard allo spazio dei nomi Premium.
1. Eseguire il commit della migrazione.
1. Svuotare le entità nello spazio dei nomi standard usando il nome post-migrazione dello spazio dei nomi.
1. Eliminare lo spazio dei nomi standard.

>[!IMPORTANT]
> Dopo che è stato eseguito il commit della migrazione, accedere allo spazio dei nomi standard precedente e svuotare le code e le sottoscrizioni. Una volta svuotati, i messaggi possono essere inviati al nuovo spazio dei nomi premium per essere elaborati dalle applicazioni riceventi. Una volta svuotate le code e le sottoscrizioni, è consigliabile eliminare lo spazio dei nomi standard precedente.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Eseguire la migrazione usando l'interfaccia della riga di comando di Azure o PowerShell

Per eseguire la migrazione dello spazio dei nomi standard del bus di servizio a Premium usando l'interfaccia della riga di comando di Azure o lo strumento PowerShell, seguire questa procedura

1. Creare un nuovo spazio dei nomi premium del bus di servizio. È possibile fare riferimento ai [modelli di Azure Resource Manager](service-bus-resource-manager-namespace.md) o [usare il portale di Azure](service-bus-create-namespace-portal.md). Assicurarsi di selezionare **Premium** per il parametro **serviceBusSku** .

1. Impostare le variabili di ambiente seguenti per semplificare i comandi di migrazione.
   ```azurecli
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > L'alias/nome post-migrazione (post_migration_dns_name) verrà usato per accedere allo spazio dei nomi standard precedente dopo la migrazione. Utilizzare questa operazione per svuotare le code e le sottoscrizioni e quindi eliminare lo spazio dei nomi.

1. Associare gli spazi dei nomi standard e Premium e avviare la sincronizzazione usando il comando seguente:

    ```azurecli
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


1. Verificare lo stato della migrazione usando il comando seguente:
    ```azurecli
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    La migrazione è considerata completa quando vengono visualizzati i valori seguenti:
    * MigrationState = "attivo"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "succeeded"

    Questo comando Visualizza anche la configurazione della migrazione. Assicurarsi che i valori siano impostati correttamente. Controllare anche lo spazio dei nomi premium nel portale per assicurarsi che tutte le code e gli argomenti siano stati creati e che corrispondano a quelli esistenti nello spazio dei nomi standard.

1. Eseguire il commit della migrazione eseguendo il comando completo seguente:
   ```azurecli
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Eseguire la migrazione usando il portale di Azure

La migrazione tramite il portale di Azure ha lo stesso flusso logico della migrazione mediante i comandi. Attenersi alla seguente procedura per eseguire la migrazione tramite il portale di Azure.

1. Nel menu di **spostamento** nel riquadro sinistro selezionare **migrazione a Premium**. Fare clic sul pulsante **Get Started (inizia** ) per passare alla pagina successiva.
    Pagina di destinazione della migrazione ![][]

1. Completare l' **installazione**.
   spazio dei nomi di installazione ![][]
   1. Creare e assegnare lo spazio dei nomi premium per la migrazione dello spazio dei nomi standard esistente a.
        ![spazio dei nomi di installazione-crea spazio dei nomi Premium][]
   1. Scegliere un **nome post-migrazione**. Questo nome verrà usato per accedere allo spazio dei nomi standard al termine della migrazione.
        spazio dei nomi di installazione ![: selezionare il nome della migrazione post][]
   1. Selezionare **' avanti '** per continuare.
1. Sincronizzare le entità tra gli spazi dei nomi standard e Premium.
    ![di installazione dello spazio dei nomi-entità di sincronizzazione-avviare][]

   1. Selezionare **Avvia sincronizzazione** per avviare la sincronizzazione delle entità.
   1. Selezionare **Sì** nella finestra di dialogo per confermare e avviare la sincronizzazione.
   1. Attendere il completamento della sincronizzazione. Lo stato è disponibile sulla barra di stato.
        ![impostazione spazio dei nomi-Sincronizza entità-stato][]
        >[!IMPORTANT]
        > Se è necessario interrompere la migrazione per qualsiasi motivo, vedere il flusso di interruzione nella sezione Domande frequenti di questo documento.
   1. Al termine della sincronizzazione, selezionare **Avanti** nella parte inferiore della pagina.

1. Esaminare le modifiche nella pagina di riepilogo. Selezionare **completa migrazione** per cambiare spazio dei nomi e completare la migrazione.
    ![opzioni del menu cambia spazio dei nomi][]  
    La pagina di conferma viene visualizzata al termine della migrazione.
    ![spazio dei nomi switch-Success][]

## <a name="caveats"></a>Avvertenze

Alcune delle funzionalità fornite dal livello standard del bus di servizio di Azure non sono supportate dal livello Premium del bus di servizio di Azure. Si tratta di un progetto perché il livello Premium offre risorse dedicate per la velocità effettiva e la latenza prevedibili.

Di seguito è riportato un elenco delle funzionalità non supportate da Premium e della relativa mitigazione. 

### <a name="express-entities"></a>Entità Express

   Le entità Express che non eseguono il commit di dati di messaggi nell'archiviazione non sono supportate in Premium. Le risorse dedicate forniscono un miglioramento significativo della velocità effettiva garantendo al tempo stesso la permanenza dei dati, come previsto da qualsiasi sistema di messaggistica aziendale.
   
   Durante la migrazione, qualsiasi entità Express nello spazio dei nomi standard verrà creata nello spazio dei nomi premium come entità non Express.
   
   Se si usano i modelli di Azure Resource Manager (ARM), assicurarsi di rimuovere il flag "enableExpress" dalla configurazione di distribuzione in modo che i flussi di lavoro automatici vengano eseguiti senza errori.

### <a name="partitioned-entities"></a>Entità partizionate

   Le entità partizionate sono supportate nel livello standard per offrire un disponibilità migliore in una configurazione multi-tenant. Con il provisioning di risorse dedicate disponibili per spazio dei nomi nel livello Premium, questo non è più necessario.
   
   Durante la migrazione, qualsiasi entità partizionata nello spazio dei nomi standard viene creata nello spazio dei nomi premium come entità non partizionata.
   
   Se il modello ARM Imposta ' enablePartitioning ' su' true ' per una coda o un argomento specifico, verrà ignorato dal broker.

## <a name="faqs"></a>Domande frequenti

### <a name="what-happens-when-the-migration-is-committed"></a>Cosa accade quando viene eseguito il commit della migrazione?

Dopo il commit della migrazione, la stringa di connessione che punta allo spazio dei nomi standard punterà allo spazio dei nomi Premium.

Le applicazioni mittente e ricevitore si disconnettono dallo spazio dei nomi standard e si riconnetteranno automaticamente allo spazio dei nomi Premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Cosa devo fare dopo aver completato la migrazione da standard a Premium?

La migrazione da standard a Premium garantisce che i metadati di entità quali argomenti, sottoscrizioni e filtri vengano copiati dallo spazio dei nomi standard allo spazio dei nomi Premium. I dati del messaggio di cui è stato eseguito il commit nello spazio dei nomi standard non vengono copiati dallo spazio dei nomi standard allo spazio dei nomi Premium.

Lo spazio dei nomi standard può includere alcuni messaggi inviati e di cui è stato eseguito il commit mentre era in corso la migrazione. Svuotare manualmente questi messaggi dallo spazio dei nomi standard e inviarli manualmente allo spazio dei nomi Premium. Per svuotare manualmente i messaggi, usare un'app console o uno script che svuota le entità dello spazio dei nomi standard usando il nome DNS post-migrazione specificato nei comandi di migrazione. Inviare questi messaggi allo spazio dei nomi premium in modo che possano essere elaborati dai destinatari.

Una volta svuotati i messaggi, eliminare lo spazio dei nomi standard.

>[!IMPORTANT]
> Dopo lo svuotamento dei messaggi dallo spazio dei nomi standard, eliminare lo spazio dei nomi standard. Questo è importante perché la stringa di connessione che inizialmente faceva riferimento allo spazio dei nomi standard fa ora riferimento allo spazio dei nomi Premium. Lo spazio dei nomi standard non sarà più necessario. L'eliminazione dello spazio dei nomi standard di cui è stata eseguita la migrazione contribuisce a ridurre la confusione successiva.

### <a name="how-much-downtime-do-i-expect"></a>Qual è il tempo di inattività previsto?
Il processo di migrazione ha lo scopo di ridurre i tempi di inattività previsti per le applicazioni. Il tempo di inattività viene ridotto utilizzando la stringa di connessione utilizzata dalle applicazioni mittente e ricevitore per puntare al nuovo spazio dei nomi Premium.

Il tempo di inattività riscontrato dall'applicazione è limitato al tempo necessario per aggiornare la voce DNS in modo da puntare allo spazio dei nomi Premium. Il tempo di inattività è di circa 5 minuti.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>È necessario apportare modifiche alla configurazione durante la migrazione?
No, per eseguire la migrazione non sono necessarie modifiche al codice o alla configurazione. La stringa di connessione utilizzata dalle applicazioni mittente e ricevitore per accedere allo spazio dei nomi standard viene mappata automaticamente per fungere da alias per lo spazio dei nomi Premium.

### <a name="what-happens-when-i-abort-the-migration"></a>Cosa accade quando si interrompe la migrazione?
La migrazione può essere interrotta usando il comando `Abort` o l'portale di Azure. 

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Portale di Azure

![Abort Flow-Abort Sync][]
![Abort Flow-Abort complete][]

Quando il processo di migrazione viene interrotto, interrompe il processo di copia delle entità (argomenti, sottoscrizioni e filtri) dallo spazio dei nomi standard allo spazio dei nomi premium e interrompe l'associazione.

La stringa di connessione non viene aggiornata in modo da puntare allo spazio dei nomi Premium. Le applicazioni esistenti continuano a funzionare come in precedenza prima di avviare la migrazione.

Tuttavia, non elimina le entità nello spazio dei nomi Premium o Elimina lo spazio dei nomi Premium. Eliminare manualmente le entità se si è scelto di non procedere con la migrazione.

>[!IMPORTANT]
> Se si decide di interrompere la migrazione, eliminare lo spazio dei nomi premium di cui è stato effettuato il provisioning per la migrazione in modo che non vengano addebitate le risorse.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Non desidero svuotare i messaggi. Che cosa occorre fare?

È possibile che siano presenti messaggi inviati dalle applicazioni del mittente e di cui è stato eseguito il commit nell'archiviazione nello spazio dei nomi standard mentre è in corso la migrazione e appena prima del commit della migrazione.

Durante la migrazione, il payload o i dati del messaggio effettivi non vengono copiati dallo spazio dei nomi standard allo spazio dei nomi Premium. I messaggi devono essere svuotati manualmente e quindi inviati allo spazio dei nomi Premium.

Tuttavia, se è possibile eseguire la migrazione durante una finestra di manutenzione o manutenzione pianificata e non si desidera svuotare manualmente e inviare i messaggi, attenersi alla procedura seguente:

1. Arrestare le applicazioni del mittente. Le applicazioni riceventi elaborano i messaggi che si trovano attualmente nello spazio dei nomi standard e svuotano la coda.
1. Una volta vuote le code e le sottoscrizioni nello spazio dei nomi standard, attenersi alla procedura descritta in precedenza per eseguire la migrazione dallo spazio dei nomi standard allo spazio dei nomi Premium.
1. Al termine della migrazione, è possibile riavviare le applicazioni del mittente.
1. I mittenti e i destinatari ora si connetteranno automaticamente allo spazio dei nomi Premium.

    >[!NOTE]
    > Non è necessario arrestare le applicazioni del ricevitore per la migrazione.
    >
    > Al termine della migrazione, le applicazioni riceventi si disconnettono dallo spazio dei nomi standard e si connettono automaticamente allo spazio dei nomi Premium.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [differenze tra la messaggistica standard e Premium](./service-bus-premium-messaging.md).
* Informazioni sugli [aspetti di disponibilità elevata e ripristino di emergenza geografico per il bus di servizio Premium](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

[Pagina di destinazione della migrazione]: ./media/service-bus-standard-premium-migration/1.png
[Spazio dei nomi di installazione]: ./media/service-bus-standard-premium-migration/2.png
[Spazio dei nomi setup-crea spazio dei nomi Premium]: ./media/service-bus-standard-premium-migration/3.png
[Imposta spazio dei nomi-Seleziona post migrazione]: ./media/service-bus-standard-premium-migration/4.png
[Configurare lo spazio dei nomi-entità di sincronizzazione-avvio]: ./media/service-bus-standard-premium-migration/5.png
[Impostazione spazio dei nomi-Sincronizza entità-stato]: ./media/service-bus-standard-premium-migration/8.png
[Opzioni del menu cambia spazio dei nomi]: ./media/service-bus-standard-premium-migration/9.png
[Switch namespace-operazione riuscita]: ./media/service-bus-standard-premium-migration/12.png

[Abort Flow-Interrompi sincronizzazione]: ./media/service-bus-standard-premium-migration/abort1.png
[Interrompi flusso-terminazione completata]: ./media/service-bus-standard-premium-migration/abort3.png
