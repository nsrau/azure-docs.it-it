---
title: Visualizzare assegnazioni di rifiuto tramite il portale di Azure | Microsoft Docs
description: Informazioni su come visualizzare gli utenti, i gruppi, le entità servizio e le identità gestite a cui è stato negato l'accesso ad azioni specifiche in ambito specifico usando il portale di Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f5870ddbbb8be0ebbeae7656485521a327b86d5b
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637726"
---
# <a name="view-deny-assignments-using-the-azure-portal"></a>Visualizzare le assegnazioni di rifiuto tramite il portale di Azure

[Le assegnazioni di rifiuto](deny-assignments.md) impediscono agli utenti di eseguire azioni specifiche, anche se un'assegnazione di ruolo concede loro l'accesso. Sebbene non sia possibile creare le proprie assegnazioni di rifiuto, è comunque necessario essere in grado di visualizzarle perché potrebbero influire sulle autorizzazioni complessive. Per ottenere informazioni su un'assegnazione di rifiuto è necessario avere l'autorizzazione `Microsoft.Authorization/denyAssignments/read`, inclusa nella maggior parte dei [ruoli predefiniti](built-in-roles.md).

Questo articolo descrive come usare il portale di Azure per visualizzare le assegnazioni di rifiuto.

> [!NOTE]
> A questo punto, le assegnazioni di rifiuto sono di sola lettura e possono essere impostate solo da Azure.

## <a name="view-deny-assignments"></a>Visualizzare le assegnazioni di rifiuto

Seguire questa procedura per visualizzare le assegnazioni nell'ambito del gruppo di gestione o di sottoscrizione.

1. Nel portale di Azure, fare clic su **Tutti i servizi** e quindi su **Gruppi di gestione** o **Sottoscrizioni**.

1. Fare clic sul gruppo di gestione o su una sottoscrizione che si desidera visualizzare.

1. Fare clic su **Controllo di accesso (IAM)**.

1. Fare clic sulla scheda **Assegnazioni di rifiuto** (oppure fare clic sul pulsante **Visualizza** nel riquadro Visualizza assegnazioni di rifiuto).

    Se sono presenti assegnazioni negate in questo ambito o ereditate da questo ambito, verranno elencate.

    ![Controllo di accesso - Scheda Assegnazioni di rifiuto](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Per visualizzare colonne aggiuntive, fare clic su **Modifica colonne**.

    ![Assegnazioni di rifiuto - Colonne](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Nome** | Il nome dell'assegnazione di rifiuto. |
    | **Tipo di entità** | Utente, gruppo, gruppo definito dal sistema gruppo o entità servizio. |
    | **Negato**  | Nome dell'entità di sicurezza che è inclusa nell'assegnazione di rifiuto. |
    | **Id** | Identificatore univoco per l'assegnazione di rifiuto. |
    | **Entità di sicurezza escluse** | Se sono presenti entità di sicurezza che sono escluse dall'assegnazione di rifiuto. |
    | **Non applicabile agli elementi figlio** | Se l'assegnazione di rifiuto è ereditata da ambiti secondari. |
    | **Con protezione sistema** | Se l'assegnazione di rifiuto è gestita da Azure. Attualmente, sempre Sì. |
    | **Ambito** | Gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa. |

1. Aggiungere un segno di spunta per gli elementi abilitati e quindi fare clic su **OK** per visualizzare le colonne selezionate.

## <a name="view-details-about-a-deny-assignment"></a>Consente di visualizzare i dettagli di un'assegnazione di rifiuto

Seguire questi passaggi per visualizzare dettagli aggiuntivi su un'assegnazione di rifiuto.

1. Aprire il riquadro **Assegnazioni di rifiuto** come descritto nella sezione precedente.

1. Fare clic sull'assegnazione di rifiuto per aprire il pannello **Utenti**.

    ![Assegnazione di rifiuto - Utenti](./media/deny-assignments-portal/deny-assignment-users.png)

    Il pannello **Utenti** include le due sezioni seguenti.

    |  |  |
    | --- | --- |
    | **Assegnazione di rifiuto applicabile a**  | Entità di sicurezza a cui si applica l'assegnazione di rifiuto. |
    | **Elementi esclusi dall'assegnazione di rifiuto** | Entità di sicurezza che sono escluse dall'assegnazione di rifiuto. |

    L'**entità definita dal servizio** rappresenta tutti gli utenti, i gruppi, le entità servizio e le identità gestite in una directory di Azure AD.

1. Per visualizzare un elenco delle autorizzazioni negate, fare clic su **Autorizzazioni negate**.

    ![Assegnazione di rifiuto - Autorizzazioni negate](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Tipo di azione | DESCRIZIONE |
    | --- | --- |
    | **Actions**  | Operazioni di gestione negate. |
    | **NotActions** | Operazioni di gestione escluse dall'operazione di gestione negata. |
    | **DataActions**  | Operazioni con dati negati. |
    | **NotDataActions** | Operazioni con dati esclusi dalle operazioni con dati negati. |

    Nell'esempio illustrato nello screenshot precedente, le autorizzazioni valide sono le seguenti:

    - Tutte le operazioni di memorizzazione sul piano dati sono negate ad eccezione delle operazioni di calcolo.

1. Per visualizzare le proprietà per un'assegnazione di rifiuto, fare clic su **Proprietà**.

    ![Assegnazione di rifiuto - Proprietà](./media/deny-assignments-portal/deny-assignment-properties.png)

    Nel pannello **Proprietà**, è possibile visualizzare il nome dell'assegnazione di rifiuto, l'ID, la descrizione e l'ambito. Il commutatore **Non si applica agli elementi figlio** indica se l'assegnazione di rifiuto è ereditata da ambiti secondari. Il commutatore **Protette del sistema** indica se questo assegnazione di rifiuto viene gestita da Azure. Attualmente, è **Sì** in tutti i casi.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulle assegnazioni di rifiuto](deny-assignments.md)
* [Elencare le assegnazioni di rifiuto usando RBAC e l'API REST](deny-assignments-rest.md)
