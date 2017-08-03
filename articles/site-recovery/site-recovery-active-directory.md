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
ms.date: 7/20/2017
ms.author: pratshar
ms.translationtype: HT
ms.sourcegitcommit: 22aa82e5cbce5b00f733f72209318c901079b665
ms.openlocfilehash: 197441fc24c178695d4eada6db59f503b21672ad
ms.contentlocale: it-it
ms.lasthandoff: 07/24/2017

---
# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Proteggere Active Directory e DNS con Azure Site Recovery
Le applicazioni aziendali, ad esempio SharePoint, Dynamics AX e SAP, dipendono dall'infrastruttura di Active Directory e DNS per funzionare correttamente. Quando si crea una soluzione di ripristino di emergenza per le applicazioni, è importante ricordare che è necessario proteggere e ripristinare Active Directory e DNS prima degli altri componenti di un'applicazione, per assicurarsi che tutto funzioni correttamente in caso di emergenza.

Site Recovery è un servizio di Azure che fornisce il ripristino di emergenza orchestrando la replica, il failover e il ripristino delle macchine virtuali. Site Recovery supporta vari scenari di replica per la protezione e il failover semplice delle macchine virtuali e delle applicazioni nei cloud pubblici, privati o di provider di servizi di hosting.

Con Site Recovery è possibile creare un piano di ripristino di emergenza interamente automatizzato per Active Directory. In caso di interruzione, è possibile avviare un failover in pochi secondi da qualsiasi luogo e fare in modo che Active Directory sia operativo in pochi minuti. Se si è distribuito Active Directory per più applicazioni, ad esempio SharePoint e SAP nel sito primario, e si vuole eseguire il failover del sito completo, è possibile eseguire prima il failover di Active Directory tramite Site Recovery e quindi il failover delle altre applicazioni usando piani di ripristino specifici per ogni applicazione.

In questo articolo viene spiegato come creare una soluzione di ripristino di emergenza per Active Directory, come eseguire failover pianificati, non pianificati e di test usando un piano di ripristino con un solo clic, le configurazioni supportate e i prerequisiti.  È necessario conoscere Active Directory e Azure Site Recovery prima di iniziare.

## <a name="replicating-domain-controller"></a>Replica del controller di dominio

È necessario configurare la [replica con Site Recovery](#enable-protection-using-site-recovery) in almeno una macchina virtuale che ospita controller di dominio e DNS. Se nell'ambiente sono presenti [più controller di dominio](#environment-with-multiple-domain-controllers), oltre a replicare la macchina virtuale controller di dominio con Site Recovery sarà necessario anche configurare un [controller di dominio aggiuntivo](#protect-active-directory-with-active-directory-replication) nel sito di destinazione (Azure o un data center secondario locale). 

### <a name="single-domain-controller-environment"></a>Ambiente con singolo controller di dominio
Se sono presenti un numero ridotto di applicazioni e un singolo controller di dominio e si vuole eseguire il failover dell'intero sito, è consigliabile usare Site Recovery per replicare il controller di dominio nel sito secondario, indipendentemente dal fatto che si esegua il failover in Azure o in un sito secondario. La stessa macchina virtuale controller di dominio/DNS replicata può essere usata anche per il [failover di test](#test-failover-considerations).

### <a name="environment-with-multiple-domain-controllers"></a>Ambiente con più controller di dominio
Se il numero di applicazioni è elevato e l'ambiente include più controller di dominio o se si intende eseguire il failover di poche applicazioni alla volta, oltre a replicare la macchina virtuale controller di dominio con Site Recovery è consigliabile anche configurare un [controller di dominio aggiuntivo](#protect-active-directory-with-active-directory-replication) nel sito di destinazione (Azure o data center secondario locale). Per il [failover di test](#test-failover-considerations), usare il controller di dominio replicato da Site Recovery e per il failover, il controller di dominio aggiuntivo nel sito di destinazione. 


Le sezioni seguenti illustrano come abilitare la protezione per un controller di dominio in Site Recovery e come configurare un controller di dominio in Azure.

## <a name="prerequisites"></a>Prerequisiti
* Una distribuzione locale del server DNS e di Active Directory.
* Un insieme di credenziali dei servizi Azure Site Recovery in una sottoscrizione di Microsoft Azure.
* Se si esegue la replica in Azure, eseguire lo strumento Azure Virtual Machine Readiness Assessment nelle VM per assicurarsi che siano compatibili con le VM di Azure e i servizi di Azure Site Recovery.

## <a name="enable-protection-using-site-recovery"></a>Abilitare la protezione usando Site Recovery
### <a name="protect-the-virtual-machine"></a>Proteggere la macchina virtuale
Abilitare la protezione della macchina virtuale controller di dominio/DNS in Site Recovery. Configurare le impostazioni di Site Recovery in base al tipo di macchina virtuale (Hyper-V o VMware). Il controller di dominio replicato con Site Recovery viene usato per il [test di failover](#test-failover-considerations). Assicurarsi che vengano soddisfatti i requisiti seguenti:

1. Il controller di dominio è un server di catalogo globale
2. Il controller di dominio deve essere proprietario del ruolo FSMO per i ruoli che saranno necessari durante un failover di test; altrimenti questi ruoli dovranno essere [riassegnati](http://aka.ms/ad_seize_fsmo) dopo il failover.

### <a name="configure-virtual-machine-network-settings"></a>Configurare le impostazioni di rete della macchina virtuale
Per la macchina virtuale controller di dominio/DNS, configurare le impostazioni di rete in Site Recovery in modo che la macchina virtuale venga collegata alla rete corretta dopo il failover. 

![Impostazioni di rete della VM](./media/site-recovery-active-directory/DNS-Target-IP.png)

## <a name="protect-active-directory-with-active-directory-replication"></a>Proteggere Active Directory con la replica di Active Directory
### <a name="site-to-site-protection"></a>Protezione da sito a sito
Creare un controller di dominio nel sito secondario. Quando si alza di livello il server al ruolo di controller di dominio, specificare il nome dello stesso dominio usato nel sito primario. È possibile usare lo snap-in **Siti e servizi di Active Directory** per configurare le impostazioni nell'oggetto collegamento di sito a cui i siti vengono aggiunti. Configurando le impostazioni in un collegamento di sito, è possibile controllare quando viene eseguita la replica tra due o più siti e con quale frequenza. Per altre informazioni, vedere [Pianificazione della replica tra siti](https://technet.microsoft.com/library/cc731862.aspx) .

### <a name="site-to-azure-protection"></a>Protezione da sito ad Azure
Seguire le istruzioni per creare un [controller di dominio in una rete virtuale di Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Quando si alza di livello il server al ruolo di controller di dominio, specificare lo stesso nome di dominio usato nel sito primario.

[Riconfigurare quindi il server DNS per la rete virtuale](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network), per usare il server DNS in Azure.

![Azure Network](./media/site-recovery-active-directory/azure-network.png)

**DNS nella rete di produzione di Azure**

## <a name="test-failover-considerations"></a>considerazioni sul failover di test
Il failover di test si verifica in una rete isolata dalla rete di produzione, in modo che non si verifichi alcun impatto sui carichi di lavoro di produzione.

Inoltre la maggior parte delle applicazioni richiede la presenza di un controller di dominio e di un server DNS per funzionare. Prima del failover di un'applicazione, è quindi necessario creare nella rete isolata un controller di dominio da usare per il failover di test. Il modo più semplice per eseguire questa operazione è replicare una macchina virtuale DNS o controller di dominio con Site Recovery. Eseguire quindi un failover di test della macchina virtuale controller di dominio prima di eseguire un failover di test del piano di ripristino per l'applicazione. Di seguito viene indicato come procedere:

1. [Replicare](site-recovery-replicate-vmware-to-azure.md) la macchina virtuale controller di dominio/DNS usando Site Recovery.
1. Creare una rete isolata. Qualsiasi rete virtuale creata in Azure per impostazione predefinita è isolata dalle altre reti. È consigliabile che l'intervallo di indirizzi IP di questa rete sia lo stesso della rete di produzione. Non abilitare la connettività da sito a sito in questa rete.
1. Specificare un indirizzo IP DNS nella rete creata, come indirizzo IP previsto per la macchina virtuale DNS. Se si esegue la replica in Azure, specificare l'indirizzo IP per la VM usata nel failover nell'impostazione **IP di destinazione** in **Calcolo e rete**. 

    ![IP di destinazione](./media/site-recovery-active-directory/DNS-Target-IP.png)**IP di destinazione**

    ![Rete di test di Azure](./media/site-recovery-active-directory/azure-test-network.png)

    **DNS nella rete di test di Azure**

> [!TIP]
> Site Recovery tenta di creare le macchine virtuali di test in una subnet con lo stesso nome e con lo stesso IP specificati nelle impostazioni **Calcolo e rete** della macchina virtuale. Se nella rete virtuale di Azure specificata per il failover di test non è disponibile una subnet con lo stesso nome, la macchina virtuale di test verrà creata nella prima subnet in ordine alfabetico. Se l'indirizzo IP di destinazione fa parte della subnet scelta, Site Recovery tenta di creare la macchina virtuale per il failover di test usando l'indirizzo IP di destinazione. Se l'indirizzo IP di destinazione non fa parte della subnet scelta, la macchina virtuale del failover di test viene creata usando qualsiasi indirizzo IP disponibile nella subnet scelta. 
>
>


1. Se si esegue la replica in un altro sito locale e si usa DHCP, seguire le istruzioni per [configurare DNS e DHCP per il failover di test](site-recovery-test-failover-vmm-to-vmm.md#prepare-dhcp)
1. Eseguire un failover di test della macchina virtuale controller di dominio nella rete isolata. Per effettuare il failover di test, usare il più recente punto di ripristino **coerente con l'applicazione** disponibile della macchina virtuale controller di dominio. 
1. Eseguire un failover di test per il piano di ripristino che contiene le macchine virtuali dell'applicazione. 
1. Al termine del test, eseguire la **pulizia del failover di test** nella macchina virtuale controller di dominio. Questo passaggio consente di eliminare il controller di dominio creato per il failover di test.


### <a name="removing-reference-to-other-domain-controllers"></a>Rimozione del riferimento ad altri controller di dominio
Quando si esegue un failover di test, nella rete di test non si includono tutti i controller di dominio. Per rimuovere il riferimento ad altri controller di dominio presenti nell'ambiente di produzione, è necessario [riassegnare i ruoli FSMO](http://aka.ms/ad_seize_fsmo) ed [eseguire la pulizia](https://technet.microsoft.com/library/cc816907.aspx) dei metadati per i controller di dominio mancanti. 



> [!IMPORTANT]
> Alcune configurazioni descritte nella sezione seguente non sono le configurazioni di controller di dominio standard/predefinite. Se non si vuole apportare queste modifiche a un controller di dominio in produzione, è possibile creare un controller di dominio dedicato da usare per il failover del test di Site Recovery e apportarvi queste modifiche.  
>
>

### <a name="issues-because-of-virtualization-safeguards"></a>Problemi dovuti alle misure di sicurezza della virtualizzazione 

A partire da Windows Server 2012, [sono state integrate misure di sicurezza aggiuntive in Active Directory Domain Services](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Queste misure di sicurezza consentono di proteggere i controller di dominio virtualizzati dai ripristino dello stato precedente USN, purché la piattaforma hypervisor sottostante supporti VM-GenerationID. Azure supporta VM-GenerationID e ciò significa che nei controller di dominio che eseguono Windows Server 2012 o versione successiva in macchine virtuali di Azure sono disponibili misure di sicurezza aggiuntive. 


Quando VM-GenerationID viene reimpostato, anche l'attributo invocationID del database di Servizi di dominio Active Directory viene reimpostato, il pool di RID viene eliminato e SYSVOL è contrassegnato come non autorevole. Per altre informazioni, vedere [Introduzione alla virtualizzazione di Servizi di dominio Active Directory](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) e il blog relativo alla [virtualizzazione di DFSR in modo sicuro](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/)

Il failover in Azure può causare la reimpostazione dell'ID di generazione di VM e ciò interviene nelle misure di sicurezza aggiuntive quando la macchina virtuale controller di dominio viene avviata in Azure. Ciò può comportare un **ritardo significativo** nella possibilità dell'utente di accedere alla macchina virtuale controller di dominio. Poiché questo controller di dominio viene usato solo in un failover di test, non sono necessari misure di sicurezza della virtualizzazione. Per assicurarsi che non cambi l'ID di generazione VM per la macchina virtuale controller di dominio, è possibile modificare il valore DWORD seguente a 4 nel controller di dominio locale.

        
        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start
 

#### <a name="symptoms-of-virtualization-safeguards"></a>Sintomi delle misure di sicurezza della virtualizzazione
 
Se le misure di sicurezza della virtualizzazione sono intervenute dopo un failover di test, è possibile che si verifichi uno o più dei seguenti sintomi:  

Modifica dell'ID di generazione

![Modifica dell'ID di generazione](./media/site-recovery-active-directory/Event2170.png)

Modifica dell'ID di chiamata

![Modifica dell'ID di chiamata](./media/site-recovery-active-directory/Event1109.png)

Condivisioni SYSVOL e Netlogon non disponibili

![Condivisione SYSVOL](./media/site-recovery-active-directory/sysvolshare.png)

![NTFRS Sysvol](./media/site-recovery-active-directory/Event13565.png)

Vengono eliminati tutti i database DFSR

![Eliminazione del database DFSR](./media/site-recovery-active-directory/Event2208.png)


> [!IMPORTANT]
> Alcune configurazioni descritte nella sezione seguente non sono le configurazioni di controller di dominio standard/predefinite. Se non si vuole apportare queste modifiche a un controller di dominio in produzione, è possibile creare un controller di dominio dedicato da usare per il failover del test di Site Recovery e apportarvi queste modifiche.  
>
>


### <a name="troubleshooting-domain-controller-issues-during-test-failover"></a>Risolvere i problemi del controller di dominio durante il failover di test


Al prompt dei comandi eseguire questo comando per verificare se le cartelle SYSVOL e NETLOGON sono condivise:

    NET SHARE

Al prompt dei comandi eseguire questo comando per verificare che il controller di dominio funzioni correttamente.

    dcdiag /v > dcdiag.txt

Nel log di output cercare il testo seguente per verificare il corretto funzionamento del controller di dominio. 

* "passed test Connectivity"
* "passed test Advertising"
* "passed test MachineAccount"

Se le condizioni precedenti vengono soddisfatte, è probabile che il controller di dominio stia funzionando correttamente. In caso contrario, attenersi alla procedura seguente.


* Eseguire un ripristino autorevole del controller di dominio.
    * Nonostante [non sia consigliabile usare il servizio Replica file](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), se viene comunque usato, seguire la procedura riportata [qui](https://support.microsoft.com/kb/290762) per eseguire un ripristino autorevole. Per altre informazioni sui valori BURFLAG citati nel collegamento precedente, vedere [qui](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Se si usa la replica DFSR, per eseguire un ripristino autorevole seguire la procedura disponibile [qui](https://support.microsoft.com/kb/2218556). A tale scopo è anche possibile usare le funzioni di Powershell disponibili in questo [collegamento](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/). 
    
* Ignorare il requisito della sincronizzazione iniziale impostando la chiave del Registro di sistema seguente su 0 nel controller di dominio locale. Se questo valore DWORD non esiste, può essere creato nel nodo "Parameters". Per altre informazioni, vedere [qui](https://support.microsoft.com/kb/2001093).

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations

* Disabilitare il requisito della disponibilità di un server di catalogo globale per la convalida dell'accesso utente impostando la chiave del Registro di sistema seguente su 1 nel controller di dominio locale. Se questo valore DWORD non esiste, può essere creato nel nodo "Lsa". Per altre informazioni, vedere [qui](http://support.microsoft.com/kb/241789).

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


