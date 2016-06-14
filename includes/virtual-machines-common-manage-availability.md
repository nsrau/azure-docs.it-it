## Confronto tra manutenzione pianificata e manutenzione non pianificata
Esistono due tipi di eventi della piattaforma Microsoft Azure che possono incidere sulla disponibilità delle macchine virtuali: la manutenzione pianificata e la manutenzione non pianificata.

- Gli **eventi di manutenzione pianificata** sono aggiornamenti periodici effettuati da Microsoft sulla piattaforma Azure sottostante per migliorare l'affidabilità, la sicurezza e le prestazioni complessive dell'infrastruttura della piattaforma su cui sono in esecuzione le macchine virtuali. La maggior parte di questi aggiornamenti viene eseguita senza alcuna conseguenza per le macchine virtuali o i servizi cloud. In alcune istanze, tuttavia, è necessario riavviare la macchina virtuale per consentire l'applicazione degli aggiornamenti all'infrastruttura della piattaforma.

- Gli **eventi di manutenzione non pianificata** hanno luogo quando si verifica un guasto dell'hardware o dell'infrastruttura fisica sottostante la macchina virtuale. Può trattarsi, ad esempio, di errori della rete locale, guasti di un disco locale o altri errori a livello di rack. Quando viene rilevato un errore di questo tipo, la piattaforma Azure esegue automaticamente la migrazione della macchina virtuale dalla macchina fisica non integra su cui è in esecuzione a una macchina fisica integra. Anche se rari, questi eventi possono anche richiedere il riavvio della macchina virtuale.

## Procedure consigliate durante la progettazione di un'applicazione per la disponibilità elevata
Per ridurre le conseguenze dei tempi di inattività causati da uno o più di questi eventi, è possibile attuare le seguenti procedure consigliate per aumentare la disponibilità delle macchine virtuali:

* [Configurare più macchine virtuali in un set di disponibilità per la ridondanza]
* [Configurare ogni livello dell'applicazione in set di disponibilità separati]
* [Combinare il bilanciamento del carico con set di disponibilità]


### Configurare più macchine virtuali in un set di disponibilità per la ridondanza
Per garantire la ridondanza dell'applicazione, è consigliabile raggruppare due o più macchine virtuali in un set di disponibilità. Questa configurazione assicura infatti che, nel corso di un evento di manutenzione pianificata o non pianificata, almeno una delle macchine virtuali sia sempre disponibile e soddisfi per almeno il 99,95% i requisiti del contratto di servizio di Azure. Per altre informazioni, vedere [Contratto di Servizio per Macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [AZURE.IMPORTANT] Evitare di lasciare un'unica istanza di macchina virtuale in un set di disponibilità. Le macchine virtuali in questa configurazione non hanno diritto a una garanzia dei contratti di servizio e subiranno tempi di inattività durante gli eventi di manutenzione pianificata di Azure.

A ogni macchina virtuale nel set di disponibilità viene assegnato un **dominio di aggiornamento** e un **dominio di errore** dalla piattaforma Azure sottostante. Per ogni set di disponibilità vengono assegnati cinque domini di aggiornamento non configurabili dall'utente per impostazione predefinita, è possibile quindi aumentare le distribuzioni Resource Manager per fornire fino a venti domini di aggiornamento, per indicare i gruppi di macchine virtuali con il relativo hardware fisico sottostante che è possibile riavviare contemporaneamente. Se in un set di disponibilità vengono configurate più di cinque macchine virtuali, la sesta macchina viene inserita nello stesso dominio di aggiornamento della prima, la settima nel dominio di aggiornamento della seconda e così via. I domini di aggiornamento non vengono necessariamente riavviati in ordine sequenziale durante la manutenzione pianificata, ma ne viene riavviato uno solo alla volta.

I domini di errore definiscono il gruppo di macchine virtuali che condividono una fonte di alimentazione e uno switch di rete comuni. Per impostazione predefinita, le macchine virtuali configurate in un set di disponibilità vengono suddivise tra un massimo di tre domini di errore per le distribuzioni Resource Manager e in due domini di errore per la distribuzione classica. Il raggruppamento di più macchine virtuali in un set di disponibilità non garantisce la protezione dell'applicazione da eventuali errori del sistema operativo o di singole applicazioni, ma limita le conseguenze prodotte da potenziali guasti dell'hardware fisico e interruzioni di rete o di alimentazione.

<!--Image reference-->
   ![Rappresentazione concettuale della configurazione di domini di aggiornamento e di errore](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)


### Configurare ogni livello dell'applicazione in set di disponibilità separati

Se le macchine virtuali sono quasi identiche e svolgono tutte la stessa funzione per l'applicazione, è consigliabile configurare un set di disponibilità per ogni livello dell'applicazione. Se in un set di disponibilità si definiscono due livelli diversi, è possibile riavviare contemporaneamente tutte le macchine virtuali dello stesso livello applicazione. Configurando almeno due macchine virtuali per ogni livello di un set di disponibilità, si garantisce che in ogni livello sia sempre disponibile almeno una macchina virtuale.

Ad esempio, è possibile inserire in un unico set di disponibilità tutte le macchine virtuali che si trovano nel front-end dell'applicazione che esegue IIS, Apache, Nginx e così via. Accertarsi che in uno stesso set di disponibilità siano inserite solo le macchine virtuali del front-end e, analogamente, che in uno specifico set di disponibilità siano inserite solo le macchine virtuali a livello dati, come le macchine virtuali di SQL Server replicate o MySQL.

<!--Image reference-->
   ![Livelli di applicazione](./media/virtual-machines-common-manage-availability/application-tiers.png)


### Combinare il bilanciamento del carico con set di disponibilità
Per ottenere una resilienza elevata dell'applicazione, è possibile combinare [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) con un set di disponibilità. Il servizio di bilanciamento del carico distribuisce il traffico tra più macchine virtuali ed è incluso nelle macchine virtuali di livello Standard. Non tutti i livelli delle macchine virtuali includono il servizio di bilanciamento del carico di Azure. Per altre informazioni sul bilanciamento del carico delle macchine virtuali, vedere [Panoramica del servizio di bilanciamento del carico](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Se il bilanciamento del carico non è configurato in modo da bilanciare il traffico tra più macchine virtuali, qualsiasi evento di manutenzione pianificata influirà sull'unica macchina virtuale di gestione del traffico, determinando un'interruzione del livello di applicazione. Associando più macchine virtuali dello stesso livello a un unico servizio di bilanciamento del carico e a uno stesso set di disponibilità, si garantisce che il traffico sia sempre gestito da almeno un'istanza.

 

<!-- Link references -->
[Configurare più macchine virtuali in un set di disponibilità per la ridondanza]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configurare ogni livello dell'applicazione in set di disponibilità separati]: #configure-each-application-tier-into-separate-availability-sets
[Combinare il bilanciamento del carico con set di disponibilità]: #combine-the-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets

<!---HONumber=AcomDC_0601_2016-->