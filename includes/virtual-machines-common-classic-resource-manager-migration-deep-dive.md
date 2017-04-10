## <a name="meaning-of-migration-of-iaas-resources-from-classic-to-resource-manager"></a>Conseguenze della migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager
Prima di esaminare i dettagli, è necessario notare la differenza tra le operazioni del piano dati e del piano di gestione sulle risorse IaaS.

* Il *piano di gestione/controllo* descrive le chiamate incluse nel piano di gestione/controllo o l'API per la modifica delle risorse. Ad esempio, operazioni come la creazione di una VM, il riavvio di una VM e l'aggiornamento di una rete virtuale con una nuova subnet gestiscono le risorse in esecuzione. Non incidono direttamente sulla connessione alle istanze.
* *piano dati* (applicazione) descrive la "fase di esecuzione" dell'applicazione stessa e prevede l'interazione con istanze che non passano attraverso l'API di Azure. L'accesso al sito Web o il pull dei dati da un'istanza di SQL Server o da un server mongoDB in esecuzione verranno considerati parte del piano dati o dell'interazione con l'applicazione. Anche la copia di un BLOB da un account di archiviazione e l'accesso a un indirizzo IP pubblico per una connessione RDP o SSH alla macchina virtuale sono parte del piano dati. Queste operazioni mantengono in esecuzione l'applicazione nelle risorse di calcolo, rete e archiviazione.

![Screenshot che illustra la differenza tra piano di gestione/controllo e piano dati](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)

> [!NOTE]
> In alcuni scenari di migrazione, la piattaforma Azure interrompe, dealloca e riavvia le macchine virtuali. Questa operazione comporta brevi tempi di inattività del piano dati.
>
>

## <a name="the-migration-experience"></a>Esperienza di migrazione
Prima di iniziare l'esperienza di migrazione, si consiglia di:

* Assicurarsi che le risorse da migrare non usino funzionalità o configurazioni non supportate. Generalmente la piattaforma rileva questi problemi e genera un errore.
* Se sono presenti macchine virtuali che non si trovano in una rete virtuale, tali VM verranno arrestate e deallocate come parte dell'operazione di preparazione. Se non si vuole perdere l'indirizzo IP pubblico, è necessario riservare l'indirizzo IP prima di avviare l'operazione di preparazione. Se tuttavia le VM si trovano in una rete virtuale, non vengono arrestate e deallocate.
* Pianificare la migrazione in orari non lavorativi, per gestire eventuali errori imprevisti durante la migrazione.
* Scaricare la configurazione corrente delle VM usando PowerShell, i comandi dell'interfaccia della riga di comando o le API REST per semplificare la convalida dopo la procedura di preparazione.
* Aggiornare gli script di automazione/messa in funzione per gestire il modello di distribuzione di Resource Manager prima di avviare la migrazione. È possibile eseguire facoltativamente operazioni GET quando lo stato delle risorse è Prepared.
* Valutare i criteri RBAC configurati nelle risorse IaaS classiche e preparare un piano per la fase successiva al completamento della migrazione.

Il flusso di lavoro della migrazione è il seguente:

![Screenshot che illustra il flusso di lavoro di migrazione](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> Tutte le operazioni descritte nelle sezioni seguenti sono idempotenti. Se vengono rilevati errori diversi da una funzionalità non supportata o un errore di configurazione, è consigliabile provare a ripetere l'operazione di preparazione, interruzione o commit. La piattaforma Azure tenterà di ripetere l'azione.
>
>

### <a name="validate"></a>Convalida
L'operazione di convalida è il primo passaggio del processo di migrazione. L'obiettivo di tale passaggio è analizzare i dati in background per le risorse in fase di migrazione, restituendo un esito positivo/negativo se le risorse sono idonee per la migrazione.

Selezionare la rete virtuale o il servizio ospitato, se non è una rete virtuale, da convalidare per la migrazione.

* Se una risorsa non è idonea per la migrazione, la piattaforma Azure mostra un elenco di tutti i motivi per cui non è supportata.

Durante la convalida dei servizi di archiviazione, l'account sottoposto a migrazione sarà disponibile in un gruppo di risorse con nome corrispondente a quello dell'account di archiviazione, a cui è stato aggiunto il termine "-Migrated".  Se, ad esempio, l'account di archiviazione è denominato "mystorage", la risorsa abilitata per Azure Resource Manager sarà disponibile in un gruppo di risorse denominato "mystorage-Migrated" e conterrà un account di archiviazione denominato "mystorage".

### <a name="prepare"></a>Preparazione
L'operazione di preparazione è il secondo passaggio del processo di migrazione. L'obiettivo di questo passaggio consiste nel simulare la trasformazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager e nel presentare la trasformazione in modo affiancato per consentirne la visualizzazione.

Selezionare la rete virtuale o il servizio ospitato, se non è una rete virtuale, da preparare per la migrazione.

* Se la risorsa non è idonea per la migrazione, la piattaforma Azure arresta il processo di migrazione ed elenca il motivo per cui l'operazione di preparazione non è riuscita.
* Se una risorsa è idonea per la migrazione, la piattaforma Azure blocca prima di tutto le operazioni del piano di gestione per le risorse in fase di migrazione. Ad esempio, non è possibile aggiungere un disco dati a una VM in fase di migrazione.

La piattaforma Azure avvia quindi la migrazione dei metadati dal modello di distribuzione classica al modello di Azure Resource Manager per le risorse in fase di migrazione.

Al termine dell'operazione di preparazione, è possibile visualizzare le risorse nel modello di distribuzione classica e nel modello Resource Manager. Per ogni servizio cloud nel modello di distribuzione classica, la piattaforma Azure crea un nome di gruppo di risorse nel formato `cloud-service-name>-Migrated`.

> [!NOTE]
> Non è possibile selezionare il nome del gruppo di risorse creato per le risorse migrate (ad esempio "-Migrated"), ma, al termine della migrazione, è possibile usare la funzionalità di spostamento di Azure Resource Manager per spostare le risorse in qualsiasi gruppo di risorse desiderato. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../articles/resource-group-move-resources.md)

Ecco due schermate che mostrano il risultato dopo un'operazione di preparazione avvenuta con successo. La prima schermata mostra un gruppo di risorse che contiene il servizio cloud originale. La seconda schermata mostra il nuovo gruppo di risorse "-Migrated" che contiene le risorse equivalenti di Azure Resource Manager.

![Schermata che mostra il servizio cloud classico del portale](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Schermata che mostra le risorse Azure Resource Manager del portale in preparazione](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

> [!NOTE]
> Le macchine virtuali che non sono in una rete virtuale classica vengono interrotte e deallocate in questa fase della migrazione.
>
>

### <a name="check-manual-or-scripted"></a>Verifica (manuale o tramite script)
Nel passaggio di verifica è possibile usare la configurazione scaricata in precedenza per convalidare la correttezza della migrazione. In alternativa, è possibile accedere al portale e controllare le proprietà e le risorse per convalidare la correttezza della migrazione dei metadati.

Se si esegue la migrazione di una rete virtuale, la maggior parte delle configurazioni delle macchine virtuali non viene riavviata. Per le applicazioni disponibili in tali VM, è possibile verificare che l'applicazione sia ancora attiva e in esecuzione.

È possibile eseguire test del monitoraggio o dell'automazione e degli script operativi per verificare se le macchine virtuali funzionano come previsto e se gli script aggiornati funzionano correttamente. Quando lo stato delle risorse è Prepared, sono supportate solo le operazioni GET .

Non esiste alcun intervallo di tempo impostato entro cui è necessario eseguire il commit della migrazione. In questo stato non sono previsti limiti di tempo. Tuttavia, il piano di gestione viene bloccato per queste risorse fino a quando non si decide di interrompere o eseguire il commit.

Se si verificano problemi, è sempre possibile interrompere la migrazione e tornare al modello di distribuzione classica. Al ritorno, la piattaforma di Azure apre le operazioni del piano di gestione sulle risorse, in modo da consentire di riprendere le operazioni normali sulle VM nel modello di distribuzione classica.

### <a name="abort"></a>Interruzione
L'interruzione è un passaggio facoltativo che consente di annullare le modifiche al modello di distribuzione classica e di arrestare la migrazione.

> [!NOTE]
> Questa operazione non può essere eseguita dopo l'avvio dell'operazione di commit.     
>
>

### <a name="commit"></a>Commit
Dopo aver completato la convalida, è possibile eseguire il commit della migrazione. Le risorse non sono più mostrate nel modello di distribuzione classica e sono disponibili solo nel modello di distribuzione Resource Manager. Le risorse migrate possono essere gestite solo nel nuovo portale.

> [!NOTE]
> Si tratta di un'operazione idempotente. In caso di esito negativo, si consiglia di riprovare. Se l'esito continua a essere negativo, creare un ticket di supporto o un post con il tag ClassicIaaSMigration nel [forum sulle VM](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).
>
>
<br>
Ecco un diagramma di flusso dei passaggi in un processo di migrazione

![Schermata che mostra i passaggi di migrazione](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-classic-to-azure-resource-manager-resources"></a>Conversione delle risorse della distribuzione classica in risorse Azure Resource Manager
Nella tabella seguente è disponibile la rappresentazione classica e di Resource Manager delle risorse. Le seguenti funzionalità e risorse non sono attualmente supportate.

| Rappresentazione classica | Rappresentazione in Resource Manager | Note dettagliate |
| --- | --- | --- |
| Nome del servizio cloud |Nome DNS |Durante la migrazione, viene creato un nuovo gruppo di risorse per ogni servizio cloud con il modello di denominazione `<cloudservicename>-migrated`. Questo gruppo di risorse contiene tutte le risorse. Il nome del servizio cloud diventa un nome DNS associato all'indirizzo IP pubblico. |
| Macchina virtuale |Macchina virtuale |Le proprietà specifiche della VM vengono migrate senza variazioni. Alcune informazioni su osProfile, come il nome del computer, non sono state archiviate nel modello di distribuzione classica e quindi rimarranno vuote dopo la migrazione. |
| Risorse del disco collegate alla VM |Dischi impliciti collegati alla VM |I dischi non vengono modellati come risorse di primo livello nel modello di distribuzione di Resource Manager. Ne verrà eseguita la migrazione come dischi impliciti della VM. Attualmente sono supportati solo i dischi collegati a una VM. Le VM di Resource Manager ora possono usare gli account di archiviazione classica, che consente di eseguire facilmente la migrazione dei dischi senza aggiornamenti. |
| Estensioni di VM |Estensioni di VM |Viene eseguita la migrazione dal modello di distribuzione classica di tutte le estensioni di risorse, a eccezione delle estensioni XML. |
| Certificati di macchine virtuali |Certificati nell'insieme di credenziali delle chiavi di Azure |Se un servizio cloud contiene i certificati di servizio, viene creato un nuovo insieme di credenziali delle chiavi di Azure per ogni servizio cloud e vengono spostati i certificati nell'insieme di credenziali delle chiavi. Le VM vengono aggiornate per fare riferimento ai certificati dell'insieme di credenziali delle chiavi. <br><br> **NOTA:** non eliminare l'insieme di credenziali poiché la macchina virtuale potrebbe entrare in uno stato di errore. È in corso un tentativo di miglioramento degli elementi back-end affinché gli insiemi di credenziali delle chiavi possano essere eliminati in maniera sicura o spostati insieme alla macchina virtuale in una nuova sottoscrizione. |
| Configurazione di WinRM |Configurazione di WinRM in osProfile |La configurazione di Gestione remota Windows viene trasferita senza variazioni, come parte della migrazione. |
| Proprietà del set di disponibilità |Risorsa del set di disponibilità | La specifica del set di disponibilità è una proprietà della VM nel modello di distribuzione classica. I set di disponibilità diventano una risorsa di primo livello come parte della migrazione. Le seguenti configurazioni non sono più supportate: più set di disponibilità per ogni servizio cloud o uno o più set di disponibilità insieme a VM non incluse in un set di disponibilità in un servizio cloud. |
| Configurazione di rete in una VM |Interfaccia di rete primaria |La configurazione di rete in una VM viene rappresentata come risorsa di interfaccia di rete primaria dopo la migrazione. Per le VM che non sono in una rete virtuale, l'indirizzo IP interno viene modificato durante la migrazione. |
| Più interfacce di rete in una VM |Interfacce di rete |Se una VM ha più interfacce di rete associate, ognuna diventerà una risorsa di primo livello come parte della migrazione nel modello di distribuzione di Resource Manager insieme a tutte le proprietà. |
| Set di endpoint con carico bilanciato |Bilanciamento del carico |Nel modello di distribuzione classica la piattaforma assegna un servizio di bilanciamento del carico implicito per ogni servizio cloud. Durante la migrazione, si crea una nuova risorsa di bilanciamento del carico e il set di endpoint di bilanciamento del carico diventa una serie di regole di bilanciamento del carico. |
| Regole NAT in ingresso |Regole NAT in ingresso |Gli endpoint di input definiti nella VM vengono convertiti in regole NAT in ingresso nel servizio di bilanciamento del carico durante la migrazione. |
| Indirizzo VIP |Indirizzo IP pubblico con nome DNS |L'indirizzo IP virtuale diventa un indirizzo IP pubblico e viene associato al bilanciamento del carico. |
| Rete virtuale |Rete virtuale |Viene eseguita la migrazione della rete virtuale con tutte le relative proprietà nel modello di distribuzione di Resource Manager. Viene creato un nuovo gruppo di risorse con il nome `-migrated`. |
| IP riservati |Indirizzo IP pubblico con allocationMethod statico |Gli indirizzi IP riservati associati al servizio di bilanciamento del carico vengono trasferiti con la migrazione del servizio cloud o della macchina virtuale. La migrazione di indirizzi IP riservati non associati non è attualmente supportata. |
| Indirizzo IP pubblico per VM |Indirizzo IP pubblico con Allocationmethod dinamico |L'indirizzo IP pubblico associato alla VM viene convertito come risorsa indirizzo IP pubblico con il metodo di allocazione impostato su statico. |
| Gruppi di sicurezza di rete |Gruppi di sicurezza di rete |I gruppi di sicurezza di rete associati a una subnet vengono clonati come parte della migrazione nel modello di distribuzione di Resource Manager. Il gruppo di sicurezza di rete nel modello di distribuzione classica non viene rimosso durante la migrazione. Tuttavia, le operazioni del piano di gestione per il gruppo di sicurezza di rete vengono bloccate mentre la migrazione è in corso. |
| Server DNS |Server DNS |Viene eseguita la migrazione dei server DNS associati a una rete virtuale o alla VM come parte della migrazione delle risorse corrispondenti insieme a tutte le proprietà. |
| Route definite dall'utente |Route definite dall'utente |Le route definite dall'utente associate a una subnet vengono clonate come parte della migrazione al modello di distribuzione di Resource Manager. Il routing definito dall'utente nel modello di distribuzione classica non viene rimosso durante la migrazione. Le operazioni del piano di gestione per il routing definito dall'utente vengono bloccate mentre la migrazione è in corso. |
| Proprietà di inoltro IP nella configurazione di rete di una VM |Proprietà di inoltro IP nella scheda di rete |La proprietà di IP in una VM viene convertita in una proprietà nell'interfaccia di rete durante la migrazione. |
| Servizio di bilanciamento del carico con più IP |Servizio di bilanciamento del carico con più risorse IP pubblico |Ogni indirizzo IP pubblico associato al servizio di bilanciamento del carico viene convertito in una risorsa IP pubblico e associato al servizio di bilanciamento del carico al termine della migrazione. |
| Nomi DNS interni nella VM |Nomi DNS interni nella NIC |Durante la migrazione, i suffissi DNS interni per le VM vengono migrati a una proprietà di sola lettura chiamata "InternalDomainNameSuffix" sulla scheda di interfaccia di rete. Il suffisso non subisce modifiche dopo la migrazione e la risoluzione della VM continuerà a funzionare come in precedenza. |
| Gateway di rete virtuale |Gateway di rete virtuale |Le proprietà del gateway di rete virtuali restano invariate durante la migrazione, così come pure l'indirizzo VIP associato al gateway. |
| Sito di rete locale |Gateway di rete locale |Le proprietà del sito di rete locale vengono migrate senza modifiche in una nuova risorsa denominata Gateway di rete locale, che rappresenta i prefissi degli indirizzi locali e l'IP del gateway remoto. |
| Riferimenti alle connessioni |Connessione |I riferimenti alla connettività tra il gateway e il sito di rete locale nella configurazione di rete sono rappresentati da una risorsa appena creata e denominata Connessione in Resource Manager dopo la migrazione. Tutte le proprietà di riferimento alla connettività nei file di configurazione di rete vengono copiate senza modifiche nella risorsa Connessione appena creata. La connettività da VNet a Vnet nella distribuzione classica viene ottenuta creando due tunnel IPsec nei siti di rete locale che rappresentano le VNet. A questo punto viene eseguita la trasformazione nel tipo di connessione da VNet a Vnet nel modello di Resource Manager senza dover ricorrere ai gateway di rete locale. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Modifiche all'automazione e agli strumenti dopo la migrazione
Come parte della migrazione delle risorse dal modello di distribuzione classica al modello di distribuzione Resource Manager, è necessario aggiornare l'automazione o gli strumenti esistenti per accertarsi che continuino a funzionare dopo la migrazione.
