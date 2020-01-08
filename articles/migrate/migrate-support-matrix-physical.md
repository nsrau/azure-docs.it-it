---
title: Supporto per la valutazione/migrazione di server fisici con Azure Migrate
description: Viene riepilogato il supporto per la valutazione/migrazione di server fisici con Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 9e749297d831aeae7d785a9a9a29bea1f8c6d5e3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454625"
---
# <a name="support-matrix-for-physical-server-assessment-and-migration"></a>Matrice di supporto per la valutazione e la migrazione di server fisici

È possibile usare il [servizio Azure migrate](migrate-overview.md) per valutare ed eseguire la migrazione di computer nel cloud Microsoft Azure. Questo articolo riepiloga le impostazioni e le limitazioni del supporto per la valutazione e la migrazione di server fisici locali.



## <a name="physical-server-scenarios"></a>Scenari di server fisici

Nella tabella sono riepilogati gli scenari supportati per i server fisici.

**Distribuzione** | **Dettagli***
--- | ---
**Valutazione di server fisici locali** | [Configurare](tutorial-prepare-physical.md) la prima valutazione.<br/><br/> [Eseguire](tutorial-assess-physical.md) una valutazione.
**Eseguire la migrazione di server fisici ad Azure** | [Provare](tutorial-migrate-physical-virtual-machines.md) a eseguire la migrazione ad Azure.


## <a name="azure-migrate-projects"></a>Progetti Azure Migrate

**Supporto** | **Dettagli**
--- | ---
**Autorizzazioni di Azure** | Per creare un progetto di Azure Migrate, è necessario disporre delle autorizzazioni Collaboratore o proprietario nella sottoscrizione.
**Server fisici** | Valutazione di un massimo di 35.000 server fisici in un singolo progetto. È possibile avere più progetti in una sottoscrizione di Azure. Un progetto può includere server fisici, macchine virtuali VMware e macchine virtuali Hyper-V, fino ai limiti di valutazione.
**Area geografica** | È possibile creare progetti Azure Migrate in diverse aree geografiche. Sebbene sia possibile creare progetti in aree geografiche specifiche, è possibile valutare o migrare i computer per altri percorsi di destinazione. L'area geografica del progetto viene usata solo per archiviare i metadati individuati.

  **Area geografica** | **Posizione di archiviazione dei metadati**
  --- | ---
  Azure per enti pubblici | Governo degli Stati Uniti - Virginia
  Asia Pacifico | Asia orientale o Asia sud-orientale
  Australia | Australia orientale o Australia sudorientale
  Brasile | Brasile meridionale
  Canada | Canada centrale o Canada orientale
  Europa | Europa settentrionale o Europa occidentale
  Francia | Francia centrale
  India | India centrale o India meridionale
  Giappone |  Giappone orientale o Giappone occidentale
  Corea | Corea centrale o Corea del sud
  Regno Unito | Regno Unito meridionale o Regno Unito occidentale
  Stati Uniti | Stati Uniti centrali o Stati Uniti occidentali 2


 > [!NOTE]
 > Il supporto per Azure per enti pubblici è attualmente disponibile solo per la [versione precedente](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) di Azure migrate.


## <a name="assessment-physical-server-requirements"></a>Valutazione-requisiti del server fisico

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione server fisico**       | Il server fisico può essere autonomo o distribuito in un cluster. |
| **Autorizzazioni**           | **Windows:** Configurare un account utente locale in tutti i server Windows che si desidera includere nell'individuazione. L'account utente deve essere aggiunto a questi gruppi: Desktop remoto utenti, Performance Monitor Users e Performance Log Users. <br/> **Linux:** È necessario un account radice nei server Linux che si desidera individuare. |
| **Sistema operativo** | Sono supportati tutti i sistemi operativi [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , tranne i seguenti:<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="assessment-appliance-requirements"></a>Valutazione-requisiti del dispositivo

Per la valutazione, Azure Migrate esegue un appliance leggero per individuare i server fisici e inviare i dati sulle prestazioni e i metadati del server a Azure Migrate. Il dispositivo può essere eseguito in un server fisico o in una macchina virtuale e viene configurato usando uno script di PowerShell che viene scaricato dal portale di Azure. La tabella seguente riepiloga i requisiti del dispositivo.

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione dell'appliance**   |  Lo script del programma di installazione del dispositivo può essere scaricato dal portale (in una cartella compressa). <br/> È possibile decomprimere la cartella ed eseguire lo script di PowerShell (AzureMigrateInstaller. ps1) in un server fisico dedicato o in una macchina virtuale per configurare l'appliance.<br/>  Il computer scelto per installare il dispositivo deve eseguire Windows Server 2016.<br/> Il computer richiede spazio sufficiente per allocare 16 GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione e un commute esterno per la macchina virtuale dell'appliance.<br/> Per l'appliance sono necessari un indirizzo IP statico o dinamico e l'accesso a Internet.
| **Progetto Azure Migrate**  |  Un appliance può essere associato a un singolo progetto.<br/> Un numero qualsiasi di Appliance può essere associato a un singolo progetto.<br/> È possibile valutare fino a 35.000 computer in un progetto.
| **Individuazione**              | Una singola appliance può individuare fino a 250 Server.
| **Gruppo di valutazione**       | È possibile aggiungere fino a 35.000 computer in un singolo gruppo.
| **Valutazione**             | È possibile valutare fino a 35.000 computer in un'unica valutazione.


## <a name="assessment-appliance-url-access"></a>Valutazione-accesso con URL dell'appliance

Per valutare le VM, il dispositivo Azure Migrate necessita della connettività Internet.

- Quando si distribuisce il dispositivo, Azure Migrate esegue un controllo della connettività agli URL riepilogati nella tabella seguente.
- Se si usa un proxy basato su URL, consentire l'accesso agli URL nella tabella, assicurandosi che il proxy risolva tutti i record CNAME ricevuti durante la ricerca degli URL.
- Se si dispone di un proxy di intercettazione, potrebbe essere necessario importare il certificato server dal server proxy al dispositivo.


**URL** | **Dettagli**  
--- | ---
*.portal.azure.com | Spostamento all'portale di Azure
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com  | Accedi alla tua sottoscrizione di Azure
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
**Server fisici** | **Windows:** Connessioni in ingresso sulla porta 443, porte WinRM 5985 (HTTP) e 5986 (HTTPS) per il pull dei metadati di configurazione e delle prestazioni dai server Windows. <br/> **Linux:**  Connessioni in ingresso sulla porta 22 (UDP) per eseguire il pull dei metadati di configurazione e delle prestazioni dai server Linux. |


## <a name="next-steps"></a>Passaggi successivi

[Preparare la valutazione del server fisico](tutorial-prepare-physical.md) per la valutazione e la migrazione dei server fisici.
