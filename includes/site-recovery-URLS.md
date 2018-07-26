---
title: File di inclusione
description: File di inclusione
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/28/2018
ms.author: raynew
ms.openlocfilehash: f7d6c3f68618fec839ccff06b73ba44d106999d2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38765539"
---
| NOME | URL commerciale | URL per enti pubblici | DESCRIZIONE |
|---|---|---|---|
| Azure AD | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Usato per il controllo di accesso e la gestione delle identità tramite Azure Active Directory |
| Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Usato per il coordinamento e il trasferimento dei dati di replica |
| Replica | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Usato per il coordinamento e le operazioni di gestione della replica |
| Archiviazione | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Usato per l'accesso all'account di archiviazione in cui sono archiviati i dati replicati |
| Telemetria (facoltativo) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Usato per la telemetria |

``time.nist.gov`` e ``time.windows.com`` sono usati per controllare la sincronizzazione tra ora di sistema e ora globale in tutte le distribuzioni.


