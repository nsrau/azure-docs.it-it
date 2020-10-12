---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 9ac9865afe37916f1777d92eab8637884eba0c08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82562126"
---
Il dispositivo è associato a un account di archiviazione usato come destinazione per i dati in Azure. L'accesso all'account di archiviazione è controllato dalla sottoscrizione e dalle due chiavi di accesso alle risorse di archiviazione a 512 bit che sono associate all'account di archiviazione.

Una delle chiavi viene usata per l'autenticazione quando il dispositivo Azure Stack Edge accede all'account di archiviazione. L'altra chiave è tenuta di riserva, in modo da poter ruotare le chiavi periodicamente.

Per motivi di sicurezza, molti data center richiedono la rotazione delle chiavi. Per la rotazione delle chiavi, è opportuno seguire queste procedure consigliate:

- La chiave dell’account di archiviazione è simile alla password radice per l'account di archiviazione. Proteggere con attenzione la chiave dell'account. Non divulgare la password ad altri utenti, non impostarla come hardcoded o non salvarla in testo normale in una posizione accessibile ad altri.
- Rigenerare la chiave dell'account tramite il portale di Azure se si ritiene che possa essere compromessa. Per altre informazioni, vedere [Gestire le chiavi di accesso dell'account di archiviazione](../articles/storage/common/storage-account-keys-manage.md).
- L'amministratore di Azure deve modificare o rigenerare periodicamente la chiave primaria o secondaria usando la sezione archiviazione del portale di Azure per accedere direttamente all'account di archiviazione.