---
title: Configurare la gestione della sessione di autenticazione - Azure Active DirectoryConfigure authentication session management - Azure Active Directory
description: Personalizzare la configurazione della sessione di autenticazione di Azure AD, inclusa la frequenza di accesso dell'utente e la persistenza della sessione del browser.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e9c0c88064c00c97de7dc58a500910e81c04eef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263283"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Configurare la gestione della sessione di autenticazione con accesso condizionaleConfigure authentication session management with Conditional Access

Nelle distribuzioni complesse, le organizzazioni potrebbero avere la necessità di limitare le sessioni di autenticazione. Alcuni scenari potrebbero includere:Some scenarios might include:

* Accesso alle risorse da un dispositivo non gestito o condiviso
* Accesso a informazioni riservate da una rete esterna
* Utenti ad alto impatto
* Applicazioni aziendali critiche

I controlli di accesso condizionale consentono di creare criteri destinati a casi d'uso specifici all'interno dell'organizzazione senza influire su tutti gli utenti.

Prima di approfondire i dettagli su come configurare il criterio, esaminiamo la configurazione predefinita.

## <a name="user-sign-in-frequency"></a>Frequenza di accesso dell'utente

La frequenza di accesso definisce il periodo di tempo prima che a un utente venga chiesto di accedere di nuovo quando si tenta di accedere a una risorsa.

La configurazione predefinita di Azure Active Directory (Azure AD) per la frequenza di accesso degli utenti è un'finestra in sequenza di 90 giorni. Chiedere agli utenti le credenziali spesso sembra una cosa sensata da fare, ma può eseguire il backfire: gli utenti che sono addestrati a immettere le proprie credenziali senza pensare possono involontariamente fornirli a una richiesta di credenziali dannoso.

Potrebbe sembrare allarmante non chiedere a un utente di accedere di nuovo, in realtà qualsiasi violazione dei criteri IT revocherà la sessione. Alcuni esempi includono (ma non sono limitati a) una modifica della password, un dispositivo non conforme o la disabilitazione dell'account. È anche possibile revocare in modo esplicito le sessioni degli utenti usando PowerShell .You can explicitly [revoke users' sessions using PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). La configurazione predefinita di Azure AD si riduce a "non chiedere agli utenti di fornire le proprie credenziali se il livello di sicurezza delle proprie sessioni non è cambiato".

L'impostazione della frequenza di accesso funziona con le app che hanno implementato protocolli OAUTH2 o OIDC in base agli standard. La maggior parte delle app native Microsoft per Windows, Mac e Mobile, incluse le seguenti applicazioni Web, è conforme all'impostazione.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Portale di amministrazione di Office 365
- Exchange Online
- SharePoint e OneDrive
- Client Web di Teams
- Dynamics CRM Online
- Portale di Azure

### <a name="user-sign-in-frequency-and-device-identities"></a>Frequenza di accesso utente e identità del dispositivo

Se si dispone di dispositivi di Azure AD aggiunti, ibridi aggiunti ad Azure AD o registrati in Azure AD, quando un utente sblocca il dispositivo o accede in modo interattivo, questo evento soddisferà anche i criteri di frequenza di accesso. Nei due esempi seguenti la frequenza di accesso utente è impostata su 1 ora:In the following 2 examples user sign-in frequency is set to 1 hour:

Esempio 1:

- Alle 00:00, un utente accede al proprio dispositivo aggiunto ad Azure AD di Windows 10 e inizia a lavorare su un documento archiviato in SharePoint Online.
- L'utente continua a lavorare sullo stesso documento sul proprio dispositivo per un'ora.
- Alle 01:00, all'utente viene richiesto di accedere nuovamente in base al requisito di frequenza di accesso nei criteri di accesso condizionale configurato dall'amministratore.

Esempio 2:

- Alle 00:00, un utente accede al proprio dispositivo aggiunto ad Azure AD di Windows 10 e inizia a lavorare su un documento archiviato in SharePoint Online.
- Alle 00:30, l'utente si alza e si prende una pausa bloccando il dispositivo.
- Alle 00:45, l'utente ritorna dalla pausa e sblocca il dispositivo.
- Alle 01:45, all'utente viene richiesto di accedere nuovamente in base al requisito di frequenza di accesso nel criterio di accesso condizionale configurato dall'amministratore dall'ultimo accesso alle 00:45.

## <a name="persistence-of-browsing-sessions"></a>Persistenza delle sessioni di esplorazione

Una sessione del browser permanente consente agli utenti di rimanere connessi dopo la chiusura e la riapertura della finestra del browser.

L'impostazione predefinita di Azure AD per la persistenza delle sessioni del browser consente agli utenti dei dispositivi personali di scegliere se mantenere la sessione visualizzando un elemento "Rimanere connessi?" dopo la corretta autenticazione. Se la persistenza del browser è configurata in ADFS usando le indicazioni fornite nell'articolo [Impostazioni Single Sign-on di ADFS,](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)sarà necessario rispettare tali criteri e rendere persistente anche la sessione di Azure AD. È inoltre possibile configurare se gli utenti nel tenant visualizzano la sezione "Rimanere connessi?" richiesta modificando l'impostazione appropriata nel riquadro di personalizzazione dell'azienda nel portale di Azure usando le indicazioni contenute nell'articolo Personalizzare la pagina di accesso di [Azure AD.](../fundamentals/customize-branding.md)

## <a name="configuring-authentication-session-controls"></a>Configurazione dei controlli della sessione di autenticazione

L'accesso condizionale è una funzionalità di Azure AD Premium e richiede una licenza Premium.Conditional Access is an Azure AD Premium capability and requires a premium license. Per altre informazioni sull'accesso condizionale, vedere [Che cos'è l'accesso condizionale in Azure Active Directory?](overview.md#license-requirements)

> [!WARNING]
> Se si utilizza la funzionalità di [durata del token configurabile](../develop/active-directory-configurable-token-lifetimes.md) attualmente in anteprima pubblica, non è supportata la creazione di due criteri diversi per la stessa combinazione di utente o app: uno con questa funzionalità e un altro con funzionalità di durata del token configurabile. Microsoft prevede di ritirare la funzionalità di durata del token configurabile il 1 maggio 2020 e sostituirla con la funzionalità di gestione della sessione di autenticazione con accesso condizionale.  

### <a name="policy-1-sign-in-frequency-control"></a>Criterio 1: controllo della frequenza di accesso

1. Creare un nuovo criterio
1. Scegli tutte le condizioni necessarie per l'ambiente del cliente, incluse le app cloud di destinazione.

   > [!NOTE]
   > Si consiglia di impostare la frequenza della richiesta di autenticazione uguale per le principali applicazioni di Microsoft Office, ad esempio Exchange Online e SharePoint Online per una migliore esperienza utente.

1. Vai alla**sessione** **Controlli** > di accesso e fai clic su **Frequenza di accesso**
1. Immettere il valore richiesto di giorni e ore nella prima casella di testo
1. Selezionare il valore **Di Ore** o **Giorni** dall'elenco a discesa
1. Salvare i criteri

![Criteri di accesso condizionale configurati per la frequenza di accessoConditional Access policy configured for sign in frequency](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

In Azure AD i dispositivi Windows registrati di Azure AD accedono al dispositivo viene considerato un prompt. Ad esempio, se la frequenza di accesso è stata configurata a 24 ore per le app di Office, gli utenti nei dispositivi Windows registrati di Azure AD soddisferanno i criteri Frequenza di accesso accedendo al dispositivo e non verrà richiesto nuovamente all'apertura delle app di Office.

Se è stata configurata una frequenza di accesso diversa per app Web diverse in esecuzione nella stessa sessione del browser, i criteri più rigorosi verranno applicati a entrambe le app perché tutte le app in esecuzione nella stessa sessione del browser condividono un singolo token di sessione.

### <a name="policy-2-persistent-browser-session"></a>Criterio 2: Sessione del browser permanente

1. Creare un nuovo criterio
1. Scegliere tutte le condizioni richieste.

   > [!NOTE]
   > Si prega di notare che questo controllo richiede di scegliere "Tutte le applicazioni cloud" come condizione. La persistenza della sessione del browser è controllata dal token di sessione di autenticazione. Tutte le schede in una sessione del browser condividono un singolo token di sessione e pertanto devono condividere lo stato di persistenza.

1. Vai a**Sessione** **controlli** > di accesso e fai clic su Sessione browser **persistente**
1. Selezionare un valore dall'elenco a discesa
1. Salva criteri

![Criteri di accesso condizionale configurati per il browser permanenteConditional Access policy configured for persistent browser](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> La configurazione della sessione del browser permanente in Access condizionale di Azure AD sovrascriverà l'opzione "Resta connesso?" se sono stati configurati entrambi i criteri nel riquadro di personalizzazione della società nel portale di Azure per lo stesso utente.

## <a name="validation"></a>Convalida

Utilizzare lo strumento What-If per simulare un account di accesso dall'utente all'applicazione di destinazione e altre condizioni in base alla configurazione dei criteri. I controlli di gestione della sessione di autenticazione vengono visualizzati nel risultato dello strumento.

![Risultati dello strumento Elementi di ricerca condizionali](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Distribuzione dei criteri

La procedura consigliata è testare i criteri prima di distribuirli nell'ambiente di produzione per assicurarsi che funzionino nel modo previsto. L'approccio ideale è usare un tenant di test per verificare se il nuovo criterio funziona nel modo previsto. Per altre informazioni, vedere l'articolo [Procedure consigliate per l'accesso condizionale in Azure Active Directory.For](best-practices.md)more information, see the article Best practices for Conditional Access in Azure Active Directory .

## <a name="next-steps"></a>Passaggi successivi

* Se si vuole sapere come configurare criteri di accesso condizionale, vedere l'articolo Richiedere l'autenticazione a più [fattori per app specifiche con l'accesso condizionale](app-based-mfa.md)di Azure Active Directory .
* Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente, vedere l'articolo [Procedure consigliate per l'accesso condizionale in Azure Active Directory.](best-practices.md)
