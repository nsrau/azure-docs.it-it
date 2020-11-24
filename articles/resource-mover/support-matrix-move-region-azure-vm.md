---
title: Matrice di supporto per lo spostamento di macchine virtuali di Azure in un'altra area con Azure Resource Mover
description: Esaminare il supporto per lo spostamento di VM di Azure tra aree con Azure Resource Mover.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: how-to
ms.date: 10/11/2020
ms.author: raynew
ms.openlocfilehash: 4da707ab698599c8ea5dd8e1ea8647f543eb2a68
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95524250"
---
# <a name="support-for-moving-azure-vms-between-azure-regions"></a>Supporto per lo trasferimento di VM di Azure tra aree di Azure

Questo articolo riepiloga il supporto e i prerequisiti quando si spostano le macchine virtuali e le risorse di rete correlate tra le aree di Azure usando il motore di risorse.

> [!IMPORTANT]
> Spostamento risorse di Azure è attualmente disponibile in anteprima.


## <a name="windows-vm-support"></a>Supporto per VM Windows

Resource Mover supporta le macchine virtuali di Azure che eseguono questi sistemi operativi Windows.

**Sistema operativo** | **Dettagli**
--- | ---
Windows Server 2019 | Supportato per Server Core, Server con Esperienza Desktop.
Windows Server 2016  | Supportato per Server Core, Server con Esperienza Desktop.
Windows Server 2012 R2 | Supportato.
Windows Server 2012 | Supportato.
Windows Server 2008 R2 con SP1/SP2 | Supportato.<br/><br/> Per i computer che eseguono Windows Server 2008 R2 con SP1/SP2, è necessario installare un aggiornamento di Windows [servicing stack (SSU)](https://support.microsoft.com/help/4490628) e un [aggiornamento SHA-2](https://support.microsoft.com/help/4474419).  SHA-1 non è supportato da settembre 2019 e se la firma del codice SHA-2 non è abilitata, l'estensione dell'agente non verrà installata o aggiornata come previsto. Altre informazioni sull'[aggiornamento e sui requisiti di SHA-2](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Supportato.
Windows 8.1 (x64) | Supportato.
Windows 8 (x64) | Supportato.
Windows 7 (x64) con SP1 e versioni successive | Installare un aggiornamento di Windows [servicing stack (SSU)](https://support.microsoft.com/help/4490628) e un [aggiornamento SHA-2](https://support.microsoft.com/help/4474419) nei computer che eseguono Windows 7 con SP1.  SHA-1 non è supportato dal 2019 settembre e se la firma del codice SHA-2 non è abilitata, il passaggio ' preparazione ' non avrà esito positivo. Altre informazioni sull'[aggiornamento e sui requisiti di SHA-2](https://aka.ms/SHA-2KB).


## <a name="linux-vm-support"></a>Supporto per VM Linux

Lo spostamento delle risorse supporta le macchine virtuali di Azure che eseguono questi sistemi operativi Linux.

**Sistema operativo** | **Dettagli**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 8.0, 8.1
Server Ubuntu 14.04 LTS | [Versioni del kernel supportate](#supported-ubuntu-kernel-versions)
Server Ubuntu 16.04 LTS | [Versioni del kernel supportate](#supported-ubuntu-kernel-versions)<br/><br/> I server Ubuntu che usano l'autenticazione basata su password e l'accesso e il pacchetto cloud-init per configurare le macchine virtuali cloud possono avere un accesso basato su password disabilitato al failover, a seconda della configurazione di cloud-init. L'accesso basato su password può essere riabilitato nella macchina virtuale reimpostando la password dal menu supporto > risoluzione dei problemi > impostazioni (della macchina virtuale di cui è stato eseguito il failover nella portale di Azure.
Server Ubuntu 18.04 LTS | [Versione kernel supportata](#supported-ubuntu-kernel-versions).
Debian 7 | [Versioni del kernel supportate](#supported-debian-kernel-versions).
Debian 8 | [Versioni del kernel supportate](#supported-debian-kernel-versions).
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [Versioni del kernel supportate](#supported-suse-linux-enterprise-server-12-kernel-versions)
SUSE Linux Enterprise Server 15 | 15 e 15 SP1. [(Versioni del kernel supportate)](#supported-suse-linux-enterprise-server-15-kernel-versions)
SUSE Linux Enterprise Server 11 | SP3
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Con kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3, 4 e 5 (UEK3, UEK4, UEK5)


### <a name="supported-ubuntu-kernel-versions"></a>Versioni del kernel Ubuntu supportate

**Versione** | **Versione del kernel** 
--- | --- 
14.04 LTS |  Da 3.13.0-24 generica a 3.13.0-170 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-148 generica<br/>Da 4.15.0-1023 Azure a 4.15.0-1045 Azure 
16.04 LTS |  Da 4.4.0-21 generica a 4.4.0-171 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>Da 4.15.0-13 generica a 4.15.0-74 generica<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>Da 4.15.0-1012 Azure a 4.15.0-1066 Azure
18.04 LTS | Da 4.15.0-20 generica a 4.15.0-74 generica </br> Da 4.18.0-13 generica a 4.18.0-25 generica </br> Da 5.0.0-15 generica a 5.0.0-37 generica </br> Da 5.3.0-19 generica a 5.3.0-24 generica </br> Da 4.15.0-1009 Azure a 4.15.0-1037 Azure </br> Da 4.18.0-1006 Azure a 4.18.0-1025 Azure </br> Da 5.0.0-1012 Azure a 5.0.0-1028 Azure </br> Da 5.3.0-1007 Azure a 5.3.0-1009 Azure


### <a name="supported-debian-kernel-versions"></a>Versioni del kernel Debian supportate 

**Versione** |  **Versione del kernel** 
--- |  --- 
Debian 7 |  Da 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 
Debian 8 |  Da 3.16.0-4-amd64 a 3.16.0-10-amd64, da 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.11-amd64 
Debian 8 |  Da 3.16.0-4-amd64 a 3.16.0-10-amd64, da 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.9-amd64

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions"></a>Versioni del kernel supportate SUSE Linux Enterprise Server 12 

**Versione** | **Versione del kernel** 
--- |  --- 
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) |  Sono supportati tutti i [kernel di borsa SUSE 12 SP1, SP2, SP3 e SP4](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> Da 4.4.138-4.7 Azure a 4.4.180-4.31 Azure</br>Da 4.12.14-6.3 Azure a 4.12.14-6.34 Azure  


### <a name="supported-suse-linux-enterprise-server-15-kernel-versions"></a>Versioni del kernel supportate SUSE Linux Enterprise Server 15

**Versione** | **Versione del kernel** |
--- |  --- |
SUSE Linux Enterprise Server 15 e 15 SP1 |  Sono supportati tutti i kernel SUSE 15 e 15 disponibili.</br></br> Da 4.12.14-5.5 Azure a 4.12.14-8.22 Azure |

## <a name="supported-linux-file-systemguest-storage"></a>Archiviazione file system/Guest Linux supportata

* File system: ext3, ext4, XFS, BTRFS
* Gestore volumi: LVM2
* Software con percorsi multipli: Mapper dispositivi


## <a name="supported-vm-compute-settings"></a>Impostazioni di calcolo VM supportate

**Impostazione** | **Supporto** | **Dettagli**
--- | --- | ---
Dimensione | Qualsiasi dimensione di VM di Azure con almeno due core CPU e 1 GB di RAM | Verificare le [dimensioni delle macchine virtuali in Azure](../virtual-machines/sizes-general.md).
Set di disponibilità | Supportato | Supportata.
Zone di disponibilità | Supportato | Supportato, a seconda del supporto dell'area di destinazione.
Immagini della raccolta di Azure (pubblicate da Microsoft) | Supportato | Supportate se la macchina virtuale viene eseguita in un sistema operativo supportato.
Immagini della raccolta di Azure (pubblicate da terze parti)  | Supportato | Supportate se la macchina virtuale viene eseguita in un sistema operativo supportato.
Immagini personalizzate (pubblicate da terze parti)| Supportato | Supportate se la macchina virtuale viene eseguita in un sistema operativo supportato.
Macchine virtuali con Site Recovery | Non supportate | Spostare le risorse tra aree per le macchine virtuali, usando Site Recovery nel back-end. Se si sta già usando Site Recovery, disabilitare la replica e quindi avviare il processo di preparazione.
Criteri RBAC di Azure | Non supportate | I criteri di controllo degli accessi in base al ruolo di Azure (RBAC) sulle VM non vengono copiati nella macchina virtuale nell'area di destinazione.
Estensioni | Non supportate | Le estensioni non vengono copiate nella macchina virtuale nell'area di destinazione. Installarli manualmente al termine dello spostamento.


## <a name="supported-vm-storage-settings"></a>Impostazioni di archiviazione delle macchine virtuali supportate

Questa tabella riepiloga il supporto per il disco del sistema operativo, il disco dati e il disco temporaneo della macchina virtuale di Azure. È importante osservare i limiti dei dischi e le destinazioni per le macchine virtuali [Linux](../virtual-machines/linux/disk-scalability-targets.md) e [Windows](../virtual-machines/windows/disk-scalability-targets.md) per evitare eventuali problemi di prestazioni.

> [!NOTE]
> Le dimensioni della macchina virtuale di destinazione devono essere maggiori o uguali a quelle della VM di origine. I parametri usati per la convalida sono: numero di dischi dati, numero di schede di rete, CPU disponibili, memoria in GB. In caso contrario, viene generato un errore.


**Componente** | **Supporto** | **Dettagli**
--- | --- | ---
Dimensione massima del disco del sistema operativo | 2048 GB | [Altre informazioni](../virtual-machines/managed-disks-overview.md) sui dischi delle VM.
Disco temporaneo | Non supportate | Il disco temporaneo è sempre escluso dal processo di preparazione.<br/><br/> Non conservare dati persistenti sul disco temporaneo. [Altre informazioni](../virtual-machines/managed-disks-overview.md#temporary-disk)
Dimensione massima del disco dati | 8192 GB per i dischi gestiti
Dimensione minima del disco dati |  2 GB per i dischi gestiti |
Numero massimo di dischi dati | Fino a 64, in conformità con il supporto per una specifica dimensione di VM di Azure | [Altre informazioni](../virtual-machines/sizes.md) sulle dimensioni delle VM.
Frequenza di modifica del disco dati | Massimo 10 Mbps per disco per l'archiviazione Premium. Massimo 2 Mbps per disco per l'archiviazione Standard. | Se la frequenza di modifica dei dati media sul disco è costantemente superiore al limite massimo, la preparazione non verrà aggiornata.<br/><br/>  Tuttavia, se il valore massimo viene superato sporadicamente, la preparazione può essere aggiornata, ma è possibile che vengano visualizzati punti di ripristino leggermente ritardati.
Disco dati (account di archiviazione standard) | Non supportata. | Modificare il tipo di archiviazione in disco gestito, quindi provare a spostarsi sulla macchina virtuale.
Disco dati (account di archiviazione Premium) | Non supportate | Modificare il tipo di archiviazione in disco gestito, quindi provare a spostarsi sulla macchina virtuale.
Disco gestito (standard) | Supportato  |
Disco gestito (Premium) | Supportato |
SSD Standard | Supportato |
Generazione 2 (avvio UEFI) | Supportato
Account di archiviazione di diagnostica di avvio | Non supportate | Riabilitarla dopo aver spostato la macchina virtuale nell'area di destinazione.

### <a name="limits-and-data-change-rates"></a>Limiti e frequenza di modifica dei dati

La tabella seguente riepiloga i limiti che si basano sui test. Questi non coprono tutte le possibili combinazioni di I/O delle applicazioni. I risultati effettivi variano in base alla combinazione di I/O dell'applicazione. È necessario considerare due limiti, la varianza dei dati per disco e la varianza dei dati per macchina virtuale.

**Destinazione di archiviazione** | **I/O medio del disco di origine** |**Varianza dati media disco di origine** | **Varianza dati totale giornaliera disco di origine**
---|---|---|---
Archiviazione standard | 8 KB    | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 8 KB    | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 16 KB | 4 MB/s |    336 GB per disco
Disco P10 o P15 Premium | 32 KB o superiori | 8 MB/s | 672 GB per disco
Disco P20, P30, P40 o P50 Premium | 8 KB    | 5 MB/s | 421 GB per disco
Disco P20, P30, P40 o P50 Premium | 16 KB o superiori |20 MB/s | 1684 GB per disco

## <a name="supported-vm-networking-settings"></a>Impostazioni di rete VM supportate

**Impostazione** | **Supporto** | **Dettagli**
--- | --- | ---
NIC | Supportato | Specificare una risorsa esistente nell'area di destinazione oppure creare una nuova risorsa durante il processo di preparazione. 
Servizio di bilanciamento del carico interno | Supportato | Specificare una risorsa esistente nell'area di destinazione oppure creare una nuova risorsa durante il processo di preparazione.  
Bilanciamento del carico pubblico | Attualmente non supportato | Specificare una risorsa esistente nell'area di destinazione oppure creare una nuova risorsa durante il processo di preparazione.  
Indirizzo IP pubblico | Supportato | Specificare una risorsa esistente nell'area di destinazione oppure creare una nuova risorsa durante il processo di preparazione.<br/><br/> L'indirizzo IP pubblico è specifico dell'area e non verrà mantenuto nell'area di destinazione dopo lo spostamento. Tenere presente questo aspetto quando si modificano le impostazioni di rete (incluse le regole di bilanciamento del carico) nel percorso di destinazione.
Gruppo di sicurezza di rete | Supportato | Specificare una risorsa esistente nell'area di destinazione oppure creare una nuova risorsa durante il processo di preparazione.  
Indirizzo IP riservato (statico) | Supportato | Attualmente non è possibile configurare questa operazione. Il valore predefinito è il valore di origine. <br/><br/> Se la scheda di interfaccia di rete nella macchina virtuale di origine ha un indirizzo IP statico e la subnet di destinazione ha lo stesso indirizzo IP disponibile, viene assegnata alla macchina virtuale di destinazione.<br/><br/> Se la subnet di destinazione non ha lo stesso indirizzo IP disponibile, lo spostamento di avvio per la macchina virtuale avrà esito negativo.
Indirizzo IP dinamico | Supportato | Attualmente non è possibile configurare questa operazione. Il valore predefinito è il valore di origine.<br/><br/> Se la scheda di interfaccia di rete nell'origine ha un indirizzo IP dinamico, anche la scheda di interfaccia di rete nella macchina virtuale di destinazione è dinamica per impostazione predefinita.
Configurazioni IP | Supportato | Attualmente non è possibile configurare questa operazione. Il valore predefinito è il valore di origine.

## <a name="outbound-access-requirements"></a>Requisiti di accesso in uscita

Per le macchine virtuali di Azure che si desidera spostare è necessario l'accesso in uscita.


### <a name="url-access"></a>accesso con URL

 Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, consentire l'accesso a questi URL:

**Nome** | **Cloud pubblico di Azure** | **Informazioni dettagliate** 
--- | --- | --- 
Archiviazione | `*.blob.core.windows.net`  | Consente la scrittura di dati dalla macchina virtuale nell'account di archiviazione della cache all'area di origine. 
Azure Active Directory | `login.microsoftonline.com`  | Fornisce l'autenticazione e l'autorizzazione per gli URL del servizio Site Recovery. 
Replica | `*.hypervrecoverymanager.windowsazure.com` | Consente alla macchina virtuale di comunicare con il servizio Site Recovery. 
Bus di servizio | `*.servicebus.windows.net` | Consente alla macchina virtuale di scrivere i dati di diagnostica e monitoraggio di Site Recovery. 

## <a name="nsg-rules"></a>Regole NSG
Se si usano regole del gruppo di sicurezza di rete (NSG) per controllare la connettività in uscita, creare queste regole dei [tag del servizio](../virtual-network/service-tags-overview.md) . Ogni regola deve consentire l'accesso in uscita su HTTPS (443).
- Creare una regola di tag di archiviazione per l'area di origine.
- Creare una regola di tag *AzureSiteRecovery* per consentire l'accesso al servizio Site Recovery in qualsiasi area. Questo tag presenta dipendenze da questi altri tag, quindi è necessario creare le regole per le seguenti operazioni:
    - *AzureActiveDirectory*
    - **EventHub*
    - *AzureKeyVault*
    - *GuestAndHybridManagement*
- È consigliabile testare le regole in un ambiente non di produzione. [Esaminare alcuni esempi](../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags). 


## <a name="next-steps"></a>Passaggi successivi

Provare a [trasferire una macchina virtuale di Azure](tutorial-move-region-virtual-machines.md) in un'altra area con il motore di risorse.