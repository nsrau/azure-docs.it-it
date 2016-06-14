<properties
	pageTitle="Configurazione di Azure Active Directory per la gestione self-service dell'accesso alle applicazioni | Microsoft Azure"
	description="La gestione self-service dei gruppi consente agli utenti di creare e gestire i gruppi di sicurezza o gruppi di Office 365 in Azure Active Directory, nonché di richiedere l'appartenenza a tali gruppi."
	services="active-directory"
	documentationCenter=""
  authors="curtand"
	manager="stevenpo"
	editor=""
	/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/03/2016"
	ms.author="curtand"/>

# Configurazione di Azure Active Directory per la gestione self-service dei gruppi

La gestione self-service dei gruppi consente agli utenti di creare e gestire gruppi di sicurezza o gruppi di Office 365 in Azure Active Directory (Azure AD). Gli utenti possono anche richiedere l'appartenenza a gruppi di sicurezza o gruppi di Office 365, che il proprietario del gruppo può quindi approvare o rifiutare. In questo modo, il controllo giornaliero dell'appartenenza al gruppo può essere delegato a persone che ne conoscono il contesto aziendale. Le funzionalità di gestione self-service dei gruppi sono disponibili solo per i gruppi di sicurezza e i gruppi di Office 365, ma non per i gruppi di protezione abilitati alla posta o le liste di distribuzione.

La gestione self-service dei gruppi comprende attualmente due scenari principali, ovvero gestione delegata e gestione self-service.

- **Gestione delegata dei gruppi**: un esempio è costituito da un amministratore che gestisce l'accesso a un'applicazione SaaS usata dall'azienda. In questo caso la gestione dei diritti di accesso diventa più onerosa e pertanto l'amministratore chiede al titolare dell'organizzazione di creare un nuovo gruppo. L'amministratore assegna l'accesso per l'applicazione al nuovo gruppo e aggiunge al gruppo tutte le persone che accedono già all'applicazione. Il titolare dell'organizzazione può quindi aggiungere altri utenti, per i quali il provisioning nell'applicazione viene effettuato automaticamente. Il titolare dell'organizzazione non deve attendere che l'amministratore gestisca l'accesso degli utenti. Se l'amministratore concede la stessa autorizzazione a un manager di un diverso gruppo aziendale, anche tale persona può gestire l'accesso dei propri utenti. Né il titolare dell'organizzazione né il manager possono visualizzare o gestire gli utenti dell'altro. L'amministratore può comunque visualizzare tutti gli utenti che hanno accesso all'applicazione e, se necessario, revocare i diritti di accesso.

- **Gestione self-service dei gruppi**: un esempio di questo scenario è costituito da due utenti che hanno entrambi siti di SharePoint Online configurati in modo indipendente e che vogliono concedere al team dell'altro l'accesso al proprio sito. A questo scopo, possono creare un gruppo in Azure AD che può essere selezionato da ognuno in SharePoint Online per consentire l'accesso al proprio sito. Se un utente desidera accedere, effettua la richiesta dal pannello di accesso e dopo aver ottenuto l'approvazione potrà accedere automaticamente a entrambi i siti di SharePoint Online. Se successivamente uno degli utenti decide che tutte le persone che accedono al sito devono poter accedere anche a una determinata applicazione SaaS, l'amministratore dell'applicazione SaaS può aggiungere i diritti di accesso per l'applicazione al sito di SharePoint Online. A questo punto, tutte le richieste approvate garantiranno l'accesso ai due siti di SharePoint Online e anche all'applicazione SaaS.

## Rendere un gruppo disponibile per la modalità self-service per gli utenti finali

1. Nel [portale di Azure classico](https://manage.windowsazure.com) aprire la directory di Azure AD.

2. Nella scheda **Configura** impostare **Gestione gruppi delegata** su Abilitata.

3. Impostare **Gli utenti possono creare gruppi di sicurezza** o **Gli utenti possono creare gruppi di Office** su Abilitato.

Quando l'opzione **Gli utenti possono creare gruppi di sicurezza** è abilitata, tutti gli utenti nella directory possono creare nuovi gruppi di sicurezza e aggiungere membri a tali gruppi. Questi nuovi gruppi saranno visibili anche nel pannello di accesso per tutti gli altri utenti. Se consentito dall'impostazione dei criteri per il gruppo, gli altri utenti potranno creare richieste di partecipazione a questi gruppi. Se l'opzione **Gli utenti possono creare gruppi di sicurezza** è disabilitata, gli utenti non possono creare gruppi né modificare i gruppi esistenti di cui sono proprietari. Possono tuttavia gestire l'appartenenza a tali gruppi e approvare le richieste di partecipazione provenienti da altri utenti.

È anche possibile usare **Utenti che possono usare la modalità self-service per i gruppi di sicurezza** per ottenere un controllo di accesso con granularità più fine sulla gestione self-service dei gruppi per gli utenti. Quando l'opzione **Gli utenti possono creare i gruppi** è abilitata, tutti gli utenti nella directory possono creare nuovi gruppi e aggiungervi membri. Impostando anche **Utenti che possono usare la modalità self-service per i gruppi di sicurezza** su Alcuni, si limita la gestione dei gruppi a un gruppo limitato di utenti. Quando questa opzione è impostata su Alcuni, perché possano creare nuovi gruppi e aggiungervi membri gli utenti devono prima essere aggiunti al gruppo SSGMSecurityGroupsUsers. Impostare **Utenti che possono usare la modalità self-service per i gruppi di sicurezza** su Tutti per consentire a tutti gli utenti nella directory di creare nuovi gruppi.

È anche possibile usare la casella **Gruppi che possono usare la modalità self-service per i gruppi di sicurezza** per specificare un nome personalizzato di un gruppo i cui membri potranno usare la modalità self-service.

## Informazioni aggiuntive

Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)

* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

* [Informazioni su Azure Active Directory](active-directory-whatis.md)

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0608_2016-->