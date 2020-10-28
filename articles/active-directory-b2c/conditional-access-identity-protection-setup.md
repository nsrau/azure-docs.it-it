---
title: Configurare Identity Protection e l'accesso condizionale in Azure AD B2C
description: Informazioni su come configurare Identity Protection e l'accesso condizionale per i tenant di Azure AD B2C in modo da visualizzare accessi rischioso e altri eventi di rischio e creare criteri in base ai rilevamenti di rischi.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb585e2ccf8c8ed071b5156961adf48d4e4b108d
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309777"
---
# <a name="set-up-identity-protection-and-conditional-access-in-azure-ad-b2c"></a>Configurare Identity Protection e l'accesso condizionale in Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Identity Protection offre il rilevamento di rischi continuo per il tenant di Azure AD B2C. Se il piano tariffario di Azure AD B2C tenant è Premium P2, è possibile visualizzare gli eventi di rischio dettagliati di Identity Protection nel portale di Azure. È anche possibile usare criteri di [accesso condizionale](../active-directory/conditional-access/overview.md) basati su questi rilevamenti di rischi per determinare le azioni da adottare e applicare i criteri dell'organizzazione.

## <a name="prerequisites"></a>Prerequisiti

- Il tenant di Azure AD B2C deve essere [collegato a una sottoscrizione di Azure AD](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription).
- Azure AD B2C Premium P2 è necessario per usare l'accesso condizionale basato su rischi utente e di accesso. Se necessario, [modificare il piano tariffario di Azure AD B2C in Premium P2](https://aka.ms/exid-pricing-tier). 
- Per gestire Identity Protection e l'accesso condizionale nel tenant B2C, è necessario un account a cui è assegnato il ruolo di amministratore globale o di amministratore della sicurezza.
- Per usare queste funzionalità nel tenant, è prima necessario passare al piano tariffario Premium P2 di Azure AD B2C.

## <a name="set-up-identity-protection"></a>Configurare Identity Protection

Identity Protection è attivato per impostazione predefinita. Per visualizzare gli eventi di rischio di Identity Protection nel tenant di Azure AD B2C, è sufficiente collegare il tenant di Azure AD B2C a una sottoscrizione di Azure AD e selezionare il piano tariffario Premium P2 di Azure AD B2C. È possibile visualizzare i report degli eventi di rischio dettagliati nel portale di Azure.

### <a name="supported-identity-protection-risk-detections"></a>Rilevamenti di rischi di Identity Protection supportati

Per Azure AD B2C sono attualmente supportati i rilevamenti di rischi seguenti:  

|Tipo di rilevamento dei rischi  |Descrizione  |
|---------|---------|
| Trasferimento atipico     | Accesso da una posizione insolita in base agli accessi recenti dell'utente.        |
|Indirizzo IP anonimo     | Accesso da indirizzo IP anonimo (ad esempio Tor Browser, VPN per navigazione in anonimato).        |
|Indirizzo IP collegato a malware     | Accesso da indirizzo IP collegato a malware.         |
|Proprietà di accesso insolite     | Accesso con proprietà non osservate di recente per l'utente specificato.        |
|L'amministratore ha confermato che l'utente è compromesso    | Un amministratore ha indicato che un utente è stato compromesso.             |
|Password spraying     | Accedere tramite un attacco password spraying.      |
|Intelligence per le minacce di Azure AD     | Le origini di intelligence Microsoft per le minacce interne ed esterne hanno identificato uno schema di attacco noto.        |

## <a name="view-risk-events-for-your-azure-ad-b2c-tenant"></a>Visualizzare gli eventi di rischio per il tenant di Azure AD B2C

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.

1. Nel portale di Azure cercare e selezionare **Azure AD B2C** .

1. In **Sicurezza** selezionare **Utenti a rischio (anteprima)** .

   ![Utenti a rischio](media/conditional-access-identity-protection-setup/risky-users.png)

1. In **Sicurezza** selezionare **Rilevamenti dei rischi (anteprima)** .

   ![Rilevamenti dei rischi](media/conditional-access-identity-protection-setup/risk-detections.png)

## <a name="add-a-conditional-access-policy"></a>Aggiungere un criterio di accesso condizionale 

Per aggiungere un criterio di accesso condizionale basato su rilevamenti di rischi di Identity Protection, assicurarsi che le impostazioni predefinite di sicurezza siano disabilitate per il tenant di Azure AD B2C e quindi creare criteri di accesso condizionale.

### <a name="to-disable-security-defaults"></a>Per disabilitare le impostazioni predefinite per la sicurezza

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.

3. Nel portale di Azure cercare e selezionare **Azure Active Directory** .

4. Selezionare **Proprietà** e quindi **Gestisci le impostazioni predefinite per la sicurezza** .

   ![Disabilitare le impostazioni predefinite per la sicurezza](media/conditional-access-identity-protection-setup/disable-security-defaults.png)

5. In Abilita le impostazioni predefinite per la sicurezza selezionare No. 

   ![Impostare l'opzione Abilita le impostazioni predefinite per la sicurezza su No](media/conditional-access-identity-protection-setup/enable-security-defaults-toggle.png)

### <a name="to-create-a-conditional-access-policy"></a>Per creare un criterio di accesso condizionale

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.

1. Nel portale di Azure cercare e selezionare **Azure AD B2C** .

1. In **Sicurezza** selezionare **Accesso condizionale (anteprima)** . Viene visualizzata la pagina **Criteri di accesso condizionale** . 

1. Selezionare **Nuovo criterio** e seguire la documentazione relativa all'accesso condizionale di Azure AD per creare un nuovo criterio. Di seguito è riportato un esempio:

   - [Accesso condizionale basato sul rischio di accesso: abilitazione con i criteri di accesso condizionale](../active-directory/conditional-access/howto-conditional-access-policy-risk.md#enable-with-conditional-access-policy)

   > [!IMPORTANT]
   > Quando si selezionano gli utenti a cui si vuole applicare il criterio, non selezionare solo **Tutti gli utenti** altrimenti è possibile che l'accesso venga bloccato anche per il proprio account.

## <a name="test-the-conditional-access-policy"></a>Testare il criterio di accesso condizionale

1. Creare un criterio di accesso condizionale come descritto in precedenza, con le impostazioni seguenti:
   
   - Per **Utenti e gruppi** selezionare l'utente di test. Non selezionare **Tutti gli utenti** altrimenti l'accesso verrà bloccato anche per il proprio account.
   - Per **Applicazioni cloud o azioni** scegliere **Selezionare le app** e quindi scegliere l'applicazione relying party.
   - Per Condizioni selezionare **Rischio di accesso** e i livelli di rischio **Elevato** , **Medio** e **Basso** .
   - Per **Concedi** scegliere **Blocca l'accesso** .

      ![Scegliere Blocca l'accesso](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

1. Per abilitare il criterio di accesso condizionale di test, selezionare **Crea** .

1. Per simulare un accesso a rischio, usare [Tor Browser](https://www.torproject.org/download/). 

1. Nel token decodificato jwt.ms relativo al tentativo di accesso è indicato che l'accesso è stato bloccato:

   ![Testare un accesso bloccato](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>Esaminare i risultati dell'accesso condizionale in Report di controllo

Per esaminare il risultato di un evento di accesso condizionale:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.

3. Nel portale di Azure cercare e selezionare **Azure AD B2C** .

4. In **Attività** selezionare **Log di controllo** .

5. Filtrare il log di controllo impostando **Categoria** su **B2C** e **Tipo di risorsa attività** su **IdentityProtection** . Selezionare quindi **Applica** .

6. Esaminare l'attività di controllo per un massimo di 7 giorni. Sono inclusi i tipi di attività seguenti:

   - **Valutazione dei criteri di accesso condizionale** : questa voce del log di controllo indica che durante un'autenticazione è stata eseguita una valutazione di accesso condizionale.
   - **Correzione degli utenti** : questa voce indica che la concessione o i requisiti di un criterio di accesso condizionale sono stati soddisfatti dall'utente finale e che questa attività è stata segnalata al motore di rischio per ridurre il rischio o mitigarlo per l'utente.

7. Selezionare una voce del log per la **valutazione dei criteri di accesso condizionale** nell'elenco per aprire la pagina **Dettagli attività: log di controllo** , che mostra gli identificatori dei log di controllo, unitamente a queste informazioni nella sezione **Altri dettagli** :

   - ConditionalAccessResult: concessione richiesta dalla valutazione dei criteri condizionali.
   - AppliedPolicies: elenco di tutti i criteri di accesso condizionale in cui sono state soddisfatte le condizioni e i criteri sono attivati.
   - ReportingPolicies: elenco dei criteri di accesso condizionale impostati sulla modalità di sola segnalazione e in cui sono state soddisfatte le condizioni.

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere l'accesso condizionale a un flusso utente](conditional-access-user-flow.md).
