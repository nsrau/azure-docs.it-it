---
title: Procedure consigliate per la sicurezza per MFA | Documentazione Microsoft
description: Questo documento illustra le procedure consigliate per l&quot;uso di Azure MFA con account Azure
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 3be7d968-96bb-4320-8701-869fd04a2595
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 999361daa2faebe3e88cab0b6085a938d6f40e9d
ms.openlocfilehash: 24ccd50de9bc4ad01855112507ece59830f861ba
ms.lasthandoff: 02/17/2017

---

# <a name="security-best-practices-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Procedure consigliate sulla sicurezza per usare Azure Multi-Factor Authentication con account Azure AD

La scelta preferita per migliorare il processo di autenticazione nella maggior parte delle organizzazioni è la verifica in due passaggi. Azure Multi-Factor Authentication (MFA) consente alle aziende di soddisfare i requisiti di sicurezza e conformità, offrendo un'esperienza di accesso semplice per gli utenti. Questo articolo illustra alcune procedure consigliate che è opportuno considerare quando si pianifica l'adozione di Azure MFA.

## <a name="best-practices-for-a-cloud-deployment"></a>Procedure consigliate per una distribuzione cloud

Esistono due modi per abilitare Azure MFA per tutti gli utenti.

* Acquistare licenze per ogni utente (Azure MFA, Azure AD Premium o Enterprise Mobility + Security)
* Creare un provider di Multi-Factor Authentication scegliendo di pagare in base al numero di utenti o di autenticazioni

### <a name="licenses"></a>Licenze
![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

Se si dispone di licenze Azure AD Premium o Enterprise Mobility + Security, si può già usare Azure MFA. L'organizzazione non ha l'esigenza di avere elementi aggiuntivi per estendere la funzionalità di verifica in due passaggi a tutti gli utenti. È sufficiente assegnare una licenza a un utente e quindi è possibile attivare l'autenticazione MFA.

Quando si configura l'autenticazione Multi-Factor Authentication, tenere in considerazione le raccomandazioni seguenti:

* Non creare un provider di Multi-Factor Authentication in base al numero di autenticazioni. Questo perché si rischia di pagare per richieste di verifica provenienti da utenti che dispongono già di licenze.
* Se non si dispone di licenze sufficienti per tutti gli utenti, è possibile creare un provider di Multi-Factor Authentication in base al numero di utenti per coprire il resto dell'organizzazione. 
* Azure AD Connect è un requisito solo se si sincronizza l'ambiente Active Directory locale con una directory di Azure AD. Se si usa una directory di Azure AD non sincronizzata con un'istanza locale di Active Directory, Azure AD Connect non è necessario.

### <a name="multi-factor-auth-provider"></a>Provider di Multi-Factor Authentication
![Provider di Multi-Factor Authentication](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Se non sono disponibili licenze che incluono Azure MFA, il primo passaggio per l'adozione di Azure MFA nel cloud consiste nel creare un provider di Multi-Factor Authentication. Per impostazione predefinita, MFA è disponibile per gli amministratori globali che dispongono di Azure Active Directory, ma i provider di Multi-Factor Authentication estendono la funzionalità di verifica in due passaggi a tutti gli utenti. 

Quando si crea il provider di autenticazione, è necessario selezionare una directory e considerare quanto segue:

* Non è necessaria una directory di Azure AD per creare un provider Multi-Factor Authentication, ma con essa è possibile ottenere più funzionalità. Le seguenti funzionalità vengono abilitate associando il provider di Multi-Factor Authentication a una directory di Azure AD:  
  * Estendere la verifica in due passaggi a tutti gli utenti  
  * Offrire funzionalità aggiuntive agli amministratori globali, ad esempio il portale di gestione, messaggi di saluto personalizzati e report.
* Le funzionalità DirSync o AAD Sync sono necessarie solo se si esegue la sincronizzazione dell'ambiente Active Directory locale con una directory di Azure AD. Se si usa una directory di Azure AD non sincronizzata con un'istanza locale di Active Directory, non sono necessarie le funzionalità DirSync o AAD Sync.
* Scegliere il modello di consumo più adatto alle esigenze aziendali. Dopo aver selezionato il modello non sarà possibile modificarlo. I due modelli sono:
  * Per autenticazione: viene applicato un addebito per ogni verifica. Usare questo modello per usare la verifica in due passaggi per qualsiasi utente che accede a una determinata app, non per utenti specifici.
  * Per utente abilitato: viene applicato un addebito per ogni utente che viene abilitato per Azure MFA. Usare questo modello se non tutti gli utenti dispongono di licenze Azure AD Premium o Enterprise Mobility Suite.

### <a name="supportability"></a>Supporto
Poiché la maggior parte degli utenti è abituata a usare le password solo per l'autenticazione, è importante che l'azienda renda consapevoli tutti gli utenti in merito a questo processo. Questa consapevolezza può ridurre la probabilità che gli utenti contattino l'help desk per problemi di lieve entità riguardanti MFA. Esistono tuttavia alcuni scenari in cui è necessario disabilitare temporaneamente MFA. Per sapere come gestire questi scenari, usare le linee guida seguenti:

* Assicurarsi che il personale di supporto tecnico sappia come gestire gli scenari in cui l'utente non è in grado di accedere perché l'app per dispositivi mobili o il telefono non ricevono una notifica o una chiamata telefonica. Il supporto tecnico può abilitare un'[opzione di bypass monouso](multi-factor-authentication-whats-next.md#one-time-bypass) per consentire all'utente di autenticarsi una sola volta "ignorando" la verifica in due passaggi. Il bypass è temporaneo e scade dopo un numero di secondi specificato.
* La [funzionalità per gli indirizzi IP attendibili](multi-factor-authentication-whats-next.md#trusted-ips) in Azure MFA può essere considerata come un modo per ridurre al minimo la verifica in due passaggi. Questa funzionalità consente agli amministratori di un tenant gestito o federato di ignorare la verifica in due passaggi per gli utenti che accedono dalla rete Intranet locale dell'azienda. Le funzionalità sono disponibili per i tenant di Azure AD che dispongono di licenze Azure AD Premium, Enterprise Mobility Suite o Azure Multi-Factor Authentication.

## <a name="best-practices-for-an-on-premises-deployment"></a>Procedure consigliate per una distribuzione locale
Se la società ha deciso di sfruttare la propria infrastruttura per abilitare MFA, è necessario distribuire un server Azure Multi-Factor Authentication locale. I componenti del server MFA sono illustrati nel diagramma seguente:

![Componenti predefiniti di un server MFA: console, motore di sincronizzazione, portale di gestione, servizio cloud](./media/multi-factor-authentication-security-best-practices/server.png)
\*Non installato per impostazione predefinita\**Installato ma non abilitato per impostazione predefinita

Il server Azure Multi-Factor Authentication può essere usato per proteggere risorse cloud e locali a cui accedono gli account Azure AD. Questa operazione è possibile solo usando la federazione.  Ciò significa che è necessario avere AD FS federato con un tenant di Azure AD.
Quando si configura il server Multi-Factor Authentication, tenere in considerazione quanto segue:

* Se non si proteggono le risorse di Azure AD tramite Active Directory Federation Services (AD FS), il primo passaggio di autenticazione viene eseguito in locale tramite AD FS. Il secondo passaggio viene eseguito in locale rispettando l'attestazione.
* Non è necessario scaricare e installare il server Multi-Factor Authentication nel server federativo AD FS. È tuttavia necessario installare l'adapter Multi-Factor Authentication per AD FS in un computer Windows Server 2012 R2 che esegue AD FS. È possibile installare il server in un computer diverso, purché sia una versione supportata, e installare separatamente l'adapter AD FS nel server federativo di AD FS. 
* L'installazione guidata dell'adapter AD FS di Multi-Factor Authentication crea un gruppo di sicurezza denominato PhoneFactor Admins in Active Directory e quindi aggiunge l'account del servizio AD FS a questo gruppo. È consigliabile verificare nel controller di dominio che il gruppo PhoneFactor Admins sia stato effettivamente creato e che l'account del servizio AD FS sia un membro di questo gruppo. Se necessario, aggiungere l'account del servizio ADFS manualmente al gruppo PhoneFactor Admins sul controller di dominio.

### <a name="user-portal"></a>Portale per gli utenti
Questo portale viene eseguito in un sito Web Internet Information Server (IIS), che consente l'uso delle funzionalità self-service e fornisce un set completo di funzionalità di amministrazione degli utenti. Per configurare questo componente, usare le linee guida seguenti:

* Usare IIS 6 o versione successiva
* Installare e registrare ASP.NET v2.0.507207
* Assicurarsi che il server possa essere distribuito in una rete perimetrale

### <a name="app-passwords"></a>Password dell'app
Se l'organizzazione è federata e usa SSO con Azure AD e si prevede di usare Azure MFA, tenere presente quanto segue quando si usano password dell'app:

* La password dell'app viene verificata da Azure AD e di conseguenza ignora la federazione. La federazione viene usata solo quando si configura la password dell'app.
* Per gli utenti federati (SSO) le password vengono archiviate nell'ID dell'organizzazione. Se l'utente lascia l'azienda, tali informazioni devono essere trasmesse nell'ID organizzazione tramite DirSync in tempo reale. La disabilitazione o l'eliminazione dell'account può richiedere fino a&3; ore per la sincronizzazione, ritardando la disabilitazione o l'eliminazione delle password dell'app in Azure AD.
* Le impostazioni locali di Controllo dell'accesso Client non vengono rispettate dalla password dell'app.
* Per le password dell'app non è disponibile alcuna funzionalità di registrazione o controllo dell'autenticazione in locale.
* Alcune architetture avanzate possono richiedere una combinazione di nome utente e password dell'organizzazione e password dell'app quando si usa la verifica in due passaggi con i client, a seconda della posizione in cui viene eseguita l'autenticazione. Per i client che si autenticano con un'infrastruttura locale, verranno usati un nome utente e una password dell'organizzazione. Per i client che eseguono l'autenticazione con AD Azure, verrà usata la password dell'app.
* Per impostazione predefinita, gli utenti non possono creare password dell'app. Se si desidera consentire agli utenti di creare password dell'app, selezionare l'opzione **Consentire agli utenti di creare password dell'app per accedere alle applicazioni non basate su browser**.

## <a name="additional-considerations"></a>Considerazione aggiuntive
Usare l'elenco seguente per leggere alcune considerazioni e procedure consigliate aggiuntive per ogni componente che sarà distribuito in locale:

- Configurare Azure Multi-Factor Authentication con [Active Directory Federation Service](multi-factor-authentication-get-started-adfs.md).
- Installare e configurare il server Azure MFA con [Autenticazione RADIUS](multi-factor-authentication-get-started-server-radius.md).
- Installare e configurare il server Azure MFA con [Autenticazione IIS](multi-factor-authentication-get-started-server-iis.md).
- Installare e configurare il server Azure MFA con l'[autenticazione di Windows](multi-factor-authentication-get-started-server-windows.md).
- Installare e configurare il server Azure MFA con [Autenticazione LDAP](multi-factor-authentication-get-started-server-ldap.md).
- Installare e configurare il server Azure MFA con [Gateway Desktop remoto e server Azure Multi-Factor Authentication usando RADIUS](multi-factor-authentication-get-started-server-rdg.md).
- Installare e configurare la sincronizzazione tra il server Azure MFA e [Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md).
- [Distribuire il servizio Web App Mobile del server Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server-webservice.md).
- [Configurazione VPN avanzata con Azure Multi-Factor Authentication](multi-factor-authentication-advanced-vpn-configurations.md) per appliance Cisco ASA, Citrix Netscaler e Juniper/Pulse Secure VPN con LDAP o RADIUS.

## <a name="next-steps"></a>Passaggi successivi
Anche se questo articolo evidenzia alcune procedure consigliate per Azure MFA, sono disponibili anche altre risorse che è possibile usare quando si pianifica la distribuzione di MFA. L'elenco seguente include alcuni articoli importanti che possono risultare utili durante questo processo:

* [Report in Azure Multi-Factor Authentication](multi-factor-authentication-manage-reports.md)
* [L'esperienza di registrazione della verifica in due passaggi](multi-factor-authentication-end-user-first-time.md)
* [Domande frequenti su Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)


