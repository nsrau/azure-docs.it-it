---
title: Proteggere Active Directory e DNS con Azure Site Recovery | Documentazione Microsoft
description: Questo articolo descrive come implementare una soluzione di ripristino di emergenza per Active Directory usando Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: af1d9b26-1956-46ef-bd05-c545980b72dc
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 1/9/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: feb0200fc27227f546da8c98f21d54f45d677c98
ms.openlocfilehash: a583225b4f3acd747a10c1c1fd337bc1b7ac599c


---
# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Proteggere Active Directory e DNS con Azure Site Recovery
Le applicazioni aziendali, ad esempio SharePoint, Dynamics AX e SAP, dipendono dall'infrastruttura di Active Directory e DNS per funzionare correttamente. Quando si crea una soluzione di ripristino di emergenza per le applicazioni, è importante ricordare che è necessario proteggere e ripristinare Active Directory e DNS prima degli altri componenti di un'applicazione, per assicurarsi che tutto funzioni correttamente in caso di emergenza.

Site Recovery è un servizio di Azure che fornisce il ripristino di emergenza orchestrando la replica, il failover e il ripristino delle macchine virtuali. Site Recovery supporta vari scenari di replica per la protezione e il failover semplice delle macchine virtuali e delle applicazioni nei cloud pubblici, privati o di provider di servizi di hosting.

Con Site Recovery è possibile creare un piano di ripristino di emergenza interamente automatizzato per Active Directory. In caso di interruzione, è possibile avviare un failover in pochi secondi da qualsiasi luogo e fare in modo che Active Directory sia operativo in pochi minuti. Se si è distribuito Active Directory per più applicazioni, ad esempio SharePoint e SAP nel sito primario, e si vuole eseguire il failover del sito completo, è possibile eseguire prima il failover di Active Directory tramite Site Recovery e quindi il failover delle altre applicazioni usando piani di ripristino specifici per ogni applicazione.

In questo articolo viene spiegato come creare una soluzione di ripristino di emergenza per Active Directory, come eseguire failover pianificati, non pianificati e di test usando un piano di ripristino con un solo clic, le configurazioni supportate e i prerequisiti.  È necessario conoscere Active Directory e Azure Site Recovery prima di iniziare.

## <a name="replicating-domain-controller"></a>Replica del controller di dominio

È necessario configurare la [replica con Site Recovery](#enable-protection-using-site-recovery) in almeno una macchina virtuale che ospita controller di dominio e DNS. Se nell'ambiente sono presenti [più controller di dominio](#environment-with-multiple-domain-controllers), oltre a replicare la macchina virtuale controller di dominio con Site Recovery sarà necessario anche configurare un [controller di dominio aggiuntivo](#protect-active-directory-with-active-directory-replication) nel sito di destinazione (Azure o un data center secondario locale). 

### <a name="single-domain-controller-environment"></a>Ambiente con singolo controller di dominio
Se sono presenti un numero ridotto di applicazioni e un singolo controller di dominio e si vuole eseguire il failover dell'intero sito, è consigliabile usare Site Recovery per replicare il controller di dominio nel sito secondario (indipendentemente dal fatto che si esegua il failover in Azure o in un sito secondario). La stessa macchina virtuale controller di dominio/DNS replicata può essere usata anche per il [failover di test](#test-failover-considerations).

### <a name="environment-with-multiple-domain-controllers"></a>Ambiente con più controller di dominio
Se il numero di applicazioni è elevato e l'ambiente include più controller di dominio o se si intende eseguire il failover di poche applicazioni alla volta, oltre a replicare la macchina virtuale controller di dominio con Site Recovery è consigliabile anche configurare un [controller di dominio aggiuntivo](#protect-active-directory-with-active-directory-replication) nel sito di destinazione (Azure o data center secondario locale). In questo scenario, il controller di dominio replicato da Site Recovery verrà usato per il [failover di test](#test-failover-considerations) e il controller di dominio aggiuntivo nel sito di destinazione verrà usato quando si esegue un failover. 


Le sezioni seguenti illustrano come abilitare la protezione per un controller di dominio in Site Recovery e come configurare un controller di dominio in Azure.

## <a name="prerequisites"></a>Prerequisiti
* Una distribuzione locale del server DNS e di Active Directory.
* Un insieme di credenziali dei servizi Azure Site Recovery in una sottoscrizione di Microsoft Azure.
* Se si esegue la replica in Azure, eseguire lo strumento Azure Virtual Machine Readiness Assessment nelle VM per assicurarsi che siano compatibili con le VM di Azure e i servizi di Azure Site Recovery.

## <a name="enable-protection-using-site-recovery"></a>Abilitare la protezione usando Site Recovery
### <a name="protect-the-virtual-machine"></a>Proteggere la macchina virtuale
Abilitare la protezione della macchina virtuale controller di dominio/DNS in Site Recovery. Configurare le impostazioni di Site Recovery in base al tipo di macchina virtuale (Hyper-V o VMware). Si consiglia una frequenza di replica coerente con l'arresto anomalo di 15 minuti.

### <a name="configure-virtual-machine-network-settings"></a>Configurare le impostazioni di rete della macchina virtuale
Per la macchina virtuale controller di dominio/DNS, configurare le impostazioni di rete in Site Recovery in modo che la VM venga collegata alla rete corretta dopo il failover. Ad esempio, se si replicano VM Hyper-V in Azure, è possibile selezionare la VM nel cloud VMM o nel gruppo di protezione per configurare le impostazioni di rete, come illustrato di seguito

![Impostazioni di rete della VM](./media/site-recovery-active-directory/DNS-Target-IP.png)

## <a name="protect-active-directory-with-active-directory-replication"></a>Proteggere Active Directory con la replica di Active Directory
### <a name="site-to-site-protection"></a>Protezione da sito a sito
Creare un controller di dominio nel sito secondario e specificare il nome dello stesso dominio che verrà usato nel sito primario quando si alza di livello il server a un ruolo di controller di dominio. È possibile usare lo snap-in **Siti e servizi di Active Directory** per configurare le impostazioni nell'oggetto collegamento di sito a cui i siti vengono aggiunti. Configurando le impostazioni in un collegamento di sito, è possibile controllare quando viene eseguita la replica tra due o più siti e con quale frequenza. Per altri dettagli, vedere [Pianificazione della replica tra siti](https://technet.microsoft.com/library/cc731862.aspx) .

### <a name="site-to-azure-protection"></a>Protezione da sito ad Azure
Seguire le istruzioni per creare un [controller di dominio in una rete virtuale di Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Quando si alza di livello il server al ruolo di controller di dominio, specificare lo stesso nome di dominio usato nel sito primario.

[Riconfigurare quindi il server DNS per la rete virtuale](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network), per usare il server DNS in Azure.

![Azure Network](./media/site-recovery-active-directory/azure-network.png)

**DNS nella rete di produzione di Azure**

## <a name="test-failover-considerations"></a>considerazioni sul failover di test
Il failover di test si verifica in una rete isolata dalla rete di produzione, in modo che non si verifichi alcun impatto sui carichi di lavoro di produzione.

La maggior parte delle applicazioni richiede anche la presenza di un controller di dominio e di un server DNS per funzionare, quindi, prima del failover dell'applicazione, è necessario creare un controller di dominio nella rete isolata da usare per il failover di test. Il modo più semplice per eseguire questa operazione è abilitare la protezione nella macchina virtuale controller di dominio/DNS con Site Recovery ed eseguire un failover di test di tale macchina virtuale, prima di eseguire un failover di test del piano di ripristino per l'applicazione. Di seguito viene indicato come procedere:

1. Abilitare la protezione in Site Recovery per la macchina virtuale controller di dominio/DNS.
1. Creare una rete isolata. Qualsiasi rete virtuale creata in Azure per impostazione predefinita è isolata dalle altre reti. È consigliabile che l'intervallo di indirizzi IP di questa rete sia lo stesso della rete di produzione. Non abilitare la connettività da sito a sito in questa rete.
1. Specificare un indirizzo IP DNS nella rete creata, come indirizzo IP previsto per la macchina virtuale DNS. Se si esegue la replica in Azure, specificare l'indirizzo IP per la VM che si userà nel failover nell'impostazione **IP di destinazione** in **Calcolo e rete**. 

    ![IP di destinazione](./media/site-recovery-active-directory/DNS-Target-IP.png)
    **IP di destinazione**

    ![Rete di test di Azure](./media/site-recovery-active-directory/azure-test-network.png)

    **DNS nella rete di test di Azure**

1. Se si esegue la replica in un altro sito locale e si usa DHCP, seguire le istruzioni per [configurare DNS e DHCP per il failover di test](site-recovery-test-failover-vmm-to-vmm.md#prepare-dhcp)
1. Eseguire un failover di test della macchina virtuale controller di dominio nella rete isolata. Per effettuare il failover di test, usare il più recente punto di ripristino **coerente con l'applicazione** disponibile della macchina virtuale controller di dominio. 
1. Eseguire un failover di test per il piano di ripristino dell'applicazione.
1. Dopo avere completato il test, contrassegnare il processo di failover di test della macchina virtuale controller di dominio del piano di ripristino come "Completato" nella scheda **Processi** nel portale di Site Recovery.


> [!TIP]
> L'indirizzo IP allocato a una macchina virtuale durante un failover di test corrisponde all'indirizzo IP che otterrebbe durante un failover pianificato o non pianificato, se l'indirizzo IP è disponibile nella rete di failover di test. Se non lo è, la macchina virtuale riceve un altro indirizzo IP disponibile nella rete di failover di test.
> 
> 


### <a name="removing-reference-to-other-domain-controllers"></a>Rimozione del riferimento ad altri controller di dominio
Quando si effettua un failover di test, nella rete di test non si includono tutti i controller di dominio. Per rimuovere il riferimento ad altri controller di dominio presenti nell'ambiente di produzione, è necessario [riassegnare i ruoli FSMO di Active Directory ed eseguire la pulizia dei metadati](http://aka.ms/ad_seize_fsmo) per i controller di dominio mancanti. 

### <a name="troubleshooting-domain-controller-issues-during-test-failover"></a>Risolvere i problemi del controller di dominio durante il failover di test


Al prompt dei comandi eseguire questo comando per verificare se le cartelle SYSVOL e NETLOGON sono condivise:

    NET SHARE

Al prompt dei comandi eseguire questo comando per verificare che il controller di dominio funzioni correttamente:

    dcdiag /v > dcdiag.txt

Nel log di output cercare il testo seguente per verificare il corretto funzionamento del controller di dominio. 

* "passed test Connectivity"
* "passed test Advertising"
* "passed test MachineAccount"

Se le condizioni precedenti vengono soddisfatte, è probabile che il controller di dominio funzioni correttamente. In caso contrario, provare a seguire questa procedura.


* Eseguire un ripristino autorevole del controller di dominio.
    * Nonostante [non sia consigliabile usare il servizio Replica file](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), se viene comunque usato seguire la procedura riportata [qui](https://support.microsoft.com/en-in/kb/290762) per eseguire un ripristino autorevole. Per altre informazioni sui valori BURFLAG citati nel collegamento precedente, vedere [qui](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Se si usa la replica DFSR, per eseguire un ripristino autorevole seguire la procedura disponibile [qui](https://support.microsoft.com/en-us/kb/2218556). A tale scopo è anche possibile usare le funzioni di Powershell disponibili in questo [collegamento](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/). 
    
* Ignorare il requisito della sincronizzazione iniziale impostando la chiave del Registro di sistema seguente su 0. Se questo valore DWORD non esiste, può essere creato nel nodo "Parameters". Per altre informazioni, vedere [qui](https://support.microsoft.com/en-us/kb/2001093).

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations

* Disabilitare il requisito della disponibilità di un server di catalogo globale per la convalida dell'accesso utente impostando la chiave del Registro di sistema seguente su 1. Se questo valore DWORD non esiste, può essere creato nel nodo "Lsa". Per altre informazioni, vedere [qui](http://support.microsoft.com/kb/241789/EN-US).

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures



### <a name="dns-and-domain-controller-on-different-machines"></a>DNS e controller di dominio su computer diversi
Se DNS non è presente nella stessa macchina virtuale del controller di dominio, è necessario creare una VM DNS per il failover di test. Se si trovano nella stessa VM, è possibile ignorare questa sezione.

È possibile utilizzare un server DNS nuovo e creare tutte le zone necessarie. Ad esempio, se il dominio di Active Directory è contoso.com, è possibile creare una zona DNS con il nome contoso.com. Le voci corrispondenti a Active Directory devono essere aggiornate in DNS, come segue:

1. Verificare che queste impostazioni siano state definite prima che vengano attivate altre macchine virtuali del piano di ripristino:
   
   * La zona deve avere il nome radice della foresta.
   * La zona deve essere sottoposta a backup.
   * La zona deve essere abilitata per aggiornamenti protetti e non protetti.
   * Il sistema di risoluzione della macchina virtuale controller di dominio deve puntare all'indirizzo IP della macchina virtuale DNS.
2. Eseguire il comando seguente nella macchina virtuale controller di dominio:
   
    `nltest /dsregdns`
3. Aggiungere una zona nel server DNS, consentire aggiornamenti non protetti e aggiungere una voce al DNS:
   
        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla protezione dei carichi di lavoro aziendali con Azure Site Recovery, leggere [Quali carichi di lavoro è possibile proteggere?](site-recovery-workload.md).




<!--HONumber=Jan17_HO4-->


