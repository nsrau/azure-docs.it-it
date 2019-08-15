---
title: Matrice di supporto Azure Migrate per la valutazione e la migrazione di Hyper-V
description: Riepiloga le impostazioni e le limitazioni per la valutazione e la migrazione di Hyper-V tramite il servizio Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: raynew
ms.openlocfilehash: 00f222472a9b41c7f95ae90bdca57f13175b2b5d
ms.sourcegitcommit: acffa72239413c62662febd4e39ebcb6c6c0dd00
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68952135"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Matrice di supporto per la valutazione e la migrazione di Hyper-V

È possibile usare il [servizio Azure migrate](migrate-overview.md) per valutare ed eseguire la migrazione di computer nel cloud Microsoft Azure. Questo articolo riepiloga le impostazioni e le limitazioni del supporto per la valutazione e la migrazione di macchine virtuali Hyper-V locali.



## <a name="hyper-v-scenarios"></a>Scenari di Hyper-V

Nella tabella sono riepilogati gli scenari supportati per le macchine virtuali Hyper-V.

**Distribuzione** | **Dettagli***
--- | ---
**Valutare le VM Hyper-V locali** | [Configurare](tutorial-prepare-hyper-v.md) la prima valutazione.<br/><br/> [Eseguire](scale-hyper-v-assessment.md) una valutazione su larga scala.
**Eseguire la migrazione di VM Hyper-V ad Azure** | [Provare](tutorial-migrate-hyper-v.md) a eseguire la migrazione ad Azure. 

La migrazione dei server Hyper-V gestiti con System Center Virtual Machine Manager (VMM) non è supportata dalla migrazione Azure Migrate server. 

## <a name="azure-migrate-projects"></a>Progetti Azure Migrate

**Supporto** | **Dettagli**
--- | ---
Autorizzazioni di Azure | Per creare un progetto di Azure Migrate, è necessario disporre delle autorizzazioni Collaboratore o proprietario nella sottoscrizione.
VM Hyper-V | Valutazione di un massimo di 35.000 macchine virtuali Hyper-V in un singolo progetto. È possibile avere più progetti in una sottoscrizione di Azure. Un progetto può includere sia macchine virtuali VMware che macchine virtuali Hyper-V, fino ai limiti di valutazione.
Geografia | È possibile creare progetti Azure Migrate in diverse aree geografiche. Sebbene sia possibile creare progetti in ographies specifici, è possibile valutare o migrare i computer per altri percorsi di destinazione. L'area geografica del progetto viene usata solo per archiviare i metadati individuati.

  **Area geografica** | **Posizione di archiviazione dei metadati**
  --- | ---
  Azure per enti pubblici | US Gov Virginia
  Asia/Pacifico | Asia orientale o Asia sud-orientale
  Australia | Australia orientale o Australia sudorientale
  Canada | Canada centrale o Canada orientale
  Europa | Europa settentrionale o Europa occidentale
  India | India centrale o India meridionale
  Giappone |  Giappone orientale o Giappone occidentale
  Regno Unito | Regno Unito meridionale o Regno Unito occidentale
  Stati Uniti | Stati Uniti centrali o Stati Uniti occidentali 2


 > [!NOTE]
 > Il supporto per Azure per enti pubblici è attualmente disponibile solo per la [versione precedente](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) di Azure migrate.


## <a name="assessment-hyper-v-host-requirements"></a>Valutazione-requisiti dell'host Hyper-V

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione host**       | L'host Hyper-V può essere autonomo o distribuito in un cluster. |
| **Autorizzazioni**           | Sono necessarie autorizzazioni di amministratore per l'host Hyper-V. <br/> In alternativa, se non si desidera assegnare autorizzazioni di amministratore, creare un account utente locale o di dominio e aggiungere l'utente a questi gruppi: utenti di gestione remota, amministratori di Hyper-V e performance monitor. |
| **Sistema operativo host** | Windows Server 2019, Windows Server 2016 o Windows Server 2012 R2.<br/> Non è possibile valutare le macchine virtuali presenti negli host Hyper-V che eseguono Windows Server 2012. |
| **Comunicazione remota di PowerShell**   | Deve essere abilitato in ogni host. |
| **Replica Hyper-V**       | Se si usa la replica Hyper-V (o sono presenti più macchine virtuali con gli stessi identificatori di VM) e si individuano le VM originali e replicate usando Azure Migrate, la valutazione generata da Azure Migrate potrebbe non essere accurata. |


## <a name="assessment-hyper-v-vm-requirements"></a>Valutazione-requisiti per le macchine virtuali Hyper-V

| **Supporto**                  | **Dettagli**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Tutti i sistemi operativi [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) supportati da Azure. |
| **Integration Services**       | Per acquisire le informazioni sul sistema operativo, è necessario che [Hyper-V Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) sia in esecuzione in macchine virtuali valutate. |



## <a name="assessment-appliance-requirements"></a>Valutazione-requisiti del dispositivo

Per la valutazione, Azure Migrate esegue un appliance leggero per individuare le VM Hyper-V e inviare i dati sulle prestazioni e i metadati della macchina virtuale a Azure Migrate. Il dispositivo viene eseguito in una macchina virtuale Hyper-V e viene configurato usando un VHD Hyper-V compresso che è possibile scaricare dal portale di Azure. La tabella seguente riepiloga i requisiti del dispositivo.

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Progetto Azure Migrate**  |  Un appliance può essere associato a un singolo progetto.<br/> È possibile individuare fino a 5000 VM Hyper-V con un unico dispositivo.
| **Hyper-V**    |  Si distribuisce l'appliance come macchina virtuale Hyper-V.<br/> La macchina virtuale dell'appliance fornita è la macchina virtuale Hyper-V versione 5,0.<br/> L'host della macchina virtuale deve eseguire Windows Server 2012 R2 o versione successiva.<br/> Richiede spazio sufficiente per allocare 16 GB di RAM, 8 vCPU e 1 compartitore esterno per la macchina virtuale dell'appliance.<br/> Il dispositivo richiede un indirizzo IP statico o dinamico e l'accesso a Internet.


## <a name="assessment-appliance-url-access"></a>Valutazione-accesso con URL dell'appliance

Per valutare le VM, il dispositivo Azure Migrate necessita della connettività Internet.

- Quando si distribuisce il dispositivo, Azure Migrate esegue un controllo della connettività agli URL riepilogati nella tabella seguente.
- Se si usa un proxy basato su URL, consentire l'accesso agli URL nella tabella, assicurandosi che il proxy risolva tutti i record CNAME ricevuti durante la ricerca degli URL.
- Se si dispone di un proxy di intercettazione, potrebbe essere necessario importare il certificato server dal server proxy al dispositivo.


**URL** | **Dettagli**  
--- | ---
*.portal.azure.com | Spostamento all'portale di Azure
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Accedere alla sottoscrizione di Azure
*.microsoftonline.com <br/> *.microsoftonline-p.com | Creazione di applicazioni Azure Active Directory per le comunicazioni da dispositivo a servizio.
management.azure.com | Creazione di applicazioni Azure Active Directory per le comunicazioni da dispositivo a servizio.
dc.services.visualstudio.com | Registrazione e monitoraggio
*.vault.azure.net | Gestisci i segreti in Azure Key Vault durante la comunicazione tra il dispositivo e il servizio.
aka.ms/* | Consente l'accesso a collegamenti aka.
https://download.microsoft.com/download/* | Consente i download dal sito di download Microsoft.



## <a name="assessment-port-requirements"></a>Valutazione-requisiti per le porte

Nella tabella seguente sono riepilogati i requisiti di porta per la valutazione.

**Dispositivo** | **Connection**
--- | ---
**Appliance** | Connessioni in ingresso sulla porta TCP 3389 per consentire le connessioni Desktop remoto al dispositivo.<br/> Connessioni in ingresso sulla porta 44368 per accedere in remoto all'app di gestione Appliance usando l'URL:``` https://<appliance-ip-or-name>:44368 ```<br/> Connessioni in uscita sulle porte 443, 5671 e 5672 per inviare i metadati di individuazione e prestazioni a Azure Migrate.
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
| **Avvio UEFI**                  | Le macchine virtuali con avvio UEFI non sono supportate per la migrazione.  |
| **Dimensioni disco**                  | 2 TB per il disco del sistema operativo, 4 TB per i dischi dati.
| **Numero disco** | Un massimo di 16 dischi per macchina virtuale.
| **Dischi/volumi crittografati**    | Non supportato per la migrazione. |
| **Dischi RDM/passthrough**      | Non supportato per la migrazione. |
| **Disco condiviso** | Le macchine virtuali che usano dischi condivisi non sono supportate per la migrazione.
| **NFS**                        | I volumi NFS montati come volumi nelle macchine virtuali non verranno replicati. |
| **ISCSI**                      | Le macchine virtuali con destinazioni iSCSI non sono supportate per la migrazione.
| **Disco di destinazione**                | È possibile eseguire la migrazione solo a macchine virtuali di Azure con Managed Disks. |
| **IPv6** | Non supportati.
| **Gruppo NIC** | Non supportati.
| **Azure Site Recovery** | Non è possibile eseguire la replica usando Azure Migrate migrazione del server se la macchina virtuale è abilitata per la replica con Azure Site Recovery.





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




## <a name="next-steps"></a>Passaggi successivi

[Preparare la valutazione della macchina virtuale Hyper-V](tutorial-prepare-hyper-v.md) per la migrazione.
