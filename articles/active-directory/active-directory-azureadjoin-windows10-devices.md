<properties 
	pageTitle="Uso di dispositivi Windows 10 in azienda | Microsoft Azure" 
	description="Offre una panoramica delle funzionalità a disposizione degli utenti e del reparto IT e confronta i diversi modi in cui è possibile effettuare il provisioning di un dispositivo e usarlo in una grande impresa con Windows 10." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2015" 
	ms.author="femila"/>

# Uso di dispositivi Windows 10 in azienda

Windows 10 offre alle organizzazioni tre modelli per consentire agli utenti di accedere alle risorse aziendali in modo pratico e sicuro.

1. **Aggiunta ad Azure AD**, una novità di Windows 10, è un'esperienza di provisioning aziendale self-service, destinata ai lavoratori che accedono principalmente alle risorse nel cloud, ad esempio Office 365.
1. **Aggiunta a un dominio**, migliore in Windows 10 se connesso ad Azure AD, per le organizzazioni che attuano importanti investimenti in app e risorse locali.
1. **Nuova esperienza BYOD semplificata** che consente agli utenti di aggiungere un account aziendale a Windows per accedere facilmente alle risorse aziendali dai dispositivi personali.

La tabella seguente offre una panoramica delle funzionalità a disposizione degli utenti e del reparto IT e confronta i diversi modi in cui è possibile effettuare il provisioning di un dispositivo e usarlo in una grande impresa con Windows 10:

| | Aggiunta a un dominio | Aggiunta ad Azure AD | Dispositivo personale |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|---------------|-----------------|
| Accesso da dispositivo Windows con account aziendali | Sì | Sì | No |
| SSO utente ad app Azure AD e Office365,[1] Single Sign-On, ovvero possibilità di accedere alle risorse aziendali senza immettere nuovamente le credenziali dopo il primo accesso. | Sì | Sì | Sì |
| SSO utente ad app Kerberos/NTLM | Sì | Limitato | Tramite VPN |
| Autenticazione avanzata e pratico accesso all'account aziendale con Microsoft Passport e Windows Hello. | Sì | Sì | Sì |
| Accesso alla sezione aziendale di Windows Store usando un account aziendale (non un account Microsoft) | Sì | Sì | Sì |
| Roaming conforme ai criteri aziendali delle impostazioni utente tra i dispositivi usando account aziendali | Sì | Sì | Sì |
| Limitazione dell'accesso alle app aziendali da dispositivi conformi ai criteri per i dispositivi aziendali | Sì | Sì | Sì |
| Provisioning self-service per gli utenti di dispositivi per lavorare da qualsiasi luogo | No | Sì | Sì |
| Possibilità di gestire i dispositivi | Sì, tramite Criteri di gruppo/SCCM | Sì | Sì |

##Dispositivi aziendali: Aggiunta ad Azure AD e Aggiunta a un dominio

Windows 10 offre ai dispositivi aziendali due modelli per accedere alle risorse aziendali:

- Aggiunta ad Azure AD
- Aggiunta a un dominio

 Sono entrambe valide opzioni, a seconda delle esigenze e dei requisiti dell'organizzazione. In alcuni casi, le organizzazioni possono sfruttare i vantaggi derivanti dall'abilitazione di entrambi i metodi di distribuzione.

## Quando usare Aggiunta ad Azure Active Directory

Aggiunta ad Azure AD è una nuova esperienza di provisioning aziendale self-service di Windows 10. È destinata ai lavoratori che accedono principalmente alle risorse aziendali nel cloud, ad esempio Office 365. È un modo semplice per configurare PC, tablet e telefoni per l'organizzazione. I dispositivi vengono gestiti tramite MDM che usa controlli coerenti nelle piattaforme Windows.

**Usare Aggiunta ad Azure AD nei casi seguenti**:

1.	Si vuole abilitare il provisioning self-service di dispositivi per i lavoratori in viaggio.
2.	Si forniscono agli utenti dispositivi mobili aziendali, ad esempio tablet e telefoni.
3.	Si vuole gestire un gruppo di utenti in Azure AD invece che in AD, ad esempio lavoratori stagionali, terzisti o studenti.
4.	Si vogliono fornire funzionalità di join ai lavoratori in succursali remote con un'infrastruttura locale limitata.
5.	Non è disponibile Active Directory locale.

In alcune organizzazioni la distribuzione dei dispositivi aziendali avverrà principalmente con Aggiunta ad Azure AD, soprattutto quando si esegue la migrazione della maggior parte o di tutte le risorse al cloud. Le organizzazioni ibride con AD e Azure AD possono anche scegliere di distribuire un metodo oppure l'altro a seconda dell'utente o del reparto. Scuole e università possono gestire il personale in AD e gli studenti in Azure AD. Alcune società invece possono scegliere di gestire gli uffici remoti o il reparto vendite in Azure AD. Entrambi i metodi Aggiunta ad Azure AD e Aggiunta a un dominio possono essere usati in un'organizzazione ibrida. Aggiunta ad Azure AD può essere un complemento perfetto per distribuire dispositivi in un ambiente aziendale.

**Se la maggior parte degli accessi alle risorse aziendali avviene tramite il cloud, un'organizzazione può usufruire di vantaggi aggiuntivi**:

- È possibile rimuovere le dipendenze dall'infrastruttura di identità locale.
- È possibile semplificare il modello di distribuzione dei dispositivi evitando le soluzioni di creazione dell'immagine consentendo la configurazione self-service.
- È possibile usare MDM per gestire tutti i dispositivi in piattaforme diverse.

Per altre informazioni su Aggiunta ad Azure AD, vedere [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-overview.md).

## Quando usare Aggiunta a un dominio (o continuare a usarlo)

Negli ultimi 15 anni, per connettere i dispositivi sui cui lavorare le aziende hanno fatto essenzialmente ricorso alla funzione di aggiunta a un dominio. In questo modo, infatti, gli utenti potevano accedere ai dispositivi tramite i propri account aziendali di Active Directory e il personale IT poteva gestire centralmente e completamente questi dispositivi. In genere, inoltre, le aziende si basano su metodi di creazione dell'immagine per effettuare il provisioning dei dispositivi e, per gestirli, usano System Center Configuration Manager (SCCM) o Criteri di gruppo.

**È necessario usare l'aggiunta a un dominio nell'organizzazione nei casi seguenti**:

- Sono state distribuite app Win32 in questi dispositivi che usano NTLM/Kerberos.
- Sono necessari Criteri di gruppo o SCCM/DCM per gestire i dispositivi.
- Si vuole continuare a usare soluzioni di creazione dell'immagine per configurare i dispositivi per i dipendenti.

**In Windows 10, in seguito alla connessione ad Azure AD la funzione di aggiunta a un dominio fornisce anche i vantaggi seguenti**:

- Autenticazione avanzata associata a dispositivo e pratico accesso all'account aziendale con Microsoft Passport e Windows Hello.
- Accesso alla sezione aziendale di Windows Store usando account aziendali (non è necessario un account Microsoft).
- Roaming conforme ai criteri aziendali delle impostazioni utente tra i dispositivi usando account aziendali (non è necessario un account Microsoft).
- Possibilità di limitare l'accesso alle app aziendali da dispositivi conformi ai criteri per i dispositivi aziendali
- Per altre informazioni su Aggiunta ad Azure AD, vedere [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-overview.md).

##Abilitazione di dispositivi personali per l'uso di account aziendali

Per supportare BYOD nell'organizzazione, Windows 10 offre all'utente la possibilità di "aggiungere un account aziendale o dell'istituto di istruzione" al PC, al tablet o al telefono. Aggiungendo un account aziendale, il dispositivo viene registrato con Azure AD e, facoltativamente, in MDM configurato dall'organizzazione per i dispositivi. Nella directory questi dispositivi verranno visualizzati come registrati invece che come aggiunti ad Azure AD. L'IT può applicare criteri diversi in base a queste informazioni, offrendo un approccio più semplice per un dispositivo personale che per un dispositivo aziendale, se necessario.

Gli utenti possono aggiungere un account aziendale al dispositivo personale in modo molto semplice: quando si accede a un'applicazione aziendale per la prima volta oppure manualmente tramite l'applicazione Impostazioni. Questo account aziendale fornirà l'accesso SSO alle risorse dell'organizzazione.

Per altre informazioni su Aggiunta a Azure AD, vedere [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md).

## Abilitazione di Aggiunta a un dominio o di Aggiunta ad Azure AD 

Tutti i metodi descritti sopra (aggiunta a un dominio, aggiunta ad Azure AD e aggiunta di un account aziendale) hanno punti di ingresso nell'esperienza utente Windows 10, tuttavia, perché l'esperienza funzioni, richiedono tutti l'intervento dell'IT per abilitare le funzionalità nell'infrastruttura.

##Aggiunta ad Azure AD

Per distribuire Aggiunta ad Azure AD per tutti i gruppi di utenti, è necessario quanto segue:
---------------------------------------------------------------------------
- Sottoscrizione di Azure AD.
- Altre funzionalità richiederanno una sottoscrizione di Azure AD Premium, ad esempio la registrazione automatica in MDM.
- Sottoscrizione di Azure AD Premium.
- MDM (sottoscrizione di Intune o MDM per Office365 o qualsiasi fornitore MDM di terze parti che si integra con Azure AD: per i dettagli, vedere la sezione Domande frequenti)
- Se l'organizzazione è ibrida, è consigliabile eseguire questa operazione:
- Distribuire Azure AD Connect per estendere la directory locale ad Azure AD.

##Aggiunta a un dominio

L'aggiunta a un dominio continua a funzionare come sempre, ma, per usufruire dei vantaggi di Azure AD, sarà necessario quanto segue:

- Sottoscrizione di Azure AD.
- Distribuire Azure AD Connect per estendere la directory locale ad Azure AD.
- Impostazione dei criteri per connettere dispositivi appartenenti a un dominio ad Azure AD.
- Per altre informazioni sull'aggiunta a un dominio in Windows 10, vedere <link-to-DJ-in-Win10-deployment-guide>.
- Per l'accesso condizionale, è possibile creare criteri che consentano l'accesso ai dispositivi appartenenti al dominio. Per abilitare regole che richiedono dispositivi compatibili, è necessario quanto segue:
- System Center Configuration Manager versione 1509 per Technical Preview (vedere la documentazione di TechNet e il post di blog).

##BYOD e aggiunta di un account aziendale

Per abilitare BYOD con gli account aziendali, è necessario quanto segue:

- Sottoscrizione di Azure AD.
- Altre funzionalità richiederanno una sottoscrizione di Azure AD Premium, ad esempio la registrazione automatica in MDM.
- Sottoscrizione di Azure AD Premium.
- MDM (sottoscrizione di Intune o MDM per Office365 o qualsiasi fornitore MDM di terze parti che si integra con Azure AD: per i dettagli, vedere la sezione Domande frequenti)

##Microsoft Passport

Per abilitare Microsoft Passport, sarà anche necessario quanto segue:

- Infrastruttura PKI per il supporto dell'autenticazione basata su certificati con Microsoft Passport.
- Sottoscrizione di Intune per il supporto dell'autenticazione basata su certificati con Microsoft Passport per l'aggiunta ad Azure e gli account aziendali.
- System Center Configuration Manager versione 1509 per Technical Preview (vedere la documentazione di TechNet e il post di blog) per il supporto dell'autenticazione basata su certificati con Microsoft Passport per Aggiunta a un dominio.
- Impostazione di criteri per abilitare Microsoft Passport nell'organizzazione.

Come alternativa all'infrastruttura PKI, è possibile abilitare Microsoft Passport basato su chiavi, eseguendo queste operazioni:

- Distribuzione di controller di dominio di Windows Server 2016 "Production Preview 1". Non è necessario alcun livello di funzionalità del dominio o della foresta. Saranno sufficienti un paio di controller di dominio per la ridondanza per ogni sito AD.
- Impostazione di criteri per abilitare Microsoft Passport nell'organizzazione.
- Per altre informazioni su Microsoft Passport e Windows Hello in Windows 10, vedere <link-to-MS-Passport-and-Windows-Hello-document>.

## Domande frequenti

###Come funziona Aggiunta all'area di lavoro in Windows 10?
Aggiunta all'area di lavoro in Windows 8.1 viene usato per abilitare BYOD. In Windows 10 BYOD viene abilitato dall'aggiunta di un account aziendale, come spiegato prima in questo documento. Per le organizzazioni che non integrano la gestione di dispositivi mobili con Azure AD, gli utenti possono registrare il dispositivo nella gestione manualmente con **Impostazioni** > **Account** > **Accesso società**.

###L'utente può connettere l'account Microsoft all'account di dominio in Windows 10?
Non in Windows 10. In Windows 8.1 gli utenti dei dispositivi aggiunti a un dominio possono "connettere" l'account Microsoft (ad esempio, Hotmail, Live, Outlook, XBox e così via) all'account di dominio per abilitare alcune esperienze, ad esempio SSO per servizi Live, l'uso di Windows Store e il roaming delle impostazioni utente tra i dispositivi. In Windows 10 la funzionalità di connessione dell'account Microsoft è stata disattivata. L'utente può aggiungere uno o più account Microsoft come account aggiuntivi per abilitare SSO per i servizi consumer, ad esempio Windows Store. Questa operazione viene eseguita in **Impostazioni** > **Account** > **Il tuo account**.

L'account Microsoft connesso degli utenti che eseguono l'aggiornamento da dispositivi Windows 8.1 aggiunti a un dominio verrà automaticamente aggiunto all'elenco di account aggiuntivi usati.


## Informazioni aggiuntive
* [Windows 10 per le aziende: modalità d'uso dei dispositivi di lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0218_2016-->