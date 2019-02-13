---
title: File di inclusione
description: File di inclusione
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/10/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 4346b347994f49774584caf31a96ff2f81fdc0e1
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55701217"
---
**Requisiti del server di configurazione/elaborazione**

**Componente** | **Requisito** 
--- | ---
**IMPOSTAZIONI HARDWARE** | 
Core CPU | 8 
RAM | 16 GB
Numero di dischi | 3, inclusi disco del sistema operativo, disco della cache del server di elaborazione e unità di conservazione per il failback 
Spazio libero su disco (cache del server di elaborazione) | 600 GB
Spazio libero su disco (disco di conservazione) | 600 GB
 | 
**IMPOSTAZIONI SOFTWARE** | 
Sistema operativo | Windows Server 2012 R2 <br> Windows Server 2016
Impostazioni locali del sistema operativo | Inglese (en-us)
Ruoli di Windows Server | Non abilitare questi ruoli: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Criteri di gruppo | Non abilitare questi criteri di gruppo: <br> - Impedisci accesso al prompt dei comandi <br> - Impedisci accesso agli strumenti di modifica del Registro di sistema <br> - Logica di attendibilità per file allegati <br> - Attiva l'esecuzione di script <br> [Altre informazioni](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Nessun sito Web predefinito preesistente <br> - Nessun sito Web o applicazione preesistente in ascolto sulla porta 443 <br>- Abilitare l'[autenticazione anonima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Abilitare l'impostazione di [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 
| 
**IMPOSTAZIONI DI RETE** | 
Tipo di indirizzo IP | statico 
Porte | 443 (orchestrazione del canale di controllo)<br>9443 (trasporto dei dati) 
Tipo di scheda di interfaccia di rete | VMXNET3 (se il server di configurazione è una macchina virtuale VMware)
 |
**Accesso a Internet** (Il server deve poter accedere agli  URL seguenti, direttamente o tramite proxy):|
\*.backup.windowsazure.com | Usato per il coordinamento e il trasferimento dei dati di replica
\*.store.core.windows.net | Usato per il coordinamento e il trasferimento dei dati replicati
\*.blob.core.windows.net | Usato per accedere all'account di archiviazione in cui sono archiviati i dati replicati
\*.hypervrecoverymanager.windowsazure.com | Usato per il coordinamento e le operazioni di gestione della replica
https:\//management.azure.com | Usato per il coordinamento e le operazioni di gestione della replica 
*.services.visualstudio.com | Utilizzato per scopi di telemetria (Facoltativo)
time.nist.gov | Usati per controllare la sincronizzazione tra ora di sistema e ora globale.
time.windows.com | Usati per controllare la sincronizzazione tra ora di sistema e ora globale.
- https:\//login.microsoftonline.com <br> - https:\//secure.aadcdn.microsoftonline-p.com <br> - https:\//login.live.com  <br> - https:\//graph.windows.net <br> - https:\//login.windows.net <br> - https:\//www.live.com <br> - https:\//www.microsoft.com | La configurazione OVF deve poter accedere a questi URL. Vengono usati per la gestione di identità e controllo di accesso da Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Per completare il download di MySQL
|
**SOFTWARE DA INSTALLARE** | 
VMware vSphere PowerCLI | [PowerCLI versione 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deve essere installato se il server di configurazione è in esecuzione in una macchina virtuale VMware.
MYSQL | MySQL deve essere installato. È possibile eseguire l'installazione manualmente o con Site Recovery. (Per altre informazioni, fare riferimento a [Configurazione delle impostazioni](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings))

**Requisiti di dimensione del server di configurazione/elaborazione**

**CPU** | **Memoria** | **Disco cache** | **Frequenza di modifica dei dati** | **Computer replicati**
--- | --- | --- | --- | ---
8 vCPU<br/><br/> 2 socket * 4 core \@ 2,5 GHz | 16 GB | 300 GB | 500 GB o inferiore | < 100 computer
12 vCPU<br/><br/> 2 socket * 6 core \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | Da 100 a 150 computer
16 vCPU<br/><br/> 2 socket * 8 core \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200 computer

