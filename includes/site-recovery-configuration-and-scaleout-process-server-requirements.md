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
ms.openlocfilehash: ade53ba29d165b3b33ef25dabda25c4e60022608
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2018
ms.locfileid: "40133385"
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
Accesso a Internet | Il server deve poter accedere a questi URL (direttamente o tramite proxy): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> - https:\//management.azure.com <br> - *.services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF necessita anche di accedere agli URL seguenti: <br> - https:\//login.microsoftonline.com <br> - https:\//secure.aadcdn.microsoftonline-p.com <br> - https:\//login.live.com  <br> - https:\//auth.gfx.ms <br> - https:\//graph.windows.net <br> - https:\//login.windows.net <br> - https:\//www.live.com <br> - https:\//www.microsoft.com <br> - https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
Porte | 443 (orchestrazione del canale di controllo)<br>9443 (trasporto dei dati) 
Tipo di scheda di interfaccia di rete | VMXNET3 (se il server di configurazione è una macchina virtuale VMware)
 | 
**SOFTWARE DA INSTALLARE** | 
VMware vSphere PowerCLI | [PowerCLI versione 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deve essere installato se il server di configurazione è in esecuzione in una macchina virtuale VMware.
MYSQL | MySQL deve essere installato. È possibile eseguire l'installazione manualmente o con Site Recovery.

**Requisiti di dimensione del server di configurazione/elaborazione**

**CPU** | **Memoria** | **Disco cache** | **Frequenza di modifica dei dati** | **Computer replicati**
--- | --- | --- | --- | ---
8 vCPU<br/><br/> 2 socket * 4 core \@ 2,5 GHz | 16 GB | 300 GB | 500 GB o inferiore | < 100 computer
12 vCPU<br/><br/> 2 socket * 6 core \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | Da 100 a 150 computer
16 vCPU<br/><br/> 2 socket * 8 core \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200 computer

