---
title: Configurare la gestione della sessione di autenticazione con l'accesso condizionale di Azure Active Directory
description: Personalizzare la configurazione di sessione di autenticazione AD Azure tra cui accesso utente nel browser e frequenza di salvataggio permanente sessione.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ff9ad850b111cf080447b699d35b4ef8205e006
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190212"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Configurare la gestione della sessione di autenticazione con l'accesso condizionale

In distribuzioni complesse, le organizzazioni potrebbero essere necessario limitare le sessioni di autenticazione. Alcuni scenari possono includere:

* Accesso alle risorse dai dispositivi non gestiti o condiviso
* Accesso a informazioni sensibili da una rete esterna
* Utenti a impatto elevato
* Applicazioni aziendali critiche

Controlli di accesso condizionale consentono di creare i criteri destinati a casi d'uso specifici all'interno dell'organizzazione senza influire su tutti gli utenti.

Prima di approfondire i dettagli su come configurare i criteri, è possibile esaminare la configurazione predefinita.

## <a name="user-sign-in-frequency"></a>Frequenza di accesso utente

Frequenza di accesso definisce il periodo di tempo prima che un utente viene richiesto di accedere di nuovo quando si tenta di accedere a una risorsa.

La configurazione predefinita di Azure Active Directory (Azure AD) per la frequenza di accesso utente è una finestra in sequenza di 90 giorni. Viene richiesto di specificare le credenziali spesso sembra una ragionevole cosa da fare, ma potrebbe ritorcersi: gli utenti che vengono sottoposti a training per immettere le credenziali senza pensare possono involontariamente definirli per una richiesta di credenziali dannoso.

Si potrebbe pensare allarmante di non richiedere all'utente di accedere nuovamente per 90 giorni, in realtà, che qualsiasi violazione dei criteri IT verrà revocato la sessione. Alcuni esempi includono (ma non sono limitati a) una modifica della password, un dispositivo o disabilitare account. È possibile anche in modo esplicito [revocare le sessioni degli utenti usando PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). La configurazione predefinita di Azure AD un po' come "non chiedere agli utenti di fornire le proprie credenziali se non è stato modificato il comportamento di sicurezza delle sessioni di".

Frequenza di accesso impostazione funziona con le app che hanno implementato protocolli OATH2 o OIDC secondo gli standard. La maggior parte delle App native di Microsoft per Windows, Mac e dispositivi mobili è conforme all'impostazione.

## <a name="persistence-of-browsing-sessions"></a>Persistenza delle sessioni di esplorazione

Una sessione del browser permanente consente agli utenti di restare connessi dopo la chiusura e riaprire la finestra del browser.

L'impostazione predefinita Azure AD per la persistenza della sessione del browser consente agli utenti nei dispositivi personali per scegliere se mantenere la sessione mostrando un "rimanere connessi?" richiedere conferma al termine dell'autenticazione. Se la persistenza del browser è configurata in ADFS seguendo le indicazioni fornite nell'articolo [AD ADFS Single Sign-On Settings](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), verranno rispettare tale criterio e mantenere nonché sessione AD Azure. È anche possibile configurare se gli utenti nel tenant di visualizzare il "rimanere connessi?" modificando l'impostazione appropriata all'interno della società riquadro nel portale di Azure seguendo le indicazioni fornite nell'articolo di personalizzazione dei messaggi di richiesta [personalizzare la pagina di accesso AD Azure](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Configurazione dei controlli di sessione di autenticazione

Accesso condizionale è una funzionalità di Azure AD Premium e richiede una licenza premium. Per altre informazioni sull'argomento, vedere [Cos'è l'accesso condizionale in Azure Active Directory](overview.md#license-requirements-for-using-conditional-access).

> [!WARNING]
> Se si usa la [durata dei token configurabili](../develop/active-directory-configurable-token-lifetimes.md) funzionalità attualmente in anteprima pubblica, si noti che non è supportata la creazione di due criteri diversi per la stessa combinazione di utente o un'app: uno con questa funzionalità e un'altra con funzionalità di durata dei token configurabili. Microsoft prevede di ritirare la funzionalità di durata dei token configurabili in 15 ottobre e sostituirla con la funzionalità di gestione sessione di autenticazione di accesso condizionale.  

### <a name="policy-1-sign-in-frequency-control"></a>Criterio 1: Controllo della frequenza di accesso

1. Crea nuovo criterio
1. Scegliere le condizioni di tutte le necessarie per l'ambiente del cliente, tra cui le app cloud di destinazione.

   > [!NOTE]
   > È consigliabile impostare la frequenza dei messaggi di richiesta di autenticazione uguale per chiave app di Microsoft Office, ad esempio Exchange Online e SharePoint Online per un'esperienza utente ottimale.

1. Passare a **controlli di accesso** > **sessione** e fare clic su **frequenza di accesso**
1. Immettere il valore obbligatorio di giorni e ore nella prima casella di testo
1. Selezionare il valore **ore** oppure **giorni** dall'elenco a discesa
1. Si salva criterio

![Criteri di accesso condizionale configurati per l'accesso in frequenza](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

In Azure AD registrato Windows dispositivi Accedi per il dispositivo viene considerato un prompt dei comandi. Ad esempio, se è stato configurato l'accesso in frequenza a 24 ore per le app di Office, gli utenti in Azure AD registrato Windows dispositivi soddisferanno i criteri di frequenza di accesso eseguendo l'accesso al dispositivo e non richiederà nuovamente quando si apre l'app di Office.

Se è stata configurata la frequenza di accesso diversi per le app web diversi in cui sono in esecuzione nella stessa sessione del browser, il criterio più restrittivo verrà applicato a entrambe le app perché tutte le App in esecuzione nella stessa sessione del browser condividono un token di sessione single.

### <a name="policy-2-persistent-browser-session"></a>Criterio 2: Sessione del browser permanente

1. Crea nuovo criterio
1. Scegliere tutte le condizioni necessarie.

   > [!NOTE]
   > Si noti che questo controllo è necessario scegliere "Tutte le app Cloud" come condizione

1. Passare a **controlli di accesso** > **sessione** e fare clic su **sessione del browser permanente**
1. Selezionare un valore dall'elenco a discesa
1. Si salva criterio

![Criteri di accesso condizionale configurati per il browser permanente](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Configurazione di sessione del Browser persistenti nell'accesso condizionale di Azure AD verrà sovrascritta la "Leggi eseguito l'accesso?" impostando il marchio riquadro nel portale di Azure per lo stesso utente, se sono stati configurati entrambi i criteri della società.

## <a name="validation"></a>Convalida

Utilizzare lo strumento di simulazione per simulare un account di accesso da parte dell'utente per l'applicazione di destinazione e altre condizioni di base di come è stato configurato il criterio. I controlli di gestione della sessione di autenticazione visualizzano nel risultato dello strumento.

![Cosa accade se lo strumento comporta l'accesso condizionale](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Distribuzione dei criteri

La procedura consigliata è testare i criteri prima di distribuirli nell'ambiente di produzione per assicurarsi che funzionino nel modo previsto. L'approccio ideale è usare un tenant di test per verificare se il nuovo criterio funziona nel modo previsto. Per altre informazioni, vedere l'articolo [procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md).

## <a name="next-steps"></a>Passaggi successivi

* Se si desidera sapere come configurare un criterio di accesso condizionale, vedere l'articolo [richiedere il MFA per App specifiche l'accesso condizionale di Azure Active Directory](app-based-mfa.md).
* Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente, vedere l'articolo [procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md).
