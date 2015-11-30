<properties 
	pageTitle="Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory | Microsoft Azure" 
	description="Questo argomento descrive la funzionalità Aggiunta di Azure AD." 
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
	ms.date="07/30/2015" 
	ms.author="femila"/>

# Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory.

## Informazioni su Aggiunta ad Azure Active Directory 
Aggiunta ad Azure Active Directory (Azure AD) è la funzionalità che permette di registrare i dispositivi di proprietà di una società in Azure Active Directory per poterli gestire in modo centralizzato. Gli utenti (dipendenti o studenti) possono quindi connettersi al cloud aziendale tramite Azure Active Directory. Questa soluzione semplifica le distribuzioni di Windows semplificate e permette di accedere ad app e risorse aziendali da qualsiasi dispositivo Windows, sia di proprietà della società che di proprietà degli utenti (BYOD).

Aggiunta ad Azure AD è una funzionalità progettata per le aziende che hanno adottato l'approccio cloud-first/cloud-only. Si tratta in genere di piccole e medie imprese che non dispongono di un'infrastruttura Active Directory locale. Questa funzionalità potrà anche essere usata dalle aziende di grandi dimensioni sui dispositivi che non sono in grado di eseguire l'aggiunta a un dominio tradizionale, ad esempio i dispositivi mobili, oppure per gli utenti che devono accedere principalmente a Office 365 o ad altre app SaaS di Azure AD.

Anche se l'aggiunta a un dominio tradizionale offre sempre la migliore esperienza locale sui dispositivi che supportano questa funzionalità, l'aggiunta ad Azure AD è ideale per i dispositivi che invece non la supportano e nei casi in cui si intende gestire gli utenti nel cloud usando le funzionalità di gestione di dispositivi mobili (MDM) anziché le tradizionali esperienze di gestione dei domini tramite Criteri di gruppo e SCCM.

![](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)


## Vantaggi dell'adozione di Aggiunta ad Azure AD 

* **Se l'azienda opera prevalentemente nel cloud**: se è stato adottato un modello che prevede la riduzione del footprint in locale a fronte di una maggiore operatività nel cloud, l'aggiunta di Azure AD può risultare estremamente utile. Gli account Azure AD possono essere creati manualmente o tramite la sincronizzazione di Active Directory locale. In entrambi i casi, l'account Azure AD creato può essere usato per accedere a Windows 10. Gli utenti possono aggiungere i propri computer ad Azure AD tramite una procedura di Configurazione guidata o dalla pagina delle Impostazioni e usufruire dell'accesso Single Sign-On per accedere alle proprie risorse cloud, ad esempio Office 365, sia dal browser che dalle applicazioni Office. 
* **Istituti di istruzione**: gli istituti di istruzione hanno due tipi di utenti, i docenti e gli studenti. I docenti sono considerati membri dell'organizzazione a più lungo termine, quindi è consigliabile creare e assegnare loro account locali. Gli studenti invece sono membri dell'organizzazione a più breve termine e possono quindi essere gestiti in Azure AD, con un livello di directory esteso nel cloud anziché in locale. Gli studenti possono accedere a Windows usando l'account Azure AD e accedere alle risorse di Office 365 nelle applicazioni Office. 
* **Negozi**: ecco un altro settore in cui i clienti manifestano l'esigenza di una gestione più facile dei lavoratori stagionali. Anche in questo caso, è possibile creare account locali per i dipendenti a tempo pieno non stagionali che usano computer aggiunti a un dominio. I lavoratori stagionali sono invece membri dell'organizzazione a più breve termine e quindi è preferibile gestirli con licenze utente che possono essere spostate con maggiore facilità. La creazione di questi utenti nel cloud con licenze di Office 365 permette loro di ottenere i vantaggi dell'accesso alle applicazioni di Windows e Office con un account Azure AD usufruendo allo stesso tempo di una maggiore mobilità delle licenze al termine del rapporto di lavoro. 
* **Altre aziende**: oltre agli scenari specifici descritti sopra, pur avendo creato gli utenti nell'istanza di Active Directory locale, si potrebbe decidere di usare comunque la funzionalità di aggiunta ad Azure AD perché offre un'esperienza di aggiunta e di gestione dei dispositivi semplificata in Azure AD, la registrazione MDM automatica e l'accesso Single Sign-On alle risorse di Azure AD e locali.  

## Funzionalità di Aggiunta ad Azure AD
Aggiunta di Azure AD offre le funzionalità seguenti:

* **Provisioning self-service dei dispositivi di proprietà della società**: con Windows 10, gli utenti finali possono configurare un dispositivo con wrapping di riduzione completamente nuovo nella Configurazione guidata, senza rivolgersi al reparto IT.


* **Supporto per i fattori di forma moderni**: Aggiunta ad Azure AD funziona anche sui dispositivi che non includono le funzionalità di aggiunta a un dominio tradizionali.


* **Uso degli account aziendali esistenti**: gli utenti finali non devono più creare e mantenere un account Microsoft personale per ottenere l'esperienza ottimale sui dispositivi di proprietà dell'azienda, come accade con Windows 8. Possono usare l'account aziendale esistente in Azure AD. Per molte organizzazioni, questo significa configurare e accedere a Windows con le stesse credenziali usate per accedere a Office 365.


* **Registrazione MDM automatica**: i dispositivi vengono registrati automaticamente nella console di gestione quando sono connessi ad Azure AD. Questo funzionerà sia con Microsoft Intune che con il software MDM di terze parti. Se la gestione viene eseguita con Intune, il reparto IT potrà monitorare e gestire i dispositivi aggiunti ad Azure AD insieme ai dispositivi aggiunti a un dominio nella console di gestione di SCCM.


* **Accesso Single Sign-On alle risorse aziendali**: gli utenti possono usufruire dell'accesso Single Sign-On dal desktop di Windows alle risorse e alle app nel cloud, ad esempio Office 365 e migliaia di applicazioni aziendali che si basano su Azure AD per l'autenticazione tramite [Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md). I dispositivi di proprietà della società aggiunti ad Azure AD possono usare l'accesso Single Sign-On per accedere alle risorse aziendali quando il dispositivo si trova nella rete aziendale e per accedervi da qualsiasi luogo quando tali risorse sono esposte tramite il [proxy applicazione di Azure AD](https://msdn.microsoft.com/library/azure/Dn768219.aspx).


* **Roaming dello stato del sistema operativo**: elementi quali impostazioni di accessibilità, siti Web e password Wi-Fi verranno sincronizzati tra tutti i dispositivi di proprietà della società senza richiedere l'uso di un account Microsoft personale.


* **Windows Store Enteprise Ready**: Windows Store supporterà l'acquisizione e la gestione delle licenze delle app con gli account Azure AD. Le organizzazioni potranno acquistare le app con contratti multilicenza e renderle disponibili per tutti gli utenti dell'organizzazione.

## Differenze di funzionamento dei dispositivi con Aggiunta ad Azure AD

| Dispositivo aziendale (aggiunto a un dominio locale) | Dispositivo aziendale (aggiunto al cloud) | Dispositivo personale |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Gli utenti accedono a Windows con le credenziali dell'account aziendale (come già accade attualmente) | Gli utenti possono accedere a Windows con le credenziali dell'account aziendale gestite in Azure AD. Questo si applica ai dispositivi aziendali nei tre casi seguenti. L'organizzazione non dispone di Active Directory locale (ad esempio, piccola impresa). L'organizzazione non crea tutti gli account utente in Active Directory (ad esempio studenti, consulenti, lavoratori stagionali). Dispositivi aziendali che non possono essere aggiunti a un dominio (locale), ad esempio telefoni o tablet che eseguono uno SKU per dispositivi mobili. Ad esempio, un dispositivo secondario che viene usato in uno stabilimento o in un negozio. Funziona per le organizzazioni federate e gestite. | Gli utenti accedono a Windows usando le credenziali dell'account Microsoft (MSA) personale (invariato) |
| Gli utenti hanno accesso al roaming delle impostazioni e a Windows Store. Questi servizi funzionano con un account aziendale (non è necessario un account Microsoft personale). È necessario che l'organizzazione connetta Active Directory locale ad Azure AD | Configurazione self-service: gli utenti possono usare una procedura di configurazione iniziale guidata tramite l'account aziendale, in alternativa al provisioning del dispositivo effettuato dal reparto IT. Entrambi i metodi sono supportati | L'aggiunta di un account aziendale gestito in Active Directory o in Azure AD è estremamente semplice |
| Accesso Single Sign-On dal desktop ad app/siti Web/risorse aziendali locali e nel cloud che usano Azure AD per l'autenticazione | Registrazione automatica nella directory aziendale (Azure AD) e registrazione automatica in MDM. (funzionalità di Azure AD Premium) | Offre accesso Single Sign-On tramite l'account aziendale per tutte le app, i siti Web e le risorse |
| Gli utenti possono aggiungere il proprio account Microsoft personale per accedere ai propri file o immagini senza compromettere i dati aziendali (le impostazioni di roaming continuano a funzionare con l'account aziendale). L'account Microsoft personale consente l'accesso SSO e non gestisce più il roaming delle impostazioni | La reimpostazione delle password self-service durante l'esecuzione di winlogon (possibilità di reimpostare le password dimenticate). Questa funzionalità richiede Azure AD Premium | Fornisce l'accesso alla sezione dello Store dedicata alle app aziendali, in modo che gli utenti possano acquistare e usare app line-of-business sui propri dispositivi personali | |

## Informazioni aggiuntive
* [Windows 10 per le aziende: modalità d'uso dei dispositivi di lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticazione delle identità senza password con Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=Nov15_HO4-->