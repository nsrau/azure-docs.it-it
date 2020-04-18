---
title: Matrice di supporto per il ripristino di emergenza della macchina virtuale di Azure con Azure Site RecoverySupport matrix for Azure VM disaster recovery with Azure Site Recovery
description: Riepiloga il supporto per il ripristino di emergenza delle macchine virtuali di Azure in un'area secondaria con Azure Site Recovery.Summarizes support for Azure VMs disaster recovery to a secondary region with Azure Site Recovery.
ms.topic: article
ms.date: 01/10/2020
ms.author: raynew
ms.openlocfilehash: 3e66d71011937c3e22c6f1537bcd448d4aa3bbf3
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605514"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Matrice di supporto per il ripristino di emergenza della macchina virtuale di Azure tra aree di AzureSupport matrix for Azure VM disaster recovery between Azure regions

Questo articolo riepiloga il supporto e i prerequisiti per il ripristino di emergenza delle macchine virtuali di Azure da un'area di Azure a un'altra, usando il servizio [Azure Site Recovery.This](site-recovery-overview.md) article summarizes support and prerequisites for disaster recovery of Azure VMs from one Azure region to another, using the Azure Site Recovery service.


## <a name="deployment-method-support"></a>Supporto del metodo di distribuzione

**Distribuzione** |  **Supporto**
--- | ---
**Azure portal** | Supportato.
**PowerShell** | Supportato. [Altre informazioni](azure-to-azure-powershell.md)
**REST API** | Supportato.
**CLI** | Attualmente non supportato


## <a name="resource-support"></a>Supporto delle risorse

**Azione risorsa** | **Dettagli**
--- | ---
**Spostare gli insiemi di credenziali tra gruppi di risorseMove vaults across resource groups** | Non supportate
**Spostamento di risorse di calcolo, archiviazione e rete tra gruppi di risorse** | Non supportato.<br/><br/> Se si sposta una macchina virtuale o componenti associati come archiviazione o rete dopo la replica della VM, è necessario disabilitare la replica e riabilitarla per la VM.
**Replica di macchine virtuali di Azure da una sottoscrizione a un'altra per il ripristino di emergenza** | Supportate nello stesso tenant di Azure Active Directory.
**Eseguire la migrazione di macchine virtuali tra aree all'interno dei cluster geografici supportati (all'interno e tra sottoscrizioni)** | Supportate nello stesso tenant di Azure Active Directory.
**Migrazione di macchine virtuali all'interno della stessa area** | Non supportato.

## <a name="region-support"></a>Supporto di area

È possibile eseguire la replica e il ripristino di macchine virtuali tra due aree qualsiasi all'interno dello stesso cluster geografico. I cluster geografici vengono definiti tenendo presente la latenza e la sovranità dei dati.


**Cluster geografico** | **Aree di AzureAzure regions**
-- | --
America | Canada orientale, Canada centrale, Stati Uniti centro-meridionali, Stati Uniti centro-occidentali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti centrali, Stati Uniti centro-settentrionali
Europa | Regno Unito Ovest, Regno Unito Sud, Nord Europa, Europa occidentale, Sud Africa Ovest, Sud Africa Nord, Norvegia Orientale, Norvegia Ovest
Asia | India meridionale, India centrale, India occidentale, Sud-est asiatico, Asia orientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea del Sud
Australia    | Australia orientale, Australia sud-orientale, Australia centrale, Australia centrale 2
Azure Government    | US GOV Virginia, US GOV Iowa, US GOV Arizona, US GOV Texas, US DOD East, US DOD Central
Germania    | Germania centrale, Germania nord-orientale
Cina | Cina orientale, Cina settentrionale, Cina settentrionale2, Cina orientale 2
Aree con restrizioni riservate al ripristino di emergenza nazionale |Germania Nord riservata alla Germania Centro-Ovest, Svizzera Ovest riservata alla Svizzera Nord, Francia Sud riservata a France Central, Emirati Arabi Uniti Centrale limitata ai clienti UAE North

>[!NOTE]
>
> - Per **Brasile Meridionale**, è possibile replicare e eseguire il failover in queste regioni: Stati Uniti centro-meridionali, Stati Uniti centro-occidentali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali, Stati Uniti occidentali 2 e Stati Uniti centro-settentrionali.
> - Il Brasile meridionale può essere usato solo come area di origine da cui le macchine virtuali possono eseguire la replica tramite Site Recovery.Brazil South can only be used as a source region from which VMs can replicate using Site Recovery. Non può fungere da area di destinazione. Ciò è dovuto a problemi di latenza dovuti a distanze geografiche. Si noti che se si esegue il failover dal Brasile meridionale come area di origine a una destinazione, il failback a Brasile sud dall'area di destinazione è supportato.
> - È possibile lavorare all'interno delle aree per le quali si dispone dell'accesso appropriato.
> - Se l'area in cui si vuole creare un insieme di credenziali non viene visualizzata, assicurarsi che la sottoscrizione abbia accesso per creare risorse in tale area.
> - Se non è possibile visualizzare un'area all'interno di un cluster geografico quando si abilita la replica, assicurarsi che la sottoscrizione disponga delle autorizzazioni per creare macchine virtuali in tale area.



## <a name="cache-storage"></a>Archiviazione cache

Questa tabella riepiloga il supporto per l'account di archiviazione della cache usato da Site Recovery durante la replica.

**Impostazione** | **Supporto** | **Dettagli**
--- | --- | ---
Account di archiviazione V2 di utilizzo generico (livelli di accesso frequente e sporadico) | Supportato | L'utilizzo di GPv2 non è consigliato perché i costi di transazione per V2 sono notevolmente superiori rispetto agli account di archiviazione V1.
Archiviazione Premium | Non supportate | Gli account di archiviazione standard vengono usati per l'archiviazione nella cache, per ottimizzare i costi.
Firewall di Archiviazione di Azure per reti virtuali  | Supportato | Se si usano account di archiviazione cache o di archiviazione di destinazione abilitati per il firewall, assicurarsi di selezionare ["Consenti ai servizi Microsoft attendibili"](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).<br></br>Assicurarsi anche di consentire l'accesso ad almeno una subnet della rete virtuale di origine.


## <a name="replicated-machine-operating-systems"></a>Sistemi operativi di computer replicati

Site Recovery supporta la replica di macchine virtuali di Azure che eseguono i sistemi operativi elencati in questa sezione. Si noti che se un computer già replicante viene successivamente aggiornato (o declassato) a un kernel principale diverso, è necessario disabilitare la replica e riattivare la replica dopo l'aggiornamento.

### <a name="windows"></a>Windows


**Sistema operativo** | **Dettagli**
--- | ---
Windows Server 2019 | Supportato per Server Core, Server con Esperienza desktop.
Windows Server 2016  | Server Core supportato, Server con esperienza desktop.
Windows Server 2012 R2 | Supportato.
Windows Server 2012 | Supportato.
Windows Server 2008 R2 con SP1/SP2 | Supportato.<br/><br/> Dalla versione [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) dell'estensione del servizio Mobility per le macchine virtuali di Azure, è necessario installare un aggiornamento dello stack di manutenzione di Windows [(SSU)](https://support.microsoft.com/help/4490628) e [SHA-2](https://support.microsoft.com/help/4474419) nei computer che eseguono Windows Server 2008 R2 SP1/SP2.  SHA-1 non è supportato da settembre 2019 e se la firma del codice SHA-2 non è abilitata l'estensione dell'agente non installerà/aggiorna come previsto. Ulteriori informazioni [sull'aggiornamento e sui requisiti SHA-2](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Supportato.
Windows 8.1 (x64) | Supportato.
Windows 8 (x64) | Supportato.
Windows 7 (x64) con SP1 e versioni successive | Dalla versione [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) dell'estensione del servizio Mobility per le macchine virtuali di Azure, è necessario installare un aggiornamento dello stack di manutenzione di Windows [(SSU)](https://support.microsoft.com/help/4490628) e [SHA-2](https://support.microsoft.com/help/4474419) nei computer che eseguono Windows 7 con SP1.  SHA-1 non è supportato da settembre 2019 e se la firma del codice SHA-2 non è abilitata l'estensione dell'agente non installerà/aggiorna come previsto.. Ulteriori informazioni [sull'aggiornamento e sui requisiti SHA-2](https://aka.ms/SHA-2KB).



#### <a name="linux"></a>Linux

**Sistema operativo** | **Dettagli**
--- | ---
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 8.0, 8.1
Server Ubuntu 14.04 LTS | [Versioni del kernel supportate](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Server Ubuntu 16.04 LTS | [Versioni del kernel supportate](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> I server Ubuntu che usano l'autenticazione basata su password e l'accesso e il pacchetto cloud-init per configurare le macchine virtuali cloud potrebbero avere l'accesso basato su password disabilitato in caso di failover (a seconda della configurazione cloudinit). L'accesso basato su password può essere riabilitato nella macchina virtuale reimpostando la password dal menu Impostazioni > di > di supporto (della macchina virtuale di cui è stato eseguito il failover nel portale di Azure.
Ubuntu 18.04 LTS Server | [Versioni del kernel supportate](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | [Versioni del kernel supportate](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Versioni del kernel supportate](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(Versioni del kernel supportate)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15 e 15 SP1. [(Versioni del kernel supportate)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> L'aggiornamento dei computer di replica da SP3 a SP4 non è supportato. Se un computer replicato è stato aggiornato, è necessario disabilitare la replica e riabilitarla dopo l'aggiornamento.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7.4, 7.5, 7.5, 7.6, [7.77](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Esecuzione del kernel compatibile con Red Hat o di Unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5)


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel Ubuntu supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione kernel** |
--- | --- | --- |
14.04 LTS | 9.32| 3.13.0-24-generico a 3.13.0-170-generic,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generico a 4.4.0-148-generico,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
14.04 LTS | 9.31 | 3.13.0-24-generico a 3.13.0-170-generic,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generico a 4.4.0-148-generico,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
14.04 LTS | 9.30 | 3.13.0-24-generico a 3.13.0-170-generic,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generico a 4.4.0-148-generico,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
14.04 LTS | 9.29 | 3.13.0-24-generico a 3.13.0-170-generic,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generico a 4.4.0-148-generico,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
|||
16.04 LTS | 9.32 | 4.4.0-21-generico a 4.4.0-171-generico,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-generico a 4.15.0-74-generic<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-azure a 4.15.0-1066-azure|
16.04 LTS | 9.31 | 4.4.0-21-generico a 4.4.0-170-generico,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-generico a 4.15.0-72-generico<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-azure a 4.15.0-1063-azure|
16.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.4.0-21-generico a 4.4.0-166-generico,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-generico a 4.15.0-66-generico<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-azure a 4.15.0-1061-azure|
16.04 LTS | 9.29 | 4.4.0-21-generico a 4.4.0-164-generico,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-generico a 4.15.0-64-generic<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-azure a 4.15.0-1059-azure|
|||
18.04 LTS | 9.32| 4.15.0-20-generico a 4.15.0-74-generic </br> 4.18.0-13-generico a 4.18.0-25-generico </br> 5.0.0-15-generico a 5.0.0-37-generico </br> 5.3.0-19-generico a 5.3.0-24-generic </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1028-azure </br> 5.3.0-1007-azure a 5.3.0-1009-azure|
18.04 LTS | 9.31| 4.15.0-20-generico a 4.15.0-72-generico </br> 4.18.0-13-generico a 4.18.0-25-generico </br> 5.0.0-15-generico a 5.0.0-37-generico </br> 5.3.0-19-generico a 5.3.0-24-generic </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1025-azure </br> 5.3.0-1007-azure|
18.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.15.0-20-generico a 4.15.0-66-generico </br> 4.18.0-13-generico a 4.18.0-25-generico </br> 5.0.0-15-generico a 5.0.0-32-generic </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1023-azure|
18.04 LTS | [9.29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | 4.15.0-20-generico a 4.15.0-64-generic </br> 4.18.0-13-generico a 4.18.0-25-generico </br> 5.0.0-15-generico a 5.0.0-29-generico </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1020-azure|


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel Debian supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione kernel** |
--- | --- | --- |
Debian 7 | 9.28,9.29,9.30,9.31 | Da 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.29,9.30,9.31 | da 4.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0.0.bpo.4-amd64 a 4.9.0-0.bpo.11-amd64 |
Debian 8 | 9.28 | da 3.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0.0.bpo.4-amd64 a 4.9.0.0.bpo.9-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel SUSE Linux Enterprise Server 12 supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.32 | Sono supportati tutti i [kernel SUSE 12 SP1,SP2,SP3,SP4,](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) di stock.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.34-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.31 | Sono supportati tutti i [kernel SUSE 12 SP1,SP2,SP3,SP4,](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) di stock.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.30 | Sono supportati tutti i [kernel SUSE 12 SP1,SP2,SP3,SP4,](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) di stock.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.29 | Sono supportati tutti i [kernel SUSE 12 SP1,SP2,SP3,SP4,](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) di stock.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.23-azure  |

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel di SUSE Linux Enterprise Server 15 supportate per le macchine virtuali di AzureSupported SUSE Linux Enterprise Server 15 kernel versions for Azure virtual machines

**Versione** | **Versione del servizio Mobility** | **Versione kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 e 15 SP1 | 9.32 | Sono supportati tutti i kernel SUSE 15 e 15.</br></br> 4.12.14-5.5-azure a 4.12.14-8.22-azure |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Computer replicati - File system/archiviazione guest Linux

* File system: ext3, ext4, XFS, BTRFS
* Gestore volumi: LVM2
* Software con percorsi multipli: mapper dispositivi


## <a name="replicated-machines---compute-settings"></a>Computer replicati - Impostazioni di calcolo

**Impostazione** | **Supporto** | **Dettagli**
--- | --- | ---
Dimensione | Macchine virtuali di Azure di qualsiasi dimensione con almeno 2 core CPU e 1 GB di RAM | Verificare le [dimensioni delle macchine virtuali in Azure](../virtual-machines/windows/sizes.md).
Set di disponibilità | Supportato | Se si abilita la replica per una macchina virtuale di Azure con le opzioni predefinite, viene creato automaticamente un set di disponibilità in base alle impostazioni dell'area di origine. È possibile modificare queste impostazioni.
Zone di disponibilità | Supportato |
Vantaggio Hybrid Use (HUB) | Supportato | Se la macchina virtuale di origine dispone di una licenza HUB abilitata, anche la macchina virtuale di failover o il failover di test userà la licenza HUB.
set di scalabilità di macchine virtuali | Non supportate |
Immagini della raccolta di Azure - Pubblicate da Microsoft | Supportato | Supportate se la macchina virtuale viene eseguita in un sistema operativo supportato.
Immagini della raccolta di Azure - Pubblicate da terze parti | Supportato | Supportate se la macchina virtuale viene eseguita in un sistema operativo supportato.
Immagini personalizzate - Pubblicate da terze parti | Supportato | Supportate se la macchina virtuale viene eseguita in un sistema operativo supportato.
Macchine virtuali migrate tramite Site Recovery | Supportato | Se una VM VMware o un computer fisico è stato migrato ad Azure tramite Site Recovery, è necessario disinstallare la versione precedente del servizio di mobilità in esecuzione nel computer e riavviare il computer prima di eseguirne la replica in un'altra area di Azure.
Criteri RBAC | Non supportate | I criteri di controllo di accesso basato sui ruoli nelle macchine virtuali non vengono replicati nella macchina virtuale di failover nell'area di destinazione.
Estensioni | Non supportate | Le estensioni non vengono replicate nella macchina virtuale di failover nell'area di destinazione. Deve essere installato manualmente dopo il failover.

## <a name="replicated-machines---disk-actions"></a>Computer replicati - Azioni del disco

**Azione** | **Dettagli**
-- | ---
Ridimensionamento del disco nella macchina virtuale replicata | Supportato nella macchina virtuale di origine prima del failover. Non è necessario disabilitare/riattivare la replica.<br/><br/> Se si modifica la macchina virtuale di origine dopo il failover, le modifiche non vengono acquisite.<br/><br/> Se si modificano le dimensioni del disco nella macchina virtuale di Azure dopo il failover, le modifiche non vengono acquisite da Site Recovery e il failback avverrà alla dimensione originale della macchina virtuale.
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
Dimensione massima del disco dati | 8192 GB per i dischi gestiti<br></br>4095 GB per dischi non gestiti|
Dimensione minima del disco dati | Nessuna restrizione per i dischi non gestiti. 2 GB per i dischi gestiti |
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
Crittografia inattivi (CMK) | Supportato | Entrambe le chiavi Software e HSM sono supportate per i dischi gestiti
Crittografia dischi di Azure (ADE) per sistema operativo Windows | Supportato per le macchine virtuali con dischi gestiti. | Le macchine virtuali che usano dischi non gestiti non sono supportate. <br/><br/> Le chiavi protette da HSM non sono supportate. |
Crittografia dischi di Azure (ADE) per sistema operativo Linux | Supportato per le macchine virtuali con dischi gestiti. | Le macchine virtuali che usano dischi non gestiti non sono supportate. <br/><br/> Le chiavi protette da HSM non sono supportate. |
Aggiunta a caldo    | Supportato | L'abilitazione della replica per un disco dati aggiunto a una macchina virtuale di Azure replicata è supportata per le macchine virtuali che usano dischi gestiti.
Disco di rimozione a caldo    | Non supportate | Se si rimuove il disco dati nella macchina virtuale, è necessario disabilitare la replica e abilitare nuovamente la replica per la macchina virtuale.
Esclusione disco | Supporto. È necessario usare [PowerShell](azure-to-azure-exclude-disks.md) per configurare. |    I dischi temporanei sono esclusi per impostazione predefinita.
Spazi di archiviazione diretta  | Supportato per i punti di ripristino coerenti con l'arresto anomalo. I punti di ripristino coerenti con l'applicazione non sono supportati. |
File server di scalabilità orizzontale  | Supportato per i punti di ripristino coerenti con l'arresto anomalo. I punti di ripristino coerenti con l'applicazione non sono supportati. |
DRBD | I dischi che fanno parte di un'installazione DRBD non sono supportati. |
Archiviazione con ridondanza locale | Supportato |
Archiviazione con ridondanza geografica | Supportato |
RA-GRS | Supportato |
ZRS | Non supportate |
Archiviazione ad accesso frequente e sporadico | Non supportate | I dischi delle macchine virtuali non sono supportati per l'archiviazione ad accesso frequente e sporadico
Firewall di Archiviazione di Azure per reti virtuali  | Supportato | Se limita l'accesso alla rete virtuale agli account di archiviazione, abilitare [Consenti servizi Microsoft attendibili](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Account di archiviazione V2 generico (livelli di accesso frequente e sporadico) | Supportato | Aumento sostanziale dei costi delle transazioni rispetto agli account di archiviazione V1 generici
Generazione 2 (avvio UEFI) | Supportato

>[!IMPORTANT]
> Per evitare problemi di prestazioni, assicurarsi di seguire gli obiettivi di scalabilità e prestazioni del disco vm per macchine virtuali Linux o [Windows.To](../virtual-machines/windows/disk-scalability-targets.md) avoid performance issues, make sure that you follow VM disk scalability and performance targets for [Linux](../virtual-machines/linux/disk-scalability-targets.md) or Windows VMs. Se si usano le impostazioni predefinite, Site Recovery crea i dischi e gli account di archiviazione necessari, in base alla configurazione di origine. Se si personalizzano e si selezionano impostazioni personalizzate, seguire gli obiettivi di scalabilità e prestazioni del disco per le macchine virtuali di origine.

## <a name="limits-and-data-change-rates"></a>Limiti e tassi di modifica dei dati

Nella tabella seguente sono riepilogati i limiti di Site Recovery.

- Questi limiti si basano sui nostri test, ma ovviamente non coprono tutte le possibili combinazioni di I/O delle applicazioni.
- I risultati effettivi possono variare in base alla combinazione di I/O dell'app.
- Esistono due limiti da considerare, per ogni varianza dei dati del disco e per varianza dei dati della macchina virtuale.

**Obiettivo di archiviazione** | **I/O medio su disco di origine** |**Media scissioni dei dati del disco di origine** | **Variazione totale dei dati del disco di origine al giorno**
---|---|---|---
Archiviazione standard | 8 KB    | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 8 KB    | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 16 KB | 4 MB/s |    336 GB per disco
Disco P10 o P15 Premium | 32 KB o superiori | 8 MB/s | 672 GB per disco
Disco P20, P30, P40 o P50 Premium | 8 KB    | 5 MB/s | 421 GB per disco
Disco P20, P30, P40 o P50 Premium | 16 KB o superiori |20 MB/s | 1684 GB per disco

## <a name="replicated-machines---networking"></a>Computer replicati - Reti
**Impostazione** | **Supporto** | **Dettagli**
--- | --- | ---
NIC | Numero massimo supportato per una specifica dimensione di macchina virtuale di Azure | Vengono create schede di interfaccia di rete contestualmente alla creazione della VM durante il failover.<br/><br/> Il numero di schede di interfaccia di rete sulla macchina virtuale di failover dipende dal numero di schede di rete sulla VM di origine quando è stata abilitata la replica. L'aggiunta o la rimozione di una scheda di interfaccia di rete dopo l'abilitazione della replica non influisce sul numero di schede sulla VM replicata dopo il failover. Si noti inoltre che l'ordine delle schede di interfaccia di rete dopo il failover non è garantito che sia lo stesso dell'ordine originale.
Servizio di bilanciamento del carico Internet | Supportato | Associare il servizio di bilanciamento del carico preconfigurato tramite uno script di Automazione di Azure in un piano di ripristino.
Servizio di bilanciamento del carico interno | Supportato | Associare il servizio di bilanciamento del carico preconfigurato tramite uno script di Automazione di Azure in un piano di ripristino.
Indirizzo IP pubblico | Supportato | Associare un indirizzo IP pubblico esistente alla scheda di interfaccia di rete. In alternativa, creare un indirizzo IP pubblico e associarlo alla scheda di interfaccia di rete tramite uno script di Automazione di Azure in un piano di ripristino.
Gruppo di sicurezza di rete o scheda di interfaccia di rete | Supportato | Associare il gruppo di sicurezza di rete alla scheda di interfaccia di rete tramite uno script di Automazione di Azure in un piano di ripristino.
Gruppo di sicurezza di rete o subnet | Supportato | Associare il gruppo di sicurezza di rete alla subnet tramite uno script di Automazione di Azure in un piano di ripristino.
Indirizzo IP riservato (statico) | Supportato | Se la scheda di rete sulla macchina virtuale di origine ha un indirizzo IP statico e la subnet di destinazione dispone dello stesso IP, viene assegnato alla macchina virtuale di failover.<br/><br/> Se la subnet di destinazione non ha lo stesso indirizzo IP, uno degli IP disponibili nella subnet viene riservato per la macchina virtuale.<br/><br/> È inoltre possibile specificare un indirizzo IP fisso e una subnet in **Elementi** > replicati**Impostazioni** > **di calcolo e** > **Interfacce**di rete .
Indirizzo IP dinamico | Supportato | Se la scheda di interfaccia di rete nella macchina virtuale di origine ha indirizzi IP dinamici, anche la scheda sulla macchina virtuale di failover è dinamica per impostazione predefinita.<br/><br/> Se necessario, è possibile modificare questa opzione in un indirizzo IP fisso.
Più indirizzi IP | Non supportate | Quando si esegue il failover di una macchina virtuale con più indirizzi IP, viene mantenuto solo l'indirizzo IP primario della scheda di interfaccia di rete nell'area di origine. Per assegnare più indirizzi IP, è possibile aggiungere macchine virtuali a un piano di [ripristino](recovery-plan-overview.md) e collegare uno script per assegnare indirizzi IP aggiuntivi al piano oppure è possibile apportare la modifica manualmente o con uno script dopo il failover.
Gestione traffico     | Supportato | È possibile preconfigurare Gestione traffico in modo che il traffico venga regolarmente indirizzato all'endpoint nell'area di origine e all'endpoint nell'area di destinazione in caso di failover.
DNS di Azure | Supportato |
DNS personalizzato    | Supportato |
Proxy non autenticato | Supportato | [Altre informazioni](site-recovery-azure-to-azure-networking-guidance.md)
Proxy autenticato | Non supportate | Se la macchina virtuale utilizza un proxy autenticato per la connettività in uscita, non può essere replicata tramite Azure Site Recovery.
Connessione VPN da sito a sito a locale<br/><br/>(con o senza ExpressRoute)| Supportato | Assicurarsi che le UDR e i gruppi di sicurezza di rete siano configurati in modo che il traffico di Site Recovery non venga instradato in locale. [Altre informazioni](site-recovery-azure-to-azure-networking-guidance.md)
Connessione da rete virtuale a rete virtuale    | Supportato | [Altre informazioni](site-recovery-azure-to-azure-networking-guidance.md)
Endpoint servizio di rete virtuale | Supportato | Se si limita l'accesso alla rete virtuale agli account di archiviazione, assicurarsi che ai servizi Microsoft attendibili sia consentito l'accesso all'account di archiviazione.
Rete accelerata | Supportato | La rete accelerata deve essere abilitata su una macchina virtuale di origine. [Altre informazioni](azure-vm-disaster-recovery-with-accelerated-networking.md)



## <a name="next-steps"></a>Passaggi successivi
- Leggere [le indicazioni sulla rete](site-recovery-azure-to-azure-networking-guidance.md) per la replica delle macchine virtuali di Azure.Read networking guidance for replicating Azure VMs.
- Distribuire il ripristino di emergenza [replicando le macchine virtuali di Azure](site-recovery-azure-to-azure.md).
