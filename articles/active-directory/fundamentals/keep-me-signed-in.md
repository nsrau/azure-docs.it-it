---
title: Configurare il ' rimanere connesso?' Richiedi account Azure Active Directory
description: Informazioni su come rimanere connessi (KMSI), in cui viene visualizzato il resto dell'accesso? prompt, come configurarlo nel portale di Azure Active Directory e come risolvere i problemi di accesso.
services: active-directory
author: CelesteDG
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: fundamentals
ms.date: 06/05/2020
ms.author: celested
ms.reviewer: asteen, jlu, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: bed6bc43dfc15abf2bdf9f38a5de2240d348d6fb
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89320257"
---
# <a name="configure-the-stay-signed-in-prompt-for-azure-ad-accounts"></a>Configurare il ' rimanere connesso?' Richiedi account Azure AD

Mantieni l'accesso (KMSI) Visualizza un messaggio **di stato connesso?** dopo che un utente ha eseguito l'accesso. Se un utente risponde **Sì** a questo prompt, il servizio Mantieni l'accesso fornisce un [token di aggiornamento](../develop/developer-glossary.md#refresh-token)permanente. Per i tenant federati, il prompt verrà visualizzato dopo che l'utente ha eseguito l'autenticazione con il servizio di identità federato.

Il diagramma seguente illustra il flusso di accesso degli utenti per un tenant gestito e un tenant federato e il nuovo prompt di Mantieni l'accesso. Questo flusso contiene la logica intelligente in modo che l'opzione **rimane connesso?** non venga visualizzata se il sistema di Machine Learning rileva un accesso ad alto rischio o un accesso da un dispositivo condiviso.

:::image type="content" source="./media/keep-me-signed-in/kmsi-workflow.png" alt-text="Diagramma che mostra il flusso di accesso utente per un tenant gestito e federato":::

> [!NOTE]
> Per configurare l'opzione Mantieni l'accesso è necessario usare Azure Active Directory (Azure AD) Premium 1, Premium 2 o Basic Edition oppure per avere una licenza di Microsoft 365. Per altre informazioni sulle licenze e sulle edizioni, vedere [Iscriversi ad Azure AD Premium](active-directory-get-started-premium.md).<br><br>Le edizioni Azure AD Premium e Basic sono disponibili per i clienti in Cina che usano l'istanza globale di Azure AD. Le edizioni Azure AD Premium e Basic non sono attualmente supportate nel servizio di Azure gestito da 21Vianet in Cina. Per ulteriori informazioni, consultare la pagina relativa all'uso del [Forum Azure ad](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="configure-kmsi"></a>Configurare KMSI

1. Accedere al [portale di Azure](https://portal.azure.com/) con un account amministratore globale per la directory.
1. Selezionare **Azure Active Directory**, selezionare informazioni personalizzate distintive dell' **azienda**, quindi selezionare **Configura**.
1. Nella sezione **Impostazioni avanzate** trovare l' **opzione Mostra per mantenere l'accesso** .

   Questa impostazione consente di scegliere se gli utenti rimangono connessi per Azure AD fino alla disconnessione esplicita.
   * Se si sceglie **No**, l'opzione **rimane connesso?** viene nascosta dopo che l'utente ha effettuato l'accesso e l'utente deve eseguire l'accesso ogni volta che il browser viene chiuso e riaperto.
   * Se si sceglie **Sì**, l'opzione **rimane connesso?** viene visualizzata all'utente.

    :::image type="content" source="./media/keep-me-signed-in/kmsi-company-branding-advanced-settings-kmsi-1.png" alt-text="Screenshot che mostra l'opzione Mostra opzione per mantenere l'accesso":::

## <a name="troubleshoot-sign-in-issues"></a>Risolvere i problemi di accesso

Se un utente non ha **eseguito** la richiesta di accesso, come illustrato nel diagramma seguente, ma abbandona il tentativo di accesso, verrà visualizzata una voce di log di accesso che indica l'interruzione.

:::image type="content" source="./media/keep-me-signed-in/kmsi-stay-signed-in-prompt.png" alt-text="Mostra l'accesso rimanente? prompt":::

I dettagli sull'errore di accesso sono i seguenti e sono evidenziati nell'esempio.

* **Codice errore di accesso**: 50140
* **Motivo**dell'errore: questo errore è dovuto a un interrupt "Mantieni l'accesso" quando l'utente esegue l'accesso.

:::image type="content" source="./media/keep-me-signed-in/kmsi-sign-ins-log-entry.png" alt-text="Esempio di voce del log di accesso con l'interrupt Mantieni l'accesso":::

È possibile impedire agli utenti di visualizzare l'interrupt impostando l' **opzione Mostra per mantenere l'accesso** impostato su **No** nelle impostazioni di personalizzazione avanzate. In questo modo viene disabilitato il prompt KMSI per tutti gli utenti nella directory Azure AD.

È anche possibile usare i controlli della sessione del browser permanenti nell'accesso condizionale per impedire agli utenti di visualizzare il prompt KMSI. Questa opzione consente di disabilitare il prompt KMSI per un gruppo selezionato di utenti, ad esempio gli amministratori globali, senza influire sul comportamento di accesso per gli altri utenti nella directory. Per altre informazioni, vedere [frequenza di accesso utente](../conditional-access/howto-conditional-access-session-lifetime.md). 

Per assicurarsi che il prompt KMSI venga visualizzato solo quando può trarre vantaggio dall'utente, il prompt di KMSI non viene intenzionalmente visualizzato negli scenari seguenti:

* L'utente ha eseguito l'accesso tramite seamless SSO e l'autenticazione integrata di Windows (IWA)
* L'utente ha eseguito l'accesso tramite Active Directory Federation Services e IWA
* L'utente è un Guest nel tenant
* Il Punteggio di rischio dell'utente è elevato
* L'accesso viene eseguito durante il flusso di consenso dell'utente o dell'amministratore
* Il controllo della sessione del browser persistente è configurato in un criterio di accesso condizionale

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle altre impostazioni che influiscono sul timeout della sessione di accesso:

* Microsoft 365- [timeout della sessione inattiva](/sharepoint/sign-out-inactive-users)
* Azure AD l'accesso condizionale- [frequenza di accesso utente](../conditional-access/howto-conditional-access-session-lifetime.md)
* Portale di Azure: [timeout di inattività a livello di directory](../../azure-portal/set-preferences.md#change-the-directory-timeout-setting-admin)