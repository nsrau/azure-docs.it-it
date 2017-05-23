## <a name="understand-planned-vs-unplanned-maintenance"></a>Confronto tra manutenzione pianificata e manutenzione non pianificata
Esistono due tipi di eventi della piattaforma Microsoft Azure che possono incidere sulla disponibilità delle macchine virtuali: la manutenzione pianificata e la manutenzione non pianificata.

* **eventi di manutenzione pianificata** sono aggiornamenti periodici effettuati da Microsoft sulla piattaforma Azure sottostante per migliorare l'affidabilità, la sicurezza e le prestazioni complessive dell'infrastruttura della piattaforma su cui sono in esecuzione le macchine virtuali. La maggior parte di questi aggiornamenti viene eseguita senza alcuna conseguenza per le macchine virtuali o i servizi cloud. In alcune istanze, tuttavia, è necessario riavviare la macchina virtuale per consentire l'applicazione degli aggiornamenti all'infrastruttura della piattaforma.
* **eventi di manutenzione non pianificata** hanno luogo quando si verifica un guasto dell'hardware o dell'infrastruttura fisica sottostante la macchina virtuale. Può trattarsi, ad esempio, di errori della rete locale, guasti di un disco locale o altri errori a livello di rack. Quando viene rilevato un errore di questo tipo, la piattaforma Azure esegue automaticamente la migrazione della macchina virtuale dal computer fisico non integro, in cui è in esecuzione, a un computer fisico integro. Anche se rari, questi eventi possono anche richiedere il riavvio della macchina virtuale.

Per ridurre le conseguenze dei tempi di inattività causati da uno o più di questi eventi, è possibile attuare le seguenti procedure consigliate per aumentare la disponibilità delle macchine virtuali:

* [Configurare più macchine virtuali in un set di disponibilità per la ridondanza]
* [Configurare ogni livello dell'applicazione in set di disponibilità separati]
* [Combinare il bilanciamento del carico con set di disponibilità]
* [Usare Managed Disks per le macchine virtuali nel set di disponibilità]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Configurare più macchine virtuali in un set di disponibilità per la ridondanza
Per garantire la ridondanza dell'applicazione, è consigliabile raggruppare due o più macchine virtuali in un set di disponibilità. Questa configurazione assicura infatti che, nel corso di un evento di manutenzione pianificata o non pianificata, almeno una delle macchine virtuali sia sempre disponibile e soddisfi per almeno il 99,95% i requisiti del contratto di servizio di Azure. Per altre informazioni, vedere [Contratto di Servizio per Macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Evitare di lasciare un'unica istanza di macchina virtuale in un set di disponibilità. Le macchine virtuali in questa configurazione non si qualificano per la garanzia del contratto di servizio e sono soggette a tempi di inattività durante gli eventi di manutenzione pianificata di Azure, se non quando una macchina virtuale singola usa [Archiviazione Premium di Azure](../articles/storage/storage-premium-storage.md). Il contratto di servizio di Azure si applica alle macchine virtuali singole che usano Archiviazione Premium.

A ciascuna macchina virtuale nel set di disponibilità viene assegnato un **dominio di aggiornamento** e un **dominio di errore** dalla piattaforma Azure sottostante. Per ogni set di disponibilità vengono assegnati cinque domini di aggiornamento non configurabili dall'utente per impostazione predefinita, è possibile quindi aumentare le distribuzioni Resource Manager per fornire fino a venti domini di aggiornamento, per indicare i gruppi di macchine virtuali con il relativo hardware fisico sottostante che è possibile riavviare contemporaneamente. Se in un set di disponibilità vengono configurate più di cinque macchine virtuali, la sesta macchina viene inserita nello stesso dominio di aggiornamento della prima, la settima nel dominio di aggiornamento della seconda e così via. I domini di aggiornamento non vengono necessariamente riavviati in ordine sequenziale durante la manutenzione pianificata, ma ne viene riavviato uno solo alla volta.

I domini di errore definiscono il gruppo di macchine virtuali che condividono una fonte di alimentazione e uno switch di rete comuni. Per impostazione predefinita, le macchine virtuali configurate in un set di disponibilità vengono suddivise tra un massimo di tre domini di errore per le distribuzioni di Resource Manager e in due domini di errore per la distribuzione classica. Il raggruppamento di più macchine virtuali in un set di disponibilità non garantisce la protezione dell'applicazione da eventuali errori del sistema operativo o di singole applicazioni, ma limita le conseguenze prodotte da potenziali guasti dell'hardware fisico e interruzioni di rete o di alimentazione.

<!--Image reference-->
   ![Rappresentazione concettuale della configurazione di domini di aggiornamento e di errore](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

### <a name="managed-disk-fault-domains-and-availability-sets"></a>Set di disponibilità e domini di errore dei dischi gestiti
Le VM che usano [Azure Managed Disks](../articles/storage/storage-faq-for-disks.md) sono allineate con i domini di errore dei dischi gestiti quando si usa un set di disponibilità gestito. Questo allineamento garantisce che tutti i dischi gestiti collegati a una VM siano nello stesso dominio di errore dei dischi gestiti. In un set di disponibilità gestito possono essere create solo VM con dischi gestiti. Il numero di domini di errore dei dischi gestiti varia in base all'area: due o tre domini di errore di dischi gestiti per area.


## <a name="configure-each-application-tier-into-separate-availability-sets"></a>Configurare ogni livello dell'applicazione in set di disponibilità separati
Se le macchine virtuali sono quasi identiche e svolgono tutte la stessa funzione per l'applicazione, è consigliabile configurare un set di disponibilità per ogni livello dell'applicazione.  Se in un set di disponibilità si definiscono due livelli diversi, è possibile riavviare contemporaneamente tutte le macchine virtuali dello stesso livello applicazione. Configurando almeno due macchine virtuali per ogni livello di un set di disponibilità, si garantisce la disponibilità di almeno una macchina virtuale in ogni livello.

Ad esempio, è possibile inserire in un unico set di disponibilità tutte le macchine virtuali che si trovano nel front-end dell'applicazione che esegue IIS, Apache e Nginx. Accertarsi che in uno stesso set di disponibilità siano inserite solo le macchine virtuali del front-end e, analogamente, che in uno specifico set di disponibilità siano inserite solo le macchine virtuali a livello dati, come le macchine virtuali di SQL Server replicate o MySQL.

<!--Image reference-->
   ![Livelli di applicazione](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>Combinare il bilanciamento del carico con set di disponibilità
Per ottenere una resilienza elevata dell'applicazione, è possibile combinare [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) con un set di disponibilità. Il servizio di bilanciamento del carico distribuisce il traffico tra più macchine virtuali ed è incluso nelle macchine virtuali di livello Standard. Non tutti i livelli delle macchine virtuali includono Azure Load Balancer. Per altre informazioni sul bilanciamento del carico delle macchine virtuali, vedere [Load Balancing virtual machines](../articles/virtual-machines/virtual-machines-linux-load-balance.md) (Bilanciamento del carico delle macchine virtuali).

Se il bilanciamento del carico non è configurato in modo da bilanciare il traffico tra più macchine virtuali, qualsiasi evento di manutenzione pianificata influirà sull'unica macchina virtuale di gestione del traffico, determinando un'interruzione del livello di applicazione. Associando più macchine virtuali dello stesso livello a un unico servizio di bilanciamento del carico e a uno stesso set di disponibilità, si garantisce che il traffico sia sempre gestito da almeno un'istanza.

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Usare Managed Disks per le macchine virtuali nel set di disponibilità
Se si usano macchine virtuali con dischi non gestiti, è fortemente consigliabile [convertire le macchine virtuali nel set di disponibilità per l'uso di Managed Disks](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md#convert-vms-in-an-availability-set-to-managed-disks-in-a-managed-availability-set).

[Managed Disks](../articles/storage/storage-managed-disks-overview.md) offre una maggiore affidabilità per i set di disponibilità, perché fa in modo che i dischi delle macchine virtuali in un set di disponibilità siano sufficientemente isolati gli uni dagli altri per evitare singoli punti di errore. Ciò avviene mediante l'inserimento automatico dei dischi in unità di scala di archiviazione diverse, dette stamp. Se uno stamp non riesce a causa di un errore hardware o software, hanno esito negativo solo le istanze delle macchine virtuali con dischi in tali stamp. 

Se si intende usare macchine virtuali con [dischi non gestiti](../articles/storage/storage-about-disks-and-vhds-windows.md#types-of-disks), seguire queste procedure consigliate per gli account di archiviazione in cui i dischi rigidi virtuali (VHD) delle macchine virtuali vengono archiviati come [BLOB di pagine](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs). 

1. **Mantenere tutti i dischi (sistema operativo e dati) associati a una macchina virtuale nello stesso account di archiviazione**
2. **Esaminare i [limiti](../articles/storage/storage-scalability-targets.md) al numero di dischi non gestiti in un account di archiviazione** prima di aggiungere altri dischi rigidi virtuali a un account di archiviazione
3. **Usare un account di archiviazione separato per ogni macchina virtuale di un set di disponibilità.** Non condividere gli account di archiviazione con più macchine virtuali in uno stesso set di disponibilità. Le macchine virtuali di set di disponibilità diversi possono condividere gli account di archiviazione, purché vengano seguite le procedure consigliate precedenti.

<!-- Link references -->
[Configurare più macchine virtuali in un set di disponibilità per la ridondanza]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configurare ogni livello dell'applicazione in set di disponibilità separati]: #configure-each-application-tier-into-separate-availability-sets
[Combinare il bilanciamento del carico con set di disponibilità]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Use Managed Disks for VMs in Availability Set]: #use-managed-disks-for-vms-in-availability-set

