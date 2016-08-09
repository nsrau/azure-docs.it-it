Le organizzazioni usano più applicazioni [Software as a Service (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) per la produttività in quanto la tecnologia e gli strumenti cloud stanno diventando sempre più disponibili. Con l'aumento del numero delle applicazioni SaaS, diventa difficile per gli amministratori gestire gli account e i diritti di accesso e per gli utenti ricordare le diverse password. Gestire singolarmente queste applicazioni richiede un impegno aggiuntivo ed è meno sicuro.


- I dipendenti che devono tenere traccia di numerose password tendono a usare metodi meno sicuri per ricordarle, annotando le password o usando le stesse password per diversi account.

- Quando arriva un nuovo dipendente o un dipendente esistente lascia l'organizzazione, è necessario eseguire il provisioning o deprovisioning di tutti i relativi account singolarmente.

- Inoltre, i dipendenti possono iniziare a usare app SaaS per il proprio lavoro senza rivolgersi al reparto IT, creando i propri account in sistemi che gli amministratori IT non hanno approvato e non monitorano.

Una soluzione per tutti questi problemi è l'accesso Single Sign-On (SSO). Si tratta del modo più semplice per gestire più app e fornire agli utenti un'esperienza di accesso coerente. Azure Active Directory (Azure AD) fornisce un'efficiente soluzione SSO e dispone di molte applicazioni già integrate, con esercitazioni che consentono agli amministratori di impostare rapidamente una nuova applicazione e avviare il provisioning degli utenti.


## Come vengono integrate le app in Azure Active Directory?  

Azure AD consente di integrare le app e gli account di cui è stato eseguito il provisioning. Questa operazione può essere eseguita tramite due approcci.

- Se l'app è già integrata nella raccolta di app, è possibile usare questo portale impostare le app e configurare le impostazioni per consentire l'accesso SSO. Per qualsiasi app nella raccolta, è possibile iniziare seguendo le semplici istruzioni dettagliate presentate nella raccolta e nel portale di Azure per abilitare Single Sign-On.

- Se l'app non è presente nella raccolta, è comunque possibile impostare la maggior parte delle app in Azure AD come un'app personalizzata. Questa operazione richiede un livello leggermente superiore di esperienza tecnica per la configurazione. È possibile aggiungere qualunque applicazione che supporta SAML 2.0 come applicazione federata o qualunque applicazione che dispone di una pagina di accesso basata su HTML come applicazione Single Sign-On con password.

Nel caso gli utenti abbiano creato i propri account per app SaaS che non sono gestite dal reparto IT, lo strumento [Cloud App Discovery](../articles/active-directory/active-directory-cloudappdiscovery-whatis.md) offre una soluzione. Questo strumento monitora il traffico Web per identificare le app che vengono usate in tutta l'organizzazione e il numero di utenti che usano ognuna di esse. Il personale IT può usare queste informazioni per determinare quali sono le app preferite dagli utenti e decidere quali integrare in Azure AD per l'accesso SSO.

Quando si integra un'app in Azure AD, è possibile mappare le identità delle applicazioni stabilite dagli utenti alle rispettive identità di Azure AD.

<!---HONumber=AcomDC_0727_2016-->