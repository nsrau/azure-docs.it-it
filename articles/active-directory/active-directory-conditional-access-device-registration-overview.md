<properties
	pageTitle="Panoramica di Registrazione dispositivo di Azure Active Directory| Microsoft Azure"
	description="rappresenta il fondamento per gli scenari di accesso condizionale basato su dispositivo. Quando un dispositivo viene registrato, Registrazione dispositivo di Azure Active Directory effettua il provisioning del dispositivo con un'identità che viene usata per autenticare il dispositivo quando l'utente esegue l'accesso."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/02/2015"
	ms.author="femila"/>

# Panoramica di Registrazione dispositivo di Azure Active Directory

Registrazione dispositivo di Azure Active Directory rappresenta il fondamento per gli scenari di accesso condizionale basato su dispositivo. Quando un dispositivo viene registrato, Registrazione dispositivo di Azure Active Directory effettua il provisioning del dispositivo con un'identità che viene usata per autenticare il dispositivo quando l'utente esegue l'accesso. Il dispositivo autenticato e gli attributi del dispositivo possono quindi essere usati per imporre criteri di accesso condizionale per le applicazioni locali e ospitate nel cloud. Insieme a una soluzione di gestione dei dispositivi mobili come Intune, gli attributi del dispositivo in Azure Active Directory verranno aggiornati con informazioni aggiuntive sul dispositivo. Ciò permette di creare regole di accesso condizionale che subordinano l'accesso dai dispositivi al rispetto dei propri standard di sicurezza e conformità. Registrazione dispositivo di Azure Active Directory è disponibile in Azure Active Directory. Il servizio include il supporto per dispositivi Windows, iOS e Android. È possibile che i singoli scenari in cui viene usato il servizio Registrazione dispositivo di Azure Active Directory prevedano requisiti più specifici e il supporto di determinate piattaforme. Di seguito sono disponibili informazioni più dettagliate sugli scenari specifici attualmente disponibili.

## Scenari abilitati da Registrazione dispositivo di Azure Active Directory

Registrazione dispositivo di Azure Active Directory può essere considerato come base per un'ampia gamma di scenari. Il generale, il servizio include il supporto per dispositivi Windows, iOS e Android. È possibile che i singoli scenari in cui viene usato il servizio Registrazione dispositivo di Azure AD prevedano requisiti più specifici e il supporto di determinate piattaforme. Gli scenari sono i seguenti: Accesso condizionale alle applicazioni ospitate in locale: è possibile usare i dispositivi registrati con criteri di accesso per le applicazioni configurate per l'uso di AD FS con Windows Server 2012 R2. Per altre informazioni sulla configurazione dell'accesso condizionale in locale, vedere Configurazione dell'accesso condizionale locale usando il servizio Registrazione dispositivo di Azure Active Directory.

Accesso condizionale per le applicazioni di Office 365 con Microsoft Intune: gli amministratori IT possono effettuare il provisioning dei criteri di accesso condizionale dei dispositivi per proteggere le risorse aziendali, consentendo allo stesso tempo agli Information Worker che usano dispositivi conformi di accedere ai servizi. Per altre informazioni, vedere Criteri di accesso condizionale dei dispositivi per i servizi di Office 365.

##Configurazione della Registrazione dispositivo di Azure Active Directory

Per il servizio Registrazione dispositivo di Azure Active Directory sono disponibili le impostazioni seguenti: Abilitare Registrazione dispositivo di Azure Active Directory nel portale di Azure.

I dispositivi Windows trovano il servizio effettuando la ricerca di record DNS noti. È necessario configurare il DNS della società in modo che i dispositivi Windows 7 e Windows 8.1 possano trovare e usare il servizio.

È possibile visualizzare e abilitare/disabilitare i dispositivi registrati tramite il portale dell'amministratore in Azure Active Directory.

## Abilitare Registrazione dispositivo di Azure Active Directory
La sezione seguente illustra come abilitare il servizio Registrazione dispositivo di Azure Active Directory per la propria directory.
Per abilitare il servizio Registrazione dispositivo di Azure Active Directory
-------------------------------------------------------------
1. Accedere al Portale di Azure come Amministratore.
1. Nel riquadro sinistro selezionare **Active Directory**.
1. Selezionare la propria directory nella scheda **Directory**.
1. Selezionare la scheda **Configura**.
1. Scorrere fino alla sezione **Dispositivi**.
1. Selezionare **TUTTI ** per **GLI UTENTI POSSONO AGGIUNGERE DISPOSITIVI ALL'AREA DI LAVORO**.
1. Selezionare il numero massimo di dispositivi da autorizzare per l'utente.

>[AZURE.NOTE]La registrazione con Microsoft Intune o con Gestione dispositivi mobili per Office 365 richiede l'aggiunta all'area di lavoro. Se è stato configurato uno di questi servizi, sarà selezionata l'opzione TUTTI e il pulsante sarà disabilitato.


Per impostazione predefinita, l'autenticazione a due fattori non è abilitata per il servizio. L'autenticazione a due fattori è tuttavia consigliabile quando si registra un dispositivo.

* Prima di richiedere l'autenticazione a due fattori per questo servizio, è necessario configurare un provider di autenticazione a due fattori in Azure Active Directory e configurare gli account utente per Multi-Factor Authentication. Vedere Aggiunta di Multi-Factor Authentication ad Azure Active Directory.

* Se si usa AD FS con Windows Server 2012 R2, è necessario configurare un modulo di autenticazione a due fattori in AD FS. Vedere Uso di Multi-Factor Authentication con Active Directory Federation Services

## Configurare l'individuazione di Registrazione dispositivo di Azure Active Directory
I dispositivi client Windows 7 e Windows 8.1 individueranno il server di registrazione dispositivo combinando il nome dell'account utente con il nome di un server di registrazione dispositivo noto. È necessario creare un record DNS CNAME che punti al record A associato al servizio Registrazione dispositivo di Azure Active Directory. Il record CNAME deve usare il prefisso noto enterpriseregistration seguito dal suffisso UPN usato per gli account utente nell'organizzazione. Se l'organizzazione usa più suffissi UPN, è necessario creare più record CNAME nel DNS.

Ad esempio, se nell'organizzazione si usano due suffissi UPN denominati @contoso.com e @region.contoso.com, si creeranno i record DNS seguenti.
 
| Voce | Tipo | Indirizzo |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com | CNAME | enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com | CNAME | enterpriseregistration.windows.net |

## Visualizzare e gestire gli oggetti dispositivo di Azure Active Directory
1. Nel portale dell'amministratore di Azure è possibile visualizzare, bloccare e sbloccare i dispositivi. Un dispositivo bloccato non avrà più accesso alle applicazioni configurate per consentire solo i dispositivi registrati.
1. Accedere al portale di Microsoft Azure come Amministratore.
1. Nel riquadro sinistro selezionare **Active Directory**.
1. Selezionare la propria directory.
1. Selezionare la scheda **Utenti**, quindi selezionare un utente per visualizzare i relativi dispositivi.
1. Selezionare la scheda **Dispositivi**.
1. Selezionare **Dispositivi registrati** dal menu a discesa.
1. Qui è possibile visualizzare, bloccare o sbloccare i dispositivi registrati dell'utente. 

## Argomenti aggiuntivi

È possibile registrare i dispositivi Windows 7 e Windows 8.1 aggiunti a un dominio con Registrazione dispositivo di Azure AD. L'argomento seguente include altre informazioni sui prerequisiti e sulle procedure necessarie per configurare la registrazione del dispositivo per i dispositivi Windows 7 e Windows 8.1. Registrazione automatica dei dispositivi con Azure Active Directory per i dispositivi Windows aggiunti a un dominio

<!---HONumber=August15_HO9-->