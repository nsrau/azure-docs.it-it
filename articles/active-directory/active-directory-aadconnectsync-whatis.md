<properties
	pageTitle="Servizio di sincronizzazione Azure AD Connect: comprendere e personalizzare la sincronizzazione | Microsoft Azure"
	description="Spiega come funziona il servizio di sincronizzazione Azure AD Connect e come personalizzarlo."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="markusvi;andkjell"/>


# Servizio di sincronizzazione Azure AD Connect: comprendere e personalizzare la sincronizzazione
Il servizio di sincronizzazione Azure Active Directory Connect (Azure AD Connect Sync) è uno dei principali componenti di Azure AD Connect che si occupa di tutte le operazioni relative alla sincronizzazione dei dati di identità tra l'ambiente locale e Azure AD nel cloud. Il servizio di sincronizzazione Azure AD Connect è il successore di DirSync, Azure AD Sync e Forefront Identity Manager con il connettore Azure Active Directory configurato.

Questo argomento fornisce informazioni di base sul **servizio di sincronizzazione Azure AD Connect**, definito anche **motore di sincronizzazione**, e include collegamenti a tutti gli altri argomenti correlati. Per collegamenti relativi ad Azure AD Connect, vedere [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

## Argomenti relativi al servizio di sincronizzazione Azure AD Connect

| Argomento | Contenuti e destinatari |
| ----- | ----- |
| **Concetti fondamentali sul servizio di sincronizzazione Azure AD Connect** ||
| [Informazioni sull'architettura](active-directory-aadconnectsync-understanding-architecture.md) | Per chi non ha familiarità con il motore di sincronizzazione e vuole ottenere informazioni sull'architettura e sulla terminologia usata. |
| [Concetti tecnici](active-directory-aadconnectsync-technical-concepts.md) | Versione breve dell'argomento relativo all'architettura, con una rapida spiegazione della terminologia usata. |
| [Topologie per Azure AD Connect](active-directory-aadconnect-topologies.md) | Illustra le diverse topologie e i diversi scenari supportati dal motore di sincronizzazione. |
| **Configurazione personalizzata** ||
| [Informazioni sulla configurazione predefinita](active-directory-aadconnectsync-understanding-default-configuration.md)| Illustra le regole predefinite e la configurazione predefinita. Illustra anche il modo in cui le regole interagiscono per consentire il funzionamento degli scenari predefiniti. |
| [Informazioni su utenti e contatti](active-directory-aadconnectsync-understanding-users-and-contacts.md) | Continua l'argomento precedente e illustra l'interazione tra le configurazioni di utenti e contatti, in particolare in un ambiente a più foreste. |
| [Informazioni sulle espressioni di provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) | Fornisce informazioni approfondite sul funzionamento del modello di configurazione e sulla sintassi per il linguaggio delle espressioni. |
| [Procedure consigliate per modificare la configurazione predefinita](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) | Quando si conoscono i dettagli degli argomenti precedenti ed è necessario apportare modifiche alla configurazione predefinita per consentirne il funzionamento con scenari o requisiti specifici. |
| [Configurare il filtro](active-directory-aadconnectsync-configure-filtering.md) | Illustra le diverse opzioni per limitare gli oggetti da sincronizzare in Azure AD e fornisce procedure dettagliate per la configurazione. |
| **Funzionalità** ||
| [Implementare la sincronizzazione password](active-directory-aadconnectsync-implement-password-synchronization.md) | Illustra il funzionamento della sincronizzazione password, spiega come implementarla, usarla e risolverne i problemi. |
| [Impedire eliminazioni accidentali](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) | Illustra la funzionalità per *impedire eliminazioni accidentali* e la relativa configurazione. |
| **Operazioni** ||
| [Attività operative e considerazioni](active-directory-aadconnectsync-operations.md) | Illustra aspetti operativi, ad esempio il ripristino di emergenza. |
| **Altri riferimenti e informazioni** ||
| [Porte](active-directory-aadconnect-ports.md) | Elenca le porte da aprire tra il motore di sincronizzazione e le directory locali e Azure AD. |
| [Attributi sincronizzati con Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) | Elenca tutti gli attributi che vengono sincronizzati tra l'istanza locale di AD e Azure AD. |
| [Riferimento alle funzioni](active-directory-aadconnectsync-functions-reference.md) | Elenca tutte le funzioni disponibili nel provisioning dichiarativo. |

## Risorse aggiuntive

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0128_2016-->