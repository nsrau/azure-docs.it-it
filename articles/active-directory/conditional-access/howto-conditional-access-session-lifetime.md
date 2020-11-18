---
title: Configurare la gestione della sessione di autenticazione-Azure Active Directory
description: Personalizzare Azure AD configurazione della sessione di autenticazione, ad esempio la frequenza di accesso utente e la persistenza della sessione del browser.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/23/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6deb0c005b5ed7daf5b30bea8a65ee70d8b460c5
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837465"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>È possibile configurare la gestione della sessione di autenticazione con l'Accesso condizionale

Nelle distribuzioni complesse, le organizzazioni potrebbero avere la necessità di limitare le sessioni di autenticazione. Alcuni scenari possono includere:

* Accesso alle risorse da un dispositivo non gestito o condiviso
* Accesso a informazioni riservate da una rete esterna
* Utenti con effetto elevato
* Applicazioni aziendali critiche

I controlli di accesso condizionale consentono di creare criteri destinati a casi d'uso specifici all'interno dell'organizzazione senza influire su tutti gli utenti.

Prima di approfondire i dettagli su come configurare il criterio, esaminiamo la configurazione predefinita.

## <a name="user-sign-in-frequency"></a>Frequenza di accesso utente

Frequenza di accesso consente di definire il periodo di tempo prima che un utente debba accedere di nuovo quando tenta di accedere a una risorsa.

La configurazione predefinita di Azure Active Directory (Azure AD) per la frequenza di accesso dell'utente è una finestra in sequenza di 90 giorni. La richiesta di credenziali da parte degli utenti è spesso un'operazione sensata, ma può essere riattiva: gli utenti che hanno eseguito il training per immettere le proprie credenziali senza pensare possono fornirli involontariamente a una richiesta di credenziali dannose.

Potrebbe sembrare allarmante non chiedere a un utente di eseguire l'accesso, in realtà qualsiasi violazione dei criteri IT revoca la sessione. Alcuni esempi includono (senza limitazioni) una modifica della password, un dispositivo incompliant o la disabilitazione dell'account. È anche possibile revocare in modo esplicito le [sessioni degli utenti usando PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0&preserve-view=true). Il Azure AD configurazione predefinita è "non richiedere agli utenti di fornire le proprie credenziali se il comportamento di sicurezza delle sessioni non è cambiato".

L'impostazione della frequenza di accesso funziona con le app che hanno implementato i protocolli OAUTH2 o OIDC in base agli standard. La maggior parte delle app Microsoft native per Windows, Mac e dispositivi mobili, incluse le applicazioni Web seguenti sono conformi all'impostazione.

- Word, Excel, PowerPoint online
- OneNote online
- Office.com
- Portale di amministrazione di Microsoft 365
- Exchange Online
- SharePoint e OneDrive
- Client Web Teams
- Dynamics CRM Online
- Portale di Azure

L'impostazione della frequenza di accesso funziona anche con le applicazioni SAML, purché non eliminino i propri cookie e vengano reindirizzati di nuovo a Azure AD per l'autenticazione a intervalli regolari.

### <a name="user-sign-in-frequency-and-multi-factor-authentication"></a>Frequenza di accesso utente e autenticazione a più fattori

Frequenza di accesso applicata in precedenza solo al primo fattore di autenticazione nei dispositivi che sono stati Azure AD Uniti, Azure AD ibrido Uniti e Azure AD registrati. Non esisteva un modo semplice per i clienti di reapplicare l'autenticazione a più fattori (multi-factor authentication) a tali dispositivi. In base ai suggerimenti dei clienti, viene applicata la frequenza di accesso anche per l'autenticazione a più fattori.

[![Frequenza di accesso e autenticazione a più fattori](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart-small.png)](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart.png#lightbox)

### <a name="user-sign-in-frequency-and-device-identities"></a>Frequenza di accesso utente e identità del dispositivo

Se si dispone di Azure AD aggiunti, ibridi Azure AD aggiunti o Azure AD dispositivi registrati, quando un utente sblocca il dispositivo o accede in modo interattivo, questo evento soddisferà anche i criteri di frequenza di accesso. Nei due esempi seguenti, la frequenza di accesso utente è impostata su 1 ora:

Esempio 1:

- Alla 00:00, un utente accede al dispositivo Windows 10 Azure AD aggiunto e inizia a lavorare in un documento archiviato in SharePoint Online.
- L'utente continua a lavorare sullo stesso documento sul dispositivo per un'ora.
- Alla 01:00, all'utente viene richiesto di eseguire di nuovo l'accesso in base ai requisiti di frequenza di accesso nel criterio di accesso condizionale configurato dall'amministratore.

Esempio 2:

- Alla 00:00, un utente accede al dispositivo Windows 10 Azure AD aggiunto e inizia a lavorare in un documento archiviato in SharePoint Online.
- Alle 00:30, l'utente diventa attivo e interrompe il blocco del dispositivo.
- Alle 00:45, l'utente ritorna dall'interruzioni e sblocca il dispositivo.
- Alla 01:45, all'utente viene richiesto di eseguire di nuovo l'accesso in base ai requisiti di frequenza di accesso nei criteri di accesso condizionale configurati dall'amministratore dall'ultimo accesso a 00:45.

## <a name="persistence-of-browsing-sessions"></a>Persistenza delle sessioni di esplorazione

Una sessione del browser persistente consente agli utenti di rimanere connessi dopo la chiusura e la riapertura della finestra del browser.

Il Azure AD predefinito per la persistenza della sessione del browser consente agli utenti dei dispositivi personali di scegliere se mantenere la sessione in modo permanente visualizzando un "rimanere connesso?" messaggio di richiesta dopo l'autenticazione riuscita. Se la persistenza del browser è configurata in AD FS usando le linee guida disponibili nell'articolo [ad FS le singole impostazioni di Sign-On](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), si conformeranno a tale criterio e si renderà persistente anche la sessione di Azure ad. È anche possibile specificare se gli utenti nel tenant visualizzano il "rimanere connessi?" richiedere modificando l'impostazione appropriata nel riquadro branding dell'azienda in portale di Azure seguendo le istruzioni riportate nell'articolo [personalizzare la pagina di accesso Azure ad](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Configurazione di controlli della sessione di autenticazione

L'accesso condizionale è una funzionalità di Azure AD Premium e richiede una licenza Premium. Per altre informazioni sull'accesso condizionale, vedere [che cos'è l'accesso condizionale in Azure Active Directory?](overview.md#license-requirements)

> [!WARNING]
> Se si usa la funzionalità di [durata dei token configurabile](../develop/active-directory-configurable-token-lifetimes.md) attualmente disponibile in anteprima pubblica, si noti che non è supportata la creazione di due criteri diversi per la stessa combinazione di utenti o app: uno con questa funzionalità e un altro con funzionalità di durata dei token configurabili. Microsoft prevede di ritirare la funzionalità di durata dei token configurabili per la durata dei token di aggiornamento e di sessione il 30 gennaio 2021 e sostituirla con la funzionalità di gestione della sessione di autenticazione dell'accesso condizionale.  
>
> Prima di abilitare la frequenza di accesso, assicurarsi che altre impostazioni di riautenticazione siano disabilitate nel tenant. Se l'autenticazione a più fattori nei dispositivi attendibili è abilitata, assicurarsi di disabilitarla prima di usare la frequenza di accesso, in quanto l'uso di queste due impostazioni può comportare la richiesta imprevista degli utenti. Per ulteriori informazioni sulle richieste di riautenticazione e sulla durata della sessione, vedere l'articolo [ottimizzare la richiesta di riautenticazione e comprendere la durata della sessione per Azure AD multi-factor authentication](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

### <a name="policy-1-sign-in-frequency-control"></a>Criteri 1: controllo della frequenza di accesso

1. Creare nuovi criteri
1. Scegliere tutte le condizioni necessarie per l'ambiente del cliente, incluse le app cloud di destinazione.

   > [!NOTE]
   > È consigliabile impostare la frequenza di richiesta di autenticazione uguale per le app chiave Microsoft Office come Exchange Online e SharePoint Online per ottimizzare l'esperienza utente.

1. Passare alla **sessione controlli di accesso**  >  **Session** e fare clic su **frequenza di** accesso
1. Immettere il valore richiesto di giorni e ore nella prima casella di testo
1. Selezionare un valore di **ore** o **giorni** dall'elenco a discesa
1. Salvare i criteri

![Criteri di accesso condizionale configurati per la frequenza di accesso](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

In Azure AD i dispositivi Windows registrati, l'accesso al dispositivo viene considerato una richiesta. Se ad esempio è stata configurata la frequenza di accesso a 24 ore per le app di Office, gli utenti in Azure AD dispositivi Windows registrati soddisferanno i criteri di frequenza di accesso effettuando l'accesso al dispositivo e non verrà più visualizzata alcuna richiesta all'apertura delle app di Office.

### <a name="policy-2-persistent-browser-session"></a>Criterio 2: sessione del browser persistente

1. Creare nuovi criteri
1. Scegliere tutte le condizioni necessarie.

   > [!NOTE]
   > Si noti che questo controllo richiede di scegliere "tutte le app Cloud" come condizione. La persistenza della sessione del browser è controllata dal token della sessione di autenticazione. Tutte le schede in una sessione del browser condividono un solo token di sessione e pertanto devono condividere lo stato di persistenza.

1. Passare alla **sessione controlli di accesso**  >  **Session** e fare clic su **sessione del browser permanente**
1. Selezionare un valore dall'elenco a discesa
1. Salva i criteri

![Criteri di accesso condizionale configurati per il browser permanente](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> La configurazione della sessione del browser persistente in Azure AD accesso condizionale sovrascriverà il "rimanere connesso?" impostazione nel riquadro Personalizzazione società nel portale di Azure per lo stesso utente se sono stati configurati entrambi i criteri.

## <a name="validation"></a>Convalida

Utilizzare lo strumento What-If per simulare l'accesso dell'utente all'applicazione di destinazione e altre condizioni in base alla configurazione dei criteri. I controlli di gestione della sessione di autenticazione vengono visualizzati nel risultato dello strumento.

![Risultati dell'accesso condizionale What If strumento](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Distribuzione dei criteri

La procedura consigliata è testare i criteri prima di distribuirli nell'ambiente di produzione per assicurarsi che funzionino nel modo previsto. L'approccio ideale è usare un tenant di test per verificare se il nuovo criterio funziona nel modo previsto. Per altre informazioni, vedere l'articolo [pianificare una distribuzione dell'accesso condizionale](plan-conditional-access.md).

## <a name="next-steps"></a>Passaggi successivi

* Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere l'articolo [pianificare una distribuzione dell'accesso condizionale](plan-conditional-access.md).