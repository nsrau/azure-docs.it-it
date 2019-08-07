---
title: Matrice di supporto per il ripristino di emergenza di VM di Azure tra aree di Azure con Azure Site Recovery | Microsoft Docs
description: Riepiloga i prerequisiti e il supporto per il ripristino di emergenza di macchine virtuali di Azure da un'area a un'altra con Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 08/06/2019
ms.author: raynew
ms.openlocfilehash: d0e24d7621218ccac23842072a5f08be94c80d9d
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840580"
---
# <a name="support-matrix-for-replicating-azure-vms-from-one-region-to-another"></a>Matrice di supporto per la replica di macchine virtuali di Azure da un'area a un'altra

Questo articolo riepiloga il supporto e i prerequisiti quando si imposta il ripristino di emergenza di macchine virtuali di Azure da un'area di Azure a un'altra, usando il servizio [Azure Site Recovery](site-recovery-overview.md) .


## <a name="deployment-method-support"></a>Supporto del metodo di distribuzione

**Distribuzione** |  **Supporto**
--- | ---
**Portale di Azure** | Supportato.
**PowerShell** | Supportato. [Altre informazioni](azure-to-azure-powershell.md)
**API REST** | Supportato.
**CLI** | Attualmente non supportato


## <a name="resource-support"></a>Supporto delle risorse

**Azione risorsa** | **Dettagli**
--- | --- | ---
**Spostare insiemi di credenziali tra gruppi di risorse** | Non supportate
**Spostamento di risorse di calcolo, archiviazione e rete tra gruppi di risorse** | Non supportati.<br/><br/> Se si sposta una macchina virtuale o componenti associati come archiviazione o rete dopo la replica della VM, è necessario disabilitare la replica e riabilitarla per la VM.
**Replica di macchine virtuali di Azure da una sottoscrizione a un'altra per il ripristino di emergenza** | Supportate nello stesso tenant di Azure Active Directory.
**Eseguire la migrazione di macchine virtuali tra aree all'interno dei cluster geografici supportati (all'interno e tra sottoscrizioni)** | Supportate nello stesso tenant di Azure Active Directory.
**Migrazione di macchine virtuali all'interno della stessa area** | Non supportati.

## <a name="region-support"></a>Supporto di area

È possibile eseguire la replica e il ripristino di macchine virtuali tra due aree qualsiasi all'interno dello stesso cluster geografico. I cluster geografici vengono definiti tenendo presente la latenza e la sovranità dei dati.


**Cluster geografico** | **Aree di Azure**
-- | --
America | Canada orientale, Canada centrale, Stati Uniti centro-meridionali, Stati Uniti centro-occidentali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti centrali, Stati Uniti centro-settentrionali
Europa | Regno Unito occidentale, Regno Unito meridionale, Europa settentrionale, Europa occidentale, Francia centrale, Francia meridionale, Sudafrica occidentale, Sudafrica settentrionale
Asia | India meridionale, India centrale, India occidentale, Asia sudorientale, Asia orientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Emirati Arabi Uniti centrali, Emirati Arabi Uniti settentrionali
Australia   | Australia orientale, Australia sud-orientale, Australia centrale, Australia centrale 2
Azure Government    | US GOV Virginia, US GOV Iowa, US GOV Arizona, US GOV Texas, US DOD East, US DOD Central 
Germania | Germania centrale, Germania nord-orientale
Cina | Cina orientale, Cina settentrionale, Cina settentrionale2, Cina orientale 2

>[!NOTE]
>
> - Per il **Brasile meridionale**, è possibile eseguire la replica e il failover in queste aree: Stati Uniti centro-occidentali, Stati Uniti centro-occidentali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali, Stati Uniti occidentali 2 e Stati Uniti centro-settentrionali
> - Il Brasile meridionale può essere usato solo come area di origine da cui è possibile eseguire la replica delle macchine virtuali usando Site Recovery. Non può fungere da area di destinazione. Ciò è dovuto a problemi di latenza dovuti a distanze geografiche.
> - È possibile lavorare all'interno di aree per le quali si dispone dell'accesso appropriato.
> - Se non viene visualizzata l'area in cui si vuole creare un insieme di credenziali, assicurarsi che la sottoscrizione abbia accesso per creare risorse in tale area.
> - Se non è possibile visualizzare un'area all'interno di un cluster geografico quando si Abilita la replica, assicurarsi che la sottoscrizione disponga delle autorizzazioni per la creazione di macchine virtuali in tale area.



## <a name="cache-storage"></a>Archiviazione cache

Questa tabella riepiloga il supporto per l'account di archiviazione della cache usato da Site Recovery durante la replica.

**Impostazione** | **Supporto** | **Dettagli**
--- | --- | ---
Account di archiviazione V2 di utilizzo generico (livelli di accesso frequente e sporadico) | Supportato | L'utilizzo di GPv2 non è consigliato perché i costi delle transazioni per V2 sono sostanzialmente più elevati di V1 account di archiviazione.
Firewall di Archiviazione di Azure per reti virtuali  | Supportato | Se si usano account di archiviazione cache o di archiviazione di destinazione abilitati per il firewall, assicurarsi di selezionare ["Consenti ai servizi Microsoft attendibili"](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).


## <a name="replicated-machine-operating-systems"></a>Sistemi operativi di computer replicati

Site Recovery supporta la replica di macchine virtuali di Azure che eseguono i sistemi operativi elencati in questa sezione.

### <a name="windows"></a>Windows

**Sistema operativo** | **Dettagli**
--- | ---
Windows Server 2019 | Server Core, Server con Esperienza Desktop
Windows Server 2016  | Server Core, Server con Esperienza Desktop
Windows Server 2012 R2 |
Windows Server 2012 |
Windows Server 2008 R2 | Con SP1 o versione successiva
Windows 10 (x64) |
Windows 8.1 (x64) |
Windows 8 (x64) |
Windows 7 (x64) | Esecuzione di SP1 o versione successiva (Windows 7 RTM non è supportato)

#### <a name="linux"></a>Linux

**Sistema operativo** | **Dettagli**
--- | ---
Red Hat Enterprise Linux. | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6  
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6
Server Ubuntu 14.04 LTS | [Versioni del kernel supportate](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Server Ubuntu 16.04 LTS | [Versioni del kernel supportate](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> I server Ubuntu che usano l'autenticazione basata su password e l'accesso e il pacchetto cloud-init per configurare le macchine virtuali cloud possono avere un accesso basato su password disabilitato al failover (a seconda della configurazione di cloudinit). È possibile riabilitare l'accesso basato su password nella macchina virtuale reimpostando la password dal menu Supporto > risoluzione dei problemi > Impostazioni (della macchina virtuale di cui è stato eseguito il failover nella portale di Azure.
Debian 7 | [Versioni del kernel supportate](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Versioni del kernel supportate](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(Versioni del kernel supportate)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> L'aggiornamento dei computer di replica da SP3 a SP4 non è supportato. Se un computer replicato è stato aggiornato, è necessario disabilitare la replica e riabilitarla dopo l'aggiornamento.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6<br/><br/> Esecuzione del kernel compatibile Red Hat o Unbreakable Enterprise kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5) 


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel Ubuntu supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
14.04 LTS | 9,27 | 3.13.0-24-generico per 3.13.0-170-generico,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generico per 4.4.0-148-generico,<br/>4.15.0-1023-Azure a 4.15.0-1045-Azure |
14.04 LTS | 9,26 | 3.13.0-24-generico per 3.13.0-170-generico,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generico per 4.4.0-148-generico,<br/>4.15.0-1023-Azure a 4.15.0-1045-Azure |
14.04 LTS | 9,25 | 3.13.0-24-generico per 3.13.0-169-generico,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generico per 4.4.0-146-generico,<br/>4.15.0-1023-da Azure a 4.15.0-1042-Azure |
14.04 LTS | 9,24 | 3.13.0-24-generico per 3.13.0-167-generico,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generico per 4.4.0-143-generico,<br/>4.15.0-1023-Azure a 4.15.0-1040-Azure |
|||
16.04 LTS | 9,27 | 4.4.0-21-generico a 4.4.0-154-Generic,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-generico a 4.15.0-55-generico<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-Azure in 4.15.0-1051-Azure|
16.04 LTS | 9,26 | 4.4.0-21-generico per 4.4.0-148-generico,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>da 4.15.0-13-generico a 4.15.0-50-generico<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-Azure a 4.15.0-1045-Azure|
16.04 LTS | 9,25 | 4.4.0-21-generico per 4.4.0-146-generico,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>da 4.15.0-13-generico a 4.15.0-48-generico<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-Azure a 4.15.0-1042-Azure|
16.04 LTS | 9,24 | 4.4.0-21-generico per 4.4.0-143-generico,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>da 4.15.0-13-generico a 4.15.0-46-generico<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-Azure a 4.15.0-1040-Azure|

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel Debian supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
Debian 7 | 9.24, 9.25, 9.26, 9.27 | Da 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9,27 | da 3.16.0-4-amd64 a 3.16.0-9-amd64, 4.9.0-0. BPO. 4-amd64 a 4.9.0 0. BPO. 9-amd64 |
Debian 8 | 9,25, 9,26 | da 3.16.0-4-amd64 a 3.16.0-8-amd64, 4.9.0 0. BPO. 4-amd64 a 4.9.0 0. BPO. 8-amd64 |
Debian 8 | 9,24 | Da 3.16.0-4-amd64 a 3.16.0-7-amd64, da 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.8-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel SUSE Linux Enterprise Server 12 supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,27 | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> SP1 (LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.115-default</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>SP2 (LTSS) 4.4.121-92.73-default a 4.4.121-92.117-default</br></br>SP3 4.4.73-5-il valore predefinito è 4.4.180-94.97-default</br></br>SP3 4.4.138-4.7-da Azure a 4.4.180-4.31-Azure</br></br>SP4 4.12.14-94.41-impostazione predefinita per 4.12.14-95.24-impostazione predefinita</br>SP4 4.12.14-6.3-Azure per 4.12.14-6.18-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,26 | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> SP1 (LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.110-default</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>SP2 (LTSS) 4.4.121-92.73-default a 4.4.121-92.109-default</br></br>SP3 4.4.73-5-il valore predefinito è 4.4.178-94.91-default</br></br>SP3 4.4.138-4.7-da Azure a 4.4.178-4,28-Azure</br></br>SP4 4.12.14-94.41-impostazione predefinita per 4.12.14-95.16-impostazione predefinita</br>SP4 4.12.14-6.3-da Azure a 4.12.14-6.9-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,25 | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.107 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>SP2 (LTSS) 4.4.121-92.73-default a 4.4.121-92.104-default</br></br>SP3 4.4.73-5-il valore predefinito è 4.4.176-94.88-default</br></br>SP3 4.4.138-4.7-da Azure a 4.4.176-4.25-Azure</br></br>SP4 4.12.14-94.41-impostazione predefinita per 4.12.14-95.13-impostazione predefinita</br>SP4 4.12.14-6.3-da Azure a 4.12.14-6.9-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,24 | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.107 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>SP2 (LTSS) 4.4.121-92.73-default a 4.4.121-92.104-default</br></br>SP3 4.4.73-5-il valore predefinito è 4.4.176-94.88-default</br></br>SP4 4.12.14-94.41-impostazione predefinita per 4.12.14-95.13-impostazione predefinita |


## <a name="replicated-machines---linux-file-systemguest-storage"></a>Computer replicati - File system/archiviazione guest Linux

* File system: ext3, ext4, ReiserFS (solo Suse Linux Enterprise Server), XFS, BTRFS
* Gestore volumi: LVM2
* Software con percorsi multipli: Mapper dispositivi


## <a name="replicated-machines---compute-settings"></a>Computer replicati - Impostazioni di calcolo

**Impostazione** | **Supporto** | **Dettagli**
--- | --- | ---
Dimensione | Macchine virtuali di Azure di qualsiasi dimensione con almeno 2 core CPU e 1 GB di RAM | Verificare le [dimensioni delle macchine virtuali in Azure](../virtual-machines/windows/sizes.md).
Set di disponibilità | Supportato | Se si Abilita la replica per una macchina virtuale di Azure con le opzioni predefinite, viene creato automaticamente un set di disponibilità in base alle impostazioni dell'area di origine. È possibile modificare queste impostazioni.
Zone di disponibilità | Supportato |
Vantaggio Hybrid Use (HUB) | Supportato | Se la macchina virtuale di origine dispone di una licenza HUB abilitata, anche la macchina virtuale di failover o il failover di test userà la licenza HUB.
Set di scalabilità di macchine virtuali | Non supportate |
Immagini della raccolta di Azure - Pubblicate da Microsoft | Supportato | Supportate se la macchina virtuale viene eseguita in un sistema operativo supportato.
Immagini della raccolta di Azure - Pubblicate da terze parti | Supportato | Supportate se la macchina virtuale viene eseguita in un sistema operativo supportato.
Immagini personalizzate - Pubblicate da terze parti | Supportato | Supportate se la macchina virtuale viene eseguita in un sistema operativo supportato.
Macchine virtuali migrate tramite Site Recovery | Supportato | Se una VM VMware o un computer fisico è stato migrato ad Azure tramite Site Recovery, è necessario disinstallare la versione precedente del servizio di mobilità in esecuzione nel computer e riavviare il computer prima di eseguirne la replica in un'altra area di Azure.
Criteri RBAC | Non supportate | I criteri di controllo degli accessi in base al ruolo nelle VM non vengono replicati nella macchina virtuale di failover nell'area di destinazione.
Estensioni | Non supportate | Le estensioni non vengono replicate nella macchina virtuale di failover nell'area di destinazione. Deve essere installato manualmente dopo il failover.

## <a name="replicated-machines---disk-actions"></a>Computer replicati - Azioni del disco

**Azione** | **Dettagli**
-- | ---
Ridimensionamento del disco nella macchina virtuale replicata | Supportato
Aggiunta di un disco a una macchina virtuale replicata | Supportato

## <a name="replicated-machines---storage"></a>Computer replicati - Archiviazione

Questa tabella riepiloga il supporto per il disco del sistema operativo, il disco dati e il disco temporaneo della macchina virtuale di Azure.

- È importante osservare i limiti dei dischi e le destinazioni per le macchine virtuali [Linux](../virtual-machines/linux/disk-scalability-targets.md) e [Windows](../virtual-machines/windows/disk-scalability-targets.md) per evitare eventuali problemi di prestazioni.
- Se si esegue la distribuzione con le impostazioni predefinite, Site Recovery crea automaticamente i dischi e gli account di archiviazione in base alle impostazioni di origine.
- Se si esegue la personalizzazione, assicurarsi di seguire le linee guida.

**Componente** | **Supporto** | **Dettagli**
--- | --- | ---
Dimensione massima del disco del sistema operativo | 2048 GB | [Altre informazioni](../virtual-machines/windows/managed-disks-overview.md) sui dischi delle VM.
Disco temporaneo | Non supportate | Il disco temporaneo è sempre escluso dalla replica.<br/><br/> Non conservare dati persistenti sul disco temporaneo. [Altre informazioni](../virtual-machines/windows/managed-disks-overview.md)
Dimensione massima del disco dati | 8192 GB per Managed Disks<br></br>4095 GB per dischi non gestiti|
Dimensioni minime disco dati | Nessuna restrizione per i dischi non gestiti. 2 GB per Managed Disks | 
Numero massimo di dischi dati | Fino a 64, in conformità con il supporto per una specifica dimensione di VM di Azure | [Altre informazioni](../virtual-machines/windows/sizes.md) sulle dimensioni delle VM.
Frequenza di modifica del disco dati | Massimo 10 Mbps per disco per l'archiviazione Premium. Massimo 2 Mbps per disco per l'archiviazione Standard. | Se la frequenza di modifica media dei dati sul disco supera costantemente il limite massimo, la replica non verrà aggiornata.<br/><br/>  Se tuttavia il limite massimo viene superato sporadicamente, la replica può essere aggiornata, ma i punti di ripristino potrebbero essere visualizzati leggermente ritardati.
Disco dati - Account di archiviazione Standard | Supportato |
Disco dati - Account di archiviazione Premium | Supportato | Se una macchina virtuale dispone di dischi distribuiti tra account di archiviazione Standard e Premium, è possibile selezionare un account di archiviazione di destinazione diverso per ogni disco per assicurarsi di avere la stessa configurazione di archiviazione nell'area di destinazione.
Managed Disks - Standard | Supportato nelle aree di Azure in cui è supportato Azure Site Recovery. |
Managed Disks - Premium | Supportato nelle aree di Azure in cui è supportato Azure Site Recovery. |
SSD Standard | Supportato |
Ridondanza | Sono supportate le archiviazioni con ridondanza locale e geografica.<br/><br/> L'archiviazione con ridondanza della zona non è supportata.
Archiviazione ad accesso frequente e sporadico | Non supportate | I dischi delle macchine virtuali non sono supportati per l'archiviazione ad accesso frequente e sporadico
Spazi di archiviazione | Supportato |
Crittografia per dati inattivi (SSE) | Supportato | La crittografia per dati inattivi (SSE) è l'impostazione predefinita per gli account di archiviazione.   
Crittografia dischi di Azure (ADE) per sistema operativo Windows | Supportato |
Crittografia dischi di Azure (ADE) per sistema operativo Linux | Non supportate |
Aggiunta a caldo | Supportato | L'abilitazione della replica per un disco dati aggiunto a una macchina virtuale di Azure replicata è supportata per le macchine virtuali che usano dischi gestiti.
Disco di rimozione a caldo | Non supportate | Se si rimuove un disco dati nella macchina virtuale, è necessario disabilitare la replica e abilitare di nuovo la replica per la macchina virtuale.
Esclusione disco | Supporto. Per configurare, è necessario usare [PowerShell](azure-to-azure-exclude-disks.md) . |  Per impostazione predefinita, i dischi temporanei sono esclusi.
Spazi di archiviazione diretta  | Supportato per i punti di ripristino coerenti con l'arresto anomalo. I punti di ripristino coerenti con l'applicazione non sono supportati. |
File server di scalabilità orizzontale  | Supportato per i punti di ripristino coerenti con l'arresto anomalo. I punti di ripristino coerenti con l'applicazione non sono supportati. |
Archiviazione con ridondanza locale | Supportato |
Archiviazione con ridondanza geografica | Supportato |
RA-GRS | Supportato |
ZRS | Non supportate |
Archiviazione ad accesso frequente e sporadico | Non supportate | I dischi delle macchine virtuali non sono supportati per l'archiviazione ad accesso frequente e sporadico
Firewall di Archiviazione di Azure per reti virtuali  | Supportato | Se si limita l'accesso alla rete virtuale agli account di archiviazione, abilitare [Consenti servizi Microsoft attendibili](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Account di archiviazione V2 generico (livelli di accesso frequente e sporadico) | Sì | Aumento sostanziale dei costi delle transazioni rispetto agli account di archiviazione V1 generici

>[!IMPORTANT]
> Per evitare problemi di prestazioni, assicurarsi di seguire gli obiettivi di scalabilità e prestazioni del disco della macchina virtuale per macchine virtuali [Linux](../virtual-machines/linux/disk-scalability-targets.md) o [Windows](../virtual-machines/windows/disk-scalability-targets.md) . Se si usano le impostazioni predefinite, Site Recovery crea i dischi e gli account di archiviazione necessari in base alla configurazione di origine. Se si personalizzano e si selezionano impostazioni personalizzate, seguire gli obiettivi di scalabilità e prestazioni del disco per le macchine virtuali di origine.

## <a name="limits-and-data-change-rates"></a>Limiti e frequenza di modifica dei dati

La tabella seguente riepiloga Site Recovery limiti.

- Questi limiti sono basati sui test, ma ovviamente non coprono tutte le possibili combinazioni di I/O delle applicazioni.
- I risultati effettivi possono variare in base alla combinazione di I/O delle app.
- È necessario considerare due limiti, la varianza dei dati per disco e la varianza dei dati per ogni macchina virtuale.
- Ad esempio, se si usa un disco P20 Premium come descritto nella tabella seguente, Site Recovery possibile gestire 5 MB di varianza per disco, con un massimo di cinque dischi per macchina virtuale, a causa del limite di 25 MB/s di varianza totale per macchina virtuale.

**Destinazione di archiviazione** | **I/O del disco di origine medio** |**Varianza dati media disco di origine** | **Varianza dati totale giornaliera disco di origine**
---|---|---|---
Archiviazione standard | 8 KB | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 8 KB  | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 16 KB | 4 MB/s |  336 GB per disco
Disco P10 o P15 Premium | 32 KB o superiori | 8 MB/s | 672 GB per disco
Disco P20, P30, P40 o P50 Premium | 8 KB    | 5 MB/s | 421 GB per disco
Disco P20, P30, P40 o P50 Premium | 16 KB o superiori |20 MB/s | 1684 GB per disco

## <a name="replicated-machines---networking"></a>Computer replicati - Reti
**Impostazione** | **Supporto** | **Dettagli**
--- | --- | ---
NIC | Numero massimo supportato per una specifica dimensione di macchina virtuale di Azure | Vengono create schede di interfaccia di rete contestualmente alla creazione della VM durante il failover.<br/><br/> Il numero di schede di interfaccia di rete sulla macchina virtuale di failover dipende dal numero di schede di rete sulla VM di origine quando è stata abilitata la replica. L'aggiunta o la rimozione di una scheda di interfaccia di rete dopo l'abilitazione della replica non influisce sul numero di schede sulla VM replicata dopo il failover. Si noti inoltre che non è garantito che l'ordine delle schede di rete dopo il failover sia uguale a quello originale.
Servizio di bilanciamento del carico Internet | Supportato | Associare il servizio di bilanciamento del carico preconfigurato tramite uno script di Automazione di Azure in un piano di ripristino.
Servizio di bilanciamento del carico interno | Supportato | Associare il servizio di bilanciamento del carico preconfigurato tramite uno script di Automazione di Azure in un piano di ripristino.
Indirizzo IP pubblico | Supportato | Associare un indirizzo IP pubblico esistente alla scheda di interfaccia di rete. In alternativa, creare un indirizzo IP pubblico e associarlo alla scheda di interfaccia di rete tramite uno script di Automazione di Azure in un piano di ripristino.
Gruppo di sicurezza di rete o scheda di interfaccia di rete | Supportato | Associare il gruppo di sicurezza di rete alla scheda di interfaccia di rete tramite uno script di Automazione di Azure in un piano di ripristino.
Gruppo di sicurezza di rete o subnet | Supportato | Associare il gruppo di sicurezza di rete alla subnet tramite uno script di Automazione di Azure in un piano di ripristino.
Indirizzo IP riservato (statico) | Supportato | Se la scheda di rete sulla macchina virtuale di origine ha un indirizzo IP statico e la subnet di destinazione dispone dello stesso IP, viene assegnato alla macchina virtuale di failover.<br/><br/> Se la subnet di destinazione non ha lo stesso indirizzo IP, uno degli IP disponibili nella subnet viene riservato per la macchina virtuale.<br/><br/> È anche possibile specificare un indirizzo IP fisso e una subnet in **Elementi replicati** > **Impostazioni** > **Calcolo e rete** > **Interfacce di rete**.
Indirizzo IP dinamico | Supportato | Se la scheda di interfaccia di rete nella macchina virtuale di origine ha indirizzi IP dinamici, anche la scheda sulla macchina virtuale di failover è dinamica per impostazione predefinita.<br/><br/> Se necessario, è possibile modificare questa opzione in un indirizzo IP fisso.
Più indirizzi IP | Non supportate | Quando si esegue il failover di una macchina virtuale con una scheda di interfaccia di rete con più indirizzi IP, viene mantenuto solo l'indirizzo IP primario della scheda di interfaccia di rete nell'area di origine. Per assegnare più indirizzi IP, è possibile aggiungere macchine virtuali a un [piano di ripristino](recovery-plan-overview.md) e associare uno script per assegnare indirizzi IP aggiuntivi al piano oppure è possibile apportare la modifica manualmente o con uno script dopo il failover. 
Gestione traffico     | Supportato | È possibile preconfigurare Gestione traffico in modo che il traffico venga regolarmente indirizzato all'endpoint nell'area di origine e all'endpoint nell'area di destinazione in caso di failover.
DNS di Azure | Supportato |
DNS personalizzato  | Supportato |
Proxy non autenticato | Supportato | [Altre informazioni](site-recovery-azure-to-azure-networking-guidance.md)    
Proxy autenticato | Non supportate | Se la macchina virtuale utilizza un proxy autenticato per la connettività in uscita, non può essere replicata tramite Azure Site Recovery.    
Connessione da sito a sito VPN a locale<br/><br/>(con o senza ExpressRoute)| Supportato | Verificare che UdR e gruppi siano configurati in modo tale che il traffico Site Recovery non venga indirizzato in locale. [Altre informazioni](site-recovery-azure-to-azure-networking-guidance.md)    
Connessione da rete virtuale a rete virtuale | Supportato | [Altre informazioni](site-recovery-azure-to-azure-networking-guidance.md)  
Endpoint servizio di rete virtuale | Supportato | Se si limita l'accesso alla rete virtuale agli account di archiviazione, assicurarsi che ai servizi Microsoft attendibili sia consentito l'accesso all'account di archiviazione.
Rete accelerata | Supportato | La rete accelerata deve essere abilitata su una macchina virtuale di origine. [Altre informazioni](azure-vm-disaster-recovery-with-accelerated-networking.md)



## <a name="next-steps"></a>Passaggi successivi
- Leggere le [linee guida sulla rete](site-recovery-azure-to-azure-networking-guidance.md) per la replica di macchine virtuali di Azure.
- Distribuire il ripristino di emergenza [replicando le macchine virtuali di Azure](site-recovery-azure-to-azure.md).
