---
title: Autenticazione pass-through di Azure AD - Avvio rapido | Microsoft Docs
description: "Questo articolo descrive le attività iniziali per l'autenticazione pass-through di Azure Active Directory (Azure AD)."
services: active-directory
keywords: Autenticazione pass-through di Azure AD Connect, installare Active Directory, componenti necessari per Azure AD, SSO, Single Sign-On
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: c1bc7cc5fe53d04019f68a520fb03c9187a6148b
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017

---

# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Autenticazione pass-through di Azure Active Directory - Avvio rapido

L'autenticazione pass-through di Azure Active Directory (Azure AD) consente agli utenti di accedere sia ad applicazioni in locale che a quelle basate sul cloud usando le stesse password. L'accesso degli utenti avviene tramite la convalida delle password direttamente in Active Directory locale.

## <a name="how-to-deploy-azure-ad-pass-through-authentication"></a>Come distribuire l'autenticazione pass-through di Azure AD

Per distribuire l'autenticazione pass-through, è necessario eseguire questi passaggi:
1. *Verificare i prerequisiti*: configurare l'ambiente locale e il tenant correttamente prima di abilitare la funzionalità.
2. *Abilitare la funzionalità*: attivare l'autenticazione pass-through nel tenant e installare un agente lightweight locale per gestire le richieste di convalida delle password.
3. *Testare la funzionalità*: testare l'accesso degli utenti mediante l'autenticazione pass-through.
4. *Verificare la disponibilità elevata*: installare un secondo agente autonomo per garantire un'elevata disponibilità per le richieste di accesso.

## <a name="step-1-check-prerequisites"></a>Passaggio 1: Verificare i prerequisiti

Accertarsi di aver soddisfatto i prerequisiti seguenti:

### <a name="on-the-azure-portal"></a>Nel Portale di Azure

1. Creare un account Administrator globale solo cloud nel tenant di Azure AD. In questo modo è possibile gestire la configurazione del tenant in caso di errore o mancata disponibilità dei servizi locali. Informazioni su come [aggiungere un account amministratore globale di tipo solo cloud](../active-directory-users-create-azure-portal.md). L'esecuzione di questo passaggio è fondamentale ed evita di rimanere bloccati fuori dal tenant.
2. Aggiungere uno o più [nomi di dominio personalizzati](../active-directory-add-domain.md) al tenant di Azure AD. Gli utenti accedono usando uno di questi nomi di dominio.

### <a name="in-your-on-premises-environment"></a>Nell'ambiente locale

1. Identificare un server che esegue Windows Server 2012 R2 o versione successiva in cui eseguire Azure AD Connect. Aggiungere il server alla stessa foresta AD degli utenti le cui password devono essere convalidate.
2. Installare la [versione più recente di Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) sul server identificato nel passaggio 2. Se Azure AD Connect è già in esecuzione, assicurarsi che la versione sia 1.1.486.0 o successiva.
3. Identificare un server aggiuntivo che esegue Windows Server 2012 R2 o versione successiva in cui eseguire un agente di autenticazione autonomo. La versione dell'agente di autenticazione deve essere 1.5.58.0 o successiva. Questo server è necessario per garantire la disponibilità elevata delle richieste di accesso. Aggiungere il server alla stessa foresta AD degli utenti le cui password devono essere convalidate.
4. Se è presente un firewall tra i server e Azure AD, è necessario configurare gli elementi seguenti:
   - Aprire le porte: verificare che gli agenti di autenticazione dei server possano effettuare richieste in uscita ad Azure AD sulle porte 80 e 443. Se il firewall impone le regole in base agli utenti di origine, aprire queste porte per il traffico proveniente da servizi di Windows in esecuzione come servizio di rete.
   - Consentire gli endpoint di Azure AD: se è abilitato il filtro degli URL, verificare che gli agenti di autenticazione possano comunicare con **\*.msappproxy.net** e **\*.servicebus.windows.net**.
   - Verificare le connessioni IP dirette: assicurare che gli agenti di autenticazione dei server possano effettuare connessioni IP dirette agli [intervalli IP dei data center di Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

## <a name="step-2-enable-the-feature"></a>Passaggio 2: Abilitare la funzionalità

L'autenticazione pass-through di Azure AD può essere abilitata tramite [Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>L'autenticazione pass-through può essere abilitata nel server primario o di gestione temporanea di Azure AD Connect. Si consiglia di abilitarla dal server primario.

Se si installa Azure AD Connect per la prima volta, scegliere il [percorso di installazione personalizzato](active-directory-aadconnect-get-started-custom.md). Nella pagina di **Accesso utente** scegliere **Autenticazione pass-through** come metodo di accesso. Al completamento, nello stesso server di Azure AD Connect viene installato un agente di autenticazione. Viene inoltre abilitata la funzionalità di autenticazione pass-through nel tenant.

![Azure AD Connect - Accesso utente](./media/active-directory-aadconnect-sso/sso3.png)

Se Azure AD Connect è già installata, (usando il percorso di [installazione rapida](active-directory-aadconnect-get-started-express.md) o di [installazione personalizzata](active-directory-aadconnect-get-started-custom.md)), scegliere la pagina **Cambia l'accesso utente** in Azure AD Connect e fare clic su **Avanti**. Selezionare quindi **Autenticazione pass-through** come il metodo di accesso. Al completamento, nello stesso server di Azure AD Connect viene installato un agente di autenticazione e la funzionalità è abilitata per il tenant.

![Azure AD Connect - Cambiare l'accesso utente](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>L'autenticazione pass-through è una funzionalità a livello di tenant. La sua attivazione influisce sugli accessi degli utenti per _tutti_ i domini gestiti nel tenant.

## <a name="step-3-test-the-feature"></a>Passaggio 3: Testare la funzionalità

Dopo il passaggio 2, gli utenti di tutti i domini gestiti nel tenant accederanno usando l'autenticazione pass-through. Gli utenti dei domini federati continueranno invece a eseguire l'accesso con Active Directory Federation Services (ADFS) o qualsiasi altro provider di federazione configurato in precedenza. Se si converte un dominio da federato a gestito, tutti gli utenti del dominio inizieranno automaticamente a eseguire l'accesso usando l'autenticazione pass-through. La funzionalità di autenticazione pass-through non ha alcun impatto sugli utenti solo cloud.

## <a name="step-4-ensure-high-availability"></a>Passaggio 4: Garantire la disponibilità elevata

Se si prevede di distribuire l'autenticazione pass-through in un ambiente di produzione, è necessario installare un agente di autenticazione autonomo. Installare questo secondo agente di autenticazione in un server _diverso_ da quello dove è in esecuzione Azure AD Connect e il primo agente di autenticazione. Questa configurazione fornisce la disponibilità elevata delle richieste di accesso. Per distribuire un agente di autenticazione, seguire queste istruzioni:

### <a name="download-and-install-the-authentication-agent-software-on-your-server"></a>Scaricare e installare il software dell'agente di autenticazione nel server

1.  [Scaricare](https://go.microsoft.com/fwlink/?linkid=837580) il software più recente dell'agente di autenticazione. Verificare che la versione sia 1.5.58.0 o successiva.
2.  Aprire il prompt dei comandi come amministratore.
3.  Eseguire il comando riportato di seguito (il parametro **/q** significa "installazione non interattiva", ovvero durante l'installazione non viene richiesto di accettare il contratto di licenza con l'utente finale):`
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
`

>[!NOTE]
>È possibile installare solo un singolo agente di autenticazione per ciascun server.

### <a name="register-the-authentication-agent-with-azure-ad"></a>Registrare l'agente di autenticazione con Azure AD

1.  Aprire una finestra di PowerShell come amministratore.
2.  Passare a **C:\Programmi\Microsoft AAD App Proxy Connector** ed eseguire lo script, come segue: `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.  Quando richiesto, immettere le credenziali dell'account amministratore globale per il tenant di Azure AD.

## <a name="next-steps"></a>Passaggi successivi
- [**Current limitations**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) (Limitazioni correnti): questa funzionalità è attualmente in anteprima. Informazioni su quali scenari sono supportati e quali non lo sono.
- [**Approfondimento tecnico**](active-directory-aadconnect-pass-through-authentication-how-it-works.md): informazioni sul funzionamento di questa funzionalità.
- [**Domande frequenti**](active-directory-aadconnect-pass-through-authentication-faq.md): risposte alle domande più frequenti.
- [**Risoluzione dei problemi**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
- [**Seamless Single Sign-On di Azure AD**](active-directory-aadconnect-sso.md): altre informazioni su questa funzionalità complementare.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.

