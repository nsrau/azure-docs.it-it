---
title: Architettura della replica da Azure ad Azure in Azure Site Recovery | Microsoft Docs
description: Questo articolo offre una panoramica dell'architettura e dei componenti usati per configurare il ripristino di emergenza di macchine virtuali di Azure tra diverse aree di Azure con il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 25cf3914274e73e0789aa87e9288649d1b0cb1eb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399583"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Architettura del ripristino di emergenza da Azure ad Azure


Questo articolo descrive l'architettura, i componenti e i processi usati per la distribuzione di una soluzione di ripristino di emergenza per le macchine virtuali di Azure tramite il servizio [Azure Site Recovery](site-recovery-overview.md). Dopo la configurazione del ripristino di emergenza, le macchine virtuali di Azure vengono replicate continuamente in un'area di destinazione diversa. In caso di guasto, è possibile effettuare il failover delle macchine virtuali nell'area secondaria e accedervi da lì. Una volta ripristinato il normale funzionamento, si può eseguire il failback e continuare a lavorare nella posizione primaria.



## <a name="architectural-components"></a>Componenti dell'architettura

La tabella seguente riepiloga i componenti coinvolti nel ripristino di emergenza delle macchine virtuali di Azure.

**Componente** | **Requisiti**
--- | ---
**Macchine virtuali nell'area di origine** | Una o più macchine virtuali di Azure in un'[area di origine supportata](azure-to-azure-support-matrix.md#region-support).<br/><br/> Le macchine virtuali possono eseguire qualsiasi [sistema operativo supportato](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).
**Archiviazione macchine virtuali di origine** | Le macchine virtuali di Azure possono essere gestite o avere dischi non gestiti distribuiti fra gli account di archiviazione.<br/><br/>[Informazioni](azure-to-azure-support-matrix.md#replicated-machines---storage) sull'archiviazione di Azure supportata.
**Reti macchine virtuali di origine** | Le macchine virtuali possono essere collocate in una o più subnet di una rete virtuale nell'area di origine. [Altre informazioni](azure-to-azure-support-matrix.md#replicated-machines---networking) sui requisiti di rete.
**Account di archiviazione della cache** | È necessario un account di archiviazione della cache nella rete di origine. Durante la replica, le modifiche alle macchine virtuali vengono memorizzate nella cache prima di essere inviate all'archivio di destinazione.<br/><br/> L'uso di una cache assicura un impatto minimo sulle applicazioni di produzione in esecuzione in una macchina virtuale.<br/><br/> [Altre informazioni](azure-to-azure-support-matrix.md#cache-storage) sui requisiti di archiviazione nella cache. 
**Risorse di destinazione** | Le risorse di destinazione vengono usate durante la replica e in caso di failover. Site Recovery può configurare le risorse di destinazione per impostazione predefinita oppure è possibile crearle o personalizzarle.<br/><br/> Nell'area di destinazione verificare che sia possibile creare macchine virtuali e che la sottoscrizione disponga di risorse sufficienti per supportare le dimensioni di macchina virtuale che saranno necessarie nell'area di destinazione. 

![Replica delle risorse di origine e di destinazione](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>Risorse di destinazione

Quando si abilita la replica per una macchina virtuale, Site Recovery offre la possibilità di creare le risorse di destinazione automaticamente. 

**Risorsa di destinazione** | **Impostazione predefinita**
--- | ---
**Sottoscrizione di destinazione** | Equivalente alla sottoscrizione di origine.
**Gruppo di risorse di destinazione** | Gruppo di risorse a cui appartengono le macchine virtuali dopo il failover.<br/><br/> Può essere una qualsiasi area di Azure ad eccezione dell'area di origine.<br/><br/> Site Recovery crea un nuovo gruppo di risorse con suffisso "asr" nell'area di destinazione.<br/><br/>
**Rete virtuale di destinazione** | Rete virtuale in cui si trovano le macchine virtuali replicate dopo il failover. Un mapping di rete viene creato tra le reti virtuali di origine e di destinazione e viceversa.<br/><br/> Site Recovery crea una nuova rete virtuale e una subnet con il suffisso "asr".
**Account di archiviazione di destinazione** |  Se la macchina virtuale non usa un disco gestito, si tratta dell'account di archiviazione in cui vengono replicati i dati.<br/><br/> Site Recovery crea un nuovo account di archiviazione nell'area di destinazione per eseguire il mirroring dell'account di archiviazione di origine.
**Dischi gestiti di replica** | Se la macchina virtuale usa un disco gestito, si tratta dei dischi gestiti in cui vengono replicati i dati.<br/><br/> Site Recovery crea dischi gestiti di replica nell'area di archiviazione per eseguire il mirroring dell'origine.
**Set di disponibilità di destinazione** |  Set di disponibilità in cui si trovano le macchine virtuali replicate dopo il failover.<br/><br/> Site Recovery crea un set di disponibilità con suffisso "asr" nell'area di destinazione per le macchine virtuali che si trovano in un set di disponibilità nella posizione di origine. Se esiste un set di disponibilità, viene usato quello e non ne viene creato uno nuovo.
**Zone di disponibilità di destinazione** | Se l'area di destinazione supporta le zone di disponibilità, Site Recovery assegna lo stesso numero di zona usato nell'area di origine.

### <a name="managing-target-resources"></a>Gestione delle risorse di destinazione

È possibile gestire le risorse di destinazione nei modi seguenti:

- È possibile modificare le impostazioni di destinazione quando si abilita la replica.
- È possibile modificare le impostazioni di destinazione quando la replica è già in funzione. L'eccezione è il tipo di disponibilità (singola istanza, set o zona). Per cambiare questa impostazione è necessario disabilitare la replica, modificare l'impostazione e quindi riabilitare la replica.



## <a name="replication-policy"></a>Criteri di replica 

Quando si abilita la replica delle macchine virtuali di Azure, per impostazione predefinita Site Recovery crea nuovi criteri di replica con le impostazioni predefinite riepilogate nella tabella.

**Impostazione criteri** | **Dettagli** | **Default**
--- | --- | ---
**Conservazione del punto di ripristino** | Specifica per quanto tempo Site Recovery conserva i punti di ripristino | 24 ore
**Frequenza snapshot coerenti con l'applicazione** | Specifica con quale frequenza Site Recovery accetta uno snapshot coerente con l'app. | Ogni 60 minuti.

### <a name="managing-replication-policies"></a>Gestione dei criteri di replica

È possibile gestire e modificare le impostazioni predefinite dei criteri di replica nei modi seguenti:
- È possibile modificare le impostazioni quando si abilita la replica.
- È possibile creare criteri di replica in qualsiasi momento e quindi applicarli quando si abilita la replica.

### <a name="multi-vm-consistency"></a>Coerenza tra più macchine virtuali

Se si vuole che le macchine virtuali vengano replicate insieme e abbiano punti di ripristino condivisi coerenti con l'arresto anomalo del sistema e coerenti con l'app in caso di failover, è possibile raccoglierle in un gruppo di replica. La coerenza tra più macchine virtuali influisce sulle prestazioni dei carichi di lavoro e deve essere usata solo per le macchine virtuali che eseguono carichi di lavoro per cui la coerenza fra tutte le macchine è fondamentale. 



## <a name="snapshots-and-recovery-points"></a>Snapshot e punti di ripristino

I punti di ripristino vengono creati da snapshot dei dischi delle macchine virtuali acquisiti in un momento specifico. Quando si effettua il failover di una macchina virtuale, si usa un punto di ripristino per ripristinare la VM nella posizione di destinazione.

Prima di effettuare il failover è importante assicurarsi che la macchina virtuale venga avviata senza danneggiamenti o perdita di dati e che i dati della VM siano coerenti per il sistema operativo e per le app in esecuzione su di essa. Tutto questo dipende dal tipo di snapshot acquisiti.

Site Recovery acquisisce snapshot nel modo seguente:

1. Site Recovery acquisisce snapshot dei dati coerenti con l'arresto anomalo del sistema per impostazione predefinita, oltre a snapshot coerenti con l'app se si specifica una frequenza.
2. Dagli snapshot vengono creati punti di ripristino che vengono archiviati in base alle impostazioni di conservazione dei criteri di replica.

### <a name="consistency"></a>Consistency

La tabella seguente illustra i vari tipi di coerenza.

### <a name="crash-consistent"></a>Coerenza con l'arresto anomalo del sistema

**Descrizione** | **Dettagli** | **Consiglio**
--- | --- | ---
Uno snapshot coerente con l'arresto anomalo del sistema acquisisce i dati contenuti nel disco al momento dell'acquisizione dello snapshot. Non include alcun dato in memoria.<br/><br/> Contiene l'equivalente dei dati su disco che sarebbero presenti se si verificasse un arresto anomalo della macchina virtuale o se il cavo di alimentazione del server venisse scollegato nell'istante esatto dell'acquisizione dello snapshot.<br/><br/> Uno snapshot coerente con l'arresto anomalo del sistema non garantisce la coerenza dei dati per il sistema operativo o per le app in esecuzione nella macchina virtuale. | Per impostazione predefinita, Site Recovery crea punti di ripristino coerenti con l'arresto anomalo del sistema ogni cinque minuti. Questa impostazione non può essere modificata.<br/><br/>  | Attualmente, la maggior parte delle app può essere ripristinata correttamente da punti coerenti con l'arresto anomalo del sistema.<br/><br/> I punti di ripristino coerenti con l'arresto anomalo del sistema sono in genere sufficienti per la replica di sistemi operativi e app come i server DHCP e i server di stampa.

### <a name="app-consistent"></a>Coerenza con l'app

**Descrizione** | **Dettagli** | **Consiglio**
--- | --- | ---
I punti di ripristino coerenti con l'app vengono creati dagli snapshot coerenti con l'app.<br/><br/> Uno snapshot coerente con l'app contiene tutte le informazioni contenute in uno snapshot coerente con l'arresto anomalo del sistema, oltre a tutti i dati in memoria e le transazioni in corso. | Gli snapshot coerenti con l'app usano il servizio Copia Shadow del volume:<br/><br/>   1) All'avvio di uno snapshot, il servizio esegue un'operazione di copia su scrittura sul volume.<br/><br/>   2) Prima di eseguire l'operazione di copia su scrittura, il servizio informa ogni app presente nella macchina virtuale che dovrà scaricare i dati residenti in memoria su disco.<br/><br/>   3) Il servizio Copia Shadow del volume consente quindi all'app di backup/ripristino di emergenza (in questo caso Site Recovery) di leggere i dati dello snapshot e di procedere. | Gli snapshot vengono acquisiti in base alla frequenza specificata. Questa frequenza deve essere sempre inferiore a quella impostata per la conservazione dei punti di ripristino. Ad esempio, se i punti di ripristino vengono conservati in base all'impostazione predefinita di 24 ore, la frequenza deve essere impostata su un periodo inferiore a 24 ore.<br/><br/>Gli snapshot coerenti con l'app sono più complessi e il loro completamento richiede più tempo rispetto agli snapshot coerenti con l'arresto anomalo del sistema.<br/><br/> Influiscono sulle prestazioni delle app in esecuzione su una macchina virtuale abilitata per la replica. 

## <a name="replication-process"></a>Processo di replica

Quando si abilita la replica per una macchina virtuale di Azure, accade quanto segue:

1. L'estensione del servizio Mobility di Site Recovery viene installata automaticamente nella macchina virtuale.
2. L'estensione registra la macchina virtuale con Site Recovery.
3. Inizia quindi la replica continua per la macchina virtuale.  Le scritture su disco vengono trasferite immediatamente nell'account di archiviazione della cache nella posizione di origine.
4. Site Recovery elabora i dati nella cache e li invia all'account di archiviazione di destinazione o ai dischi gestiti di replica.
5. Al termine dell'elaborazione dei dati, i punti di ripristino coerenti con l'arresto anomalo del sistema vengono generati ogni pochi minuti. I punti di ripristino coerenti con l'app vengono generati in base all'impostazione specificata nei criteri di replica.

![Abilitare il processo di replica - Passaggio 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**Processo di replica**

## <a name="connectivity-requirements"></a>Requisiti di connettività

 Le macchine virtuali di Azure di cui si esegue la replica hanno bisogno di connettività in uscita. Site Recovery non necessita mai della connettività in ingresso per la VM. 

### <a name="outbound-connectivity-urls"></a>Connettività in uscita (URL)

Se l'accesso in uscita per le macchine virtuali è controllato tramite URL, consentire gli URL seguenti.

| **URL** | **Dettagli** |
| ------- | ----------- |
| *.blob.core.windows.net | Consente la scrittura di dati dalla macchina virtuale nell'account di archiviazione della cache all'area di origine. |
| login.microsoftonline.com | Fornisce l'autenticazione e l'autorizzazione per gli URL del servizio Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Consente alla macchina virtuale di comunicare con il servizio Site Recovery. |
| *.servicebus.windows.net | Consente alla macchina virtuale di scrivere i dati di diagnostica e monitoraggio di Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Connettività in uscita per gli intervalli di indirizzi IP

Per controllare la connettività in uscita per le macchine virtuali tramite indirizzi IP, consentire questi indirizzi.

#### <a name="source-region-rules"></a>Regole dell'area di origine

**Regola** |  **Dettagli** | **Tag di servizio**
--- | --- | --- 
Consenti HTTPS in uscita: porta 443 | Consente gli intervalli che corrispondono agli account di archiviazione nell'area di origine. | Spazio di archiviazione. \<area-name >.
Consenti HTTPS in uscita: porta 443 | Consente gli intervalli che corrispondono ad Azure Active Directory (Azure AD).<br/><br/> Se in futuro vengono aggiunti indirizzi di Azure AD, è necessario creare nuove regole di gruppo di sicurezza di rete (NSG).  | AzureActiveDirectory
Consenti HTTPS in uscita: porta 443 | Consente l'accesso agli [endpoint di Site Recovery](https://aka.ms/site-recovery-public-ips) che corrispondono alla posizione di destinazione. 

#### <a name="target-region-rules"></a>Regole dell'area di destinazione

**Regola** |  **Dettagli** | **Tag di servizio**
--- | --- | --- 
Consenti HTTPS in uscita: porta 443 | Consente gli intervalli che corrispondono agli account di archiviazione nell'area di destinazione. | Spazio di archiviazione. \<area-name >.
Consenti HTTPS in uscita: porta 443 | Consente gli intervalli che corrispondono ad Azure AD.<br/><br/> Se in futuro vengono aggiunti indirizzi di Azure AD, è necessario creare nuove regole NSG.  | AzureActiveDirectory
Consenti HTTPS in uscita: porta 443 | Consente l'accesso agli [endpoint di Site Recovery](https://aka.ms/site-recovery-public-ips) che corrispondono alla posizione di origine. 


#### <a name="control-access-with-nsg-rules"></a>Controllare l'accesso con le regole NSG

Se si controlla la connettività delle macchine virtuali filtrando il traffico da e verso reti/subnet di Azure mediante [regole NSG](https://docs.microsoft.com/azure/virtual-network/security-overview), tenere presenti i requisiti seguenti:

- Le regole NSG per l'area di Azure di origine devono consentire l'accesso in uscita per il traffico di replica.
- È consigliabile creare le regole in un ambiente di test prima di usarle in un ambiente di produzione.
- Usare [tag di servizio](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) invece di consentire singoli indirizzi IP.
    - I tag di servizio rappresentano un gruppo di prefissi di indirizzi IP raggruppati per ridurre al minimo la complessità della creazione delle regole di sicurezza.
    - Microsoft aggiorna automaticamente i tag di servizio nel corso del tempo. 
 
Leggere altre informazioni sulla [connettività in uscita](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) per Site Recovery e sul [controllo della connettività con le regole NSG](concepts-network-security-group-with-site-recovery.md).


### <a name="connectivity-for-multi-vm-consistency"></a>Connettività per la coerenza tra più macchine virtuali

Se si abilita la coerenza tra più macchine virtuali, i computer inclusi nel gruppo di replica comunicano tra loro sulla porta 20004.
- Verificare che nessuna appliance firewall blocchi la comunicazione interna tra VM sulla porta 20004.
- Se le VM Linux devono far parte di un gruppo di replica, verificare che il traffico in uscita sulla porta 20004 venga aperto manualmente in base alle indicazioni della versione Linux specifica.




## <a name="failover-process"></a>Processo di failover

Quando si avvia un failover, le macchine virtuali vengono create nel gruppo di risorse di destinazione, nella rete virtuale di destinazione, nella subnet di destinazione e nel set di disponibilità di destinazione. Durante un failover è possibile usare qualsiasi punto di ripristino.

![Processo di failover](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Passaggi successivi

[Replicare rapidamente](azure-to-azure-quickstart.md) una macchina virtuale di Azure in un'area secondaria.
