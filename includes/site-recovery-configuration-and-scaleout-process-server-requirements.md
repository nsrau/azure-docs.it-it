---
title: includere il file
description: includere file
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 07/08/2020
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 60012f79c3c04a4ff14c4a7f0609b6940d3402c4
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86545094"
---
**Requisiti del server di configurazione e di elaborazione**


## <a name="hardware-requirements"></a>Requisiti hardware

**Componente** | **Requisito** 
--- | ---
Core CPU | 8 
RAM | 16 GB
Numero di dischi | 3, inclusi disco del sistema operativo, disco della cache del server di elaborazione e unità di conservazione per il failback 
Spazio libero su disco (cache del server di elaborazione) | 600 GB
Spazio libero su disco (disco di conservazione) | 600 GB
 | 

## <a name="software-requirements"></a>Requisiti software

**Componente** | **Requisito** 
--- | ---
Sistema operativo | Windows Server 2012 R2 <br> Windows Server 2016
Impostazioni locali del sistema operativo | Inglese (en-*)
Ruoli di Windows Server | Non abilitare questi ruoli: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Criteri di gruppo | Non abilitare questi criteri di gruppo: <br> - Impedisci accesso al prompt dei comandi <br> - Impedisci accesso agli strumenti di modifica del Registro di sistema <br> - Logica di attendibilità per file allegati <br> - Attiva l'esecuzione di script <br> [Altre informazioni](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Nessun sito Web predefinito preesistente <br> - Nessun sito Web o applicazione preesistente in ascolto sulla porta 443 <br>- Abilitare l'[autenticazione anonima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Abilitare l'impostazione di [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 
FIPS (Federal Information Processing Standards) | Non abilitare la modalità FIPS
|

## <a name="network-requirements"></a>Requisiti di rete

**Componente** | **Requisito** 
--- | --- 
Tipo di indirizzo IP | Statico 
Porte | 443 (orchestrazione del canale di controllo)<br>9443 (trasporto dei dati) 
Tipo di scheda di interfaccia di rete | VMXNET3 (se il server di configurazione è una macchina virtuale VMware)
 |
**Accesso a Internet** (il server deve accedere agli URL seguenti, direttamente o tramite proxy):|
\*.backup.windowsazure.com | Usato per il coordinamento e il trasferimento dei dati replicati
\*.blob.core.windows.net | Consente di accedere all'account di archiviazione in cui sono archiviati i dati replicati. È possibile specificare l'URL specifico dell'account di archiviazione della cache.
\*.hypervrecoverymanager.windowsazure.com | Usato per il coordinamento e le operazioni di gestione della replica
https:\//login.microsoftonline.com | Usato per il coordinamento e le operazioni di gestione della replica 
time.nist.gov | Usato per controllare la sincronizzazione dell'ora tra sistema e ora globale
time.windows.com | Usato per controllare la sincronizzazione dell'ora tra sistema e ora globale
| <ul> <li> https:\//management.azure.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https: \/ /login.Live.com </li><li> https: \/ /Graph.Windows.NET </li><li> https:\//login.windows.net </li><li> *. services.visualstudio.com (facoltativo) </li><li> https: \/ /www.Live.com </li><li> https: \/ /www.Microsoft.com </li></ul> | L'installazione di OVF richiede l'accesso a questi URL aggiuntivi. Vengono usati per il controllo di accesso e la gestione delle identità Azure Active Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | Per completare il download di MySQL. </br> In alcune aree, il download potrebbe essere reindirizzato all'URL della rete CDN. Verificare che anche l'URL della rete CDN sia incluso nell'elenco elementi consentiti, se necessario.
|

> [!NOTE]
> Se si dispone di [collegamenti privati](../articles/site-recovery/hybrid-how-to-enable-replication-private-endpoints.md) per la connettività a Site Recovery insieme di credenziali, non è necessario alcun accesso a Internet aggiuntivo per il server di configurazione. Un'eccezione a questo problema si verifica durante la configurazione del computer CS con il modello OVA, sarà necessario accedere agli URL seguenti sopra e sopra accesso privato a collegamento https://management.azure.com , https://www.live.com e https://www.microsoft.com . Se non si vuole consentire l'accesso a questi URL, configurare il CS usando Unified Installer.

## <a name="required-software"></a>Requisiti software

**Componente** | **Requisito** 
--- | ---
VMware vSphere PowerCLI | [PowerCLI versione 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deve essere installato se il server di configurazione è in esecuzione in una macchina virtuale VMware.
MYSQL | MySQL deve essere installato. È possibile eseguire l'installazione manualmente o con Site Recovery. (Per altre informazioni, fare riferimento a [Configurazione delle impostazioni](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings))
|

## <a name="sizing-and-capacity-requirements"></a>Requisiti di dimensioni e capacità

La tabella seguente riepiloga i requisiti di capacità del server di configurazione. Se si esegue la replica di più macchine virtuali VMware, rivedere le [considerazioni sulla pianificazione della capacità](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) ed eseguire lo [strumento Azure Site Recovery Deployment Planner](../articles/site-recovery/site-recovery-deployment-planner.md).


**CPU** | **Memoria** | **Disco della cache** | **Frequenza di modifica dei dati** | **Computer replicati**
--- | --- | --- | --- | ---
8 vCPU<br/><br/> 2 socket * 4 core \@ 2,5 GHz | 16 GB | 300 GB | 500 GB o inferiore | < 100 computer
12 vCPU<br/><br/> 2 socket * 6 core \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | Da 100 a 150 computer
16 vCPU<br/><br/> 2 socket * 8 core \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200 computer
|

