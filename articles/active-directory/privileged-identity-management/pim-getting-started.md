---
title: Iniziare a usare PIM - Azure Active Directory Documenti Microsoft
description: Informazioni su come iniziare a usare Azure AD Privileged Identity Management (PIM) nel portale di Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 03/13/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cbb9b4340a7cdb9be5039722a8f75e09288ec48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472861"
---
# <a name="start-using-privileged-identity-management"></a>Iniziare a usare Privileged Identity Management

Con Privileged Identity Management (PIM), è possibile gestire, controllare e monitorare l'accesso all'interno dell'organizzazione Azure Active Directory (Azure AD). Questo ambito include l'accesso alle risorse di Azure, Azure AD e altri servizi online Microsoft come Office 365 o Microsoft Intune.This scope includes access to Azure resources, Azure AD, and other Microsoft online services like Office 365 or Microsoft Intune.

In questo articolo viene descritto come abilitare e iniziare a utilizzare Gestione identità con privilegi.

## <a name="prerequisites"></a>Prerequisiti

Per utilizzare Gestione identità con privilegi, è necessario disporre di una delle licenze seguenti:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Per ulteriori informazioni, vedere [Requisiti di licenza per l'utilizzo](subscription-requirements.md)di Gestione identità con privilegi .

## <a name="sign-up-pim-for-azure-ad-roles"></a>Iscrivere PIM ai ruoli di Azure AD

Dopo aver abilitato la gestione delle identità con privilegi per la directory, è necessario registrarsi con Gestione delle identità con privilegi per gestire i ruoli di Azure AD.

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Ruoli di Azure AD**.

    ![Registrare i ruoli di Gestione identità con privilegi per Azure ADSign up Privileged Identity Management for Azure AD roles](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Selezionare **Iscriviti**.

1. Nel messaggio visualizzato fare clic su **Sì** per registrare Gestione identità con privilegi per gestire i ruoli di Azure AD.

    ![Messaggio Di iscrizione a Privileged Identity Management per i ruoli di Azure ADSign up Privileged Identity Management for Azure AD roles message](./media/pim-getting-started/sign-up-pim-message.png)

    Al termine dell'iscrizione, verranno abilitate le opzioni di Azure AD. Potrebbe essere necessario aggiornare il portale.

    Per informazioni su come individuare e selezionare le risorse di Azure da proteggere con la gestione delle identità con privilegi, vedere Individuare le risorse di [Azure da gestire in Gestione identità con privilegi.](pim-resource-roles-discover-resources.md)

## <a name="navigate-to-your-tasks"></a>Accedere alle attività

Dopo aver configurato la gestione delle identità con privilegi, è possibile avviare le attività di gestione delle identità.

![Finestra di spostamento in Gestione identità con privilegi che mostra le opzioni Attività e Gestisci](./media/pim-getting-started/pim-quickstart-tasks.png)

| Attività e gestione | Descrizione |
| --- | --- |
| **Ruoli personali**  | Visualizza un elenco di ruoli idonei e attivi assegnati all'utente. Qui è possibile attivare tutti i ruoli idonei assegnati. |
| **Richieste personali** | Visualizza le richieste in sospeso per attivare le assegnazioni di ruolo idonee. |
| **Approvare le richieste** | Visualizza un elenco di richieste per attivare i ruoli idonei per gli utenti della directory che possono essere approvati. |
| **Verificare l'accesso** | Elenca le verifiche di accesso attive assegnate all'utente per essere completate, sia per se stesso che per altri utenti. |
| **Ruoli di Azure AD** | Visualizza un dashboard e le impostazioni per gli amministratori dei ruoli con privilegi per gestire le assegnazioni di ruolo di Azure AD. Per gli utenti che non sono amministratori dei ruoli con privilegi, questo dashboard è disabilitato. Questi utenti possono accedere a un dashboard speciale denominato My view (Visualizzazione personalizzata). Il dashboard My view (Visualizzazione personalizzata) visualizza solo informazioni relative all'accesso dell'utente al dashboard, non l'intero tenant. |
| **Risorse di Azure** | Visualizza un dashboard e le impostazioni per gli amministratori dei ruoli con privilegi per gestire le assegnazioni dei ruoli della risorsa di Azure AD. Per gli utenti che non sono amministratori dei ruoli con privilegi, questo dashboard è disabilitato. Questi utenti possono accedere a un dashboard speciale denominato My view (Visualizzazione personalizzata). Il dashboard My view (Visualizzazione personalizzata) visualizza solo informazioni relative all'accesso dell'utente al dashboard, non l'intero tenant. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Aggiungere un riquadro PIM al dashboard

Per semplificare l'apertura di Gestione identità con privilegi, aggiungere un riquadro Gestione identità con privilegi al dashboard del portale di Azure.To make it easier to open Privileged Identity Management, add a Privileged Identity Management tile to your Azure portal dashboard.

1. Accedere al [portale](https://portal.azure.com/)di Azure .

1. Selezionare **Tutti i servizi** e individuare il servizio Gestione identità con privilegi di Azure **AD.**

    ![Azure AD Privileged Identity Management in Tutti i servizi](./media/pim-getting-started/pim-all-services-find.png)

1. Selezionare la Guida introduttiva di Gestione identità con privilegi.

1. Selezionare **Aggiungi pannello al dashboard** per aggiungere il pannello Guida introduttiva di Gestione identità con privilegi al dashboard.

    ![Icona di pushper per aggiungere il pannello Gestione identità con privilegi al dashboard](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Nel dashboard di Azure viene visualizzato un riquadro simile al seguente:

    ![Riquadro Guida introduttiva Gestione identità con privilegi nel dashboard](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli di Azure AD in Gestione identità con privilegiAssign Azure AD roles in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Individuazione delle risorse di Azure da gestire in Gestione identità con privilegiDiscover Azure resources to manage in Privileged Identity Management](pim-resource-roles-discover-resources.md)
