---
title: Attivare i ruoli di risorse di Azure in PIM | Microsoft Docs
description: Informazioni su come attivare i ruoli di risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 08/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 234c1d71f0ec17d15a4dd589e3db92fd9bf68df2
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189490"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Attivare i ruoli di risorse di Azure in PIM
Privileged Identity Management (PIM) introduce una nuova esperienza nell'attivazione di ruoli per le risorse di Azure. I membri di ruoli idonei possono pianificare l'attivazione per una data e un'ora future. Possono anche selezionare una durata specifica dell'attivazione entro il valore massimo configurato dagli amministratori. Per altre informazioni, vedere [Come attivare o disattivare i ruoli in Azure AD Privileged Identity Management](pim-how-to-activate-role.md).

## <a name="activate-a-role"></a>Attivare un ruolo
Passare alla sezione **Ruoli personali** nel riquadro sinistro. Selezionare **Attiva** per il ruolo da attivare.

![Scheda "Ruoli idonei" nel riquadro "Ruoli personali"](media/azure-pim-resource-rbac/rbac-roles.png)

Dal menu **Attivazioni** immettere la data e l'ora di inizio per attivare il ruolo. Facoltativamente ridurre la durata dell'attivazione, ovvero il periodo di tempo per cui il ruolo è attivo, e immettere una motivazione, se necessario. Selezionare quindi **Attiva**.

Se la data e l'ora di inizio non vengono modificate, il ruolo verrà attivato entro pochi secondi. Nel riquadro **Ruoli personali** verrà visualizzato un messaggio banner relativo a un ruolo accodato per l'attivazione. Selezionare il pulsante Aggiorna per deselezionare questo messaggio.

![Riquadro "Ruoli personali" con un messaggio banner e una notifica di approvazione in sospeso](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Se l'attivazione è pianificata per una data e un'ora future, la richiesta in sospeso verrà visualizzata nella scheda **Richieste in sospeso** nel riquadro a sinistra. Se l'attivazione del ruolo non è più necessaria, l'utente può annullare la richiesta selezionando il pulsante **Annulla**.

![Elenco di richieste in sospeso con i pulsanti "Annulla"](media/azure-pim-resource-rbac/rbac-activate-pending.png)

## <a name="use-a-role-immediately-after-activation"></a>Usare un ruolo immediatamente dopo l'attivazione

A causa della memorizzazione nella cache, le attivazioni non si verifichino immediatamente nel portale di Azure senza un aggiornamento. Se è necessario ridurre la possibilità di ritardi dopo l'attivazione di un ruolo, è possibile usare la pagina **Accesso all'applicazione** nel portale. Le applicazioni a cui si esegue l'accesso da questa pagina verificano immediatamente la presenza di nuove assegnazioni di ruolo.

1. Aprire Azure AD Privileged Identity Management.

1. Fare clic sulla pagina **Accesso all'applicazione**.

    ![Accesso alle applicazioni di PIM - schermata](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. Fare clic su **Risorse di Azure** per riaprire il portale in corrispondenza della pagina **Tutte le risorse**.

    Quando si fa clic su questo collegamento, vengono eseguiti un aggiornamento e quindi un controllo per le nuove assegnazioni di ruolo delle risorse di Azure.

## <a name="apply-just-enough-administration-practices"></a>Applicare le procedure Just Enough Administration

PIM per Risorse di Azure semplifica l'uso delle procedure consigliate JEA (Just Enough Administration) con le assegnazioni di ruoli per le risorse di Azure. Gli utenti e i membri dei gruppi con assegnazioni nelle sottoscrizioni o nei gruppi di risorse di Azure possono attivare la rispettiva assegnazione di ruolo con un ambito ridotto. 

Nella pagina di ricerca è possibile trovare la risorsa subordinata da gestire.

![Selezione di una risorsa](media/azure-pim-resource-rbac/azure-resources-02.png)

Selezionare **Ruoli personali** dal riquadro a sinistra e scegliere il ruolo appropriato da attivare. Si noti che il tipo di assegnazione è **Ereditato**, poiché il ruolo è stato assegnato alla sottoscrizione, invece che al gruppo di risorse.

![Elenco di assegnazioni di ruoli idonei con il tipo di assegnazione evidenziato](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>Passaggi successivi

- [Attivare i ruoli della directory di Azure AD in PIM](pim-how-to-activate-role.md)