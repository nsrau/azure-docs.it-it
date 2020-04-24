---
title: Elencare le assegnazioni di rifiuto per le risorse di Azure con il portale di Azure
description: Informazioni su come elencare gli utenti, i gruppi, le entità servizio e le identità gestite a cui è stato negato l'accesso a specifiche azioni di risorse di Azure in determinati ambiti usando il portale di Azure.
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
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4db76e5c6191457346ca1f95678cf73843334d3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137431"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-azure-portal"></a>Elencare le assegnazioni di rifiuto per le risorse di Azure usando il portale di Azure

Le [assegnazioni di rifiuto](deny-assignments.md) impediscono agli utenti di eseguire azioni specifiche sulle risorse di Azure, anche se un'assegnazione di ruolo concede loro l'accesso. Questo articolo descrive come elencare le assegnazioni di rifiuto usando il portale di Azure.

> [!NOTE]
> Non è possibile creare direttamente le assegnazioni di rifiuto. Per informazioni sul modo in cui vengono create le assegnazioni Deny, vedere [Deny assegnazioni](deny-assignments.md).

## <a name="prerequisites"></a>Prerequisiti

Per ottenere informazioni su un'assegnazione Deny, è necessario disporre di:

- `Microsoft.Authorization/denyAssignments/read`autorizzazione, inclusa nella maggior parte dei [ruoli predefiniti per le risorse di Azure](built-in-roles.md).

## <a name="list-deny-assignments"></a>Visualizzare le assegnazioni di rifiuto

Attenersi alla procedura seguente per elencare le assegnazioni di accesso negato nell'ambito della sottoscrizione o del gruppo di gestione.

1. Nel portale di Azure, fare clic su **Tutti i servizi** e quindi su **Gruppi di gestione** o **Sottoscrizioni**.

1. Fare clic sul gruppo di gestione o sulla sottoscrizione che si desidera elencare.

1. Fare clic su **controllo di accesso (IAM)**.

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

## <a name="list-details-about-a-deny-assignment"></a>Elenca i dettagli relativi a un'assegnazione di negazione

Attenersi alla seguente procedura per elencare ulteriori dettagli su un'assegnazione di negazione.

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

    | Tipo di azione | Descrizione |
    | --- | --- |
    | **Azioni**  | Operazioni di gestione negate. |
    | **NotActions** | Operazioni di gestione escluse dall'operazione di gestione negata. |
    | **Dataactions**  | Operazioni con dati negati. |
    | **NotDataActions** | Operazioni con dati esclusi dalle operazioni con dati negati. |

    Nell'esempio illustrato nello screenshot precedente, le autorizzazioni valide sono le seguenti:

    - Tutte le operazioni di memorizzazione sul piano dati sono negate ad eccezione delle operazioni di calcolo.

1. Per visualizzare le proprietà per un'assegnazione di rifiuto, fare clic su **Proprietà**.

    ![Assegnazione di rifiuto - Proprietà](./media/deny-assignments-portal/deny-assignment-properties.png)

    Nel pannello **Proprietà**, è possibile visualizzare il nome dell'assegnazione di rifiuto, l'ID, la descrizione e l'ambito. Il commutatore **Non si applica agli elementi figlio** indica se l'assegnazione di rifiuto è ereditata da ambiti secondari. Il commutatore **Protette del sistema** indica se questo assegnazione di rifiuto viene gestita da Azure. Attualmente, è **Sì** in tutti i casi.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulle assegnazioni di rifiuto per le risorse di Azure](deny-assignments.md)
* [Elencare le assegnazioni di rifiuto per le risorse di Azure usando Azure PowerShell](deny-assignments-powershell.md)
