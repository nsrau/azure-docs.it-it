---
title: SKU di VM supportati per criteri di Azure
description: Articolo che descrive gli SKU di VM supportati (dall'editore, dall'offerta di immagine e dallo SKU di immagini) supportati per i criteri predefiniti di Azure forniti da backup
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1b6a94b0f57ecfea946654c6cae38ac335335e00
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980114"
---
# <a name="supported-vm-skus-for-azure-policy"></a>SKU di VM supportati per criteri di Azure

Backup di Azure fornisce un criterio predefinito (usando criteri di Azure) che può essere assegnato a **tutte le macchine virtuali di Azure in una posizione specificata all'interno di una sottoscrizione o di un gruppo di risorse**. Quando questo criterio viene assegnato a un determinato ambito, tutte le nuove macchine virtuali create in tale ambito vengono automaticamente configurate per il backup in un insieme di credenziali **esistente nello stesso percorso e nella stessa sottoscrizione**. La tabella seguente elenca tutti gli SKU di VM supportati da questo criterio.

### <a name="supported-vms"></a>**Macchine virtuali supportate**

**Nome criterio:** Configurare il backup nelle macchine virtuali di un percorso in un insieme di credenziali centrale esistente nello stesso percorso

Editore di immagini | Offerta immagine | SKU dell'immagine
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2008 R2 SP (2008-R2-SP1-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 Datacenter (2012-Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 Datacenter (2012-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2 Datacenter (2012-R2-Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 R2 Datacenter (2012-R2-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 datacenter (2016-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter-Server Core (2016-Datacenter-Server-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter-Server Core (2016-Datacenter-Server-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 datacenter (2016-datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core con contenitori (2016-Datacenter-con-contenitori)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 host sessione Desktop remoto 2016 (2016-datacenter-with-RDSH)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 datacenter (2019-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core (2019-datacenter-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core (2019-datacenter-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core con contenitori (2019-datacenter-Core-with-containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core con contenitori (2019-datacenter-Core-with-containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 datacenter (2019-datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter con contenitori (2019-Datacenter-con-container)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter con contenitori (2019-datacenter-with-containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 datacenter (zh-CN) (2019-datacenter-zhcn)
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-con-Containers-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1803-con-Containers-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | Tutti gli SKU di immagini
MicrosoftSQLServer | SQL2016SP1-WS2016 | Tutti gli SKU di immagini
MicrosoftSQLServer | SQL2016-WS2016  | Tutti gli SKU di immagini
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | Tutti gli SKU di immagini
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | Tutti gli SKU di immagini
MicrosoftSQLServer | SQL2016-WS2012R2 | Tutti gli SKU di immagini
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Tutti gli SKU di immagini
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | Tutti gli SKU di immagini
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | Tutti gli SKU di immagini
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | Tutti gli SKU di immagini
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | Tutti gli SKU di immagini
MicrosoftRServer | MLServer-WS2016 | Tutti gli SKU di immagini
MicrosoftVisualStudio | VisualStudio | Tutti gli SKU di immagini
MicrosoftVisualStudio | Windows | Tutti gli SKU di immagini
MicrosoftDynamicsAX | Dynamics  | Pre-req-AX7-OneBox-U8
microsoft-ads | Windows-Data-Science-VM | Tutti gli SKU di immagini
MicrosoftWindowsDesktop | Windows-10 | Tutti gli SKU di immagini
RedHat | RHEL | 6,7, 6,8, 6,9, 6,10, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7
RedHat | RHEL-SAP-HANA | 6,7, 7,2, 7,3
SUSE | SLES | 12. X
SUSE | SLES-HPC | 12. X
SUSE | SLES-HPC-priorità | 12. X
SUSE | SLES-SAP | 12. X
SUSE | SLES-SAP-BYOS | 12. X
SUSE | SLES-priorità | 12. X
SUSE | SLES-BYOS | 12. X
SUSE | SLES-SAPCAL | 12. X
SUSE | SLES-standard | 12. X
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
Canonical | UbuntuServer | 18,04-GIORNALIERA-LTS
Canonical | UbuntuServer | 18.04-LTS
Oracle | Oracle-Linux | 6,8, 6,9, 6,10, 7,3, 7,4, 7,5, 7,6
OpenLogic | CentOS | 6. X, 7. X
OpenLogic | CentOS-LVM | 6. X, 7. X
OpenLogic | CentOS – SRIOV | 6. X, 7. X
cloudera | cloudera-centos-os | 7. X
