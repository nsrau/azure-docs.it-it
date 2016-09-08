<properties
	pageTitle="Migrazione di risorse IaaS supportata dalla piattaforma dal modello classico al modello di Azure Resource Manager | Microsoft Azure"
	description="Questo articolo illustra la migrazione di risorse supportata dalla piattaforma dal modello classico al modello di Azure Resource Manager"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="mahthi"/>

# Migrazione di risorse IaaS supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager

Questo articolo illustra il modo in cui viene abilitata la migrazione di risorse dell'infrastruttura distribuita come servizio (IaaS) dal modello di distribuzione classica al modello di Azure Resource Manager. Altre informazioni su [funzionalità e vantaggi di Azure Resource Manager](../resource-group-overview.md). Sono disponibili indicazioni su come connettere le risorse dai due modelli di distribuzione che coesistono nella sottoscrizione mediante i gateway da sito a sito per le reti virtuali.

## Obiettivo della migrazione

Resource Manager consente di distribuire applicazioni complesse mediante modelli, configura le macchine virtuali tramite le estensioni di macchina virtuale e incorpora la gestione degli accessi e l'uso dei tag. Azure Resource Manager include anche una distribuzione parallela e scalabile per le macchine virtuali nei set di disponibilità. Il nuovo modello offre inoltre la gestione del ciclo di vita delle risorse di calcolo, rete e archiviazione, in modo indipendente. Infine, ci si concentra anche sull'abilitazione della sicurezza predefinita tramite l'imposizione di macchine virtuali in una rete virtuale.

In Azure Resource Manager sono supportate quasi tutte le funzionalità del modello di distribuzione classica per calcolo, rete e archiviazione. Per trarre vantaggio dalle nuove funzionalità di Azure Resource Manager, è possibile migrare le distribuzioni esistenti dal modello di distribuzione classica.

## Modifiche all'automazione e agli strumenti dopo la migrazione

Come parte della migrazione delle risorse dal modello di distribuzione classica al modello di distribuzione di Resource Manager, è necessario aggiornare l'automazione o gli strumenti esistenti per accertarsi che continuino a funzionare dopo la migrazione.

## Conseguenze della migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager

Prima di esaminare i dettagli, è necessario notare la differenza tra le operazioni del piano dati e del piano di gestione sulle risorse IaaS.

- Il *piano di gestione* descrive le chiamate incluse nel piano di gestione o l'API per la modifica delle risorse. Ad esempio, operazioni come la creazione di una VM, il riavvio di una VM e l'aggiornamento di una rete virtuale con una nuova subnet gestiscono le risorse in esecuzione. Non incidono direttamente sulla connessione alle istanze.
- Il *piano dati* (applicazione) descrive la "fase di esecuzione" dell'applicazione stessa e prevede l'interazione con istanze che non passano attraverso l'API di Azure. L'accesso al sito Web o il pull dei dati da un'istanza di SQL Server o da un server mongoDB in esecuzione verranno considerati parte del piano dati o dell'interazione con l'applicazione. Anche la copia di un BLOB da un account di archiviazione e l'accesso a un indirizzo IP pubblico per una connessione RDP o SSH alla macchina virtuale sono parte del piano dati. Queste operazioni mantengono in esecuzione l'applicazione nelle risorse di calcolo, rete e archiviazione.

>[AZURE.NOTE] In alcuni scenari di migrazione, la piattaforma Azure interrompe, dealloca e riavvia le macchine virtuali. Questa operazione comporta brevi tempi di inattività del piano dati.

## Ambiti di migrazione supportati

Esistono tre ambiti di migrazione che hanno come obiettivi principali il calcolo, la rete e l'archiviazione.

### Migrazione di macchine virtuali (non in una rete virtuale)

Nel modello di distribuzione di Resource Manager è applicata la sicurezza predefinita delle applicazioni. Nel modello di Resource Manager tutte le macchine virtuali devono trovarsi in una rete virtuale. La piattaforma Azure riavvia (`Stop`, `Deallocate`, e `Start`) le VM nell'ambito della migrazione. Per le reti virtuali sono disponibili due opzioni:

- È possibile richiedere alla piattaforma di creare una nuova rete virtuale ed eseguire la migrazione della macchina virtuale nella nuova rete virtuale.
- È possibile eseguire la migrazione della macchina virtuale in una rete virtuale esistente in Resource Manager.

>[AZURE.NOTE] In questo ambito di migrazione, le operazioni del piano di gestione e del piano dati potrebbero non essere consentite per un determinato periodo di tempo durante la migrazione.

### Migrazione di macchine virtuali (in una rete virtuale)

Per la maggior parte delle configurazioni di VM, viene eseguita solo la migrazione dei metadati tra il modello di distribuzione classica e il modello di Resource Manager. Le VM sottostanti sono in esecuzione nello stesso hardware, nella stessa rete e con la stessa risorsa di archiviazione. È possibile che le operazioni del "piano di gestione" non siano consentite per un determinato periodo di tempo durante la migrazione. Tuttavia il piano dati continua a funzionare. Ovvero le applicazioni in esecuzione sulle VM (modello classico) non subiscono alcun tempo di inattività durante la migrazione.

Le seguenti configurazioni non sono attualmente supportate. Se in futuro verrà aggiunto il rispettivo supporto, è possibile che alcune VM in questa configurazione subiscano tempi di inattività (arresto, deallocazione e riavvio).

-	Un singolo servizio cloud include più di un set di disponibilità.
-	In un singolo servizio cloud sono presenti uno o più set di disponibilità e VM non incluse in un set di disponibilità.

>[AZURE.NOTE] In questo ambito di migrazione è possibile che le operazioni del piano di gestione non siano consentite per un determinato periodo di tempo durante la migrazione. Per alcune configurazioni, come illustrato in precedenza, il piano dati subirà tempi di inattività.

### Migrazione degli account di archiviazione

Per consentire una migrazione senza problemi, è possibile distribuire VM di Resource Manager in un account di archiviazione classico. Questa funzionalità consente di eseguire la migrazione di risorse di calcolo e di rete indipendentemente dagli account di archiviazione. Una volta eseguita la migrazione su macchine virtuali e rete virtuale, sarà necessario fare lo stesso sugli account di archiviazione per completare la procedura di migrazione.

>[AZURE.NOTE] Il modello di distribuzione di Resource Manager non prevede il concetto di immagini e dischi classici. Quando viene migrato l'account di archiviazione, le immagini e i dischi classici non sono visualizzati nello stack di Resource Manager ma i VHD di supporto rimangono nell'account di archiviazione.

## Funzionalità e configurazioni non supportate

Alcune funzionalità e configurazioni non sono attualmente supportate. Nelle sezioni seguenti sono descritti i consigli in merito.

### Funzionalità non supportate

Le seguenti funzionalità non sono attualmente supportate. È possibile rimuovere facoltativamente queste impostazioni, eseguire la migrazione delle macchine virtuali e quindi riabilitare le impostazioni nel modello di distribuzione di Resource Manager.

Provider di risorse | Funzionalità
---------- | ------------
Calcolo | Dischi di macchine virtuali non associati.
Calcolo | Immagini di macchine virtuali.
Rete | ACL endpoint.
Rete | Gateway di rete virtuale (da sito a sito, Azure ExpressRoute, gateway applicazione, da punto a sito).
Rete | Reti virtuali usando il peering delle reti virtuali. (Eseguire la migrazione della rete virtuale a ARM, quindi eseguire il peering) Ulteriori informazioni su [Peering reti virtuali](../virtual-network/virtual-network-peering-overview.md).
Rete | Profili di Gestione traffico.

### Configurazioni non supportate

Le seguenti configurazioni non sono attualmente supportate.

Service | Configurazione | Raccomandazione
---------- | ------------ | ------------
Gestione risorse | Controllo degli accessi in base al ruolo (RBAC) per le risorse classiche | Poiché l'URI delle risorse viene modificato dopo la migrazione, è consigliabile pianificare gli aggiornamenti dei criteri RBAC che devono essere eseguiti dopo la migrazione.
Calcolo | Più subnet associate a una macchina virtuale | Aggiornare la configurazione delle subnet in modo che faccia riferimento solo alle subnet.
Calcolo | Macchine virtuali appartenenti a una rete virtuale, ma senza assegnazione esplicita di una subnet | È facoltativamente possibile eliminare la VM.
Calcolo | Macchine virtuali con avvisi e criteri di ridimensionamento automatico | La migrazione viene eseguita e queste impostazioni vengono eliminate. È quindi consigliabile valutare l'ambiente prima di eseguire la migrazione. In alternativa, è possibile riconfigurare le impostazioni relative agli avvisi al termine della migrazione.
Calcolo | Estensioni XML della VM (Visual Studio Debugger, Web Deploy e Remote Debugging) | Questa operazione non è supportata. È consigliabile rimuovere queste estensioni dalla macchina virtuale per continuare la migrazione.
Calcolo | Diagnostica di avvio con archiviazione Premium | Disabilitare la funzionalità di diagnostica di avvio per le VM prima di proseguire con la migrazione. Sarà possibile riabilitare la diagnostica di avvio nello stack di Resource Manager al termine della migrazione. Inoltre, i BLOB in uso per le schermate e i registri seriali devono essere eliminati in modo da non ricevere addebiti in relazione a essi.
Calcolo | Servizi cloud che includono ruoli Web/di lavoro | Non supportato attualmente.
Rete | Reti virtuali contenenti macchine virtuali e ruoli Web/di lavoro | Non supportato attualmente.
Servizio app di Azure | Rete virtuale contenente ambienti del servizio app | Non supportato attualmente.
HDInsight di Azure | Rete virtuale contenente servizi HDInsight | Non supportato attualmente.
Servizi del ciclo di vita Microsoft Dynamics | Rete virtuale contenente macchine virtuali gestite da Dynamics Lifecycle Services | Non supportato attualmente.

## Esperienza di migrazione

Prima di iniziare l'esperienza di migrazione, si consiglia di:

- Assicurarsi che le risorse da migrare non usino funzionalità o configurazioni non supportate. Generalmente la piattaforma rileva questi problemi e genera un errore.
- Se sono presenti macchine virtuali che non si trovano in una rete virtuale, tali VM verranno arrestate e deallocate come parte dell'operazione di preparazione. Se non si vuole perdere l'indirizzo IP pubblico, è necessario riservare l'indirizzo IP prima di avviare l'operazione di preparazione. Se tuttavia le VM si trovano in una rete virtuale, non vengono arrestate e deallocate.
- Pianificare la migrazione in orari non lavorativi, per gestire eventuali errori imprevisti durante la migrazione.
- Scaricare la configurazione corrente delle VM usando PowerShell, i comandi dell'interfaccia della riga di comando o le API REST per semplificare la convalida dopo la procedura di preparazione.
- Aggiornare gli script di automazione/messa in funzione per gestire il modello di distribuzione di Resource Manager prima di avviare la migrazione. È possibile eseguire facoltativamente operazioni GET quando lo stato delle risorse è Prepared.
- Valutare i criteri RBAC configurati nelle risorse IaaS classiche e preparare un piano per la fase successiva al completamento della migrazione.

Il flusso di lavoro della migrazione è il seguente:

![Screenshot che illustra il flusso di lavoro di migrazione](./media/virtual-machines-windows-migration-classic-resource-manager/migration-workflow.png)

>[AZURE.NOTE] Tutte le operazioni descritte nelle sezioni seguenti sono idempotenti. Se vengono rilevati errori diversi da una funzionalità non supportata o un errore di configurazione, è consigliabile provare a ripetere l'operazione di preparazione, interruzione o commit. La piattaforma Azure tenterà di ripetere l'azione.

### Convalida

L'operazione di convalida è il primo passaggio del processo di migrazione. L'obiettivo di tale passaggio è analizzare i dati in background per le risorse in fase di migrazione, restituendo un esito positivo/negativo se le risorse sono idonee per la migrazione.

Selezionare la rete virtuale o il servizio ospitato, se non è una rete virtuale, da convalidare per la migrazione.

* Se una risorsa non è idonea per la migrazione, la piattaforma Azure mostra un elenco di tutti i motivi alla base di ciò.

### Preparazione

L'operazione di preparazione è il secondo passaggio del processo di migrazione. L'obiettivo di questo passaggio consiste nel simulare la trasformazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager e nel presentare la trasformazione in modo affiancato per consentirne la visualizzazione.

Selezionare la rete virtuale o il servizio ospitato, se non è una rete virtuale, da preparare per la migrazione.

* Se la risorsa non è idonea per la migrazione, la piattaforma Azure arresta il processo di migrazione ed elenca il motivo per cui l'operazione di preparazione non è riuscita.
* Se una risorsa è idonea per la migrazione, la piattaforma Azure blocca prima di tutto le operazioni del piano di gestione per le risorse in fase di migrazione. Ad esempio, non è possibile aggiungere un disco dati a una VM in fase di migrazione.

La piattaforma Azure avvia quindi la migrazione dei metadati dal modello di distribuzione classica al modello di Azure Resource Manager per le risorse in fase di migrazione.

Al termine dell'operazione di preparazione, è possibile visualizzare le risorse nel modello di distribuzione classica e nel modello di Resource Manager. Per ogni servizio cloud nel modello di distribuzione classica, la piattaforma Azure crea un nome di gruppo di risorse nel formato `cloud-service-name>-migrated`.

>[AZURE.NOTE] Le macchine virtuali che non sono in una rete virtuale classica vengono interrotte e deallocate in questa fase della migrazione.

### Verifica (manuale o tramite script)

Nel passaggio di verifica è possibile usare la configurazione scaricata in precedenza per convalidare la correttezza della migrazione. In alternativa, è possibile accedere al portale e controllare le proprietà e le risorse per convalidare la correttezza della migrazione dei metadati.

Se si esegue la migrazione di una rete virtuale, la maggior parte delle configurazioni delle macchine virtuali non viene riavviata. Per le applicazioni disponibili in tali VM, è possibile verificare che l'applicazione sia ancora attiva e in esecuzione.

È possibile eseguire test del monitoraggio o dell'automazione e degli script operativi per verificare se le macchine virtuali funzionano come previsto e se gli script aggiornati funzionano correttamente. Quando lo stato delle risorse è Prepared, sono supportate solo le operazioni GET .

Non esiste alcun intervallo di tempo impostato entro cui è necessario eseguire il commit della migrazione. In questo stato non sono previsti limiti di tempo. Tuttavia, il piano di gestione viene bloccato per queste risorse fino a quando non si decide di interrompere o eseguire il commit.

Se si verificano problemi, è sempre possibile interrompere la migrazione e tornare al modello di distribuzione classica. Al ritorno, la piattaforma di Azure apre le operazioni del piano di gestione sulle risorse, in modo da consentire di riprendere le operazioni normali sulle VM nel modello di distribuzione classica.

### Interruzione

L'interruzione è un passaggio facoltativo che consente di annullare le modifiche al modello di distribuzione classica e di arrestare la migrazione.

>[AZURE.NOTE] Questa operazione non può essere eseguita dopo l'avvio dell'operazione di commit.

### Commit

Dopo aver completato la convalida, è possibile eseguire il commit della migrazione. Le risorse non sono più mostrate nel modello di distribuzione classica e sono disponibili solo nel modello di distribuzione di Resource Manager. Le risorse migrate possono essere gestite solo nel nuovo portale.

>[AZURE.NOTE] Si tratta di un'operazione idempotente. In caso di esito negativo, si consiglia di riprovare. Se l'esito continua a essere negativo, creare un ticket di supporto o un post con il tag ClassicIaaSMigration nel [forum sulle VM](https://social.msdn.microsoft.com/Forums/azure/it-IT/home?forum=WAVirtualMachinesforWindows).

## Domande frequenti

**Il piano di migrazione influisce sui servizi o sulle applicazioni esistenti in esecuzione nelle macchine virtuali di Azure?**

No. Le VM (classiche) sono servizi completamente supportati con disponibilità generale. È possibile continuare a usare queste risorse per sfruttare meglio la presenza su Microsoft Azure.

**Che cosa accade alle macchine virtuali se non è prevista la migrazione a breve?**

Le API classiche e il modello di risorse classico non saranno deprecati. L'obiettivo è quello di semplificare la migrazione, tenendo conto delle funzionalità avanzate disponibili nel modello di distribuzione di Resource Manager. È quindi consigliabile esaminare [alcuni dei miglioramenti](virtual-machines-windows-compare-deployment-models.md) apportati come parte di IaaS in Resource Manager.

**Qual è l'impatto di questo piano di migrazione per gli strumenti esistenti?**

L'aggiornamento degli strumenti al modello di distribuzione di Resource Manager è una delle modifiche più importanti da tenere in considerazione per i piani di migrazione.

**Quale durata avrà il tempo di inattività del piano di gestione?**

Dipende dal numero di risorse sottoposte a migrazione. Per le distribuzioni di dimensioni minori, ovvero con poche decine di VM, l'intera migrazione dovrebbe richiedere meno di un'ora. Per le distribuzioni su larga scala (centinaia di VM), la migrazione può richiedere alcune ore.

**È possibile eseguire il rollback dopo il commit delle risorse sottoposte a migrazione in Resource Manager?**

È possibile interrompere la migrazione finché lo stato delle risorse è Prepared. Il rollback non è supportato dopo il completamento della migrazione delle risorse mediante l'operazione di commit.

**È possibile eseguire il rollback della migrazione in caso di esito negativo dell'operazione di commit?**

Non è possibile interrompere la migrazione in caso di esito negativo dell'operazione di commit. Tutte le operazioni di migrazione, inclusa l'operazione Commit, sono idempotenti. È quindi consigliabile provare a ripetere l'operazione dopo breve tempo. Se l'errore persiste, creare un ticket di supporto o un post con il tag ClassicIaaSMigration nel [forum sulle VM](https://social.msdn.microsoft.com/Forums/azure/it-IT/home?forum=WAVirtualMachinesforWindows).

**In caso si debba usare le risorse IaaS in Resource Manager, è necessario acquistare un altro circuito ExpressRoute?**

No. È stata recentemente abilitata la [coesistenza di un circuito ExpressRoute tra il modello di distribuzione classica e il modello di distribuzione Resource Manager](../expressroute/expressroute-howto-coexist-resource-manager.md). Se si dispone già di un circuito ExpressRoute, non è necessario acquistare un nuovo.

**Che cosa succede se sono stati configurati criteri di controllo degli accessi in base al ruolo per le risorse IaaS classiche?**

Durante la migrazione le risorse si trasformano dal modello di distribuzione classica ad Azure Resource Manager. È quindi consigliabile pianificare gli aggiornamenti dei criteri RBAC che devono essere apportati dopo la migrazione.

**Che cosa succede se sono attualmente in uso Azure Site Recovery o i servizi di backup di Azure?**

Il supporto per Azure Site Recovery e per il backup per le VM in Resource Manager è stato aggiunto di recente. Siamo lavorando per abilitare anche la capacità di supportare la migrazione delle VM in Resource Manager. È attualmente consigliabile non eseguire la migrazione se si usano queste funzionalità.

**È possibile convalidare la sottoscrizione o le risorse per verificare se sono idonee per la migrazione?**

Sì. Nell'opzione di migrazione supportata dalla piattaforma il primo passaggio per la preparazione della migrazione consiste nel verificare se le risorse sono idonee alla migrazione. In caso di esito negativo dell'operazione di convalida, si ricevono tutti i messaggi per tutti i motivi che impediscono il completamento della migrazione.

**Che cosa succede se si verifica un errore di quota durante la preparazione delle risorse IaaS per la migrazione?**

Si consiglia di interrompere la migrazione e quindi inviare una richiesta di supporto per aumentare le quote nell'area in cui si sta eseguendo la migrazione delle macchine virtuali. Dopo l'approvazione della richiesta relativa alla quota, è possibile avviare di nuovo l'esecuzione della procedura di migrazione.

**Come si segnala un problema?**

Ogni utente è invitato a pubblicare problemi e domande sulla migrazione nel [forum sulle VM](https://social.msdn.microsoft.com/Forums/azure/it-IT/home?forum=WAVirtualMachinesforWindows) con la parola chiave ClassicIaaSMigration. Si consiglia di pubblicare tutte le eventuali domande in questo forum. Se si dispone di un contratto di supporto, è possibile anche registrare un ticket di supporto.

**Che cosa succede se non si vogliono usare i nomi scelti dalla piattaforma per le risorse durante la migrazione?**

Tutti i nomi di risorse specificati in modo esplicito nel modello di distribuzione classica vengono mantenuti durante la migrazione. In alcuni casi vengono create nuove risorse. Ad esempio, viene creata un'interfaccia di rete per ogni VM. Non è attualmente supportata la possibilità di controllare i nomi delle nuove risorse create durante la migrazione. Nel [forum di commenti e suggerimenti su Azure](http://feedback.azure.com) è possibile registrare i voti per questa funzionalità.

**Un messaggio visualizzato informa che*lo stato generale dell'agente VM è segnalato come Non pronto. Di conseguenza, non è possibile eseguire la migrazione della VM. Il messaggio invita a controllare che l'agente VM segnali lo stato Pronto*. In un altro messaggio si legge che *la VM contiene un'estensione di cui non segnala lo stato. Di conseguenza, non è possibile eseguire la migrazione di questa VM.***

Questo messaggio viene ricevuto quando la VM non dispone di connettività in uscita a Internet. L'agente VM usa la connettività in uscita per raggiungere l'account di archiviazione di Azure in modo da aggiornare lo stato dell'agente ogni 5 minuti.

## Passaggi successivi
Dopo avere compreso i concetti fondamentali della migrazione di risorse IaaS classiche in Resource Manager, è possibile avviare la migrazione delle risorse.

- [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Usare PowerShell per eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Usare l'interfaccia della riga di comando per eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Clonare una macchina virtuale classica in Azure Resource Manager usando script PowerShell della community](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0824_2016-->