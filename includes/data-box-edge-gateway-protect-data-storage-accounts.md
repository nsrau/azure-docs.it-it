---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 23587e617c62cfe4aa24256330c5f7673dd1c674
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684448"
---
Il dispositivo è associato a un account di archiviazione che viene usato come una destinazione per i dati in Azure. Accede all'account di archiviazione viene controllato tramite la sottoscrizione e l'archiviazione a 512 bit due tasti di associato a tale account di archiviazione.

Una delle chiavi viene usata per l'autenticazione quando il dispositivo perimetrale finestra dati accede all'account di archiviazione. L'altra chiave è tenuta di riserva per far ruotare periodicamente le chiavi.

Per motivi di sicurezza, molti Data Center richiedono la rotazione della chiave. Per la rotazione delle chiavi, è opportuno seguire queste procedure consigliate:

- La chiave dell’account di archiviazione è simile alla password radice per l'account di archiviazione. Proteggere con attenzione la chiave dell'account. Non distribuire la password per altri utenti, hard codificarlo o salvarlo in un punto qualsiasi in testo normale accessibile ad altri utenti.
- [Rigenerare la chiave dell'account](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) usando il portale di Azure se si ritiene possa essere stata compromessa.
- L'amministratore di Azure deve modificare o rigenerare periodicamente la chiave primaria o secondaria usando la sezione Archiviazione del portale di Azure per accedere direttamente all'account di archiviazione.