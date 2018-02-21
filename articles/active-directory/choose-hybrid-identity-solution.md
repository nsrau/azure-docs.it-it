---
title: "Scegliere una soluzione ibrida di gestione delle identità di Azure | Microsoft Docs"
description: "Informazioni generali sulle soluzioni ibride di gestione delle identità disponibili e le raccomandazioni per migliorare il processo decisionale in termini di identity governance per la propria organizzazione."
keywords: 
author: jeffgilb
manager: mtillman
ms.reviewer: jsnow
ms.author: billmath
ms.date: 01/03/2018
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: 78813cfbfc9d28e9578f1970083e17d423e071b5
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="microsoft-hybrid-identity-solutions"></a>Soluzioni ibride di gestione delle identità
Le soluzioni ibride di gestione delle identità di [Microsoft Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) consentono di sincronizzare oggetti di directory locali con Azure AD gestendo al contempo gli utenti in locale. La prima decisione da adottare quando si intende sincronizzare Windows Server Active Directory in locale con Azure AD è se usare l'identità sincronizzata o l'identità federata. Le identità sincronizzate e, facoltativamente, gli hash delle password, consentono agli utenti di usare la stessa password per accedere alle risorse aziendali locali e basate su cloud. Per i requisiti di scenari più avanzati, ad esempio Single Sign-On (SSO) o l'autenticazione MFA locale, è necessario distribuire Active Directory Federation Services (ADFS) in identità federate. 

Sono disponibili diverse opzioni per la configurazione della gestione di identità ibrida. Questo articolo contiene informazioni utili per scegliere l'opzione più adatta all'organizzazione in base alla facilità di distribuzione e alle esigenze specifiche a livello di gestione dell'accesso e delle identità. Quando si valuta il modello di identità migliore in base alle esigenze specifiche dell'organizzazione, è necessario considerare fattori come il tempo, l'infrastruttura esistente, la complessità e i costi. Questi fattori variano in base all'organizzazione e possono cambiare nel tempo. Tuttavia, se i requisiti cambiano, si dispone della flessibilità per passare a un modello di gestione delle identità diverso.

> [!TIP]
> Queste soluzioni vengono distribuite da [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="synchronized-identity"></a>Identità sincronizzata 
L'identità sincronizzata è il modo più semplice per sincronizzare gli oggetti di directory locali (utenti e gruppi) con Azure AD. 

![Identità ibrida sincronizzata](./media/choose-hybrid-identity-solution/synchronized-identity.png)

Sebbene l'identità sincronizzata sia il metodo più semplice e rapido, gli utenti devono tuttavia mantenere una password diversa per le risorse basate su cloud. Per evitare questo problema, è possibile anche (facoltativamente) [sincronizzare un hash delle password utente](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization#what-is-password-synchronization) nella directory di Azure AD. La sincronizzazione di hash delle password consente agli utenti di accedere alle risorse aziendali basate su cloud con lo stesso nome utente e password usati in locale. Azure AD Connect controlla periodicamente la directory locale per rilevare eventuali modifiche e mantiene sincronizzata la directory di Azure AD. Quando cambia un attributo utente o una password in Active Directory locale, il valore viene aggiornato automaticamente in Azure AD. 

Per la maggior parte delle organizzazioni che ha solo la necessità di consentire agli utenti di accedere a Office 365, alle applicazioni SaaS e ad altre risorse basate su AD Azure, è consigliabile l'opzione di sincronizzazione password predefinita. Se questa opzione non è adatta, è necessario scegliere tra l'autenticazione pass-through e AD FS.

> [!TIP]
> Le password utente vengono archiviate in Windows Server Active Directory locale sotto forma di un valore hash che rappresenta la password utente effettiva. Un valore hash è il risultato di una funzione matematica unidirezionale, chiamata algoritmo di hash. Non esiste un metodo per ripristinare la versione in testo normale di una password dal risultato di una funzione unidirezionale. Non è possibile usare l'hash della password per accedere alla rete locale. Se si sceglie di sincronizzare le password, Azure AD Connect estrae gli hash delle password da Active Directory locale e applica un'elaborazione della sicurezza aggiuntiva all'hash delle password prima della sincronizzazione in Azure AD. La sincronizzazione delle password può essere usata anche insieme al writeback delle password per consentire la reimpostazione autonoma delle password in Azure AD. È possibile abilitare l'accesso Single Sign-On anche per gli utenti di computer aggiunti al dominio connessi alla rete aziendale. Con l'accesso Single Sign-On, gli utenti abilitati possono accedere in modo sicuro alle risorse cloud immettendo semplicemente un nome utente. 

## <a name="pass-through-authentication"></a>Autenticazione pass-through
L'[autenticazione pass-through di Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) offre una soluzione di convalida delle password semplice per i servizi basati su Azure AD usando Active Directory locale. È consigliabile usare l'autenticazione pass-through se i criteri di conformità e sicurezza per l'organizzazione non consentono l'invio di password degli utenti, anche in formato hash, ed è sufficiente supportare l'accesso SSO desktop per i dispositivi appartenenti a un dominio. L'autenticazione pass-through non richiede la distribuzione nella rete perimetrale, semplificando l'infrastruttura di distribuzione rispetto ad AD FS. Quando gli utenti eseguono l'accesso usando Azure AD, questo metodo di autenticazione convalida le loro password direttamente con l'istanza locale di Active Directory.

![Autenticazione pass-through](./media/choose-hybrid-identity-solution/pass-through-authentication.png)

Con l'autenticazione pass-through non è necessaria un'infrastruttura di rete complessa e non è necessario archiviare le password locali nel cloud. Combinata con l'accesso Single Sign-On, l'autenticazione pass-through offre un'esperienza realmente integrata in fase di accesso ad Azure AD o ad altri servizi cloud.

L'autenticazione pass-through può essere configurata tramite Azure AD Connect e usa un semplice agente locale che rimane in ascolto delle richieste di convalida delle password. L'agente può essere facilmente distribuito su più computer per garantire un'elevata disponibilità e bilanciamento del carico. Dal momento che tutte le comunicazioni avvengono solo in uscita, non esiste alcun requisito per l'installazione del connettore in una rete perimetrale. Di seguito sono riportati i requisiti del computer server per il connettore:

- Windows Server 2012 R2 o versione successiva
- Aggiunta a un dominio nella foresta tramite cui gli utenti sono convalidati

Non è attualmente supportata l'autenticazione pass-through quando si usano dispositivi Windows 10 aggiunti ad Azure AD. Tuttavia, è possibile usare la sincronizzazione dell'hash delle password come fallback automatico per supportare i client Windows 10 e legacy indicati in precedenza. Durante l'anteprima, la sincronizzazione di hash delle password è abilitata per impostazione predefinita quando si seleziona l'autenticazione pass-through come opzione di accesso in Azure AD Connect.


## <a name="federated-identity-ad-fs"></a>Identità federata (AD FS)
Per un maggiore controllo sul modo in cui gli utenti accedono a Office 365 e ad altri servizi cloud, è possibile impostare la sincronizzazione delle directory con l'accesso Single Sign-On (SSO) usando [Active Directory Federation Services (ADFS)](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server-2016). La federazione degli accessi dell'utente con AD FS delega l'autenticazione a un server locale che convalida le credenziali utente. In questo modello, le credenziali di Active Directory locale non vengono mai passate ad Azure AD.

![Identità federata](./media/choose-hybrid-identity-solution/federated-identity.png)

Questo metodo di accesso, noto anche come federazione delle identità, assicura che tutte le autenticazioni utente vengano controllate in locale e consente agli amministratori di implementare livelli di controllo dell'accesso più rigorosi. La federazione delle identità con AD FS è l'opzione più complessa e richiede la distribuzione di server aggiuntivi nell'ambiente locale. La federazione delle identità impegna a fornire il supporto 24 ore su 24, 7 giorni su 7 per l'infrastruttura AD FS e Active Directory. Questo livello elevato di supporto è necessario perché se non sono disponibili l'accesso a Internet locale, il controller di dominio o i server AD FS, gli utenti non possono accedere ai servizi cloud.

> [!TIP]
> Se si decide di usare la federazione con Active Directory Federation Services (AD FS), è possibile configurare la sincronizzazione delle password come backup in caso di errore dell'infrastruttura di AD FS.


## <a name="common-scenarios-and-recommendations"></a>Scenari comuni e raccomandazioni
Di seguito sono riportati alcuni scenari comuni di gestione di identità ibride e degli accessi con raccomandazioni riguardo all'opzione o alle opzioni di gestione delle identità ibride più appropriate per ciascuno di essi.

|Esigenza:|PWS e SSO<sup>1</sup>| PTA e SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|Sincronizzare automaticamente nel cloud nuovi account utente, di contatti o di gruppo creati in Active Directory locale.|![Consigliato](./media/choose-hybrid-identity-solution/ic195031.png)| ![Consigliato](./media/choose-hybrid-identity-solution/ic195031.png) |![Consigliato](./media/choose-hybrid-identity-solution/ic195031.png)|
|Configurare il tenant per scenari ibridi di Office 365|![Consigliato](./media/choose-hybrid-identity-solution/ic195031.png)| ![Consigliato](./media/choose-hybrid-identity-solution/ic195031.png) |![Consigliato](./media/choose-hybrid-identity-solution/ic195031.png)|
|Consentire agli utenti di accedere ai servizi cloud usando la propria password locale|![Consigliato](./media/choose-hybrid-identity-solution/ic195031.png)| ![Consigliato](./media/choose-hybrid-identity-solution/ic195031.png) |![Consigliato](./media/choose-hybrid-identity-solution/ic195031.png)|
|Implementare l'accesso SSO usando le credenziali aziendali|![Consigliato](./media/choose-hybrid-identity-solution/ic195031.png)| ![Consigliato](./media/choose-hybrid-identity-solution/ic195031.png) |![Consigliato](./media/choose-hybrid-identity-solution/ic195031.png)|
|Assicurarsi che gli hash delle password non vengano archiviati nel cloud| |![Consigliato](./media/choose-hybrid-identity-solution/ic195031.png)|![Consigliato](./media/choose-hybrid-identity-solution/ic195031.png)|
|Abilitare soluzioni di autenticazione a più fattori locali| | |![Consigliato](./media/choose-hybrid-identity-solution/ic195031.png)|
|Supportare l'autenticazione di smart card per gli utenti<sup>4</sup>| | |![Consigliato](./media/choose-hybrid-identity-solution/ic195031.png)|
|Visualizzare le notifiche di scadenza delle password nel portale di Office e sul desktop di Windows 10| | |![Consigliato](./media/choose-hybrid-identity-solution/ic195031.png)|

> <sup>1</sup> Sincronizzazione delle password con l'accesso Single Sign-On (SSO) 

> <sup>2</sup> Autenticazione pass-through e accesso Single Sign-On. 

> <sup>3</sup> Accesso Single Sign-On federato con AD FS.

> <sup>4</sup> ADFS può essere integrato con l'infrastruttura a chiave pubblica aziendale per consentire l'accesso usando certificati. Questi certificati possono essere certificati software distribuiti tramite canali di provisioning attendibili, ad esempio i certificati di gestione di dispositivi mobili (MDM) o l'oggetto Criteri di gruppo o smart card (comprese le schede PIV/CAC) o Hello for Business (cert-trust). Per altre informazioni sul supporto dell'autenticazione con smart card, vedere [questo blog](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).


## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni in un ambiente Azure di prova](https://aka.ms/aad-poc)

[Installare Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)

[Monitorare la sincronizzazione delle identità ibride](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)

