---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 73a8f20122e21c4557289995c15b008401285011
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131650"
---
Usare sempre [Azure ad gruppi di sicurezza](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups) come entità assegnata in una voce ACL. Evitare di assegnare direttamente singoli utenti o entità servizio. L'uso di questa struttura consentirà di aggiungere e rimuovere utenti o entità servizio senza la necessità di riapplicare gli elenchi di controllo di accesso a un'intera struttura di directory. Al contrario, è possibile aggiungere o rimuovere utenti ed entità servizio solo dal gruppo di sicurezza Azure AD appropriato. 

Sono disponibili molti modi diversi per configurare i gruppi. Si supponga, ad esempio, di disporre di una directory denominata **/LogData** che contiene i dati di log generati dal server. Azure Data Factory (ADF) inserisce i dati in tale cartella. Gli utenti specifici del team di progettazione dei servizi caricano i log e gestiscono altri utenti di questa cartella e diversi cluster di databricks analizzano i log da tale cartella. 

Per abilitare queste attività, è possibile creare un `LogsWriter` gruppo e un `LogsReader` gruppo. Quindi, è possibile assegnare le autorizzazioni come indicato di seguito:

- Aggiungere il `LogsWriter` gruppo all'ACL della directory **/LogData** con le `rwx` autorizzazioni.
- Aggiungere il `LogsReader` gruppo all'ACL della directory **/LogData** con le `r-x` autorizzazioni.
- Aggiungere l'oggetto entità servizio o identità del servizio gestita (MSI) per ADF al `LogsWriters` gruppo.
- Aggiungere gli utenti del team di progettazione dei servizi al `LogsWriter` gruppo.
- Aggiungere l'oggetto entità servizio o MSI per databricks al `LogsReader` gruppo.

Se un utente del team di progettazione dei servizi lascia l'azienda, è possibile rimuoverli semplicemente dal `LogsWriter` gruppo. Se l'utente non è stato aggiunto a un gruppo, ma è stata aggiunta una voce ACL dedicata per tale utente, è necessario rimuovere la voce ACL dalla directory **/LogData** . Sarà anche necessario rimuovere la voce da tutte le sottodirectory e i file nell'intera gerarchia di directory della directory **/LogData** . 

Per creare un gruppo e aggiungere membri, vedere [creare un gruppo di base e aggiungere membri con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).