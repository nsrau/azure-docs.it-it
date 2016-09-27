<properties
	pageTitle="Accedere a un insieme di credenziali delle chiavi protetto da firewall | Microsoft Azure"
	description="Informazioni su come accedere a un insieme di credenziali delle chiavi da un'applicazione protetta da firewall"
	services="key-vault"
	documentationCenter=""
	authors="amitbapat"
	manager="mbaldwin"
	tags="azure-resource-manager"/>  

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/13/2016"
	ms.author="ambapat"/>  

# Accesso a un insieme di credenziali delle chiavi protetto da firewall
### D: l'applicazione client dell'insieme di credenziali delle chiavi deve essere protetta da un firewall. Quali porte/host/indirizzi IP è necessario aprire per consentire l'accesso all'insieme di credenziali delle chiavi?

Per accedere a un insieme di credenziali delle chiavi, l'applicazione client dell'insieme di credenziali delle chiavi deve poter accedere a più endpoint per varie funzionalità.

- Autenticazione tramite Azure Active Directory
- Gestione dell'insieme di credenziali delle chiavi. Include la creazione, la lettura, l'aggiornamento e l'eliminazione e anche l'impostazione dei criteri di accesso tramite Azure Resource Manager.
- L'accesso agli oggetti (chiavi e segreti) archiviati nell'insieme di credenziali delle chiavi e la loro gestione vengono eseguiti tramite l'endpoint specifico dell'insieme di credenziali delle chiavi, ad esempio [https://nomecredenziali.vault.azure.net](https://yourvaultname.vault.azure.net).

Esistono alcune varianti a seconda della configurazione e dell'ambiente.

## Porte

Tutto il traffico verso l'insieme di credenziali delle chiavi per tutte le 3 funzioni (autenticazione, gestione e accesso al piano dati) passa per la porta HTTPS 443. Per CRL verrà tuttavia generato occasionalmente traffico HTTP (porta 80). I client che supportano OCSP non devono raggiungere CRL, ma possono in alcuni casi raggiungere [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).

## Autenticazione

L'applicazione client dell'insieme di credenziali delle chiavi dovrà accedere agli endpoint di Azure Active Directory per l'autenticazione. L'endpoint usato dipende dalla configurazione del tenant AAD, dal tipo di entità (entità utente, entità servizio) e dal tipo di account, ad esempio account Microsoft o ID organizzazione.

| Tipo di entità | Endpoint:porta |
|----------------|---------------|
| Utente che usa l'account Microsoft,<br> ad esempio user@hotmail.com | **Globale:**<br> login.microsoftonline.com:443<br><br> **Azure per la Cina:**<br> login.chinacloudapi.cn:443<br><br>**Azure per il governo degli Stati Uniti:**<br> login-us.microsoftonline.com:443<br><br>**Azure per la Germania:**<br> login.microsoftonline.de:443<br><br> e <br>login.live.com:443 |
| Entità utente/servizio che usa l'ID organizzazione con AAD, ad esempio user@contoso.com | **Globale:**<br> login.microsoftonline.com:443<br><br> **Azure per la Cina:**<br> login.chinacloudapi.cn:443<br><br>**Azure per il governo degli Stati Uniti:**<br> login-us.microsoftonline.com:443<br><br>**Azure per la Germania:**<br> login.microsoftonline.de:443 |
| Entità utente/servizio che usa l'ID organizzazione + ADFS o altri endpoint federati, ad esempio user@contoso.com | Tutti gli endpoint precedenti per l'ID organizzazione più ADFS o altri endpoint federati |

Esistono altri possibili scenari complessi. Vedere [Scenari di autenticazione per Azure AD](/documentation/articles/active-directory-authentication-scenarios/), [Integrazione di applicazioni con Azure Active Directory](/documentation/articles/active-directory-integrating-applications/) e [Guida per gli sviluppatori di Azure Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx) per altre informazioni.

## Gestione dell'insieme di credenziali delle chiavi

Per la gestione dell'insieme di credenziali delle chiavi (CRUD e impostazione dei criteri di accesso), l'applicazione client dell'insieme di credenziali delle chiavi deve accedere all'endpoint di Azure Resource Manager.

| Tipo di operazione | Endpoint:porta |
|----------------|---------------|
| Operazioni del piano di controllo dell'insieme di credenziali delle chiavi<br> tramite Azure Resource Manager | **Globale:**<br> management.azure.com:443<br><br> **Azure per la Cina:**<br> management.chinacloudapi.cn:443<br><br> **Azure per il governo degli Stati Uniti:**<br> management.usgovcloudapi.net:443<br><br> **Azure per la Germania:**<br> management.microsoftazure.de:443 |
| API Graph di Azure Active Directory | **Globale:**<br> graph.windows.net:443<br><br> **Azure per la Cina:**<br> graph.chinacloudapi.cn:443<br><br> **Azure per il governo degli Stati Uniti:**<br> graph.windows.net:443<br><br> **Azure per la Germania:**<br> graph.cloudapi.de:443 |

## Operazioni dell'insieme di credenziali delle chiavi

Per tutte le operazioni di gestione e crittografia degli oggetti (chiavi e segreti) dell'insieme di credenziali delle chiavi, il client dell'insieme di credenziali delle chiavi deve accedere all'endpoint dell'insieme stesso. Il suffisso DNS dell'endpoint varia a seconda della posizione dell'insieme di credenziali delle chiavi. L'endpoint dell'insieme di credenziali delle chiavi è nel formato: <nome-insieme>.<suffisso-dns-area> come descritto nella tabella seguente.

| Tipo di operazione | Endpoint:porta |
|----------------|---------------|
| Operazioni dell'insieme di credenziali delle chiavi come le operazioni di crittografia sulle chiavi, la creazione, la lettura, l'aggiornamento e l'eliminazione di chiavi e segreti, l'impostazione e il recupero di tag e altri attributi per gli oggetti dell'insieme di credenziali delle chiavi (chiavi/segreti) | **Globale:**<br> &lt;nome-insieme-credenziali&gt;.vault.azure.net:443<br><br> **Azure per la Cina:**<br> &lt;nome-insieme-credenziali&gt;.vault.azure.cn:443<br><br> **Azure per il governo degli Stati Uniti:**<br> &lt;nome-insieme-credenziali&gt;.vault.usgovcloudapi.net:443<br><br> **Azure per la Germania:**<br> &lt;nome-insieme-credenziali&gt;.vault.microsoftazure.de:443 |

## Intervalli di indirizzi IP

Il servizio dell'insieme di credenziali delle chiavi usa a sua volta altre risorse di Azure come l'infrastruttura PaaS, quindi non è possibile indicare un intervallo specifico di indirizzi IP usati dagli endpoint del servizio dell'insieme di credenziali delle chiavi in un momento qualsiasi. Se tuttavia il firewall supporta solo gli intervalli di indirizzi IP, vedere il documento [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (Intervalli IP del data center di Azure). Per l'autenticazione e l'identità (Azure Active Directory), l'applicazione deve potersi connettere agli endpoint descritti in [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## Passaggi successivi

- In caso di domande sull'insieme di credenziali delle chiavi, visitare i [forum sull'insieme di credenziali delle chiavi di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

<!---HONumber=AcomDC_0921_2016-->