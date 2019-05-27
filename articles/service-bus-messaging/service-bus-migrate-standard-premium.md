---
title: Eseguire la migrazione esistente spazi dei nomi standard del Bus di servizio di Azure per il livello premium | Microsoft Docs
description: Guida alla migrazione di esistente del Bus di servizio spazi dei nomi standard a premium
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: darosa
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 65c207b4d03e7d156c8c871a3642601fd0489ead
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991412"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Eseguire la migrazione esistente spazi dei nomi standard del Bus di servizio di Azure per il livello premium
Il Bus di servizio di Azure offerto in precedenza, gli spazi dei nomi solo nel livello standard. Gli spazi dei nomi sono impostazioni di multi-tenant che sono ottimizzate per gli ambienti di sviluppo e una velocità effettiva bassa. Il livello premium offre risorse dedicate per ogni spazio dei nomi per aumentare la velocità effettiva a un prezzo fisso e latenza prevedibile. Il livello premium è ottimizzato per la velocità effettiva elevata e ambienti di produzione che richiedono funzionalità aggiuntive dell'organizzazione.

Questo articolo descrive come eseguire la migrazione di spazi dei nomi esistenti di livello standard al livello premium.  

>[!WARNING]
> La migrazione è progettata per gli spazi dei nomi standard del Bus di servizio per l'aggiornamento al livello premium. Lo strumento di migrazione non supporta il downgrade.

Alcuni punti da notare: 
- Questa migrazione deve essere eseguita sul posto, vale a dire esistenti applicazioni mittenti e riceventi **non richiedono alcuna modifica al codice o configurazione**. La stringa di connessione esistenti punteranno automaticamente al nuovo spazio dei nomi premium.
- Il **premium** dello spazio dei nomi deve avere **Nessuna entità** in esso per la migrazione abbia esito positivo. 
- Tutti i **entities** nello spazio dei nomi standard vengono **copiati** allo spazio dei nomi premium durante il processo di migrazione. 
- Supporta la migrazione **1.000 entità per ogni unità di messaggistica** nel livello premium. Per identificare quante unità di messaggistica è necessario, iniziare con il numero di entità che è necessario sullo spazio dei nomi standard corrente. 
- Non è possibile eseguire la migrazione direttamente da **il livello basic** al **livello premier**, ma è possibile eseguire in modo indiretto tramite la migrazione da basic a standard prima e quindi da standard a premium nel passaggio successivo.

## <a name="migration-steps"></a>Passaggi della migrazione
Alcune condizioni associate con il processo di migrazione. Acquisire familiarità con i passaggi seguenti per ridurre la possibilità di errori. Questi passaggi illustrano il processo di migrazione e le informazioni dettagliate sono elencati nelle sezioni che seguono.

1. Creare un nuovo spazio dei nomi premium.
1. Associare gli spazi dei nomi standard e premium tra loro.
1. Entità di sincronizzazione (copia sulla) dallo standard per lo spazio dei nomi premium.
1. Eseguire il commit della migrazione.
1. Svuotare le entità nello spazio dei nomi standard usando il nome di post-migrazione dello spazio dei nomi.
1. Eliminare lo spazio dei nomi standard.

>[!IMPORTANT]
> Dopo la migrazione è stato eseguito il commit, il precedente spazio dei nomi standard di accesso e svuotare le code e sottoscrizioni. Dopo che i messaggi sono stati svuotati, possono essere inviati per il nuovo spazio dei nomi premium per essere elaborati dalle applicazioni ricevitore. Dopo che sono state svuotate le code e sottoscrizioni, è consigliabile eliminare il precedente spazio dei nomi standard.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Eseguire la migrazione tramite il comando di Azure o PowerShell

Per eseguire la migrazione di spazio dei nomi di Bus di servizio standard a premium tramite il comando di Azure o lo strumento di PowerShell, seguire questa procedura.

1. Creare un nuovo spazio dei nomi premium del Bus di servizio. È possibile fare riferimento il [modelli di Azure Resource Manager](service-bus-resource-manager-namespace.md) oppure [usare il portale di Azure](service-bus-create-namespace-portal.md). Assicurarsi di selezionare **premium** per il **serviceBusSku** parametro.

1. Impostare le variabili di ambiente seguenti per semplificare i comandi di migrazione.
   ```azurecli
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > Il post-migrazione/nome di alias (post_migration_dns_name) da utilizzare per il precedente dopo la migrazione dello spazio dei nomi standard di accesso. Utilizzare questa opzione per svuotare le code e le sottoscrizioni e quindi eliminare lo spazio dei nomi.

1. Associare gli spazi dei nomi standard e premium, quindi avviare la sincronizzazione tramite il comando seguente:

    ```azurecli
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


1. Controllare lo stato della migrazione utilizzando il comando seguente:
    ```azurecli
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    La migrazione viene considerata completa quando vengono visualizzati i valori seguenti:
    * MigrationState = "Attivo"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Succeeded"

    Questo comando Visualizza inoltre la configurazione della migrazione. Verificare che i valori siano impostati correttamente. Verificare anche lo spazio dei nomi premium del portale per verificare che tutte le code e argomenti sono stati creati e che corrispondano a quanto è presente nello spazio dei nomi standard.

1. Eseguire il commit della migrazione eseguendo il comando seguente completato:
   ```azurecli
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Eseguire la migrazione tramite il portale di Azure

Migrazione tramite il portale di Azure ha lo stesso flusso logico come eseguire la migrazione usando i comandi. Seguire questi passaggi per eseguire la migrazione tramite il portale di Azure.

1. Nel **navigazione** dal menu nel riquadro sinistro, seleziona **eseguire la migrazione alla versione premium**. Scegliere il **iniziare a usare** pulsante per passare alla pagina successiva.
    ![Pagina di destinazione della migrazione][]

1. Completa **programma di installazione**.
   ![Il programma di installazione dello spazio dei nomi][]
   1. Creare e assegnare lo spazio dei nomi premium per eseguire la migrazione dello spazio dei nomi standard esistente a.
        ![Configurare lo spazio dei nomi: creare spazio dei nomi premium][]
   1. Scegliere una **nome dopo la migrazione**. Si userà questo nome per lo spazio dei nomi standard di accesso dopo la migrazione è completata.
        ![Configurare lo spazio dei nomi - scegliere nome migrazione post][]
   1. Selezionare **'Next'** per continuare.
1. Entità di sincronizzazione tra gli spazi dei nomi standard e premium.
    ![Configurare lo spazio dei nomi - sincronizzazione entità - inizio][]

   1. Selezionare **Avvia sincronizzazione** per avviare la sincronizzazione delle entità.
   1. Selezionare **Sì** nella finestra di dialogo per confermare e avviare la sincronizzazione.
   1. Attendere fino al completamento della sincronizzazione. Lo stato è disponibile sulla barra di stato.
        ![Stato dello spazio dei nomi - sincronizzazione entità - installazione][]
        >[!IMPORTANT]
        > Se è necessario interrompere la migrazione per qualsiasi motivo, esaminare il flusso di interruzione nella sezione Domande frequenti di questo documento.
   1. Dopo aver completata la sincronizzazione, selezionare **successivo** nella parte inferiore della pagina.

1. Esaminare le modifiche nella pagina di riepilogo. Selezionare **completare la migrazione** per passare gli spazi dei nomi e completare la migrazione.
    ![Passare lo spazio dei nomi - opzione di menu][] viene visualizzata la pagina di conferma una volta completata la migrazione.
    ![Spazio dei nomi switch - operazione riuscita][]

## <a name="faqs"></a>Domande frequenti

### <a name="what-happens-when-the-migration-is-committed"></a>Cosa accade quando viene eseguito il commit della migrazione?

Dopo aver eseguito il commit della migrazione, la stringa di connessione che fa riferimento allo spazio dei nomi standard farà riferimento allo spazio dei nomi premium.

Le applicazioni mittente e ricevitore verranno disconnettersi il Namespace standard e riconnettersi automaticamente allo spazio dei nomi premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Cosa fare dopo aver completato lo standard di migrazione a premium?

Lo standard per la migrazione di premium garantisce che i metadati delle entità, ad esempio argomenti, sottoscrizioni e i filtri vengono copiati dallo spazio dei nomi standard per lo spazio dei nomi premium. I dati del messaggio che è stato eseguito il commit dello spazio dei nomi standard non vengono copiati dallo spazio dei nomi standard per lo spazio dei nomi premium.

Lo spazio dei nomi standard può avere alcuni messaggi che sono stati inviati e il commit durante la migrazione in corso. Svuotare manualmente questi messaggi dal Namespace standard e inviare manualmente al premio Namespace. Per svuotare manualmente i messaggi, usare un'app console o uno script che Svuota le entità standard dello spazio dei nomi usando il nome DNS migrazione Post specificata nei comandi di migrazione. Inviare i messaggi allo spazio dei nomi premium in modo che possono essere elaborati dai ricevitori.

Dopo che i messaggi sono stati svuotati, eliminare lo spazio dei nomi standard.

>[!IMPORTANT]
> Dopo che sono stati svuotati i messaggi dallo spazio dei nomi standard, eliminare lo spazio dei nomi standard. Questo è importante perché la stringa di connessione che inizialmente definito a spazio dei nomi standard ora fa riferimento allo spazio dei nomi premium. È il Namespace standard non è più necessario. Eliminare lo spazio dei nomi standard che sono stati migrati aiuta a ridurre la confusione successive.

### <a name="how-much-downtime-do-i-expect"></a>Tempi di inattività previsti?
Il processo di migrazione è progettato per ridurre il tempo di inattività previsto per le applicazioni. Tempo di inattività viene ridotto usando la stringa di connessione utilizzate dalle applicazioni mittente e ricevitore in modo che punti al nuovo spazio dei nomi premium.

I tempi di inattività viene riscontrato dall'applicazione è limitata al tempo che necessario per aggiornare la voce DNS in modo che punti allo spazio dei nomi premium. Tempi di inattività è di circa 5 minuti.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>È necessario apportare eventuali modifiche alla configurazione durante la migrazione?
No, non sono presenti modifiche di configurazione o codice necessari per eseguire la migrazione. La stringa di connessione usata dalle applicazioni mittente e ricevitore per accedere il Namespace standard viene automaticamente eseguito il mapping per agire come un alias per lo spazio dei nomi premium.

### <a name="what-happens-when-i-abort-the-migration"></a>Cosa accade quando interrompe la migrazione?
La migrazione può essere interrotta tramite l'utilizzo di `Abort` comando o tramite il portale di Azure. 

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Portale di Azure

![Interrompere il flusso: abort sync][]
![Abort flow - interruzione completa][]

Quando il processo di migrazione viene interrotta, interrompe il processo di copia le entità (argomenti, sottoscrizioni e i filtri) dallo standard per lo spazio dei nomi premium e interrompe l'associazione.

La stringa di connessione non viene aggiornata in modo da puntare allo spazio dei nomi premium. Le applicazioni esistenti continuano a funzionare come prima di iniziare la migrazione.

Tuttavia, non eliminare le entità nello spazio dei nomi premium o eliminare lo spazio dei nomi premium. Se si è deciso di non procedere con la migrazione, eliminare manualmente le entità.

>[!IMPORTANT]
> Se si decide di interrompere la migrazione, eliminare il piano premium Namespace che è stato effettuato il provisioning per la migrazione in modo che non vengono addebitati per le risorse.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Non desidero avere svuotare i messaggi. Cosa devo fare?

Possono essere presenti messaggi inviati dalle applicazioni mittente e il commit nella risorsa di archiviazione nel Namespace standard durante la migrazione è in corso e poco prima che venga eseguito il commit della migrazione.

Durante la migrazione, non vengono copiati dallo standard/payload di dati effettivo del messaggio per lo spazio dei nomi premium. I messaggi devono essere svuotati manualmente e quindi inviati allo spazio dei nomi premium.

Tuttavia, se è possibile eseguire la migrazione durante una finestra di manutenzione/manutenzione pianificata e non si vuole svuotare manualmente e inviare i messaggi, seguire questi passaggi:

1. Arrestare le applicazioni mittente. Le applicazioni di ricezione elaborerà i messaggi che sono attualmente nello spazio dei nomi standard e si scaricherà la coda.
1. Dopo che le code e sottoscrizioni nel Namespace standard sono vuote, attenersi alla procedura descritta in precedenza per eseguire la migrazione dallo standard per lo spazio dei nomi premium.
1. Al termine della migrazione, è possibile riavviare le applicazioni mittente.
1. I mittenti e ricevitori ora si connetteranno automaticamente allo spazio dei nomi premium.

    >[!NOTE]
    > Non è necessario arrestare le applicazioni ricevitore per la migrazione.
    >
    > Al termine della migrazione, le applicazioni ricevitore saranno disconnettere dallo spazio dei nomi standard e automaticamente connettersi allo spazio dei nomi premium.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [differenze tra la messaggistica standard e premium](./service-bus-premium-messaging.md).
* Scopri le [aspetti del ripristino di emergenza geografico e la disponibilità elevata per il livello premium del Bus di servizio](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

[Pagina di destinazione della migrazione]: ./media/service-bus-standard-premium-migration/1.png
[Il programma di installazione dello spazio dei nomi]: ./media/service-bus-standard-premium-migration/2.png
[Configurare lo spazio dei nomi: creare spazio dei nomi premium]: ./media/service-bus-standard-premium-migration/3.png
[Configurare lo spazio dei nomi - scegliere nome migrazione post]: ./media/service-bus-standard-premium-migration/4.png
[Configurare lo spazio dei nomi - sincronizzazione entità - inizio]: ./media/service-bus-standard-premium-migration/5.png
[Stato dello spazio dei nomi - sincronizzazione entità - installazione]: ./media/service-bus-standard-premium-migration/8.png
[Passare lo spazio dei nomi - opzione di menu]: ./media/service-bus-standard-premium-migration/9.png
[Spazio dei nomi switch - operazione riuscita]: ./media/service-bus-standard-premium-migration/12.png

[Interrompere il flusso: sincronizzazione di interruzione]: ./media/service-bus-standard-premium-migration/abort1.png
[Interrompere il flusso - interruzione completa]: ./media/service-bus-standard-premium-migration/abort3.png
