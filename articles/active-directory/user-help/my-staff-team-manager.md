---
title: Gestione delle password e dei numeri di telefono con personale (anteprima)-Azure AD | Microsoft Docs
description: Gestisci password e numeri di telefono per gli utenti con il personale
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 29c27c876e90bce6a38226f68adf44a26465cfea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770860"
---
# <a name="delegate-user-management-with-my-staff-preview"></a>Delegare la gestione degli utenti con il personale (anteprima)

L'organizzazione può utilizzare il **personale** per delegare le attività di gestione degli utenti a cifre di autorità, ad esempio un responsabile del negozio o un responsabile del team, per consentire ai membri del personale di accedere alle applicazioni necessarie. Se il membro del team non è in grado di accedere a un'applicazione perché dimentica una password, la produttività viene persa. Questo consente anche di aumentare i costi di supporto e causare un collo di bottiglia nei processi amministrativi.  Con il personale, un membro del team che non può accedere al proprio account può riottenere l'accesso con pochi clic, senza richiedere alcuna guida dell'amministratore.

## <a name="manage-your-staff-in-my-staff"></a>Gestisci il personale del personale

La gestione dei membri del team nel personale è semplice. Per iniziare, [passare a personale](https://aka.ms/mystaff), selezionare un team o una località e quindi selezionare un utente. Le località e i membri del team in una località sono determinati dall'amministratore IT e non è possibile modificarli.

Se si gestiscono più percorsi, quando si passa al personale è necessario selezionare una località per visualizzare il membro del team assegnato al percorso.

Se non si dispone ancora di autorizzazioni sufficienti per accedere al personale, verrà visualizzato il messaggio seguente: "Oops, sembra che non sia autorizzato a visualizzare il personale al momento. Per ulteriori informazioni, contattare l'amministratore. "

### <a name="find-a-staff-member-in-my-staff"></a>Trova un membro del personale del personale

Prima di poter iniziare a gestirli, è necessario aprire il profilo di un membro del personale.

1. [Aprire il personale](https://aka.ms/mystaff) e, se necessario, selezionare una località.

    ![Selezionare una località per un membro del team nel personale](media/my-staff-team-manager/allaus.png)

1. Aprire il profilo di un membro del team.

    ![Selezionare uno degli utenti in una località del personale](media/my-staff-team-manager/aupage.png)

## <a name="reset-a-user-password"></a>Reimpostare la password di un utente

Se l'organizzazione ha concesso l'autorizzazione, è possibile reimpostare le password per i membri del personale.

1. [Aprire il personale](https://aka.ms/mystaff).
1. Aprire il profilo di un membro del personale.
1. Selezionare **Reimposta password**.

    ![Reimpostare una password utente nel personale](media/my-staff-team-manager/resetpassword1.png)

1. Generare o immettere la nuova password. Potrebbe essere visualizzata una password temporanea generata automaticamente oppure è possibile che venga richiesto di immettere una password temporanea per l'utente.

    ![Copia la password utente temporanea dopo una reimpostazione del personale](media/my-staff-team-manager/resetpassword2.png)

Dopo aver reimpostato la password dell'utente, assegnare all'utente la password temporanea. Quando l'utente accede con la password temporanea, è necessario modificarlo.

## <a name="manage-a-users-phone-number"></a>Gestire il numero di telefono di un utente

Se l'organizzazione ha concesso l'autorizzazione, è possibile gestire i numeri di telefono per i membri del personale.

### <a name="add-a-phone-number"></a>Aggiungere un numero di telefono

1. [Aprire il personale](https://aka.ms/mystaff).
1. Aprire il profilo di un membro del personale.
1. Selezionare **Aggiungi numero di telefono**.

    ![Aggiungi un numero di telefono dell'utente al personale](media/my-staff-team-manager/addphone1.png)

1. Aggiungere il numero di telefono e selezionare **Salva**.

    ![Salva il numero di telefono dell'utente aggiunto nel personale](media/my-staff-team-manager/addphone2.png)

Dopo aver registrato un numero di telefono per un utente, può usarlo per accedere con SMS, eseguire la verifica in due passaggi o reimpostare la password autonomamente, a seconda delle impostazioni dell'organizzazione.

![Nuovo numero di telefono registrato con il personale](media/my-staff-team-manager/addphone3.png)

### <a name="edit-a-phone-number"></a>Modificare un numero di telefono

1. [Aprire il personale](https://aka.ms/mystaff).
1. Aprire il profilo di un membro del personale.
1. Selezionare **modifica numero di telefono**.

    ![Selezionare modifica dal profilo utente nel personale](media/my-staff-team-manager/editphone2.png)

1. Immettere il nuovo numero di telefono e selezionare **Salva**.

    ![Modificare il numero di telefono di un membro del personale nel personale](media/my-staff-team-manager/editphone1.png)

### <a name="enable-phone-number-sign-in-for-a-user"></a>Abilitare l'accesso ai numeri di telefono per un utente

Se l'accesso con un numero di telefono come nome utente (accesso SMS) è abilitato nell'organizzazione, è possibile aggiungere questa autenticazione a un numero di telefono dell'utente esistente.

1. [Aprire il personale](https://aka.ms/mystaff).
1. Aprire il profilo di un membro del personale.
1. Se nella parte inferiore della schermata è presente un messaggio che informa che è disponibile l'accesso con il numero di telefono come nome utente, selezionare **Abilita** per avviare il processo. Questo messaggio viene visualizzato se l'utente è stato abilitato per accedere con il numero di telefono.

    ![Visualizza il messaggio quando l'accesso tramite telefono è supportato in una località del personale](media/my-staff-team-manager/enableforms1.png)

1. Al termine, scegliere **OK**.

    ![Rimuovere un numero di telefono del membro del personale nel personale](media/my-staff-team-manager/enableforms2.png)

### <a name="remove-a-phone-number"></a>Rimuovere un numero di telefono

1. [Aprire il personale](https://aka.ms/mystaff).
1. Aprire il profilo di un membro del personale.
1. Selezionare **Rimuovi numero di telefono**.
1. Al termine, selezionare **Elimina** .

    ![Rimuovere un numero di telefono del membro del personale nel personale](media/my-staff-team-manager/deletephone1.png)
