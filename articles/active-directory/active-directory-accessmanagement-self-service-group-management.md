<properties pageTitle="Configurazione di Azure AD per la gestione self-service dell'accesso alle applicazioni | Microsoft Azure" description="Argomento che descrive come gestire i gruppi in Azure AD." services="active-directory" documentationCenter="" authors="femila" manager="swadhwa"" editor="" tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>

#Configurazione di Azure AD per la gestione self-service dell'accesso alle applicazioni

La funzionalità di gestione self-service dei gruppi consente agli utenti di creare e gestire i gruppi di sicurezza in Microsoft Azure Active Directory (AD), nonché di richiedere l'appartenenza a tali gruppi. La richiesta può successivamente essere approvata o rifiutata dal proprietario del gruppo. Grazie alle funzionalità di gestione self-service dei gruppi, il controllo giornaliero dell'appartenenza al gruppo può essere delegato a persone che ne conoscono il contesto aziendale.

La gestione self-service dei gruppi è attualmente costituita da due scenari principali, ovvero gestione delegata e gestione self-service.


- La **gestione delegata dei gruppi** fa riferimento a uno scenario in cui un amministratore gestisce l'accesso a un'applicazione SaaS usata dall'azienda. In questo caso la gestione dei diritti di accesso diventa più onerosa e pertanto l'amministratore chiede al titolare dell'organizzazione di creare un nuovo gruppo. L'amministratore assegna l'accesso per l'applicazione a un nuovo gruppo creato dal titolare dell'azienda e inserisce in questo gruppo tutte le persone che hanno accesso all'applicazione. Il titolare dell'azienda può quindi aggiungere altri utenti e per tali utenti viene automaticamente eseguito il provisioning nell'applicazione. Il titolare dell'organizzazione non deve attendere che l'amministratore esegua questa operazione, ma può gestire personalmente l'accesso degli utenti. L'amministratore può eseguire la stessa operazione per un assistente amministrativo di un diverso gruppo aziendale in modo che sia il titolare dell'azienda che l'assistente amministrativo possano gestire l'accesso per i propri utenti senza interferenze. L'amministratore può comunque visualizzare tutti gli utenti che hanno accesso all'applicazione e, se necessario, revocare i diritti di accesso.


- La **gestione self-service dei gruppi** fa riferimento a uno scenario in cui due utenti dispongono di siti di SharePoint Online configurati in modo indipendente, ma entrambi desiderano semplificare l'accesso al proprio sito da parte del team dell'altro utente. Pertanto, creano un gruppo in Azure AD e in SharePoint Online ognuno di essi seleziona lo stesso gruppo per consentire l'accesso ai propri siti. Se un utente desidera accedere, effettua la richiesta dal pannello di accesso e dopo aver ottenuto l'approvazione potrà accedere automaticamente a entrambi i siti di SharePoint Online. Successivamente uno degli utenti decide che tutti gli utenti che accedono al proprio sito devono poter accedere anche a una particolare applicazione SaaS. Potrà chiedere all'amministratore di tale applicazione di concedere i diritti di accesso per tale applicazione al proprio sito. A questo punto, tutte le richieste approvate garantiranno l'accesso ai due siti di SharePoint Online e anche all'applicazione SaaS.



##Rendere un gruppo disponibile per la modalità self-service per gli utenti finali

Nella scheda Configura del portale di gestione di Azure impostare l'opzione Gestione gruppi delegata su Abilitata e quindi impostare l'opzione Gli utenti possono creare i gruppi su Abilitato.

Se l'opzione **Gli utenti possono creare i gruppi** è impostata su **Abilitato**, tutti gli utenti nella directory possono creare nuovi gruppi di sicurezza e aggiungere membri a tali gruppi. Si noti che questi nuovi gruppi sono visibili anche nel pannello di accesso per tutti gli altri utenti e che gli altri utenti possono creare richieste di iscrizione a questi gruppi se ciò è consentito dall'impostazione dei criteri del gruppo. Se questa opzione è impostata su Disabilitato, gli utenti non possono creare gruppi né modificare i gruppi esistenti di cui sono proprietari, ma possono comunque gestire le appartenenze a tali gruppi e approvare le richieste di iscrizione ricevute da altri utenti.

È inoltre possibile utilizzare l'opzione Utenti che possono usare la modalità self-service per i gruppi di sicurezza per ottenere un controllo più dettagliato dell'accesso alle funzionalità di gestione self-service dei gruppi. Quando l'opzione Gli utenti possono creare i gruppi è impostata su Abilitato, tutti gli utenti nella directory possono creare nuovi gruppi di sicurezza e aggiungere membri a tali gruppi. Inoltre, l'impostazione dell'opzione Utenti che possono usare la modalità self-service per i gruppi di sicurezza su Alcuni consente di limitare la gestione dei gruppi di sicurezza a un gruppo limitato di utenti. Quando questa opzione è impostata su Alcuni, nella directory viene creato un gruppo denominato SSGMSecurityGroupsUsers; solo gli utenti membri di questo gruppo possono quindi creare nuovi gruppi di sicurezza e aggiungere membri a tali gruppi all'interno della directory. Impostare l'opzione Utenti che possono usare la modalità self-service per i gruppi di sicurezza su Tutti per consentire a tutti gli utenti nella directory di creare nuovi gruppi di sicurezza.

È inoltre possibile utilizzare il campo Gruppi che possono usare la modalità self-service per i gruppi di sicurezza (impostato su 'SSGMSecurityGroupsUsers' per impostazione predefinita) per specificare il nome personalizzato per un gruppo che conterrà tutti gli utenti autorizzati a usare la modalità self-service e a creare nuovi gruppi di sicurezza nella directory.

Di seguito sono elencati alcuni argomenti contenenti informazioni aggiuntive su Azure Active Directory

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)

* [Informazioni su Azure Active Directory](active-directory-whatis.md)

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=July15_HO4-->