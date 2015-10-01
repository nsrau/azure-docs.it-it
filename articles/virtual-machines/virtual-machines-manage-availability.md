<properties
	pageTitle="Gestire la disponibilità delle macchine virtuali | Microsoft Azure"
	description="Informazioni su come usare più macchine virtuali per garantire la disponibilità per l'applicazione Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="kenazk"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/23/2015"
	ms.author="kenazk"/>

#Gestione della disponibilità delle macchine virtuali

## Confronto tra manutenzione pianificata e manutenzione non pianificata
Esistono due tipi di eventi della piattaforma Microsoft Azure che possono incidere sulla disponibilità delle macchine virtuali: la manutenzione pianificata e la manutenzione non pianificata.

- Gli **eventi di manutenzione pianificata** sono aggiornamenti periodici effettuati da Microsoft sulla piattaforma Azure sottostante per migliorare l'affidabilità, la sicurezza e le prestazioni complessive dell'infrastruttura della piattaforma su cui sono in esecuzione le macchine virtuali. La maggior parte di questi aggiornamenti viene eseguita senza alcuna conseguenza per le macchine virtuali o i servizi cloud. In alcune istanze, tuttavia, è necessario riavviare la macchina virtuale per consentire l'applicazione degli aggiornamenti all'infrastruttura della piattaforma.

- Gli **eventi di manutenzione non pianificata** hanno luogo quando si verifica un guasto dell'hardware o dell'infrastruttura fisica sottostante la macchina virtuale. Può trattarsi, ad esempio, di errori della rete locale, guasti di un disco locale o altri errori a livello di rack. Quando viene rilevato un errore di questo tipo, la piattaforma Azure esegue automaticamente la migrazione della macchina virtuale dalla macchina fisica non integra su cui è in esecuzione a una macchina fisica integra. Anche se rari, questi eventi possono anche richiedere il riavvio della macchina virtuale.

## Procedure consigliate durante la progettazione di un'applicazione per la disponibilità elevata
Per ridurre le conseguenze dei tempi di inattività causati da uno o più di questi eventi, è possibile attuare le seguenti procedure consigliate per aumentare la disponibilità delle macchine virtuali:

* [Configurare più macchine virtuali in un set di disponibilità per la ridondanza](#configurare-più-macchine-virtuali-in-un-set-di-disponibilità-per-la-ridondanza)
* [Configurare ogni livello dell'applicazione in set di disponibilità separati](#configurare-ogni-livello-dell-applicazione-in-set-di-disponibilità-separati)
* [Combinare il servizio bilanciamento del carico con set di disponibilità](#combinare-il-servizio-bilanciamento-del-carico-con-set-di-disponibilità)
* [Evitare singole istanze di macchine virtuali nei set di disponibilità](#evitare-singole-istanze-di-macchine-virtuali-nei-set-di-disponibilità)

<h3><a id="configurare-più-macchine-virtuali-in-un-set-di-disponibilità-per-la-ridondanza"></a>Configurare più macchine virtuali in un set di disponibilità per la ridondanza</h3>
Per garantire ridondanza a un'applicazione è consigliabile raggruppare due o più macchine virtuali in un set di disponibilità. Questa configurazione assicura infatti che, nel corso di un evento di manutenzione pianificata o non pianificata, almeno una delle macchine virtuali sia sempre disponibile e soddisfi per almeno il 99,95% i requisiti del contratto di servizio di Azure. Per altre informazioni sui contratti di servizio, vedere la sezione "Servizi cloud, macchine virtuali e rete virtuale" in [Contratti di servizio](../../../support/legal/sla/).

A ogni macchina virtuale nel set di disponibilità viene assegnato un dominio di aggiornamento e un dominio di errore dalla piattaforma Azure sottostante. Per ogni set di disponibilità, inoltre, vengono assegnati cinque domini di aggiornamento non configurabili dall'utente che indicano i gruppi di macchine virtuali, con il relativo hardware fisico sottostante, che è possibile aggiornare contemporaneamente. Se in un set di disponibilità vengono configurate più di cinque macchine virtuali, la sesta macchina viene inserita nello stesso dominio di aggiornamento della prima, la settima nel dominio di aggiornamento della seconda e così via. I domini di aggiornamento non vengono necessariamente riavviati in ordine sequenziale durante gli eventi di manutenzione pianificata, ma viene sempre riavviato un dominio di aggiornamento per volta.

I domini di errore definiscono il gruppo di macchine virtuali che condividono una fonte di alimentazione e uno switch di rete comuni. Per impostazione predefinita, le macchine virtuali configurate nell'ambito di un set di disponibilità vengono suddivise tra due domini di errore. Il raggruppamento di più macchine virtuali in un set di disponibilità non garantisce la protezione dell'applicazione da eventuali errori del sistema operativo o di singole applicazioni, ma limita le conseguenze prodotte da potenziali guasti dell'hardware fisico e interruzioni di rete o di alimentazione.

<!--Image reference-->
   ![configurazione dei domini di aggiornamento/errore](./media/virtual-machines-manage-availability/ud-fd-configuration.png)

>[AZURE.NOTE]Per istruzioni, vedere [Come configurare un set di disponibilità per le macchine virtuali][].

<h3><a id="configurare-ogni-livello-dell-applicazione-in-set-di-disponibilità-separati"></a>Configurare ogni livello dell'applicazione in set di disponibilità separati</h3>
Se le macchine virtuali di un set di disponibilità sono pressoché identiche e svolgono tutte la stessa funzione per l'applicazione, è consigliabile configurare un set di disponibilità per ciascun livello dell'applicazione. Se in un set di disponibilità si definiscono due livelli diversi, è possibile riavviare contemporaneamente tutte le macchine virtuali dello stesso livello di applicazione. Configurando almeno due macchine virtuali per ogni livello di un set di disponibilità, si garantisce che in ogni livello sia sempre disponibile almeno una macchina virtuale.

Ad esempio, è possibile inserire in un unico set di disponibilità tutte le macchine virtuali che si trovano nel front-end dell'applicazione che esegue IIS, Apache, Nginx e così via. Accertarsi che in uno stesso set di disponibilità siano inserite esclusivamente le macchine virtuali del front-end e, analogamente, che in uno specifico set di disponibilità siano inserite solo le macchine virtuali a livello di dati, come le macchine virtuali SQL Server replicate o MySQL.

<!--Image reference-->
   ![Livelli di applicazione](./media/virtual-machines-manage-availability/application-tiers.png)


<h3><a id="combinare-il-servizio-bilanciamento-del-carico-con-set-di-disponibilità"></a>Combinare il servizio bilanciamento del carico con set di disponibilità</h3>
Per ottenere un'elevata resilienza dell'applicazione è possibile combinare il servizio di bilanciamento del carico di Azure con un set di disponibilità. Il servizio di bilanciamento del carico distribuisce il traffico tra più macchine virtuali ed è incluso nelle macchine virtuali di livello Standard. Non tutti i livelli delle macchine virtuali includono il servizio di bilanciamento del carico di Azure. Per altre informazioni sul bilanciamento del carico delle macchine virtuali, leggere [Bilanciamento del carico delle macchine virtuali](../load-balance-virtual-machines.md).

Se il bilanciamento del carico non è configurato in modo da bilanciare il traffico tra più macchine virtuali, qualsiasi evento di manutenzione pianificata influirà sull'unica macchina virtuale di gestione del traffico, determinando un'interruzione del livello di applicazione. Associando più macchine virtuali dello stesso livello a un unico servizio di bilanciamento del carico e a uno stesso set di disponibilità si garantisce che il traffico sia sempre gestito da almeno un'istanza.

<h3><a id="evitare-singole-istanze-di-macchine-virtuali-nei-set-di-disponibilità"></a>Evitare singole istanze di macchine virtuali nei set di disponibilità</h3>
Evitare di lasciare un'unica istanza di macchina virtuale in un set di disponibilità. Le macchine virtuali in questa configurazione non hanno diritto a una garanzia dei contratti di servizio e subiranno tempi di inattività durante gli eventi di manutenzione pianificata di Azure. L’istanza di macchina virtuale singola all’interno di un set di disponibilità, riceverà inoltre una notifica di posta elettronica avanzata nella notifica della manutenzione pianificata delle macchine virtuali a più istanze.

<!-- Link references -->
[Come configurare un set di disponibilità per le macchine virtuali]: virtual-machines-how-to-configure-availability.md

<!-----HONumber=August15_HO8---->