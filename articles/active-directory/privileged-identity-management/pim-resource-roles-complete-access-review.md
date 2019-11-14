---
title: Completare la verifica dell'accesso dei ruoli delle risorse di Azure in PIM-Azure AD | Microsoft Docs
description: Informazioni su come completare una verifica di accesso dei ruoli delle risorse di Azure Privileged Identity Management in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e45249245aaab97070b7e774d4b6bab6827bdc9
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021990"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Completare una verifica di accesso dei ruoli delle risorse di Azure in Privileged Identity Management

Gli amministratori del ruolo con privilegi possono verificare l'accesso con privilegi dopo l' [avvio di una verifica di accesso](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) in Azure Active Directory (Azure AD) Invia automaticamente un messaggio di posta elettronica che richiede agli utenti di verificare l'accesso. Agli utenti che non hanno ricevuto il messaggio di posta elettronica è possibile inviare le istruzioni su [come eseguire una verifica di accesso](pim-resource-roles-perform-access-review.md).

Trascorso il periodo della verifica di accesso o al termine della verifica automatica di tutti gli utenti, seguire la procedura descritta in questo articolo per gestire la verifica e visualizzare i risultati.

## <a name="manage-access-reviews"></a>Gestire le verifiche di accesso

1. Accedere al [portale di Azure](https://portal.azure.com/). Nel Dashboard selezionare il servizio **risorse di Azure** .

2. Selezionare la risorsa.

3. Selezionare la sezione **Verifiche dell'accesso** del dashboard.

    ![Risorse di Azure-elenco di verifiche di accesso che mostra ruolo, proprietario, data di inizio, data di fine e stato](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Fare clic sulla verifica dell'accesso che si vuole gestire.

Nella pagina dei dettagli per la verifica dell'accesso sono disponibili diverse opzioni per la gestione della revisione. descritte di seguito:

![Opzioni per la gestione di una verifica-arresta, Reimposta, applica, Elimina](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Arresto

Tutte le verifiche di accesso hanno una data di fine. Selezionare **Interrompi** per terminarla in anticipo. Tutti gli utenti che non hanno completato la revisione entro questo periodo di tempo non saranno in grado di completare l'operazione dopo aver interrotto la revisione. Non è possibile riavviare una verifica dopo che è stata interrotta.

### <a name="reset"></a>Reimpostazione

È possibile reimpostare una verifica di accesso in modo da rimuovere tutte le decisioni prese. Dopo aver reimpostato una verifica di accesso, tutti gli utenti vengono contrassegnati come non rivisti.

### <a name="apply"></a>Applica

Al termine di una verifica di accesso, selezionare **applica** per implementare il risultato della verifica. Se l'accesso di un utente è stato negato nel corso della verifica, questo passaggio consente di rimuovere la relativa assegnazione di ruolo.  

### <a name="delete"></a>Elimina

Se la verifica non è più necessaria, eliminarla. Selezionare **Elimina** yo rimuovere la revisione dal servizio Privileged Identity Management.

## <a name="results"></a>Risultati

Nella pagina **risultati** visualizzare e scaricare un elenco dei risultati della verifica.

![Pagina dei risultati in cui sono elencati utenti, risultati, motivo, rivisti, applicati da e risultato applicazione](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Revisori

Visualizzare e aggiungere i revisori per la verifica di accesso esistente. Ricordare ai revisori di completare le verifiche.

![Nome del listato della pagina revisori e nome dell'entità utente](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Passaggi successivi

- [Avviare una verifica di accesso per i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-start-access-review.md)
- [Eseguire una verifica di accesso dei ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-perform-access-review.md)
