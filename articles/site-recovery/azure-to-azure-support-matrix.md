---
title: Matrice di supporto per il ripristino di emergenza di macchine virtuali di Azure tra aree di Azure con Azure Site Recovery | Microsoft Docs
description: Vengono riepilogati i prerequisiti e supporto di ripristino di emergenza di macchine virtuali di Azure da un'area a un'altra con Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 06/09/2019
ms.author: raynew
ms.openlocfilehash: 6a6db30bfe6e4dc1eed04d11b53113c90210a5cf
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275213"
---
# <a name="support-matrix-for-replicating-azure-vms-from-one-region-to-another"></a>Matrice di supporto per la replica di macchine virtuali di Azure da un'area a un'altra

Questo articolo vengono riepilogati i prerequisiti e supporto quando si impostano di ripristino di emergenza di macchine virtuali di Azure da un'area di Azure a un altro, usando il [Azure Site Recovery](site-recovery-overview.md) servizio.


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
Asia | India meridionale, India centrale, Asia sud-orientale, Asia orientale, Giappone orientale, Giappone occidentale, Corea del Sud centrale, Corea del Sud meridionale
Australia   | Australia orientale, Australia sud-orientale, Australia centrale, Australia centrale 2
Azure Government    | US GOV Virginia, US GOV Iowa, US GOV Arizona, US GOV Texas, US DOD East, US DOD Central 
Germania | Germania centrale, Germania nord-orientale
Cina | Cina orientale, Cina settentrionale, Cina settentrionale2, Cina orientale 2

>[!NOTE]
>
> - Per la **Brasile meridionale**, è possibile eseguire la replica e failover in queste aree: Stati Uniti centro-meridionali, Stati Uniti centro occidentali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali, Stati Uniti occidentali 2 e North Central US.
> - Brasile meridionale sono utilizzabili solo come un'area di origine da cui possono replicare le macchine virtuali usando Site Recovery. Non può fungere da un'area di destinazione. Ciò è dovuto a problemi di latenza a causa di distanze geografiche.
> - È possibile lavorare all'interno di aree per cui si dispone di accesso appropriato.
> - Se non viene visualizzata l'area in cui si desidera creare un insieme di credenziali, assicurarsi che la sottoscrizione ha accesso a creare risorse in tale area.
> - Se quando si abilita la replica non è possibile visualizzare un'area all'interno di un cluster geografico, assicurarsi che la sottoscrizione disponga delle autorizzazioni per creare macchine virtuali in tale area.



## <a name="cache-storage"></a>Archiviazione cache

Questa tabella riepiloga il supporto per l'account di archiviazione della cache usato da Site Recovery durante la replica.

**Impostazione** | **Supporto** | **Dettagli**
--- | --- | ---
Account di archiviazione V2 di utilizzo generico (livelli di accesso frequente e sporadico) | Non supportati. | La limitazione sussiste per l'archiviazione della cache, poiché i costi di transazione per V2 sono molto più elevati rispetto agli account di archiviazione V1.
Firewall di Archiviazione di Azure per reti virtuali  | Supportato | Se si usano account di archiviazione cache o di archiviazione di destinazione abilitati per il firewall, assicurarsi di selezionare ["Consenti ai servizi Microsoft attendibili"](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).


## <a name="replicated-machine-operating-systems"></a>Sistemi operativi di computer replicati

Site Recovery supporta la replica di macchine virtuali di Azure che eseguono i sistemi operativi elencati in questa sezione.

### <a name="windows"></a>Windows

**Sistema operativo** | **Dettagli**
--- | ---
Windows Server 2019 |
Windows Server 2016  | Server Core, Server con Esperienza Desktop
Windows Server 2012 R2 |
Windows Server 2012 |
Windows Server 2008 R2 | Con SP1 o versione successiva
Windows 10 (x64) |
Windows 8.1 (x64) |
Windows 8 (x64) |
Windows 7 (x64) | Con SP1 o versione successiva (Windows 7 RTM non è supportato)

#### <a name="linux"></a>Linux

**Sistema operativo** | **Dettagli**
--- | ---
Red Hat Enterprise Linux. | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6  
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6
Server Ubuntu 14.04 LTS | [Versioni del kernel supportate](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Server Ubuntu 16.04 LTS | [Versioni del kernel supportate](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Server Ubuntu usando l'autenticazione basata su password e accesso e il pacchetto cloud-init per configurare i cloud macchine virtuali, potrebbe essere basato su password accesso disabilitato in caso di failover (a seconda della configurazione di cloudinit). Accesso basato su password può essere riabilitato nella macchina virtuale reimpostando la password tramite il supporto tecnico > Risoluzione dei problemi > dal menu Impostazioni (di failover della macchina virtuale nel portale di Azure.
Debian 7 | [Versioni del kernel supportate](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Versioni del kernel supportate](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(Versioni del kernel supportate)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> L'aggiornamento dei computer di replica da SP3 a SP4 non è supportato. Se un computer replicato è stato aggiornato, è necessario disabilitare la replica e riabilitarla dopo l'aggiornamento.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5 <br/><br/> Con kernel compatibile Red Hat o di Unbreakable Enterprise Kernel versione 3 (UEK3).


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel Ubuntu supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
14.04 LTS | 9.25 | 3.13.0-24-Generic a 3.13.0-169-generic,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-Generic a 4.4.0-146-generic,<br/>4.15.0-1023-Azure a 4.15.0-1042-azure |
14.04 LTS | 9.24 | 3.13.0-24-Generic a 3.13.0-167-generic,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-Generic a 4.4.0-143-generic,<br/>4.15.0-1023-Azure a 4.15.0-1040-azure |
14.04 LTS | 9.23 | 3.13.0-24-Generic a 3.13.0-165-generic,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-Generic a 4.4.0-142-generic,<br/>4.15.0-1023-Azure a 4.15.0-1037-azure |
14.04 LTS | 9.22 | Da 3.13.0-24 generica a 3.13.0-164 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-140 generica<br/>Da 4.15.0-1023 Azure a 4.15.0-1036 Azure |
|||
16.04 LTS | 9.25 | 4.4.0-21-Generic a 4.4.0-146-generic,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-Generic a 4.15.0-48-generic<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-Azure a 4.15.0-1042-azure|
16.04 LTS | 9.24 | 4.4.0-21-Generic a 4.4.0-143-generic,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-Generic a 4.15.0-46-generic<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-Azure a 4.15.0-1040-azure|
16.04 LTS | 9.23 | 4.4.0-21-Generic a 4.4.0-142-generic,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-Generic a 4.15.0-45-generic<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-Azure a 4.15.0-1037-azure|
16.04 LTS | 9.22 | Da 4.4.0-21 generica a 4.4.0-140 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>Da 4.15.0-13 generica a 4.15.0-43 generica<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>Da 4.15.0-1012 Azure a 4.15.0-1036 Azure|

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel Debian supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
Debian 7 | 9.22,9.23,9.24,9.25 | Da 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.25 | 3.16.0-4-amd64 to 3.16.0-8-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.8-amd64 |
Debian 8 | 9.22, 9.23, 9.24 | Da 3.16.0-4-amd64 a 3.16.0-7-amd64, da 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.8-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel SUSE Linux Enterprise Server 12 supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.25 | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.107 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>4\.4.121-92.73-default SP2(LTSS) a 4.4.121-92.104-default</br></br>4\.4.73-5-default SP3 a 4.4.176-94.88-default</br></br>4\.4.138-4.7-azure SP3 a 4.4.176-4.25-azure</br></br>4\.12.14-94.41-default SP4 a 4.12.14-95.13-default</br>4\.12.14-6.3-azure SP4 a 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.24 | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.107 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>4\.4.121-92.73-default SP2(LTSS) a 4.4.121-92.104-default</br></br>4\.4.73-5-default SP3 a 4.4.176-94.88-default</br></br>4\.12.14-94.41-default SP4 a 4.12.14-95.13-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9.23 | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.107 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>4\.4.121-92.73-default SP2(LTSS) a 4.4.121-92.101-default</br></br>Da 4.4.73-5 SP3 predefinita a 4.4.162-94.69 predefinita</br></br>4\.12.14-94.41-default SP4 a 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.22 | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.107 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>Da 4.4.121-92.73 SP2(LTSS) predefinita a 4.4.121-92.98 predefinita</br></br>Da 4.4.73-5 SP3 predefinita a 4.4.162-94.72 predefinita |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Computer replicati - File system/archiviazione guest Linux

* File system: ext3, ext4, ReiserFS (solo Suse Linux Enterprise Server), XFS, BTRFS
* Gestore volumi: LVM2
* Software con percorsi multipli: Mapper dispositivi


## <a name="replicated-machines---compute-settings"></a>Computer replicati - Impostazioni di calcolo

**Impostazione** | **Supporto** | **Dettagli**
--- | --- | ---
Dimensione | Macchine virtuali di Azure di qualsiasi dimensione con almeno 2 core CPU e 1 GB di RAM | Verificare le [dimensioni delle macchine virtuali in Azure](../virtual-machines/windows/sizes.md).
Set di disponibilità | Supportato | Se si abilita la replica per una macchina virtuale di Azure con le opzioni predefinite, un set di disponibilità viene creato automaticamente in base alle impostazioni dell'area di origine. È possibile modificare queste impostazioni.
Zone di disponibilità | Supportato |
Vantaggio Hybrid Use (HUB) | Supportato | Se la macchina virtuale di origine dispone di una licenza HUB abilitata, anche la macchina virtuale di failover o il failover di test userà la licenza HUB.
Set di scalabilità di macchine virtuali | Non supportate |
Immagini della raccolta di Azure - Pubblicate da Microsoft | Supportato | Supportate se la macchina virtuale viene eseguita in un sistema operativo supportato.
Immagini della raccolta di Azure - Pubblicate da terze parti | Supportato | Supportate se la macchina virtuale viene eseguita in un sistema operativo supportato.
Immagini personalizzate - Pubblicate da terze parti | Supportato | Supportate se la macchina virtuale viene eseguita in un sistema operativo supportato.
Macchine virtuali migrate tramite Site Recovery | Supportato | Se una VM VMware o un computer fisico è stato migrato ad Azure tramite Site Recovery, è necessario disinstallare la versione precedente del servizio di mobilità in esecuzione nel computer e riavviare il computer prima di eseguirne la replica in un'altra area di Azure.
Criteri RBAC | Non supportate | Accesso criteri di controllo degli accessi in macchine virtuali non vengono replicati per il failover della macchina virtuale nell'area di destinazione basato su ruoli.
Estensioni | Non supportate | Le estensioni non vengono replicate per il failover della macchina virtuale nell'area di destinazione. Deve essere installato manualmente dopo il failover.

## <a name="replicated-machines---disk-actions"></a>Computer replicati - Azioni del disco

**Azione** | **Dettagli**
-- | ---
Ridimensionamento del disco nella macchina virtuale replicata | Supportato
Aggiunta di un disco a una macchina virtuale replicata | Non supportati.<br/><br/> È necessario disabilitare la replica per la macchina virtuale, aggiungere il disco e quindi riabilitare nuovamente la replica.

## <a name="replicated-machines---storage"></a>Computer replicati - Archiviazione

Questa tabella riepiloga il supporto per il disco del sistema operativo, il disco dati e il disco temporaneo della macchina virtuale di Azure.

- È importante osservare i limiti dei dischi e le destinazioni per le macchine virtuali [Linux](../virtual-machines/linux/disk-scalability-targets.md) e [Windows](../virtual-machines/windows/disk-scalability-targets.md) per evitare eventuali problemi di prestazioni.
- Se si esegue la distribuzione con le impostazioni predefinite, Site Recovery crea automaticamente i dischi e gli account di archiviazione in base alle impostazioni di origine.
- Se si esegue la personalizzazione, assicurarsi di seguire le linee guida.

**Componente** | **Supporto** | **Dettagli**
--- | --- | ---
Dimensione massima del disco del sistema operativo | 2048 GB | [Altre informazioni](../virtual-machines/windows/managed-disks-overview.md) sui dischi delle VM.
Disco temporaneo | Non supportate | Il disco temporaneo è sempre escluso dalla replica.<br/><br/> Non conservare dati persistenti sul disco temporaneo. [Altre informazioni](../virtual-machines/windows/managed-disks-overview.md)
Dimensione massima del disco dati | 4095 GB |
Dimensioni minime del disco dati | Nessuna restrizione per i dischi non gestiti. 2 GB per i dischi gestiti | 
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
Crittografia dischi di Azure (ADE) per sistema operativo Windows | Le macchine virtuali abilitate per [la crittografia con l'app di Azure AD](https://aka.ms/ade-aad-app) sono supportate |
Crittografia dischi di Azure (ADE) per sistema operativo Linux | Non supportate |
Aggiunta a caldo | Supportato | L'abilitazione della replica per un disco dati che aggiunta a una VM di Azure replicate è supportata per le macchine virtuali che usano dischi gestiti.
Rimozione a caldo disco | Non supportate | Se si rimuove il disco dati nella macchina virtuale, è necessario disabilitare la replica e abilitarla nuovamente per la macchina virtuale.
Esclusione disco | supporto tecnico. È necessario utilizzare [Powershell](azure-to-azure-exclude-disks.md) configurare. |  I dischi temporanei vengono esclusi per impostazione predefinita.
Spazi di archiviazione diretta  | Supportato per i punti di ripristino coerenti con l'arresto anomalo. I punti di ripristino coerenti con l'applicazione non sono supportati. |
File server di scalabilità orizzontale  | Supportato per i punti di ripristino coerenti con l'arresto anomalo. I punti di ripristino coerenti con l'applicazione non sono supportati. |
Archiviazione con ridondanza locale | Supportato |
Archiviazione con ridondanza geografica | Supportato |
RA-GRS | Supportato |
ZRS | Non supportate |
Archiviazione ad accesso frequente e sporadico | Non supportate | I dischi delle macchine virtuali non sono supportati per l'archiviazione ad accesso frequente e sporadico
Firewall di Archiviazione di Azure per reti virtuali  | Supportato | Se limitare l'accesso alla rete virtuale per gli account di archiviazione, abilitare [Consenti attendibili Microsoft services](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Account di archiviazione V2 generico (livelli di accesso frequente e sporadico) | No | Aumento sostanziale dei costi delle transazioni rispetto agli account di archiviazione V1 generici

>[!IMPORTANT]
> Per evitare problemi di prestazioni, assicurarsi di seguire obiettivi della macchina virtuale del disco scalabilità e prestazioni per [Linux](../virtual-machines/linux/disk-scalability-targets.md) oppure [Windows](../virtual-machines/windows/disk-scalability-targets.md) macchine virtuali. Se si usano le impostazioni predefinite, Site Recovery crea l'account di archiviazione, in base alla configurazione di origine e i dischi necessari. Se si personalizzano e si selezionano impostazioni specifiche, seguire gli obiettivi di scalabilità e prestazioni del disco per le VM di origine.

## <a name="limits-and-data-change-rates"></a>Dati e i limiti di frequenza di modifica

La tabella seguente riepiloga i limiti di Site Recovery.

- Questi limiti si basano su test di Microsoft, ma ovviamente non coprono tutte le combinazioni dei / o le applicazioni.
- Risultati effettivi possono variare in base alle app i/o combinazione.
- Esistono due limiti da considerare, per ogni disco dati per i dati della macchina virtuale di varianza e della varianza.
- Ad esempio, se si usa un disco P20 Premium come descritto nella tabella seguente, Site Recovery può gestire 5 MB di varianza per ogni disco, con al massimo di cinque tali dischi per ogni macchina virtuale, a causa del limite di 25 varianza totale MB/s per ogni macchina virtuale.

**Destinazione di archiviazione** | **I/o disco di origine medio** |**Varianza dati media disco di origine** | **Varianza dati totale giornaliera disco di origine**
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
NIC | Numero massimo supportato per una specifica dimensione di macchina virtuale di Azure | Vengono create schede di interfaccia di rete contestualmente alla creazione della VM durante il failover.<br/><br/> Il numero di schede di interfaccia di rete sulla macchina virtuale di failover dipende dal numero di schede di rete sulla VM di origine quando è stata abilitata la replica. L'aggiunta o la rimozione di una scheda di interfaccia di rete dopo l'abilitazione della replica non influisce sul numero di schede sulla VM replicata dopo il failover. Si noti che l'ordine delle schede di rete dopo il failover non è necessariamente anche corrispondere all'ordine originale.
Servizio di bilanciamento del carico Internet | Supportato | Associare il servizio di bilanciamento del carico preconfigurato tramite uno script di Automazione di Azure in un piano di ripristino.
Servizio di bilanciamento del carico interno | Supportato | Associare il servizio di bilanciamento del carico preconfigurato tramite uno script di Automazione di Azure in un piano di ripristino.
Indirizzo IP pubblico | Supportato | Associare un indirizzo IP pubblico esistente alla scheda di interfaccia di rete. In alternativa, creare un indirizzo IP pubblico e associarlo alla scheda di interfaccia di rete tramite uno script di Automazione di Azure in un piano di ripristino.
Gruppo di sicurezza di rete o scheda di interfaccia di rete | Supportato | Associare il gruppo di sicurezza di rete alla scheda di interfaccia di rete tramite uno script di Automazione di Azure in un piano di ripristino.
Gruppo di sicurezza di rete o subnet | Supportato | Associare il gruppo di sicurezza di rete alla subnet tramite uno script di Automazione di Azure in un piano di ripristino.
Indirizzo IP riservato (statico) | Supportato | Se la scheda di rete sulla macchina virtuale di origine ha un indirizzo IP statico e la subnet di destinazione dispone dello stesso IP, viene assegnato alla macchina virtuale di failover.<br/><br/> Se la subnet di destinazione non ha lo stesso indirizzo IP, uno degli IP disponibili nella subnet viene riservato per la macchina virtuale.<br/><br/> È anche possibile specificare un indirizzo IP fisso e una subnet in **Elementi replicati** > **Impostazioni** > **Calcolo e rete** > **Interfacce di rete**.
Indirizzo IP dinamico | Supportato | Se la scheda di interfaccia di rete nella macchina virtuale di origine ha indirizzi IP dinamici, anche la scheda sulla macchina virtuale di failover è dinamica per impostazione predefinita.<br/><br/> Se necessario, è possibile modificare questa opzione in un indirizzo IP fisso.
Più indirizzi IP | Non supportate | Quando esegue il failover una macchina virtuale con un'interfaccia di rete con più indirizzi IP, viene mantenuto solo l'indirizzo IP primaria dell'interfaccia di rete nell'area di origine. Per assegnare più indirizzi IP, è possibile aggiungere VM a un [piano di ripristino](recovery-plan-overview.md) e allegare uno script per assegnare indirizzi IP aggiuntivi al piano oppure è possibile apportare la modifica manualmente o tramite uno script dopo il failover. 
Gestione traffico     | Supportato | È possibile preconfigurare Gestione traffico in modo che il traffico venga regolarmente indirizzato all'endpoint nell'area di origine e all'endpoint nell'area di destinazione in caso di failover.
DNS di Azure | Supportato |
DNS personalizzato  | Supportato |
Proxy non autenticato | Supportato | [Learn more].(site-recovery-azure-to-azure-networking-guidance.md)   
Proxy autenticato | Non supportate | Se la macchina virtuale utilizza un proxy autenticato per la connettività in uscita, non può essere replicata tramite Azure Site Recovery.    
Connessione site-to-site VPN da sito locale<br/><br/>(con o senza ExpressRoute)| Supportato | Assicurarsi che le route definite dall'utente e gli Nsg sono configurati in modo che il traffico di Site Recovery non viene instradato in locale. [Altre informazioni](site-recovery-azure-to-azure-networking-guidance.md)    
Connessione da rete virtuale a rete virtuale | Supportato | [Altre informazioni](site-recovery-azure-to-azure-networking-guidance.md)  
Endpoint servizio di rete virtuale | Supportato | Se si limita l'accesso alla rete virtuale agli account di archiviazione, assicurarsi che ai servizi Microsoft attendibili sia consentito l'accesso all'account di archiviazione.
Rete accelerata | Supportato | La rete accelerata deve essere abilitata su una macchina virtuale di origine. [Altre informazioni](azure-vm-disaster-recovery-with-accelerated-networking.md)



## <a name="next-steps"></a>Passaggi successivi
- Lettura [informazioni aggiuntive sulla rete](site-recovery-azure-to-azure-networking-guidance.md) per la replica di macchine virtuali di Azure.
- Distribuire il ripristino di emergenza [replicando le macchine virtuali di Azure](site-recovery-azure-to-azure.md).
