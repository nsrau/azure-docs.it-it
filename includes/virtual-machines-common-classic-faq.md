


Questo articolo analizza alcune delle domande più comuni poste dagli utenti sulle macchine virtuali di Azure create con un modello di distribuzione classica.

## <a name="can-i-migrate-my-vm-created-in-the-classic-deployment-model-to-the-new-resource-manager-model"></a>È possibile eseguire la migrazione della VM creata nel modello di distribuzione classica al nuovo modello di Resource Manager?
Sì. Per istruzioni su come eseguire la operazione, vedere:

* [Migrazione dal modello classico ad Azure Resource Manager con Azure PowerShell](../articles/virtual-machines/windows/migration-classic-resource-manager-ps.md).
* [Migrazione dal modello classico ad Azure Resource Manager con l'interfaccia della riga di comando di Azure](../articles/virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md).

## <a name="what-can-i-run-on-an-azure-vm"></a>Cosa è possibile eseguire in una VM di Azure?
Tutti i sottoscrittori possono eseguire software del server in una macchina virtuale Azure. È possibile eseguire versioni recenti di Windows Server, nonché un'ampia gamma di distribuzioni di Linux. Per ulteriori informazioni di supporto, vedere:

• Per VM di Windows - [Supporto del software del server Microsoft per macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=393550)

• Per VM di Linux -- [Distribuzioni di Linux supportate da Azure](http://go.microsoft.com/fwlink/p/?LinkId=393551)

Per le immagini client Windows, alcune versioni di Windows 7 e Windows 8.1 sono disponibili agli abbonati MSDN Azure con benefici e agli abbonati MSDN sviluppo e test con pagamento in base al consumo, per attività di sviluppo e test. Per ulteriori informazioni, incluse le istruzioni e limitazioni, vedere [Immagini Client Windows per gli abbonati MSDN](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/).

## <a name="why-are-affinity-groups-being-deprecated"></a>Perché i gruppi di affinità sono deprecati?
I gruppi di affinità sono un concetto legacy per un raggruppamento geografico delle distribuzioni di servizi cloud di un cliente e degli account di archiviazione in Azure. Originariamente venivano forniti per migliorare le prestazioni di rete da VM a VM nelle prime progettazioni di rete di Azure. Inoltre, supportavano la versione iniziale di reti virtuali (VNet), che erano limitate a un set ridotto di componenti hardware in un'area.

L'attuale rete di Azure all'interno di un'area è progettata in modo che i gruppi di affinità non siano più necessari. Le reti virtuali fanno anche parte di un ambito a livello di area, quindi un gruppo di affinità non è più necessario quando si usa una rete virtuale. A seguito di questi miglioramenti, non è più consigliabile che i clienti usino i gruppi di affinità, perché possono costituire una limitazione in alcuni scenari. L'uso dei gruppi di affinità comporterà l'inutile associazione delle VM a componenti hardware specifici, con la conseguente limitazione delle opzioni relative alle dimensioni delle VM disponibili. Può anche comportare errori correlati alla capacità quando si aggiungono nuove VM se l'hardware specifico associato al gruppo di affinità ha quasi esaurito la capacità.

Le funzionalità del gruppo di affinità sono già deprecate nel modello di distribuzione Azure Resource Manager e nel Portale di Azure. Per il Portale di Azure classico, viene anche deprecato il supporto per la creazione di gruppi di affinità e la creazione di risorse di archiviazione aggiunte a un gruppo di affinità dal portale classico. Non è necessario modificare i servizi cloud esistenti che usano un gruppo di affinità. I nuovi servizi cloud non dovranno invece usare gruppi di affinità, a meno che questo approccio non sia consigliato da un tecnico di Azure.

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanta memoria è possibile utilizzare con una macchina virtuale?
Ogni disco dati può essere fino a 1 TB. Il numero di dischi dati che è possibile utilizzare dipende dalla dimensione della macchina virtuale. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Un account di archiviazione Azure fornisce memoria per il disco del sistema operativo e per qualsiasi disco dati. Ogni disco è un file con estensione vhd archiviato come BLOB di pagine. Per informazioni sui prezzi, vedere [Dettagli prezzi di archiviazione](http://go.microsoft.com/fwlink/p/?LinkId=396819).

## <a name="which-virtual-hard-disk-types-can-i-use"></a>Quali tipi di disco rigido virtuale è possibile utilizzare?
Azure supporta solo dischi rigidi virtuali fissi in formato VHD. Se si è in possesso di un VHDX che si desidera usare in Azure, è necessario prima convertirlo mediante la console di gestione di Hyper-V o il cmdlet [convert-VHD](http://go.microsoft.com/fwlink/p/?LinkId=393656) . Dopo aver fatto ciò, utilizzare il cmdlet [Add-AzureVHD](https://msdn.microsoft.com/library/azure/dn495173.aspx) (in modalità gestione dei servizi) per caricare il disco rigido virtuale in un account di archiviazione in Azure, in modo da poterlo utilizzare con le macchine virtuali.

* Per le istruzioni di Linux, vedere [Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Linux](../articles/virtual-machines/linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).
* Per le istruzioni di Windows, vedere [Creazione e caricamento di un disco rigido virtuale con Windows Server in Azure](../articles/virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="are-these-virtual-machines-the-same-as-hyper-v-virtual-machines"></a>Queste macchine virtuali sono le stessi macchine virtuali di Hyper-V?
In molti aspetti sono simili alla "generazione 1" di  VM di Hyper-V, ma non sono esattamente la stessa cosa. Entrambi i tipi forniscono un hardware virtualizzato e i dischi rigidi virtuali in formato VHD sono compatibili. Ciò significa che è possibile spostarli tra Hyper-V e Azure. Tre differenze principali che talvolta sorprendono gli utenti di Hyper-V sono:

* Azure non fornisce l'accesso tramite console a una macchina virtuale. Non è possibile accedere a una macchina virtuale finché non si è verificato l'avvio.
* Le macchine virtuali di Azure nella maggior parte delle [dimensioni](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dispongono di una sola scheda di rete virtuale, pertanto possono avere un solo indirizzo IP esterno. (Le dimensioni A8 e A9 utilizzano una seconda scheda di rete per la comunicazione delle applicazioni tra istanze in scenari limitati.)
* Le macchine virtuali di Azure non supportano le funzionalità della macchina virtuale Hyper-V di seconda generazione. Per informazioni dettagliate su queste funzionalità, vedere [Specifiche delle macchine virtuali per Hyper-V](http://technet.microsoft.com/library/dn592184.aspx) e [Panoramica delle macchine virtuali di seconda generazione](https://technet.microsoft.com/library/dn282285.aspx).

## <a name="can-these-virtual-machines-use-my-existing-on-premises-networking-infrastructure"></a>Queste macchine virtuali possono utilizzare l’infrastruttura di rete locale esistente?
Per le macchine virtuali create nel modello di distribuzione classica, è possibile utilizzare la rete virtuale di Azure per estendere l'infrastruttura esistente. L'approccio è simile all’apertura di una succursale. È possibile effettuare il provisioning e la gestione delle reti private virtuali (VPN) in Azure, nonché collegarle in modo sicuro all’infrastruttura IT locale. Per informazioni dettagliate, vedere [Panoramica della rete virtuale](../articles/virtual-network/virtual-networks-overview.md).

È necessario specificare la rete alla quale si desidera che la macchina virtuale appartenga, quando quest’ultima viene creata. Non è possibile unire una macchina virtuale esistente a una rete virtuale. Tuttavia, è possibile risolvere il problema scollegando il disco rigido virtuale (VHD) dalla macchina virtuale esistente e quindi utilizzarlo per creare una nuova macchina virtuale con la configurazione di rete desiderata.

## <a name="how-can-i-access--my-virtual-machine"></a>Come si accede alla macchina virtuale?
È necessario stabilire una connessione remota per accedere alla macchina virtuale, usando la Connessione Desktop Remoto per una VM di Windows o una Secure Shell (SSH) per una VM di Linux. Per le istruzioni, vedere

* [How to Log on to a Virtual Machine Running Windows Server](../articles/virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (Come accedere a una macchina virtuale che esegue Windows Server). Sono supportate al massimo 2 connessioni simultanee, a meno che il server non sia configurato come host sessione Servizi Desktop remoto.  
* [How to Log on to a Virtual Machine Running Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Come accedere a una macchina virtuale che esegue Linux). Per impostazione predefinita, la SSH consente un massimo di 10 connessioni simultanee. È possibile aumentare questo numero modificando il file di configurazione.

Se si verificano problemi relativi a Desktop remoto o SSH, installare e usare l'estensione [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per risolvere il problema.

Per le macchine virtuali Windows, opzioni aggiuntive includono:

* Nel portale di Azure, trovare la macchina virtuale, quindi fare clic su **Reimposta accesso remoto** dalla barra dei comandi.
* Consultare [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure che esegue Windows](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Utilizzare la comunicazione remota di Windows PowerShell per effettuare la connessione alla macchina virtuale o creare endpoint aggiuntivi per la connessone di altre risorse alla macchina virtuale. Per informazioni dettagliate, vedere [Come configurare gli endpoint in una macchina virtuale](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Se si ha familiarità con Hyper-V, è possibile che si stia cercando uno strumento simile a VMConnect. Azure non offre uno strumento simile, poiché l'accesso da console a una macchina virtuale non è supportato.

## <a name="can-i-use-the-temporary-disk-the-d-drive-for-windows-or-devsdb1-for-linux-to-store-data"></a>È possibile utilizzare il disco temporaneo (L’unità D: per Windows o /dev/sdb1 per Linux) per archiviare i dati?
Non si deve usare il disco temporaneo (L’unità D: per Windows o /dev/sdb1 per Linux) per archiviare i dati. Si tratta solo di memorie temporanee, pertanto si rischierebbe di perdere dati che non possono essere recuperati. Questo problema può verificarsi quando si sposta la macchina virtuale in un host diverso. Il ridimensionamento di una macchina virtuale, l'aggiornamento dell'host o un errore hardware nell'host sono alcuni dei motivi che potrebbero portare a spostare una macchina virtuale.

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Come modificare la lettera di unità del disco temporaneo?
In una macchina virtuale di Windows, è possibile modificare la lettera di unità spostando il file di paging e riassegnando le lettere di unità, ma è necessario assicurarsi di che eseguire i passaggi in un ordine specifico. Per le istruzioni, vedere [Modificare la lettera di unità di un disco temporaneo di Windows](../articles/virtual-machines/windows/change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="how-can-i-upgrade-the-guest-operating-system"></a>Come è possibile aggiornare il sistema operativo guest?
Il termine aggiornamento indica in genere il passaggio a una versione più recente del sistema operativo, rimanendo sullo stesso hardware. Per le VM di Azure, il processo per lo spostamento a una versione più recente è diverso per Linux e Windows:

* Per le VM di Linux, utilizzare il pacchetto strumenti di gestione e le procedure appropriate per la distribuzione.
* Per una macchina virtuale di Windows, è necessario eseguire la migrazione al server usando strumenti come quelli di migrazione di Windows Server. Non tentare di aggiornare il sistema operativo guest mentre questo risiede in Azure. Questo non è supportato a causa del rischio di perdere l'accesso alla macchina virtuale. Se si verificano problemi durante l'aggiornamento, si potrebbe perdere la possibilità di avviare una sessione Desktop remoto e non si sarebbe più in grado di risolvere i problemi.

Per informazioni generali sugli strumenti e sui processi per la migrazione a Windows Server, vedere [Migrazione dei ruoli e delle funzionalità a Windows Server](http://go.microsoft.com/fwlink/p/?LinkId=396940).

## <a name="whats-the-default-user-name-and-password-on-the-virtual-machine"></a>Quali sono il nome utente e la password predefiniti per accedere alla macchina virtuale?
Le immagini fornite da Azure non dispongono di un nome utente e una password predefiniti. Quando si crea la macchina virtuale utilizzando una di queste immagini, è necessario specificare un nome utente e una password, che verranno utilizzati per accedere alla macchina virtuale.

Se si dimentica il nome utente o la password ed è stato installato l'agente VM, è possibile installare e usare l'estensione [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per risolvere il problema.

Informazioni aggiuntive:

* Se si usa il portale di Azure, per le immagini Linux, 'azureuser' è specificato come un nome utente predefinito, ma è possibile modificare questa impostazione utilizzando 'Da raccolta' anziché 'Creazione rapida' come metodo per creare la macchina virtuale. Utilizzando il metodo 'Da raccolta', è inoltre possibile decidere se utilizzare una password, una chiave SSH o entrambe per l'accesso. L'account utente è un utente senza privilegi che dispone di accesso 'sudo' per l'esecuzione di comandi con privilegi. L'account 'root' è disabilitato.
* Per le immagini di Windows, è necessario fornire un nome utente e una password quando si crea la VM. L'account viene aggiunto al gruppo Administrators.

## <a name="can-azure-run-anti-virus-on-my-virtual-machines"></a>Azure è in grado di eseguire software antivirus nelle macchine virtuali?
Azure offre diverse opzioni per le soluzioni antivirus, ma tali soluzioni dovranno essere gestite dall'utente- Ad esempio, potrebbe essere necessaria una sottoscrizione separata per il software antimalware e sarà necessario stabilire quando eseguire le analisi e installare gli aggiornamenti. È possibile aggiungere il supporto antivirus con un'estensione VM per Microsoft Antimalware, Symantec Endpoint Protection o TrendMicro Deep Security Agent quando si crea una macchina virtuale Windows o in un momento successivo. Le estensioni Symantec e TrendMicro consentono di usare una sottoscrizione di prova gratuita a tempo limitato o una sottoscrizione aziendale esistente. Microsoft Antimalware è disponibile gratuitamente. Per informazioni dettagliate, vedere:

* [Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Azure](http://go.microsoft.com/fwlink/p/?LinkId=404207)
* [Come installare e configurare Trend Micro Deep Security come servizio in una macchina virtuale di Azure](http://go.microsoft.com/fwlink/p/?LinkId=404206)
* [Distribuzione di soluzioni antimalware in macchine virtuali di Azure](https://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)

## <a name="what-are-my-options-for-backup-and-recovery"></a>Quali sono le opzioni per il backup e il ripristino?
Backup di Azure è disponibile come anteprima in alcune aree. Per informazioni dettagliate, vedere [Backup delle macchine virtuali di Azure](../articles/backup/backup-azure-arm-vms.md). Altre soluzioni sono disponibili da partner certificati. Per scoprire ciò che è attualmente disponibile, eseguire la ricerca in Azure Marketplace.

Un'altra opzione consiste nell'utilizzare le funzionalità di snapshot dell'archiviazione BLOB. Per fare ciò, è necessario arrestare la VM prima di qualsiasi operazione che si basa su uno snapshot del BLOB. Questo salva le scritture di dati in sospeso  e inserisce il file system in uno stato coerente.

## <a name="how-does-azure-charge-for-my-vm"></a>Come viene addebitata la VM da Azure?
Azure addebita un costo orario in base alla dimensione della VM e al sistema operativo. Per le ore parziali, Azure addebita il costo solo dei minuti usati. Se si crea la VM con un'immagine di VM contenente dei software preinstallati, potrebbero essere applicati costi orari aggiuntivi per i software. Azure addebita separatamente l’archiviazione per il sistema operativo della VM e i dischi dati. L’archiviazione su disco temporaneo è gratuita.

Viene applicato un addebito quando lo stato della VM è in esecuzione o arrestato, ma non vengono applicati addebiti quando lo stato della VM viene arrestato (deallocato). Per mettere una VM nello stato arrestato (deallocato), effettuare una delle seguenti operazioni:

* Arrestare o eliminare la macchina virtuale dal portale di Azure.
* Utilizzare il cmdlet Stop-AzureVM disponibile nel modulo Azure PowerShell.
* Utilizzare l’operazione Shutdown Role nel servizio di gestione API REST e specificare il valore StoppedDeallocated per l’elemento PostShutdownAction.

Per informazioni dettagliate, vedere [Prezzi delle macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="will-azure-reboot-my-vm-for-maintenance"></a>Azure riavvia la VM per manutenzione?
Azure talvolta riavvia la VM come parte dei regolari aggiornamenti pianificati di  manutenzione nei datacenter di Azure.

Gli eventi di manutenzione non pianificati possono verificarsi quando Azure rileva un problema hardware grave che interessa la VM. Per gli eventi non pianificati, Azure effettua automaticamente la migrazione della VM a un host integro e riavvia la VM.

Per qualsiasi VM autonoma (vale a dire quando la VM non fa parte di un set di disponibilità), Azure notifica l'amministratore del servizio di sottoscrizione tramite posta elettronica almeno una settimana prima della manutenzione pianificata, perché le VM potrebbero essere riavviate durante l'aggiornamento. Le applicazioni in esecuzione nelle macchine virtuali potrebbero subire un tempo di inattività.

È inoltre possibile utilizzare il portale di Azure o Azure PowerShell per visualizzare i log di riavvio quando si è verificato il riavvio per interventi di manutenzione pianificato. Per informazioni dettagliate, vedere [Visualizzazione dei registri di riavvio della macchina virtuale](https://azure.microsoft.com/blog/2015/04/01/viewing-vm-reboot-logs/).

Per garantire la ridondanza, inserire due o più VM configurate in modo analogo nello stesso set di disponibilità. In questo modo si assicura che almeno una VM sia disponibile durante la manutenzione pianificata o non pianificata. Azure garantisce determinati livelli di disponibilità della VM per questa configurazione. Per informazioni dettagliate, vedere [Gestione della disponibilità delle macchine virtuali](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="additional-resources"></a>Risorse aggiuntive
[Informazioni sulle macchine virtuali di Azure](../articles/virtual-machines/virtual-machines-linux-about.md)

[Creare e gestire VM Linux con l'interfaccia della riga di comando di Azure](../articles/virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Creare e gestire VM Windows con Azure PowerShell](../articles/virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

