---
title: Privileged Identity Management per le risorse di Azure - Completare una verifica di accesso per le risorse di Azure | Microsoft Docs
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
ms.openlocfilehash: 5ce02c2d27ec3de87fe44e9c904f19409600e5c5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---finish-access-review"></a>Privileged Identity Management - Ruolo per le risorse - Completare una verifica di accesso
Gli amministratori dei ruoli con privilegi possono esaminare l'accesso con privilegi dopo che è stata [avviata una verifica della sicurezza](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) per le risorse di Azure invia automaticamente un messaggio di posta elettronica che richiede agli utenti di controllare l'accesso. Agli utenti che non hanno ricevuto il messaggio di posta elettronica, è possibile inviare le istruzioni descritte in [Come eseguire una verifica della sicurezza](pim-resource-roles-perform-access-review.md).

Trascorso il periodo della verifica della sicurezza o al termine della verifica automatica di tutti gli utenti, seguire la procedura descritta in questo articolo per gestire la verifica e visualizzare i risultati.

## <a name="manage-security-reviews"></a>Gestire le verifiche della sicurezza
1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare l'applicazione **Risorse di Azure** sul dashboard.
2. Selezionare la risorsa.
3. Selezionare la sezione **Verifiche dell'accesso** del dashboard.
![](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)
4. Fare clic sulla verifica dell'accesso che si vuole gestire.

Nel pannello dei dettagli della verifica dell'accesso sono disponibili alcune opzioni per la gestione della verifica.
![](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Arresto
Tutte le verifiche di accesso hanno una data di fine, ma il pulsante **Interrompi** consente di completare l'operazione in anticipo. Eventuali utenti non sottoposti a verifica fino a questo momento, non potranno essere controllati dopo l'interruzione della verifica. Non è possibile riavviare una verifica dopo che è stata interrotta.

### <a name="reset"></a>Reimpostazione
È possibile reimpostare una verifica dell'accesso in modo da rimuovere tutte le decisioni prese. Dopo aver reimpostato una verifica dell'accesso, tutti gli utenti vengono nuovamente contrassegnati come utenti non verificati. 

### <a name="apply"></a>Applica
Al termine di una verifica di accesso in corrispondenza della data di fine o in caso di interruzione manuale, il pulsante **Applica** implementa il risultato della verifica. Se l'accesso di un utente è stato negato nel corso della verifica, questo passaggio consente di rimuovere l'assegnazione di ruolo.  

### <a name="delete"></a>Delete
Se la verifica non è più necessaria, eliminarla. Il pulsante **Elimina** rimuove la verifica dall'applicazione PIM.

## <a name="results"></a>Risultati
Visualizzare e scaricare un elenco dei risultati della verifica nella scheda Risultati. ![](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Revisori
Visualizzare e aggiungere i revisori per la verifica di accesso esistente. Ricordare ai revisori di completare le verifiche.
![](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



