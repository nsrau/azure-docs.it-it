---
title: SKU di macchine virtuali supportate per Criteri di Azure
description: Un articolo che descrive gli SKU delle macchine virtuali supportati (per editore, offerta di immagini e SKU immagine) supportati per i criteri di Azure predefiniti forniti da Backup
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1b6a94b0f57ecfea946654c6cae38ac335335e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980114"
---
# <a name="supported-vm-skus-for-azure-policy"></a>SKU di macchine virtuali supportate per Criteri di Azure

Backup di Azure offre criteri predefiniti (tramite Criteri di Azure) che possono essere assegnati a tutte le macchine virtuali di **Azure in una posizione specificata all'interno**di una sottoscrizione o di un gruppo di risorse. Quando questo criterio viene assegnato a un determinato ambito, tutte le nuove macchine virtuali create in tale ambito vengono configurate automaticamente per il backup in un **insieme di credenziali esistente nella stessa posizione e sottoscrizione.** La tabella seguente elenca tutti gli SKU delle macchine virtuali supportati da questo criterio.

### <a name="supported-vms"></a>**VM supportate**

**Nome criterio:** Configurare il backup nelle macchine virtuali di una posizione in un insieme di credenziali centrale esistente nella stessa posizione

Autore di immagini | Immagine Offerta | SKU dell'immagine
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2008 R2 SP (2008-R2-SP1-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 Datacenter (2012-Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 Datacenter (2012-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2 Datacenter (2012-R2-Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 R2 Datacenter (2012-R2-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter (2016-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter - Server Core (2016-Datacenter-Server-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter - Server Core (2016-Datacenter-Server-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter (2016-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core con contenitori (2016-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | Host sessione Desktop remoto di Windows Server 2016 2016 (2016-Datacenter-with-RDSH)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (2019-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core con contenitori (2019-Datacenter-Core-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core con contenitori (2019-Datacenter-Core-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter (2019-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter con contenitori (2019-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter con contenitori (2019-Datacenter-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (zh-cn) (2019-Datacenter-zhcn)
MicrosoftWindowsServer | WindowsServerSemiAnnual (informazioni in stato di errore) | Datacenter-Core-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual (informazioni in stato di errore) | Datacenter-Core-1709-with-Containers-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual (informazioni in stato di errore) | Datacenter-Core-1803-with-Containers-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | Tutti gli SKU delle immagini
MicrosoftSQLServer | SQL2016SP1-WS2016 | Tutti gli SKU delle immagini
MicrosoftSQLServer | SQL2016-WS2016  | Tutti gli SKU delle immagini
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | Tutti gli SKU delle immagini
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | Tutti gli SKU delle immagini
MicrosoftSQLServer | SQL2016-WS2012R2 | Tutti gli SKU delle immagini
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Tutti gli SKU delle immagini
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | Tutti gli SKU delle immagini
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | Tutti gli SKU delle immagini
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | Tutti gli SKU delle immagini
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | Tutti gli SKU delle immagini
MicrosoftRServer | Server di lavoro-WS2016 | Tutti gli SKU delle immagini
MicrosoftVisualStudio | VisualStudio | Tutti gli SKU delle immagini
MicrosoftVisualStudio | WINDOWS | Tutti gli SKU delle immagini
MicrosoftDynamicsAX | Dynamics  | Pre-Req-AX7-Onebox-U8
microsoft-ads | windows-data-science-vm | Tutti gli SKU delle immagini
MicrosoftWindowsDesktop | Windows-10 | Tutti gli SKU delle immagini
RedHat | RHEL | 6.7, 6.8, 6.9, 6.10, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7
RedHat | RHEL-SAP-HANA | 6.7, 7.2, 7.3
SUSE | SLES | 12.X (in vi e dei min
SUSE | SLES-HPC | 12.X (in vi e dei min
SUSE | Priorità SLES-HPC | 12.X (in vi e dei min
SUSE | SLES-SAP | 12.X (in vi e dei min
SUSE | SLES-SAP-BYOS | 12.X (in vi e dei min
SUSE | Priorità SLES | 12.X (in vi e dei min
SUSE | SLES-BYOS | 12.X (in vi e dei min
SUSE | SLES-SAPCAL | 12.X (in vi e dei min
SUSE | SLES-Standard | 12.X (in vi e dei min
Canonical | UbuntuServer | 14.04.0-LTS
Canonical | UbuntuServer | 14.04.1-LTS
Canonical | UbuntuServer | 14.04.2-LTS
Canonical | UbuntuServer | 14.04.3-LTS
Canonical | UbuntuServer | 14.04.4-LTS
Canonical | UbuntuServer | 14.04.5-DAILY-LTS
Canonical | UbuntuServer | 14.04.5-LTS
Canonical | UbuntuServer | 16.04-DAILY-LTS
Canonical | UbuntuServer | 16.04-LTS
Canonical | UbuntuServer | 16.04.0-LTS
Canonical | UbuntuServer | 18.04-DAILY-LTS
Canonical | UbuntuServer | 18.04-LTS
Oracle | Oracle-Linux | 6.8, 6.9, 6.10, 7.3, 7.4, 7.5, 7.6
OpenLogic | CentOS | 6.X, 7.X
OpenLogic | CentOS-LVM | 6.X, 7.X
OpenLogic | CentOS-SRIOV | 6.X, 7.X
cloudera | cloudera-centos-os | 7.X (in cla).
