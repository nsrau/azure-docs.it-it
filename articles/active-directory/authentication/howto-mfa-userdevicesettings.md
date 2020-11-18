---
title: Gestire i metodi di autenticazione per Azure AD Multi-Factor Authentication-Azure Active Directory
description: Informazioni su come configurare Azure Active Directory impostazioni utente per Azure AD Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1117b5194dae92a8025594ed71fc629670d875cf
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94838928"
---
# <a name="manage-user-authentication-methods-for-azure-ad-multi-factor-authentication"></a>Gestire i metodi di autenticazione utente per Azure AD Multi-Factor Authentication

Gli utenti in Azure AD hanno due set distinti di informazioni di contatto:  

- Informazioni di contatto del profilo pubblico, che viene gestito nel profilo utente e visibile ai membri dell'organizzazione. Per gli utenti sincronizzati da Active Directory locali, queste informazioni vengono gestite in Active Directory Domain Services Windows Server locale.
- Metodi di autenticazione, che vengono sempre mantenuti privati e utilizzati solo per l'autenticazione, incluso multi-factor authentication. Gli amministratori possono gestire questi metodi nel pannello del metodo di autenticazione di un utente e gli utenti possono gestire i metodi nella pagina delle informazioni di sicurezza dell'account.

Quando si gestiscono Azure AD metodi Multi-Factor Authentication per gli utenti, gli amministratori dell'autenticazione possono: 

1. Aggiungere i metodi di autenticazione per un utente specifico, inclusi i numeri di telefono usati per l'autenticazione a più fattori.
1. Reimposta la password di un utente.
1. Richiedere a un utente di ripetere la registrazione per l'autenticazione a più fattori.
1. Revocare le sessioni di autenticazione a più fattori esistenti.
1. Elimina le password dell'app esistente di un utente  

## <a name="add-authentication-methods-for-a-user"></a>Aggiungere metodi di autenticazione per un utente 

È possibile aggiungere metodi di autenticazione per un utente tramite il portale di Azure o Microsoft Graph.  

> [!NOTE]
> Per motivi di sicurezza, i campi delle informazioni di contatto dell'utente pubblico non devono essere usati per eseguire l'autenticazione a più fattori. Gli utenti devono invece popolare i numeri dei metodi di autenticazione da usare per l'autenticazione a più fattori.  

:::image type="content" source="media/howto-mfa-userdevicesettings/add-authentication-method-detail.png" alt-text="Aggiungere metodi di autenticazione dal portale di Azure":::

Per aggiungere metodi di autenticazione per un utente tramite la portale di Azure:  

1. Accedere al **portale di Azure**. 
1. Passare a **Azure Active Directory**  >  **utenti**  >  **tutti gli utenti**. 
1. Scegliere l'utente per il quale si desidera aggiungere un metodo di autenticazione e selezionare **metodi di autenticazione**.  
1. Nella parte superiore della finestra selezionare **+ Aggiungi metodo di autenticazione**.
   1. Selezionare un metodo (numero di telefono o indirizzo di posta elettronica). È possibile usare la posta elettronica per la reimpostazione della password self-service ma non per l'autenticazione. Quando si aggiunge un numero di telefono, selezionare un tipo di telefono e immettere il numero di telefono con formato valido, ad esempio + 1 4255551234.
   1. Selezionare **Aggiungi**.

> [!NOTE]
> L'esperienza di anteprima consente agli amministratori di aggiungere tutti i metodi di autenticazione disponibili per gli utenti, mentre l'esperienza originale consente solo l'aggiornamento di metodi telefonici e telefoni alternativi.

### <a name="manage-methods-using-powershell"></a>Gestire i metodi usando PowerShell:  

Installare il modulo di PowerShell Microsoft. Graph. Identity. accesso usando i comandi seguenti. 

```powershell
Install-module Microsoft.Graph.Identity.Signins
Connect-MgGraph -Scopes UserAuthenticationMethod.ReadWrite.All
Select-MgProfile -Name beta
```

Elenca i metodi di autenticazione basati su telefono per un utente specifico.

```powershell
Get-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com
```

Creare un metodo di autenticazione del telefono cellulare per un utente specifico.

```powershell
New-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -phoneType “mobile” -phoneNumber "+1 7748933135"
```

Rimuovere un metodo telefonico specifico per un utente

```powershell
Remove-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -PhoneAuthenticationMethodId 3179e48a-750b-4051-897c-87b9720928f7
```

I metodi di autenticazione possono essere gestiti anche usando le API Microsoft Graph. per altre informazioni, vedere la pagina relativa alla [Panoramica dell'API dei metodi di autenticazione](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true) del documento Azure ad

## <a name="manage-user-authentication-options"></a>Gestisci opzioni di autenticazione utente

Se viene assegnato il ruolo di *amministratore dell'autenticazione* , è possibile richiedere agli utenti di reimpostare la password, ripetere la registrazione per l'autenticazione a più fattori o revocare le sessioni di autenticazione a più fattori esistenti dall'oggetto utente. Per gestire le impostazioni utente, completare i passaggi seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
1. Scegliere l'utente su cui si vuole eseguire un'azione e selezionare **metodi di autenticazione**. Nella parte superiore della finestra, scegliere una delle opzioni seguenti per l'utente:
   - **Reset password Reimposta** la password dell'utente e assegna una password temporanea che deve essere modificata al successivo accesso.
   - È **necessario ripetere la registrazione** dell'autenticazione a più fattori, in modo che quando l'utente esegue l'accesso alla prossima volta, viene richiesto di configurare un nuovo metodo di autenticazione a più fattori.
   
      > [!NOTE]
      > I metodi di autenticazione attualmente registrati dell'utente non vengono eliminati quando un amministratore richiede la ripetizione della registrazione per l'autenticazione a più fattori. Quando un utente esegue nuovamente la registrazione per l'autenticazione a più fattori, è consigliabile esaminare le informazioni di sicurezza ed eliminare eventuali metodi di autenticazione precedentemente registrati che non sono più utilizzabili.
   
   - **Revoca le sessioni** di autenticazione a più fattori consente di cancellare le sessioni di autenticazione a più fattori memorizzate dall'utente e le richiede di eseguire l'autenticazione a più fattori la volta successiva che è richiesta dal criterio sul dispositivo.
   
    :::image type="content" source="media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png" alt-text="Gestire i metodi di autenticazione dal portale di Azure":::

## <a name="delete-users-existing-app-passwords"></a>Elimina le password dell'app esistenti degli utenti

Per gli utenti che hanno definito password di app, gli amministratori possono anche scegliere di eliminare queste password, causando un errore di autenticazione legacy in tali applicazioni. Queste azioni potrebbero essere necessarie se è necessario fornire assistenza a un utente o se è necessario reimpostare i metodi di autenticazione. Le app non basate su browser associate a queste password di app smetteranno di funzionare fino a quando non verrà creata una nuova password dell'app. 

Per eliminare le password dell'app di un utente, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Sul lato sinistro selezionare **Azure Active Directory**  >  **utenti**  >  **tutti gli utenti**.
1. Selezionare **Multi-Factor Authentication**. Potrebbe essere necessario scorrere verso destra per visualizzare l'opzione del menu. Selezionare la schermata di esempio seguente per visualizzare la finestra di portale di Azure completa e il percorso del menu: [ ![ selezionare multi-factor authentication dalla finestra utenti in Azure ad.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Selezionare la casella accanto a uno o più utenti che si desidera gestire. A destra viene visualizzato un elenco di opzioni di passaggio rapido.
1. Selezionare **Gestisci impostazioni utente**, quindi selezionare la casella **Elimina tutte le password dell'app esistenti generate dagli utenti selezionati**, come illustrato nell'esempio seguente: ![ eliminare tutte le password dell'app esistenti](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Selezionare **Save (Salva**) e quindi **Close (Chiudi**).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come configurare le singole impostazioni utente. Per configurare le impostazioni generali del servizio Multi-Factor Authentication Azure AD, vedere [configurare le impostazioni di multi-factor authentication di Azure ad](howto-mfa-mfasettings.md).

Se gli utenti necessitano di assistenza, vedere la [Guida dell'utente per Azure AD multi-factor authentication](../user-help/multi-factor-authentication-end-user-first-time.md).
