---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180791"
---
Il dispositivo è associato a un account di archiviazione che viene usato come una destinazione per i dati in Azure. Accede all'account di archiviazione viene controllato tramite la sottoscrizione e l'archiviazione a 512 bit due tasti di associato a tale account di archiviazione.

Una delle chiavi viene usata per l'autenticazione quando il dispositivo perimetrale finestra dati accede all'account di archiviazione. L'altra chiave è tenuta di riserva, in modo che è possibile ruotare le chiavi periodicamente.

Per motivi di sicurezza, molti Data Center richiedono la rotazione della chiave. Per la rotazione delle chiavi, è opportuno seguire queste procedure consigliate:

- La chiave dell’account di archiviazione è simile alla password radice per l'account di archiviazione. Proteggere con attenzione la chiave dell'account. Non distribuire la password per altri utenti, hard codificarlo o salvarlo in un punto qualsiasi in testo normale accessibile ad altri utenti.
- [Rigenerare la chiave dell'account](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) tramite Azure portale se si sospetta che potrebbe essere compromessa.
- L'amministratore di Azure deve modificare o rigenerare la chiave primaria o secondaria usando la sezione archiviazione del portale di Azure per accedere direttamente all'account di archiviazione periodicamente.