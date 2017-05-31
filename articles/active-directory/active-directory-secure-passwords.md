---
title: "Sicurezza delle password a più livelli di Azure AD | Microsoft Docs"
description: Questo articolo spiega in che modo Azure AD applica le password complesse e protegge le password degli utenti dai criminali informatici.
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: joflore
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 32464307ccb082b25538eaa522c1cdedef1ca555
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---
# <a name="a-multi-tiered-approach-to-azure-ad-password-security"></a>Approccio multilivello alla sicurezza delle password di Azure AD

Questo articolo illustra alcune procedure consigliate che è possibile seguire come utente o amministratore per proteggere il proprio account Azure Active Directory (Azure AD) o Microsoft.

 > [!NOTE]
 > Gli amministratori di Azure Active Directory possono reimpostare le password degli utenti seguendo le indicazioni fornite nell'articolo [Reimpostare la password per un utente in Azure Active Directory](active-directory-users-reset-password-azure-portal.md).
 >
 > Gli utenti possono reimpostare la propria password seguendo le indicazioni fornite nell'articolo [Password di Azure AD dimenticata](active-directory-passwords-update-your-own-password.md).
 >

## <a name="password-requirements"></a>Requisiti delle password

Azure AD adotta i seguenti approcci comuni per la protezione delle password:

* Requisiti di lunghezza delle password
* Requisiti di complessità delle password
* Scadenza regolare e periodica delle password

Per informazioni sulla reimpostazione delle password in Azure Active Directory, vedere l'argomento [Reimpostazione self-service delle password di Azure AD per i professionisti IT](active-directory-passwords.md).

## <a name="azure-ad-password-protections"></a>Protezione delle password di Azure AD

Azure AD e il sistema di account Microsoft usano approcci di settore comprovati per assicurare la protezione delle password di utenti e amministratori, tra cui:

* Password vietate in modo dinamico
* Smart Password Lockout

Per informazioni sulla gestione delle password in base alla ricerca corrente, vedere il white paper contenente le [indicazioni sulle password](http://aka.ms/passwordguidance).

### <a name="dynamically-banned-passwords"></a>Password vietate in modo dinamico

Azure AD e il sistema di account Microsoft salvaguardano la protezione delle password vietando in modo dinamico le password usate comunemente. Il team di Azure AD Identity Protection analizza periodicamente gli elenchi delle password vietate, impedendo agli utenti di selezionare le password usate comunemente. Questo servizio è disponibile in Azure AD e per i clienti del servizio account Microsoft.

Quando si creano password, è importante che gli amministratori invitino gli utenti a scegliere passphrase che includano una combinazione univoca di lettere, numeri, caratteri o parole. Questo approccio contribuisce a rendere quasi impossibile il rischio di compromissione delle password degli utenti e allo stesso tempo le rende più facili da ricordare.

#### <a name="password-breaches"></a>Violazioni delle password

Microsoft cerca sempre di anticipare le mosse dei criminali informatici.

Il team di Azure AD Identity Protection analizza costantemente le password più comuni. Anche i criminali informatici usano strategie simili per gli attacchi, ad esempio creando un [tabella arcobaleno](https://en.wikipedia.org/wiki/Rainbow_table) per violare gli hash delle password.

Microsoft analizza continuamente le [violazioni di dati](https://www.privacyrights.org/data-breaches) per mantenere un elenco di password da vietare aggiornato in modo dinamico che assicura l'esclusione delle password vulnerabili prima che diventino una minaccia reale per i clienti di Azure AD. Per altre informazioni sulle attività volte a garantire la sicurezza, vedere il [Microsoft Security Intelligence Report](https://www.microsoft.com/security/sir/default.aspx).

### <a name="smart-password-lockout"></a>Smart Password Lockout

Quando Azure AD rileva un potenziale criminale informatico che prova a violare una password utente, l'account utente viene bloccato con Smart Password Lockout. Azure AD è progettato per determinare i rischi associati a sessioni di accesso specifiche. Usando i dati di sicurezza più aggiornati, Microsoft applica quindi la semantica di blocco alle minacce informatiche.

Se un utente viene bloccato da Azure AD, la schermata sarà simile alla seguente:

  ![Bloccato da Azure AD](./media/active-directory-secure-passwords/locked-out-azuread.png)

Per altri account Microsoft, la schermata sarà simile alla seguente:

  ![Bloccato da un account Microsoft](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Per informazioni sulla reimpostazione delle password in Azure Active Directory, vedere l'argomento [Reimpostazione self-service delle password di Azure AD per i professionisti IT](active-directory-passwords.md).

  >[!NOTE]
  >Se si è amministratori di Azure AD è consigliabile usare [Windows Hello](https://www.microsoft.com/windows/windows-hello) per evitare che gli utenti creino password tradizionali.
  >

## <a name="next-steps"></a>Passaggi successivi

* [Come aggiornare la password](active-directory-passwords-update-your-own-password.md)
* [Concetti fondamentali sulla gestione delle identità di Azure](fundamentals-identity.md)
* [Rapporto sull'attività di reimpostazione password](active-directory-passwords-reporting.md)



