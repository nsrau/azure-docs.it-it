<properties 
	pageTitle="Informazioni: Gestione delle password di Azure AD | Microsoft Azure"
	description="Descrizione delle funzionalità di gestione delle password in Azure AD, incluse la reimpostazione delle password, la modifica, il report di gestione delle password e il writeback in Active Directory locale." 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2015" 
	ms.author="asteen"/>

# Gestire le password da qualsiasi posizione
Self-Service è stato a lungo un obiettivo chiave per i reparti IT di tutto il mondo come misura di riduzione dei costi e facile utilizzo. In effetti, nel mercato si sono diffusi prodotti che consentono di gestire gruppi locali, password o profili utente dal cloud o in locale. Azure AD si distingue da queste offerte fornendo alcune delle funzionalità self-service più efficaci e semplici da usare disponibili attualmente.

**Gestione delle password di Azure AD** è un insieme di funzionalità che consentono agli utenti di gestire le password da qualsiasi dispositivo, in qualsiasi momento, da qualsiasi posizione, pur rimanendo conforme ai criteri di sicurezza definiti.

## Panoramica
È possibile iniziare con una distribuzione pilota di Gestione delle password di Azure AD in meno di cinque minuti e distribuire l’intero servizio a tutta l'organizzazione in poche ore. Di seguito sono riportate alcune risorse utili per iniziare a usare il servizio:

* [**Funzionamento**](active-directory-passwords-how-it-works.md): informazioni sui sei diversi componenti del servizio e sulle relative funzioni
* [**Introduzione**](active-directory-passwords-getting-started.md): informazioni su come consentire agli utenti di reimpostare e modificare le password cloud o locali
* [**Personalizzazione**](active-directory-passwords-customize.md): informazioni su come personalizzare l'aspetto e il comportamento del servizio in base alle esigenze dell'organizzazione
* [**Procedure consigliate**](active-directory-passwords-best-practices.md): informazioni su come distribuire rapidamente e gestire in modo efficace le password nell'organizzazione
* [**Informazioni dettagliate**](active-directory-passwords-get-insights.md): informazioni sulle funzionalità di creazione report integrate
* [**Domande frequenti**](active-directory-passwords-faq.md): risposte alle domande frequenti
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md): informazioni su come risolvere rapidamente eventuali problemi con il servizio
* [**Ulteriori informazioni**](active-directory-passwords-learn-more.md): approfondimenti sui dettagli tecnici del funzionamento del servizio


## Operazioni possibili con Gestione delle password di Azure AD
Di seguito sono elencate alcune operazioni che è possibile eseguire con le funzionalità di gestione delle password di Azure AD.

- La **modifica delle password self-service** consente agli utenti finali o agli amministratori di cambiare le password scadute o non scadute senza dover chiamare un amministratore o rivolgersi al Supporto tecnico.
- La **reimpostazione delle password self-service** consente agli utenti finali o agli amministratori di reimpostare automaticamente le password senza dover chiamare un amministratore o rivolgersi al Supporto tecnico. La reimpostazione delle password self-service è disponibile solo in Azure AD Premium o Basic. Per altre informazioni, vedere Edizioni di Azure Active Directory.
- La **reimpostazione delle password avviata dall'amministratore** consente a un amministratore di reimpostare la password di un utente finale o di un altro amministratore dal [portale di gestione di Azure](https://manage.windowsazure.com).
- I **report sulle attività di gestione delle password** forniscono agli amministratori informazioni dettagliate sulle attività di reimpostazione delle password e di registrazione che si verificano all'interno dell'organizzazione. 
- Il **writeback delle password** permette di gestire le password locali dal cloud, in modo che tutti gli scenari descritti in precedenza possano essere eseguiti da o per conto di utenti federati o con sincronizzazione tramite password. Per il writeback delle password è necessaria una licenza di Azure AD Premium. Per altre informazioni, vedere Introduzione a Azure AD Premium.

## Uso di Gestione delle password di Azure AD
Di seguito sono elencati alcuni motivi per cui è consigliabile usare le funzionalità di gestione delle password di Azure AD.

- **Ridurre i costi**: la reimpostazione delle password assistita dal supporto rappresenta in genere il 20% della spesa IT dell'organizzazione
- **Migliorare le esperienze dell’utente**: gli utenti non vogliono chiamare il supporto tecnico e trascorrere un'ora al telefono ogni volta che dimenticano le proprie password
- **Ridurre i volumi di supporto tecnico**: la gestione delle password è il singolo driver di supporto tecnico più grande per la maggior parte delle organizzazioni
- **Consentire la mobilità**: gli utenti possono reimpostare le password da ovunque si trovino

## Aggiornamenti recenti del servizio

**Aggiornamenti a livello di usabilità della pagina di registrazione**: ottobre 2015

- Dopo che i dati di un utente sono stati registrati, sarà ora sufficiente fare clic su "esito positivo" per aggiornare i dati senza dover inviare nuovamente il messaggio di posta elettronica o effettuare una nuova telefonata.

**Maggiore affidabilità del writeback delle password**: settembre 2015

- A partire dalla versione di settembre di Azure AD Connect, l'agente di writeback delle password effettuerà ripetuti tentativi di connessione e disporrà di funzionalità di failover aggiuntive e più affidabili.

**API per il recupero dei dati di report sulla reimpostazione della password**: agosto 2015

- I dati relativi ai report sulla reimpostazione delle password possono ora essere recuperati direttamente dall'[API di Report ed eventi di Azure AD](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).

**Supporto per la reimpostazione delle password di Azure AD durante l'aggiunta a un dominio cloud**: agosto 2015

- Qualsiasi utente cloud può ora reimpostare la password direttamente dalla schermata di accesso di Windows 10 durante l'esperienza di aggiunta a un dominio cloud. Si noti che questa funzionalità non è ancora esposta nella schermata di accesso di Windows 10.

**Implementare la registrazione per la reimpostazione delle password all'accesso alle app di Azure e federate**: luglio 2015

- Oltre all'implementazione della registrazione all'accesso a myapps.microsoft.com, è ora supportata l'implementazione della registrazione durante l'accesso al portale di gestione di Azure e alle applicazioni federate Single Sign-On

**Supporto per la localizzazione della domanda di sicurezza**: maggio 2015

- È ora possibile selezionare domande di sicurezza predefinite, localizzate nel set di lingue completo di O365 durante la configurazione delle domande di sicurezza per la reimpostazione delle password.

**Supporto per lo sblocco dell'account durante la reimpostazione delle password**: giugno 2015

- Se si usa il writeback delle password e si reimposta la password quando l'account è bloccato, l'account di Active Directory verrà automaticamente sbloccato.

**Registrazione SSPR personalizzata**: aprile 2015

- La pagina di registrazione per la reimpostazione delle password è ora personalizzata con il logo della società.

**Domande di sicurezza**: marzo 2015

- Le domande di sicurezza sono state rilasciate e sono disponibili a livello generale.

**Sblocco dell’account**: marzo 2015

- I nuovi utenti possono sbloccare i propri account quando si verifica la reimpostazione delle password

## Presto disponibile

Di seguito sono riportate alcune interessanti funzionalità in fase di elaborazione.

**Supporto per lo sblocco di account di Active Directory senza la reimpostazione della password**: presto disponibile

- Numerosi utenti hanno richiesto la possibilità di sbloccare gli account di Active Directory separatamente rispetto alla reimpostazione della password. Microsoft è lieta di annunciare che questa funzionalità è in fase di ultimazione e verrà rilasciata agli utenti che usano il writeback delle password.

**Supporto per i promemoria relativi all'aggiornamento dei dati registrati durante l'accesso**: in fase di elaborazione

- Sono ora supportati i promemoria per gli utenti relativi all'aggiornamento dei dati registrati all'accesso a myapps.microsoft.com, ma la possibilità di estenderli a tutti gli accessi è ancora in fase di elaborazione.

**Implementare la registrazione per la reimpostazione delle password all'accesso alle app di Office 365**: in fase di elaborazione

- All'esperienza di accesso ad Azure AD vengono aggiunte sempre più app di Office. Al momento dell'aggiunta, verranno automaticamente supportate per la registrazione SSPR.

<br/> <br/> <br/>

**Risorse aggiuntive**


* [Funzionamento della gestione delle password](active-directory-passwords-how-it-works.md)
* [Introduzione alla gestione delle password](active-directory-passwords-getting-started.md)
* [Personalizzare la gestione delle password](active-directory-passwords-customize.md)
* [Procedure consigliate per la gestione delle password](active-directory-passwords-best-practices.md)
* [Come ottenere informazioni dettagliate con i report di gestione delle password](active-directory-passwords-get-insights.md)
* [Domande frequenti sulla gestione delle password](active-directory-passwords-faq.md)
* [Risolvere i problemi relativi alla gestione delle password](active-directory-passwords-troubleshoot.md)
* [Altre informazioni](active-directory-passwords-learn-more.md)
* [Gestione delle password su MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)

<!---HONumber=Oct15_HO4-->