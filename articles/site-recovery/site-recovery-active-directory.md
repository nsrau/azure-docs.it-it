<properties
	pageTitle="Proteggere Active Directory e DNS con Azure Site Recovery | Microsoft Azure"
	description="Questo articolo descrive come implementare una soluzione di ripristino di emergenza per Active Directory usando Azure Site Recovery."
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
	ms.date="06/13/2016"
	ms.author="pratshar"/>

# Proteggere Active Directory e DNS con Azure Site Recovery

Le applicazioni aziendali, ad esempio SharePoint, Dynamics AX e SAP, dipendono dall'infrastruttura di Active Directory e DNS per funzionare correttamente. Quando si crea una soluzione di ripristino di emergenza per le applicazioni, è importante ricordare che è necessario proteggere e ripristinare Active Directory e DNS prima degli altri componenti di un'applicazione, per assicurarsi che tutto funzioni correttamente in caso di emergenza.

Site Recovery è un servizio di Azure che fornisce il ripristino di emergenza orchestrando la replica, il failover e il ripristino delle macchine virtuali. Site Recovery supporta una scenari di replica per la protezione e il failover semplice delle macchine virtuali e delle applicazioni nei cloud pubblici, privati o di provider di servizi di hosting.

Con Site Recovery è possibile creare un piano di ripristino di emergenza interamente automatizzato per Active Directory. In caso di un'interruzione, è possibile avviare un failover in pochi secondi da qualsiasi luogo e fare in modo che Active Directory sia operativo in pochi minuti. Se si è distribuito Active Directory per più applicazioni, ad esempio SharePoint e SAP nel sito primario, e si vuole eseguire il failover del sito completo, è possibile eseguire prima il failover di Active Directory tramite Site Recovery e quindi il failover delle altre applicazioni usando piani di ripristino specifici per ogni applicazione.

In questo articolo viene spiegato come creare una soluzione di ripristino di emergenza per Active Directory, come eseguire failover pianificati, non pianificati e di test usando un piano di ripristino con un solo clic, le configurazioni supportate e i prerequisiti. È necessario conoscere Active Directory e Azure Site Recovery prima di iniziare.

Sono consigliate due opzioni a seconda della complessità dell'ambiente.

### Opzione 1

Se si ha un numero ridotto di applicazioni e un singolo controller di dominio e si vuole eseguire il failover dell'intero sito, è consigliabile usare Site Recovery per replicare il controller di dominio nel sito secondario (indipendentemente dal fatto che si esegua il failover in Azure o in un sito secondario). La stessa macchina virtuale replicata può essere usata anche per il failover di test.

### Opzione 2

Se il numero di applicazioni è elevato ed esiste più di un controller di dominio nell'ambiente o se si prevede di eseguire il failover di alcune applicazioni contemporaneamente, oltre a replicare la macchina virtuale controller di dominio con Site Recovery è consigliabile anche configurare un controller di dominio aggiuntivo nel sito di destinazione (Azure o un data center secondario locale).

>[AZURE.NOTE] Anche se si implementerà l'opzione 2, per eseguire un failover di test, sarà tuttavia necessario replicare il controller di dominio usando Site Recovery. Per altre informazioni, leggere le [considerazioni sul failover di test](#considerations-for-test-failover).


Le sezioni seguenti illustrano come abilitare la protezione per un controller di dominio in Site Recovery e come configurare un controller di dominio in Azure.


## Prerequisiti

- Una distribuzione locale del server DNS e di Active Directory.
- Un insieme di credenziali dei servizi Azure Site Recovery in una sottoscrizione di Microsoft Azure.
- Se si esegue la replica in Azure, eseguire lo strumento Azure Virtual Machine Readiness Assessment nelle VM per assicurarsi che siano compatibili con le VM di Azure e i servizi di Azure Site Recovery.


## Abilitare la protezione usando Site Recovery


### Proteggere la macchina virtuale

Abilitare la protezione della macchina virtuale controller di dominio/DNS in Site Recovery. Configurare le impostazioni di Site Recovery in base al tipo di macchina virtuale (Hyper-V o VMware). Si consiglia una frequenza di replica coerente con l'arresto anomalo di 15 minuti.

###Configurare le impostazioni di rete della macchina virtuale

Per la macchina virtuale controller di dominio/DNS, configurare le impostazioni di rete in Site Recovery in modo che la VM venga collegata alla rete corretta dopo il failover. Ad esempio, se si replicano VM Hyper-V in Azure, è possibile selezionare la VM nel cloud VMM o nel gruppo di protezione per configurare le impostazioni di rete, come illustrato di seguito

![Impostazioni di rete della VM](./media/site-recovery-active-directory/VM-Network-Settings.png)  

## Proteggere Active Directory con la replica di Active Directory

### Protezione da sito a sito

Creare un controller di dominio nel sito secondario e specificare il nome dello stesso dominio che verrà usato nel sito primario quando si alza di livello il server a un ruolo di controller di dominio. È possibile usare lo snap-in **Siti e servizi di Active Directory** per configurare le impostazioni nell'oggetto collegamento di sito a cui i siti vengono aggiunti. Configurando le impostazioni in un collegamento di sito, è possibile controllare quando viene eseguita la replica tra due o più siti e con quale frequenza. Per altri dettagli, vedere [Pianificazione della replica tra siti](https://technet.microsoft.com/library/cc731862.aspx).

###Protezione da sito ad Azure

Seguire le istruzioni per creare un [controller di dominio in una rete virtuale di Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Quando si alza di livello il server a un ruolo di controller di dominio, specificare lo stesso nome di dominio usato nel sito primario.

[Riconfigurare quindi il server DNS per la rete virtuale](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network), per usare il server DNS in Azure.

![Azure Network](./media/site-recovery-active-directory/azure-network.png)

## Considerazioni sul failover di test

Il failover di test si verifica in una rete isolata dalla rete di produzione, in modo che non si verifichi alcun impatto sui carichi di lavoro di produzione.

La maggior parte delle applicazioni richiede anche la presenza di un controller di dominio e di un server DNS per funzionare, quindi, prima del failover dell'applicazione, è necessario creare un controller di dominio nella rete isolata da usare per il failover di test. Il modo più semplice per eseguire questa operazione è abilitare la protezione nella macchina virtuale controller di dominio/DNS con Site Recovery ed eseguire un failover di test di tale macchina virtuale, prima di eseguire un failover di test del piano di ripristino per l'applicazione. Di seguito viene indicato come procedere:

1. Abilitare la protezione in Site Recovery per la macchina virtuale controller di dominio/DNS.
2. Creare una rete isolata. Qualsiasi rete virtuale creata in Azure per impostazione predefinita è isolata dalle altre reti. È consigliabile che l'intervallo di indirizzi IP di questa rete sia lo stesso della rete di produzione. Non abilitare la connettività da sito a sito in questa rete.
3. Specificare un indirizzo IP DNS nella rete creata, come indirizzo IP previsto per la macchina virtuale DNS. Se si esegue la replica in Azure, fornire l'indirizzo IP per la VM che verrà usata in caso di failover in base alle impostazioni **IP di destinazione** nella proprietà della VM. Se si esegue la replica in un altro sito locale e si usa DHCP, seguire le istruzioni per [configurare DNS e DHCP per il failover di test](site-recovery-failover.md#prepare-dhcp).

>[AZURE.NOTE] L'indirizzo IP allocato a una macchina virtuale durante un failover di test corrisponde all'indirizzo IP che otterrebbe durante un failover pianificato o non pianificato, se l'indirizzo IP è disponibile nella rete di failover di test. Se non lo è, la macchina virtuale riceve un altro indirizzo IP disponibile nella rete di failover di test.

4. Nella macchina virtuale controller di dominio eseguire un failover di test nella rete isolata.
5. Eseguire un failover di test per il piano di ripristino dell'applicazione.
6. Dopo avere completato il test, contrassegnare il failover di test del processo della macchina virtuale controller di dominio e il piano di ripristino come "Completato" nella scheda **Processi** nel portale di Site Recovery.

### DNS e controller di dominio su computer diversi

Se DNS non è presente nella stessa macchina virtuale del controller di dominio, è necessario creare una VM DNS per il failover di test. Se si trovano nella stessa VM, è possibile ignorare questa sezione.

È possibile utilizzare un server DNS nuovo e creare tutte le zone necessarie. Ad esempio, se il dominio di Active Directory è contoso.com, è possibile creare una zona DNS con il nome contoso.com. Le voci corrispondenti a Active Directory devono essere aggiornate in DNS, come segue:

1. Verificare che queste impostazioni siano state definite prima che vengano attivate altre macchine virtuali del piano di ripristino:

	- La zona deve avere il nome radice della foresta.
	- La zona deve essere sottoposta a backup.
	- La zona deve essere abilitata per aggiornamenti protetti e non protetti.
	- Il sistema di risoluzione della macchina virtuale controller di dominio deve puntare all'indirizzo IP della macchina virtuale DNS.

2. Eseguire il comando seguente nella macchina virtuale controller di dominio:

	`nltest /dsregdns`  

3. Aggiungere una zona nel server DNS, consentire aggiornamenti non protetti e aggiungere una voce al DNS:

	    dnscmd /zoneadd contoso.com  /Primary
	    dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
	    dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
	    dnscmd /config contoso.com /allowupdate 1


## Passaggi successivi

Per altre informazioni sulla protezione dei carichi di lavoro aziendali con Azure Site Recovery, leggere [Quali carichi di lavoro è possibile proteggere?](../site-recovery/site-recovery-workload.md).

<!---HONumber=AcomDC_0810_2016-->