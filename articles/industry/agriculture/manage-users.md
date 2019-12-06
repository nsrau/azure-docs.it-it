---
title: Gestire gli utenti
description: Viene descritto come gestire gli utenti
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 37dacf0adfc9e3dcbab963cef50e2ee5209df314
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852354"
---
# <a name="manage-users"></a>Gestire gli utenti

Azure FarmBeats include la gestione degli utenti per utenti che fanno parte del Azure Active Directory (Azure AD). Sarà possibile aggiungere utenti all'istanza di Azure FarmBeats per accedere alle API, visualizzare le mappe generate e i dati di telemetria del sensore dalla farm

## <a name="prerequisites"></a>Prerequisiti

- È necessaria una distribuzione di Azure FarmBeats. Per altre informazioni su come configurare Azure FarmBeats, vedere [distribuire FarmBeats](prepare-for-deployment.md) .
- ID di posta elettronica degli utenti che si vuole aggiungere o rimuovere dall'istanza di Azure FarmBeats.

## <a name="manage-azure-farmbeats-users"></a>Gestire gli utenti di Azure FarmBeats

Azure FarmBeats USA Azure AD per l'autenticazione, il controllo di accesso e i ruoli. Gli utenti nel tenant di Azure AD possono essere aggiunti come utenti in Azure FarmBeats.

> [!NOTE]
> Se l'utente che si sta provando ad aggiungere non è presente nel tenant di Azure AD, seguire le istruzioni nella sezione **aggiungere Azure ad utenti** per completare la configurazione, prima di procedere con la configurazione come utenti di Azure FarmBeats.

**Ruoli**

Attualmente sono supportati due tipi di ruoli utente in Azure FarmBeats:

 - **Amministrazione** : tutti gli accessi alle API dell'hub dati di Azure FarmBeats. Gli utenti con questo ruolo possono eseguire query su tutti gli oggetti dell'hub dati di Azure FarmBeats, eseguire tutte le operazioni dall'acceleratore di FarmBeats.
 - Sola **lettura** : accesso di sola lettura alle API dell'hub dati FarmBeats. Gli utenti possono visualizzare le API dell'hub dati, i dashboard acceleratori e le mappe. Un utente con ruolo di "sola lettura" non sarà in grado di eseguire operazioni quali la generazione di mappe, l'associazione di dispositivi o la creazione di farm.


## <a name="add-user-to-azure-farmbeats"></a>Aggiungere un utente ad Azure FarmBeats

Per aggiungere un utente ad Azure FarmBeats, 
1.  Accedere al tasto di scelta rapida e fare clic sull'icona delle impostazioni
2.  Fare clic su controllo di accesso

    ![Beat Farm progetto](./media/create-farms/settings-users-1.png)

3.  Immettere l'ID di posta elettronica dell'utente a cui si vuole concedere l'accesso
4.  Selezionare il ruolo desiderato, ovvero amministratore o di sola lettura
5.  Fare clic su Aggiungi ruolo

Gli utenti aggiunti saranno ora in grado di accedere ad Azure FarmBeats (sia hub dati che acceleratore).

## <a name="delete-user-from-azure-farmbeats"></a>Elimina utente da Azure FarmBeats

Per rimuovere un utente dal sistema FarmBeats di Azure, è possibile
1.  Accedere al tasto di scelta rapida e fare clic sull'icona delle impostazioni
2.  Fare clic su controllo di accesso
3.  Fare clic sull'icona di eliminazione accanto all'ID di posta elettronica dell'utente che si desidera rimuovere.

L'utente viene rimosso dal sistema. Per confermare l'operazione completata, viene visualizzato il messaggio seguente:


![Beat Farm progetto](./media/create-farms/manage-users-2.png)


## <a name="add-azure-ad-users"></a>Aggiungere utenti di Azure AD

> [!NOTE]
> È necessario eseguire i passaggi seguenti, solo se l'utente che si sta tentando di fornire l'accesso ad Azure FarmBeats non è presente nel tenant Azure AD. Se l'utente è già presente, non è necessario eseguire i passaggi seguenti

Gli utenti di FarmBeats devono esistere nel tenant di Azure AD prima di poterli assegnare a applicazioni e ruoli. Per aggiungere utenti ad Azure AD, completare i passaggi seguenti:
1.  Accedere al [portale di Azure](https://portal.azure.com/).
2.  Selezionare l'account nell'angolo in alto a destra e passare al tenant Azure AD associato a FarmBeats
3.  Selezionare **Azure Active Directory > Utenti**. Verrà visualizzato un elenco di utenti nella directory.
4.  Per aggiungere utenti alla directory, selezionare **Nuovo utente**. Per gli utenti esterni, selezionare **Nuovo utente guest**.

    ![Beat Farm progetto](./media/create-farms/manage-users-3.png)

5.  Completare i campi obbligatori per il nuovo utente. Selezionare **Create** (Crea).

Per altre informazioni su come gestire gli utenti in Azure AD, visitare il sito della documentazione di [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/).

## <a name="next-steps"></a>Passaggi successivi

Azure FarmBeats è stato distribuito. A questo punto, informazioni su come [creare farm](manage-farms.md#create-farms).

