Questo articolo illustra un insieme di procedure consolidate per l'esecuzione di una VM Linux in Azure, con particolare attenzione agli aspetti di scalabilità, disponibilità, gestibilità e sicurezza. Azure supporta l'esecuzione di molte distribuzioni Linux diffuse, tra cui CentOS, Debian, Red Hat Enterprise, Ubuntu e FreeBSD. Per altre informazioni, vedere [Azure e Linux][azure-linux].

> [!NOTE]
> Azure offre due diversi modelli di distribuzione, ovvero [Resource Manager][resource-manager-overview] e la distribuzione classica. Questo articolo usa Azure Resource Manager, consigliato da Microsoft per le nuove distribuzioni.
> 
> 

Non è consigliabile usare una singola VM per carichi di lavoro di importanza strategica perché crea un singolo punto di guasto. Per una disponibilità più elevata, distribuire più VM in un [set di disponibilità][availability-set]. Per altre informazioni, vedere [Esecuzione di più VM in Azure][multi-vm]. 

## <a name="architecture-diagram"></a>Diagramma dell'architettura
Il provisioning di una VM in Azure coinvolge un altri componenti mobili oltre alla VM in sé. È necessario prendere in considerazione elementi di calcolo, di rete e archiviazione.

> Un documento di Visio che include questo diagramma di architettura è disponibile per il download nell'[Area download Microsoft][visio-download]. Questo diagramma è nella pagina "Compute - single VM".
> 
> 

![[0]][0]

* **Gruppo di risorse.** Un [*gruppo di risorse*][resource-manager-overview] è un contenitore in cui risiedono le risorse correlate. Creare un gruppo di risorse in cui includere le risorse per questa VM.
* **VM**. È possibile effettuare il provisioning di una macchina virtuale da un elenco di immagini pubblicate oppure da un file di disco rigido virtuale (VHD) caricato nell'archivio BLOB di Azure.
* **Disco del sistema operativo.** Il disco del sistema operativo è un VHD archiviato in [Archiviazione di Azure][azure-storage]. Viene quindi salvato in modo permanente anche se il computer host si arresta. Il disco del sistema operativo è `/dev/sda1`.
* **Disco temporaneo.** La VM viene creata con un disco temporaneo. Questo disco viene archiviato in un'unità fisica nel computer host. *Non* viene salvato nell'Archiviazione di Azure ed è possibile che venga eliminato durante i riavvii e altri eventi del ciclo di vita della VM. Usare questo disco solo per dati temporanei, ad esempio file di paging o di scambio. Il disco temporaneo è `/dev/sdb1` ed è montato in `/mnt/resource` o `/mnt`.
* **Dischi dati.** Un [disco dati][data-disk] è un VHD persistente usato per i dati dell'applicazione. I dischi dati vengono archiviati nell'Archiviazione di Azure, come il disco del sistema operativo.
* **Rete virtuale (VNet) e subnet.** Ogni VM in Azure viene distribuita in una rete virtuale (VNet), che viene a sua volta suddivisa in subnet.
* **Indirizzo IP pubblico.** È necessario un indirizzo IP pubblico per comunicare con la VM&mdash;ad esempio tramite SSH.
* **Interfaccia di rete (NIC)**. La scheda di interfaccia di rete consente alla VM di comunicare con la rete virtuale.
* **Gruppo di sicurezza di rete (NSG)**. Il [gruppo di sicurezza di rete][nsg] viene usato per consentire/negare il traffico di rete verso la subnet. Un NSG può essere associato a una singola scheda di interfaccia di rete o a una subnet. Se lo si associa a una subnet, le regole dell’NSG si applicano a tutte le VM nella subnet.
* **Diagnostica.** La registrazione diagnostica è essenziale per la gestione e la risoluzione dei problemi della macchina virtuale.

## <a name="recommendations"></a>Indicazioni

Le raccomandazioni seguenti sono valide per la maggior parte degli scenari. Seguire queste indicazioni, a meno che non si disponga di un requisito specifico che le escluda. 

### <a name="vm-recommendations"></a>Indicazioni per le VM

Azure offre diverse dimensioni per le macchine virtuali, ma è consigliabile scegliere le serie DS e GS perché supportano [Archiviazione Premium][premium-storage]. Selezionare una di queste dimensioni del computer, a meno che non si abbia un carico di lavoro specializzato, ad esempio di high-performance computing. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali in Azure][virtual-machine-sizes].

Se si sposta un carico di lavoro esistente in Azure, per iniziare scegliere le dimensioni della VM più simili a quelle dei server locali. Misurare quindi le prestazioni del carico di lavoro effettivo in relazione agli aspetti di CPU, memoria e operazioni di input/output (IOPS) del disco e regolare le dimensioni secondo necessità. Se sono necessarie più schede di interfaccia di rete per la VM, si noti che il numero massimo di schede di interfaccia di rete dipende dalle [dimensioni della VM][vm-size-tables].

Quando si effettua il provisioning della VM e di altre risorse, è necessario specificare una località. È in genere consigliabile scegliere la località più vicina agli utenti interni o ai clienti. È tuttavia possibile che non tutte le dimensioni della VM siano disponibili in tutte le aree. Per informazioni dettagliate, vedere i [servizi disponibili in base all'area][services-by-region]. Per elencare le dimensioni delle VM disponibili in una determinata area, eseguire questo comando dell'interfaccia della riga di comando di Azure:

```
azure vm sizes --location <location>
```

Per informazioni sulla scelta di un'immagine di VM pubblicata, vedere [Selezionare immagini di VM Linux con l'interfaccia della riga di comando di Azure][select-vm-image].

### <a name="disk-and-storage-recommendations"></a>Indicazioni per il disco e l'archiviazione

Per ottimizzare le prestazioni I/O del disco, si consiglia di usare [Archiviazione Premium][premium-storage], che archivia i dati in unità SSD (Solid State Drive). I costi dipendono dalle dimensioni del disco sottoposto a provisioning. Anche IOPS e velocità effettiva, ovvero la velocità di trasferimento dati, dipendono dalle dimensioni del disco. Quando si effettua il provisioning di un disco è quindi consigliabile tenere in considerazione tutti e tre i fattori, ovvero capacità, IOPS e velocità effettiva. 

Creare account di archiviazione di Azure separati per ogni VM e per contenere i dischi rigidi virtuali in modo da evitare di raggiungere i limiti di operazioni di I/O al secondo per gli account di archiviazione. 

Aggiungere uno o più dischi dati. Quando si crea un VHD, il disco non è formattato. Accedere alla VM per formattare il disco. Nella shell di Linux, i dischi dati vengono visualizzati come `/dev/sdc`, `/dev/sdd` e così via. È possibile eseguire `lsblk` per elencare i dispositivi a blocchi, ad esempio i dischi. Per usare un disco dati, creare una partizione e un file system, quindi montare il disco. Ad esempio:

```bat
# Create a partition.
sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

Se sono presenti molti dischi dati, occorre prestare attenzione ai limiti totali di I/O dell'account di archiviazione. Per altre informazioni, vedere [Limiti relativi ai dischi della macchina virtuale][vm-disk-limits].

Quando si aggiunge un disco dati, al disco viene assegnato un ID numero di unità logica (LUN). È anche possibile specificare l'ID LUN &mdash; ad esempio se si intende sostituire un disco e si vuole mantenere lo stesso ID LUN o se si ha un'applicazione che cerca un ID LUN specifico. Tuttavia, tenere presente che gli ID LUN devono essere univoci per ogni disco.

È possibile modificare l'utilità di pianificazione I/O per ottimizzare le prestazioni nelle unità SSD perché i dischi delle VM negli account di Archiviazione Premium sono SSD. Normalmente si consiglia di usare l'utilità di pianificazione NOOP per le unità SSD, ma è necessario usare uno strumento come [iostat] per monitorare le prestazioni di I/O del disco relative al un carico di lavoro specifico.

Per prestazioni ottimali, creare un account di archiviazione separato per i log di diagnostica. Un account di archiviazione con ridondanza locale standard è sufficiente per i log di diagnostica.

### <a name="network-recommendations"></a>Indicazioni per la rete

L'indirizzo IP pubblico può essere dinamico o statico. Per impostazione predefinita, è dinamico.

* Riservare un [indirizzo IP statico][static-ip] se è necessario un indirizzo IP fisso che non subirà modifiche &mdash; ad esempio se è necessario creare un record A nel DNS o se è necessario aggiungere l'indirizzo IP a un elenco di indirizzi attendibili.
* È inoltre possibile creare un nome di dominio completo (FQDN) per l'indirizzo IP. È quindi possibile registrare un [record CNAME][cname-record] nel DNS che punta al nome FQDN. Per altre informazioni, vedere [Creare un nome di dominio completo nel portale di Azure][fqdn].

Tutti i gruppi di sicurezza di rete contengono un set di [regole predefinite][nsg-default-rules], inclusa una regola che blocca tutto il traffico Internet in ingresso. Le regole predefinite non possono essere eliminate, ma altre regole possono eseguirne l'override. Per consentire il traffico Internet, creare regole per indirizzare il traffico in ingresso a determinate porte &mdash; ad esempio la porta 80 per il traffico HTTP.  

Per abilitare SSH, aggiungere una regola al gruppo di sicurezza di rete per consentire il traffico in arrivo sulla porta TCP 22.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Per aumentare o ridurre le prestazioni, [modificare le dimensioni della VM][vm-resize]. 

Per scalare orizzontalmente, inserire due o più VM in un set di disponibilità nell'ambito di un servizio di bilanciamento del carico. Per informazioni dettagliate, vedere [Esecuzione di più VM in Azure][multi-vm].

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Per una disponibilità più elevata, distribuire più VM in un set di disponibilità. Sarà così disponibile anche un [contratto di servizio][vm-sla] (SLA) di livello più elevato. 

È possibile che la VM sia interessata da attività di [manutenzione pianificata][planned-maintenance] o [manutenzione non pianificata][manage-vm-availability]. È possibile usare i [log di riavvio della VM][reboot-logs] per determinare se un riavvio della VM è stato provocato da attività di manutenzione pianificata.

I dischi rigidi virtuali vengono archiviati nell'[archivio di Azure][azure-storage] e l'archivio di Azure viene replicato per assicurare durabilità e disponibilità.

Per proteggersi dalla perdita accidentale di dati durante le operazioni normali, ad esempio a causa di un errore dell'utente, è consigliabile implementare anche i backup temporizzati usando [snapshot di BLOB][blob-snapshot] o un altro strumento.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

**Gruppi di risorse.** Posizionare in uno stesso [gruppo di risorse][resource-manager-overview] le risorse strettamente associate che condividono lo stesso ciclo di vita. I gruppi di risorse consentono di distribuire e monitorare le risorse in gruppo, distribuendo i costi per ogni gruppo di risorse. È inoltre possibile eliminare un intero set di risorse, operazione molto utile nelle distribuzioni di test. Assegnare alle risorse nomi significativi. In tal modo, è più semplice individuare una specifica risorsa e comprenderne il ruolo. Vedere [Recommended Naming Conventions for Azure Resources][naming conventions] (Convenzioni di denominazione consigliate per le risorse di Azure).

**SSH**. Prima di creare una VM Linux, generare una coppia di chiavi RSA pubblica/privata a 2.048 bit. Quando si crea la VM, utilizzare il file di chiave pubblica. Per altre informazioni, vedere [Come usare SSH con Linux e Mac in Azure][ssh-linux].

**Diagnostica delle VM.** Abilitare il monitoraggio e la diagnostica, tra cui le metriche di base sull'integrità, i log relativi all'infrastruttura di diagnostica e la [diagnostica di avvio][boot-diagnostics]. La diagnostica di avvio permette di diagnosticare gli errori di avvio quando la VM passa a uno stato non avviabile. Per altre informazioni, vedere [Abilitare il monitoraggio e la diagnostica][enable-monitoring].  

Per abilitare la diagnostica, eseguire il comando seguente dell'interfaccia della riga di comando:

```
azure vm enable-diag <resource-group> <vm-name>
```

**Arresto di una VM.** Azure distingue tra gli stati "Arrestato" e "Deallocato". L'addebito avviene quando lo stato della VM viene arrestato, ma non quando la VM viene deallocata.

Usare il comando seguente dell'interfaccia della riga di comando per deallocare una VM:

```
azure vm deallocate <resource-group> <vm-name>
```

Anche il pulsante **Arresta** nel portale di Azure consente di deallocare la VM. Se, tuttavia, l'arresto viene effettuato tramite il sistema operativo ad accesso eseguito, la VM viene arrestata ma *non* deallocata, quindi gli addebiti continueranno a essere effettuati.

**Eliminazione di una VM.** Se si elimina una VM, i VHD non vengono eliminati. È quindi possibile eliminare in modo sicuro la macchina virtuale senza perdere dati. Verranno tuttavia applicati comunque addebiti per l'archiviazione. Per eliminare il VHD, eliminare il file dall'[archivio BLOB][blob-storage].

Per impedire l'eliminazione accidentale, usare un [blocco di risorsa][resource-lock] per bloccare l'intero gruppo di risorse o le singole risorse, ad esempio la VM. 

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Automatizzare gli aggiornamenti del sistema operativo usando l'estensione [OSPatching] della VM. Installare l'estensione al momento di eseguire il provisioning della VM. È possibile specificare la frequenza di installazione delle patch e stabilire se eseguire il riavvio dopo l'applicazione di patch.

Usare il [controllo degli accessi in base al ruolo][rbac] per controllare l'accesso alle risorse di Azure da distribuire. Il controllo degli accessi in base al ruolo consente di assegnare i ruoli di autorizzazione ai membri del proprio team DevOps. Ad esempio, il ruolo di lettura permette di visualizzare le risorse di Azure, ma non di crearle, gestirle o eliminarle. Alcuni ruoli sono specifici di determinati tipi di risorse di Azure. Ad esempio, il ruolo di Collaboratore Macchina virtuale consente di riavviare o deallocare una VM, reimpostare la password di amministratore, creare una VM e così via. Altri [ruoli predefiniti di Controllo degli accessi in base al ruolo][rbac-roles] che potrebbero essere utili per questa architettura di riferimento includono [Utente DevTest Labs][rbac-devtest] e [Collaboratore Rete][rbac-network]. 

Oltre a poter assegnare un utente a più ruoli, è possibile creare ruoli personalizzati per autorizzazioni ancora più dettagliate.

d> [!NOTE]
> Il controllo degli accessi in base al ruolo non limita le azioni eseguibili da un utente registrato in una VM. Le autorizzazioni sono determinate dal tipo di account sul sistema operativo guest.   
> 
> 

Per verificare le azioni di provisioning e altri eventi della VM, usare i [log di controllo][audit-logs].

Se è necessario crittografare i dischi del sistema operativo e i dischi dati, usare [Crittografia dischi di Azure ][disk-encryption]. 

## <a name="solution-deployment"></a>Distribuzione della soluzione
m Una distribuzione di questa architettura di riferimento è disponibile in [GitHub][github-folder]. Include una rete virtuale, un gruppo di sicurezza di rete e una singola VM. Per distribuire l'architettura, seguire questi passaggi: 

1. Fare clic con il pulsante destro del mouse sul pulsante seguente e scegliere "Apri collegamento in una nuova scheda" o "Apri collegamento in una nuova finestra".
   [![Distribuzione in Azure](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)
2. Dopo avere aperto il collegamento nel portale di Azure, è necessario immettere i valori per alcune impostazioni: 
   
   * Poiché il nome del **gruppo di risorse** è già definito nel file dei parametri, selezionare **Crea nuovo** e immettere `ra-single-vm-rg` nella casella di testo.
   * Selezionare l'area dalla casella di riepilogo a discesa **Località**.
   * Non modificare le caselle di testo **Template Root Uri** (URI radice modello) né **Parameter Root Uri** (URI radice parametro).
.   *Selezionare **linux** nella casella di riepilogo a discesa **Tipo di sistema operativo**.
   * Leggere i termini e le condizioni, quindi fare clic sulla casella di controllo **Accetto le condizioni riportate sopra**.
   * Fare clic sul pulsante **Acquista**.
3. Attendere il completamento della distribuzione.
4. I file dei parametri includono un nome utente e una password amministratore hardcoded. È consigliabile cambiarli immediatamente. Fare clic sulla VM denominata `ra-single-vm0 ` nel portale di Azure. Fare quindi clic su **Reimposta password** nella sezione **Supporto e risoluzione dei problemi**. Selezionare **Reimposta password** nella casella di riepilogo a discesa **Modalità**, quindi selezionare un nuovo **nome utente** e una nuova **password**. Fare clic sul pulsante **Aggiorna** per rendere permanenti il nuovo nome utente e la nuova password.

## <a name="next-steps"></a>Passaggi successivi
Per una maggiore disponibilità, distribuire due o più macchine virtuali dietro un servizio di bilanciamento del carico. Per altre informazioni, vedere [Esecuzione di più VM in Azure][multi-vm].

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: ../articles/virtual-machines/virtual-machines-windows-create-availability-set.md
[azure-cli]: /cli/azure/get-started-with-az-cli2
[azure-linux]: ../articles/virtual-machines/virtual-machines-linux-azure-overview.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/storage/storage-about-disks-and-vhds-linux.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-linux-portal-create-fqdn.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-labs-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[select-vm-image]: ../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssh-linux]: ../articles/virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-account-limits]: ../articles/azure-subscription-service-limits.md#storage-limits
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-linux-sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-size-tables]: ../articles/virtual-machines/virtual-machines-windows-sizes.md#size-tables
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[components]: #Solution-components
[blocks]: https://github.com/mspnp/template-building-blocks
[0]: ./media/guidance-blueprints/compute-single-vm.png "Singola architettura VM di Linux in Azure"

