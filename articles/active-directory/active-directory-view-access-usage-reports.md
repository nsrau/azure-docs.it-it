<properties
	pageTitle="Visualizzare i report di accesso e utilizzo | Microsoft Azure"
	description="Illustra come visualizzare i report di accesso e utilizzo per ottenere informazioni sull'integrità e sicurezza della directory dell'organizzazione."
	services="active-directory"
	documentationCenter=""
	authors="dhanyahk"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="dhanyahk"/>


# Visualizzare i report di accesso e utilizzo

*Questo documento fa parte della [guida alla creazione di report in Azure Active Directory](active-directory-reporting-guide.md).*

È possibile usare i report di utilizzo e accesso di Azure Active Directory per ottenere informazioni sull'integrità e sicurezza della directory dell'organizzazione. Con queste informazioni un amministratore di directory può stabilire meglio dove potrebbero esserci possibili rischi per la sicurezza in modo da poterne pianificare adeguatamente la riduzione.

Nel portale di gestione di Azure i report sono suddivisi nei seguenti modi:

- Report anomalie: contiene eventi di accesso individuati come anomali. L'obiettivo è rendere gli utenti consapevoli di tale attività e consentire loro di essere in grado di stabilire se un evento è sospetto.
- Report applicazioni integrate: fornisce informazioni dettagliate sull'uso delle applicazioni cloud nell'organizzazione. Azure Active Directory offre l'integrazione con migliaia di applicazioni cloud.
- Report di errori: indica errori che possono verificarsi durante il provisioning di account ad applicazioni esterne.
- Report specifici degli utenti: visualizza i dati del dispositivo/dell'attività di accesso per un utente specifico.
- Log attività: contengono un record di tutti gli eventi controllati nelle ultime 24 ore, negli ultimi 7 giorni o negli ultimi 30 giorni, nonché le modifiche delle attività di gruppo e le attività di registrazione e di reimpostazione password.

> [AZURE.NOTE]
>
- Alcuni report di utilizzo di anomalie e risorse avanzati sono disponibili solo quando si abilita [Azure Active Directory Premium](active-directory-get-started-premium.md). I report avanzati consentono di migliorare la sicurezza dell'accesso, di rispondere a potenziali rischi e ottenere l'accesso alle analisi sull'accesso al dispositivo e l'utilizzo delle applicazioni.
- Azure Active Directory Premium e le edizioni Basic sono disponibili per i clienti in Cina usando l'istanza a livello mondiale di Azure Active Directory. Azure Active Directory Premium e le edizioni Basic non sono attualmente supportati nel servizio di Microsoft Azure gestito da 21Vianet in Cina. Per altre informazioni, è possibile contattare Microsoft attraverso il [forum di Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## Report

|	Report |	Descrizione |
|	------												|	-----																						|
|	**Report Anomalie dell'attività**
|	[Accessi da origini sconosciute](active-directory-reporting-sign-ins-from-unknown-sources.md) |	Può indicare un tentativo di accedere senza tracciamento. |
|	[Accessi dopo più errori](active-directory-reporting-sign-ins-after-multiple-failures.md) |	Può indicare un attacco di forza bruta riuscito. |
|	[Accessi da più aree geografiche](active-directory-reporting-sign-ins-from-multiple-geographies.md) |	Può indicare che più utenti stanno accedendo con lo stesso account. |
|	[Accessi da indirizzi IP con attività sospetta](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md) |	Può indicare un accesso riuscito in seguito a un prolungato tentativo di intrusione. |
|	[Accessi da dispositivi potenzialmente infetti](active-directory-reporting-sign-ins-from-possibly-infected-devices.md) |	Può indicare un tentativo di accesso da dispositivi probabilmente infetti. |
|	[Attività di accesso irregolare](active-directory-reporting-irregular-sign-in-activity.md) |	Può indicare eventi anomali nei modelli di utilizzo dell'accesso da parte degli utenti. |
|	[Utenti con anomalie dell'attività di accesso](active-directory-reporting-users-with-anomalous-sign-in-activity.md) |	Indica utenti i cui account sono stati compromessi. |
|	Utenti con credenziali perse |	Utenti con credenziali perse |
|	**Log attività**
|	Report di controllo |	Eventi controllati nella directory |
|	Attività di reimpostazione password |	Fornisce una visualizzazione dettagliata delle reimpostazioni della password all'interno dell'organizzazione. |
|	Attività di registrazione reimpostazione password |	Fornisce una visualizzazione dettagliata delle registrazioni di reimpostazione della password all'interno dell'organizzazione. |
|	Attività dei gruppi self-service |	Fornisce un log attività per tutte le attività self-service del gruppo nella directory |
|	**Applicazioni integrate**
|	Utilizzo applicazioni |	Fornisce un riepilogo di utilizzo per tutte le applicazioni SaaS integrate con la directory. |
|	Attività di provisioning dell'account |	Fornisce una cronologia dei tentativi di provisioning degli account nelle applicazioni esterne. |
|	Stato rollover della password |	Fornisce una panoramica dettagliata dello stato di rollover automatico delle password di applicazioni SaaS. |
|	Errori di provisioning dell'account |	Indica un impatto sull'accesso degli utenti alle applicazioni esterne. |
|	**Rights Management**
|	Utilizzo di RMS |	Fornisce un riepilogo dell'utilizzo di Rights Management |
|	Utenti RMS più attivi |	Visualizza l'elenco dei primi 1000 utenti attivi che hanno eseguito l'accesso ai file protetti con RMS |
|	Utilizzo dispositivi RMS |	Visualizza l'elenco dei dispositivi usati per accedere ai file protetti con RMS |
|	Utilizzo applicazioni abilitate per RMS |	Fornisce l'utilizzo delle applicazioni abilitate per RMS |

## Edizioni dei report

|	Report |	Free |	Basic |	Premium |
|	------												|	----	|	-----	|	--------	|
|	**Report Anomalie dell'attività**
|	Accessi da origini sconosciute |	✓ |	✓ |	✓ |
|	Accessi dopo più errori |	✓ |	✓ |	✓ |
|	Accessi da più aree geografiche |	✓ |	✓ |	✓ |
|	Accessi da indirizzi IP con attività sospetta | | |	✓ |
|	Accessi da dispositivi potenzialmente infetti | | |	✓ |
|	Attività di accesso irregolare | | |	✓ |
|	Utenti con anomalie dell'attività di accesso | | |	✓ |
|	Utenti con credenziali perse | | |	✓ |
|	**Log attività**
|	Report di controllo |	✓ |	✓ |	✓ |
|	Attività di reimpostazione password | | |	✓ |
|	Attività di registrazione reimpostazione password | | |	✓ |
|	Attività dei gruppi self-service | | |	✓ |
|	**Applicazioni integrate**
|	Utilizzo applicazioni | | |	✓ |
|	Attività di provisioning dell'account |	✓ |	✓ |	✓ |
|	Stato rollover della password | | |	✓ |
|	Errori di provisioning dell'account |	✓ |	✓ |	✓ |
|	**Rights Management**
|	Utilizzo di RMS | | |	Solo RMS |
|	Utenti RMS più attivi | | |	Solo RMS |
|	Utilizzo dispositivi RMS | | |	Solo RMS |
|	Utilizzo applicazioni abilitate per RMS | | |	Solo RMS |



## Report Anomalie dell'attività
<p>I report relativi ad attività di acceso anomale segnalano le attività di accesso sospette ad Office 365, al portale di gestione di Azure, al pannello di accesso di Azure AD, a Office 365, a SharePoint Online, a Dynamics CRM Online e ad altri servizi online Microsoft.</p>
<p>Tutti questi report, ad eccezione del report "Accessi dopo errori multipli", segnalano inoltre gli accessi <i>federati</i> ai servizi menzionati in precedenza, indipendentemente dal provider di federazione. </p>
<p>Sono disponibili i seguenti report: </p><ul>
<li>[Accessi da origini sconosciute](active-directory-reporting-sign-ins-from-unknown-sources.md).</li>
<li>[Accessi dopo più errori](active-directory-reporting-sign-ins-after-multiple-failures.md).</li>
<li>[Accessi da più aree geografiche](active-directory-reporting-sign-ins-after-multiple-failures.md).</li>
<li>[Accessi da indirizzi IP con attività sospetta](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md).</li>
<li>[Attività di accesso irregolare](active-directory-reporting-irregular-sign-in-activity.md).</li>
<li>[Accessi da dispositivi probabilmente infetti](active-directory-reporting-sign-ins-from-possibly-infected-devices.md).</li>
<li>[Utenti con anomalie dell'attività di accesso](active-directory-reporting-users-with-anomalous-sign-in-activity.md).</li>
<li>Utenti con credenziali perse</li></ul>










## Log attività

### Report di controllo

| Descrizione | Percorso report |
| :-------------     | :-------        |
| Mostra un record di tutti gli eventi controllati entro le ultime 24 ore, gli ultimi 7 giorni o gli ultimi 30 giorni. <br /> Per altre informazioni, vedere [Eventi del report di controllo di Azure Active Directory](active-directory-reporting-audit-events.md) | Scheda Directory > Report |

![Report di controllo](./media/active-directory-view-access-usage-reports/auditReport.PNG)

### Attività di reimpostazione password

| Descrizione | Percorso report |
| :-------------     | :-------        |
| Vengono illustrati tutti i tentativi di reimpostazione della password che si sono verificati nell'organizzazione. | Scheda Directory > Report |

![Attività di reimpostazione password](./media/active-directory-view-access-usage-reports/passwordResetActivity.PNG)

### Attività di registrazione reimpostazione password

| Descrizione | Percorso report |
| :-------------     | :-------        |
| Vengono illustrate tutte le registrazioni per la reimpostazione della password che si sono verificate nell'organizzazione | Scheda Directory > Report |

![Attività di registrazione reimpostazione password](./media/active-directory-view-access-usage-reports/passwordResetRegistrationActivity.PNG)

### Attività dei gruppi self-service

| Descrizione | Percorso report |
| :-------------     | :-------        |
| Mostra tutte le attività per i gruppi gestiti in modalità self-service nella directory. | Scheda Directory > Utenti > <i>Utente</i> > Dispositivi |

![Attività dei gruppi self-service](./media/active-directory-view-access-usage-reports/selfServiceGroupsActivity.PNG)











## Report Applicazioni integrate

### Utilizzo dell'applicazione: riepilogo

| Descrizione | Percorso report |
| :-------------     | :-------        |
| Usare questo report per visualizzare l'utilizzo di tutte le applicazioni SaaS presenti nella directory. Questo report si basa sul numero di selezioni dell'applicazione eseguite nel Pannello di accesso. | Scheda Directory > Report |

Questo report include gli accessi a _tutte_ le applicazioni a cui ha accesso la directory, comprese le applicazioni Microsoft pre-integrate.

Le applicazioni Microsoft preintegrate includono Office 365, SharePoint, il portale di gestione di Azure e altre.

![Riepilogo utilizzo applicazione](./media/active-directory-view-access-usage-reports/applicationUsage.PNG)


### Utilizzo dell'applicazione: dettagliato

| Descrizione | Percorso report |
| :-------------     | :-------        |
| Usare questo report per visualizzare l'uso di una specifica applicazione SaaS. Questo report si basa sul numero di selezioni dell'applicazione eseguite nel Pannello di accesso. | Scheda Directory > Report |

### Dashboard dell'applicazione

| Descrizione | Percorso report |
| :-------------     | :-------        |
| Questo report indica il numero cumulativo di accessi all'applicazione dagli utenti dell'organizzazione in un intervallo di tempo selezionato. Il grafico nella pagina del dashboard facilita l'identificazione delle tendenze di tutti gli utilizzi dell'applicazione. | Scheda Directory > Applicazione > Dashboard |

## Report di errori

### Errori di provisioning dell'account

| Descrizione | Percorso report |
| :-------------     | :-------        |
| Usare questo report per monitorare gli errori che si verificano durante la sincronizzazione degli account da applicazioni SaaS ad Azure Active Directory. | Scheda Directory > Report |

![Errori di provisioning dell'account](./media/active-directory-view-access-usage-reports/accountProvisioningErrors.PNG)









## Report specifici degli utenti

### Dispositivi

| Descrizione | Percorso report |
| :-------------     | :-------        |
| Usare questo report per visualizzare l'indirizzo IP e la posizione geografica di dispositivi usato da un utente specifico per accedere ad Azure Active Directory. | Scheda Directory > Utenti > <i>Utente</i> > Dispositivi |

### Attività

| Descrizione | Percorso report |
| :-------------     | :-------        |
| Mostra l'attività di accesso per un utente. Il report include diverse informazioni, tra cui l'applicazione a cui è stato effettuato l'accesso, il dispositivo usato, l'indirizzo IP e la posizione. Microsoft non raccoglie informazioni sulla cronologia degli utenti che accedono con un account Microsoft. | Scheda Directory > Utenti > <i>Utente</i> > Attività |

#### Gli eventi di accesso inclusi nel report delle attività utente

Solo determinati tipi di eventi di accesso verranno visualizzati nel report delle attività utente.

| Tipo evento | Incluso |
| ----------------------								| ---------		|
| Accessi al [Pannello di accesso](http://myapps.microsoft.com/) | Sì |
| Accessi al [portale di gestione di Azure](https://manage.windowsazure.com/) | Sì |
| Accessi al [portale di Microsoft Azure](https://portal.azure.com/) | Sì |
| Accessi per il [portale di Office 365](http://portal.office.com/) | Sì |
| Accessi a un'applicazione nativa, ad esempio Outlook (vedere l'eccezione riportata di seguito) | Sì |
| Accessi a un'app federativa/con provisioning tramite il pannello di accesso, come Salesforce | Sì |
| Accessi a un'app basata su password tramite il pannello di accesso, come Twitter | Sì |
| Accessi a un'app di business personalizzata che è stata aggiunta alla directory | No (disponibile a breve) |
| Accessi a un'app del proxy dell'applicazione di Azure AD che è stata aggiunta alla directory | No (disponibile a breve) |

> Nota: per ridurre la quantità di disturbo in questo report, non vengono visualizzati gli accessi all'[Assistente per l'accesso ai Microsoft Online Services](http://community.office365.com/it-IT/w/sso/534.aspx).










## Aspetti da considerare quando si sospetta la violazione della sicurezza

Se si sospetta che un account utente potrebbe essere compromesso o qualsiasi tipo di attività utente sospetta potrebbe causare una violazione della protezione dei dati della directory nel cloud, si consiglia una o più delle seguenti operazioni:

- Contattare l'utente per verificare l'attività
- Reimpostare la password dell'utente
- [Abilitare l'autenticazione a più fattori](../multi-factor-authentication/multi-factor-authentication-get-started.md) per una maggiore sicurezza

## Visualizzare o scaricare un report

1. Nel portale di Azure classico, fare clic su **Active Directory**, poi sul nome della directory dell'organizzazione e quindi su **Report**.
2. Nella pagina Report, fare clic sul report che si desidera visualizzare e/o scaricare.

    > [AZURE.NOTE] Se questa è la prima volta che si usa la funzionalità di creazione di report di Azure Active Directory, verrà visualizzato un messaggio relativo al consenso esplicito. Se si accetta, fare clic sull'icona del segno di spunta per continuare.

3. Fare clic sul menu a discesa accanto a Intervallo e quindi selezionare uno degli intervalli di tempo che devono essere usati durante la generazione di questo report:
    - Ultime 24 ore
    - Ultimi 7 giorni
    - Ultimi 30 giorni
4. Fare clic sull'icona del segno di spunta per eseguire il report.
	- Nel portale di Azure classico vengono visualizzati fino a 1000 eventi.
5. Se applicabile, fare clic su **Scarica** per scaricare il report in un file compresso in formato valori separati da virgole (CSV) per visualizzarlo offline o a scopo di archiviazione.
	- Fino a 75.000 eventi verranno inclusi nel file scaricato.
	- Per altri dati, vedere [Introduzione all'API di creazione report](active-directory-reporting-api-getting-started.md).

## Ignorare un evento

Se si stanno visualizzando tutti i report anomalie, si noterà che è possibile ignorare vari eventi che vengono visualizzati nei report correlati. Per ignorare un evento, evidenziarlo nel report e quindi fare clic su **Ignora**. Il pulsante **Ignora** rimuoverà definitivamente l'evento evidenziato dal report e può essere usato solo dagli amministratori globali autorizzati.

## Notifiche automatiche tramite posta elettronica

Per altre informazioni sulle notifiche della funzionalità di creazione di report di Azure AD, vedere [Notifiche relative alla funzionalità di creazione di report di Azure Active Directory](active-directory-reporting-notifications.md).

## Passaggi successivi

- [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Aggiungere informazioni personalizzate distintive dell'azienda alle pagine Accedi e Pannello di accesso](active-directory-add-company-branding.md)

<!---HONumber=AcomDC_0928_2016-->