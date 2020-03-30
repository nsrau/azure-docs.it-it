---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8840e6649058837207c75dbdf7baa5c0b11ef387
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75467080"
---
Your device is associated with a storage account that's used as a destination for your data in Azure. L'accesso all'account di archiviazione è controllato dalla sottoscrizione e due chiavi di accesso all'archiviazione a 512 bit associate a tale account di archiviazione.

Una delle chiavi viene usata per l'autenticazione quando il dispositivo Data Box Edge accede all'account di archiviazione. L'altro tasto è tenuto in riserva, in modo da poter ruotare le chiavi periodicamente.

Per motivi di sicurezza, molti data center richiedono la rotazione delle chiavi. Per la rotazione delle chiavi, è opportuno seguire queste procedure consigliate:

- La chiave dell’account di archiviazione è simile alla password radice per l'account di archiviazione. Proteggi con attenzione la chiave del tuo account. Non distribuire la password ad altri utenti, codificarla o salvarla in qualsiasi punto in testo normale accessibile ad altri utenti.
- Rigenerare la chiave dell'account tramite il portale di Azure se si ritiene che potrebbe essere compromessa. Per altre informazioni, vedere [Gestire le chiavi di accesso dell'account di archiviazione](../articles/storage/common/storage-account-keys-manage.md).
- L'amministratore di Azure deve modificare o rigenerare periodicamente la chiave primaria o secondaria usando la sezione Archiviazione del portale di Azure per accedere direttamente all'account di archiviazione.