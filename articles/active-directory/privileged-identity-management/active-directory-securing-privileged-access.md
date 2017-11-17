---
title: Protezione dell'accesso con privilegi in Azure AD | Documentazione Microsoft
description: Argomento che descrive gli approcci per la protezione dell'accesso con privilegi ai servizi Azure, Azure Active Directory e Microsoft Online Services.
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: mwahl
ms.assetid: 235a0ce9-1daf-4433-8f65-9c6afcd64d08
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: kgremban
ms.custom: pim
ms.openlocfilehash: 00fc998666574f0b85b968cba174d328a300e861
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="securing-privileged-access-in-azure-ad"></a>Protezione dell'accesso con privilegi in Azure AD
La protezione dell'accesso con privilegi è il primo passaggio fondamentale per proteggere gli asset aziendali in un'organizzazione moderna. Gli account con privilegi sono gli account che amministrano e gestiscono i sistemi IT. Gli utenti malintenzionati usano questi account per ottenere l'accesso ai dati e ai sistemi di un'organizzazione. Per proteggere l'accesso con privilegi, è necessario isolare gli account e i sistemi dal rischio di esposizione a utenti malintenzionati.

Il numero di utenti che ottiene l'accesso con privilegi tramite i servizi cloud è in aumento. Tra questi, sono inclusi gli amministratori globali di Office 365, gli amministratori delle sottoscrizioni di Azure e gli utenti che dispongono dell'accesso amministrativo alle macchine virtuali o alle app SaaS.

Microsoft consiglia di seguire questa guida di orientamento sulla [protezione dell'accesso con privilegi](https://technet.microsoft.com/library/mt631194.aspx).

Per i clienti che usano Azure Active Directory, Office 365 o altri servizi e applicazioni Microsoft, questi principi si applicano sia se gli account utente sono gestiti e autenticati da Active Directory o Azure Active Directory o meno. Le sezioni seguenti offrono maggiori informazioni sulle funzionalità di Azure AD per il supporto della protezione dell'accesso con privilegi.

## <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication
Per aumentare la sicurezza dell'autenticazione degli amministratori, è opportuno richiedere la verifica in due passaggi prima di concedere i privilegi. La verifica in due passaggi è un metodo di verifica dell'identità dell'utente che richiede l'uso di più fattori, oltre a un nome utente e una password. Fornisce un secondo livello di sicurezza agli accessi e alle transazioni degli utenti.

Azure Multi-Factor Authentication, o MFA, è una soluzione per la verifica in due passaggi che permette di proteggere l'accesso ai dati e alle applicazioni, garantendo al tempo stesso agli utenti una procedura di accesso semplice. Offre un'autenticazione avanzata con una gamma di opzioni di verifica semplici che includono:

- le telefonate
- i messaggi di testo
- le notifiche dell'app per dispositivi mobili
- i codici di verifica dell'app per dispositivi mobili
- Token OATH di terze parti

Per una panoramica del funzionamento di Azure Multi-Factor Authentication, vedere il video seguente:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]

Per altre informazioni, vedere [MFA for Office 365 and MFA for Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) (MFA per Office 365 e MFA per Azure).

## <a name="time-bound-privileges"></a>Privilegi con vincoli di tempo
Per alcune organizzazioni, il numero di utenti assegnati a ruoli con privilegi elevati potrebbe essere ritenuto eccessivo. Un utente potrebbe essere stato aggiunto al ruolo per un'attività specifica, ad esempio l'iscrizione a un servizio, ma aver usato tali privilegi raramente in seguito.

Per ridurre il tempo di esposizione dei privilegi e aumentare la visibilità del relativo utilizzo, limitare gli utenti a prendere solo i propri privilegi "just in time" (JIT), oppure ad assegnare questi ruoli per un periodo di tempo ridotto sapendo che i privilegi verranno revocati automaticamente. Per Azure Active Directory, Risorse di Azure (Anteprima) e Microsoft Online Services è possibile usare [Azure Active Directory Privileged Identity Management (PIM)](http://aka.ms/AzurePIM).

![Dashboard di PIM][2]

## <a name="attack-detection"></a>Rilevamento degli attacchi
[Azure Active Directory Identity Protection](../active-directory-identityprotection.md) offre una visualizzazione consolidata degli eventi di rischio e delle potenziali vulnerabilità che interessano le identità dell'organizzazione. In base agli eventi di rischio, Identity Protection calcola un livello di rischio utente per ogni utente e permette di configurare criteri basati sul rischio per proteggere automaticamente le identità dell'organizzazione. I criteri basati sul rischio, insieme ad altri controlli di accesso condizionale forniti da Azure Active Directory e da EMS, possono bloccare automaticamente l'utente o proporre suggerimenti, ad esempio la reimpostazione della password e l'imposizione dell'autenticazione a più fattori.

![Azure AD Identity Protection][3]

## <a name="conditional-access"></a>Accesso condizionale
Il controllo di accesso condizionale consente ad Azure Active Directory di controllare le condizioni specifiche selezionate durante l'autenticazione di un utente e prima di consentire l'accesso a un'applicazione. Se tali condizioni vengono soddisfatte, l'utente viene autenticato e gli viene consentito l'accesso all'applicazione.

## <a name="related-articles"></a>Articoli correlati
* Abilitare [Azure Multi-Factor Authentication](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
* Abilitare [Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-configure.md)
* Abilitare [Azure AD Identity Protection](../active-directory-identityprotection.md)
* Abilitare i [controlli di accesso condizionale](../active-directory-conditional-access-azure-portal.md)

Per altre informazioni su come creare una guida di orientamento alla sicurezza completa, vedere la sezione "Customer responsibilities and roadmap" del documento [Microsoft Cloud Security for Enterprise Architects](http://aka.ms/securecustomer) . Per altre informazioni sull'utilizzo dei servizi Microsoft illustrati in questi argomenti, contattare il rappresentante Microsoft oppure visitare la pagina relativa alle [soluzioni per la sicurezza informatica](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

<!--Image references-->
[1]: ../media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ../media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ../media/active-directory-identityprotection/29.png
