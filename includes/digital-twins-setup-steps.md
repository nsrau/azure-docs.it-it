---
author: baanders
description: Panoramica del file di inclusione per i passaggi nell'installazione dei dispositivi gemelli di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b8751685cfd273171cb8a5b500e4bed41ce2f59e
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407523"
---
>[!NOTE]
>Queste operazioni devono essere completate da un utente con un ruolo *proprietario* nella sottoscrizione di Azure. Anche se alcune parti possono essere completate senza questa autorizzazione elevata, sarà necessaria una collaborazione del proprietario per configurare completamente un'istanza utilizzabile. Per altre informazioni, vedere la sezione [*prerequisiti: autorizzazioni necessarie*](#prerequisites-permission-requirements) di seguito.

La configurazione completa per una nuova istanza di Azure Digital Twins è costituita da tre parti:
1. **Creazione dell'istanza**
2. **Impostazione delle autorizzazioni di accesso utente**: gli utenti di Azure devono avere il ruolo *proprietario (anteprima) di Azure Digital gemelli* nell'istanza di Azure Digital Twins per poterlo gestire e i relativi dati. In questo passaggio, come proprietario nella sottoscrizione di Azure, questo ruolo verrà assegnato alla persona che gestirà l'istanza di Azure Digital gemelli. Potrebbe trattarsi di un utente o di un altro utente nell'organizzazione.
3. **Impostazione delle autorizzazioni di accesso per le applicazioni client**: è normale scrivere un'applicazione client che verrà usata per interagire con l'istanza. Per consentire all'app client di accedere ai dispositivi gemelli digitali di Azure, è necessario configurare una *registrazione dell'app* in [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) che l'applicazione client utilizzerà per l'autenticazione all'istanza.

Per continuare, sarà necessaria una sottoscrizione di Azure. È possibile configurarne uno gratuitamente [qui](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
