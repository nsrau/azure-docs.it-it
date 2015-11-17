<properties
   pageTitle="Procedure consigliate per gli aggiornamenti software nell'ambiente IaaS di Microsoft Azure | Microsoft Azure"
   description="L'articolo fornisce una raccolta di procedure consigliate per gli aggiornamenti software in un ambiente IaaS di Microsoft Azure. È destinato ai professionisti IT e agli analisti della sicurezza che si occupano quotidianamente di controllo modifiche, aggiornamenti software e gestione di asset, inclusi i responsabili delle attività di sicurezza e conformità dell'organizzazione."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriD"
   manager="swadhwa"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/03/2015"
   ms.author="yurid"/>

#Procedure consigliate per gli aggiornamenti software nell'ambiente IaaS di Microsoft Azure

Prima di addentrarsi in una qualsiasi discussione sulle procedure consigliate per un ambiente IaaS di Azure, è importante comprendere quali sono gli scenari che richiedono la gestione degli aggiornamenti software. Il diagramma sottostante dovrebbe risultare utile in questo senso:

![Modelli di cloud e responsabilità](./media/azure-security-best-practices-software-updates-iaas/sec-cloudstack.png)

Nel modello di data center tradizionale, in cui l'intera infrastruttura è locale, l'utente ha la piena responsabilità della gestione degli aggiornamenti per sistemi operativi, applicazioni, dispositivi di rete, come router e commutatori, e hardware (firmware). In uno scenario IaaS si dovrà comunque continuare a gestire gli aggiornamenti dei sistemi operativi e delle applicazioni, tuttavia l'intera infrastruttura su cui poggiano i sistemi operativi e le applicazioni viene gestita da Microsoft. In tutti questi modelli i clienti continuano a essere proprietari dei propri dati e responsabili della loro protezione a livello di endpoint.

In uno scenario PaaS si avranno ancora meno responsabilità per gli aggiornamenti software, perché la gestione degli aggiornamenti per il sistema operativo è a cura di Microsoft. In uno scenario SaaS Microsoft si assume la responsabilità degli aggiornamenti software per l'intero stack.

Questi stessi principi si applicano a uno scenario ibrido, dove l'azienda usa macchine virtuali IaaS di Azure che comunicano con le risorse locali, come illustrato nel diagramma seguente.

![Tipico scenario ibrido con Microsoft Azure](./media/azure-security-best-practices-software-updates-iaas/sec-azconnectonpre.png)

## Valutazione iniziale

Anche se l'azienda usa già un sistema di gestione degli aggiornamenti e ha già implementato criteri di aggiornamento software, è importante riesaminare spesso le valutazioni dei criteri precedenti e aggiornarli in base ai requisiti attuali. Ciò significa che è necessario avere familiarità con lo stato attuale delle risorse dell'azienda. Per raggiungere questo stato, è necessario conoscere quanto segue:

-   Computer fisici e virtuali dell'azienda.

-   Sistemi operativi e versioni in esecuzione su ognuno di questi computer fisici e virtuali.

-   Aggiornamenti software attualmente installati in ogni computer (versioni dei Service Pack, aggiornamenti software e altre modifiche).

-   Funzione eseguita da ogni computer dell'azienda.

-   Applicazioni e programmi in esecuzione in ogni computer.

-   Proprietà e informazioni di contatto per ogni computer.

-   Asset disponibili nel proprio ambiente e il relativo valore per determinare in quali aree sono necessarie maggiori attenzioni e protezione.

-   Problemi noti di sicurezza e processi in atto nell'azienda per identificare i nuovi problemi di sicurezza o le modifiche a livello di sicurezza.

-   Contromisure distribuite per proteggere l'ambiente.

È consigliabile aggiornare regolarmente queste informazioni, che devono essere immediatamente disponibili per chi è coinvolto nel processo di gestione degli aggiornamenti software.

## Stabilire una baseline

Una parte importante del processo di gestione degli aggiornamenti software è la creazione di installazioni standard iniziali, dette baseline, di versioni del sistema operativo, applicazioni e hardware per i computer dell'azienda. Una baseline è la configurazione di un prodotto o un sistema definita in un momento specifico. La baseline di un'applicazione o un sistema operativo, ad esempio, consente di ricompilare un computer o un servizio a uno stato specifico.

Le baseline sono fondamentali per trovare e correggere potenziali problemi e semplificare il processo di gestione degli aggiornamenti software, sia riducendo il numero di aggiornamenti software che è necessario distribuire nell'azienda, sia migliorando la capacità di monitorare la conformità.

Dopo l'esecuzione del controllo iniziale dell'azienda, è consigliabile usare le informazioni ottenute da tale controllo per definire una baseline operativa per i componenti IT nel proprio ambiente di produzione. Potrebbero essere necessarie più baseline, a seconda dei diversi tipi di hardware e software distribuiti nell'ambiente di produzione.

Ad esempio, alcuni server che eseguono Windows Server 2012 richiedono un aggiornamento software per impedire che rimangano in sospeso all'avvio del processo di arresto. Una baseline per questi server dovrà includere questo aggiornamento software.

Nelle organizzazioni di grandi dimensioni è spesso utile suddividere i computer dell'azienda in categorie di asset e mantenere ogni categoria in una baseline standard usando le stesse versioni del software e dei relativi aggiornamenti. È quindi possibile usare queste categorie di asset nel definire le priorità per una distribuzione di aggiornamenti software.

## Sottoscrivere i servizi di notifica degli aggiornamenti software appropriati

Dopo aver eseguito un controllo iniziale del software in uso nell'azienda, è necessario determinare il metodo migliore per ricevere le notifiche di nuovi aggiornamenti software per ogni prodotto software e versione. A seconda del prodotto software, i metodi di notifica migliori potrebbero essere notifiche tramite posta elettronica, siti Web o pubblicazioni su computer.

Ad esempio, Microsoft Security Response Center (MSRC) risponde a tutte le problematiche relative alla sicurezza dei prodotti Microsoft e fornisce il Bollettino Microsoft sulla sicurezza, un servizio gratuito di notifica tramite posta elettronica sulle ultime vulnerabilità identificate e sugli aggiornamenti software rilasciati per risolvere tali vulnerabilità. È possibile sottoscrivere questo servizio all'indirizzo http://www.microsoft.com/technet/security/bulletin/notify.mspx

## Considerazioni sugli aggiornamenti software

Dopo aver eseguito un controllo iniziale del software usato nell'azienda, è necessario determinare i requisiti per l'installazione del sistema di gestione degli aggiornamenti software, che dipende dal sistema di gestione degli aggiornamenti software già in uso. Per WSUS vedere [Procedure consigliate per Windows Server Update Services](https://technet.microsoft.com/it-IT/library/Cc708536), per System Center vedere [Pianificazione degli aggiornamenti software in Configuration Manager](https://technet.microsoft.com/it-IT/library/gg712696).

Ci sono tuttavia alcune considerazioni generali e procedure consigliate che è possibile applicare indipendentemente dalla soluzione usata, come illustrato nelle sezioni seguenti.

### Configurazione dell'ambiente

Quando si pianifica la configurazione dell'ambiente di gestione degli aggiornamenti software, prendere in considerazione le procedure seguenti:

-   **Creare raccolte di aggiornamenti software di produzione in base a criteri stabili**: in genere, l'uso di criteri stabili per creare raccolte per l'inventario e la distribuzione degli aggiornamenti software contribuisce a semplificare tutte le fasi del processo di gestione degli aggiornamenti software. I criteri stabili possono includere la versione del sistema operativo client installato e il livello di Service Pack, il ruolo del sistema o l'organizzazione di destinazione.

-   **Creare raccolte di pre-produzione che includono i computer di riferimento**: la raccolta di pre-produzione deve includere configurazioni rappresentative delle versioni del sistema operativo, del software line-of-business e di altro software in esecuzione nell'azienda.

È anche opportuno considerare dove si troverà il server di aggiornamento software, se nell'infrastruttura IaaS di Azure nel cloud o in locale. Si tratta di una decisione importante, perché è necessario valutare la quantità di traffico tra le risorse locali e l'infrastruttura di Azure. Per altre informazioni su come connettere l'infrastruttura locale ad Azure, vedere [Connettere una rete locale a una rete virtuale di Microsoft Azure](https://technet.microsoft.com/it-IT/library/Dn786406.aspx).

Le opzioni di progettazione che determinano dove si troverà il server di aggiornamento variano anche in base all'infrastruttura corrente e al sistema di aggiornamento software attualmente in uso. Per WSUS vedere [Distribuire Windows Server Update Services nell'organizzazione](https://technet.microsoft.com/it-IT/library/hh852340.aspx) e per System Center Configuration Manager vedere [Pianificazione di siti e gerarchie in Configuration Manager](https://technet.microsoft.com/it-IT/library/Gg712681.aspx).

### Backup

I backup regolari sono importanti non solo per la piattaforma di gestione degli aggiornamenti software stessa, ma anche per i server che verranno aggiornati. Le organizzazioni con un [processo di gestione delle modifiche](https://technet.microsoft.com/it-IT/library/cc543216.aspx) in atto, richiederanno all'IT di giustificare i motivi per cui il server deve essere aggiornato, il tempo di inattività stimato e il possibile impatto. Per garantire che sia disponibile una configurazione di ripristino dello stato precedente nel caso di un aggiornamento non riuscito, assicurarsi di eseguire regolarmente il backup del sistema.

Ecco alcune opzioni di backup per l'ambiente IaaS di Azure:

-   [Protezione del carico di lavoro IaaS di Azure tramite Data Protection Manager](https://azure.microsoft.com/blog/2014/09/08/azure-iaas-workload-protection-using-data-protection-manager/)

-   [Eseguire il backup di macchine virtuali di Azure](../backup/backup-azure-vms.md)

### Monitoraggio

Per ogni aggiornamento software autorizzato, è opportuno eseguire report regolari per monitorare il numero di aggiornamenti mancanti o installati oppure di aggiornamenti con stato incompleto. Analogamente, la creazione di report per gli aggiornamenti software non ancora autorizzati può rendere più facile prendere decisioni di distribuzione.

È anche consigliabile prendere in considerazione le attività seguenti:

-   Eseguire un controllo degli aggiornamenti della sicurezza applicabili e installati per tutti i computer dell'azienda.

-   Autorizzare e distribuire gli aggiornamenti ai computer appropriati.

-   Tenere traccia dell'inventario e aggiornare lo stato dell'installazione e di avanzamento per tutti i computer dell'azienda.

Oltre alle considerazioni generali descritte in questo articolo, è consigliabile tenere presenti anche le procedure consigliate per ogni prodotto, ad esempio, se si ha una macchina virtuale in Azure con SQL Server, assicurarsi di seguire le indicazioni relative agli aggiornamenti software per quel prodotto.

## Passaggi successivi

Usare le linee guida descritte in questo articolo per determinare le opzioni migliori per gli aggiornamenti software delle macchine virtuali nell'ambiente IaaS di Azure. Esistono molte analogie tra le procedure consigliate per gli aggiornamenti software in un data center tradizionale e un ambiente IaaS di Azure, quindi è consigliabile valutare i criteri di aggiornamento software attuali e includere nel processo di aggiornamento software globale le macchine virtuali di Azure e le procedure consigliate pertinenti descritte in questo articolo.

<!---HONumber=Nov15_HO2-->