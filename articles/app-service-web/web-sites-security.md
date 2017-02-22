---
title: Garantire la sicurezza di un&quot;app in Servizio app di Azure
description: Informazioni sulla sicurezza di app Web, back-end di app per dispositivi mobili o app per le API in Servizio app di Azure.
services: app-service
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 5ce560b4-42d7-4b20-935c-0445fd539e39
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/12/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 641fe31fc4b160fe5f0628df717006fb540394b9


---
# <a name="secure-an-app-in-azure-app-service"></a>Garantire la sicurezza di un'app in Servizio app di Azure
Questo articolo offre informazioni iniziali sulla sicurezza di app Web, back-end per app per dispositivi mobili o app per le API in Servizio app di Azure. 

Servizio app di Azure offre due livelli di sicurezza: 

* **Sicurezza dell'infrastruttura e della piattaforma:** Azure offre i servizi necessari per eseguire operazioni in modo sicuro nel cloud.
* **Sicurezza delle applicazioni:** è responsabilità dell'utente progettare le applicazioni in modo sicuro. Questo comprende aspetti come l'integrazione con Azure Active Directory, la gestione dei certificati e la verifica che la comunicazione con i diversi servizi sia sicura. 

#### <a name="infrastructure-and-platform-security"></a>Sicurezza dell'infrastruttura e della piattaforma:
Poiché il servizio app gestisce le VM di Azure, l'archiviazione, le connessioni di rete, i framework Web, le funzionalità di gestione e integrazione e molto altro, è attivamente protetto e sottoposto a continui controlli approfonditi sulla conformità per assicurare che:

* Le app del servizio app sono isolate sia da Internet che dalle risorse di Azure di altri clienti.
* La comunicazione di segreti (ad esempio, stringhe di connessione) tra l'app del servizio app e altre risorse di Azure (ad esempio, database SQL) in un gruppo di risorse rimanga all'interno di Azure e non superi i limiti di rete. I segreti siano sempre crittografati.
* Tutte le comunicazioni tra l'app del servizio app e le risorse esterne, ad esempio gestione di PowerShell, interfaccia della riga di comando, Azure SDK, API REST e connessioni ibride, siano crittografate correttamente.
* La gestione delle minacce&24; ore su&24; protegge le risorse del servizio app da malware, attacchi Distributed Denial of Service (DDoS), attacchi man-in-the-middle (MITM) e altre minacce. 

Per altre informazioni sulla sicurezza della piattaforma e dell'infrastruttura in Azure, vedere [Centro protezione di Azure](/support/trust-center/security/).

#### <a name="application-security"></a>Sicurezza delle applicazioni:
Mentre Azure è responsabile della sicurezza dell'infrastruttura e della piattaforma su cui l'applicazione viene eseguita, è responsabilità dell'utente proteggere l'applicazione stessa. In altre parole, è necessario sviluppare, distribuire e gestire il codice e i contenuti dell'applicazione in modo sicuro. In caso contrario, il codice o il contenuto dell'applicazione può essere esposto a minacce, ad esempio:

* Attacchi SQL injection
* Hijack della sessione
* Script da altri siti
* Attacchi MITM a livello di applicazione
* Attacchi DDoS a livello di applicazione

Una discussione completa sulle considerazioni relative alla sicurezza delle applicazioni basate su Web non rientra negli obiettivi di questo documento. Come punto di partenza per altre informazioni sulla sicurezza dell'applicazione, vedere l'[Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page), in particolare il [progetto Top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), che elenca le dieci principali lacune critiche nella sicurezza delle applicazioni Web, come stabilito dai membri di OWASP.

## <a name="perform-penetration-testing-on-your-app"></a>Eseguire test di penetrazione sull'app
Uno dei modi più semplici per iniziare a eseguire test di vulnerabilità per le app del servizio app consiste nell'usare l' [integrazione con Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) per eseguire un'analisi delle vulnerabilità nell'app in un solo clic. È possibile visualizzare i risultati del test in un report di facile comprensione e imparare a risolvere ogni vulnerabilità con istruzioni dettagliate.

Se si preferisce eseguire i propri test di penetrazione o usare un altro gruppo o fornitore di strumenti di scansione, è necessario seguire il [processo di approvazione dei test di penetrazione di Azure](https://security-forms.azure.com/penetration-testing/terms) e ottenere l'approvazione preventiva per eseguire i test di penetrazione desiderati.

## <a name="a-namehttpsa-secure-communication-with-customers"></a><a name="https"></a> Garantire la sicurezza delle comunicazioni con i clienti
Se si usa il nome di dominio **\*.azurewebsites.net** creato per l'app del servizio app, è possibile usare immediatamente HTTPS, in quanto viene reso disponibile un certificato SSL per tutti i nomi di dominio **\*.azurewebsites.net**. Se il sito usa un [nome di dominio personalizzato](web-sites-custom-domain-name.md) è possibile caricare un certificato SSL per [abilitare HTTPS](web-sites-configure-ssl-certificate.md) per il dominio personalizzato.

L'abilitazione di [HTTPS](https://en.wikipedia.org/wiki/HTTPS) aumenta la protezione da attacchi MITM nelle comunicazioni tra l'app e gli utenti.

## <a name="secure-data-tier"></a>Sicurezza del livello dati
Il servizio app si integra strettamente con il database SQL, in modo che tutte le stringhe di connessione vengano crittografate a tutti i livelli e vengano decrittografate solo sulla VM che esegue l'app *e* solo quando l'app viene eseguita. Il database SQL di Azure include anche molte funzionalità di sicurezza per proteggere i dati dell'applicazione da minacce informatiche, tra cui [crittografia dei dati inattivi](https://msdn.microsoft.com/library/dn948096.aspx), [crittografia sempre attiva](https://msdn.microsoft.com/library/mt163865.aspx), [maschera dati dinamica](../sql-database/sql-database-dynamic-data-masking-get-started.md) e [rilevamento delle minacce](../sql-database/sql-database-threat-detection.md). In caso di dati sensibili o requisiti di conformità, vedere l'articolo relativo alla [sicurezza del database SQL](../sql-database/sql-database-security-overview.md) per altre informazioni su come proteggere i dati.

Se si usa un provider di database di terze parti, ad esempio ClearDB, è consigliabile consultare direttamente la documentazione del fornitore per le procedure di sicurezza consigliate.  

## <a name="a-namedevelopa-secure-development-and-deployment"></a><a name="develop"></a> Garantire la sicurezza dello sviluppo e della distribuzione
### <a name="publishing-profiles-and-publish-settings"></a>Profili e impostazioni di pubblicazione
Quando si sviluppano applicazioni, si eseguono attività di gestione o si automatizzano attività usando utilità come **Visual Studio**, **Web Matrix**, **Azure PowerShell** o l'**interfaccia della riga di comando di Azure**, è possibile usare un file di *impostazioni di pubblicazione* o un *profilo di pubblicazione*. Entrambi i tipi di file eseguono l'autenticazione dell'utente con Azure e devono essere protetti per evitare accessi non autorizzati.

* Un file di **impostazioni di pubblicazione** contiene
  
  * L'ID sottoscrizione di Azure
  * Un certificato di gestione che consente di eseguire attività di gestione per la sottoscrizione *senza dover inserire un nome o una password dell'account*.
* Un file **profilo di pubblicazione** contiene
  
  * Informazioni per la pubblicazione nell'app

Se si usa un'utilità che usa un file di impostazioni di pubblicazione o un file di profilo di pubblicazione, importare il file contenente le impostazioni o il profilo di pubblicazione nell'utilità e quindi **eliminare** il file. Se si vuole tenere il file, ad esempio per condividerlo con gli altri che lavorano sul progetto, archiviarlo in un percorso sicuro, ad esempio una directory *crittografata* con autorizzazioni limitate.

Inoltre, assicurarsi che le credenziali importate siano protette. Ad esempio, **Azure PowerShell** e l'**interfaccia della riga di comando di Azure** archiviano entrambe le informazioni importate nella **home directory** (*~* nei sistemi Linux oppure OS X e */users/yourusername* nei sistemi Windows). Per una maggiore protezione è opportuno **crittografare** questi percorsi con gli strumenti di crittografia disponibili per il sistema operativo.

### <a name="configuration-settings-and-connection-strings"></a>Impostazioni di configurazione e stringhe di connessione
Di solito le stringhe di connessione, le credenziali di autenticazione e altre informazioni riservate vengono archiviate nei file di configurazione. Purtroppo questi file possono essere esposti nel sito Web o inseriti in un repository pubblico che ne espone le informazioni. Una ricerca semplice in [GitHub](https://github.com), ad esempio, può portare alla luce numerosi file di configurazione con segreti esposti nei repository pubblici.

La procedura consigliata consiste nell'evitare di includere queste informazioni nei file di configurazione dell'applicazione. Il servizio app consente di archiviare le informazioni di configurazione nell'ambiente di runtime come **impostazioni app** e **stringhe di connessione**. I valori vengono esposti nell'applicazione al runtime mediante *variabili di ambiente* per la maggior parte dei linguaggi di programmazione. Per le applicazioni .NET questi valori vengono inseriti nella configurazione .NET al runtime. Ad eccezione di queste situazioni, queste impostazioni di configurazione rimarranno crittografate, a meno che non vengano visualizzate o configurate usando il [portale di Azure](https://portal.azure.com) o utilità come PowerShell o l'interfaccia della riga di comando di Azure. 

L'archiviazione delle informazioni di configurazione nel servizio app consente all'amministratore dell'app di bloccare le informazioni riservate per le app di produzione. Gli sviluppatori possono usare un set di impostazioni di configurazione separato per lo sviluppo di app e le impostazioni possono essere sostituite automaticamente dalle impostazioni configurate nel servizio app. Anche per gli sviluppatori non è necessario conoscere i segreti configurati per l'app di produzione. Per altre informazioni sulla configurazione delle impostazioni e delle stringhe di connessione delle app nel servizio app, vedere l'articolo relativo alla [configurazione di app Web](web-sites-configure.md).

### <a name="ftps"></a>FTPS
Servizio App di Azure offre all'app accesso FTP sicuro al file system tramite **FTPS**. Ciò consente di accedere in modo sicuro al codice dell'applicazione nell'app Web e ai log di diagnostica. È consigliabile usare sempre FTPS anziché FTP. 

È possibile trovare il collegamento FTPS per l'app con i passaggi seguenti:

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Selezionare **Esplora tutto**.
3. Nel pannello **Sfoglia** selezionare **Servizi app**.
4. Nel pannello **Servizi app** selezionare l'app desiderata.
5. Nel pannello dell'app selezionare **Tutte le impostazioni**.
6. Nel pannello **Impostazioni** selezionare **Proprietà**.
7. Il collegamenti FTP e FTPS sono disponibili nel pannello **Impostazioni** . 

Per altre informazioni su FTPS, vedere [File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla sicurezza della piattaforma Azure, informazioni su come segnalare un **uso improprio o un problema di sicurezza** o per segnalare a Microsoft che si sta per eseguire un **test di penetrazione** del sito, vedere la sezione sulla sicurezza del [Centro protezione di Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

Per altre informazioni sui file **web.config** o **applicationhost.config** nelle app del servizio app, vedere l'articolo relativo alle [opzioni di configurazione non bloccate nelle app Web del servizio app di Azure](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Per informazioni sulla registrazione di informazioni nelle app del servizio app, che potrebbero essere utili per il rilevamento di attacchi, vedere l'articolo relativo all' [abilitazione della registrazione diagnostica](web-sites-enable-diagnostic-log.md).

> [!NOTE]
> Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](https://azure.microsoft.com/try/app-service/), dove è possibile creare un'app iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

## <a name="whats-changed"></a>Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)




<!--HONumber=Feb17_HO3-->


