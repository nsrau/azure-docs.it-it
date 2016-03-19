<properties
	pageTitle="Risolvere i problemi del Proxy dell’applicazione | Microsoft Azure"
	description="Illustra come risolvere gli errori nel Proxy applicazione di Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="kgremban"/>



# Risolvere i problemi del Proxy applicazione


> [AZURE.NOTE] Il Proxy applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).

Se si verificano errori durante l'accesso a un'applicazione pubblicata o durante la pubblicazione di applicazioni, controllare le opzioni seguenti per verificare se il Proxy applicazione di Microsoft Azure AD funziona correttamente:

- Aprire la console di Servizi Windows e verificare che il servizio relativo al connettore Proxy applicazione di Microsoft AAD sia abilitato e in esecuzione. È anche consigliabile vedere la pagina delle proprietà del servizio Proxy applicazione, come illustrato nell'immagine seguente:

  ![Schermata della finestra delle proprietà del connettore Proxy applicazione di Microsoft AAD](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)

- Aprire il Visualizzatore eventi e cercare gli eventi correlati al connettore Proxy applicazione disponibile in **Registri applicazioni e servizi** > **Microsoft** > **AadApplicationProxy** > **Connector** > **Admin**.
- Se necessario, sono disponibili log più dettagliati attivando i log di analisi e debug e il log della sessione del connettore del Proxy dell’applicazione.


## Errori generali

Tipi di errore | Descrizione | Risoluzione
--- | --- | ---
Non è possibile accedere a questa app aziendale. L'utente non è autorizzato ad accedere a questa applicazione. Autorizzazione non riuscita. Assicurarsi di assegnare all'utente l'accesso a questa applicazione. | L'utente potrebbe non essere stato assegnato per l'applicazione. | Passare alla scheda **Applicazione**, quindi in **Utenti e gruppi** assegnare l'utente o il gruppo di utenti all'applicazione.
Non è possibile accedere a questa app aziendale. L'utente non è autorizzato ad accedere a questa applicazione. Autorizzazione non riuscita. Accertarsi che l'utente abbia una licenza per Azure Active Directory Premium o Basic. | Questo errore potrebbe essere visualizzato dall'utente quando prova ad accedere all'app pubblicata, se non gli è stata esplicitamente assegnata una licenza Premium/Basic dall'amministratore del sottoscrittore. | Accedere alla scheda **Licenze** di Active Directory del sottoscrittore e verificare che all'utente o al gruppo di utenti sia stata assegnata una licenza Premium o Basic.


## Risoluzione dei problemi del connettore
Se durante l'installazione guidata del connettore la registrazione non riesce, è possibile visualizzare il motivo dell'errore esaminando il registro eventi in **Registri di Windows** > **Applicazione** o eseguendo il comando di Windows PowerShell seguente.

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

| Tipi di errore | Descrizione | Risoluzione |
| --- | --- | --- |
| La registrazione del connettore non è riuscita: assicurarsi di avere abilitato il Proxy applicazione nel portale di gestione di Azure e di avere immesso il nome utente e la password di Active Directory corretti. Errore: "Si sono verificati uno o più errori". | La finestra di registrazione è stata chiusa senza eseguire l'accesso ad Azure AD. | Eseguire di nuovo l'installazione guidata del connettore e registrarlo. |
| La registrazione del connettore non è riuscita: assicurarsi di avere abilitato il Proxy applicazione nel portale di gestione di Azure e di avere immesso il nome utente e la password di Active Directory corretti. Errore: "AADSTS50001: la risorsa `https://proxy.cloudwebappproxy.net/registerapp` è disabilitata". | Il Proxy applicazione è disabilitato. | Abilitare il proxy dell'applicazione nel portale di Azure classico prima di provare a registrare il connettore. Per altre informazioni su come abilitare il Proxy applicazione, vedere [Abilitare i servizi del Proxy applicazione](active-directory-application-proxy-enable.md). |
| La registrazione del connettore non è riuscita: assicurarsi di avere abilitato il Proxy applicazione nel portale di gestione di Azure e di avere immesso il nome utente e la password di Active Directory corretti. Errore: "Si sono verificati uno o più errori". | Se la finestra di registrazione si apre e poi si chiude immediatamente senza consentire all'utente di accedere, è probabile che venga visualizzato questo errore. L'errore si verifica quando vengono rilevati errori di rete nel sistema. | Assicurarsi che sia possibile connettersi da un browser a un sito Web pubblico e che le porte siano aperte come specificato in [Prerequisiti del Proxy dell’applicazione](active-directory-application-proxy-enable.md). |
| La registrazione del connettore non è riuscita: assicurarsi che il computer sia connesso a Internet. Errore: "Nessun endpoint in ascolto su `https://connector.msappproxy.net:9090/register/RegisterConnector` in grado di accettare il messaggio. Ciò è spesso causato da un'azione SOAP o un indirizzo non corretto. Per altri dettagli, vedere InnerException, se presente". | Se si accede usando il nome utente e la password di Azure AD e viene visualizzato questo errore, è possibile che tutte le porte con un valore superiore a 8081 siano bloccate. | Assicurarsi che tutte le porte necessarie siano aperte. Per altre informazioni, vedere [Prerequisiti del Proxy dell’applicazione](active-directory-application-proxy-enable.md). |
| Viene visualizzato un errore di cancellazione nella finestra di registrazione. Non è possibile continuare. È solo possibile chiudere la finestra. | È stato immesso un nome utente o una password non valida. | Riprovare. |
| La registrazione del connettore non è riuscita: assicurarsi di avere abilitato il Proxy applicazione nel portale di gestione di Azure e di avere immesso il nome utente e la password di Active Directory corretti. Errore: "AADSTS50059: non sono state trovate informazioni di identificazione del tenant nella richiesta o incluse in modo implicito nelle credenziali fornite e la ricerca in base all'URI dell'entità servizio non è riuscita". | Si sta provando ad accedere con un account Microsoft e non con un dominio che fa parte dell'ID organizzazione della directory a cui si vuole accedere. | Assicurarsi che l'amministratore faccia parte dello stesso nome di dominio del dominio tenant. Ad esempio, se il dominio di Azure AD è contoso.com, l'amministratore dovrà essere admin@contoso.com. |
| Non è possibile recuperare i criteri di esecuzione correnti per l'esecuzione degli script PowerShell. | Se l'installazione del connettore non riesce, verificare che il criterio di esecuzione di PowerShell non sia disabilitato. | Aprire l'Editor Criteri di gruppo. Passare a Configurazione computer > Modelli amministrativi > Componenti di Windows > Windows PowerShell e fare doppio clic su **Attiva l'esecuzione di script**. Questa opzione può essere impostata su **Non configurata** o **Attivata**. Se impostata su **Attivata**, assicurarsi che in Opzioni la voce Criteri di esecuzione sia impostata su **Consenti script locali e script remoti firmati** o su **Consenti tutti gli script**. |
| Non è stato possibile configurare il connettore. | Il certificato client del connettore usato per l'autenticazione è scaduto. Questo errore può essere visualizzato anche se l'installazione del connettore è protetta da un proxy. In questo caso il connettore non può accedere a Internet e non riuscirà a fornire applicazioni agli utenti remoti. | Rinnovare manualmente l'attendibilità con il cmdlet `Register-AppProxyConnector` in Windows PowerShell. Se il connettore è protetto da un proxy, è necessario concedere l'accesso a Internet agli account del connettore "servizi di rete" e "sistema locale". A questo scopo, è possibile concedere agli account l'accesso al proxy o impostarli perché ignorino il proxy. |
| La registrazione del connettore non è riuscita: per registrare il connettore, è necessario essere un amministratore globale di Active Directory. Errore: "La richiesta di registrazione è stata negata". | L'alias con cui si sta provando ad accedere non è un amministratore nel dominio. Il connettore viene sempre installato per la directory a cui appartiene il dominio dell'utente. | Assicurarsi che l'account amministratore con cui si sta provando ad accedere abbia le autorizzazioni globali per il tenant di Azure AD.|


## Errori di Kerberos

| Tipi di errore | Descrizione | Risoluzione |
| --- | --- | --- |
| Non è possibile recuperare i criteri di esecuzione correnti per l'esecuzione degli script PowerShell. | Se l'installazione del connettore non riesce, verificare che il criterio di esecuzione di PowerShell non sia disabilitato. | Aprire l'Editor Criteri di gruppo. Passare a Configurazione computer > Modelli amministrativi > Componenti di Windows > Windows PowerShell e fare doppio clic su **Attiva l'esecuzione di script**. Questa opzione può essere impostata su **Non configurata** o **Attivata**. Se impostata su **Attivata**, assicurarsi che in Opzioni la voce Criteri di esecuzione sia impostata su **Consenti script locali e script remoti firmati** o su **Consenti tutti gli script**. |
| 12008: Azure AD ha superato il numero massimo di tentativi di autenticazione Kerberos consentiti al server back-end. | Questo evento può indicare una configurazione non corretta tra Azure AD e il server back-end dell'applicazione oppure un problema di configurazione di data e ora su entrambi i computer. | Il server back-end ha rifiutato il ticket Kerberos creato da Azure AD. Verificare che la configurazione di Azure AD e del server back-end dell'applicazione siano corrette. Assicurarsi che la configurazione di data e ora in Azure AD e nel server back-end dell'applicazione siano sincronizzate. |
| 13016: Azure AD non riesce a recuperare un ticket Kerberos per conto dell'utente, perché non esiste alcun UPN nel token perimetrale o nel cookie di accesso. | Si è verificato un problema con la configurazione del servizio token di sicurezza. | Correggere la configurazione dell'attestazione UPN nel servizio token di sicurezza. |
| 13019: Azure AD non riesce a recuperare un ticket Kerberos per conto dell'utente a causa dell'errore generale dell'API seguente. | Questo evento può indicare una configurazione non corretta tra Azure AD e il server controller di dominio oppure un problema di configurazione di data e ora su entrambi i computer. | Il controller di dominio ha rifiutato il ticket Kerberos creato da Azure AD. Verificare che la configurazione di Azure AD e del server back-end dell'applicazione siano corrette, in particolare la configurazione del nome dell'entità servizio. Verificare che Azure AD sia aggiunto allo stesso dominio del controller di dominio, per assicurarsi che il controller di dominio stabilisca relazioni di trust con Azure AD. Assicurarsi che la configurazione di data e ora in Azure AD e nel controller di dominio dell'applicazione siano sincronizzate. |
| 13020: Azure AD non riesce a recuperare un ticket Kerberos per conto dell'utente, perché il nome dell'entità servizio del server back-end non è definito. | Questo evento può indicare una configurazione non corretta tra Azure AD e il server controller di dominio oppure un problema di configurazione di data e ora su entrambi i computer. | Il controller di dominio ha rifiutato il ticket Kerberos creato da Azure AD. Verificare che la configurazione di Azure AD e del server back-end dell'applicazione siano corrette, in particolare la configurazione del nome dell'entità servizio. Verificare che Azure AD sia aggiunto allo stesso dominio del controller di dominio, per assicurarsi che il controller di dominio stabilisca relazioni di trust con Azure AD. Assicurarsi che la configurazione di data e ora in Azure AD e nel controller di dominio dell'applicazione siano sincronizzate. |
| 13022: Azure AD non riesce ad autenticare l'utente perché il server back-end risponde ai tentativi di autenticazione Kerberos con un errore HTTP 401. | Questo evento può indicare una configurazione non corretta tra Azure AD e il server back-end dell'applicazione oppure un problema di configurazione di data e ora su entrambi i computer. | Il server back-end ha rifiutato il ticket Kerberos creato da Azure AD. Verificare che la configurazione di Azure AD e del server back-end dell'applicazione siano corrette. Assicurarsi che la configurazione di data e ora in Azure AD e nel server back-end dell'applicazione siano sincronizzate. |
| Il sito Web non riesce a visualizzare la pagina. | L'utente può visualizzare questo errore quando prova ad accedere all'app pubblicata, se l'applicazione è un'applicazione con autenticazione integrata di Windows e il nome dell'entità servizio definito per l'applicazione non è corretto. | Per le app con autenticazione integrata di Windows: assicurarsi che il nome dell'entità servizio configurato per l'applicazione sia corretto. |
| Il sito Web non riesce a visualizzare la pagina. | L'utente può visualizzare questo errore quando prova ad accedere all'app pubblicata, se l'applicazione è di tipo OWA e il problema può dipendere da uno dei motivi seguenti: <br> - Il nome dell'entità servizio dell'applicazione definito non è corretto. <br> - L'utente che ha provato ad accedere all'applicazione sta usando un account Microsoft invece dell'account aziendale appropriato oppure si tratta di un utente guest. <br> - L'utente che ha provato ad accedere all'applicazione non è definito correttamente per l'applicazione sul lato locale. | I passaggi per attenuare i problemi sono i seguenti: <br> - Assicurarsi che il nome dell'entità servizio configurato per l'applicazione sia corretto. <br> - Assicurarsi che l'utente acceda con il proprio account aziendale corrispondente al dominio dell'applicazione pubblicata. Gli utenti con account Microsoft o guest non possono accedere alle applicazioni con autenticazione integrata di Windows. <br> - Assicurarsi che l'utente abbia le autorizzazioni appropriate definite per questa applicazione back-end nel computer locale. |
| Non è possibile accedere a questa app aziendale. L'utente non è autorizzato ad accedere a questa applicazione. Autorizzazione non riuscita. Assicurarsi di assegnare all'utente l'accesso a questa applicazione. | L'utente può visualizzare questo errore quando prova ad accedere all'app pubblicata se sta usando un account Microsoft invece dell'account aziendale appropriato oppure è un utente guest. | Gli utenti con account Microsoft o guest non possono accedere alle applicazioni con autenticazione integrata di Windows. Assicurarsi che l'utente acceda con il proprio account aziendale corrispondente al dominio dell'applicazione pubblicata. |
| Non è possibile accedere a questa app aziendale in questo momento. Riprovare più tardi. Timeout del connettore. | L'utente può visualizzare questo errore quando prova ad accedere all'app pubblicata, se l'utente stesso non è definito correttamente per l'applicazione sul lato locale. | Assicurarsi che l'utente abbia le autorizzazioni appropriate definite per questa applicazione back-end nel computer locale. |


## Vedere anche
Si può fare molto di più con il proxy dell'applicazione:


- [Pubblicare le applicazioni con il proxy di applicazione](active-directory-application-proxy-publish.md)
- [Pubblicare applicazioni mediante il proprio nome di dominio](active-directory-application-proxy-custom-domains.md)
- [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)
- [Lavorare con applicazioni grado di riconoscere attestazioni](active-directory-application-proxy-claims-aware-apps.md)

## Ulteriori informazioni sul proxy dell’applicazione
- [Dare un'occhiata alla nostra Guida in linea](active-directory-application-proxy-enable.md)
- [Blog del proxy dell'applicazione](http://blogs.technet.com/b/applicationproxyblog/)
- [Guarda i nostri video su Channel 9!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Risorse aggiuntive
- [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
- [Iscriversi ad Azure come organizzazione](sign-up-organization.md)
- [Identità di Azure](fundamentals-identity.md)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png

<!---HONumber=AcomDC_0211_2016-->