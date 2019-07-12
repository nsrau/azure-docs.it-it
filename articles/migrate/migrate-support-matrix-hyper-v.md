---
title: Matrice di supporto di Azure Migrate per la valutazione di Hyper-V e la migrazione
description: Vengono riepilogate le impostazioni e le limitazioni per la valutazione di Hyper-V e la migrazione usando il servizio Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/02/2019
ms.author: raynew
ms.openlocfilehash: f6edbe19429b38d68aea1f1ecfe426c9b2d194d0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811349"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Matrice di supporto per la valutazione di Hyper-V e la migrazione

È possibile usare la [servizio Azure Migrate](migrate-overview.md) valutare ed eseguire la migrazione di macchine nel cloud di Microsoft Azure. Questo articolo riepiloga le impostazioni del supporto e le limitazioni per la valutazione e la migrazione di macchine virtuali Hyper-V locali.



## <a name="hyper-v-scenarios"></a>Scenari di Hyper-V

La tabella riepiloga gli scenari supportati per le macchine virtuali Hyper-V.

**Distribuzione** | **Dettagli*** 
--- | --- 
**Valutare le macchine virtuali Hyper-V in locale** | [Configurare](tutorial-prepare-hyper-v.md) la prima valutazione.<br/><br/> [Eseguire](scale-hyper-v-assessment.md) una valutazione su larga scala.
**Eseguire la migrazione di macchine virtuali Hyper-V ad Azure** | [Provare](tutorial-migrate-hyper-v.md) migrazione ad Azure.

    

## <a name="azure-migrate-projects"></a>Progetti di Azure Migrate

**Supporto** | **Dettagli**
--- | ---
Autorizzazioni di Azure | Sono necessarie autorizzazioni di proprietario o collaboratore nella sottoscrizione per creare un progetto Azure Migrate.
VM Hyper-V | Consente di valutare fino a 10.000 host Hyper-v in un singolo progetto.

Un progetto può includere sia macchine virtuali VMware e le macchine virtuali Hyper-V, fino ai limiti di valutazione.


## <a name="assessment-hyper-v-host-requirements"></a>Requisiti host Assessment Hyper-V

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione di host**       | L'host Hyper-V può essere autonomo o distribuito in un cluster. |
| **Autorizzazioni**           | Sono necessarie autorizzazioni di amministratore nell'host Hyper-V. |
| **Sistema operativo host** | Windows Server 2016 o Windows Server 2012 R2.<br/> Non è possibile valutare le macchine virtuali presenti negli host di Hyper-V che eseguono Windows Server 2019. |
| **Comunicazione remota di PowerShell**   | Deve essere abilitata in ogni host. |
| **Replica Hyper-V**       | Se si usa la Replica Hyper-V (o si dispone di più macchine virtuali con gli stessi identificatori della macchina virtuale) e individuare entrambi l'originale e replicate le macchine virtuali con Azure Migrate, la valutazione generata da Azure Migrate potrebbe non essere accurata. |


## <a name="assessment-hyper-v-vm-requirements"></a>Requisiti per le VM della valutazione-Hyper-V

| **Supporto**                  | **Dettagli**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Tutti i [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) i sistemi operativi supportati da Azure. |
| **Autorizzazioni**           | Sono necessarie autorizzazioni di amministratore in ogni macchina virtuale Hyper-V che si desidera valutare. |
| **Integration Services**       | [Servizi di integrazione Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) deve essere in esecuzione nelle macchine virtuali di cui viene valutato, per acquisire informazioni sul sistema operativo. |
| **Modifiche necessarie per Azure** | Alcune macchine virtuali possono richiedere modifiche, in modo che possano eseguire in Azure. Azure Migrate rende tali modifiche automaticamente per i sistemi operativi seguenti:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7,8<br/><br/> Per altri sistemi operativi, è necessario apportare le modifiche manualmente prima della migrazione. Gli articoli pertinenti contengono le istruzioni su come eseguire questa operazione. |
| **Avvio di Linux**                 | Se /boot si trova in una partizione dedicata, dovrebbero risiedere su disco del sistema operativo e non essere distribuito tra più dischi.<br/> Se /boot fa parte della partizione radice (/), quindi la partizione '/' deve essere il disco del sistema operativo e non su altri dischi. |
| **Avvio UEFI**                  | Le macchine virtuali con UEFI boot non sono supportate per la migrazione. |
| **I dischi/volumi crittografati**    | Le macchine virtuali con dischi/volumi crittografati non sono supportate per la migrazione. |
| **Dischi RDM/pass-through**      | Se le macchine virtuali hanno dischi RDM o pass-through, questi dischi non verranno replicati in Azure. |
| **NFS**                        | Non è possibile replicare volumi NFS montati come volumi nelle macchine virtuali. |
| **Disco di destinazione**                | Azure Migrate valutazioni consigliano la migrazione delle macchine virtuali di Azure con managed disks solo. |


## <a name="assessment-appliance-requirements"></a>Requisiti di valutazione di appliance

Per la valutazione, Azure Migrate esegue un'appliance leggera per individuare le macchine virtuali Hyper-V e inviare dati di metadati e le prestazioni della macchina virtuale in Azure Migrate. L'appliance viene eseguita in una macchina virtuale Hyper-V e si configura utilizzo di un disco rigido virtuale Hyper-V compresso scaricato dal portale di Azure. Nella tabella seguente vengono riepilogati i requisiti di appliance.

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Progetto Azure Migrate**  |  Un dispositivo può essere associato a un singolo progetto.<br/> È possibile individuare fino a 5000 macchine virtuali Hyper-V con una singola appliance.
| **Limitazioni di Hyper-V**    |  Si distribuisce il dispositivo come una macchina virtuale Hyper-V.<br/> Macchina virtuale fornita l'appliance si trova la macchina virtuale Hyper-V versione 5.0.<br/> L'host di macchina virtuale deve essere in esecuzione Windows Server 2012 R2 o versione successiva.<br/> È necessario spazio sufficiente per allocare 16 GB di RAM, 4 processori virtuali, e passare 1 esterno per l'appliance della macchina virtuale.<br/> Appliance richiede un indirizzo IP statico o dinamico e l'accesso a internet.
| **Appliance di Hyper-V**      |  L'appliance è configurata come una macchina virtuale Hyper-V.<br/> Il disco rigido virtuale specificato per il download è la macchina virtuale Hyper-V versione 5.0.
| **Host**                   | L'host di macchina virtuale in esecuzione la macchina virtuale appliance deve essere in esecuzione Windows Server 2012 R2 o versione successiva.<br/> È necessario spazio sufficiente per allocare RAM, 4 processori virtuali e un commutatore esterno 16 GB per macchina virtuale dell'appliance.<br/> Appliance richiede un indirizzo IP statico o dinamico e l'accesso a internet. |
| **Supporto della migrazione**      | Per avviare la replica delle macchine, il servizio di migrazione Gateway nell'appliance deve essere 1.18.7141.12919 o versione successiva. Accedere all'app web di appliance per controllare la versione. |

## <a name="assessment-appliance-url-access"></a>Accesso con URL della valutazione di appliance

Per valutare le macchine virtuali, l'appliance Azure Migrate richiede la connettività internet.

- Quando si distribuisce l'appliance, Azure Migrate esegue una verifica della connettività agli URL riepilogati nella tabella riportata di seguito.
- Se si usa un firewall.proxy basato su URL, consentire l'accesso agli URL nella tabella, assicurandosi che il proxy venga risolto alcun record CNAME ricevuti durante la ricerca degli URL.
- Se si dispone di un proxy di intercettazione, occorre importare il certificato del server dal server proxy per l'appliance. 

    
**URL** | **Dettagli**  
--- | --- 
*.portal.azure.com | Navigazione nel portale di Azure
*.windows.net | Accedere alla sottoscrizione di Azure.
*.microsoftonline.com | Applicazioni di creazione di Azure Active Directory per appliance per le comunicazioni tra servizi.
management.azure.com | Applicazioni di creazione di Azure Active Directory per appliance per le comunicazioni tra servizi.
dc.services.visualstudio.com | Registrazione e monitoraggio 
*.vault.azure.net | Gestire i segreti in Azure Key Vault durante la comunicazione tra il dispositivo e il servizio.


## <a name="assessment-port-requirements"></a>Requisiti delle porte di valutazione

Nella tabella seguente vengono riepilogati i requisiti di porta per la valutazione.

**Dispositivo** | **Connection**
--- | --- 
**Appliance** | Connessioni in ingresso sulla porta TCP 3389 per consentire le connessioni desktop remoto all'appliance.<br/> Connessioni sulla porta 44368 per accedere in remoto le app di gestione di appliance usando l'URL in ingresso: https://<appliance-ip-or-name>:44368<br/> Connessioni in uscita sulla porta 443 per inviare i metadati di individuazione e le prestazioni di Azure Migrate.
**Host/cluster Hyper-V** | Connessioni sulle porte WinRM 5985 (HTTP) e 5986 (HTTPS) per eseguire il pull dei metadati di configurazione e sulle prestazioni delle macchine virtuali Hyper-V Usa una sessione di modello CIM (Common Information) in ingresso.

## <a name="migration-hyper-v-host-requirements"></a>Requisiti di migrazione Hyper-V host

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione di host**       | L'host Hyper-V può essere autonomo o distribuito in un cluster. |
| **Autorizzazioni**           | Sono necessarie autorizzazioni di amministratore nell'host Hyper-V. |
| **Sistema operativo host** | Windows Server 2019, Windows Server 2016 o Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>Requisiti delle macchine Virtuali di migrazione Hyper-V

| **Supporto**                  | **Dettagli**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Tutti i [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) i sistemi operativi supportati da Azure. |
| **Autorizzazioni**           | Sono necessarie autorizzazioni di amministratore in ogni macchina virtuale Hyper-V che si desidera valutare. |
| **Integration Services**       | [Servizi di integrazione Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) deve essere in esecuzione nelle macchine virtuali di cui viene valutato, per acquisire informazioni sul sistema operativo. |
| **Modifiche necessarie per Azure** | Alcune macchine virtuali possono richiedere modifiche, in modo che possano eseguire in Azure. Azure Migrate rende tali modifiche automaticamente per i sistemi operativi seguenti:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7,8<br/><br/> Per altri sistemi operativi, è necessario apportare le modifiche manualmente prima della migrazione. Gli articoli pertinenti contengono le istruzioni su come eseguire questa operazione. |
| **Avvio di Linux**                 | Se /boot si trova in una partizione dedicata, dovrebbero risiedere su disco del sistema operativo e non essere distribuito tra più dischi.<br/> Se /boot fa parte della partizione radice (/), quindi la partizione '/' deve essere il disco del sistema operativo e non su altri dischi. |
| **Avvio UEFI**                  | Le macchine virtuali con UEFI boot non sono supportate per la migrazione. |
| **I dischi/volumi crittografati**    | Le macchine virtuali con dischi/volumi crittografati non sono supportate per la migrazione. |
| **Dischi RDM/pass-through**      | Se le macchine virtuali hanno dischi RDM o pass-through, questi dischi non verranno replicati in Azure. |
| **NFS**                        | Non è possibile replicare volumi NFS montati come volumi nelle macchine virtuali. |
| **Disco di destinazione**                | È possibile eseguire la migrazione alle macchine virtuali di Azure con managed disks solo. |




## <a name="migration-hyper-v-host-url-access"></a>Accesso con URL host di migrazione Hyper-V

La tabella seguente riepiloga i requisiti di accesso URL per gli host Hyper-V.

**URL** | **Dettagli**  
--- | ---
login.microsoftonline.com | Identità e controllo gestione dell'accesso mediante Active Directory.
*.backup.windowsazure.com | Trasferimento dei dati di replica e coordinamento.
*.hypervrecoverymanager.windowsazure.com | Connettersi all'URL del servizio Azure Migrate.
*.blob.core.windows.net | Caricare i dati per gli account di archiviazione.
dc.services.visualstudio.com | Caricare i log dell'app usati per il monitoraggio interno.
time.windows.com | Verifica la sincronizzazione dell'ora tra sistema e ora globale.

## <a name="migration-port-access"></a>Accesso alla porta di migrazione

Nella tabella seguente vengono riepilogati i requisiti di porta nelle macchine virtuali e host Hyper-V per la migrazione della macchina virtuale.

**Dispositivo** | **Connection**
--- | --- 
Host Hyper-V e macchine virtuali | Le connessioni in uscita su HTTPS la porta 443 per inviare dati di replica della macchina virtuale di Azure Migrate.

  
## <a name="migration-vm-disk-support"></a>Supporto per dischi di macchina virtuale di migrazione 

**Supporto** | **Dettagli**
--- | ---
Dischi migrati | Le macchine virtuali possono essere eseguite solo a managed disks (HHD standard, premium SSD) in Azure.
   

## <a name="next-steps"></a>Passaggi successivi

[Preparare per la valutazione delle VM Hyper-V](tutorial-prepare-hyper-v.md) per la migrazione.




 
