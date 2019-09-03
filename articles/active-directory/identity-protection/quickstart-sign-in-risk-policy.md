---
title: "Guida introduttiva: bloccare l'accesso quando viene rilevato un rischio per la sessione con Azure Active Directory Identity Protection | Microsoft Docs"
description: La presente guida di avvio rapido descrive come configurare criteri di accesso condizionale per il rischio di accesso di Azure Active Directory (Azure AD) Identity Protection per bloccare gli accessi in base ai rischi per la sessione.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: quickstart
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed5e5e26a27e13ba09ffcc97e0b2b0f1b37bc8bd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127705"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-identity-protection"></a>Guida introduttiva: bloccare l'accesso quando viene rilevato un rischio per la sessione con Azure Active Directory Identity Protection  

Per mantenere l'ambiente protetto, è possibile bloccare l'accesso per gli utenti sospetti. Azure Active Directory (Azure AD) Identity Protection analizza ogni accesso e calcola la probabilità che un tentativo di accesso non sia stato eseguito dal proprietario legittimo di un account utente. La probabilità (bassa, media, elevata) è indicata in forma di valore calcolato denominato livello di rischio di accesso. Impostando la condizione di rischio di accesso, è possibile configurare criteri di accesso condizionale per il rischio di accesso per rispondere a livelli di rischio di accesso specifici. 

Questa guida di avvio rapido illustra come configurare criteri di accesso condizionale per il rischio di accesso per bloccare un accesso quando è stato rilevato un livello di rischio di accesso medio o superiore. 

![Crea criteri](./media/quickstart-sign-in-risk-policy/1004.png)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti 

Per completare lo scenario in questa esercitazione, sono necessari gli elementi seguenti:

- **Accesso a un'edizione di Azure AD Premium P2**: Azure AD Identity Protection è una funzionalità di Azure AD Premium P2. 
- **Identity Protection**: lo scenario in questa guida introduttiva richiede l'abilitazione di Identity Protection. Per scoprire come abilitare Identity Protection, vedere [Abilitazione di Azure Active Directory Identity Protection](../identity-protection/enable.md).
- **Tor Browser**: [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) è progettato per salvaguardare la privacy online. Identity Protection rileva un accesso da un Tor Browser come **accesso da indirizzi IP anonimi**, con un livello di rischio medio. Per altre informazioni, vedere [Rilevamenti dei rischi in Azure Active Directory](../reports-monitoring/concept-risk-events.md).  
- **Account di test denominato Alain Charon**: se non si conosce la procedura per creare un account di test, vedere [Aggiungere un nuovo utente](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Verificare il proprio accesso 

L'obiettivo di questo passaggio è assicurarsi che l'account di test possa accedere al tenant tramite Tor Browser.

**Per verificare il proprio accesso:**

1. Accedere al [portale di Azure](https://portal.azure.com) come **Alain Charon**.
2. Uscire, 

## <a name="create-your-conditional-access-policy"></a>Creare i criteri di accesso condizionale 

Lo scenario di questo argomento di avvio rapido usa l'accesso da un Tor Browser per generare un rilevamento del rischio di tipo **accessi provenienti da indirizzi IP anonimi**. Il livello di rischio di questo rilevamento è medio. Per rispondere a questo rilevamento, impostare la condizione di rischio di accesso su medio. 

Questa sezione illustra come creare i criteri di accesso condizionale richiesti per il rischio di accesso. Nei criteri, impostare:

|Impostazione |Valore|
|---     | --- |
| Utenti  | Alain Charon  |
| Condizioni | Rischio di accesso, livello medio e superiore |
| Controlli | Blocca accesso |

![Crea criteri](./media/quickstart-sign-in-risk-policy/201.png)

**Per configurare i criteri di accesso condizionale:**

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.
2. Andare alla [pagina Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/Overview).
3. Nella pagina **Azure AD Identity Protection** fare clic su **Criteri di rischio di accesso** nella sezione **Configura**.
4. Nella sezione **Assegnazioni** della pagina dei criteri fare clic su **Utenti**.
5. Nella pagina **Utenti** fare clic su **Seleziona utenti**.
6. Nella pagina **Seleziona utenti** selezionare **Alain Charon**, quindi fare clic su **Seleziona**.
7. Nella pagina **Utenti** fare clic su **Fine**. 
8. Nella sezione **Assegnazioni** della pagina dei criteri fare clic su **Condizioni**.
9. Nella pagina **Condizioni** fare clic su **Rischio di accesso**.
10. Nella pagina **Rischio di accesso** selezionare **Medio e superiore**, quindi fare clic su **Seleziona**. 
11. Nella pagina **Condizioni** fare clic su **Fatto**.
12. Nella sezione **Controlli** della pagina dei criteri fare clic su **Accesso**.
13. Nella pagina **Accesso** fare clic su **Consenti accesso**, selezionare **Richiedi autenticazione a più fattori** e quindi fare clic su **Seleziona**.
14. Nella pagina dei criteri fare clic su **Salva**.  

## <a name="test-your-conditional-access-policy"></a>Testare i criteri di accesso condizionale

Per testare i criteri, provare a effettuare l'accesso al [portale di Azure](https://portal.azure.com) come **Alan Charon** usando Tor Browser. Il tentativo di accesso dovrebbe essere bloccato dai criteri di accesso condizionale.

![Autenticazione a più fattori](./media/quickstart-sign-in-risk-policy/203.png)


## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare l'utente di test, Tor Browser e disabilitare i criteri di accesso condizionale per il rischio di accesso:

- Se non si conosce la procedura per eliminare un utente di Azure AD, vedere [Come aggiungere o eliminare utenti](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Per istruzioni su come rimuovere Tor Browser, vedere [Disinstallazione](https://tb-manual.torproject.org/uninstalling/).
