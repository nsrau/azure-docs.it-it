<properties
	pageTitle="Servizio di sincronizzazione Azure AD Connect: comprendere e personalizzare la sincronizzazione | Microsoft Azure"
	description="Spiega come funziona il servizio di sincronizzazione Azure AD Connect e come personalizzarlo."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/29/2016"
	ms.author="markusvi;andkjell"/>


# Servizio di sincronizzazione Azure AD Connect: Comprendere e personalizzare la sincronizzazione
Il servizio di sincronizzazione Azure Active Directory Connect (servizio di sincronizzazione Azure AD Connect) è un componente importante di Azure AD Connect. Si occupa di tutte le operazioni che riguardano la sincronizzazione dei dati relativi alle identità tra l'ambiente locale e Azure AD. Il servizio di sincronizzazione Azure AD Connect è il successore di DirSync, Azure AD Sync e Forefront Identity Manager con il connettore Azure Active Directory configurato.

Questo argomento fornisce informazioni di base sul **servizio di sincronizzazione Azure AD Connect**, detto anche **motore di sincronizzazione**, e include un elenco di collegamenti a tutti gli altri argomenti correlati. Per collegamenti relativi ad Azure AD Connect, vedere [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

Il servizio di sincronizzazione è costituito da due componenti: la **sincronizzazione di Azure AD Connect** locale e il servizio di Azure AD chiamato **servizio di sincronizzazione di Azure AD Connect**. Il servizio è comune a DirSync, Azure AD Sync e Azure AD Connect.

## Argomenti relativi al servizio di sincronizzazione Azure AD Connect

Argomento | Contenuti e destinatari
----- | -----
**Concetti fondamentali sul servizio di sincronizzazione Azure AD Connect** |
[Informazioni sull'architettura](active-directory-aadconnectsync-understanding-architecture.md) | Per chi non ha familiarità con il motore di sincronizzazione e vuole avere informazioni sull'architettura e sulla terminologia usata.
[Concetti tecnici](active-directory-aadconnectsync-technical-concepts.md) | Versione breve dell'argomento relativo all'architettura, con una rapida spiegazione della terminologia usata.
[Topologie per Azure AD Connect](active-directory-aadconnect-topologies.md) | Illustra le diverse topologie e i diversi scenari supportati dal motore di sincronizzazione.
**Configurazione personalizzata** |
[Nuova esecuzione dell'installazione guidata](active-directory-aadconnectsync-installation-wizard.md) | Spiega quali opzioni sono disponibili se si esegue di nuovo l'installazione guidata di Azure AD Connect.
[Informazioni sul provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md)| Descrive il modello di configurazione denominato provisioning dichiarativo.
[Informazioni sulle espressioni di provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) | Descrive la sintassi del linguaggio delle espressioni usato nel provisioning dichiarativo.
[Informazioni sulla configurazione predefinita](active-directory-aadconnectsync-understanding-default-configuration.md)| Illustra le regole predefinite e la configurazione predefinita. Illustra anche il modo in cui le regole interagiscono per consentire il funzionamento degli scenari predefiniti.
[Informazioni su utenti e contatti](active-directory-aadconnectsync-understanding-users-and-contacts.md) | Continua l'argomento precedente e illustra l'interazione tra le configurazioni di utenti e contatti, in particolare in un ambiente con più foreste.
[Servizio di sincronizzazione Azure AD Connect: Come apportare modifiche alla configurazione predefinita](active-directory-aadconnectsync-change-the-configuration.md) | Illustra in dettaglio come creare una configurazione comune per i flussi di attributi.
[Procedure consigliate per modificare la configurazione predefinita](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) | Limitazioni del supporto e come apportare modifiche alla configurazione predefinita.
[Configurare il filtro](active-directory-aadconnectsync-configure-filtering.md) | Illustra le diverse opzioni per limitare gli oggetti da sincronizzare in Azure AD e fornisce procedure dettagliate per la configurazione di queste opzioni.
**Funzionalità e scenari** |
[Impedire eliminazioni accidentali](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) | Illustra la funzionalità che *impedisce le eliminazioni accidentali* e spiega come configurarla.
[Utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md) | Illustra l'utilità di pianificazione predefinita, che importa, sincronizza ed esporta i dati.
[Implementare la sincronizzazione password](active-directory-aadconnectsync-implement-password-synchronization.md) | Illustra il funzionamento della sincronizzazione password, spiega come implementarla, usarla e risolverne i problemi.
[Writeback dei dispositivi](active-directory-aadconnect-feature-device-writeback.md) | Illustra il funzionamento del writeback dei dispositivi in Azure AD Connect.
[Estensioni della directory](active-directory-aadconnectsync-feature-directory-extensions.md) | Illustra come estendere lo schema di Azure AD con i propri attributi personalizzati.
**Servizio di sincronizzazione** |
[Funzionalità del servizio di sincronizzazione di Azure AD Connect](active-directory-aadconnectsyncservice-features.md) | Descrive il servizio di sincronizzazione e la modifica delle impostazioni di sincronizzazione in Azure AD.
[Resilienza degli attributi duplicati](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) | Descrive come abilitare e usare la resilienza dei valori di attributo duplicati **userPrincipalName** e **proxyAddresses**.
**Operazioni e interfaccia utente** |
[Synchronization Service Manager.](active-directory-aadconnectsync-service-manager-ui.md) | Descrive l'interfaccia utente di Synchronization Service Manager, comprese le schede [Operations](active-directory-aadconnectsync-service-manager-ui-operations.md) (Operazioni), [Connectors](active-directory-aadconnectsync-service-manager-ui-connectors.md) (Connettori), [Metaverse Designer](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) (Progettazione metaverse) e [Metaverse Search](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) (Ricerca metaverse).
[Attività operative e considerazioni](active-directory-aadconnectsync-operations.md) | Illustra aspetti operativi, ad esempio il ripristino di emergenza.
**Procedura:** |
[Reimpostare l'account Azure AD](active-directory-aadconnectsync-howto-azureadaccount.md) | Come reimpostare le credenziali dell'account del servizio usato per la connessione dal servizio di sincronizzazione Azure AD Connect ad Azure AD.
**Altri riferimenti e informazioni** |
[Porte](active-directory-aadconnect-ports.md) | Elenca le porte da aprire tra il motore di sincronizzazione e le directory locali e Azure AD.
[Attributi sincronizzati con Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) | Elenca tutti gli attributi che vengono sincronizzati tra l'istanza locale di AD e Azure AD.
[Riferimento alle funzioni](active-directory-aadconnectsync-functions-reference.md) | Elenca tutte le funzioni disponibili nel provisioning dichiarativo.

## Risorse aggiuntive

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0907_2016-->