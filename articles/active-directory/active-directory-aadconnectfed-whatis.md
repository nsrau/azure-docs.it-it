<properties
	pageTitle="Azure AD Connect e federazione | Microsoft Azure"
	description="Questa pagina è il punto centrale per tutta la documentazione correlata alle operazioni di AD FS tramite Azure AD Connect"
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/04/2016"
	ms.author="anandy"/>


# Azure AD Connect e federazione

Azure AD Connect permette di configurare la federazione con l'istanza di AD FS locale e Azure AD. Grazie all'accesso federato, gli utenti possono accedere ai servizi basati su Azure AD con le proprie password locali e, se usano la rete aziendale, senza dover immettere di nuovo le password. L’opzione di federazione con ADFS consente di distribuire un ADFS nuovo o di specificarne uno esistente nella farm Windows Server 2012 R2.

Questo argomento è il punto centrale per le informazioni sulle funzionalità relative alla federazione per Azure AD Connect e contiene un elenco di collegamenti ad altri argomenti correlati. Per i collegamenti relativi ad Azure AD Connect, vedere Integrazione delle identità locali con Azure Active Directory.

## Azure AD Connect - Argomenti sulla federazione

| Argomento | Contenuti e destinatari |
|:------|:-----------|
| **Opzioni di accesso utente di Azure AD Connect** ||
| [Informazioni sulle opzioni di accesso utente](active-directory-aadconnect-user-signin.md) | Descrizione di diverse opzioni di accesso utente e del modo in cui influiscono sull'esperienza utente di accesso di Azure |
| **Installazione di AD FS con Azure AD Connect**||
| [Prerequisiti](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) | Prerequisiti per una corretta installazione di AD FS con Azure AD Connect|
| [Configurare la farm AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) | Come installare una nuova farm AD FS tramite Azure AD Connect |
| **Modifica della configurazione di AD FS** | |
| [Ripristino del trust](active-directory-aadconnect-federation-management.md#reparing-the-trust) | Come ripristinare il trust corrente tra l'istanza di AD FS locale e Office 365/Azure |
| [Aggiunta di un nuovo server AD FS](active-directory-aadconnect-federation-management.md#adding-a-new-ad-fs-server) | Espansione della farm AD FS con un server AD FS aggiuntivo successivamente all'installazione iniziale |
| [Aggiunta di un nuovo server WAP AD FS](active-directory-aadconnect-federation-management.md#adding-a-new-wap-server) | Espansione della farm AD FS con un server WAP aggiuntivo successivamente all'installazione iniziale |
| [Aggiungere un nuovo dominio federato](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain) | Aggiunta di un altro dominio per la federazione con Azure AD |
|**Attività successive all'installazione**||
| [Aggiungere l'illustrazione o il logo personalizzato della società](active-directory-aadconnect-federation-management.md#add-custom-company-logo-or-illustration)| Come modificare l'esperienza di accesso specificando il logo personalizzato da visualizzare nella pagina di accesso di AD FS |
| [Aggiungere una descrizione di accesso](active-directory-aadconnect-federation-management.md#add-sign-in-description) | Modifica della descrizione di accesso nella pagina di accesso di AD FS | 
| [Modificare le regole attestazioni per AD FS](active-directory-aadconnect-federation-management.md#modifying-ad-fs-claim-rules) | Modifica/aggiunta di regole attestazioni AD FS corrispondenti alla configurazione della sincronizzazione di Azure AD Connect |


## Risorse aggiuntive

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0511_2016-->