---
title: Uso di dispositivi Windows 10 in azienda | Microsoft Docs
description: Offre una panoramica delle funzionalità a disposizione degli utenti e degli amministratori IT, mettendo a confronto i diversi modi in cui è possibile effettuare il provisioning di un dispositivo e usarlo in una organizzazione con Windows 10.
services: active-directory
documentationcenter: ''
author: femila
manager: swadhwa
editor: ''
tags: azure-classic-portal

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila

---
# <a name="using-windows-10-devices-in-your-workplace"></a>Uso di dispositivi Windows 10 in azienda
Si applica a: PC Windows 10

Windows 10 offre alle organizzazioni tre modelli per consentire agli utenti di accedere alle risorse aziendali in modo pratico e sicuro.

* **Aggiunta ad Azure Active Directory** (Aggiunta ad Azure AD), per gli utenti che accedono a risorse quali Office 365 principalmente nel cloud. Aggiunta ad Azure AD è un'esperienza di provisioning aziendale self-service che costituisce una novità di Windows 10.
* **Aggiunta a un dominio**, per le organizzazioni che hanno investito in risorse e app locali. Aggiunta a un dominio offre un'esperienza migliorata in Windows 10 quando si è connessi ad Azure AD.
* **Nuova esperienza BYOD semplificata**, che consente agli utenti di aggiungere a Windows un account aziendale o dell'istituto di istruzione per accedere facilmente alle risorse dai dispositivi personali.

La tabella seguente offre una panoramica delle funzionalità a disposizione degli utenti e degli amministratori IT, mettendo a confronto i diversi modi in cui è possibile effettuare il provisioning di un dispositivo e usarlo in una organizzazione con Windows 10:

|  | Aggiunta a un dominio | Aggiunta ad Azure AD | Dispositivo personale |
| --- | --- | --- | --- |
| Accesso a un dispositivo Windows per gli account aziendali o dell'istituto di istruzione. |Sì |Sì |No |
| Single Sign-On (SSO) dell'utente alle app Azure AD e a Office 365. Il Single Sign-On è la possibilità di eseguire l'accesso una sola volta per accedere alle risorse aziendali. |Sì |Sì |Sì |
| SSO dell'utente alle app Kerberos/NTLM. |Sì |Limitato |Sì, tramite VPN |
| Autorizzazione avanzata e pratico accesso all'account aziendale o dell'istituto di istruzione con Microsoft Passport e Windows Hello. |Sì |Sì |Sì |
| Accesso alla sezione aziendale di Windows Store con un account aziendale o dell'istituto di istruzione, non un account Microsoft. |Sì |Sì |Sì |
| Roaming delle impostazioni utente tra dispositivi conforme ai criteri dell'organizzazione con account aziendali o dell'istituto di istruzione. |Sì |Sì |Sì |
| Possibilità di limitare l'accesso alle app aziendali ai dispositivi conformi ai criteri per i dispositivi aziendali. |Sì |Sì |Sì |
| Provisioning self-service per gli utenti di dispositivi per lavorare da qualsiasi luogo. |No |Sì |Sì |
| Possibilità di gestire i dispositivi. |Sì, tramite Criteri di gruppo/SCCM |Sì |Sì |

## <a name="use-work-owned-devices-with-azure-ad-join-and-domain-join-in-windows-10"></a>Uso di dispositivi aziendali con Aggiunta ad Azure AD e aggiunta a un dominio in Windows 10
Windows 10 offre ai dispositivi aziendali due modi per accedere alle risorse aziendali:

* Aggiunta ad Azure AD
* Aggiunta a un dominio
  
  Sono entrambe valide opzioni, a seconda delle esigenze e dei requisiti dell'organizzazione. In alcuni casi, le organizzazioni possono sfruttare i vantaggi derivanti dall'abilitazione di entrambi i metodi di distribuzione.

## <a name="when-to-use-azure-active-directory-join"></a>Quando usare Aggiunta ad Azure Active Directory
Aggiunta ad Azure AD è una nuova esperienza di provisioning aziendale self-service di Windows 10.  È destinato ai lavoratori che accedono alle risorse aziendali come Office 365 principalmente nel cloud. È un modo semplice per configurare computer, tablet e telefoni per l'organizzazione. Fa uso della gestione di dispositivi mobili, che garantisce controlli coerenti nelle piattaforme Windows.

**Usare Aggiunta ad Azure AD nei casi seguenti**:

* Si vuole abilitare il provisioning self-service di dispositivi per i lavoratori in viaggio.
* Si forniscono agli utenti dispositivi mobili aziendali, ad esempio tablet e telefoni.
* Si vuole gestire un gruppo di utenti in Azure AD invece che in Active Directory, ad esempio lavoratori stagionali, terzisti o studenti.
* Si vogliono fornire funzionalità di join ai lavoratori in succursali remote con un'infrastruttura locale limitata.
* Non si ha un'istanza di Active Directory locale.

In alcune organizzazioni la distribuzione dei dispositivi aziendali avverrà principalmente con Aggiunta ad Azure AD, soprattutto quando si esegue la migrazione della maggior parte o di tutte le risorse al cloud. Le organizzazioni ibride con Active Directory e Azure AD possono anche scegliere di distribuire un metodo oppure l'altro a seconda dell'utente o del reparto.

Distretti scolastici e università, ad esempio, possono gestire il personale di Active Directory e gli studenti in Azure AD. Alcune società possono scegliere di gestire gli uffici remoti o i reparti vendite in Azure AD. In un'organizzazione ibrida è possibile usare entrambi i metodi, Aggiunta ad Azure AD e aggiunta a un dominio. Aggiunta ad Azure AD può rappresentare un'importante integrazione all'aggiunta a un dominio per la distribuzione di dispositivi in un ambiente aziendale.

**Se la maggior parte degli accessi alle risorse aziendali avviene tramite il cloud, l'organizzazione può usufruire di vantaggi aggiuntivi se**:

* È possibile rimuovere le dipendenze dall'infrastruttura di identità locale.
* È possibile semplificare il modello di distribuzione dei dispositivi evitando le soluzioni di creazione dell'immagine consentendo la configurazione self-service.
* È possibile usare la gestione di dispositivi mobili per gestire tutti i dispositivi in piattaforme diverse.

Per altre informazioni su Aggiunta ad Azure AD, vedere [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-overview.md).

## <a name="when-to-use-domain-join-(or-keep-using-it)"></a>Quando usare o continuare a usare l'aggiunta a un dominio
Sono molte le organizzazioni che negli ultimi 15 anni hanno usato l'aggiunta a un dominio per connettere i dispositivi aziendali. L'aggiunta a un dominio consente agli utenti di accedere ai dispositivi con il proprio account aziendale o dell'istituto di istruzione di Active Directory e consente ai responsabili IT di gestire tali dispositivi in modo centralizzato e completo. In genere le aziende si basano su metodi di creazione dell'immagine per effettuare il provisioning dei dispositivi e, per gestirli, usano System Center Configuration Manager (SCCM) o Criteri di gruppo.

**L'organizzazione dovrebbe usare o continuare a usare l'aggiunta a un dominio nei casi seguenti**:

* Sono state distribuite app Win32 in questi dispositivi che usano NTLM/Kerberos.
* Sono necessari Criteri di gruppo o SCCM/DCM per gestire i dispositivi.
* Si vuole continuare a usare soluzioni di creazione dell'immagine per configurare i dispositivi per i dipendenti.

**In Windows 10, con la connessione ad Azure AD la funzione di aggiunta a un dominio offre anche i vantaggi seguenti**:

* Autenticazione avanzata associata a dispositivo e pratico accesso all'account aziendale o dell'istituto di istruzione con Microsoft Passport e Windows Hello.
* Accesso alla sezione aziendale di Windows Store per i dispositivi che usano account aziendali o dell'istituto di istruzione, non è necessario un account Microsoft.
* Roaming delle impostazioni utente tra dispositivi conforme ai criteri dell'organizzazione usando account aziendali o dell'istituto di istruzione, non è necessario un account Microsoft.
* Possibilità di limitare l'accesso alle app aziendali ai dispositivi conformi ai criteri per i dispositivi aziendali.

Per altre informazioni su Aggiunta ad Azure AD, vedere [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-overview.md).

## <a name="enable-joining-of-personally-owned-devices-for-work-or-school"></a>Abilitare l'aggiunta di dispositivi personali per il lavoro o lo studio
Per supportare l'uso di BYOD nell'organizzazione, Windows 10 offre all'utente la possibilità di aggiungere un account aziendale o dell'istituto di istruzione al proprio computer, al tablet o al telefono. Dopo l'aggiunta dell'account aziendale o dell'istituto di istruzione il dispositivo viene registrato in Azure AD e, facoltativamente, anche nel sistema di gestione di dispositivi mobili configurato dall'organizzazione. Nella directory questi dispositivi verranno visualizzati come registrati invece che come aggiunti ad Azure AD. Gli amministratori IT possono applicare criteri diversi in base a queste informazioni, offrendo un approccio più semplice per un dispositivo personale che per un dispositivo aziendale, se necessario.

Gli utenti possono aggiungere un account aziendale o dell'istituto di istruzione al dispositivo personale in modo molto pratico, quando accedono a un'applicazione di lavoro per la prima volta o, manualmente, usando il menu Impostazioni. L'account fornirà l'accesso SSO alle risorse dell'organizzazione.

Per altre informazioni su Aggiunta a Azure AD, vedere [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md).

## <a name="enable-domain-join-or-azure-ad-join"></a>Abilitare l'aggiunta a un dominio o Aggiunta ad Azure AD
Tutti i metodi descritti finora, aggiunta a un dominio, Aggiunta ad Azure AD e Aggiungi un account aziendale o dell'istituto di istruzione, hanno punti di ingresso nell'esperienza utente di Windows 10. Il loro funzionamento, tuttavia, richiede l'abilitazione della funzionalità nell'infrastruttura da parte di un amministratore IT.

## <a name="requirements-for-deploying-azure-ad-join"></a>Requisiti per la distribuzione di Aggiunta ad Azure AD
Per distribuire Aggiunta ad Azure AD per tutti i gruppi di utenti, è necessario quanto segue:

* Una sottoscrizione di Azure AD.
* Una sottoscrizione di Azure AD Premium, ad esempio la registrazione per la gestione di dispositivi mobili automatica, se sono necessarie altre funzionalità.
* Gestione di dispositivi mobili, ad esempio una sottoscrizione di Microsoft Intune, la gestione di dispositivi mobili per Office 365 o fornitori partner di gestione di dispositivi mobili che si integrano con Azure AD. Per altre informazioni, vedere la sezione [Domande frequenti](#frequently-asked-questions) verso la fine di questo articolo.

Per le organizzazioni ibride, è consigliabile distribuire Azure AD Connect per estendere la directory locale ad Azure AD.

## <a name="requirements-for-using-domain-join-with-azure-ad"></a>Requisiti per l'uso dell'aggiunta a un dominio con Azure AD
L'aggiunta a un dominio continua a funzionare normalmente. Per usufruire dei vantaggi di Azure AD è necessario quanto segue:

* Una sottoscrizione di Azure AD.
* Una distribuzione di Azure AD Connect per estendere la directory locale ad Azure AD.
* Criteri che consentano l'accesso condizionale ad Azure AD per i dispositivi aggiunti a un dominio.
* Criterio che consentano l'accesso ai dispositivi aggiunti a un dominio per poter limitare l'accesso per alcuni dispositivi.
* System Center Configuration Manager versione 1509 per Technical Preview, per abilitare regole per la richiesta di dispositivi compatibili. Per informazioni, vedere la documentazione di TechNet e il post di blog.

Per altre informazioni sull'aggiunta a un dominio di Windows 10, vedere <link-to-DJ-in-Win10-deployment-guide>.

## <a name="requirements-for-using-byod-and-"add-a-work-or-school-account""></a>Requisiti per l'uso di BYOD con l'opzione Aggiungi un account aziendale o dell'istituto di istruzione
Per consentire l'uso di BYOD (Bring Your Own Device) con gli account aziendali o dell'istituto di istruzione, è necessario quanto segue:

* Una sottoscrizione di Azure AD.
* Una sottoscrizione di Azure AD Premium, ad esempio la registrazione per la gestione di dispositivi mobili automatica, se sono necessarie altre funzionalità.

## <a name="requirements-for-using-microsoft-passport"></a>Requisiti per l'uso di Microsoft Passport
Per abilitare Microsoft Passport, è necessario quanto segue:

* Un'infrastruttura a chiave pubblica (PKI) per il supporto dell'autenticazione basata su certificati che usa Microsoft Passport.
* Una sottoscrizione di Intune per il supporto dell'autenticazione basata su certificati che usa Microsoft Passport per l'aggiunta ad Azure AD e gli account aziendali o dell'istituto di istruzione.
* System Center Configuration Manager versione 1509 per Technical Preview (vedere la documentazione di TechNet e il post di blog) per il supporto dell'autenticazione basata su certificati che usa Microsoft Passport per l'aggiunta a un dominio.
* Criteri per abilitare Microsoft Passport nell'organizzazione.

Come alternativa all'infrastruttura PKI, è possibile abilitare Microsoft Passport basato su chiavi eseguendo queste operazioni:

* Distribuzione di controller di dominio di Windows Server 2016 "Production Preview 1". Non è necessario alcun livello di funzionalità del dominio o della foresta. Saranno sufficienti vari controller di dominio per la ridondanza per ogni sito Active Directory.
* Impostazione di criteri per abilitare Microsoft Passport nell'organizzazione.

Per altre informazioni su Microsoft Passport e Windows Hello in Windows 10, vedere <link-to-MS-Passport-and-Windows-Hello-document>.

## <a name="frequently-asked-questions"></a>Domande frequenti
### <a name="which-partner-mobile-device-management-products-integrate-with-azure-ad?"></a>Quali prodotti partner di gestione di dispositivi mobili si integrano con Azure AD?
I prodotti dei fornitori seguenti si integrano con Azure AD per la registrazione unificata e l'accesso condizionale in Windows 10:

* AirWatch di VMware
* Citrix Xenmobile
* Lightspeed Mobile Manager
* Gestione di dispositivi mobili locale SOTI

### <a name="what-about-workplace-join-in-windows-10?"></a>Come funziona Aggiunta all'area di lavoro in Windows 10?
Aggiunta all'area di lavoro viene usato in Windows 8.1 per abilitare BYOD. In Windows 10 BYOD viene abilitato tramite l'opzione Aggiungi un account aziendale o dell'istituto di istruzione, come illustrato in precedenza. Per le organizzazioni che non integrano la gestione di dispositivi mobili con Azure AD, gli utenti possono registrare il dispositivo nella gestione manualmente con **Impostazioni** > **Account** > **Accesso società**.

### <a name="can-users-connect-their-microsoft-account-to-their-domain-account-in-windows-10?"></a>Gli utenti possono connettere l'account Microsoft all'account di dominio in Windows 10?
Non in Windows 10. In Windows 8.1 gli utenti dei dispositivi aggiunti a un dominio possono "connettere" l'account Microsoft, ad esempio Hotmail, Live, Outlook, Xbox e così via, all'account di dominio per abilitare alcune esperienze, ad esempio SSO per servizi Live, l'uso di Windows Store e il roaming delle impostazioni utente tra i dispositivi. In Windows 10 la funzionalità di connessione dell'account Microsoft è stata disattivata. L'utente può aggiungere uno o più account Microsoft come account aggiuntivi per abilitare la funzionalità SSO per i servizi consumer, ad esempio Windows Store. Questa operazione viene eseguita in **Impostazioni** > **Account** > **Il tuo account**.

L'account Microsoft connesso degli utenti che eseguono l'aggiornamento da dispositivi Windows 8.1 aggiunti a un dominio verrà aggiunto automaticamente all'elenco di account aggiuntivi usati.

## <a name="additional-information"></a>Informazioni aggiuntive
* [Windows 10 per le aziende: modalità d'uso dei dispositivi di lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)

<!--HONumber=Oct16_HO2-->


