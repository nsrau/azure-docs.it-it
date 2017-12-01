---
title: Introduzione ad Azure Active Directory | Microsoft Docs
description: "Ottenere licenze, aggiungere il nome di dominio, creare una pagina di accesso personalizzata e aggiungere la funzionalità di reimpostazione password self-service in Azure Active Directory"
keywords: 
author: curtand
manager: michael.tillman
ms.author: curtand
ms.reviewer: jsnow
ms.date: 11/14/2017
ms.topic: article
ms.prod: 
ms.service: active-directory
ms.workload: identity
ms.technology: 
ms.assetid: 
services: active-directory
ms.custom: it-pro
ms.openlocfilehash: dd745869494a1ed740a0a05a5508363334aa7360
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2017
---
# <a name="get-started-with-azure-ad"></a>Introduzione ad Azure AD
La gestione delle identità moderna richiede un'affidabilità scalabile e coerente per garantire la disponibilità di applicazioni e servizi solo agli utenti autenticati. Per supportare adeguatamete le esigenze di gestione delle identità degli utenti, il reparto IT ha bisogno di un modo per fornire l'accesso alle app pubbliche software as a service (SaaS), un modo per ospitare una linea interna di app aziendali e anche modi per migliorare lo sviluppo e l'utilizzo delle app localmente. Tutti questi requisiti evidenziano la necessità di una soluzione di gestione delle identità basata su cloud.      

Azure Active Directory (Azure AD) è il servizio Microsoft di gestione di identità e directory basato sul cloud e multi-tenant. Azure AD comprende servizi directory core, amministrazione avanzata delle identità e gestione dell'accesso alle applicazioni. La progettazione multi-tenant, geograficamente distribuita e a disponibilità elevata di Azure AD offre l'affidabilità adeguata per le esigenze aziendali più importanti.

Azure AD include un gruppo completo di funzionalità di gestione delle identità, inclusa la possibilità di sincronizzare le informazioni delle risorse locali, il branding aziendale personalizzabile, una gestione delle licenze semplice e la gestione delle password self-service.  Queste funzionalità facili da configurare possono essere d'aiuto per iniziare a usare Azure AD per proteggere le applicazioni basate su cloud, semplificare i processi IT, ridurre i costi e contribuire a garantire il raggiungimento degli obiettivi aziendali relativi alla conformità.

![Azure AD ](./media/get-started-azure-ad/Azure_Active_Directory.png)

La parte rimanente di questo articolo descrive come iniziare a usare Azure AD. 

## <a name="sign-up-for-azure-active-directory-premium"></a>Effettuare l'iscrizione ad Azure Active Directory Premium
Per [iniziare a usare Azure Active Directory (Azure AD) Premium](active-directory-get-started-premium.md), è possibile acquistare le licenze e associarle alla sottoscrizione di Azure. Se si crea una nuova sottoscrizione di Azure, è anche necessario attivare il piano di licenze e l'accesso al servizio Azure AD. 

Sono disponibili diverse opzioni per l'iscrizione ad Active Directory Premium: 

- Usare la sottoscrizione di Azure o l'abbonamento a Office 365
- Usare un piano di licenza di Enterprise Mobility + Security
- Usare un piano per contratti multilicenza Microsoft

### <a name="verification-step"></a>Passaggio di verifica
Dopo aver attivato la sottoscrizione, assicurarsi di poter accedere al servizio.

## <a name="add-a-custom-domain-name"></a>Aggiungere un nome di dominio personalizzato
Ogni directory di Azure AD include un nome di dominio iniziale in formato *nomedominio*.onmicrosoft.com. Il nome di dominio iniziale non può essere modificato o eliminato, ma è anche possibile [aggiungere ad Azure AD il nome di dominio aziendale](add-custom-domain.md). Ad esempio, è probabile che l'organizzazione abbia altri nomi di dominio usati per le attività aziendali e utenti che eseguono l'accesso con il nome di dominio aziendale. L'aggiunta di nomi di dominio personalizzati ad Azure AD consente di assegnare nella directory nomi utente familiari agli utenti, ad esempio "alice@contoso.com" invece di "alice@.onmicrosoft.com": Il processo è semplice:

1. Aggiungere il nome di dominio personalizzato alla directory
2. Aggiungere una voce DNS per il nome di dominio nel registrar
3. Verificare il nome di dominio personalizzato in Azure AD

### <a name="verification-step"></a>Passaggio di verifica
Dopo aver aggiunto un dominio personalizzato, verificare che abbia lo stato **Verificato** visualizzato nel pannello **Nomi di dominio personalizzati** del portale di Azure AD.

## <a name="add-company-branding-to-your-sign-in-page"></a>Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso 
Per evitare confusione, molte aziende vogliono applicare un aspetto coerente a tutti i siti Web e servizi che gestiscono. Azure Active Directory (Azure AD) offre questa funzionalità consentendo di [personalizzare l'aspetto della pagina di accesso, in modo da includere il logo e le combinazioni di colori personalizzate dell'azienda](customize-branding.md). La pagina di accesso è la pagina visualizzata quando si accede a Office 365 o ad altre applicazioni basate sul Web che usano Azure AD come provider di identità. Interagire con questa pagina per immettere le credenziali.

### <a name="verification-step"></a>Passaggio di verifica
Accedere al portale di Azure e assicurarsi che la pagina di accesso personalizzata e le eventuali personalizzazioni aggiuntive per la lingua siano state configurate correttamente. 

## <a name="add-new-users"></a>Aggiungere nuovi utenti
È possibile [aggiungere nuovi utenti ad Azure AD dell'organizzazione AD](add-users-azure-active-directory.md) uno alla volta, usando il portale di Azure o sincronizzando i dati delle risorse locali di Windows Server AD. È possibile aggiungere gli utenti basati su cloud direttamente dal portale di Azure AD o sincronizzare le informazioni utente locali.

Per abilitare la sincronizzazione delle identità locali con Azure AD è necessario installare e configurare [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) in un server dell'organizzazione. Questa applicazione gestisce la sincronizzazione di utenti e gruppi dall'origine delle identità esistente al tenant di Azure AD.

### <a name="verification-step"></a>Passaggio di verifica
Dopo la creazione o la sincronizzazione dei nuovi utenti, assicurarsi che siano visibili in Azure AD.

## <a name="assign-licenses"></a>Assegnare licenze
Anche se per configurare le funzionalità a pagamento è sufficiente ottenere una sottoscrizione, è comunque necessario [assegnare agli utenti le licenze](license-users-groups.md) per le funzionalità a pagamento di Azure AD Premium. Agli utenti che devono accedere a una funzionalità a pagamento di Azure AD, o che vengono gestiti tramite questa funzionalità, deve essere assegnata una licenza. Un'assegnazione di licenza consiste nell'associazione di un utente a un servizio acquistato, ad esempio Azure AD Premium o Basic oppure Enterprise Mobility + Security.

Per impostare le regole, si può usare l'assegnazione delle licenze in base al gruppo, come negli esempi seguenti:

- Tutti gli utenti nella directory ottengono automaticamente una licenza
- Tutti gli utenti con la posizione appropriata ottengono una licenza
- La decisione può essere delegata ad altri responsabili nell'organizzazione (tramite gruppi self-service)

### <a name="verification-step"></a>Passaggio di verifica
Vedere le licenze assegnate e disponibili in **Azure Active Directory** > **Licenze** > **Tutti i prodotti**.

## <a name="configure-self-service-password-reset"></a>Configurare la reimpostazione self-service delle password
La [reimpostazione self-service della password (SSPR)](active-directory-passwords-getting-started.md) offre agli amministratori IT una modalità semplice per consentire agli utenti di reimpostare o sbloccare le password o gli account. Il sistema include report dettagliati per tenere traccia del momento in cui gli utenti usano il sistema, oltre a notifiche per segnalare usi impropri.

### <a name="verification-step"></a>Passaggio di verifica
Vedere le proprietà SSPR abilitate in **Azure Active Directory** > **Reimpostazione password**, per assicurarsi che siano state eseguite le assegnazioni corrette di utenti e gruppi. 


## <a name="next-steps"></a>Passaggi successivi
[Pagina di prodotto di Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

[Pagina dei prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)