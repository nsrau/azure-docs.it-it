---
title: Matrice di supporto Azure Migrate per la valutazione e la migrazione di Hyper-V
description: Riepiloga le impostazioni e le limitazioni per la valutazione e la migrazione di Hyper-V tramite il servizio Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: raynew
ms.openlocfilehash: 507ca6daa30a19b73848d6d3cf253390baf496af
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372463"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Matrice di supporto per la valutazione e la migrazione di Hyper-V

È possibile usare il [servizio Azure migrate](migrate-overview.md) per valutare ed eseguire la migrazione di computer nel cloud Microsoft Azure. Questo articolo riepiloga le impostazioni e le limitazioni del supporto per la valutazione e la migrazione di macchine virtuali Hyper-V locali.



## <a name="hyper-v-scenarios"></a>Scenari di Hyper-V

Nella tabella sono riepilogati gli scenari supportati per le macchine virtuali Hyper-V.

**Distribuzione** | **Dettagli***
--- | ---
**Valutare le VM Hyper-V locali** | [Configurare](tutorial-prepare-hyper-v.md) la prima valutazione.<br/><br/> [Eseguire](scale-hyper-v-assessment.md) una valutazione su larga scala.
**Eseguire la migrazione di VM Hyper-V ad Azure** | [Provare](tutorial-migrate-hyper-v.md) a eseguire la migrazione ad Azure.



## <a name="azure-migrate-projects"></a>Progetti Azure Migrate

**Supporto** | **Dettagli**
--- | ---
Autorizzazioni di Azure | Per creare un progetto di Azure Migrate, è necessario disporre delle autorizzazioni Collaboratore o proprietario nella sottoscrizione.
VM Hyper-V | Valutazione di un massimo di 10.000 macchine virtuali Hyper-V in un singolo progetto.

Un progetto può includere sia macchine virtuali VMware che macchine virtuali Hyper-V, fino ai limiti di valutazione.

**Geografia** Sono disponibili diverse aree geografiche in cui è possibile creare un progetto di Azure Migrate. Anche se è possibile creare progetti solo in queste aree geografiche, è comunque possibile valutare o migrare i computer per altri percorsi di destinazione. L'area geografica del progetto viene usata solo per archiviare i metadati individuati.


 **Area geografica** | **Posizione di archiviazione dei metadati**
 --- | ---
 Azure per enti pubblici | US Gov Virginia
 Asia/Pacifico | Asia sud-orientale o Asia orientale
 Europa | Europa meridionale o Europa occidentale
 Regno Unito | Regno Unito meridionale o Regno Unito occidentale
 Stati Uniti | Stati Uniti centrali o Stati Uniti occidentali 2


 > [!NOTE]
 > Il supporto per Azure per enti pubblici è attualmente disponibile solo per la [versione precedente](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) di Azure migrate.


## <a name="assessment-hyper-v-host-requirements"></a>Valutazione-requisiti dell'host Hyper-V

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione host**       | L'host Hyper-V può essere autonomo o distribuito in un cluster. |
| **Autorizzazioni**           | Sono necessarie autorizzazioni di amministratore per l'host Hyper-V. |
| **Sistema operativo host** | Windows Server 2016 o Windows Server 2012 R2.<br/> Non è possibile valutare le macchine virtuali presenti negli host Hyper-V che eseguono Windows Server 2019. |
| **Comunicazione remota di PowerShell**   | Deve essere abilitato in ogni host. |
| **Replica Hyper-V**       | Se si usa la replica Hyper-V (o sono presenti più macchine virtuali con gli stessi identificatori di VM) e si individuano le VM originali e replicate usando Azure Migrate, la valutazione generata da Azure Migrate potrebbe non essere accurata. |


## <a name="assessment-hyper-v-vm-requirements"></a>Valutazione-requisiti per le macchine virtuali Hyper-V

| **Supporto**                  | **Dettagli**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Tutti i sistemi operativi [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) supportati da Azure. |
| **Autorizzazioni**           | Sono necessarie autorizzazioni di amministratore per ogni macchina virtuale Hyper-V che si desidera valutare. |
| **Integration Services**       | Per acquisire le informazioni sul sistema operativo, è necessario che [Hyper-V Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) sia in esecuzione in macchine virtuali valutate. |
| **Modifiche necessarie per Azure** | Alcune macchine virtuali potrebbero richiedere modifiche in modo che possano essere eseguite in Azure. Azure Migrate apporta automaticamente queste modifiche per i sistemi operativi seguenti:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> Per altri sistemi operativi, è necessario apportare modifiche manualmente prima della migrazione. Gli articoli pertinenti contengono istruzioni su come eseguire questa operazione. |
| **Avvio di Linux**                 | Se/boot si trova in una partizione dedicata, deve risiedere nel disco del sistema operativo e non essere distribuito tra più dischi.<br/> Se/boot fa parte della partizione radice (/), la partizione '/' deve trovarsi nel disco del sistema operativo e non si estende su altri dischi. |
| **Avvio UEFI**                  | Le macchine virtuali con avvio UEFI non sono supportate per la migrazione. |
| **Dischi/volumi crittografati**    | Le macchine virtuali con dischi/volumi crittografati non sono supportate per la migrazione. |
| **Dischi RDM/passthrough**      | Se le VM contengono dischi RDM o passthrough, questi dischi non verranno replicati in Azure. |
| **NFS**                        | I volumi NFS montati come volumi nelle macchine virtuali non verranno replicati. |
| **Disco di destinazione**                | Azure Migrate Assessment consigliano la migrazione alle macchine virtuali di Azure solo con Managed Disks. |


## <a name="assessment-appliance-requirements"></a>Valutazione-requisiti del dispositivo

Per la valutazione, Azure Migrate esegue un appliance leggero per individuare le VM Hyper-V e inviare i dati sulle prestazioni e i metadati della macchina virtuale a Azure Migrate. Il dispositivo viene eseguito in una macchina virtuale Hyper-V e viene configurato usando un VHD Hyper-V compresso che è possibile scaricare dal portale di Azure. La tabella seguente riepiloga i requisiti del dispositivo.

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Progetto Azure Migrate**  |  Un appliance può essere associato a un singolo progetto.<br/> È possibile individuare fino a 5000 VM Hyper-V con un unico dispositivo.
| **Limitazioni di Hyper-V**    |  Si distribuisce l'appliance come macchina virtuale Hyper-V.<br/> La macchina virtuale dell'appliance fornita è la macchina virtuale Hyper-V versione 5,0.<br/> L'host della macchina virtuale deve eseguire Windows Server 2012 R2 o versione successiva.<br/> Richiede spazio sufficiente per allocare 16 GB di RAM, 4 processori virtuali e 1 compartitore esterno per la macchina virtuale dell'appliance.<br/> Il dispositivo richiede un indirizzo IP statico o dinamico e l'accesso a Internet.
| **Appliance Hyper-V**      |  Il dispositivo è configurato come macchina virtuale Hyper-V.<br/> Il disco rigido virtuale fornito per il download è la macchina virtuale Hyper-V 5,0.
| **Host**                   | L'host della macchina virtuale che esegue la macchina virtuale dell'appliance deve eseguire Windows Server 2012 R2 o versione successiva.<br/> Richiede spazio sufficiente per allocare 16 GB di RAM, 4 processori virtuali e un compartitore esterno per la macchina virtuale dell'appliance.<br/> Il dispositivo richiede un indirizzo IP statico o dinamico e l'accesso a Internet. |
| **Supporto per la migrazione**      | Per avviare la replica dei computer, il servizio gateway di migrazione nel dispositivo deve essere 1.18.7141.12919 o versione successiva. Accedere all'app Web Appliance per verificare la versione. |

## <a name="assessment-appliance-url-access"></a>Valutazione-accesso con URL dell'appliance

Per valutare le VM, il dispositivo Azure Migrate necessita della connettività Internet.

- Quando si distribuisce il dispositivo, Azure Migrate esegue un controllo della connettività agli URL riepilogati nella tabella seguente.
- Se si usa un firewall basato su URL. proxy, consentire l'accesso agli URL nella tabella, assicurandosi che il proxy risolva tutti i record CNAME ricevuti durante la ricerca degli URL.
- Se si dispone di un proxy di intercettazione, potrebbe essere necessario importare il certificato server dal server proxy al dispositivo.


**URL** | **Dettagli**  
--- | ---
*.portal.azure.com | Spostamento all'portale di Azure
*.windows.net | Accedere alla sottoscrizione di Azure
*.microsoftonline.com | Creazione di applicazioni Azure Active Directory per le comunicazioni da dispositivo a servizio.
management.azure.com | Creazione di applicazioni Azure Active Directory per le comunicazioni da dispositivo a servizio.
dc.services.visualstudio.com | Registrazione e monitoraggio
*.vault.azure.net | Gestisci i segreti in Azure Key Vault durante la comunicazione tra il dispositivo e il servizio.


## <a name="assessment-port-requirements"></a>Valutazione-requisiti per le porte

Nella tabella seguente sono riepilogati i requisiti di porta per la valutazione.

**Dispositivo** | **Connection**
--- | ---
**Appliance** | Connessioni in ingresso sulla porta TCP 3389 per consentire le connessioni Desktop remoto al dispositivo.<br/> Connessioni in ingresso sulla porta 44368 per accedere in remoto all'app di gestione Appliance usando l'URL: https://< Appliance-IP-or-name >: 44368<br/> Connessioni in uscita sulla porta 443 per inviare i metadati di individuazione e di prestazioni a Azure Migrate.
**Host/cluster Hyper-V** | Connessioni in ingresso sulle porte WinRM 5985 (HTTP) e 5986 (HTTPS) per eseguire il pull dei metadati di configurazione e delle prestazioni delle VM Hyper-V usando una sessione di Common Information Model (CIM).

## <a name="migration-hyper-v-host-requirements"></a>Migrazione: requisiti dell'host Hyper-V

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione host**       | L'host Hyper-V può essere autonomo o distribuito in un cluster. |
| **Autorizzazioni**           | Sono necessarie autorizzazioni di amministratore per l'host Hyper-V. |
| **Sistema operativo host** | Windows Server 2019, Windows Server 2016 o Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>Migrazione: requisiti della macchina virtuale Hyper-V

| **Supporto**                  | **Dettagli**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Tutti i sistemi operativi [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) supportati da Azure. |
| **Autorizzazioni**           | Sono necessarie autorizzazioni di amministratore per ogni macchina virtuale Hyper-V che si desidera valutare. |
| **Integration Services**       | Per acquisire le informazioni sul sistema operativo, è necessario che [Hyper-V Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) sia in esecuzione in macchine virtuali valutate. |
| **Modifiche necessarie per Azure** | Alcune macchine virtuali potrebbero richiedere modifiche in modo che possano essere eseguite in Azure. Azure Migrate apporta automaticamente queste modifiche per i sistemi operativi seguenti:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> Per altri sistemi operativi, è necessario apportare modifiche manualmente prima della migrazione. Gli articoli pertinenti contengono istruzioni su come eseguire questa operazione. |
| **Avvio di Linux**                 | Se/boot si trova in una partizione dedicata, deve risiedere nel disco del sistema operativo e non essere distribuito tra più dischi.<br/> Se/boot fa parte della partizione radice (/), la partizione '/' deve trovarsi nel disco del sistema operativo e non si estende su altri dischi. |
| **Avvio UEFI**                  | Le macchine virtuali con avvio UEFI non sono supportate per la migrazione. |
| **Dischi/volumi crittografati**    | Le macchine virtuali con dischi/volumi crittografati non sono supportate per la migrazione. |
| **Dischi RDM/passthrough**      | Se le VM contengono dischi RDM o passthrough, questi dischi non verranno replicati in Azure. |
| **NFS**                        | I volumi NFS montati come volumi nelle macchine virtuali non verranno replicati. |
| **Disco di destinazione**                | È possibile eseguire la migrazione solo a macchine virtuali di Azure con Managed Disks. |




## <a name="migration-hyper-v-host-url-access"></a>Migrazione-accesso con URL host Hyper-V

Nella tabella seguente sono riepilogati i requisiti di accesso all'URL per gli host Hyper-V.

**URL** | **Dettagli**  
--- | ---
login.microsoftonline.com | Controllo di accesso e gestione delle identità con Active Directory.
*.backup.windowsazure.com | Trasferimento e coordinamento dei dati di replica.
*.hypervrecoverymanager.windowsazure.com | Connettersi agli URL del servizio Azure Migrate.
*.blob.core.windows.net | Caricare i dati negli account di archiviazione.
dc.services.visualstudio.com | Caricare i log delle app usati per il monitoraggio interno.
time.windows.com | Verifica la sincronizzazione dell'ora tra il sistema e l'ora globale.

## <a name="migration-port-access"></a>Migrazione-accesso alle porte

Nella tabella seguente sono riepilogati i requisiti di porta per le macchine virtuali e gli host Hyper-V per la migrazione delle VM.

**Dispositivo** | **Connection**
--- | ---
Host/VM Hyper-V | Connessioni in uscita sulla porta HTTPS 443 per inviare i dati di replica della macchina virtuale a Azure Migrate.


## <a name="migration-vm-disk-support"></a>Migrazione: supporto del disco della macchina virtuale

**Supporto** | **Dettagli**
--- | ---
Dischi migrati | È possibile eseguire la migrazione delle macchine virtuali solo a Managed Disks (standard HHD, SSD Premium) in Azure.


## <a name="next-steps"></a>Passaggi successivi

[Preparare la valutazione della macchina virtuale Hyper-V](tutorial-prepare-hyper-v.md) per la migrazione.
