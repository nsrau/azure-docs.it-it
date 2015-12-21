<properties
	pageTitle="Edizioni di Azure Active Directory | Microsoft Azure"
	description="Argomento che illustra la possibilità di scegliere tra edizioni gratuite e a pagamento di Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="MarkusVi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/04/2015"
	ms.author="markvi"/>

# Edizioni di Azure Active Directory

Tutti i servizi aziendali di Microsoft Online si basano su Azure Active Directory per le esigenze di gestione delle identità e degli accessi. Sottoscrivendo qualsiasi servizio aziendale di Microsoft Online (ad esempio Office 365, Microsoft Azure e così via), si ottiene una directory di Azure AD con accesso a tutte le funzionalità gratuite descritte di seguito.

Azure Active Directory è un servizio che offre funzionalità complete di gestione delle identità e degli accessi nel cloud per dipendenti, partner e clienti. Combina servizi directory, governance avanzata delle identità, una piattaforma avanzata basata su standard per gli sviluppatori e gestione degli accessi alle applicazioni per l'applicazione personalizzata o per una delle migliaia di applicazioni preintegrate. Con l'edizione Free di Azure Active Directory, è possibile gestire utenti e gruppi, eseguire la sincronizzazione con le directory locali, disporre dell'accesso Single Sign-On per Azure, Office 365 e numerose applicazioni SaaS note, ad esempio Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox e molte altre. Per altre informazioni su Azure Active Directory, vedere [Informazioni su Azure Active Directory](active-directory-whatis.md).


Per migliorare Azure Active Directory, è possibile aggiungere funzionalità a pagamento mediante le edizioni Basic e Premium di Azure Active Directory. Le edizioni a pagamento di Azure Active Directory, realizzate a partire dalla directory gratuita esistente, forniscono funzionalità aziendali avanzate che estendono alla forza lavoro mobile servizi come la gestione self-service, il monitoraggio avanzato, la creazione di report di sicurezza, Multi-Factor Authentication (MFA) e l'accesso sicuro.

Le sottoscrizioni di Office 365 includono funzionalità di Azure Active Directory aggiuntive, descritte nella tabella di confronto seguente.


> [AZURE.NOTE]Per le opzioni relative ai prezzi di queste edizioni, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). <br>Azure Active Directory Premium e Azure Active Directory Basic non sono attualmente supportate in Cina. Per altre informazioni, è possibile contattare Microsoft attraverso il forum di Azure Active Directory.


- **Azure Active Directory Basic**: progettata per gli utenti che svolgono attività basate sul cloud, questa edizione garantisce l'accesso alle applicazioni incentrate su cloud e soluzioni di gestione delle identità self-service. L'edizione Basic di Azure Active Directory offre funzionalità per il miglioramento della produttività e la riduzione dei costi, ad esempio la gestione degli accessi basata sui gruppi, la reimpostazione delle password self-service per le applicazioni cloud e il proxy dell'applicazione di Azure Active Directory (per pubblicare applicazioni Web locali usando Azure Active Directory), supportate da un contratto di servizio a livello aziendale che garantisce un tempo di attività del 99,9%.
 
- **Azure Active Directory Premium**: progettata per le organizzazioni con più complesse esigenze di gestione delle identità e degli accessi, l'edizione Premium di Azure Active Directory include capacità aggiuntive di gestione delle identità a livello aziendale con numerose funzionalità e consente agli utenti ibridi di accedere facilmente a funzionalità locali e cloud. Questa edizione include tutte le funzionalità necessarie per gli Information Worker e gli amministratori di identità in ambienti ibridi, tra cui accesso alle applicazioni, gestione delle identità e degli accessi self-service, protezione e sicurezza dell'identità nel cloud. Supporta risorse avanzate di amministrazione e delega come i gruppi dinamici e la gestione self-service dei gruppi. Include Microsoft Identity Manager, una suite locale di gestione delle identità e degli accessi, e offre soluzioni con funzionalità cloud di writeback come la reimpostazione self-service delle password per gli utenti locali.

Per iscriversi e iniziare a usare subito Active Directory Premium, vedere [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md).


> [AZURE.NOTE]Tramite le edizioni con pagamento a consumo sono disponibili diverse funzionalità di Azure Active Directory:
>
>- Active Directory B2C è la soluzione di gestione delle identità e degli accessi per le applicazioni rivolte ai consumatori. Per altre informazioni, vedere la pagina relativa ad [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
 
>-	Il servizio Azure Multi-Factor Authentication può essere usato tramite provider per utente o provider per autenticazione. Per altre informazioni, vedere [Informazioni su Azure Multi-Factor Authentication](multi-factor-authentication.md).


<br>

| Tipo di funzionalità| Funzionalità| Edizione gratuita| Edizione Basic| Edizione Premium| Solo app di Office 365 |
| --- | --- | --- | --- | --- | --- |
| **Funzionalità comuni**| Oggetti directory [1]| Fino a 500.000 oggetti| Nessun limite di oggetti| Nessun limite di oggetti| Nessun limite di oggetti per gli account utente di Office 365|
| | [Gestione di utenti e gruppi (aggiunta/aggiornamento/eliminazione), provisioning basato sull'utente](active-directory-administer.md), [registrazione dei dispositivi](active-directory-conditional-access-device-registration-overview.md)| ![Controllo][12]| ![Controllo][12]| ![Controllo][12]| ![Controllo][12]|
| | [App da SSO a SaaS, app personalizzate, app del proxy dell'applicazione](active-directory-enable-sso-scenario.md)| 10 app per utente [2]| 10 app per utente [2]| Nessun limite| 10 app per utente [2]|
| | [Modifica delle password self-service per gli utenti cloud](active-directory-passwords-update-your-own-password.md)| ![Controllo][12]| ![Controllo][12]| ![Controllo][12]| ![Controllo][12]|
| | [Connect: per la sincronizzazione tra le directory locali e Azure Active Directory](active-directory-aadconnect.md)| ![Controllo][12]| ![Controllo][12]| ![Controllo][12]| ![Controllo][12]|
| | **Anteprima**:[ collaborazione B2B](active-directory-b2b-collaboration-overview.md)| ![Controllo][12]| ![Controllo][12]| ![Controllo][12]| ![Controllo][12]|
| | [Report sull'utilizzo e la sicurezza](active-directory-view-access-usage-reports.md)| Report di base| Report di base| Report avanzati| Report di base|
| **Funzionalità Premium e Basic**| [Gestione e provisioning degli accessi alle applicazioni basati sui gruppi](active-directory-accessmanagement-group-saasapps.md)| | ![Controllo][12]| ![Controllo][12]| |
| | [Reimpostazione delle password self-service per gli utenti cloud](active-directory-passwords.md)| | ![Controllo][12]| ![Controllo][12]| ![Controllo][12]|
| | [Informazioni distintive dell'azienda (pagine di accesso/personalizzazione del panello di accesso)](active-directory-add-company-branding.md)| | ![Controllo][12]| ![Controllo][12]| ![Controllo][12]|
| | [Proxy dell'applicazione](active-directory-application-proxy-get-started.md)| | ![Controllo][12]| ![Controllo][12]| |
| | [Tempo di attività previsto dal contratto di servizio con disponibilità elevata (99,9%)](https://azure.microsoft.com/support/legal/sla/)| | ![Controllo][12]| ![Controllo][12]| ![Controllo][12]|
| **Funzionalità solo Premium**| Gestione self-service di gruppi, aggiunta self-service di applicazioni, appartenenza a gruppi dinamici| | | ![Controllo][12]| |
| | [Reimpostazione, modifica, sblocco con writeback in locale delle password in modalità self-service](active-directory-passwords-getting-started.md/#enable-users-to-reset-or-change-their-ad-passwords)| | | ![Controllo][12]| |
| | [Multi-Factor Authentication (cloud e in locale)](multi-factor-authentication.md)| | | ![Controllo][12]| Limitato al cloud solo per le app di Office 365|
| | [Licenze utente di Microsoft Identity Manager (MIM) e server MIM [3]](http://www.microsoft.com/server-cloud/products/microsoft-identity-manager/default.aspx)| | | ![Controllo][12]| |
| | [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)| | | ![Controllo][12]| |
| | [Collegamento di integrità di Active Directory di Azure](active-directory-aadconnect-health.md)| | | ![Controllo][12]| |
| | Rollover automatico delle password per account di gruppo| | | ![Controllo][12]| |
| | **Anteprima**: accesso condizionale| | | ![Controllo][12]| |
| | **Anteprima**: Privileged Identity Management| | | ![Controllo][12]| |
| **Funzionalità di Windows 10 relative all'aggiunta ad Azure Active Directory**| Aggiunta di un dispositivo Windows 10 ad Azure AD, Desktop SSO, Microsoft Passport per Azure AD, con ripristino Bitlocker per amministratori| ![Controllo][12]| ![Controllo][12]| ![Controllo][12]| ![Controllo][12]|
| | Iscrizione automatica MDM, ripristino Bitlocker self-service, amministratori locali aggiuntivi per dispositivi Windows 10 tramite aggiunta ad Azure AD| | | ![Controllo][12]| |





[1] La quota di utilizzo predefinita è 150.000 oggetti. Un oggetto è una voce del servizio directory ed è rappresentato dal relativo nome distinto univoco. Un esempio di oggetto è un'immissione utente a scopi di autenticazione. Se è necessario superare questa quota predefinita, contattare il supporto tecnico. Il limite di 500.000 oggetti non si applica a Office 365, Microsoft Intune o altri servizi online Microsoft a pagamento che usano Azure Active Directory per i servizi di directory.

[2] Con Azure AD Free e Azure AD Basic, gli utenti finali a cui è stato assegnato l'accesso alle app SaaS possono visualizzare fino a 10 app nel proprio pannello di accesso e possono ottenere l'accesso SSO a tali app. Con le sottoscrizioni Free e Basic gli amministratori possono configurare l'accesso SSO e assegnare agli utenti l'accesso a qualsiasi numero di app SaaS, ma gli utenti finali possono vedere un massimo di 10 app alla volta nel proprio pannello di accesso.

[3] Server di gestione delle identità Microsoft per i diritti di software che vengono concessi con licenza di Windows Server (qualsiasi edizione). Microsoft Identity Manager viene eseguito sul sistema operativo Windows Server. Di conseguenza, purché il server esegua una copia con licenza valida di Windows Server, Microsoft Identity Manager può essere installato e usato su tale server. Nessuna altra licenza separata è necessaria per il Server di gestione identità Microsoft.



## Passaggi successivi

- [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Aggiungere informazioni personalizzate distintive dell'azienda alle pagine Accedi e Pannello di accesso](active-directory-add-company-branding.md)
- [Visualizzare i report di accesso e utilizzo](active-directory-view-access-usage-reports.md)


<!--Image references-->
[12]: ./media/active-directory-editions/ic195031.png

<!---HONumber=AcomDC_1210_2015-->