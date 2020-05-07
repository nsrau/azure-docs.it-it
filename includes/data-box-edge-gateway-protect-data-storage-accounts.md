---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 9ac9865afe37916f1777d92eab8637884eba0c08
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562126"
---
Il dispositivo è associato a un account di archiviazione usato come destinazione per i dati in Azure. L'accesso all'account di archiviazione è controllato dalla sottoscrizione e dalle chiavi di accesso alle archiviazioni a 2 512 bit associate a tale account di archiviazione.

Una delle chiavi viene usata per l'autenticazione quando il dispositivo Azure Stack Edge accede all'account di archiviazione. L'altra chiave viene mantenuta in riserva, quindi è possibile ruotare periodicamente le chiavi.

Per motivi di sicurezza, molti data center richiedono la rotazione delle chiavi. Per la rotazione delle chiavi, è opportuno seguire queste procedure consigliate:

- La chiave dell’account di archiviazione è simile alla password radice per l'account di archiviazione. Proteggere con attenzione la chiave dell'account. Non distribuire la password ad altri utenti, impostarla come hardcoded o salvarla in qualsiasi punto del testo normale accessibile ad altri utenti.
- Rigenerare la chiave dell'account tramite il portale di Azure se si ritiene che possa essere compromessa. Per altre informazioni, vedere [Gestire le chiavi di accesso dell'account di archiviazione](../articles/storage/common/storage-account-keys-manage.md).
- L'amministratore di Azure deve modificare o rigenerare periodicamente la chiave primaria o secondaria usando la sezione archiviazione del portale di Azure per accedere direttamente all'account di archiviazione.