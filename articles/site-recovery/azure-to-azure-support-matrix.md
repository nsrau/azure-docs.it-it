---
title: Matrice di supporto per il ripristino di emergenza delle macchine virtuali di Azure con Azure Site Recovery
description: Informazioni di riepilogo sul supporto del ripristino di emergenza delle macchine virtuali di Azure in un'area secondaria con Azure Site Recovery.
ms.topic: article
ms.date: 07/14/2020
ms.author: raynew
ms.openlocfilehash: b73a6b8c024cfa5ed7188bbf3fed2bbb7142a2a0
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186638"
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
Europa | Regno Unito occidentale, Regno Unito meridionale, Europa settentrionale, Europa occidentale, Sudafrica occidentale, Sudafrica settentrionale, Norvegia orientale, Norvegia occidentale, Francia centrale, Svizzera settentrionale
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
Firewall di Archiviazione di Azure per reti virtuali  | Supportato | Se si usano account di archiviazione cache o di archiviazione di destinazione abilitati per il firewall, assicurarsi di selezionare ["Consenti ai servizi Microsoft attendibili"](../storage/common/storage-network-security.md#exceptions).<br></br>Assicurarsi anche di consentire l'accesso ad almeno una subnet della rete virtuale di origine.


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
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6,[7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4564347/), [7,9](https://support.microsoft.com/help/4578241/), [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1, [8,2](https://support.microsoft.com/help/4570609/)
CentOS | 6,5, 6,6, 6,7, 6,8, 6,9, 6,10 </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7, [7,8](https://support.microsoft.com/help/4564347/), [7,9 versione pre-GA](https://support.microsoft.com/help/4578241/), 7,9 versione GA è supportata dalla patch per la correzione a caldo di 9,37 * * </br> 8,0, 8,1, [8,2](https://support.microsoft.com/en-us/help/4570609)
Server Ubuntu 14.04 LTS | Include il supporto per tutti i 14,04. versioni *x* ; [Versioni del kernel supportate](#supported-ubuntu-kernel-versions-for-azure-virtual-machines). 
Server Ubuntu 16.04 LTS | Include il supporto per tutti i 16,04. versioni *x* ; [Versione kernel supportata](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Per i server Ubuntu che usano l'accesso e l'autenticazione basati su password e il pacchetto cloud-init per configurare le macchine virtuali nel cloud, è possibile che l'accesso basato su password venga disabilitato in caso di failover, a seconda della configurazione di cloudinit. L'accesso basato su password può essere riabilitato nella macchina virtuale reimpostando la password da Supporto > Risoluzione dei problemi > menu Impostazioni della macchina virtuale con failover nel portale di Azure.
Server Ubuntu 18.04 LTS | Include il supporto per tutti i 18,04. versioni *x* ; [Versione kernel supportata](#supported-ubuntu-kernel-versions-for-azure-virtual-machines) |
Server Ubuntu 20,04 LTS | Include il supporto per tutti i 20,04. versioni *x* ; [Versione kernel supportata](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | Include il supporto per tutti i 7. versioni del [kernel supportate](#supported-debian-kernel-versions-for-azure-virtual-machines) per le versioni *x*
Debian 8 | Include il supporto per tutti gli 8. versioni del [kernel supportate](#supported-debian-kernel-versions-for-azure-virtual-machines) per le versioni *x*
Debian 9 | Include il supporto per 9,1 a 9,13. Debian 9,0 non è supportata. [Versioni del kernel supportate](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4, SP5  [(versioni del kernel supportate)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15, SP1, SP2[(versioni del kernel supportate)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> L'aggiornamento dei computer di replica da SP3 a SP4 non è supportato. Se un computer replicato è stato aggiornato, è necessario disabilitare la replica e riabilitarla dopo l'aggiornamento.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4573888/), [8,0](https://support.microsoft.com/help/4573888/), [8,1](https://support.microsoft.com/help/4573888/)  <br/> Con kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3, 4 e 5 (UEK3, UEK4, UEK5)<br/><br/>8.1<br/>In esecuzione su tutti i kernel UEK e RedHat kernel <= 3.10.0-1062. * sono supportati in [9,35](https://support.microsoft.com/help/4573888/) il supporto per il resto dei kernel RedHat è disponibile in [9,36](https://support.microsoft.com/help/4578241/)

* * Nota: per supportare i kernel Linux più recenti entro 15 giorni dal rilascio, Azure Site Recovery distribuisce patch di correzione a caldo oltre alla versione più recente dell'agente di mobilità. Questa correzione viene implementata tra due versioni principali. Per eseguire l'aggiornamento alla versione più recente dell'agente Mobility (inclusa patch per la correzione a caldo), seguire i passaggi descritti in [questo articolo](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure). Questa patch è attualmente implementata per gli agenti di mobilità usati in uno scenario di ripristino di emergenza di Azure in Azure.

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel Ubuntu supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
14.04 LTS | [9,34](https://support.microsoft.com/help/4570609), [9,35](https://support.microsoft.com/help/4573888/), [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/)| Da 3.13.0-24 generica a 3.13.0-170 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-148 generica<br/>Da 4.15.0-1023 Azure a 4.15.0-1045 Azure |
|||
16.04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | 4.4.0-21-generico per 4.4.0-190-generico,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-generico a 4.15.0-118-generico<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-Azure in 4.15.0-1096-Azure </br> 4.4.0-193-Generic, 4.15.0-120-Generic, 4.15.0-122-Generic, 4.15.0-1098-Azure fino a 9,38 patch per la correzione a caldo * *|
16.04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-generico a 4.4.0-189-generico,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>da 4.15.0-13-generico a 4.15.0-115-generico<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-Azure in 4.15.0-1093-Azure </br> 4.4.0-190-Generic, 4.15.0-117-Generic, 4.15.0-118-Generic, 4.15.0-1095-Azure, 4.15.0-1096-Azure 9,37 tramite patch di correzione a caldo * *|
16.04 LTS | [9,36](https://support.microsoft.com/help/4578241/)| 4.4.0-21-generico per 4.4.0-187-generico,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-generico a 4.15.0-112-generico<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-Azure in 4.15.0-1092-Azure |
16.04 LTS | [9,34](https://support.microsoft.com/help/4570609), [9,35](https://support.microsoft.com/help/4573888/) | da 4.4.0-21-generico a 4.4.0-184-generico<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>da 4.15.0-13-generico a 4.15.0-106-generico<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-Azure in 4.15.0-1089-Azure</br> 4.15.0-107-Generic, 4.4.0-185-Generic & 4.15.0-1091-Azure fino a 9,35 patch per la correzione a caldo * * |
|||
18.04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | 4.15.0-20-generico a 4.15.0-118-generico </br> Da 4.18.0-13 generica a 4.18.0-25 generica </br> 5.0.0-15-generico a 5.0.0-61-generico </br> 5.3.0-19-generico a 5.3.0-67-generico </br> 5.4.0-37-generico a 5.4.0-48-generico</br> 4.15.0-1009-Azure a 4.15.0-1096-Azure </br> Da 4.18.0-1006 Azure a 4.18.0-1025 Azure </br> 5.0.0-1012-Azure in 5.0.0-1036-Azure </br> 5.3.0-1007-da Azure a 5.3.0-1035-Azure </br> 5.4.0-1020-Azure a 5.4.0-1026-Azure </br> 4.15.0-121-Generic, 4.15.0-122-Generic, 5.0.0-62-Generic, 5.3.0-68-Generic, 5.4.0-51-Generic, 5.4.0-52-Generic, 4.15.0-1099-Azure, 5.4.0-1031-Azure tramite la patch per la correzione a caldo 9,38 * *|
18.04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.15.0-20-generico a 4.15.0-115-generico </br> Da 4.18.0-13 generica a 4.18.0-25 generica </br> 5.0.0-15-generico a 5.0.0-60-generico </br> 5.3.0-19-generico a 5.3.0-66-generico </br> 5.4.0-37-generico a 5.4.0-45-generico</br> 4.15.0-1009-Azure a 4.15.0-1093-Azure </br> Da 4.18.0-1006 Azure a 4.18.0-1025 Azure </br> 5.0.0-1012-Azure in 5.0.0-1036-Azure </br> 5.3.0-1007-da Azure a 5.3.0-1035-Azure </br> 5.4.0-1020-Azure a 5.4.0-1023-Azure</br> 4.15.0-117-Generic, 4.15.0-118-Generic, 5.0.0-61-Generic, 5.3.0-67-Generic, 5.4.0-47-Generic, 5.4.0-48-Generic, 4.15.0-1095-Azure, 4.15.0-1096-Azure, 5.4.0-1025-Azure, 5.4.0-1026-Azure 9,37 tramite patch di correzione a caldo * *|
18.04 LTS | [9,36](https://support.microsoft.com/help/4578241/) | 4.15.0-20-generico a 4.15.0-112-generico </br> Da 4.18.0-13 generica a 4.18.0-25 generica </br> 5.0.0-15-generico a 5.0.0-58-generico </br> 5.3.0-19-generico a 5.3.0-65-generico </br> 5.4.0-37-generico a 5.4.0-42-generico</br> 4.15.0-1009-Azure a 4.15.0-1092-Azure </br> Da 4.18.0-1006 Azure a 4.18.0-1025 Azure </br> 5.0.0-1012-Azure in 5.0.0-1036-Azure </br> 5.3.0-1007-da Azure a 5.3.0-1032-Azure </br> 5.4.0-1020-da Azure a 5.4.0-1022-Azure </br> 5.0.0-60-Generic & 5.3.0-1035-Azure fino a 9,36 patch per la correzione a caldo * *|
18.04 LTS | [9,34](https://support.microsoft.com/help/4570609), [9,35](https://support.microsoft.com/help/4573888/) | 4.15.0-20-generico a 4.15.0-108-generico </br> Da 4.18.0-13 generica a 4.18.0-25 generica </br> 5.0.0-15-generico a 5.0.0-52-generico </br> 5.3.0-19-generico a 5.3.0-61-generico </br> 4.15.0-1009-Azure a 4.15.0-1089-Azure </br> Da 4.18.0-1006 Azure a 4.18.0-1025 Azure </br> 5.0.0-1012-Azure in 5.0.0-1036-Azure </br> 5.3.0-1007-da Azure a 5.3.0-1031-Azure </br> 4.15.0-109-Generic, 5.0.0-53-Generic, 5.3.0-62-Generic, 4.15.0-1091-Azure & 5.3.0-1032-Azure 9,35 tramite patch di correzione a caldo * *|
|||
20,04 LTS |[9,38](https://support.microsoft.com/help/4590304/) | 5.4.0-26-generico a 5.4.0-48 </br> -Generic 5.4.0-1010-Azure in 5.4.0-1026-Azure </br> 5.4.0-51-Generic, 5.4.0-52-Generic, 5.8.0-23-Generic, 5.4.0-1031-da Azure a 9,38 patch di correzione a caldo * *
20,04 LTS |[9,37](https://support.microsoft.com/help/4582666/) | 5.4.0-26-generico a 5.4.0-45 </br> -Generic 5.4.0-1010-Azure a 5.4.0-1023-Azure </br> 5.4.0-47-Generic, 5.4.0-48-Generic, 5.4.0-1025-Azure, 5.4.0-1026-da Azure a 9,37 patch di correzione a caldo * *
20,04 LTS |[9,36](https://support.microsoft.com/help/4578241/) | 5.4.0-26-generico a 5.4.0-42 </br> -Generic 5.4.0-1010-da Azure a 5.4.0-1022-Azure

* * Nota: per supportare i kernel Linux più recenti entro 15 giorni dal rilascio, Azure Site Recovery distribuisce patch di correzione a caldo oltre alla versione più recente dell'agente di mobilità. Questa correzione viene implementata tra due versioni principali. Per eseguire l'aggiornamento alla versione più recente dell'agente Mobility (inclusa patch per la correzione a caldo), seguire i passaggi descritti in [questo articolo](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure). Questa patch è attualmente implementata per gli agenti di mobilità usati in uno scenario di ripristino di emergenza di Azure in Azure.

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel Debian supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
Debian 7 |  [9,34](https://support.microsoft.com/help/4570609), [9,35](https://support.microsoft.com/help/4573888/), [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/) | Da 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9,35](https://support.microsoft.com/help/4573888/, ), [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/) | da 3.16.0-4-amd64 a 3.16.0-11-amd64, 4.9.0-0. BPO. 4-amd64 a 4.9.0 0. BPO. 11-amd64 |
Debian 8 | [9,34](https://support.microsoft.com/help/4570609) | Da 3.16.0-4-amd64 a 3.16.0-10-amd64, da 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.11-amd64 |
|||
Debian 9,1 | [9,38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-amd64 a 4.9.0-13-amd64 </br> 4.19.0-0. BPO. 1-amd64 a 4.19.0-0. BPO. 11-amd64 </br> 4.19.0-0. BPO. 1-cloud-amd64 a 4.19.0-0. BPO. 11-cloud-amd64 </br> 
Debian 9,1 | [9,37](https://support.microsoft.com/help/4582666/) | da 4.9.0-3-amd64 a 4.9.0-13-amd64, 4.19.0-0. BPO. 6-amd64 a 4.19.0 0. BPO. 10-amd64, 4.19.0 0. BPO. 6-cloud-amd64 a 4.19.0 0. BPO. 10-cloud-amd64

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel SUSE Linux Enterprise Server 12 supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,38](https://support.microsoft.com/help/4590304/) | Sono supportati tutti i [kernel di borsa SUSE 12 SP1, SP2, SP3 e SP4](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> Da 4.4.138-4.7 Azure a 4.4.180-4.31 Azure</br>da 4.12.14-6.3-Azure a 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure per 4.12.14-16.28-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/) | Sono supportati tutti i [kernel di borsa SUSE 12 SP1, SP2, SP3 e SP4](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> Da 4.4.138-4.7 Azure a 4.4.180-4.31 Azure</br>da 4.12.14-6.3-Azure a 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure per 4.12.14-16.22-Azure </br> 4.12.14-16.25-Azure, 4.12.14-16.28-da Azure a 9,37 patch per la correzione a caldo * *|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,34](https://support.microsoft.com/help/4570609), [9,35](https://support.microsoft.com/help/4573888/) | Sono supportati tutti i [kernel di borsa SUSE 12 SP1, SP2, SP3 e SP4](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> Da 4.4.138-4.7 Azure a 4.4.180-4.31 Azure</br>da 4.12.14-6.3-Azure a 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure per 4.12.14-16.19-Azure  |

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel SUSE Linux Enterprise Server 15 supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,38](https://support.microsoft.com/help/4590304/)  | Per impostazione predefinita, sono supportati tutti i [kernel stock SUSE 15 e 15](https://www.suse.com/support/kb/doc/?id=000019587).</br></br> 4.12.14-5.5-da Azure a 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-da Azure a 4.12.14-8.44-Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure in 5.3.18-18.18-Azure </br> 4.12.14-8.47-Azure, 5.3.18-18.21-da Azure a 9,38 patch per la correzione a caldo * *
SUSE Linux Enterprise Server 15 e 15 SP1 | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/)  | Per impostazione predefinita, sono supportati tutti i [kernel stock SUSE 15 e 15](https://www.suse.com/support/kb/doc/?id=000019587).</br></br> 4.12.14-5.5-da Azure a 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-da Azure a 4.12.14-8.38-Azure </br> 4.12.14-8.41-Azure, 4.12.14-8.44-da Azure a 9,37 patch per la correzione a caldo * *
SUSE Linux Enterprise Server 15 e 15 SP1 | [9,34](https://support.microsoft.com/help/4570609), [9,35](https://support.microsoft.com/help/4573888/)  | Per impostazione predefinita, sono supportati tutti i [kernel stock SUSE 15 e 15](https://www.suse.com/support/kb/doc/?id=000019587).</br></br> 4.12.14-5.5-da Azure a 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-da Azure a 4.12.14-8.33-Azure 


## <a name="replicated-machines---linux-file-systemguest-storage"></a>Computer replicati - File system/archiviazione guest Linux

* File system: ext3, ext4, XFS, BTRFS
* Gestore volumi: LVM2

> [!NOTE]
> Il software a percorsi multipli non è supportato. 


## <a name="replicated-machines---compute-settings"></a>Computer replicati - Impostazioni di calcolo

**Impostazione** | **Supporto** | **Dettagli**
--- | --- | ---
Dimensione | Macchine virtuali di Azure di qualsiasi dimensione con almeno 2 core CPU e 1 GB di RAM | Verificare le [dimensioni delle macchine virtuali in Azure](../virtual-machines/sizes.md).
Set di disponibilità | Supportato | Se si abilita la replica per una macchina virtuale di Azure con le opzioni predefinite, viene automaticamente creato un set di disponibilità in base alle impostazioni dell'area di origine. È possibile modificare queste impostazioni.
Zone di disponibilità | Supportato |
Vantaggio Hybrid Use (HUB) | Supportato | Se la macchina virtuale di origine dispone di una licenza HUB abilitata, anche la macchina virtuale di failover o il failover di test userà la licenza HUB.
set di scalabilità di macchine virtuali | Non supportate |
Immagini della raccolta di Azure - Pubblicate da Microsoft | Supportato | Supportate se la macchina virtuale viene eseguita in un sistema operativo supportato.
Immagini della raccolta di Azure - Pubblicate da terze parti | Supportato | Supportate se la macchina virtuale viene eseguita in un sistema operativo supportato.
Immagini personalizzate - Pubblicate da terze parti | Supportato | Supportate se la macchina virtuale viene eseguita in un sistema operativo supportato.
Macchine virtuali migrate tramite Site Recovery | Supportato | Se una VM VMware o un computer fisico è stato migrato ad Azure tramite Site Recovery, è necessario disinstallare la versione precedente del servizio di mobilità in esecuzione nel computer e riavviare il computer prima di eseguirne la replica in un'altra area di Azure.
Criteri RBAC di Azure | Non supportate | I criteri di controllo degli accessi in base al ruolo di Azure (RBAC) sulle VM non vengono replicati nella macchina virtuale di failover nell'area di destinazione.
Estensioni | Non supportate | Le estensioni non vengono replicate nella macchina virtuale di failover nell'area di destinazione. L'estensione deve essere installata manualmente dopo il failover.
Gruppi di posizionamento di prossimità | Supportato | Le macchine virtuali che si trovano all'interno di un gruppo di posizionamento di prossimità possono essere protette usando Site Recovery.


## <a name="replicated-machines---disk-actions"></a>Computer replicati - Azioni del disco

**Azione** | **Dettagli**
-- | ---
Ridimensionamento del disco nella macchina virtuale replicata | Supportata nella macchina virtuale di origine prima del failover. Non è necessario disabilitare e riabilitare la replica.<br/><br/> Se si modifica la macchina virtuale di origine dopo il failover, le modifiche non vengono acquisite.<br/><br/> Se si modificano le dimensioni del disco nella macchina virtuale di Azure dopo il failover, le modifiche non vengono acquisite da Site Recovery e il failback si verificherà sulle dimensioni originali della macchina virtuale.
Aggiunta di un disco a una macchina virtuale replicata | Supportato
Modifiche offline ai dischi protetti | La disconnessione di dischi e l'esecuzione di modifiche offline a tali dischi richiedono l'attivazione di una risincronizzazione completa.

## <a name="replicated-machines---storage"></a>Computer replicati - Archiviazione

Questa tabella riepiloga il supporto per il disco del sistema operativo, il disco dati e il disco temporaneo della macchina virtuale di Azure.

- È importante osservare i limiti dei dischi e le destinazioni per le macchine virtuali [Linux](../virtual-machines/linux/disk-scalability-targets.md) e [Windows](../virtual-machines/windows/disk-scalability-targets.md) per evitare eventuali problemi di prestazioni.
- Se si esegue la distribuzione con le impostazioni predefinite, Site Recovery crea automaticamente i dischi e gli account di archiviazione in base alle impostazioni di origine.
- Se si esegue la personalizzazione, assicurarsi di seguire le linee guida.

**Componente** | **Supporto** | **Dettagli**
--- | --- | ---
Dimensione massima del disco del sistema operativo | 2048 GB | [Altre informazioni](../virtual-machines/managed-disks-overview.md) sui dischi delle VM.
Disco temporaneo | Non supportate | Il disco temporaneo è sempre escluso dalla replica.<br/><br/> Non conservare dati persistenti sul disco temporaneo. [Altre informazioni](../virtual-machines/managed-disks-overview.md)
Dimensione massima del disco dati | 8192 GB per i dischi gestiti<br></br>4095 GB per i dischi non gestiti|
Dimensione minima del disco dati | Nessuna restrizione per i dischi non gestiti. 2 GB per i dischi gestiti |
Numero massimo di dischi dati | Fino a 64, in conformità con il supporto per una specifica dimensione di VM di Azure | [Altre informazioni](../virtual-machines/sizes.md) sulle dimensioni delle VM.
Frequenza di modifica del disco dati | Massimo 20 MBps per disco per archiviazione Premium. Massimo 2 Mbps per disco per l'archiviazione Standard. | Se la frequenza di modifica media dei dati sul disco supera costantemente il limite massimo, la replica non verrà aggiornata.<br/><br/>  Se tuttavia il limite massimo viene superato sporadicamente, la replica può essere aggiornata, ma i punti di ripristino potrebbero essere visualizzati leggermente ritardati.
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
Crittografia a doppio inattivo | Supportato | Altre informazioni sulle aree supportate per [Windows](../virtual-machines/windows/disk-encryption.md) e [Linux](../virtual-machines/linux/disk-encryption.md)
Crittografia dischi di Azure (ADE) per sistema operativo Windows | Supportato per le macchine virtuali con dischi gestiti. | Le macchine virtuali che usano dischi non gestiti non sono supportate. <br/><br/> Le chiavi con protezione HSM non sono supportate. <br/><br/> La crittografia dei singoli volumi in un unico disco non è supportata. |
Crittografia dischi di Azure (ADE) per sistema operativo Linux | Supportato per le macchine virtuali con dischi gestiti. | Le macchine virtuali che usano dischi non gestiti non sono supportate. <br/><br/> Le chiavi con protezione HSM non sono supportate. <br/><br/> La crittografia dei singoli volumi in un unico disco non è supportata. <br><br> Problema noto relativo all'abilitazione della replica. [Altre informazioni.](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
Rotazione della chiave SAS | Non supportato | Se la chiave SAS per gli account di archiviazione viene ruotata, il cliente deve disabilitare e riabilitare la replica. |
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
Firewall di Archiviazione di Azure per reti virtuali  | Supportato | Se si limita l'accesso alla rete virtuale agli account di archiviazione, abilitare [Consenti servizi Microsoft attendibili](../storage/common/storage-network-security.md#exceptions).
Account di archiviazione V2 generico (livelli di accesso frequente e sporadico) | Supportato | Aumento sostanziale dei costi delle transazioni rispetto agli account di archiviazione V1 generici
Generazione 2 (avvio UEFI) | Supportato
Dischi NVMe | Non supportate
Dischi condivisi di Azure | Non supportate
Opzione di trasferimento sicuro | Supportato
Dischi abilitati per l'acceleratore di scrittura | Non supportate

>[!IMPORTANT]
> Per evitare problemi di prestazioni, assicurarsi di osservare gli obiettivi di scalabilità e prestazioni del disco della macchina virtuale per le macchine virtuali [Linux](../virtual-machines/linux/disk-scalability-targets.md) o [Windows](../virtual-machines/windows/disk-scalability-targets.md). Se si usano le impostazioni predefinite, Site Recovery crea i dischi e gli account di archiviazione necessari in base alla configurazione di origine. Se si personalizzano e si selezionano impostazioni personalizzate, rispettare gli obiettivi di scalabilità e prestazioni dei dischi per le macchine virtuali.

## <a name="limits-and-data-change-rates"></a>Limiti e frequenza di modifica dei dati

La tabella seguente offre un riepilogo dei limiti di Site Recovery.

- Questi limiti sono basati sui test di Microsoft, ma non coprono tutte le possibili combinazioni di I/O delle applicazioni.
- I risultati effettivi possono variare in base alla combinazione di I/O dell'applicazione.
- È necessario considerare due limiti, la varianza dei dati del disco e la varianza dei dati della macchina virtuale.
- Il limite corrente per la varianza dei dati per macchina virtuale è 54 MB/s, indipendentemente dalle dimensioni.

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
NIC | Numero massimo supportato per una specifica dimensione di macchina virtuale di Azure | Vengono create schede di interfaccia di rete contestualmente alla creazione della VM durante il failover.<br/><br/> Il numero di schede di interfaccia di rete sulla macchina virtuale di failover dipende dal numero di schede di rete sulla VM di origine quando è stata abilitata la replica. L'aggiunta o la rimozione di una scheda di interfaccia di rete dopo l'abilitazione della replica non influisce sul numero di schede sulla VM replicata dopo il failover. <br/><br/> Non è garantito che l'ordine delle schede di rete dopo il failover sia uguale a quello originale. <br/><br/> È possibile rinominare le schede di rete nell'area di destinazione in base alle convenzioni di denominazione dell'organizzazione. La ridenominazione NIC è supportata tramite PowerShell.
Servizio di bilanciamento del carico Internet | Non supportate | È possibile configurare il servizio di bilanciamento del carico pubblico/Internet nell'area primaria. Tuttavia, i bilanciamenti del carico pubblico/Internet non sono supportati da Azure Site Recovery nell'area di ripristino di emergenza.
Servizio di bilanciamento del carico interno | Supportato | Associare il servizio di bilanciamento del carico preconfigurato tramite uno script di Automazione di Azure in un piano di ripristino.
Indirizzo IP pubblico | Supportato | Associare un indirizzo IP pubblico esistente alla scheda di interfaccia di rete. In alternativa, creare un indirizzo IP pubblico e associarlo alla scheda di interfaccia di rete tramite uno script di Automazione di Azure in un piano di ripristino.
Gruppo di sicurezza di rete o scheda di interfaccia di rete | Supportato | Associare il gruppo di sicurezza di rete alla scheda di interfaccia di rete tramite uno script di Automazione di Azure in un piano di ripristino.
Gruppo di sicurezza di rete o subnet | Supportato | Associare il gruppo di sicurezza di rete alla subnet tramite uno script di Automazione di Azure in un piano di ripristino.
Indirizzo IP riservato (statico) | Supportato | Se la scheda di rete sulla macchina virtuale di origine ha un indirizzo IP statico e la subnet di destinazione dispone dello stesso IP, viene assegnato alla macchina virtuale di failover.<br/><br/> Se la subnet di destinazione non ha lo stesso indirizzo IP, uno degli IP disponibili nella subnet viene riservato per la macchina virtuale.<br/><br/> È anche possibile specificare un indirizzo IP fisso e una subnet in **Elementi replicati** > **Impostazioni** > **Calcolo e rete** > **Interfacce di rete** .
Indirizzo IP dinamico | Supportato | Se la scheda di interfaccia di rete nella macchina virtuale di origine ha indirizzi IP dinamici, anche la scheda sulla macchina virtuale di failover è dinamica per impostazione predefinita.<br/><br/> Se necessario, è possibile modificare questa opzione in un indirizzo IP fisso.
Più indirizzi IP | Non supportate | Quando si esegue il failover di una macchina virtuale con una scheda di interfaccia di rete con più indirizzi IP, viene mantenuto solo l'indirizzo IP primario della scheda di interfaccia di rete nell'area di origine. Per assegnare più indirizzi IP, è possibile aggiungere macchine virtuali a un [piano di ripristino](recovery-plan-overview.md) e associare uno script per assegnare indirizzi IP aggiuntivi al piano oppure è possibile apportare la modifica manualmente o con uno script dopo il failover.
Gestione traffico     | Supportato | È possibile preconfigurare Gestione traffico in modo che il traffico venga regolarmente indirizzato all'endpoint nell'area di origine e all'endpoint nell'area di destinazione in caso di failover.
DNS di Azure | Supportato |
DNS personalizzato    | Supportato |
Proxy non autenticato | Supportato | [Altre informazioni](./azure-to-azure-about-networking.md)
Proxy autenticato | Non supportate | Se la macchina virtuale utilizza un proxy autenticato per la connettività in uscita, non può essere replicata tramite Azure Site Recovery.
Connessione da sito a sito VPN a locale<br/><br/>(con o senza ExpressRoute)| Supportato | Verificare che le route definite dall'utente e i gruppi di sicurezza di rete siano configurati in modo che il traffico di Site Recovery non venga instradato in locale. [Altre informazioni](./azure-to-azure-about-networking.md)
Connessione da rete virtuale a rete virtuale    | Supportato | [Altre informazioni](./azure-to-azure-about-networking.md)
Endpoint servizio di rete virtuale | Supportato | Se si limita l'accesso alla rete virtuale agli account di archiviazione, assicurarsi che ai servizi Microsoft attendibili sia consentito l'accesso all'account di archiviazione.
Rete accelerata | Supportato | La rete accelerata deve essere abilitata su una macchina virtuale di origine. [Altre informazioni](azure-vm-disaster-recovery-with-accelerated-networking.md)
Palo Alto Network Appliance | Non supportate | Con appliance di terze parti, vengono spesso imposte restrizioni dal provider all'interno della macchina virtuale. Azure Site Recovery richiede che agente, estensioni e connettività in uscita siano disponibili. Tuttavia, l'appliance non consente la configurazione di alcuna attività in uscita all'interno della macchina virtuale.
IPv6  | Non supportate | Anche le configurazioni miste che includono IPv4 e IPv6 non sono supportate. Liberare la subnet dell'intervallo IPv6 prima di qualsiasi operazione di Site Recovery.
Accesso a collegamento privato al servizio Site Recovery | Supportato | [Altre informazioni](azure-to-azure-how-to-enable-replication-private-endpoints.md)



## <a name="next-steps"></a>Passaggi successivi

- Leggere il [materiale sussidiario per la rete](./azure-to-azure-about-networking.md) per la replica delle macchine virtuali di Azure.
- Distribuire il ripristino di emergenza [replicando le macchine virtuali di Azure](./azure-to-azure-quickstart.md).
