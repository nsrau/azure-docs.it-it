---
title: Completare la revisione dell'accesso dei ruoli delle risorse di Azure in PIM - Azure AD Documenti Microsoft
description: Informazioni su come completare una verifica di accesso dei ruoli delle risorse di Azure Gestione delle identità con privilegi in Azure Active Directory.Learn how to complete an access review of Azure resource roles Privileged Identity Management in Azure Active Directory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021990"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Completare una verifica di accesso dei ruoli delle risorse di Azure in Gestione delle identità con privilegiComplete an access review of Azure resource roles in Privileged Identity Management

Gli amministratori dei ruoli con privilegi possono esaminare l'accesso con privilegi dopo aver avviato una verifica di [accesso.](pim-resource-roles-start-access-review.md) Gestione identità con privilegi (PIM) in Azure Active Directory (Azure AD) invia automaticamente un messaggio di posta elettronica che richiede agli utenti di esaminare l'accesso. Agli utenti che non hanno ricevuto il messaggio di posta elettronica è possibile inviare le istruzioni su [come eseguire una verifica di accesso](pim-resource-roles-perform-access-review.md).

Trascorso il periodo della verifica di accesso o al termine della verifica automatica di tutti gli utenti, seguire la procedura descritta in questo articolo per gestire la verifica e visualizzare i risultati.

## <a name="manage-access-reviews"></a>Gestire le verifiche di accesso

1. Passare al [portale di Azure](https://portal.azure.com/). Nel dashboard selezionare il servizio Risorse di **Azure.On** the dashboard, select the Azure resources service.

2. Selezionare la risorsa.

3. Selezionare la sezione **Verifiche dell'accesso** del dashboard.

    ![Risorse di Azure - Elenco delle verifiche di accesso che mostra il ruolo, il proprietario, la data di inizio, la data di fine e lo stato](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Fare clic sulla verifica dell'accesso che si vuole gestire.

Nella pagina dei dettagli della verifica di accesso sono disponibili diverse opzioni per la gestione di tale revisione. descritte di seguito:

![Opzioni per la gestione di una revisione - Interrompi, Reimposta, Applica, Elimina](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Arresto

Tutte le verifiche di accesso hanno una data di fine. Selezionare **Interrompi** per completarla in anticipo. Gli utenti che non hanno completato la revisione entro questo periodo non saranno in grado di completarla dopo aver interrotto la revisione. Non è possibile riavviare una verifica dopo che è stata interrotta.

### <a name="reset"></a>Reset

È possibile reimpostare una verifica di accesso in modo da rimuovere tutte le decisioni prese. Dopo aver reimpostato una verifica di accesso, tutti gli utenti vengono contrassegnati come non rivisti di nuovo.

### <a name="apply"></a>Applica

Al termine di una verifica di accesso, selezionare **Applica** per implementare il risultato della revisione. Se l'accesso di un utente è stato negato nel corso della verifica, questo passaggio consente di rimuovere la relativa assegnazione di ruolo.  

### <a name="delete"></a>Delete

Se la verifica non è più necessaria, eliminarla. Selezionare **Elimina** yo per rimuovere la revisione dal servizio Gestione identità con privilegi.

## <a name="results"></a>Risultati

Nella pagina **Risultati,** visualizzare e scaricare un elenco dei risultati della recensione.

![Pagina dei risultati che elenca gli utenti, il risultato, il motivo, la revisione da, applicato da e applica il risultato](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Revisori

Visualizzare e aggiungere i revisori per la verifica di accesso esistente. Ricordare ai revisori di completare le verifiche.

![Pagina dei revisori che elenca il nome e il nome dell'entità utente](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Passaggi successivi

- [Avviare una verifica di accesso per i ruoli delle risorse di Azure in Gestione identità con privilegiStart an access review for Azure resource roles in Privileged Identity Management](pim-resource-roles-start-access-review.md)
- [Eseguire una verifica di accesso dei ruoli delle risorse di Azure in Gestione delle identità con privilegiPerform an access review of my Azure resource roles in Privileged Identity Management](pim-resource-roles-perform-access-review.md)
