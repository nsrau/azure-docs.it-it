<properties
	pageTitle="Istruzioni di ASR per Active Directory | Microsoft Azure" 
	description="In questo articolo viene spiegato in dettaglio come creare una soluzione di ripristino di emergenza per AD utilizzando Azure Site Recovery, eseguire failover pianificati/non pianificati/di test utilizzando piani di ripristino con un solo clic, configurazioni supportate e prerequisiti." 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="10/12/2015" 
	ms.author="pratshar"/>

#Soluzione di ripristino di emergenza automatizzata per Active Directory e DNS mediante ASR


Tutte le applicazioni aziendali, ad esempio SharePoint, Dynamics AX e SAP, dipendono dall'infrastruttura di AD e DNS per funzionare correttamente. Quando si crea una soluzione di ripristino di emergenza per una di queste applicazioni, nel caso di un'interruzione è importante proteggere e ripristinare AD prima della riattivazione degli altri componenti dell'applicazione.

Azure Site Recovery è un servizio basato su Azure che fornisce funzionalità di ripristino di emergenza per il coordinamento di replica, failover e ripristino delle macchine virtuali. Azure Site Recovery supporta una serie di tecnologie di replica per la replica, la protezione e il failover semplice delle macchine virtuali e delle applicazioni nei cloud pubblici/privati o di provider di servizi di hosting.

Con Azure Site Recovery è possibile creare un piano di ripristino di emergenza completo e automatizzato per AD. È possibile avviare il failover in pochi secondi da qualsiasi luogo in caso di un'interruzione e fare in modo che AD sia operativa in pochi minuti. Se nel sito primario è disponibile una singola istanza di AD per più applicazioni, ad esempio SharePoint e SAP, e si sceglie di eseguire il failover dell'intero sito, è possibile farlo prima con AD usando ASR e quindi procedere al failover delle altre applicazioni usando piani ripristino specifici di quelle applicazioni.

In questo articolo viene spiegato in dettaglio come creare una soluzione di ripristino di emergenza per AD, eseguire failover pianificati/non pianificati/di test utilizzando piani di ripristino con un solo clic, configurazioni supportate e prerequisiti. Si prevede che i destinatari abbiano familiarità con AD e Azure Site Recovery. Esistono due opzioni consigliate in base alla complessità dell'ambiente locale del cliente per la protezione di AD.

####Opzione 1

Se è presente un numero limitato di applicazioni e un singolo controller di dominio nell'ambiente e si eseguirà il failover dell'intero sito contemporaneamente, è consigliabile utilizzare la replica di ASR per replicare i computer del controller di dominio nel sito secondario (applicabile sia per l’operazione da sito a sito, che da sito ad Azure). La stessa macchina virtuale replicata può essere utilizzata anche per il failover di test.

####Opzione 2
Se è presente un numero elevato di applicazioni ed esiste più di un controller di dominio nell'ambiente o si prevede di eseguire il failover di poche applicazioni contemporaneamente, oltre a consentire la replica di ASR nella macchina virtuale controller di dominio (da utilizzare per il failvoer di test) è consigliabile inoltre configurare un controller di dominio aggiuntivo nel sito di ripristino di emergenza (sito secondario locale o in Azure).

>[AZURE.NOTE]Anche se si intende procedere con l'opzione 2, per effettuare un failover di test è ancora necessario configurare la replica ASR per il controller di dominio. Per altri dettagli, vedere la sezione [Considerazioni sul failover di test](#considerations-for-test-failover).


Nelle sezioni di seguito viene illustrato come abilitare la protezione nel controller di dominio in ASR e come configurare un controller di dominio in Azure.


##Prerequisiti

L'implementazione del ripristino di emergenza per AD utilizzando Azure Site Recovery richiede il completamento dei seguenti prerequisiti.

- Una distribuzione locale del server DNS e di Active Directory
- L’insieme di credenziali di Azure Site Recovery Services deve essere stato creato nella sottoscrizione Microsoft Azure 
- Se Azure è il sito di ripristino, eseguire lo strumento Azure Virtual Machine Readiness Assessment nelle VM per assicurarsi che siano compatibili con le VM di Azure e i Servizi di Azure Site Recovery


##Abilitare la protezione per Controller di dominio/DNS mediante ASR


###Proteggere le VM
Abilitare la protezione della VM Controller di dominio/DNS in ASR. Eseguire la configurazione di Azure Site Recovery rilevante in base alla distribuzione della VM in Hyper-V o su VMware. La frequenza della coerenza per arresto anomalo del sistema da configurare è 15 minuti.

###Configurare le impostazioni di rete della VM
- Per la macchina virtuale Controller di dominio/DNS, configurare le impostazioni di rete in ASR in modo che la VM venga collegata alla rete corretta dopo un failover. 
- Ad esempio, quando si utilizza Azure come sito del ripristino di emergenza, è possibile selezionare la VM nel "Cloud VMM" o nel "Gruppo di protezione" per configurare le impostazioni di rete, come illustrato nello snapshot di seguito.
- 
![Impostazioni di rete della VM](./media/site-recovery-active-directory/VM-Network-Settings.png)

##Abilitare la protezione per AD utilizzando la replica di AD
###Scenario da sito a sito

Creare un controller di dominio nel sito (DR) secondario e durante la promozione del server al ruolo di controller di dominio, assegnare il nome dello stesso dominio in uso nel sito primario. È possibile utilizzare i siti di Active Directory e gli snap-in dei servizi per configurare le impostazioni nell'oggetto di collegamento di sito a cui vengono aggiunti i siti. Configurando le impostazioni in un collegamento di sito, è possibile controllare quando viene eseguita la replica tra due o più siti e con quale frequenza. È possibile fare riferimento a ['Pianificazione della replica tra siti'](https://technet.microsoft.com/it-IT/library/cc731862.aspx) per ulteriori dettagli.

###Scenario da sito ad Azure
Seguire queste istruzioni per creare un [controller di dominio in una rete virtuale di Azure](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md). Assegnare il nome dello stesso dominio in uso nel sito primario durante la promozione del server al ruolo di controller di dominio.

A questo punto è necessario [riconfigurare il server dns per la rete virtuale](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network) per utilizzare il server DNS in Azure
  
![Azure Network](./media/site-recovery-active-directory/azure-network.png)

##Considerazioni sul failover di test
Il failover di test viene eseguito in una rete isolata dalla rete di produzione, in modo che non si verifichi alcun impatto sul carico di lavoro di produzione. Inoltre la maggior parte delle applicazioni richiede la presenza di un controller di dominio e di un server DNS per funzionare. Pertanto, prima del failover di un'applicazione, è necessario creare nella rete isolata un controller di dominio da utilizzare per il failover di test. Il modo più semplice per eseguire questa operazione è abilitare prima la protezione nella macchina virtuale controller di dominio/DNS dominio mediante ASR e prima di attivare il failover di test del piano di ripristino dell'applicazione, avviare il failover di test della macchina virtuale controller di dominio. Di seguito si trovano istruzioni passo passo per eseguire il failover di test:

1. Abilitare la protezione della macchina virtuale DNS/controller di dominio come per qualsiasi altra macchina virtuale.
2. Creare una rete isolata. Qualsiasi rete virtuale creata in Azure per impostazione predefinita è isolata dalle altre reti. È consigliabile che l'intervallo IP di questa rete sia lo stesso della rete di produzione. Non abilitare la connettività da sito a sito in questa rete.
3. Fornire l’IP del DNS nella rete creata nel passaggio precedente come IP che si prevede che la VM DNS ottenga. Se si utilizza Azure come sito di ripristino di emergenza, è possibile fornire l'indirizzo IP per la VM che verrà utilizzata in caso di failover in base alle impostazioni 'IP di destinazione' nella proprietà della VM. Se il sito di ripristino di emergenza è locale e si utilizza DHCP, seguire le istruzioni fornite qui per [configurare DNS e DHCP per il failover di test](site-recovery-failover.md#prepare-dhcp) 

>[AZURE.NOTE]L'indirizzo IP assegnato a una macchina virtuale in un failover di test corrisponde all'indirizzo IP che otterrebbe nel caso di un failover pianificato o non pianificato, a condizione che questo indirizzo IP sia disponibile nella rete di failover di test. Se lo stesso indirizzo IP non è disponibile nella rete di failover di test, la macchina virtuale otterrà un altro indirizzo IP disponibile in tale rete.

4. Passare alla macchina virtuale controller di dominio ed eseguire il suo failover di test nella rete isolata. 
5. Eseguire il failover del piano di ripristino dell'applicazione.
6. Una volta completato il test, contrassegnare il failover di test del processo di macchina virtuale controller di dominio e il piano di ripristino come 'Completato' nella scheda processi in ASR. 

###DNS non nella stessa macchina virtuale come controller di dominio: 
Nel caso in cui DNS non sia presente nella stessa macchina virtuale del controller di dominio, è necessario creare un DNS per il failover di test. Nel caso in cui si trovino sulla stessa VM, è possibile ignorare questa sezione. È possibile utilizzare un server DNS nuovo e creare tutte le zone necessarie. Ad esempio, se il dominio di Active Directory è contoso.com, è possibile creare una zona con il nome contoso.com. Le voci corrispondenti a Active Directory devono essere aggiornate in DNS. Procedere come segue:

- Verificare che le impostazioni seguenti siano state definite prima che vengano attivate altre macchine virtuali del piano di ripristino:
	- La zona deve avere il nome radice della foresta.
	- La zona deve essere sottoposta a backup.
	- La zona deve essere abilitata per aggiornamenti protetti e non protetti.
	- Il sistema di risoluzione della macchina virtuale controller di dominio deve puntare all'indirizzo IP della macchina virtuale DNS.
- Eseguire il comando seguente nella directory della macchina virtuale controller di dominio: nltest /dsregdns

- **Aggiunta della zona**: utilizzare lo script di esempio seguente per aggiungere una zona nel server DNS, consentire aggiornamenti non protetti e aggiungere una voce al DNS:

	    dnscmd /zoneadd contoso.com  /Primary 
	    dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1 
	    dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM> 
	    dnscmd /config contoso.com /allowupdate 1


##Riepilogo
Con Azure Site Recovery è possibile creare un piano di ripristino di emergenza completo e automatizzato per AD. È possibile avviare il failover in pochi secondi da qualsiasi luogo in caso di un'interruzione e fare in modo che AD sia operativa in pochi minuti. Se nel sito primario è disponibile una singola istanza di AD per più applicazioni, ad esempio SharePoint e SAP, e si sceglie di eseguire il failover dell'intero sito, è possibile farlo prima con AD usando ASR e quindi procedere al failover delle altre applicazioni usando piani ripristino specifici di quelle applicazioni.


Fare riferimento a [Istruzioni del carico di lavoro di ripristino del sito](../site-recovery/site-recovery-workload.md) per ulteriori informazioni sulla protezione dei diversi carichi di lavoro mediante ASR.

<!---HONumber=Oct15_HO3-->