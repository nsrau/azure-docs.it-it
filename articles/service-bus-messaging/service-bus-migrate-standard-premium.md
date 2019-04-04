---
title: Eseguire la migrazione di Azure Service Bus Standard spazi dei nomi esistenti al livello Premium | Microsoft Docs
description: Guida alla migrazione di Azure Service Bus Standard spazi dei nomi esistenti alla versione Premium
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
ms.date: 02/18/2019
ms.author: aschhab
ms.openlocfilehash: 7b153c36e10f1d4e2be2a0cf42f998c31cb6473a
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896426"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-premium-tier"></a>Eseguire la migrazione di Azure Service Bus Standard spazi dei nomi esistenti al livello Premium

Il Bus di servizio di Azure offerto in precedenza, gli spazi dei nomi solo nel livello Standard. Queste sono impostazioni di multi-tenant che sono state ottimizzate per gli ambienti di sviluppo e una velocità effettiva bassa.

Di recente, il Bus di servizio di Azure è espanso per offrire il livello Premium che offre risorse dedicate per ogni spazio dei nomi per aumentare la velocità effettiva a un prezzo fisso che è ottimizzato per ambienti di produzione e di velocità effettiva elevata e latenza prevedibile che richiedono funzionalità aggiuntive dell'organizzazione.

Di seguito gli strumenti che abilita i spazi dei nomi di livello Standard per eseguire la migrazione al livello Premium.

>[!WARNING]
> La migrazione è concepita per lo spazio dei nomi del Bus di servizio Standard per essere ***aggiornato*** al livello Premium.
>
> Gli strumenti di migrazione ***non*** supporta il downgrade.
>[!NOTE]
> Questa migrazione deve verificarsi ***posto***.
>
> Ciò implica che applicazioni ricevitore e mittente esistente non richiedono qualsiasi modifica apportata al codice o configurazione.
>
> La stringa di connessione esistenti punteranno automaticamente al nuovo spazio dei nomi premium.
>
> Inoltre, tutte le entità nello spazio dei nomi Standard sono **copiate** nello spazio dei nomi Premium durante il processo di migrazione.
>
>
> Supportiamo ***1000 entità per ogni unità di messaggistica*** in Premium, in modo da identificare il numero di unità di messaggistica è necessario, avviare con il numero di entità che presentano sullo spazio dei nomi Standard corrente.

## <a name="migration-steps"></a>Passaggi della migrazione

>[!IMPORTANT]
> Esistono alcune limitazioni associati al processo di migrazione. Si invita completamente acquisire familiarità con i passaggi necessari per ridurre la possibilità di errori.

Il processo di migrazione dettagliate concrete dettagliate, vedere le guide seguenti.

I passaggi logici necessari sono:

1. Creare un nuovo spazio dei nomi Premium.
2. Associa lo spazio dei nomi Standard e Premium tra loro.
3. Entità di sincronizzazione (copia sulla) dal livello Standard per spazio dei nomi Premium
4. Eseguire il commit della migrazione
5. Svuotare le entità nello spazio dei nomi Standard usando il nome di post-migrazione dello spazio dei nomi
6. Eliminare lo spazio dei nomi Standard

>[!NOTE]
> Dopo la migrazione è stato eseguito il commit, è estremamente importante accedere il precedente spazio dei nomi Standard e svuotare le code e sottoscrizioni.
>
> Una volta che i messaggi sono stati svuotati possono essere inviati per il nuovo spazio dei nomi premium per essere elaborati dalle applicazioni ricevitore.
>
> Dopo che sono state svuotate le code e sottoscrizioni, è consigliabile eliminare il precedente spazio dei nomi Standard. È non sarà più necessario!

### <a name="migrate-using-azure-cli-or-powershell"></a>Eseguire la migrazione usando Azure o PowerShell

Per eseguire la migrazione dello spazio dei nomi del Bus di servizio Standard a Premium usando lo strumento della riga di comando di Azure o PowerShell, vedere la seguente Guida.

1. Creare un nuovo spazio dei nomi Premium del Bus di servizio. È possibile fare riferimento il [modelli di Azure Resource Manager](service-bus-resource-manager-namespace.md) oppure [usare il portale di Azure](service-bus-create-namespace-portal.md). Assicurarsi di selezionare "Premium" per il **serviceBusSku** parametro.

2. Impostare le seguenti variabili di ambiente per semplificare i comandi di migrazione.
   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the Premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the Standard namespace>
   ```

    >[!IMPORTANT]
    > Il nome di post-migrazione (post_migration_dns_name) da utilizzare per il precedente dopo la migrazione dello spazio dei nomi Standard di accesso. È necessario utilizzare tale a svuotare le code e le sottoscrizioni e quindi eliminare lo spazio dei nomi.

3. **Coppia** spazi dei nomi Standard e Premium e **Avvia sincronizzazione** utilizzando il seguente comando:

    ```
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


4. Controllare lo stato della migrazione utilizzando il seguente comando:
    ```
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    La migrazione viene considerata completa quando
    * MigrationState = "Attivo"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Succeeded"

    Questo comando Visualizza inoltre la configurazione della migrazione. . Ricontrollare per verificare che i valori siano impostati come in precedenza dichiarato.

    Inoltre, anche controllare lo spazio dei nomi Premium del portale per assicurarsi che tutte le code e argomenti sono stati creati e che corrispondano a ciò che esiste nello spazio dei nomi Standard.

5. Eseguire il commit della migrazione eseguendo il comando completo riportato di seguito
   ```
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-using-azure-portal"></a>Eseguire la migrazione tramite il portale di Azure

Migrazione tramite il portale di Azure ha lo stesso flusso logico come eseguire la migrazione usando i comandi. Vedere la seguente Guida per il processo passo a passo per eseguire la migrazione tramite il portale.

1. Selezionare il **'Migrate a Premium'** opzione di menu dal menu di navigazione nel riquadro sinistro. Scegliere il **Introduzione a** pulsante per procedere alla pagina successiva.
    ![Pagina di destinazione della migrazione][]

2. Completa **programma di installazione**.
   ![Il programma di installazione dello spazio dei nomi][]
   1. Creare e assegnare lo spazio dei nomi Premium per eseguire la migrazione dello spazio dei nomi Standard esistente a.
        ![Configurare lo spazio dei nomi: creare spazio dei nomi premium][]
   2. Selezionare il **"Nome dopo la migrazione"** per accedere lo spazio dei nomi Standard da una volta completata la migrazione.
        ![Configurare lo spazio dei nomi - scegliere nome migrazione post][]
   3. Fare clic su **'Next'** per procedere.
3. **Sincronizzazione** entità tra lo spazio dei nomi Standard e Premium.
    ![Configurare lo spazio dei nomi - sincronizzazione entità - inizio][]

   1. Fare clic su **Avvia sincronizzazione** per avviare la sincronizzazione delle entità.
   2. Fare clic su **'Sì'** nella finestra popup di conferma per avviare la sincronizzazione.
   3. Attendere finché il **sincronizzazione** è stata completata. Lo stato viene resa disponibile sulla barra di stato.
        ![Stato dello spazio dei nomi - sincronizzazione entità - installazione][]
        >[!IMPORTANT]
        > Se devi **Abort** per qualsiasi motivo, vedere il flusso di interruzione nella sezione Domande frequenti di questo documento.
   4. Una volta completata la sincronizzazione, fare clic sui **'Next'** nella parte inferiore della pagina.

4. Esaminare le modifiche nella pagina di riepilogo.
    ![Passare lo spazio dei nomi - opzione di menu][]

5. Fare clic su **'Completa la migrazione'** per invertire gli spazi dei nomi e completare la migrazione.
    ![Spazio dei nomi switch - operazione riuscita][]

## <a name="faqs"></a>Domande frequenti

### <a name="what-happens-when-the-migration-is-committed"></a>Cosa accade quando viene eseguito il commit della migrazione?

Dopo aver eseguito il commit della migrazione, la stringa di connessione che fa riferimento allo spazio dei nomi Standard farà riferimento allo spazio dei nomi Premium.

Le applicazioni mittente e ricevitore verranno disconnettersi il Namespace Standard e riconnettersi automaticamente allo spazio dei nomi Premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Cosa fare dopo aver completato lo Standard di migrazione a Premium?

Lo Standard per la migrazione di Premium garantisce che i metadati delle entità (argomenti, sottoscrizioni, i filtri, e così via) vengono copiati dallo Standard per spazio dei nomi Premium. I dati del messaggio che è stato eseguito il commit dello spazio dei nomi Standard non viene copiati tramite dallo Standard per spazio dei nomi Premium.

Per questo motivo, lo spazio dei nomi Standard può avere alcuni messaggi che sono stati inviati e il commit durante la migrazione in corso. Questi messaggi devono essere svuotati dal Namespace Standard manualmente e inviati tramite il Namespace Premium manualmente.

A tale scopo, si ***deve*** usare un'app console o uno script che svuotare le entità Standard dello spazio dei nomi usando la **nome DNS migrazione Post** di specificati nei comandi di migrazione e quindi inviare questi messaggi in il Namespace Premium, in modo che possono essere elaborati dai ricevitori.

Una volta che i messaggi sono stati svuotati, procedere al eliminare lo spazio dei nomi Standard.

>[!IMPORTANT]
> Si noti che dopo che sono stati svuotati i messaggi dallo spazio dei nomi Standard, si **necessario** eliminare lo spazio dei nomi Standard.
>
> Questo è importante perché la stringa di connessione che inizialmente definito a spazio dei nomi Standard ora fa effettivamente riferimento allo spazio dei nomi Premium. Non dover questo Namespace Standard più.
>
> Lo spazio dei nomi Standard che sono stati migrati consente l'eliminazione consente di ridurre la confusione in un secondo momento. 

### <a name="how-much-downtime-do-i-expect"></a>Tempi di inattività previsti?
Il processo di migrazione descritto in precedenza è progettato per ridurre il tempo di inattività previsto per le applicazioni. Questa operazione viene eseguita utilizzando la stringa di connessione utilizzate dalle applicazioni mittente e ricevitore in modo che punti al nuovo spazio dei nomi Premium.

Il tempo di inattività esperti per l'applicazione è limitata alla quantità di tempo impiegato per aggiornare la voce DNS in modo che punti allo spazio dei nomi Premium.

Ciò può essere considerata ***circa 5 minuti***.

### <a name="do-i-have-to-make-any-configuration-changes-while-performing-the-migration"></a>È necessario apportare eventuali modifiche alla configurazione durante l'esecuzione della migrazione?
No, non sono presenti modifiche di codice/configurazione necessari per eseguire la migrazione. La stringa di connessione usata dalle applicazioni mittente e ricevitore per il Namespace Standard di accesso viene automaticamente eseguito il mapping per agire come un **alias** per il Namespace Premium.

### <a name="what-happens-when-i-abort-the-migration"></a>Cosa accade quando interrompe la migrazione?
Migrazione può essere interrotti o utilizzando il comando 'Interrompi' tramite il portale di Azure. 

#### <a name="azure-cli-or-powershell"></a>Comando di Azure o PowerShell

    az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace

#### <a name="azure-portal"></a>Portale di Azure

![Interrompere il flusso: abort sincronizzazione][]
![Abort flow - interruzione completa][]

Quando il processo di migrazione viene interrotta, viene effettivamente interrompe il processo di copia sulle entità (argomenti, sottoscrizioni e i filtri) dal livello Standard per spazio dei nomi Premium e interrompe l'associazione.

La stringa di connessione **non è** aggiornato per puntare allo spazio dei nomi Premium. Le applicazioni esistenti continuano a funzionare come prima di iniziare la migrazione.

Tuttavia, si **non** Elimina le entità nello spazio dei nomi Premium o lo spazio dei nomi Premium se stesso. Questa operazione deve essere eseguita manualmente se si è deciso di non procedere con la migrazione dopo che tutti.

>[!IMPORTANT]
> Se si decide di interrompere la migrazione, eliminare il Namespace Premium che è stato effettuato il provisioning per la migrazione, in modo che non vengono addebitati per le risorse.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Non desidero avere svuotare i messaggi. Cosa devo fare?

Possono essere presenti messaggi inviati dalle applicazioni mittente e il commit nella risorsa di archiviazione nel Namespace Standard durante la migrazione sia in esecuzione e viene eseguito il commit immediatamente prima della migrazione.

Dato che durante la migrazione/payload di dati effettivo del messaggio non viene copiato tramite da Standard a Premium, questi nomi devono essere svuotati manualmente e quindi inviati allo spazio dei nomi Premium.

Se è possibile eseguire la migrazione durante una finestra di manutenzione pianificata/manutenzione e non vuole svuotare manualmente e inviare i messaggi, tuttavia, seguire il seguito questa procedura:

1. Arrestare le applicazioni mittente e consentire i ricevitori elaborare i messaggi che sono attualmente nello spazio dei nomi Standard e svuotare la coda.
2. Una volta le code e sottoscrizioni nel Namespace Standard sono vuote, attenersi alla procedura descritta in precedenza per eseguire la migrazione da Standard a spazio dei nomi Premium.
3. Al termine della migrazione, è possibile riavviare le applicazioni mittente.
4. I mittenti e ricevitori ora si connetteranno automaticamente allo spazio dei nomi Premium.

    >[!NOTE]
    > Il ricevitore non debba essere arrestato per la migrazione.
    >
    > Al termine della migrazione, i ricevitori verranno disconnettersi dallo spazio dei nomi Standard e automaticamente connettersi allo spazio dei nomi Premium.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [le differenze tra Standard e la messaggistica Premium](./service-bus-premium-messaging.md)
* Scopri gli aspetti di ripristino geografico Diaster e disponibilità elevata per il livello Premium del Bus di servizio [qui](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)

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
