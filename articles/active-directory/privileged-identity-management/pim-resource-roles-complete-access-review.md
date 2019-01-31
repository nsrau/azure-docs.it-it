---
title: Completare una verifica di accesso per i ruoli delle risorse di Azure in PIM | Microsoft Docs
description: Informazioni su come completare una verifica di accesso per i ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 1e2bdeeb8f2b59d69e761303176c36b26f47d4c8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165490"
---
# <a name="complete-an-access-review-for-azure-resource-roles-in-pim"></a>Completare una verifica di accesso per i ruoli delle risorse di Azure in PIM
Gli amministratori dei ruoli con privilegi possono esaminare l'accesso con privilegi dopo che è stata [avviata una verifica di accesso](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) per le risorse di Azure invia automaticamente un messaggio di posta elettronica che chiede agli utenti di verificare l'accesso. Agli utenti che non hanno ricevuto il messaggio di posta elettronica è possibile inviare le istruzioni su [come eseguire una verifica di accesso](pim-resource-roles-perform-access-review.md).

Trascorso il periodo della verifica di accesso o al termine della verifica automatica di tutti gli utenti, seguire la procedura descritta in questo articolo per gestire la verifica e visualizzare i risultati.

## <a name="manage-access-reviews"></a>Gestire le verifiche di accesso
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

### <a name="apply"></a>Applica
Dopo aver completato una verifica di accesso, usare il pulsante **Applica** per implementare il risultato della verifica. Se l'accesso di un utente è stato negato nel corso della verifica, questo passaggio consente di rimuovere la relativa assegnazione di ruolo.  

### <a name="delete"></a>Delete
Se la verifica non è più necessaria, eliminarla. Il pulsante **Elimina** rimuove la verifica dall'applicazione PIM.

## <a name="results"></a>Risultati
Visualizzare e scaricare un elenco dei risultati della verifica nella scheda **Risultati**. 
![Scheda Risultati](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Revisori
Visualizzare e aggiungere i revisori per la verifica di accesso esistente. Ricordare ai revisori di completare le verifiche.
![Aggiungi revisori](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Passaggi successivi

- [Avviare una verifica di accesso per i ruoli delle risorse di Azure in PIM](pim-resource-roles-start-access-review.md)
- [Eseguire una verifica di accesso dei ruoli delle risorse di Azure in PIM](pim-resource-roles-perform-access-review.md)
