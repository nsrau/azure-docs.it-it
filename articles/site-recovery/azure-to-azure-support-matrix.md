---
title: Matrice di supporto per il ripristino di emergenza delle macchine virtuali di Azure con Azure Site Recovery
description: Informazioni di riepilogo sul supporto del ripristino di emergenza delle macchine virtuali di Azure in un'area secondaria con Azure Site Recovery.
ms.topic: article
ms.date: 01/10/2020
ms.author: raynew
ms.openlocfilehash: 970046f000a844ce6ee7207ad1bb33f4a327a63d
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848108"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Matrice di supporto per il ripristino di emergenza delle macchine virtuali di Azure tra aree di Azure

Questo articolo offre informazioni di riepilogo sul supporto e i prerequisiti per il ripristino di emergenza di macchine virtuali di Azure da un'area di Azure a un'altra usando il servizio [Azure Site Recovery](site-recovery-overview.md).


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
**Spostamento di insiemi di credenziali tra gruppi di risorse** | Non supportate
**Spostamento di risorse di calcolo, archiviazione e rete tra gruppi di risorse** | Non supportato.<br/><br/> Se si sposta una macchina virtuale o componenti associati come archiviazione o rete dopo la replica della VM, è necessario disabilitare la replica e riabilitarla per la VM.
**Replica di macchine virtuali di Azure da una sottoscrizione a un'altra per il ripristino di emergenza** | Supportate nello stesso tenant di Azure Active Directory.
**Eseguire la migrazione di macchine virtuali tra aree all'interno dei cluster geografici supportati (all'interno e tra sottoscrizioni)** | Supportate nello stesso tenant di Azure Active Directory.
**Migrazione di macchine virtuali all'interno della stessa area** | Non supportato.

## <a name="region-support"></a>Supporto di area

È possibile eseguire la replica e il ripristino di macchine virtuali tra due aree qualsiasi all'interno dello stesso cluster geografico. I cluster geografici vengono definiti tenendo presente la latenza e la sovranità dei dati.


**Cluster geografico** | **Aree di Azure**
-- | --
America | Canada orientale, Canada centrale, Stati Uniti centro-meridionali, Stati Uniti centro-occidentali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti centrali, Stati Uniti centro-settentrionali
Europa | Regno Unito occidentale, Regno Unito meridionale, Europa settentrionale, Europa occidentale, Sudafrica occidentale, Sudafrica settentrionale, Norvegia orientale, Norvegia occidentale
Asia | India meridionale, India centrale, India occidentale, Asia sud-orientale, Asia orientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale
Australia    | Australia orientale, Australia sud-orientale, Australia centrale, Australia centrale 2
Azure Government    | US GOV Virginia, US GOV Iowa, US GOV Arizona, US GOV Texas, US DOD East, US DOD Central
Germania    | Germania centrale, Germania nord-orientale
Cina | Cina orientale, Cina settentrionale, Cina settentrionale2, Cina orientale 2
Aree con restrizioni riservate per il ripristino di emergenza in un paese |Germania settentrionale riservata per Germania centro-occidentale, Svizzera occidentale riservata per Svizzera settentrionale, Francia meridionale riservata per Francia centrale, Emirati Arabi Uniti centrali limitata per i clienti Emirati Arabi Uniti settentrionali

>[!NOTE]
>
> - Per l'area **Brasile meridionale** è possibile eseguire la replica e il failover nelle aree seguenti: Stati Uniti centro-meridionali, Stati Uniti centro-occidentali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali, Stati Uniti occidentali 2 e Stati Uniti centro-settentrionali.
> - L'area Brasile meridionale può essere usata solo come area di origine da cui è possibile eseguire la replica delle macchine virtuali usando Site Recovery. Non può essere usata come area di destinazione. Ciò è dovuto a problemi di latenza causati dalle distanze geografiche. Si noti che se si esegue il failover dall'area Brasile meridionale come area di origine a una destinazione, è supportato il failback nell'area Brasile meridionale dall'area di destinazione.
> - È possibile lavorare all'interno delle aree per le quali si dispone dell'accesso appropriato.
> - Se l'area in cui si vuole creare un insieme di credenziali non viene visualizzata, assicurarsi che la sottoscrizione abbia l'accesso per la creazione di risorse nell'area.
> - Se un'area non viene visualizzata all'interno di un cluster geografico quando si abilita la replica, assicurarsi che la sottoscrizione abbia le autorizzazioni per la creazione di macchine virtuali nell'area.



## <a name="cache-storage"></a>Archiviazione cache

Questa tabella riepiloga il supporto per l'account di archiviazione della cache usato da Site Recovery durante la replica.

**Impostazione** | **Supporto** | **Dettagli**
--- | --- | ---
Account di archiviazione V2 di utilizzo generico (livelli di accesso frequente e sporadico) | Supportato | L'uso di GPv2 non è consigliato poiché i costi di transazione per V2 sono molto più elevati rispetto agli account di archiviazione V1.
Archiviazione Premium | Non supportate | Gli account di archiviazione standard vengono usati per l'archiviazione della cache per ottimizzare i costi.
Firewall di Archiviazione di Azure per reti virtuali  | Supportato | Se si usano account di archiviazione cache o di archiviazione di destinazione abilitati per il firewall, assicurarsi di selezionare ["Consenti ai servizi Microsoft attendibili"](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).<br></br>Assicurarsi anche di consentire l'accesso ad almeno una subnet della rete virtuale di origine.


## <a name="replicated-machine-operating-systems"></a>Sistemi operativi di computer replicati

Site Recovery supporta la replica di macchine virtuali di Azure che eseguono i sistemi operativi elencati in questa sezione. Si noti che se un computer che esegue la replica viene successivamente aggiornato o sottoposto a downgrade a un kernel principale diverso, è necessario disabilitare la replica e riabilitarla dopo l'aggiornamento.

### <a name="windows"></a>Windows


**Sistema operativo** | **Dettagli**
--- | ---
Windows Server 2019 | Supportato per Server Core, Server con Esperienza Desktop.
Windows Server 2016  | Supportato per Server Core, Server con Esperienza Desktop.
Windows Server 2012 R2 | Supportato.
Windows Server 2012 | Supportato.
Windows Server 2008 R2 con SP1/SP2 | Supportato.<br/><br/> Dalla versione [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) dell'estensione del servizio di mobilità per le macchine virtuali di Azure, è necessario installare un [aggiornamento dello stack di manutenzione (SSU)](https://support.microsoft.com/help/4490628) e un [aggiornamento SHA-2](https://support.microsoft.com/help/4474419) nei computer che eseguono Windows Server 2008 R2 SP1/SP2.  SHA-1 non è supportato da settembre 2019 e se la firma del codice SHA-2 non è abilitata, l'estensione dell'agente non verrà installata o aggiornata come previsto. Altre informazioni sull'[aggiornamento e sui requisiti di SHA-2](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Supportato.
Windows 8.1 (x64) | Supportato.
Windows 8 (x64) | Supportato.
Windows 7 (x64) con SP1 e versioni successive | Dalla versione [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) dell'estensione del servizio di mobilità per le macchine virtuali di Azure, è necessario installare un [aggiornamento dello stack di manutenzione (SSU)](https://support.microsoft.com/help/4490628) e un [aggiornamento SHA-2](https://support.microsoft.com/help/4474419) nei computer che eseguono Windows 7 con SP1.  SHA-1 non è supportato da settembre 2019 e se la firma del codice SHA-2 non è abilitata, l'estensione dell'agente non verrà installata o aggiornata come previsto. Altre informazioni sull'[aggiornamento e sui requisiti di SHA-2](https://aka.ms/SHA-2KB).



#### <a name="linux"></a>Linux

**Sistema operativo** | **Dettagli**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 8.0, 8.1
Server Ubuntu 14.04 LTS | [Versioni del kernel supportate](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Server Ubuntu 16.04 LTS | [Versioni del kernel supportate](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Per i server Ubuntu che usano l'accesso e l'autenticazione basati su password e il pacchetto cloud-init per configurare le macchine virtuali nel cloud, è possibile che l'accesso basato su password venga disabilitato in caso di failover, a seconda della configurazione di cloudinit. L'accesso basato su password può essere riabilitato nella macchina virtuale reimpostando la password da Supporto > Risoluzione dei problemi > menu Impostazioni della macchina virtuale con failover nel portale di Azure.
Server Ubuntu 18.04 LTS | [Versioni del kernel supportate](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | [Versioni del kernel supportate](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Versioni del kernel supportate](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(Versioni del kernel supportate)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15 e 15 SP1. [(Versioni del kernel supportate)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> L'aggiornamento dei computer di replica da SP3 a SP4 non è supportato. Se un computer replicato è stato aggiornato, è necessario disabilitare la replica e riabilitarla dopo l'aggiornamento.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Con kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3, 4 e 5 (UEK3, UEK4, UEK5)


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel Ubuntu supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
14.04 LTS | 9.32| Da 3.13.0-24 generica a 3.13.0-170 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-148 generica<br/>Da 4.15.0-1023 Azure a 4.15.0-1045 Azure |
14.04 LTS | 9.31 | Da 3.13.0-24 generica a 3.13.0-170 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-148 generica<br/>Da 4.15.0-1023 Azure a 4.15.0-1045 Azure |
14.04 LTS | 9.30 | Da 3.13.0-24 generica a 3.13.0-170 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-148 generica<br/>Da 4.15.0-1023 Azure a 4.15.0-1045 Azure |
14.04 LTS | 9.29 | Da 3.13.0-24 generica a 3.13.0-170 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-148 generica<br/>Da 4.15.0-1023 Azure a 4.15.0-1045 Azure |
|||
16.04 LTS | 9.32 | Da 4.4.0-21 generica a 4.4.0-171 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>Da 4.15.0-13 generica a 4.15.0-74 generica<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>Da 4.15.0-1012 Azure a 4.15.0-1066 Azure|
16.04 LTS | 9.31 | Da 4.4.0-21 generica a 4.4.0-170 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>Da 4.15.0-13 generica a 4.15.0-72 generica<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>Da 4.15.0-1012 Azure a 4.15.0-1063 Azure|
16.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | Da 4.4.0-21 generica a 4.4.0-166 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>Da 4.15.0-13 generica a 4.15.0-66 generica<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>Da 4.15.0-1012 Azure a 4.15.0-1061 Azure|
16.04 LTS | 9.29 | Da 4.4.0-21 generica a 4.4.0-164 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>Da 4.15.0-13 generica a 4.15.0-64 generica<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>Da 4.15.0-1012 Azure a 4.15.0-1059 Azure|
|||
18.04 LTS | 9.32| Da 4.15.0-20 generica a 4.15.0-74 generica </br> Da 4.18.0-13 generica a 4.18.0-25 generica </br> Da 5.0.0-15 generica a 5.0.0-37 generica </br> Da 5.3.0-19 generica a 5.3.0-24 generica </br> Da 4.15.0-1009 Azure a 4.15.0-1037 Azure </br> Da 4.18.0-1006 Azure a 4.18.0-1025 Azure </br> Da 5.0.0-1012 Azure a 5.0.0-1028 Azure </br> Da 5.3.0-1007 Azure a 5.3.0-1009 Azure|
18.04 LTS | 9.31| Da 4.15.0-20 generica a 4.15.0-72 generica </br> Da 4.18.0-13 generica a 4.18.0-25 generica </br> Da 5.0.0-15 generica a 5.0.0-37 generica </br> Da 5.3.0-19 generica a 5.3.0-24 generica </br> Da 4.15.0-1009 Azure a 4.15.0-1037 Azure </br> Da 4.18.0-1006 Azure a 4.18.0-1025 Azure </br> Da 5.0.0-1012 Azure a 5.0.0-1025 Azure </br> 5.3.0-1007 Azure|
18.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | Da 4.15.0-20 generica a 4.15.0-66 generica </br> Da 4.18.0-13 generica a 4.18.0-25 generica </br> Da 5.0.0-15 generica a 5.0.0-32 generica </br> Da 4.15.0-1009 Azure a 4.15.0-1037 Azure </br> Da 4.18.0-1006 Azure a 4.18.0-1025 Azure </br> Da 5.0.0-1012 Azure a 5.0.0-1023 Azure|
18.04 LTS | [9.29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | Da 4.15.0-20 generica a 4.15.0-64 generica </br> Da 4.18.0-13 generica a 4.18.0-25 generica </br> Da 5.0.0-15 generica a 5.0.0-29 generica </br> Da 4.15.0-1009 Azure a 4.15.0-1037 Azure </br> Da 4.18.0-1006 Azure a 4.18.0-1025 Azure </br> Da 5.0.0-1012 Azure a 5.0.0-1020 Azure|


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel Debian supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
Debian 7 | 9.28, 9.29, 9.30, 9.31 | Da 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.29, 9.30, 9.31 | Da 3.16.0-4-amd64 a 3.16.0-10-amd64, da 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.11-amd64 |
Debian 8 | 9.28 | Da 3.16.0-4-amd64 a 3.16.0-10-amd64, da 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.9-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel SUSE Linux Enterprise Server 12 supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9.32 | Sono supportati tutti i kernel stock SUSE 12 SP1, SP2, SP3, SP4.</br></br> Da 4.4.138-4.7 Azure a 4.4.180-4.31 Azure</br>Da 4.12.14-6.3 Azure a 4.12.14-6.34 Azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9.31 | Sono supportati tutti i kernel stock SUSE 12 SP1, SP2, SP3, SP4.</br></br> Da 4.4.138-4.7 Azure a 4.4.180-4.31 Azure</br>Da 4.12.14-6.3 Azure a 4.12.14-6.29 Azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9.30 | Sono supportati tutti i kernel stock SUSE 12 SP1, SP2, SP3, SP4.</br></br> Da 4.4.138-4.7 Azure a 4.4.180-4.31 Azure</br>Da 4.12.14-6.3 Azure a 4.12.14-6.29 Azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9.29 | Sono supportati tutti i kernel stock SUSE 12 SP1, SP2, SP3, SP4.</br></br> Da 4.4.138-4.7 Azure a 4.4.180-4.31 Azure</br>Da 4.12.14-6.3 Azure a 4.12.14-6.23 Azure  |

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel SUSE Linux Enterprise Server 15 supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 e 15 SP1 | 9.32 | Per impostazione predefinita, sono supportati tutti i [kernel stock SUSE 15 e 15](https://www.suse.com/support/kb/doc/?id=000019587).</br></br> Da 4.12.14-5.5 Azure a 4.12.14-8.22 Azure |


## <a name="replicated-machines---linux-file-systemguest-storage"></a>Computer replicati - File system/archiviazione guest Linux

* File system: ext3, ext4, XFS, BTRFS
* Gestore volumi: LVM2
* Software con percorsi multipli: Mapper dispositivi


## <a name="replicated-machines---compute-settings"></a>Computer replicati - Impostazioni di calcolo

**Impostazione** | **Supporto** | **Dettagli**
--- | --- | ---
Dimensione | Macchine virtuali di Azure di qualsiasi dimensione con almeno 2 core CPU e 1 GB di RAM | Verificare le [dimensioni delle macchine virtuali in Azure](../virtual-machines/windows/sizes.md).
Set di disponibilità | Supportato | Se si abilita la replica per una macchina virtuale di Azure con le opzioni predefinite, viene automaticamente creato un set di disponibilità in base alle impostazioni dell'area di origine. È possibile modificare queste impostazioni.
Zone di disponibilità | Supportato |
Vantaggio Hybrid Use (HUB) | Supportato | Se la macchina virtuale di origine dispone di una licenza HUB abilitata, anche la macchina virtuale di failover o il failover di test userà la licenza HUB.
set di scalabilità di macchine virtuali | Non supportate |
Immagini della raccolta di Azure - Pubblicate da Microsoft | Supportato | Supportate se la macchina virtuale viene eseguita in un sistema operativo supportato.
Immagini della raccolta di Azure - Pubblicate da terze parti | Supportato | Supportate se la macchina virtuale viene eseguita in un sistema operativo supportato.
Immagini personalizzate - Pubblicate da terze parti | Supportato | Supportate se la macchina virtuale viene eseguita in un sistema operativo supportato.
Macchine virtuali migrate tramite Site Recovery | Supportato | Se una VM VMware o un computer fisico è stato migrato ad Azure tramite Site Recovery, è necessario disinstallare la versione precedente del servizio di mobilità in esecuzione nel computer e riavviare il computer prima di eseguirne la replica in un'altra area di Azure.
Criteri di controllo degli accessi in base al ruolo | Non supportate | I criteri di controllo degli accessi in base al ruolo nelle macchine virtuali non vengono replicati nella macchina virtuale di failover nell'area di destinazione.
Estensioni | Non supportate | Le estensioni non vengono replicate nella macchina virtuale di failover nell'area di destinazione. L'estensione deve essere installata manualmente dopo il failover.
Gruppi di posizionamento di prossimità | Non supportate | Non è possibile proteggere le macchine virtuali che si trovano all'interno di un gruppo di posizionamento di prossimità con Site Recovery.


## <a name="replicated-machines---disk-actions"></a>Computer replicati - Azioni del disco

**Azione** | **Dettagli**
-- | ---
Ridimensionamento del disco nella macchina virtuale replicata | Supportata nella macchina virtuale di origine prima del failover. Non è necessario disabilitare e riabilitare la replica.<br/><br/> Se si modifica la macchina virtuale di origine dopo il failover, le modifiche non vengono acquisite.<br/><br/> Se si modificano le dimensioni del disco nella macchina virtuale di Azure dopo il failover, le modifiche non vengono acquisite da Site Recovery e il failback si verificherà sulle dimensioni originali della macchina virtuale.
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
Dimensione massima del disco dati | 8192 GB per i dischi gestiti<br></br>4095 GB per i dischi non gestiti|
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
Crittografia dei dati inattivi (CMK) | Supportato | Sono supportate le chiavi s e le chiavi dei moduli di protezione hardware (HSM) per i dischi gestiti
Crittografia dischi di Azure (ADE) per sistema operativo Windows | Supportato per le macchine virtuali con dischi gestiti. | Le macchine virtuali che usano dischi non gestiti non sono supportate. <br/><br/> Le chiavi con protezione HSM non sono supportate. <br/><br/> La crittografia dei singoli volumi in un unico disco non è supportata. |
Crittografia dischi di Azure (ADE) per sistema operativo Linux | Supportato per le macchine virtuali con dischi gestiti. | Le macchine virtuali che usano dischi non gestiti non sono supportate. <br/><br/> Le chiavi con protezione HSM non sono supportate. <br/><br/> La crittografia dei singoli volumi in un unico disco non è supportata. |
Aggiunta a caldo    | Supportato | L'abilitazione della replica per un disco dati aggiunto a una macchina virtuale di Azure replicata è supportata per le macchine virtuali che usano dischi gestiti. <br/><br/> È possibile aggiungere a caldo un solo disco alla volta a una macchina virtuale di Azure. L'aggiunta parallela di più dischi non è supportata. |
Rimozione a caldo del disco    | Non supportate | Se si rimuovono dischi dati nella macchina virtuale, è necessario disabilitare la replica e abilitarla nuovamente per la macchina virtuale.
Esclusione disco | Supportato. È necessario usare [PowerShell](azure-to-azure-exclude-disks.md) per la configurazione. |    I dischi temporanei sono esclusi per impostazione predefinita.
Spazi di archiviazione diretta  | Supportato per i punti di ripristino coerenti con l'arresto anomalo. I punti di ripristino coerenti con l'applicazione non sono supportati. |
File server di scalabilità orizzontale  | Supportato per i punti di ripristino coerenti con l'arresto anomalo. I punti di ripristino coerenti con l'applicazione non sono supportati. |
DRBD | I dischi che fanno parte di un'installazione DRBD non sono supportati. |
Archiviazione con ridondanza locale | Supportato |
Archiviazione con ridondanza geografica | Supportato |
RA-GRS | Supportato |
ZRS | Non supportate |
Archiviazione ad accesso frequente e sporadico | Non supportate | I dischi delle macchine virtuali non sono supportati per l'archiviazione ad accesso frequente e sporadico
Firewall di Archiviazione di Azure per reti virtuali  | Supportato | Se si limita l'accesso alla rete virtuale agli account di archiviazione, abilitare [Consenti servizi Microsoft attendibili](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Account di archiviazione V2 generico (livelli di accesso frequente e sporadico) | Supportato | Aumento sostanziale dei costi delle transazioni rispetto agli account di archiviazione V1 generici
Generazione 2 (avvio UEFI) | Supportato
Dischi NVMe | Non supportate

>[!IMPORTANT]
> Per evitare problemi di prestazioni, assicurarsi di osservare gli obiettivi di scalabilità e prestazioni del disco della macchina virtuale per le macchine virtuali [Linux](../virtual-machines/linux/disk-scalability-targets.md) o [Windows](../virtual-machines/windows/disk-scalability-targets.md). Se si usano le impostazioni predefinite, Site Recovery crea i dischi e gli account di archiviazione necessari in base alla configurazione di origine. Se si personalizzano e si selezionano impostazioni personalizzate, rispettare gli obiettivi di scalabilità e prestazioni dei dischi per le macchine virtuali.

## <a name="limits-and-data-change-rates"></a>Limiti e frequenza di modifica dei dati

La tabella seguente offre un riepilogo dei limiti di Site Recovery.

- Questi limiti sono basati sui test di Microsoft, ma non coprono tutte le possibili combinazioni di I/O delle applicazioni.
- I risultati effettivi possono variare in base alla combinazione di I/O dell'applicazione.
- È necessario considerare due limiti, la varianza dei dati del disco e la varianza dei dati della macchina virtuale.

**Destinazione di archiviazione** | **I/O medio del disco di origine** |**Varianza dati media disco di origine** | **Varianza dati totale giornaliera disco di origine**
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
NIC | Numero massimo supportato per una specifica dimensione di macchina virtuale di Azure | Vengono create schede di interfaccia di rete contestualmente alla creazione della VM durante il failover.<br/><br/> Il numero di schede di interfaccia di rete sulla macchina virtuale di failover dipende dal numero di schede di rete sulla VM di origine quando è stata abilitata la replica. L'aggiunta o la rimozione di una scheda di interfaccia di rete dopo l'abilitazione della replica non influisce sul numero di schede sulla VM replicata dopo il failover. Si noti anche che l'ordine delle schede di interfaccia di rete dopo il failover non corrisponde necessariamente all'ordine originale.
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
DNS personalizzato    | Supportato |
Proxy non autenticato | Supportato | [Altre informazioni](site-recovery-azure-to-azure-networking-guidance.md)
Proxy autenticato | Non supportate | Se la macchina virtuale utilizza un proxy autenticato per la connettività in uscita, non può essere replicata tramite Azure Site Recovery.
Connessione da sito a sito VPN a locale<br/><br/>(con o senza ExpressRoute)| Supportato | Verificare che le route definite dall'utente e i gruppi di sicurezza di rete siano configurati in modo che il traffico di Site Recovery non venga instradato in locale. [Altre informazioni](site-recovery-azure-to-azure-networking-guidance.md)
Connessione da rete virtuale a rete virtuale    | Supportato | [Altre informazioni](site-recovery-azure-to-azure-networking-guidance.md)
Endpoint servizio di rete virtuale | Supportato | Se si limita l'accesso alla rete virtuale agli account di archiviazione, assicurarsi che ai servizi Microsoft attendibili sia consentito l'accesso all'account di archiviazione.
Rete accelerata | Supportato | La rete accelerata deve essere abilitata su una macchina virtuale di origine. [Altre informazioni](azure-vm-disaster-recovery-with-accelerated-networking.md)
Palo Alto Network Appliance | Non supportate | Con appliance di terze parti, vengono spesso imposte restrizioni dal provider all'interno della macchina virtuale. Azure Site Recovery richiede che agente, estensioni e connettività in uscita siano disponibili. Tuttavia, l'appliance non consente la configurazione di alcuna attività in uscita all'interno della macchina virtuale.
IPv6  | Non supportate | Anche le configurazioni miste che includono IPv4 e IPv6 non sono supportate. Liberare la subnet dell'intervallo IPv6 prima di qualsiasi operazione di Site Recovery.



## <a name="next-steps"></a>Passaggi successivi
- Leggere il [materiale sussidiario per la rete](site-recovery-azure-to-azure-networking-guidance.md) per la replica delle macchine virtuali di Azure.
- Distribuire il ripristino di emergenza [replicando le macchine virtuali di Azure](site-recovery-azure-to-azure.md).
