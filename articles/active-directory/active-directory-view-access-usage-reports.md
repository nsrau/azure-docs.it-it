<properties 
	pageTitle="Visualizzare i report di accesso e utilizzo" 
	description="Un argomento che illustra come visualizzare i report di accesso e utilizzo per ottenere informazioni sull'integrità e sicurezza della directory dell'organizzazione." 
	services="active-directory" 
	documentationCenter="" 
	authors="kenhoff" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/10/2015" 
	ms.author="kenhoff;Justinha"/>

# Visualizzare i report di accesso e utilizzo

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
- Azure Active Directory Premium e le edizioni Basic sono disponibili per i clienti in Cina usando l'istanza a livello mondiale di Azure Active Directory. Azure Active Directory Premium e le edizioni Basic non sono attualmente supportati nel servizio di Microsoft Azure gestito da 21Vianet in Cina. Per altre informazioni, è possibile contattare Microsoft attraverso il [forum di Azure Active Directory](http://feedback.azure.com/forums/169401-azure-active-directory).


## Report anomalie

Nome report | Disponibile in questa edizione    	
------------- | -------------  
[Accessi da origini sconosciute](#sign-ins-from-unknown-sources) | Gratuito e Premium
[Accessi dopo più errori](#sign-ins-after-multiple-failures) | Gratuito e Premium
[Accessi da più aree geografiche](sign-ins-from-multiple-geographies) | Gratuito e Premium
[Accessi da indirizzi IP con attività sospetta](#sign-ins-from-ip-addresses-with-suspicious-activity) | Premium
[Attività di accesso con anomalie](#anamolous-sign-in-activity) | Premium
[Accessi da dispositivi potenzialmente infetti](#sign-ins-from-possibly-infected-devices) | Premium
[Utenti con anomalie dell'attività di accesso](#users-with-anomalous-sign-in-activity) | Premium
[Utilizzo dell'applicazione: riepilogo](#application-usage-summary) | Premium
[Utilizzo dell'applicazione: dettagliato](#application-usage-detailed) | Premium
[Dashboard dell'applicazione](#application-dashboard) | Gratuito e Premium
[Errori di provisioning dell'account](#account-provisioning-errors) | Gratuito e Premium
[Dispositivi](#devices) | Premium
[Attività](#activity) | Gratuito e Premium
[Report di controllo](#audit-report) | Gratuito e Premium
[Report attività dei gruppi](#groups-activity-report) | Premium
[Report attività di registrazione per la reimpostazione password](#password-reset-registration-activity-report) | Premium
[Attività di reimpostazione password](#password-reset-activity) | Premium
 

### Accessi da origini sconosciute

| Descrizione | Percorso report |
| :-------------     | :-------        |
| <p>Questo report indica gli utenti che hanno effettuato correttamente l'accesso alla directory mentre gli era assegnato un indirizzo IP riconosciuto da Microsoft come un indirizzo IP proxy anonimo. Questi proxy vengono spesso usati dagli utenti che vogliono nascondere l'indirizzo IP del computer e possono essere usati per attacchi dannosi, talvolta pirati informatici usano questi proxy. </p><p> I risultati del report mostreranno il numero di volte in cui un utente ha effettuato correttamente l'accesso alla directory da quell'indirizzo e l'indirizzo IP del proxy.</p> | Scheda Directory > Report |

### Accessi dopo più errori

| Descrizione | Percorso report |
| :-------------     | :-------        |
| Questo report indica gli utenti che hanno eseguito correttamente l'accesso dopo più tentativi di accesso consecutivi non riusciti. Le possibili cause includono: <ul><li>L'utente ha dimenticato la password</li><li>L'utente è vittima di un attacco di forza bruta che ha portato all'individuazione della password</li></ul><p>Nei risultati del report verranno mostrati il numero di accesso consecutivi non riusciti eseguiti prima dell'accesso riuscito e un timestamp associato al primo accesso riuscito.</p><p><b>Impostazioni report</b>: è possibile configurare il numero minimo di tentativi di accesso non riusciti consecutivi eseguiti prima della visualizzazione del report. Quando si apportano modifiche a questa impostazione si noti che le modifiche non si applicano a eventuali accessi non riusciti attualmente visualizzati nel report. Verranno tuttavia applicati a tutti gli accessi successivi. Le modifiche a questo report possono essere apportate solo dagli amministratori autorizzati. | Scheda Directory > Report |

### Accessi da più aree geografiche

| Descrizione | Percorso report |
| :-------------     | :-------        |
| <p>Questo report include attività di accesso completate da un utente laddove due accessi sembrano provenire da diverse aree, ma dato il tempo intercorso tra gli accessi non è possibile che l'utente si sia spostato da un'area all'altra. Le possibili cause includono:</p><ul><li>L'utente sta condividendo la password</li><li>L'utente usa un desktop remoto per avviare un Web browser per l'accesso</li><li>Un pirata informatico ha effettuato l'accesso all'account di un utente da un altro paese.</li></ul><p>Nei risultati del report verranno mostrati gli eventi di accesso riuscito, nonché il tempo tra gli accessi, le aree da cui sembrano provenire gli accessi e il tempo di spostamento stimato tra tali aree.</p><p>Il tempo di spostamento mostrato è solo una stima e può essere diverso da quello effettivo necessario per spostarsi tra le posizioni. Non viene inoltre generato alcun evento per gli accessi tra aree adiacenti.</p> | Scheda Directory > Report |

### Accessi da indirizzi IP con attività sospetta

| Descrizione | Percorso report |
| :-------------     | :-------        |
| <p>Questo report include tentativi di accesso eseguiti da indirizzi IP in cui è stata notata un'attività sospetta. Un'attività sospetta include molti tentativi di accesso non riusciti dallo stesso indirizzo IP in un breve periodo di tempo e altre attività ritenute sospette. È possibile che un pirata informatico abbia tentato l'accesso da questo indirizzo IP.</p><p>I risultati di questo report mostreranno che i tentativi originati da un indirizzo IP in cui è stata notata un'attività sospetta, insieme al timestamp associato all'accesso.</p> | Scheda Directory > Report |

### Attività di accesso con anomalie

| Descrizione | Percorso report |
| :-------------     | :-------        |
| <p>Questo report include accessi identificati come "anomali" dagli algoritmi di apprendimento automatico. I motivi per contrassegnare un tentativo di accesso come irregolare prevedono posizioni di accesso imprevisto, ora del giorno e posizioni o una combinazione di queste. È possibile che un pirata informatico abbia tentato di accedere con questo account. L'algoritmo di apprendimento automatico classifica gli eventi come "anomali" o "sospetti", laddove "sospetti" indica una maggiore probabilità di una violazione della sicurezza.</p><p>Nei Risultati del report verranno mostrati questi accessi, insieme alla classificazione, alla posizione e a un timestamp associato a ogni accesso.</p><p>Se in un periodo di 30 giorni o meno verranno rilevati 10 o più eventi di accesso anomalo, Microsoft invierà agli amministratori globali una notifica tramite posta elettronica. Assicurarsi di includere aad-alerts-noreply@mail.windowsazure.com nell'elenco dei mittenti attendibili.</p> | Scheda Directory > Report |

### Accessi da dispositivi potenzialmente infetti

| Descrizione | Percorso report |
| :-------------     | :-------        |
| <p>Usare questo report per visualizzare accessi eseguiti da dispositivi in cui è possibile sia presente malware. Gli indirizzi IP degli accessi vengono messi in correlazione con gli indirizzi IP dai quali si è tentato di contattare un server con malware.</p><p>Consiglio: poiché in questo report si presuppone che un indirizzo IP sia associato allo stesso dispositivo in entrambi i casi, si consiglia di contattare l'utente e analizzare il dispositivo dell'utente per verificare.</p><p>Per altre informazioni su come impedire la diffusione di malware, vedere il sito Web [Malware Protection Center](http://go.microsoft.com/fwlink/?linkid=335773). </p> | Scheda Directory > Report |

### Utenti con anomalie dell'attività di accesso

| Descrizione | Percorso report |
| :-------------     | :-------        |
| <p>Usare questo report per visualizzare tutti gli account utente per i quali è stata individuata un'attività anomala. Nel report sono inclusi i dati provenienti da tutti gli altri report relativi ad attività anomale. I risultati del report conterranno i dettagli relativi all'utente, il motivo per cui l'evento di accesso è stato identificato come anomalo, data e ora e altre informazioni rilevanti sull'evento.</p> | Scheda Directory > Report |

## Report applicazioni integrate

### Utilizzo dell'applicazione: riepilogo

| Descrizione | Percorso report |
| :-------------     | :-------        |
| Usare questo report per visualizzare l'utilizzo di tutte le applicazioni SaaS presenti nella directory. Questo report si basa sul numero di selezioni dell'applicazione eseguite nel Pannello di accesso. | Scheda Directory > Report |

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
| Accessi al [portale di Microsoft Azure](http://portal.azure.com/) | Sì |
| Accessi per il [portale di Office 365](http://portal.office.com/) | Sì |
| Accessi a un'applicazione nativa, ad esempio Outlook (vedere l'eccezione riportata di seguito) | Sì |
| Accessi a un'app federativa/con provisioning tramite il pannello di accesso, come Salesforce | Sì |
| Accessi a un'app basata su password tramite il pannello di accesso, come Twitter | No (disponibile a breve) |
| Accessi a un'app di business personalizzata che è stata aggiunta alla directory | No (disponibile a breve) |

> Nota: per ridurre la quantità di disturbo in questo report, non vengono visualizzati gli accessi all'app nativa [Lync/Skype per Business](http://products.office.com/it-it/skype-for-business/online-meetings) ed eseguiti dall'[Assistente per l'accesso ai Microsoft Online Services](http://community.office365.com/it-it/w/sso/534.aspx).

## Log attività

### Report di controllo

| Descrizione | Percorso report |
| :-------------     | :-------        |
| Mostra un record di tutti gli eventi controllati entro le ultime 24 ore, gli ultimi 7 giorni o gli ultimi 30 giorni. <br /> Per altre informazioni, vedere [Eventi del report di controllo di Azure Active Directory](active-directory-reporting-audit-events.md) | Scheda Directory > Report |

### Report attività dei gruppi

| Descrizione | Percorso report |
| :-------------     | :-------        |
| Mostra tutte le attività per i gruppi gestiti in modalità self-service nella directory. | Scheda Directory > Utenti > <i>Utente</i> > Dispositivi |

### Report attività di registrazione per la reimpostazione password

| Descrizione | Percorso report |
| :-------------     | :-------        |
| Vengono illustrate tutte le registrazioni per la reimpostazione della password che si sono verificate nell'organizzazione | Scheda Directory > Report |

### Attività di reimpostazione password

| Descrizione | Percorso report |
| :-------------     | :-------        |
| Vengono illustrati tutti i tentativi di reimpostazione della password che si sono verificati nell'organizzazione. | Scheda Directory > Report |

## Aspetti da considerare quando si sospetta la violazione della sicurezza

Se si sospetta che un account utente potrebbe essere compromesso o qualsiasi tipo di attività utente sospetta potrebbe causare una violazione della protezione dei dati della directory nel cloud, si consiglia una o più delle seguenti operazioni:

- Contattare l'utente per verificare l'attività
- Reimpostare la password dell'utente
- [Abilitare l'autenticazione a più fattori](http://go.microsoft.com/fwlink/?linkid=335774) per una maggiore sicurezza

## Visualizzare o scaricare un report

1. Nel portale di gestione di Azure, fare clic su **Active Directory**, fare clic sul nome della directory dell'organizzazione e quindi fare clic su **Report**.
2. Nella pagina Report, fare clic sul report che si desidera visualizzare e/o scaricare.
    >
    > [AZURE.NOTE]
    
3. Fare clic sul menu a discesa accanto a Intervallo e quindi selezionare uno degli intervalli di tempo che devono essere usati durante la generazione di questo report:
    - Ultime 24 ore
    - Ultimi 7 giorni
    - Ultimi 30 giorni
4. Fare clic sull'icona del segno di spunta per eseguire il report.
5. Se applicabile, fare clic su **Download** per scaricare il report in un file compresso in formato valori separati da virgole (CSV) per visualizzarlo offline o a scopo di archiviazione.

## Ignorare un evento

Se si stanno visualizzando tutti i report anomalie, si noterà che è possibile ignorare vari eventi che vengono visualizzati nei report correlati. Per ignorare un evento, evidenziarlo nel report e quindi fare clic su **Ignora**. Il pulsante **Ignora** rimuoverà definitivamente l'evento evidenziato dal report e può essere usato solo dagli amministratori globali autorizzati.

## Notifiche automatiche tramite posta elettronica 

### Quali report generano notifiche tramite posta elettronica

A questo punto, solo il rapporto Attività di accesso con anomalie e Utenti con anomalie dell'attività di accesso usano il sistema di notifica tramite posta elettronica.

### Che cosa attiva l'invio della notifica tramite posta elettronica?

Per impostazione predefinita, Azure Active Directory è impostato per l'invio automatico delle notifiche tramite posta elettronica a tutti gli amministratori globali. Il messaggio viene inviato quando si verificano le condizioni seguenti per ogni report.

Per il report Utenti con anomalie dell'attività di accesso:

- Origini sconosciute: 10 eventi
- Più errori: 10 eventi
- Accessi da indirizzi IP con attività sospetta: 10 eventi
- Dispositivi infetti: 10 eventi

Report Utenti con anomalie dell'attività di accesso

- Origini sconosciute: 10 eventi
- Più errori: 10 eventi
- Accessi da indirizzi IP con attività sospetta: 10 eventi
- Dispositivi infetti: 5 eventi
- Report accessi anomali: 15 eventi

Il messaggio di posta elettronica viene inviato se una delle condizioni precedenti viene soddisfatta entro 30 giorni o poiché l'ultimo messaggio di posta elettronica è stato inviato se è inferiore a 30 giorni.

Per accessi anomali si intendono quelli che sono stati identificati come "anomali" dagli algoritmi di Machine Learning, sulla base di posizioni di accesso impreviste, ora del giorno e posizioni impreviste o una combinazione di questi fattori. È possibile che un pirata informatico abbia tentato di accedere con questo account. Per altre informazioni su questo report, vedere la tabella riportata in precedenza.

### A chi vengono inviate le notifiche tramite posta elettronica?

Il messaggio di posta elettronica viene inviato a tutti gli amministratori globali ai quali è stata assegnata una licenza di Active Directory Premium. Per garantire il recapito, il messaggio viene inviato anche all'indirizzo di posta elettronica alternativo degli amministratori . Per essere certi di ricevere il messaggio, gli amministratori devono aggiungere aad-alerts-noreply@mail.windowsazure.com nei rispettivi elenchi di mittenti attendibili.

### Con quale frequenza vengono inviati i messaggi di posta elettronica?

Dopo l'invio di un messaggio di posta elettronica, quello successivo verrà inviato solo se entro 30 giorni dall'invio si verificano almeno 10 o nuovi eventi di accesso anomalo. Come si accede al report indicato nel messaggio di posta elettronica?

Quando si fa clic sul collegamento, si verrà reindirizzati alla pagina del report nel portale di gestione di Azure. Per accedere al report, è necessario essere:

- Un amministratore o un coamministratore della sottoscrizioni di Azure
- Un amministratore globale nella directory e assegnata una licenza di Active Directory Premium. Per altre informazioni, vedere Informazioni su Azure Active Directory. 

### È possibile disattivare questi messaggi di posta elettronica?

Sì, per disattivare le notifiche correlate a anomali accessi all'interno del portale di gestione di Azure, fare clic su **Configura** e quindi selezionare **Disabilitato** sotto la sezione **Notifiche**.

## Passaggi successivi

- [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso](active-directory-add-company-branding.md)

<!---HONumber=58--> 