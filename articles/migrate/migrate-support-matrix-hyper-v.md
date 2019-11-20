---
title: Supporto per la valutazione/migrazione di Hyper-V in Azure Migrate
description: Informazioni sul supporto per la valutazione e la migrazione di Hyper-V con Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 6562d3f15d080a3bbc54a9985c12eae5908a9980
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186648"
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
VM Hyper-V | Valutazione di un massimo di 35.000 macchine virtuali Hyper-V in un singolo progetto. È possibile avere più progetti in una sottoscrizione di Azure. Un progetto può includere sia macchine virtuali VMware che macchine virtuali Hyper-V, fino ai limiti di valutazione.
Area geografica | [Esaminare](migrate-support-matrix.md#supported-geographies) le aree geografiche supportate.


## <a name="assessment-hyper-v-host-requirements"></a>Valutazione-requisiti dell'host Hyper-V

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione host**       | L'host Hyper-V può essere autonomo o distribuito in un cluster. |
| **autorizzazioni**           | Sono necessarie autorizzazioni di amministratore per l'host Hyper-V. <br/> In alternativa, se non si desidera assegnare autorizzazioni di amministratore, creare un account utente locale o di dominio e aggiungere l'utente a questi gruppi: utenti di gestione remota, amministratori di Hyper-V e performance monitor. |
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
| **Distribuzione dell'appliance**   |  Si distribuisce l'appliance come macchina virtuale Hyper-V.<br/> La macchina virtuale dell'appliance fornita da Azure Migrate è la macchina virtuale Hyper-V 5,0.<br/> L'host Hyper-V deve eseguire Windows Server 2012 R2 o versione successiva.<br/> L'host necessita di spazio sufficiente per allocare 16 GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione e un commute esterno per la macchina virtuale dell'appliance.<br/> Per l'appliance sono necessari un indirizzo IP statico o dinamico e l'accesso a Internet.
| **Progetto Azure Migrate**  |  Un appliance può essere associato a un singolo progetto.<br/> Un numero qualsiasi di Appliance può essere associato a un singolo progetto.<br/> È possibile valutare fino a 35.000 VM in un progetto.
| **Host Hyper-V**          | Un appliance può connettersi a un massimo di 300 host Hyper-V.
| **Individuazione**              | Un singolo dispositivo può individuare fino a 5000 VM.
| **Gruppo di valutazione**       | È possibile aggiungere fino a 35.000 computer in un singolo gruppo.
| **Valutazione**             | È possibile valutare fino a 35.000 VM in un'unica valutazione.



## <a name="assessment-appliance-url-access"></a>Valutazione-accesso con URL dell'appliance

Per valutare le VM, il dispositivo Azure Migrate necessita della connettività Internet.

- Quando si distribuisce il dispositivo, Azure Migrate esegue un controllo della connettività agli URL riepilogati nella tabella seguente.
- Se si usa un proxy basato su URL, consentire l'accesso agli URL nella tabella, assicurandosi che il proxy risolva tutti i record CNAME ricevuti durante la ricerca degli URL.
- Se si dispone di un proxy di intercettazione, potrebbe essere necessario importare il certificato server dal server proxy al dispositivo.


**URL** | **Dettagli**  
--- | ---
*.portal.azure.com | Spostamento all'portale di Azure
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Accedere alla sottoscrizione di Azure.
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
**Appliance** | Connessioni in ingresso sulla porta TCP 3389 per consentire le connessioni Desktop remoto al dispositivo.<br/> Connessioni in ingresso sulla porta 44368 per accedere in remoto all'app di gestione Appliance usando l'URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Connessioni in uscita sulle porte 443, 5671 e 5672 per inviare i metadati di individuazione e prestazioni a Azure Migrate.
**Host/cluster Hyper-V** | Connessioni in ingresso sulle porte WinRM 5985 (HTTP) e 5986 (HTTPS) per eseguire il pull dei metadati di configurazione e delle prestazioni delle VM Hyper-V usando una sessione di Common Information Model (CIM).

## <a name="migration-limitations"></a>Migrazione: limitazioni
È possibile selezionare fino a 10 macchine virtuali contemporaneamente per la replica. Se si desidera eseguire la migrazione di più computer, eseguire la replica in gruppi di 10.

## <a name="migration-hyper-v-host-requirements"></a>Migrazione: requisiti dell'host Hyper-V

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione host**       | L'host Hyper-V può essere autonomo o distribuito in un cluster. |
| **autorizzazioni**           | Sono necessarie autorizzazioni di amministratore per l'host Hyper-V. |
| **Sistema operativo host** | Windows Server 2019, Windows Server 2016 o Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>Migrazione: requisiti della macchina virtuale Hyper-V

| **Supporto**                  | **Dettagli**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Tutti i sistemi operativi [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) supportati da Azure. |
| **autorizzazioni**           | Sono necessarie autorizzazioni di amministratore per ogni macchina virtuale Hyper-V che si desidera valutare. |
| **Integration Services**       | Per acquisire le informazioni sul sistema operativo, è necessario che [Hyper-V Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) sia in esecuzione in macchine virtuali valutate. |
| **Modifiche necessarie per Azure** | Alcune macchine virtuali potrebbero richiedere alcune modifiche per la corretta esecuzione in Azure. Azure Migrate apporta automaticamente queste modifiche per i sistemi operativi seguenti:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> Per altri sistemi operativi, è necessario apportare modifiche manualmente prima della migrazione. Gli articoli pertinenti contengono istruzioni su come eseguire questa operazione. |
| **Avvio di Linux**                 | Se/boot si trova in una partizione dedicata, deve risiedere nel disco del sistema operativo e non essere distribuito tra più dischi.<br/> Se/boot fa parte della partizione radice (/), la partizione '/' deve trovarsi nel disco del sistema operativo e non si estende su altri dischi. |
| **Avvio UEFI**                  | La macchina virtuale di cui è stata eseguita la migrazione in Azure verrà automaticamente convertita in una macchina virtuale di avvio BIOS. Nella macchina virtuale deve essere in esecuzione solo Windows Server 2012 e versioni successive. Il disco del sistema operativo deve avere fino a cinque partizioni o meno e la dimensione del disco del sistema operativo deve essere inferiore a 300 GB.
  |
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
