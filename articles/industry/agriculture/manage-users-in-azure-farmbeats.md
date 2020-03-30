---
title: Gestire gli utenti in Azure FarmBeatsManage users in Azure FarmBeats
description: Questo articolo descrive come gestire gli utenti in Azure FarmBeats.This article describes how to manage users in Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 6ccc29422c6abf1120d13c05d10125efd0871ca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502056"
---
# <a name="manage-users"></a>Gestire gli utenti

Azure FarmBeats include la gestione degli utenti per le persone che fanno parte dell'istanza di Azure Active Directory (Azure AD). È possibile aggiungere utenti all'istanza di FarmBeats di Azure per accedere alle API, visualizzare le mappe generate e accedere ai dati di telemetria dei sensori dalla farm.

## <a name="prerequisites"></a>Prerequisiti

- È necessaria l'installazione di Azure FarmBeats.Azure FarmBeats installation is required. Per altre informazioni, vedere [Installare Azure FarmBeats.](install-azure-farmbeats.md)
- ID di posta elettronica degli utenti che si desidera aggiungere o rimuovere dall'istanza di Azure FarmBeats.

## <a name="manage-azure-farmbeats-users"></a>Gestire gli utenti di Azure FarmBeatsManage Azure FarmBeats users

Azure FarmBeats usa Azure AD per l'autenticazione, il controllo degli accessi e i ruoli. È possibile aggiungere utenti nel tenant di Azure AD come utenti in FarmBeats di Azure.You can add users in the Azure AD tenant as users in Azure FarmBeats.

> [!NOTE]
> Se un utente non è un utente tenant di Azure AD, seguire le istruzioni nella sezione Aggiungere utenti di **Azure AD** per completare la configurazione.

Azure FarmBeats supporta due tipi di ruoli utente:Azure FarmBeats supports two types of user roles:

 - **Amministratore:** accesso completo alle API Datahub di Azure FarmBeats.Admin : Complete access to Azure FarmBeats Datahub APIs. Gli utenti in questo ruolo possono eseguire query su tutti gli oggetti Datahub di Azure FarmBeats ed eseguire tutte le operazioni da FarmBeats Accelerator.Users in this role can query all Azure FarmBeats Datahub objects and perform all operations from the FarmBeats Accelerator.
 - **Sola lettura:** accesso in sola lettura alle API FarmBeats Datahub. Gli utenti possono visualizzare le API Datahub, i dashboard di accelerazione e le mappe. Gli utenti con accesso in sola lettura non possono eseguire operazioni quali la generazione di mappe, l'associazione di dispositivi o la creazione di farm.

## <a name="add-users-to-azure-farmbeats"></a>Aggiungere utenti ad Azure FarmBeatsAdd users to Azure FarmBeats

To add users to Azure FarmBeats:

1. Accedere ad Acceleratore, quindi selezionare l'icona **Impostazioni.**
2. Selezionare **Controllo di accesso**.

    ![Riquadro Impostazioni farm](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. Immettere l'ID di posta elettronica dell'utente a cui si desidera concedere l'accesso.
4. Selezionare il ruolo desiderato, **Admin** o **Read-Only**.
5. Selezionare **Aggiungi ruolo**.

L'utente aggiunto può ora accedere ad Azure FarmBeats (sia Hub dati che Acceleratore).

## <a name="delete-users-from-azure-farmbeats"></a>Eliminare utenti da Azure FarmBeatsDelete users from Azure FarmBeats

Per rimuovere utenti dal sistema Azure FarmBeats:

1. Accedere ad Acceleratore, quindi selezionare l'icona **Impostazioni.**
2. Selezionare **Controllo di accesso**.
3. Selezionare **Elimina**.

   L'utente viene eliminato dal sistema. Riceverai il seguente messaggio di conferma:

   ![Messaggio di conferma di Azure FarmBeatsAzure FarmBeats confirmation message](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Aggiungere utenti di Azure AD

> [!NOTE]
> Gli utenti di Azure FarmBeats devono esistere nel tenant di Azure AD prima di assegnarli ad applicazioni e ruoli. Se un utente non esiste nel tenant di Azure AD, seguire le istruzioni in questa sezione. Ignorare le istruzioni, se un utente esiste già nel tenant di Azure AD.

Seguire i passaggi per aggiungere utenti ad Azure AD:Follow the steps to add users to Azure AD:

1. Accedere al [portale](https://portal.azure.com/)di Azure .
2. In alto a destra selezionare l'account e quindi passare al tenant di Azure AD associato a FarmBeats.At the top right, select your account, and then switch to the Azure AD tenant that's associated with FarmBeats.
3. Selezionare**Utenti** **di Azure Active Directory** > .

    Viene visualizzato un elenco di utenti di Azure AD.

4. Per aggiungere un utente alla directory, selezionare **Nuovo utente**. Per aggiungere un utente esterno, selezionare **Nuovo utente guest**.

    ![Il riquadro "Tutti gli utenti"](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. Selezionare il nome del nuovo utente e quindi compilare i campi obbligatori per tale utente.
6. Selezionare **Crea**.

Per informazioni sulla gestione degli utenti di Azure AD, vedere [Aggiungere o eliminare utenti in Azure AD.](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/)

## <a name="next-steps"></a>Passaggi successivi

Gli utenti sono stati aggiunti correttamente all'istanza di FarmBeats di Azure.You have successfully added users to your Azure FarmBeats instance. A questo punto, imparare a [creare e gestire le farm](manage-farms-in-azure-farmbeats.md#create-farms).
