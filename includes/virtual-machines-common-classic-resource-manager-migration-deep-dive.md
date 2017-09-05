## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>Eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager
È importante comprendere prima di tutto la differenza tra le operazioni a livello di piano dati e quelle a livello di piano di gestione sulle risorse dell'infrastruttura distribuita come servizio (IaaS).

* Il *piano di gestione/controllo* descrive le chiamate che arrivano al piano di gestione/controllo o l'API per la modifica delle risorse. Ad esempio, operazioni come la creazione di una VM, il riavvio di una VM e l'aggiornamento di una rete virtuale con una nuova subnet gestiscono le risorse in esecuzione. Non incidono direttamente sulla connessione alle macchine virtuali.
* Il *piano dati* (applicazione) descrive la fase di esecuzione dell'applicazione stessa e prevede l'interazione con istanze che non passano attraverso l'API di Azure. Ad esempio, l'accesso a un sito Web o il pull dei dati da un'istanza di SQL Server o da un server MongoDB in esecuzione sono interazioni a livello di piano dati o dell'applicazione. Altri esempi includono la copia di un BLOB da un account di archiviazione e l'accesso a un indirizzo IP pubblico per l'uso di Remote Desktop Protocol (RDP) o Secure Shell (SSH) nella macchina virtuale. Queste operazioni mantengono in esecuzione l'applicazione nelle risorse di calcolo, rete e archiviazione.

Il piano dati è lo stesso sia per il modello di distribuzione classica che per gli stack di Azure Resource Manager. La differenza sta nel fatto che, durante il processo di migrazione, Microsoft converte la rappresentazione delle risorse dal modello di distribuzione classica a quella nello stack di Azure Resource Manager. È quindi necessario usare nuovi strumenti, API e SDK per gestire le risorse nello stack di Azure Resource Manager.

![Diagramma che mostra la differenza tra piano di gestione/controllo e piano dati](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> In alcuni scenari di migrazione, la piattaforma Azure interrompe, dealloca e riavvia le macchine virtuali. Questo provoca un breve tempo di inattività del piano dati.
>

## <a name="the-migration-experience"></a>Esperienza di migrazione
Prima di iniziare la migrazione:

* Assicurarsi che le risorse da migrare non usino funzionalità o configurazioni non supportate. Generalmente la piattaforma rileva questi problemi e genera un errore.
* Le eventuali macchine virtuali che non si trovano in una rete virtuale vengono arrestate e deallocate come parte dell'operazione di preparazione. Per non perdere l'indirizzo IP pubblico, valutare l'opportunità di riservare l'indirizzo IP prima di avviare l'operazione di preparazione. Se le macchine virtuali si trovano in una rete virtuale, non vengono arrestate né deallocate.
* Pianificare la migrazione in orari non lavorativi, per gestire eventuali errori imprevisti durante la migrazione.
* Scaricare la configurazione corrente delle VM usando PowerShell, i comandi dell'interfaccia della riga di comando o le API REST per semplificare la convalida dopo la procedura di preparazione.
* Aggiornare gli script di automazione e messa in funzione per gestire il modello di distribuzione Resource Manager prima di avviare la migrazione. È possibile eseguire facoltativamente operazioni GET quando lo stato delle risorse è Prepared.
* Valutare i criteri di controllo degli accessi in base al ruolo configurati nelle risorse IaaS del modello di distribuzione classica e preparare un piano per la fase successiva al completamento della migrazione.

Il flusso di lavoro della migrazione è il seguente:

![Diagramma che mostra il flusso di lavoro di migrazione](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> Tutte le operazioni descritte nelle sezioni seguenti sono idempotenti. Se vengono rilevati errori diversi da una funzionalità non supportata o un errore di configurazione, provare a ripetere l'operazione di preparazione, interruzione o commit. Azure prova a ripetere l'azione.
>
>

### <a name="validate"></a>Convalida
L'operazione di convalida è il primo passaggio del processo di migrazione. L'obiettivo di questo passaggio è analizzare lo stato delle risorse di cui eseguire la migrazione nel modello di distribuzione classica. L'operazione valuta se la migrazione delle risorse potrà dare un esito positivo o negativo.

Selezionare la rete virtuale o un servizio cloud, se non è una rete virtuale, da convalidare per la migrazione. Se la risorsa non è idonea per la migrazione, Azure ne elenca i motivi.

#### <a name="checks-not-done-in-the-validate-operation"></a>Controlli non eseguiti nell'operazione di convalida

L'operazione di convalida analizza unicamente lo stato delle risorse nel modello di distribuzione classica. Può verificare tutti gli errori e gli scenari non supportati a causa di varie configurazioni nel modello di distribuzione classica. Non può verificare tutti i problemi che lo stack di Azure Resource Manager potrebbe imporre sulle risorse durante la migrazione. La verifica di tali problemi avviene unicamente quando le risorse vengono sottoposte a trasformazione nel passaggio successivo della migrazione, vale a dire l'operazione di preparazione. La tabella seguente elenca tutti i problemi che non vengono verificati durante l'operazione di convalida:


|Controlli di rete non eseguiti nell'operazione di convalida|
|-|
|Rete virtuale con gateway ExpressRoute e VPN.|
|Connessione gateway di rete virtuale in stato disconnesso.|
|Pre-migrazione di tutti i circuiti ExpressRoute nello stack di Azure Resource Manager.|
|Verifica delle quote di Azure Resource Manager per le risorse di rete. Ad esempio: indirizzo IP pubblico statico, indirizzi IP pubblici dinamici, servizio di bilanciamento del carico, gruppi di sicurezza di rete, tabelle route e interfacce di rete. |
| Validità di tutte le regole del servizio di bilanciamento del carico nella distribuzione e nella rete virtuale. |
| Indirizzi IP privati in conflitto tra macchine virtuali in fase di arresto-deallocazione nella stessa rete virtuale. |

### <a name="prepare"></a>Preparazione
L'operazione di preparazione è il secondo passaggio del processo di migrazione. L'obiettivo di questo passaggio è simulare la trasformazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager. L'operazione di preparazione consente anche la visualizzazione affiancata del processo.

> [!NOTE] 
> In questo passaggio le risorse nel modello di distribuzione classica non vengono modificate. È quindi possibile eseguire questo passaggio in sicurezza se si sta provando la migrazione. 

Selezionare la rete virtuale o il servizio cloud, se non è una rete virtuale, da preparare per la migrazione.

* Se la risorsa non è idonea per la migrazione, Azure arresta il processo di migrazione ed elenca i motivi per cui l'operazione di preparazione non è riuscita.
* Se la risorsa è idonea per la migrazione, Azure blocca le operazioni del piano di gestione per le risorse in fase di migrazione. Ad esempio, non è possibile aggiungere un disco dati a una VM in fase di migrazione.

Azure avvia quindi la migrazione dei metadati dal modello di distribuzione classica ad Azure Resource Manager per le risorse in fase di migrazione.

Al termine dell'operazione di preparazione, è possibile visualizzare le risorse sia nel modello di distribuzione classica che in Azure Resource Manager. Per ogni servizio cloud nel modello di distribuzione classica, la piattaforma Azure crea un nome di gruppo di risorse nel formato `cloud-service-name>-Migrated`.

> [!NOTE]
> Non è possibile selezionare il nome di un gruppo di risorse creato per le risorse di cui è stata eseguita la migrazione, vale a dire "-Migrated". Al termine della migrazione, tuttavia, è possibile usare la funzionalità di Azure Resource Manager per spostare le risorse in qualsiasi gruppo di risorse. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../articles/resource-group-move-resources.md).

I due screenshot seguenti mostrano il risultato dopo l'esito positivo di un'operazione di preparazione. Il primo screenshot mostra un gruppo di risorse che contiene il servizio cloud originale. Il secondo screenshot mostra il nuovo gruppo di risorse "-Migrated" che contiene le risorse equivalenti di Azure Resource Manager.

![Screenshot che mostra il servizio cloud originale](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Screenshot che mostra le risorse di Azure Resource Manager nell'operazione di preparazione](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

Di seguito vengono mostrati i retroscena delle risorse al termine della fase di preparazione. Si noti che la risorsa nel piano dati è la stessa. Viene rappresentata sia nel piano di gestione (modello di distribuzione classica) che nel piano di controllo (Resource Manager).

![Diagramma della fase di preparazione](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Le macchine virtuali che non sono in una rete virtuale nel modello di distribuzione classica vengono interrotte e deallocate in questa fase della migrazione.
>

### <a name="check-manual-or-scripted"></a>Verifica (manuale o tramite script)
Nel passaggio di verifica è possibile usare la configurazione scaricata in precedenza per convalidare la correttezza della migrazione. In alternativa, è possibile accedere al portale e controllare le proprietà e le risorse per convalidare la correttezza della migrazione dei metadati.

Se si esegue la migrazione di una rete virtuale, la maggior parte delle configurazioni delle macchine virtuali non viene riavviata. Per le applicazioni disponibili in tali macchine virtuali, è possibile verificare che l'applicazione sia ancora in esecuzione.

È possibile testare gli script di monitoraggio e operativi per verificare il corretto funzionamento delle macchine virtuali e degli script aggiornati. Quando lo stato delle risorse è Prepared, sono supportate solo le operazioni GET .

Non c'è un intervallo di tempo definito entro cui è necessario eseguire il commit della migrazione. In questo stato non sono previsti limiti di tempo. Tuttavia, il piano di gestione viene bloccato per queste risorse fino a quando non si decide di interrompere o eseguire il commit.

Se si verificano problemi, è sempre possibile interrompere la migrazione e tornare al modello di distribuzione classica. Tornando indietro, Azure apre le operazioni del piano di gestione sulle risorse, per consentire di riprendere le normali operazioni sulle macchine virtuali nel modello di distribuzione classica.

### <a name="abort"></a>Interruzione
Questo è un passaggio facoltativo che permette di annullare le modifiche al modello di distribuzione classica e di arrestare la migrazione. L'operazione elimina i metadati di Resource Manager creati nella fase di preparazione per le risorse. 

![Diagramma della fase di interruzione](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Non è possibile eseguire questa operazione dopo aver avviato l'operazione di commit.     
>

### <a name="commit"></a>Commit
Dopo aver completato la convalida, è possibile eseguire il commit della migrazione. Le risorse non vengono più visualizzate nel modello di distribuzione classica e sono disponibili solo nel modello di distribuzione Resource Manager. Le risorse migrate possono essere gestite solo nel nuovo portale.

> [!NOTE]
> Si tratta di un'operazione idempotente. In caso di esito negativo, ripetere l'operazione. Se l'esito continua a essere negativo, creare un ticket di supporto o un post con il tag "ClassicIaaSMigration" nel [forum sulle VM](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).
>
>

![Diagramma della fase di commit](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Diagramma di flusso della migrazione

Il diagramma di flusso seguente mostra come procedere con la migrazione:

![Schermata che mostra i passaggi di migrazione](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>Conversione delle risorse dal modello di distribuzione classica ad Azure Resource Manager
La tabella seguente mostra la rappresentazione delle risorse nel modello di distribuzione classica e Resource Manager. Le seguenti funzionalità e risorse non sono attualmente supportate.

| Rappresentazione classica | Rappresentazione in Resource Manager | Note |
| --- | --- | --- |
| Nome del servizio cloud |Nome DNS |Durante la migrazione, viene creato un nuovo gruppo di risorse per ogni servizio cloud con il modello di denominazione `<cloudservicename>-migrated`. Questo gruppo di risorse contiene tutte le risorse. Il nome del servizio cloud diventa un nome DNS associato all'indirizzo IP pubblico. |
| Macchina virtuale |Macchina virtuale |Le proprietà specifiche della VM vengono migrate senza variazioni. Alcune informazioni su osProfile, come il nome del computer, non vengono archiviate nel modello di distribuzione classica e rimangono vuote dopo la migrazione. |
| Risorse del disco collegate alla VM |Dischi impliciti collegati alla VM |I dischi non vengono modellati come risorse di primo livello nel modello di distribuzione di Resource Manager. Ne verrà eseguita la migrazione come dischi impliciti della VM. Attualmente sono supportati solo i dischi collegati a una VM. Le macchine virtuali di Resource Manager ora possono usare gli account di archiviazione nel modello di distribuzione classica. Questo permette di eseguire facilmente la migrazione dei dischi senza aggiornamenti. |
| Estensioni di VM |Estensioni di VM |Viene eseguita la migrazione dal modello di distribuzione classica di tutte le estensioni di risorse, a eccezione delle estensioni XML. |
| Certificati di macchine virtuali |Certificati nell'insieme di credenziali delle chiavi di Azure |Se un servizio cloud contiene i certificati di servizio, la migrazione crea un nuovo insieme di credenziali delle chiavi di Azure per ogni servizio cloud e sposta i certificati nell'insieme di credenziali delle chiavi. Le VM vengono aggiornate per fare riferimento ai certificati dell'insieme di credenziali delle chiavi. <br><br> Non eliminare l'insieme di credenziali delle chiavi. Ciò può provocare uno stato di errore della macchina virtuale. |
| Configurazione di WinRM |Configurazione di WinRM in osProfile |La configurazione di Gestione remota Windows viene trasferita senza variazioni, come parte della migrazione. |
| Proprietà del set di disponibilità |Risorsa del set di disponibilità | La specifica del set di disponibilità è una proprietà della macchina virtuale nel modello di distribuzione classica. I set di disponibilità diventano una risorsa di primo livello come parte della migrazione. Le seguenti configurazioni non sono più supportate: più set di disponibilità per ogni servizio cloud o uno o più set di disponibilità insieme a VM non incluse in un set di disponibilità in un servizio cloud. |
| Configurazione di rete in una VM |Interfaccia di rete primaria |La configurazione di rete in una VM viene rappresentata come risorsa di interfaccia di rete primaria dopo la migrazione. Per le VM che non sono in una rete virtuale, l'indirizzo IP interno viene modificato durante la migrazione. |
| Più interfacce di rete in una VM |Interfacce di rete |Se una macchina virtuale ha più interfacce di rete associate, ognuna diventa una risorsa di primo livello come parte della migrazione, insieme a tutte le proprietà. |
| Set di endpoint con carico bilanciato |Bilanciamento del carico |Nel modello di distribuzione classica la piattaforma assegna un servizio di bilanciamento del carico implicito per ogni servizio cloud. Durante la migrazione, si crea una nuova risorsa di bilanciamento del carico e il set di endpoint di bilanciamento del carico diventa una serie di regole di bilanciamento del carico. |
| Regole NAT in ingresso |Regole NAT in ingresso |Gli endpoint di input definiti nella VM vengono convertiti in regole NAT in ingresso nel servizio di bilanciamento del carico durante la migrazione. |
| Indirizzo VIP |Indirizzo IP pubblico con nome DNS |L'indirizzo IP virtuale diventa un indirizzo IP pubblico e viene associato al servizio di bilanciamento del carico. La migrazione di un indirizzo IP virtuale può essere eseguita solo se all'indirizzo è assegnato un endpoint di input. |
| Rete virtuale |Rete virtuale |Viene eseguita la migrazione della rete virtuale con tutte le relative proprietà nel modello di distribuzione di Resource Manager. Viene creato un nuovo gruppo di risorse con il nome `-migrated`. |
| IP riservati |Indirizzo IP pubblico con allocationMethod statico |Gli indirizzi IP riservati associati al servizio di bilanciamento del carico vengono trasferiti con la migrazione del servizio cloud o della macchina virtuale. La migrazione di indirizzi IP riservati non associati non è attualmente supportata. |
| Indirizzo IP pubblico per VM |Indirizzo IP pubblico con Allocationmethod dinamico |L'indirizzo IP pubblico associato alla VM viene convertito come risorsa indirizzo IP pubblico con il metodo di allocazione impostato su statico. |
| Gruppi di sicurezza di rete |Gruppi di sicurezza di rete |I gruppi di sicurezza di rete associati a una subnet vengono clonati come parte della migrazione nel modello di distribuzione di Resource Manager. Il gruppo di sicurezza di rete nel modello di distribuzione classica non viene rimosso durante la migrazione. Tuttavia, le operazioni del piano di gestione per il gruppo di sicurezza di rete vengono bloccate mentre la migrazione è in corso. |
| Server DNS |Server DNS |Viene eseguita la migrazione dei server DNS associati a una rete virtuale o alla VM come parte della migrazione delle risorse corrispondenti insieme a tutte le proprietà. |
| Route definite dall'utente |Route definite dall'utente |Le route definite dall'utente associate a una subnet vengono clonate come parte della migrazione al modello di distribuzione di Resource Manager. Il routing definito dall'utente nel modello di distribuzione classica non viene rimosso durante la migrazione. Le operazioni del piano di gestione per il routing definito dall'utente vengono bloccate mentre la migrazione è in corso. |
| Proprietà di inoltro IP nella configurazione di rete di una VM |Proprietà di inoltro IP nella scheda di rete |La proprietà di IP in una VM viene convertita in una proprietà nell'interfaccia di rete durante la migrazione. |
| Servizio di bilanciamento del carico con più IP |Servizio di bilanciamento del carico con più risorse IP pubblico |Ogni indirizzo IP pubblico associato al servizio di bilanciamento del carico viene convertito in una risorsa IP pubblico e associato al servizio di bilanciamento del carico al termine della migrazione. |
| Nomi DNS interni nella VM |Nomi DNS interni nella NIC |Durante la migrazione, i suffissi DNS interni per le VM vengono migrati a una proprietà di sola lettura chiamata "InternalDomainNameSuffix" sulla scheda di interfaccia di rete. Il suffisso non subisce modifiche dopo la migrazione e la risoluzione della macchina virtuale continua a funzionare come in precedenza. |
| Gateway di rete virtuale |Gateway di rete virtuale |Le proprietà del gateway di rete virtuale restano invariate dopo la migrazione, così come pure l'indirizzo VIP associato al gateway. |
| Sito di rete locale |Gateway di rete locale |Le proprietà del sito di rete locale non vengono modificate durante la migrazione in una nuova risorsa denominata Gateway di rete locale, che rappresenta i prefissi degli indirizzi locali e l'indirizzo IP del gateway remoto. |
| Riferimenti alle connessioni |Connessione |I riferimenti alla connettività tra il gateway e il sito di rete locale nella configurazione di rete sono rappresentati da una nuova risorsa denominata Connessione. Tutte le proprietà del riferimento alla connettività nei file di configurazione di rete vengono copiate senza modifiche nella risorsa Connessione. Per la connettività tra reti virtuali nel modello di distribuzione classica è possibile creare due tunnel IPSec ai siti di rete locale che rappresentano le reti virtuali. A questo punto viene eseguita la trasformazione nel tipo di connessione da rete virtuale a rete virtuale nel modello di distribuzione Resource Manager, senza dover ricorrere ai gateway di rete locale. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Modifiche all'automazione e agli strumenti dopo la migrazione
Come parte della migrazione delle risorse dal modello di distribuzione classica al modello di distribuzione Resource Manager, è necessario aggiornare l'automazione o gli strumenti esistenti per fare in modo che continuino a funzionare dopo la migrazione.
