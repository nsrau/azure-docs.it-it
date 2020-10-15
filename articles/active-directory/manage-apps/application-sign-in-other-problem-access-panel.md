---
title: Risolvere i problemi di accesso a un'applicazione da Azure AD app personali
description: Risolvere i problemi di accesso a un'applicazione da Azure AD app personali
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 3b9acc3f91a91527e96f450d825d06f6b909e15d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91729152"
---
# <a name="troubleshoot-problems-signing-in-to-an-application-from-azure-ad-my-apps"></a>Risolvere i problemi di accesso a un'applicazione da Azure AD app personali

My Apps è un portale basato sul Web che consente a un utente con un account aziendale o dell'Istituto di istruzione in Azure Active Directory (Azure AD) di visualizzare e avviare applicazioni basate sul cloud a cui l'amministratore Azure AD ha concesso l'accesso. È possibile accedere alle app personali usando un Web browser all'indirizzo [https://myapps.microsoft.com](https://myapps.microsoft.com) .

Per altre informazioni sull'uso di Azure AD come provider di identità per un'app, vedere la pagina relativa alla [gestione delle applicazioni in Azure ad](what-is-application-management.md). Per iniziare rapidamente, vedere la [serie di guide introduttive sulla gestione delle applicazioni](view-applications-portal.md).

Queste applicazioni sono configurate per conto dell'utente nel portale di Azure AD. L'applicazione deve essere configurata correttamente e assegnata all'utente o a un gruppo di cui l'utente è membro per visualizzare l'applicazione in app personali. 

Il tipo di app che l'utente può visualizzare rientra nelle categorie seguenti:
-   Applicazioni Microsoft 365
-   Applicazioni Microsoft e di terze parti configurate con il servizio Single Sign-On basato su federazione
-   Applicazioni SSO basate su password
-   Applicazioni con soluzioni SSO esistenti

Ecco alcuni aspetti da controllare se un'app viene visualizzata o meno.
- Assicurarsi che l'app venga aggiunta a Azure AD e assicurarsi che l'utente sia assegnato. Per altre informazioni, vedere la [serie di guide introduttive sulla gestione delle applicazioni](add-application-portal.md).
- Se un'app è stata aggiunta di recente, chiedere all'utente di disconnettersi ed eseguire di nuovo l'accesso. 
- Se l'app richiede una licenza, ad esempio Office, assicurarsi che all'utente sia assegnata la licenza appropriata.
- Il tempo necessario per le modifiche alle licenze può variare a seconda delle dimensioni e della complessità del gruppo.

## <a name="general-issues-to-check-first"></a>Problemi generali da verificare prima

-   Verificare che il Web browser soddisfi i requisiti, vedere [browser supportati dalle app personali](../user-help/my-apps-portal-end-user-access.md).
-   Verificare che il browser dell'utente abbia aggiunto l'URL ai **siti attendibili** dell'applicazione.
-   Assicurarsi di controllare che l'applicazione sia **configurata** correttamente.
-   Verificare che l'account dell'utente sia **abilitato** per l'accesso.
-   Verificare che l'account dell'utente non sia **bloccato**.
-   Verificare che la **password dell'utente non sia scaduta o non sia stata dimenticata**.
-   Verificare che **Multi-Factor Authentication** non blocchi l'accesso utente.
-   Verificare che un criterio di **accesso condizionale** o di **protezione delle identità** non blocchi l'accesso utente.
-   Verificare che le **informazioni di contatto per l'autenticazione** di un utente siano aggiornate per permettere l'applicazione di Multi-Factor Authentication o di criteri di accesso condizionale.
-   Assicurarsi anche di cancellare i cookie del browser e riprovare ad accedere.

## <a name="problems-with-the-users-account"></a>Problemi relativi all'account dell'utente
L'accesso alle app personali può essere bloccato a causa di un problema con l'account dell'utente. Di seguito sono riportati alcuni modi in cui è possibile risolvere i problemi relativi agli utenti e alle impostazioni degli account:
-   [Controllare se esiste un account utente in Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)
-   [Controllare lo stato dell'account di un utente](#check-a-users-account-status)
-   [Reimposta la password di un utente](#reset-a-users-password)
-   [Abilitare la reimpostazione self-service delle password](#enable-self-service-password-reset)
-   [Controllare lo stato di autenticazione a più fattori di un utente](#check-a-users-multi-factor-authentication-status)
-   [Controllare le informazioni di contatto per l'autenticazione di un utente](#check-a-users-authentication-contact-info)
-   [Controllare le appartenenze a gruppi dell'utente](#check-a-users-group-memberships)
-   [Controllare le licenze assegnate di un utente](#check-a-users-assigned-licenses)
-   [Assegnare una licenza a un utente](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Controllare se esiste un account utente in Azure Active Directory
Per controllare se l'account di un utente è presente, seguire questa procedura:
1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.
2.  Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.
3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.
4.  Selezionare **utenti e gruppi** nel menu di navigazione.
5.  Selezionare **Tutti gli utenti**.
6.  **Cercare** l'utente a cui si è interessati e **selezionare la riga** da selezionare.
7.  Controllare le proprietà dell'oggetto utente per assicurarsi che vengano specificate come previsto e che non manchi alcun dato.

### <a name="check-a-users-account-status"></a>Controllare lo stato dell'account di un utente
Per controllare lo stato dell'account di un utente, seguire questa procedura:
1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.
2.  Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.
3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.
4.  Selezionare **utenti e gruppi** nel menu di navigazione.
5.  Selezionare **Tutti gli utenti**.
6.  **Cercare** l'utente a cui si è interessati e **selezionare la riga** da selezionare.
7.  Selezionare **profilo**.
8.  In **Impostazioni** assicurarsi che l'opzione **Blocca l'accesso** sia impostata su **No**.

### <a name="reset-a-users-password"></a>Reimpostare la password di un utente
Per reimpostare la password di un utente, seguire questa procedura:
1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.
2.  Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.
3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.
4.  Selezionare **utenti e gruppi** nel menu di navigazione.
5.  Selezionare **Tutti gli utenti**.
6.  **Cercare** l'utente a cui si è interessati e **selezionare la riga** da selezionare.
7.  Selezionare il pulsante **Reimposta password** nella parte superiore del riquadro utente.
8.  Selezionare il pulsante **Reimposta password** nel riquadro **Reimposta password** visualizzato.
9.  Copiare la **password temporanea** o **immettere una nuova password** per l'utente.
10. Comunicare questa nuova password all'utente, che dovrà modificarla al successivo accesso ad Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Abilitare la reimpostazione self-service delle password
Per abilitare la reimpostazione self-service delle password, seguire questa procedura di distribuzione:
-   [Consentire agli utenti di reimpostare le password di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)
-   [Consentire agli utenti di reimpostare o modificare le password locali di Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>Controllare lo stato di autenticazione a più fattori di un utente
Per controllare lo stato di autenticazione a più fattori di un utente, seguire questa procedura:
1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.
2. Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.
3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.
4. Selezionare **utenti e gruppi** nel menu di navigazione.
5. Selezionare **Tutti gli utenti**.
6. Selezionare il pulsante **multi-factor authentication** nella parte superiore del riquadro.
7. Una volta caricato il **portale di amministrazione di multi-factor authentication** , assicurarsi di essere nella scheda **utenti** .
8. Trovare l'utente nell'elenco degli utenti tramite una ricerca, l'applicazione di un filtro o l'ordinamento.
9. Selezionare l'utente nell'elenco di utenti e scegliere **Abilita**, **Disabilita** o **Applica** per l'autenticazione a più fattori nel modo desiderato.
   >[!NOTE]
   >Se lo stato di un utente è impostato su **Applicato**, è possibile impostarlo temporaneamente su **Disattivato** per permettere all'utente di accedere di nuovo al proprio account. A questo punto, è possibile modificare nuovamente lo stato in **Attivato** in modo che l'utente debba registrare di nuovo le informazioni di contatto al successivo accesso. In alternativa, è possibile eseguire la procedura descritta in [Controllare le informazioni di contatto per l'autenticazione di un utente](#check-a-users-authentication-contact-info) per verificare o impostare questi dati per l'utente.

### <a name="check-a-users-authentication-contact-info"></a>Controllare le informazioni di contatto per l'autenticazione di un utente
Per controllare le informazioni di contatto per l'autenticazione di un utente usate per l'autenticazione a più fattori, l'accesso condizionale, la protezione delle identità e la reimpostazione della password, seguire questa procedura:
1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.
2.  Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.
3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.
4.  Selezionare **utenti e gruppi** nel menu di navigazione.
5.  Selezionare **Tutti gli utenti**.
6.  **Cercare** l'utente a cui si è interessati e **selezionare la riga** da selezionare.
7.  Selezionare **profilo**.
8.  Scorrere fino a **Informazioni di contatto per l'autenticazione**.
9.  Fare clic su **Verifica** per controllare i dati registrati per l'utente e aggiornarli nel modo necessario.

### <a name="check-a-users-group-memberships"></a>Controllare le appartenenze a gruppi dell'utente
Per controllare l'appartenenza a gruppi di un utente, seguire questa procedura:
1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.
2.  Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.
3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.
4.  Selezionare **utenti e gruppi** nel menu di navigazione.
5.  Selezionare **Tutti gli utenti**.
6.  **Cercare** l'utente a cui si è interessati e **selezionare la riga** da selezionare.
7.  Selezionare **gruppi** per visualizzare i gruppi di cui l'utente è membro.

### <a name="check-a-users-assigned-licenses"></a>Controllare le licenze assegnate di un utente
Per controllare le licenze assegnate a un utente, seguire questa procedura:
1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.
2.  Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.
3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.
4.  Selezionare **utenti e gruppi** nel menu di navigazione.
5.  Selezionare **Tutti gli utenti**.
6.  **Cercare** l'utente a cui si è interessati e **selezionare la riga** da selezionare.
7.  Selezionare **licenze** per visualizzare le licenze attualmente assegnate all'utente.

### <a name="assign-a-user-a-license"></a>Assegnare una licenza a un utente 
Per assegnare una licenza a un utente, seguire questa procedura:
1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.
2.  Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.
3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.
4.  Selezionare **utenti e gruppi** nel menu di navigazione.
5.  Selezionare **Tutti gli utenti**.
6.  **Cercare** l'utente a cui si è interessati e **selezionare la riga** da selezionare.
7.  Selezionare **licenze** per visualizzare le licenze attualmente assegnate all'utente.
8.  Selezionare il pulsante **assegna** .
9.  Selezionare **uno o più prodotti** nell'elenco dei prodotti disponibili.
10. **Facoltativo** selezionare l'elemento **Opzioni di assegnazione** per assegnare in modo granulare i prodotti. Selezionare **OK**.
11. Selezionare il pulsante **assegna** per assegnare queste licenze a questo utente.

## <a name="troubleshooting-deep-links"></a>Risoluzione dei problemi relativi ai collegamenti profondi
I collegamenti diretti o gli URL di accesso utente sono collegamenti che gli utenti potrebbero utilizzare per accedere alle applicazioni con SSO basato su password direttamente dalla barra degli indirizzi del browser. Passando a questo collegamento, gli utenti accedono automaticamente all'applicazione senza dover prima accedere alle app personali. Il collegamento è identico a quello usato dagli utenti per accedere a queste applicazioni dal Microsoft 365 l'utilità di avvio delle applicazioni.

### <a name="checking-the-deep-link"></a>Controllo del collegamento profondo

Per verificare se il collegamento è corretto, seguire questa procedura:
1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore.**
2. Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.
3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.
4. Selezionare **applicazioni aziendali** nel menu di spostamento a sinistra Azure Active Directory.
5. Selezionare **tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.
   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.
6. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore.**
7. Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.
8. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.
9. Selezionare **applicazioni aziendali** nel menu di spostamento a sinistra Azure Active Directory.
10. Selezionare **tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.
    * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.
11. Selezionare l'applicazione per cui si desidera controllare il collegamento profondo.
12. Trovare l'etichetta **URL accesso utente**. Il collegamento Deep deve corrispondere a questo URL.

## <a name="contact-support"></a>Contattare il supporto tecnico
Aprire un ticket di supporto con le informazioni seguenti, se disponibili:
-   ID errore di correlazione
-   UPN (indirizzo di posta elettronica dell'utente)
-   ID tenant
-   Tipo di browser
-   Fuso orario e ora o intervallo di tempo durante il quale si verifica l'errore
-   Tracce Fiddler

## <a name="next-steps"></a>Passaggi successivi
- [Serie di guide di avvio rapido sulla gestione delle applicazioni](view-applications-portal.md)
