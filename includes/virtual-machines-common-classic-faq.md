


Questo articolo analizza alcune delle domande più comuni poste dagli utenti sulle macchine virtuali di Azure create con un modello di distribuzione classica.

## Cosa è possibile eseguire in una VM di Azure?

Tutti i sottoscrittori possono eseguire software del server in una macchina virtuale Azure. È possibile eseguire versioni recenti di Windows Server, nonché un'ampia gamma di distribuzioni di Linux. Per ulteriori informazioni di supporto, vedere:

• Per VM di Windows - [Supporto del software del server Microsoft per macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=393550)

• Per VM di Linux -- [Distribuzioni di Linux supportate da Azure](http://go.microsoft.com/fwlink/p/?LinkId=393551)

Per le immagini client Windows, alcune versioni di Windows 7 e Windows 8.1 sono disponibili agli abbonati MSDN Azure con benefici e agli abbonati MSDN sviluppo e test con pagamento in base al consumo, per attività di sviluppo e test. Per ulteriori informazioni, incluse le istruzioni e limitazioni, vedere [Immagini Client Windows per gli abbonati MSDN](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/).

## Quanta memoria è possibile utilizzare con una macchina virtuale?

Ogni disco dati può essere fino a 1 TB. Il numero di dischi dati che è possibile utilizzare dipende dalla dimensione della macchina virtuale. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](virtual-machines-linux-sizes.md).

Un account di archiviazione Azure fornisce memoria per il disco del sistema operativo e per qualsiasi disco dati. Ogni disco è un file con estensione vhd archiviato come BLOB di pagine. Per informazioni sui prezzi, vedere [Dettagli prezzi di archiviazione](http://go.microsoft.com/fwlink/p/?LinkId=396819).

## Quali tipi di disco rigido virtuale è possibile utilizzare?

Azure supporta solo dischi rigidi virtuali fissi in formato VHD. Se si è in possesso di un VHDX che si desidera usare in Azure, è necessario prima convertirlo mediante la console di gestione di Hyper-V o il cmdlet [convert-VHD](http://go.microsoft.com/fwlink/p/?LinkId=393656) . Dopo aver fatto ciò, utilizzare il cmdlet [Add-AzureVHD](https://msdn.microsoft.com/library/azure/dn495173.aspx) (in modalità gestione dei servizi) per caricare il disco rigido virtuale in un account di archiviazione in Azure, in modo da poterlo utilizzare con le macchine virtuali.

- Per le istruzioni di Linux, vedere [Creazione e caricamento di un disco rigido virtuale contenente il sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md)

- Per le istruzioni di Windows, vedere [Creare e caricare un disco rigido virtuale di Windows Server in Azure](virtual-machines-windows-classic-createupload-vhd.md).

## Queste macchine virtuali sono le stessi macchine virtuali di Hyper-V?

In molti aspetti sono simili alla "generazione 1" di VM di Hyper-V, ma non sono esattamente la stessa cosa. Entrambi i tipi forniscono un hardware virtualizzato e i dischi rigidi virtuali in formato VHD sono compatibili. Ciò significa che è possibile spostarli tra Hyper-V e Azure. Tre differenze principali che talvolta sorprendono gli utenti di Hyper-V sono:

- Azure non fornisce l'accesso tramite console a una macchina virtuale. Non è possibile accedere a una macchina virtuale finché non si è verificato l'avvio.
- Le macchine virtuali di Azure nella maggior parte delle [dimensioni](virtual-machines-linux-sizes.md) dispongono di una sola scheda di rete virtuale, pertanto possono avere un solo indirizzo IP esterno. (Le dimensioni A8 e A9 utilizzano una seconda scheda di rete per la comunicazione delle applicazioni tra istanze in scenari limitati.)
- Le macchine virtuali di Azure non supportano le funzionalità della macchina virtuale Hyper-V di seconda generazione. Per informazioni dettagliate su queste funzionalità, vedere [Specifiche delle macchine virtuali per Hyper-V](http://technet.microsoft.com/library/dn592184.aspx) e [Panoramica della macchina virtuale di seconda generazione](https://technet.microsoft.com/library/dn282285.aspx).

## Queste macchine virtuali possono utilizzare l’infrastruttura di rete locale esistente?

Per le macchine virtuali create nel modello di distribuzione classica, è possibile utilizzare la rete virtuale di Azure per estendere l'infrastruttura esistente. L'approccio è simile all’apertura di una succursale. È possibile effettuare il provisioning e la gestione delle reti private virtuali (VPN) in Azure, nonché collegarle in modo sicuro all’infrastruttura IT locale. Per informazioni dettagliate, vedere [Panoramica della rete virtuale](../virtual-network/virtual-networks-overview.md).

È necessario specificare la rete alla quale si desidera che la macchina virtuale appartenga, quando quest’ultima viene creata. Non è possibile unire una macchina virtuale esistente a una rete virtuale. Tuttavia, è possibile risolvere il problema scollegando il disco rigido virtuale (VHD) dalla macchina virtuale esistente e quindi utilizzarlo per creare una nuova macchina virtuale con la configurazione di rete desiderata.

## Come si accede alla macchina virtuale?

È necessario stabilire una connessione remota per accedere alla macchina virtuale, utilizzando la Connessione Desktop Remoto per una VM di Windows o una Secure Shell (SSH) per una VM di Linux. Per le istruzioni, vedere

- [Come accedere a una macchina virtuale che esegue Windows Server](virtual-machines-windows-classic-connect-logon.md) Sono supportate al massimo 2 connessioni simultanee, a meno che il server non sia configurato come host sessione Servizi Desktop remoto.  
- [Come accedere a una macchina virtuale che esegue Linux](virtual-machines-linux-classic-log-on.md) Per impostazione predefinita, la SSH consente un massimo di 10 connessioni simultanee. È possibile aumentare questo numero modificando il file di configurazione.


Se si verificano problemi relativi a Desktop remoto o SSH, installare e utilizzare l'estensione [VMAccess](virtual-machines-windows-extensions-features.md) per risolvere il problema.

Per le macchine virtuali Windows, opzioni aggiuntive includono:

- Nel portale di Azure classico, trovare la macchina virtuale, quindi fare clic su **Reimposta accesso remoto** dalla barra dei comandi.
- Consulta [Risoluzione dei problemi relativi alle connessioni di Desktop remoto a una macchina virtuale di Azure basata su Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).
- Utilizzare la comunicazione remota di Windows PowerShell per effettuare la connessione alla macchina virtuale o creare endpoint aggiuntivi per la connessone di altre risorse alla macchina virtuale. Per informazioni dettagliate, vedere [Come configurare gli endpoint in una macchina virtuale](virtual-machines-windows-classic-setup-endpoints.md).

Se si ha familiarità con Hyper-V, è possibile che si stia cercando uno strumento simile a VMConnect. Azure non offre uno strumento simile, poiché l'accesso da console a una macchina virtuale non è supportato.

## È possibile utilizzare il disco temporaneo (L’unità D: per Windows o /dev/sdb1 per Linux) per archiviare i dati?

Non si deve usare il disco temporaneo (L’unità D: per Windows o /dev/sdb1 per Linux) per archiviare i dati. Si tratta solo di memorie temporanee, pertanto si rischierebbe di perdere dati che non possono essere recuperati. Questo problema può verificarsi quando si sposta la macchina virtuale in un host diverso. Il ridimensionamento di una macchina virtuale, l'aggiornamento dell'host o un errore hardware nell'host sono alcuni dei motivi che potrebbero portare a spostare una macchina virtuale.

## Come modificare la lettera di unità del disco temporaneo?

In una macchina virtuale di Windows, è possibile modificare la lettera di unità spostando il file di paging e riassegnando le lettere di unità, ma è necessario assicurarsi di che eseguire i passaggi in un ordine specifico. Per le istruzioni, vedere [Modificare la lettera di unità di un disco temporaneo di Windows](virtual-machines-windows-classic-change-drive-letter.md).

## Come è possibile aggiornare il sistema operativo guest?

Il termine aggiornamento indica in genere il passaggio a una versione più recente del sistema operativo, rimanendo sullo stesso hardware. Per le VM di Azure, il processo per lo spostamento a una versione più recente è diverso per Linux e Windows:

- Per le VM di Linux, utilizzare il pacchetto strumenti di gestione e le procedure appropriate per la distribuzione.
- Per una macchina virtuale di Windows, è necessario eseguire la migrazione al server usando strumenti come quelli di migrazione di Windows Server. Non tentare di aggiornare il sistema operativo guest mentre questo risiede in Azure. Questo non è supportato a causa del rischio di perdere l'accesso alla macchina virtuale. Se si verificano problemi durante l'aggiornamento, si potrebbe perdere la possibilità di avviare una sessione Desktop remoto e non si sarebbe più in grado di risolvere i problemi. 

Per informazioni generali sugli strumenti e sui processi per la migrazione a Windows Server, vedere [Migrazione dei ruoli e delle funzionalità a Windows Server](http://go.microsoft.com/fwlink/p/?LinkId=396940).



## Quali sono il nome utente e la password predefiniti per accedere alla macchina virtuale?

Le immagini fornite da Azure non dispongono di un nome utente e una password predefiniti. Quando si crea la macchina virtuale utilizzando una di queste immagini, è necessario specificare un nome utente e una password, che verranno utilizzati per accedere alla macchina virtuale.

Se si dimentica il nome utente o la password ed è stato installato l'agente VM, è possibile installare e utilizzare l'estensione [VMAccess](virtual-machines-windows-extensions-features.md) per risolvere il problema.

Informazioni aggiuntive:


- Per le immagini Linux, se si usa il portale di Azure classico, viene fornito il nome utente predefinito 'azureuser', che tuttavia si può modificare usando 'Da raccolta' invece di 'Creazione rapida' come metodo di creazione della macchina virtuale. Utilizzando il metodo 'Da raccolta', è inoltre possibile decidere se utilizzare una password, una chiave SSH o entrambe per l'accesso. L'account utente è un utente senza privilegi che dispone di accesso 'sudo' per l'esecuzione di comandi con privilegi. L'account 'root' è disabilitato.


- Per le immagini di Windows, è necessario fornire un nome utente e una password quando si crea la VM. L'account viene aggiunto al gruppo Administrators.

## Azure è in grado di eseguire software antivirus nelle macchine virtuali?

Azure offre diverse opzioni per le soluzioni antivirus, ma tali soluzioni dovranno essere gestite dall'utente- Ad esempio, potrebbe essere necessaria una sottoscrizione separata per il software antimalware e sarà necessario stabilire quando eseguire le analisi e installare gli aggiornamenti. È possibile aggiungere il supporto antivirus con un'estensione VM per Microsoft Antimalware, Symantec Endpoint Protection o TrendMicro Deep Security Agent quando si crea una macchina virtuale Windows o in un momento successivo. Le estensioni Symantec e TrendMicro consentono di utilizzare una sottoscrizione di prova gratuita a tempo limitato o una sottoscrizione aziendale esistente. Microsoft Antimalware è disponibile gratuitamente. Per informazioni dettagliate, vedere:

- [Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Azure](http://go.microsoft.com/fwlink/p/?LinkId=404207)
- [Come installare e configurare Trend Micro Deep Security come servizio in una macchina virtuale di Azure](http://go.microsoft.com/fwlink/p/?LinkId=404206)
- [Distribuzione di soluzioni antimalware in macchine virtuali di Azure](https://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)

## Quali sono le opzioni per il backup e il ripristino?

Backup di Azure è disponibile come anteprima in alcune aree. Per informazioni dettagliate, vedere [Backup delle macchine virtuali di Azure](backup-azure-vms.md). Altre soluzioni sono disponibili da partner certificati. Per scoprire ciò che è attualmente disponibile, eseguire la ricerca in Azure Marketplace.

Un'altra opzione consiste nell'utilizzare le funzionalità di snapshot dell'archiviazione BLOB. Per fare ciò, è necessario arrestare la VM prima di qualsiasi operazione che si basa su uno snapshot del BLOB. Questo salva le scritture di dati in sospeso e inserisce il file system in uno stato coerente.

## Come viene addebitata la VM da Azure?

Azure addebita un costo orario in base alla dimensione della VM e al sistema operativo. Per le ore parziali, Azure addebita il costo solo dei minuti usati. Se si crea la VM con un'immagine di VM contenente dei software preinstallati, potrebbero essere applicati costi orari aggiuntivi per i software. Azure addebita separatamente l’archiviazione per il sistema operativo della VM e i dischi dati. L’archiviazione su disco temporaneo è gratuita.

Viene applicato un addebito quando lo stato della VM è in esecuzione o arrestato, ma non vengono applicati addebiti quando lo stato della VM viene arrestato (deallocato). Per mettere una VM nello stato arrestato (deallocato), effettuare una delle seguenti operazioni:

- Arrestare o eliminare la VM dal portale di Azure classico.
- Utilizzare il cmdlet Stop-AzureVM disponibile nel modulo Azure PowerShell.
- Utilizzare l’operazione Shutdown Role nel servizio di gestione API REST e specificare il valore StoppedDeallocated per l’elemento PostShutdownAction.

Per informazioni dettagliate, vedere [Prezzi delle macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/).

## Azure riavvia la VM per manutenzione?

Azure talvolta riavvia la VM come parte dei regolari aggiornamenti pianificati di manutenzione nei datacenter di Azure.

Gli eventi di manutenzione non pianificati possono verificarsi quando Azure rileva un problema hardware grave che interessa la VM. Per gli eventi non pianificati, Azure effettua automaticamente la migrazione della VM a un host integro e riavvia la VM.

Per qualsiasi VM autonoma (vale a dire quando la VM non fa parte di un set di disponibilità), Azure notifica l'amministratore del servizio di sottoscrizione tramite posta elettronica almeno una settimana prima della manutenzione pianificata, perché le VM potrebbero essere riavviate durante l'aggiornamento. Le applicazioni in esecuzione nelle macchine virtuali potrebbero subire un tempo di inattività.

È inoltre possibile utilizzare il portale di Azure classico oppure Azure PowerShell per visualizzare i registri di riavvio quando il riavvio si è verificato per interventi di manutenzione pianificata. Per informazioni dettagliate, vedere [Visualizzazione dei registri di riavvio della macchina virtuale](https://azure.microsoft.com/blog/2015/04/01/viewing-vm-reboot-logs/).

Per garantire la ridondanza, inserire due o più VM configurate in modo analogo nello stesso set di disponibilità. In questo modo si assicura che almeno una VM sia disponibile durante la manutenzione pianificata o non pianificata. Azure garantisce determinati livelli di disponibilità della VM per questa configurazione. Per informazioni dettagliate, vedere [Gestione della disponibilità delle macchine virtuali](virtual-machines-windows-manage-availability.md).

## Risorse aggiuntive

[Informazioni sulle macchine virtuali di Azure](virtual-machines-linux-about.md)

[Diversi modi per creare una macchina virtuale Linux](virtual-machines-linux-creation-choices.md)

[Diversi modi per creare una macchina virtuale Windows](virtual-machines-windows-creation-choices.md)

<!---HONumber=AcomDC_0323_2016-->