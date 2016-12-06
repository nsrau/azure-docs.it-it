---
title: Protezione dell&quot;accesso con privilegi in Azure AD | Documentazione Microsoft
description: Argomento che descrive gli approcci per la protezione dell&quot;accesso con privilegi ai servizi Azure, Azure Active Directory e Microsoft Online Services.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: mwahl
ms.assetid: 235a0ce9-1daf-4433-8f65-9c6afcd64d08
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 1150f7eaba41e42c94ed7297bd7603dc506a97a9


---
# <a name="securing-privileged-access-in-azure-ad"></a>Protezione dell'accesso con privilegi in Azure AD
La protezione dell'accesso con privilegi è il primo passaggio fondamentale per proteggere gli asset aziendali in un'organizzazione moderna. Gli account con privilegi sono quelli che amministrano e gestiscono i sistemi IT. Gli utenti malintenzionati usano questi account per ottenere l'accesso ai dati e ai sistemi di un'organizzazione. Per proteggere l'accesso con privilegi, è necessario isolare gli account e i sistemi dal rischio di esposizione a utenti malintenzionati.

Il numero di utenti che ottiene l'accesso con privilegi tramite i servizi cloud è in aumento. Tra questi, sono inclusi gli amministratori globali di Office 365, gli amministratori delle sottoscrizioni di Azure e gli utenti che dispongono dell'accesso amministrativo alle macchine virtuali o alle app SaaS.

Microsoft consiglia di seguire questa guida di orientamento sulla [protezione dell'accesso con privilegi](https://technet.microsoft.com/library/mt631194.aspx).

Per i clienti che usano Azure Active Directory per gestire l'accesso ad Azure, a Office 365 o ad altri servizi e applicazioni Microsoft, questi principi si applicano sia se gli account utente sono gestiti e autenticati da Active Directory o Azure Active Directory o meno. Le sezioni seguenti offrono maggiori informazioni sulle funzionalità di Azure AD per il supporto della protezione dell'accesso con privilegi.

## <a name="multi-factor-authentication"></a>Autenticazione a più fattori
Per aumentare la sicurezza dell'autenticazione degli amministratori, è opportuno richiedere l'autenticazione a più fattori (MFA) prima di concedere i privilegi. MFA è un metodo di verifica dell'identità dell'utente che richiede l'uso di più fattori, oltre a un nome utente e una password. Fornisce un secondo livello di sicurezza agli accessi e alle transazioni degli utenti.

Azure multi-Factor Authentication contribuisce a salvaguardare l'accesso a dati e applicazioni rispondendo alla richiesta degli utenti di poter usare un processo di accesso semplice. Offre autenticazione avanzata tramite una gamma di semplici opzioni di verifica, ad esempio una telefonata, un SMS, una notifica dell'app per dispositivi mobili oppure un codice di verifica e token OATH di terze parti.

Per una panoramica del funzionamento di Azure Multi-Factor Authentication, vedere il video seguente.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Windows-Azure-Multi-Factor-Authentication/player]
> 
> 

Per altre informazioni, vedere il post del blog relativo all' [autenticazione a più fattori per Office 365 e per Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/).

## <a name="time-bound-privileges"></a>Privilegi con vincoli di tempo
Per alcune organizzazioni, il numero di utenti assegnati a ruoli con privilegi elevati potrebbe essere ritenuto eccessivo. Un utente potrebbe essere stato aggiunto al ruolo per un'attività specifica, ad esempio l'iscrizione a un servizio, ma aver usato tali privilegi raramente in seguito.

Per ridurre il tempo di esposizione dei privilegi e aumentare la visibilità sul relativo utilizzo, è possibile vincolare gli utenti all'uso dei privilegi in modalità JIT (Just-In-Time) solo quando devono eseguire un'attività. Per Azure Active Directory e Microsoft Online Services è possibile usare [Azure AD Privileged Identity Management (PIM)](http://aka.ms/AzurePIM).

![Dashboard di PIM][2]

## <a name="attack-detection"></a>Rilevamento degli attacchi
[Azure Active Directory Identity Protection](../active-directory-identityprotection.md) offre una visualizzazione consolidata degli eventi di rischio e delle potenziali vulnerabilità che interessano le identità dell'organizzazione. In base agli eventi di rischio, Identity Protection calcola un livello di rischio utente per ogni utente e permette di configurare criteri basati sul rischio per proteggere automaticamente le identità dell'organizzazione. I criteri basati sul rischio, insieme ad altri controlli di accesso condizionale forniti da Azure Active Directory e da EMS, possono bloccare automaticamente l'utente o proporre suggerimenti, ad esempio la reimpostazione della password e l'imposizione dell'autenticazione a più fattori.

![Azure AD Identity Protection][3]

## <a name="conditional-access"></a>Accesso condizionale
Il controllo di accesso condizionale consente ad Azure Active Directory di controllare le condizioni specifiche selezionate durante l'autenticazione di un utente e prima di consentire l'accesso a un'applicazione. Se tali condizioni vengono soddisfatte, l'utente viene autenticato e gli viene consentito l'accesso all'applicazione.

![Impostazione delle regole di accesso condizionale con MFA][4]

## <a name="related-articles"></a>Articoli correlati
* Abilitare [Azure Multi-Factor Authentication](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
* Abilitare [Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-configure.md)
* Abilitare [Azure AD Identity Protection](../active-directory-identityprotection.md)
* Abilitare i [controlli di accesso condizionale](../active-directory-conditional-access.md)

Per altre informazioni su come creare una guida di orientamento alla sicurezza completa, vedere la sezione "Customer responsibilities and roadmap" del documento [Microsoft Cloud Security for Enterprise Architects](http://aka.ms/securecustomer) . Per altre informazioni sull'utilizzo dei servizi Microsoft illustrati in questi argomenti, contattare il rappresentante Microsoft oppure visitare la pagina relativa alle [soluzioni per la sicurezza informatica](https://www.microsoft.com/microsoftservices/campaigns/cybersecurity-protection.aspx).

<!--Image references-->
[1]: ../media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ../media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ../media/active-directory-identityprotection/29.png
[4]: ../media/active-directory-conditional-access/conditionalaccess-saas-apps.png



<!--HONumber=Nov16_HO3-->


