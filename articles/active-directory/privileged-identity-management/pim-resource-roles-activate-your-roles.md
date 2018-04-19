---
title: Privileged Identity Management per risorse di Azure - Attivare i ruoli | Microsoft Docs
description: Descrive come attivare i ruoli in Privileged Identity Management.
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
ms.openlocfilehash: 3e5456e7a632639cb82d7ba2b2e073938b1798ef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---activate"></a>Privileged Identity Management - Ruoli delle risorse - Attivazione
L'attivazione dei ruoli per le risorse di Azure introduce una nuova esperienza che consente ai membri dei ruoli idonei di pianificare l'attivazione per una data/ora futura e selezionare una durata di attivazione specifica entro il valore massimo, configurato dagli amministratori. Altre informazioni sull'[attivazione dei ruoli di Azure AD sono disponibili qui](../active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="activate-roles"></a>Attivare i ruoli
Passare alla sezione Ruoli personali sulla barra di spostamento a sinistra. Fare clic su "Attiva" per il ruolo che si desidera attivare.
![](media/azure-pim-resource-rbac/rbac-roles.png)

Dal menu Attivazioni immettere la data e l'ora di inizio per attivare il ruolo. Ridurre facoltativamente la durata dell'attivazione, ovvero il periodo di tempo per cui il ruolo rimane attivo, e immettere una giustificazione, se necessario. Fare clic su Attiva.

Se la data e l'ora di inizio non vengono modificate, il ruolo verrà attivato entro pochi secondi. Nella pagina Ruoli personali verrà visualizzato un messaggio di avvio relativo a un ruolo accodato per l'attivazione. Fare clic sul pulsante Aggiorna per chiudere il messaggio.

![](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Se l'attivazione è pianificata per una data e un'ora future, la richiesta in sospeso verrà visualizzata nella scheda Richieste in sospeso nel menu di spostamento a sinistra. Se l'attivazione del ruolo non è più necessaria, l'utente può annullare la richiesta facendo clic sul pulsante Annulla disponibile nel lato destro della pagina.

![](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="just-enough-administration"></a>Just Enough Administration

PIM per Risorse di Azure semplifica l'uso delle procedure consigliate JEA (Just Enough Administration) con le assegnazioni di ruolo della risorsa. Gli utenti e i membri dei gruppi con assegnazioni nelle sottoscrizioni o nei gruppi di risorse di Azure possono attivare la rispettiva assegnazione di ruolo con un ambito ridotto. 

Dalla pagina di ricerca è possibile trovare la risorsa subordinata da gestire.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Selezionare Ruoli personali dal menu di spostamento a sinistra e scegliere il ruolo appropriato da attivare. Si noti che il tipo di assegnazione è Ereditato, poiché il ruolo è stato assegnato alla sottoscrizione, invece che al gruppo di risorse, come mostrato di seguito.

![](media/azure-pim-resource-rbac/my-roles-02.png)
