---
title: Elenco negare le assegnazioni per le risorse di Azure usando il portale di Azure | Microsoft Docs
description: Informazioni su come elenco di utenti, gruppi, le entità servizio e identità gestite che è stata negata accedere a risorse di Azure specifiche azioni in ambiti specifici usando il portale di Azure.
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
ms.openlocfilehash: 21ffb1a2539a2e724a91dd3b2818270a5e573ef8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67127496"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-azure-portal"></a>Elenco negare le assegnazioni per le risorse di Azure usando il portale di Azure

Le [assegnazioni di rifiuto](deny-assignments.md) impediscono agli utenti di eseguire azioni specifiche sulle risorse di Azure, anche se un'assegnazione di ruolo concede loro l'accesso. Questo articolo descrive come elencare negare le assegnazioni nel portale di Azure.

> [!NOTE]
> È possibile creare direttamente il proprio negare le assegnazioni. Per informazioni su come negare le assegnazioni vengono create, vedere [negare le assegnazioni](deny-assignments.md).

## <a name="prerequisites"></a>Prerequisiti

Per ottenere informazioni su un'assegnazione di negazione, è necessario disporre di:

- `Microsoft.Authorization/denyAssignments/read` autorizzazione di accesso, che è incluso nella maggior parte [ruoli predefiniti per le risorse di Azure](built-in-roles.md).

## <a name="list-deny-assignments"></a>Elenco negare le assegnazioni

Seguire questi passaggi per ottenere l'elenco negare le assegnazioni nell'ambito del gruppo sottoscrizione o di gestione.

1. Nel portale di Azure, fare clic su **Tutti i servizi** e quindi su **Gruppi di gestione** o **Sottoscrizioni**.

1. Fare clic sul gruppo di gestione o di una sottoscrizione che si desidera elencare.

1. Fare clic su **Controllo di accesso (IAM)** .

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

## <a name="list-details-about-a-deny-assignment"></a>Elencare i dettagli su un'assegnazione di negazione

Seguire questi passaggi per elencare informazioni dettagliate aggiuntive su un'assegnazione di negazione.

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

* [Informazioni sulle assegnazioni di rifiuto per le risorse di Azure](deny-assignments.md)
* [Elenco negare le assegnazioni per le risorse di Azure usando Azure PowerShell](deny-assignments-powershell.md)
