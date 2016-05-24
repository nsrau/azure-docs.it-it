<properties
	pageTitle="Migrazione supportata dalla piattaforma di risorse IaaS dal modello classico al modello di Azure Resource Manager"
	description="Questo articolo illustra la gestione delle funzionalità del servizio di migrazione supportate dalla piattaforma in Azure Resource Manager"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Migrazione supportata dalla piattaforma di risorse IaaS dal modello classico al modello di Azure Resource Manager

Il supporto per le macchine virtuali in Azure Resource Manager è stato annunciato quasi un anno fa. In questo articolo sono disponibili altre informazioni sui miglioramenti del supporto e sulle funzionalità aggiuntive supportate. Sono disponibili anche indicazioni su come ottimizzare la connessione e ottenere la coesistenza dei due modelli di distribuzione nella sottoscrizione mediante i gateway da sito a sito per le reti virtuali. In questo articolo viene illustrato il modo in cui viene abilitata la migrazione di risorse IaaS dal modello classico al modello di Resource Manager.

## Obiettivo della migrazione

La nuova esperienza offre funzionalità straordinarie e sono disponibili nuove API nelle macchine virtuali, che probabilmente cambieranno in molti modi l'uso del cloud. Il rilascio del nuovo modello consente di distribuire, gestire e monitorare i servizi correlati in un gruppo di risorse. Resource Manager consente di distribuire applicazioni complesse mediante modelli, configura le macchine virtuali tramite le estensioni di macchina virtuale e incorpora la gestione degli accessi e l'uso di tag. Include anche una distribuzione parallela e scalabile per le macchine virtuali nei set di disponibilità. Il nuovo modello offre anche la gestione del ciclo di vita delle risorse di calcolo, rete e archiviazione, in modo indipendente. È incentrato anche sull'abilitazione della sicurezza per impostazione predefinita mediante l'imposizione di macchine virtuali in una rete virtuale.

Dal punto di vista delle funzionalità, quasi tutte le funzionalità sono supportate per le risorse di calcolo, rete e archiviazione in Azure Resource Manager, con alcune eccezioni, che verranno probabilmente risolte nei prossimi mesi. L'esperienza utente è inoltre in continuo miglioramento e vengono aggiunte sempre più funzionalità al portale di Azure per consentire l'interazione tra le esperienze.

A causa di questa nuova funzionalità e della base di distribuzione crescente nel nuovo Azure Resource Manager, si vuole consentire ai clienti di eseguire la migrazione delle distribuzioni esistenti nel modello classico.

>[AZURE.NOTE] Con questo annuncio viene resa disponibile l'anteprima pubblica del servizio di migrazione. Durante l'anteprima pubblica è consigliabile eseguire solo la migrazione dei carichi di lavoro di tipo sviluppo e test nella sottoscrizione di Azure.

## Modifiche all'automazione e agli strumenti dopo la migrazione

Si noti che una delle modifiche importanti da includere nella migrazione delle risorse dal modello classico al modello di Resource Manager consiste negli aggiornamenti all'automazione o agli strumenti esistenti, in modo da assicurarne il funzionamento anche dopo la migrazione delle risorse.

## Conseguenze della migrazione delle risorse IaaS dal modello classico al modello di Resource Manager

Prima di esaminare i dettagli, è necessaria una breve spiegazione della differenza tra le operazioni del piano dati e del piano di gestione sulle risorse IaaS. La comprensione di queste differenze è essenziale, perché illustra il modo in cui si prevede di supportare la migrazione.

- Piano di gestione: illustra le chiamate incluse nel piano di gestione o l'API per la modifica delle risorse. Ad esempio, la creazione di una macchina virtuale, il riavvio di una VM, l'aggiornamento di una rete virtuale con una subnet e così via. Tutte queste operazioni gestiscono le risorse in esecuzione, ma non influiscono direttamente sulla connessione alle istanze.
- Piano dati (applicazione): descrive la "fase di esecuzione" dell'applicazione stessa e prevede l'interazione con istanze che non passano attraverso l'API di Azure. L'accesso al sito Web o il pull dei dati da un server SQL o mongoDB in esecuzione verranno considerati parte del piano dati o dell'interazione con l'applicazione. Anche la copia di un BLOB da un account di archiviazione e l'accesso a un indirizzo IP pubblico per una connessione RDP o SSH alla macchina virtuale sono parte del piano dati. Queste operazioni mantengono in esecuzione l'applicazione nelle risorse di calcolo, rete e archiviazione.

>[AZURE.NOTE] In alcuni scenari di migrazione (per altri dettagli, vedere le configurazioni non supportate) le macchine virtuali verranno arrestate, deallocate e riavviate e si verificherà un breve tempo di inattività del piano dati.

## Ambiti della migrazione supportati

Durante l'anteprima pubblica vengono offerti due ambiti di migrazione, destinati principalmente alle risorse di calcolo e rete. Il supporto per la migrazione degli account di archiviazione è pianificato e verrà rilasciato a breve. Per consentire una migrazione senza problemi, tuttavia, gli account di archiviazione classici sono stati abilitati in modo da includere dischi per le VM di Resource Manager. Altre informazioni sono disponibili più avanti.

### Migrazione di macchine virtuali (non in una rete virtuale)

Nel modello di distribuzione Resource Manager la sicurezza delle applicazione viene imposta per impostazione predefinita. Tutte le macchine virtuali devono quindi trovarsi in una rete virtuale nel modello di Resource Manager. Come parte della migrazione verranno quindi riavviate le macchine virtuali (arresto, deallocazione e avvio). Per le reti virtuali sono disponibili un paio di opzioni:
- È possibile richiedere alla piattaforma di creare una nuova rete virtuale ed eseguire la migrazione della macchina virtuale nella nuova rete virtuale (oppure)
- È possibile eseguire la migrazione della macchina virtuale in una rete virtuale esistente in Resource Manager

>[AZURE.NOTE] In questo ambito di migrazione è possibile che le operazioni del "piano di gestione" e del "piano dati" non siano consentite per un determinato periodo di tempo durante la migrazione.

### Migrazione di macchine virtuali (in una rete virtuale)

In questo ambito, per la maggior parte delle configurazioni di VM, viene eseguita solo la migrazione dei metadati tra il modello classico e il modello di Resource Manager. Le macchine virtuali sottostanti sono in esecuzione nello stesso hardware, nella stessa rete e con le stesse risorse di archiviazione. Quando si fa riferimento alla migrazione dei metadati dal modello classico al modello di Resource Manager, è quindi possibile che le operazioni del "piano di gestione" non siano consentite per un determinato periodo di tempo durante la migrazione. Il piano dati, tuttavia, continuerà a funzionare, ovvero le applicazioni in esecuzione sulle macchine virtuali (modello classico) non subiranno alcun tempo di inattività durante la migrazione.

Al momento non sono supportate le configurazioni seguenti. Quando verrà aggiunto in futuro il rispettivo supporto, è possibile che alcune VM in questa configurazione subiscano tempi di inattività (arresto, deallocazione e riavvio):

-	Se un singolo servizio cloud include più di un set di disponibilità
-	Se sono presenti "uno o più set di disponibilità" e "VM non incluse in un set di disponibilità" in un singolo servizio cloud

>[AZURE.NOTE] In questo ambito di migrazione è possibile che le operazioni del "piano di gestione" non siano consentite per un determinato periodo di tempo durante la migrazione. Per alcune configurazioni speciali, come illustrato in precedenza, il "piano dati" subirà tempi di inattività.

### Account di archiviazione e migrazione

La migrazione degli account di archiviazione non è attualmente supportata per l'anteprima pubblica. Il supporto per la migrazione degli account di archiviazione è tuttavia previsto e dovrebbe essere disponibile a breve. La migrazione e il comportamento dei rispettivi account di archiviazione presentano due aspetti importanti.

- Consentendo la distribuzione di VM di Resource Manager in un account di archiviazione classico per permettere una migrazione senza problemi, si consente di distribuire VM di Resource Manager in un account di archiviazione classico. Questa funzionalità consente di eseguire la migrazione di risorse di calcolo e di rete indipendentemente dagli account di archiviazione.
- Come procedura consigliata per la migrazione degli account di archiviazione quando la migrazione di tali account è supportata, la piattaforma imporrà la separazione delle risorse di calcolo e di rete dagli account di archiviazione, per offrire un'esperienza senza problemi.

## Funzionalità e configurazioni non supportate

Al momento non sono supportate alcune funzionalità e configurazioni. Microsoft si impegna per rendere disponibile il supporto, ma la sezione seguente include indicazioni e pianificazioni relative a tali funzionalità e configurazioni.

### Funzionalità non supportate

Le funzionalità elencate di seguito non sono supportate per l'anteprima pubblica. È possibile rimuovere facoltativamente queste impostazioni, eseguire la migrazione delle macchine virtuali e quindi riabilitarle nel modello di distribuzione Resource Manager. Il supporto per la maggior parte di queste funzionalità è pianificato e verrà rilasciato non appena disponibile.

Provider di risorse | Funzionalità
---------- | ------------
Calcolo | Diagnostica di avvio
Calcolo | Dischi di macchine virtuali non associati
Calcolo | Immagini di macchina virtuale
Rete | IP riservati non associati [se non collegati a una macchina virtuale]. Gli IP riservati collegati alle VM sono supportati.
Rete | Gruppi di sicurezza di rete non associati [se non collegati a una rete virtuale o a un'interfaccia di rete]. I gruppi di sicurezza di rete a cui fanno riferimento le reti virtuali sono supportati.
Rete | ACL endpoint
Rete | Gateway di rete virtuale (da sito a sito, Express Route, da punto a sito)
Archiviazione | Account di archiviazione

### Configurazione non supportate

Le configurazioni elencate di seguito non sono supportate per l'anteprima pubblica. Più avanti sono disponibili alcune indicazioni. Il supporto per alcune configurazioni è pianificato e verrà rilasciato non appena disponibile.

Servizio | Configurazione | Raccomandazione
---------- | ------------ | ------------
Gestione risorse | Controllo degli accessi in base al ruolo per risorse classiche | Poiché l'URI delle risorse viene modificato dopo la migrazione, è consigliabile pianificare in anticipo gli aggiornamenti dei criteri RBAC che devono essere apportati dopo la migrazione.
Calcolo | Più subnet associate a una macchina virtuale | È consigliabile aggiornare la configurazione della subnet in modo che faccia riferimento solo a una subnet.
Calcolo | Macchine virtuali appartenenti a una rete virtuale, ma senza assegnazione esplicita di una subnet | È facoltativamente possibile eliminare la VM. Il supporto per questa funzionalità è attualmente pianificato.
Calcolo | Macchine virtuali con avvisi e criteri di ridimensionamento automatico | Attualmente la migrazione verrà eseguita e queste impostazioni verranno eliminate. È quindi consigliabile valutare l'ambiente prima di eseguire la migrazione. In alternativa, è anche possibile riconfigurare le impostazioni relative agli avvisi al termine della migrazione.
Calcolo | Estensioni di macchina virtuale XML [debugger di Visual Studio, WebDeploy e RemoteDebug] | Non verranno supportate. È consigliabile rimuovere queste estensioni dalla macchina virtuale per continuare la migrazione.
Calcolo | Servizi cloud che includono ruoli Web/di lavoro | Supporto attualmente non disponibile e in fase di pianificazione.
Rete | Reti virtuali contenenti macchine virtuali e ruoli Web/di lavoro | Supporto attualmente non disponibile e in fase di pianificazione.
Rete | Subnet contenenti spazi nel nome | Il supporto per questa funzionalità è pianificato.
Servizi app | Rete virtuale contenente ambienti del servizio app | Supporto attualmente non disponibile e in fase di pianificazione.
Servizi HDInsight | Rete virtuale contenente servizi HDInsight | Supporto attualmente non disponibile e in fase di pianificazione.
Servizi del ciclo di vita Dynamics | Rete virtuale contenente macchine virtuali gestite da Dynamics Lifecycle Services | Supporto attualmente non disponibile e in fase di pianificazione.

## Esperienza di migrazione

Prima di iniziare l'esperienza di migrazione, è consigliabile eseguire alcuni passaggi:

1. Assicurarsi che le risorse pianificate per la migrazione non si basino su funzionalità o configurazioni non supportate. Nella maggior parte dei casi la piattaforma rileva questi problemi e genera un errore.
2. Se sono presenti macchine virtuali che non si trovano in una rete virtuale, tali VM verranno arrestate e deallocate come parte dell'operazione di preparazione. Se non si vuole perdere l'indirizzo IP pubblico, è quindi necessario riservare l'indirizzo IP prima di attivare la preparazione. Se tuttavia le macchine virtuali si trovano in una rete virtuale, non verranno arrestate e deallocate.
3. Azure consiglia di configurare un ambiente di prova con un'impostazione analoga e di eseguire la migrazione di queste risorse prima della migrazione delle distribuzioni di produzione.
4. Pianificare la migrazione in orari non lavorativi, per gestire eventuali errori imprevisti durante la migrazione.
5. Scaricare la configurazione corrente delle macchine virtuali usando PowerShell, i comandi dell'interfaccia della riga di comando o le API REST per semplificare la convalida dopo la procedura di preparazione.
6. Aggiornare gli script di automazione/messa in funzione per gestire il modello di distribuzione Resource Manager prima di avviare la migrazione. È anche possibile eseguire facoltativamente operazioni GET quando lo stato delle risorse è Prepared.
7. Valutare i criteri RBAC configurati nelle risorse IaaS classiche e avere a disposizione un piano al completamento della migrazione.

Con l'annuncio dell'anteprima pubblica viene aggiunto il supporto per l'attivazione della migrazione mediante API REST, PowerShell e l'interfaccia della riga di comando di Azure. Il supporto del portale di Azure per la migrazione, in modo da consentire di visualizzare ed esaminare la migrazione dal modello classico al modello di Azure Resource Manager, è attualmente in fase di pianificazione.

![Screenshot che illustra il flusso di lavoro di migrazione](./media/virtual-machines-windows-migration-classic-resource-manager/migration-workflow.png)

1.	Preparazione
	* Questo è il primo passaggio del processo di migrazione. L'obiettivo di questo passaggio consiste nel simulare la trasformazione delle risorse IaaS dal modello classico al modello di Resource Manager e nel presentare la trasformazione in modo affiancato per consentirne la visualizzazione. Ecco il flusso dettagliato di azioni.
  * Selezionare la rete virtuale o il servizio ospitato, se non è una rete virtuale, da preparare per la migrazione.
  *	La piattaforma eseguirà sempre prima di tutto un'analisi dei dati in background per le risorse in fase di migrazione e restituirà un esito positivo/negativo se le risorse sono idonee per la migrazione.
	*	Se la risorsa non è idonea per la migrazione, verranno elencati i motivi per cui non ne è supportata la migrazione.
	* Se la risorsa è idonea per la migrazione, la piattaforma blocca prima di tutto le operazioni del piano di gestione per le risorse sottoposte a migrazione. Ad esempio, non sarà possibile aggiungere un disco dati a una VM sottoposta a migrazione.
  *	La piattaforma avvierà quindi la migrazione di metadati dal modello classico al modello di Resource Manager per le risorse sottoposte a migrazione.  
  *	Al termine dell'operazione di preparazione, sarà possibile visualizzare le risorse nel modello classico e nel modello di Resource Manager. Per ogni servizio cloud nel modello di distribuzione classica, verrà creato un nome di gruppo di risorse con formato `cloud-service-name>-migrated`.

2.	Verifica manuale o tramite script
  * In questo passaggio è possibile usare la configurazione scaricata in precedenza per convalidare la correttezza della migrazione. In alternativa, è anche possibile accedere al portale e controllare le proprietà e le risorse per convalidare la correttezza della migrazione dei metadati.
	* Se si esegue la migrazione di una rete virtuale, la maggior parte delle configurazioni di macchine virtuali non verrà riavviata. Per le applicazioni disponibili in tali VM, è possibile verificare che l'applicazione sia ancora attiva e in esecuzione.
	* È possibile eseguire test del monitoraggio o dell'automazione e degli script operativi per verificare se le macchine virtuali funzionano come previsto e se gli script aggiornati funzionano correttamente. Si noti che verranno supportate solo operazioni GET quando lo stato delle risorse è Prepared.
  * Non è previsto alcun intervallo di tempo impostato entro cui è necessario eseguire il "commit" della migrazione. In questo stato non sono previsti limiti di tempo. Si noti tuttavia che il piano di gestione verrà bloccato per queste risorse fino a quando non si decide di "interrompere" o eseguire il "commit".
  * Se si verificano problemi, è sempre possibile "interrompere" la migrazione e tornare al modello di distribuzione "classica". Al ritorno, verranno aperte le operazioni del piano di gestione sulle risorse, in modo da consentire di riprendere le operazioni normali sulle macchine virtuali nel modello di distribuzione classica.

3. Interruzione
  * Questo è un passaggio facoltativo che consente di annullare le modifiche al modello di distribuzione classica e di interrompere la migrazione. Si noti che questa operazione non può essere eseguita dopo l'attivazione dell'operazione "Commit". 	

4.	Commit
  * Al termine della convalida, è possibile eseguire il "commit" della migrazione e le risorse non verranno più visualizzate nel modello di distribuzione classica, ma saranno disponibili solo nel modello di distribuzione Resource Manager. Questo significa anche che le risorse sottoposte a migrazione possono essere gestite solo nel nuovo portale.
	* Se questa operazione ha esito negativo, è consigliabile riprovare a eseguirla un paio di volte. Se l'esito continua a essere negativo, creare un ticket di supporto o creare un post del forum con tag ClassicIaaSMigration [in questa pagina](https://social.msdn.microsoft.com/Forums/azure/it-IT/home?forum=WAVirtualMachinesforWindows).

>[AZURE.NOTE] Si noti che tutte le operazioni illustrate di seguito sono idempotenti. Se vengono rilevati errori diversi da una funzionalità non supportata o un errore di configurazione, è consigliabile provare a ripetere l'operazione di preparazione, interruzione o commit. La piattaforma proverà di nuovo a eseguire l'azione.

## Passaggi successivi
Dopo avere compreso i concetti fondamentali della migrazione di risorse IaaS classiche in Resource Manager, è possibile avviare la migrazione delle risorse.

- [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello classico al modello di Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Usare PowerShell per eseguire la migrazione di risorse IaaS dal modello classico al modello di Azure Resource Manager](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Usare l'interfaccia della riga di comando per eseguire la migrazione di risorse IaaS dal modello classico al modello di Azure Resource Manager](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Clonare una macchina virtuale classica in Azure Resource Manager usando script PowerShell della community](virtual-machines-windows-migration-scripts.md)

## Domande frequenti

**Il piano di migrazione influisce sui servizi o sulle applicazioni esistenti in esecuzione nelle macchine virtuali di Azure?**

No. Le macchine virtuali (modello classico) sono servizi con disponibilità generale completamente supportati ed è possibile continuare a sfruttare i vantaggi di queste risorse per espandere la propria presenza sul cloud di Microsoft Azure.

**Che cosa accade alle macchine virtuali se non è prevista la migrazione a breve?**

Le API classiche e il modello di risorse classico non vengono deprecati. Si vuole semplificare al massimo la migrazione, grazie alle funzionalità avanzate disponibili nel modello di distribuzione Resource Manager. È quindi consigliabile esaminare alcuni miglioramenti apportati come parte di IaaS in Resource Manager, come illustrato [qui](virtual-machines-windows-compare-deployment-models.md).

**Qual è l'impatto di questo piano di migrazione per gli strumenti esistenti?**

L'aggiornamento degli strumenti al modello di distribuzione Resource Manager è una delle modifiche più importanti da tenere in considerazione per i piani di migrazione.

**Quale durata avrà il tempo di inattività del piano di gestione?**

Dipende dal numero di risorse sottoposte a migrazione. Per le distribuzioni di dimensioni minori, ovvero con poche decine di macchine virtuali, l'intera migrazione end-to-end dovrebbe richiedere meno di un'ora. Per le distribuzioni su larga scala, tuttavia, ovvero con centinaia di macchine virtuali, possono essere necessarie alcune ore. Poiché il servizio è disponibile in anteprima pubblica, è consigliabile eseguirlo nella sottoscrizione di sviluppo o test per valutarne l'impatto.

**È possibile eseguire il rollback dopo il commit delle risorse sottoposte a migrazione in Resource Manager?**

È possibile interrompere la migrazione finché lo stato delle risorse è "Prepared". Il rollback non è supportato dopo il completamento della migrazione delle risorse mediante l'operazione Commit.

**È possibile eseguire il rollback della migrazione in caso di esito negativo dell'operazione Commit?**

Non è possibile interrompere la migrazione in caso di esito negativo dell'operazione Commit. Tutte le operazioni di migrazione, inclusa l'operazione Commit, sono idempotenti. È quindi consigliabile provare a ripetere l'operazione dopo un breve intervallo di tempo. Se l'errore persiste, creare un ticket di supporto o creare un post di forum con tag ClassicIaaSMigration [in questa pagina](https://social.msdn.microsoft.com/Forums/azure/it-IT/home?forum=WAVirtualMachinesforWindows).

**È necessario acquistare un altro circuito Express Route se occorre usare le risorse IaaS in Resource manager?**

No. È stata recentemente abilitata la [coesistenza di un circuito Express Route nel modello classico e nel modello di Resource Manager](../expressroute/expressroute-howto-coexist-resource-manager.md). Non è necessario acquistare un nuovo circuito Express Route se ne è già disponibile uno.

**È disponibile una guida di orientamento per quando verranno aggiunti scenari non supportati nell'elenco di migrazione?**

Il primo insieme di scenari è attualmente in fase di inserimento in H1 CY 2016. L'aggiunta di supporto per le funzionalità non supportate continuerà dopo la prima versione.

**Che cosa succede se sono stati configurati criteri di controllo degli accessi in base al ruolo per le risorse IaaS classiche?**

Durante la migrazione le risorse si trasformano dal modello classico al modello di Resource Manager. È quindi consigliabile pianificare in anticipo gli aggiornamenti dei criteri RBAC che devono essere apportati dopo la migrazione.

**Che cosa succede se sono attualmente in uso Azure Site Recovery o i servizi di backup di Azure?**

Il supporto per Azure Site Recovery e per il backup per le macchine virtuali in Resource Manager è stato aggiunto di recente. Siamo collaborando attivamente con questi team per abilitare la funzionalità per il supporto della migrazione di VM anche in Resource Manager. È attualmente consigliabile non eseguire la migrazione se si usano queste funzionalità.

**È possibile convalidare la sottoscrizione o le risorse per verificare se sono idonee per la migrazione?**

L'operazione di preparazione esegue attualmente una convalida implicita per le risorse in fase di preparazione per la migrazione. Nell'opzione di migrazione supportata dalla piattaforma il primo passaggio della preparazione della migrazione consiste nel verificare se le risorse sono idonee alla migrazione. Se la convalida ha esito negativo, non verrà eseguita alcuna operazione sulle risorse. È tuttavia previsto anche il rilascio di una nuova azione di convalida per semplificare la procedura.

**Che cosa succede se si verifica un errore di quota durante la preparazione delle risorse IaaS per la migrazione?**

È consigliabile interrompere la migrazione e quindi inviare una richiesta di supporto per aumentare le quote nell'area in cui si sta eseguendo la migrazione delle macchine virtuali. Dopo l'approvazione della richiesta relativa alla quota, avviare di nuovo l'esecuzione della procedura di migrazione.

**Come si segnala un problema se non si ha alcun contratto di supporto?**

Inserire i problemi e le domande sulla migrazione nel forum sulle Macchine virtuali con la parola chiave ClassicIaaSMigration [in questa pagina](https://social.msdn.microsoft.com/Forums/azure/it-IT/home?forum=WAVirtualMachinesforWindows). È consigliabile inserire tutte le domande in questo forum. Se tuttavia si ha un contratto di supporto, è possibile creare anche un ticket di supporto.

**Che cosa succede se non si vogliono usare i nomi scelti dalla piattaforma per i gruppi di risorse durante la migrazione?**

Verrà presto annunciato il supporto per lo spostamento di risorse tra gruppi di risorse. Quando tale funzionalità sarà supportata, sarà possibile spostare le risorse nel gruppo di risorse scelto.

**Che cosa succede se non si vogliono usare i nomi scelti dalla piattaforma per le risorse durante la migrazione?**

Tutti i nomi di risorse specificati in modo esplicito nel modello di distribuzione classica verranno mantenuti durante la migrazione. In alcuni casi verranno create nuove risorse. Ad esempio, verrà creata un'interfaccia di rete per ogni VM. Non è attualmente supportata la possibilità di controllare i nomi delle nuove risorse create durante la migrazione. Inviare il proprio voto per la funzionalità [in questa pagina](http://feedback.azure.com)

<!---HONumber=AcomDC_0511_2016-->