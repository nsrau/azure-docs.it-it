---
title: File di inclusione
description: File di inclusione
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: afeae4af9b41bf434b26833a3bd927118a4697ae
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180373"
---
**Requisiti del server di configurazione/elaborazione per la replica di server fisici**

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
IIS | - Nessun sito Web predefinito preesistente <br> - Nessun sito Web o applicazione preesistente in ascolto sulla porta 443 <br>- Abilitare l'[autenticazione anonima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Abilitare l'impostazione di [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx).
Tipo di indirizzo IP | statico 
| 
**IMPOSTAZIONI DI ACCESSO** | 
MYSQL | MySQL deve essere installato nel server di configurazione. È possibile eseguire l'installazione manualmente o con Site Recovery durante la distribuzione. Per l'installazione con Site Recovery, verificare che il computer possa raggiungere http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
URL | Il server di configurazione deve poter accedere a questi URL (direttamente o tramite proxy):<br/><br/> Azure AD: `login.microsoftonline.com`; `login.microsoftonline.us`; `*.accesscontrol.windows.net`<br/><br/> Trasferimento dati di replica: `*.backup.windowsazure.com`; `*.backup.windowsazure.us`<br/><br/> Gestione delle repliche: `*.hypervrecoverymanager.windowsazure.com`; `*.hypervrecoverymanager.windowsazure.us`; `https://management.azure.com`; `*.services.visualstudio.com`<br/><br/> Accesso alle risorse di archiviazione `*.blob.core.windows.net`; `*.blob.core.usgovcloudapi.net`<br/><br/> Sincronizzazione dell'ora: `time.nist.gov`; `time.windows.com`<br/><br/> Dati di telemetria (facoltativo): `dc.services.visualstudio.com`
Firewall | Le regole del firewall basate sull'indirizzo IP devono consentire la comunicazione con gli URL di Azure. Per semplificare e limitare gli intervalli IP, è consigliabile usare il filtraggio degli URL.<br/><br/>**Per gli indirizzi IP commerciali:**<br/><br/>- Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).<br/><br/> - Consentire gli intervalli di indirizzi IP per gli Stati Uniti occidentali (usati per il Servizio di controllo di accesso e la gestione delle identità).<br/><br/> - Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione per supportare gli URL necessari per Azure Active Directory, il backup, la replica e l'archiviazione.<br/><br/> **Per gli indirizzi IP per enti pubblici:**<br/><br/> - Consentire gli intervalli IP del data center di Azure per enti pubblici e la porta HTTPS (443).<br/><br/> - Consentire gli intervalli di indirizzi IP per le aree di Azure per enti pubblici degli Stati Uniti (Virginia, Texas, Arizona e Iowa) per supportare gli URL necessari per Azure Active Directory, il backup, la replica e l'archiviazione.
Porte | Consentire 443 (orchestrazione del canale di controllo)<br/><br/> Consentire 9443 (trasporto dei dati) 


**Requisiti di dimensione del server di configurazione/elaborazione**

**CPU** | **Memoria** | **Disco cache** | **Frequenza di modifica dei dati** | **Computer replicati**
--- | --- | --- | --- | ---
8 vCPU<br/><br/> 2 socket * 4 core \@ 2,5 GHz | 16 GB | 300 GB | 500 GB o inferiore | < 100 computer
12 vCPU<br/><br/> 2 socket * 6 core \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | Da 100 a 150 computer
16 vCPU<br/><br/> 2 socket * 8 core \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200 computer

