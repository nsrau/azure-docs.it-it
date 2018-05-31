---
title: Completare una verifica di accesso per le risorse di Azure usando Privileged Identity Management | Microsoft Docs
description: Descrive come completare una verifica di accesso per le risorse di Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: ae64d9ebbca80f6c21b8c7f352022a0878518e65
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149339"
---
# <a name="complete-an-access-review-for-azure-resources-by-using-privileged-identity-management"></a>Completare una verifica di accesso per le risorse di Azure usando Privileged Identity Management
Gli amministratori dei ruoli con privilegi possono esaminare l'accesso con privilegi dopo che è stata [avviata una verifica della sicurezza](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) per le risorse di Azure invia automaticamente un messaggio di posta elettronica che chiede agli utenti di verificare l'accesso. Agli utenti che non hanno ricevuto il messaggio di posta elettronica è possibile inviare le istruzioni su [come eseguire una verifica della sicurezza](pim-resource-roles-perform-access-review.md).

Trascorso il periodo della verifica, o dopo che tutti gli utenti hanno completato la verifica autonomamente, seguire la procedura descritta in questo articolo per gestire la verifica e visualizzare i risultati.

## <a name="manage-security-reviews"></a>Gestire le verifiche della sicurezza
1. Accedere al [portale di Azure](https://portal.azure.com/). Quindi, nel dashboard, selezionare l'applicazione **Risorse di Azure**.

2. Selezionare la risorsa.

3. Selezionare la sezione **Verifiche dell'accesso** del dashboard.
![Verifiche di accesso](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)

4. Fare clic sulla verifica dell'accesso che si vuole gestire.

Nel pannello dei dettagli della verifica di accesso sono disponibili alcune opzioni per la gestione della verifica, descritte di seguito:

![Opzioni per la gestione della verifica](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Arresto
Tutte le verifiche di accesso hanno una data di fine, ma il pulsante **Interrompi** consente di completare l'operazione in anticipo. Una volta interrotta la verifica, gli utenti che non l'hanno ancora completata non saranno più in grado di completarla. Non è possibile riavviare una verifica dopo che è stata interrotta.

### <a name="reset"></a>Reimpostazione
È possibile reimpostare una verifica di accesso in modo da rimuovere tutte le decisioni prese. Dopo aver reimpostato una verifica di accesso, tutti gli utenti vengono nuovamente contrassegnati come utenti non verificati. 

### <a name="apply"></a>Applicazione
Dopo aver completato una verifica di accesso, usare il pulsante **Applica** per implementare il risultato della verifica. Se l'accesso di un utente è stato negato nel corso della verifica, questo passaggio consente di rimuovere la relativa assegnazione di ruolo.  

### <a name="delete"></a>Eliminazione
Se la verifica non è più necessaria, eliminarla. Il pulsante **Elimina** rimuove la verifica dall'applicazione PIM.

## <a name="results"></a>Risultati
Visualizzare e scaricare un elenco dei risultati della verifica nella scheda **Risultati**. 
![Scheda Risultati](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Revisori
Visualizzare e aggiungere i revisori per la verifica di accesso esistente. Ricordare ai revisori di completare le verifiche.
![Aggiungi revisori](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



