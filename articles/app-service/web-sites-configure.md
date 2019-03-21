---
title: Configurare le app - Servizio app di Azure
description: Come configurare un'app nel Servizio app di Azure
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: fb8dedac8b795ec127d7b4a14728d73c9397a1dd
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807406"
---
# <a name="configure-apps-in-azure-app-service"></a>Configurare le app nel Servizio app di Azure

Questo argomento descrive come configurare un'app Web, un back-end per dispositivi mobili o un'app API usando il [portale di Azure].

## <a name="application-settings"></a>Impostazioni dell'applicazione
1. Nel [portale di Azure] aprire il pannello relativo all'app.
2. Fare clic su **Impostazioni applicazione**.

![Impostazioni dell'applicazione][configure01]

Nel pannello **Impostazioni applicazione** le impostazioni sono raggruppate in diverse categorie.

### <a name="general-settings"></a>Impostazioni generali
**Versioni del framework**. Impostare le opzioni seguenti se l'app usa uno dei seguenti framework: 

* **.NET Framework**: impostare la versione .NET Framework. 
* **PHP**: impostare la versione PHP oppure scegliere **DISATTIVATO** per disabilitare PHP. 
* **Java**: selezionare la versione di Java oppure **DISATTIVATO** per disabilitare Java. Utilizzare l'opzione **Contenitore Web** per scegliere tra le versioni Tomcat e Jetty.
* **Python**: selezionare la versione Python oppure **DISATTIVATO** per disabilitare Python.

Per motivi tecnici, l'abilitazione di Java per le proprie app disabilita le opzioni di .NET, PHP e Python.

<a name="platform"></a>
**Piattaforma**. Scegliere se eseguire l'app in un ambiente a 32 bit o a 64 bit. L'ambiente a 64 bit richiede il livello Basic o Standard. I livelli Gratuito e Condiviso vengono eseguiti sempre in un ambiente a 32 bit.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

**Web Socket**. Impostare **ATTIVATO** per abilitare il protocollo WebSocket, ad esempio se l'app usa [ASP.NET SignalR] o [socket.io](https://socket.io/).

<a name="alwayson"></a>
**Always On**. Per impostazione predefinita, le app vengono scaricate se restano inattive per un determinato periodo di tempo. Ciò consente al sistema di conservare le risorse. In modalità Basic o Standard è possibile abilitare **Always On** affinché l'app rimanga sempre caricata. Se nell'app vengono eseguiti processi Web continui o processi Web attivati mediante un'espressione CRON è necessario abilitare **Always On**, altrimenti l'esecuzione dei processi Web potrebbe non avvenire in modo affidabile.

**Versione pipeline gestita**. Consente di impostare la [modalità pipeline]IIS. Lasciare questa opzione impostata su Integrato (predefinita), tranne nel caso in cui un'app meno recente richieda una versione precedente di IIS.

**Versione HTTP**. Impostata su **2.0** per abilitare il supporto per il protocollo [HTTPS/2](https://wikipedia.org/wiki/HTTP/2). 

> [!NOTE]
> I browser più recenti supportano il protocollo HTTP/2 solo su TLS, mentre il traffico non crittografato continua a usare il protocollo HTTP/1.1. Per garantire che i browser client si connettano all'app tramite HTTP/2, [acquistare un certificato del servizio app](web-sites-purchase-ssl-web-site.md) per il dominio personalizzato dell'app o [associare un certificato di terze parti](app-service-web-tutorial-custom-ssl.md).

**Affinità ARR**. In un'applicazione scalata orizzontalmente su più istanze di macchina virtuale, i cookie di affinità ARR garantiscono che il client venga instradato verso la stessa istanza per tutta la durata della sessione. Per migliorare le prestazioni delle applicazioni senza stato, impostare questa opzione su **Disattivata**.   

**Scambio automatico**. Se si abilita l'opzione Scambio automatico per uno slot di distribuzione, il servizio app immette automaticamente l'app in produzione quando si esegue il push di un aggiornamento nello slot. Per altre informazioni, vedere [Configurare gli ambienti di gestione temporanea nel Servizio app di Azure](deploy-staging-slots.md).

### <a name="debugging"></a>Debug
**Debug remoto**. Abilita il debug remoto. Quando questa opzione è abilitata, è possibile usare il debugger remoto in Visual Studio per connettersi direttamente all'app. Il debug remoto resterà abilitato per 48 ore. 

### <a name="app-settings"></a>Impostazioni app
Questa sezione riporta le coppie nome/valore che verranno caricate all'avvio dell'app. 

* Per le app.NET, queste impostazioni verranno inserite nella configurazione .NET `AppSettings` in fase di esecuzione, sostituendo le impostazioni esistenti. 
* Per il servizio app in Linux o app Web per contenitori, se la struttura della chiave JSON è stata annidata nel nome, come ad esempio `ApplicationInsights:InstrumentationKey`, `ApplicationInsights__InstrumentationKey` dovrà essere il nome della chiave. Si noti quindi che `:` deve essere sostituito con `__` (doppio carattere di sottolineatura).
* Le applicazioni PHP, Python, Java e Node possono accedere a queste impostazioni come variabili di ambiente durante il runtime. Per ciascuna impostazione dell'app vengono create due variabili di ambiente, una con il nome specificato dalla voce dell'impostazione dell'app e l'altra con il prefisso APPSETTING_. Entrambe contengono lo stesso valore.

Le impostazioni dell'app vengono sempre crittografate quando sono archiviate (crittografia dei dati inattivi).

Le impostazioni dell'app possono essere risolte da Key Vault usando [riferimenti a Key Vault](app-service-key-vault-references.md).

### <a name="connection-strings"></a>Stringhe di connessione
Stringhe di connessione per le risorse collegate. 

Per le app .NET, tali stringhe di connessione vengono inserite nelle impostazioni `connectionStrings` della configurazione .NET in fase di runtime, sostituendo le voci esistenti in cui la chiave è uguale al nome del database collegato. 

Per le applicazioni PHP, Python, Java e Node queste impostazioni saranno disponibili come variabili di ambiente durante il runtime, con il tipo di connessione come prefisso. I prefissi delle variabili di ambiente sono i seguenti: 

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* Database SQL: `SQLAZURECONNSTR_`
* Personalizzato: `CUSTOMCONNSTR_`

Ad esempio, se una stringa di connessione MySql venisse denominata `connectionstring1`, l'accesso avverrebbe attraverso la variabile di ambiente`MYSQLCONNSTR_connectionString1`.

Le stringhe di connessione vengono sempre crittografate quando sono archiviate (crittografia dei dati inattivi).

Le stringhe di connessione possono essere risolte da Key Vault usando [riferimenti a Key Vault](app-service-key-vault-references.md).

### <a name="default-documents"></a>Documenti predefiniti
Il documento predefinito è rappresentato dalla pagina Web visualizzata nell'URL radice di un sito Web.  Viene utilizzato il primo file corrispondente dell'elenco. 

Le app potrebbero usare moduli che vengono instradati in base all'URL invece di visualizzare contenuto statico. In tal caso non è presente alcun documento predefinito.    

### <a name="handler-mappings"></a>Mapping dei gestori
Usare quest'area per aggiungere processori script personalizzati allo scopo di gestire le richieste di estensioni di file specifiche. 

* **Estensione**. L'estensione file da gestire, ad esempio *.php o handler.fcgi. 
* **Percorso processore script**. Il percorso assoluto del processore script. Le richieste di file che corrispondono a questo modello saranno elaborate dal processore script. Utilizzare il percorso `D:\home\site\wwwroot` per fare riferimento alla directory radice della propria app.
* **Argomenti aggiuntivi**. Argomenti facoltativi della riga dei comando per il processore script 

### <a name="virtual-applications-and-directories"></a>Applicazioni e directory virtuali
Per configurare applicazioni e directory virtuali, specificare ogni directory virtuale e il corrispondente percorso fisico relativo alla radice del sito. È facoltativamente possibile selezionare la casella di controllo **Applicazione** in modo da contrassegnare una directory virtuale come applicazione.

## <a name="enabling-diagnostic-logs"></a>Abilitazione dei log di diagnostica
Per abilitare i log di diagnostica:

1. Nel pannello dell'app fare clic su **Tutte le impostazioni**.
2. Fare clic su **Log diagnostici**. 

Opzioni per la scrittura dei log di diagnostica da un'applicazione Web che supporta la registrazione: 

* **Registrazione applicazioni**. Consente di scrivere i log delle applicazioni nel file system. La registrazione ha una durata di 12 ore. 

**Livello**. Quando viene abilitata la registrazione, questa opzione consente di specificare la quantità di informazioni registrata (Errore, Avviso, Informazioni, Dettagliato).

**Registrazione del server Web**. I log vengono salvati nel formato file di log esteso W3C. 

**Messaggi di errore dettagliati**. Consente di salvare messaggi di errore dettagliati in file htm. I file vengono salvati nel percorso /LogFiles/DetailedErrors. 

**Traccia delle richieste non riuscite**. Consente di registrare le richieste non riuscite ai file XML. I file vengono salvati in /LogFiles/W3SVC*xxx*, dove xxx è un identificatore univoco. Questa cartella contiene un file XSL e uno o più file XML. Assicurarsi di scaricare il file XSL, perché fornisce la funzionalità di formattazione e filtro dei contenuti dei file XML.

Per visualizzare i file di log, è necessario creare le credenziali FTP, come descritto di seguito:

1. Nel pannello dell'app fare clic su **Tutte le impostazioni**.
2. Fare clic su **Credenziali distribuzione**.
3. Immettere un nome utente e una password.
4. Fare clic su **Save**.

![Reimpostare le credenziali di distribuzione][configure03]

Il nome utente completo FTP è "app\nomeutente", dove *app* è il nome dell'app. Il nome utente è elencato nel pannello dell'app in **Informazioni di base**.

![Credenziali di distribuzione FTP][configure02]

## <a name="other-configuration-tasks"></a>Altre attività di configurazione
### <a name="ssl"></a>SSL
In modalità Basic o Standard è possibile caricare certificati SSL per un dominio personalizzato. Per altre informazioni, vedere [Abilitare HTTPS per un'app](app-service-web-tutorial-custom-ssl.md). 

Per visualizzare i certificati caricati, fare clic su **Tutte le impostazioni** > **Domini e SSL personalizzati**.

### <a name="domain-names"></a>Nomi di dominio
Aggiungere nomi di dominio personalizzati per la propria app. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato per un'app nel Servizio app di Azure](app-service-web-tutorial-custom-domain.md).

Per visualizzare i nomi di dominio, fare clic su **Tutte le impostazioni** > **Domini e SSL personalizzati**.

### <a name="deployments"></a>Deployments
* Configurare la distribuzione continua. Vedere [Uso di Git per distribuire app nel Servizio app di Azure](deploy-local-git.md).
* Slot di distribuzione. Vedere [Configurare gli ambienti di gestione temporanea nel Servizio app di Azure].

Per visualizzare gli slot di distribuzione, fare clic su **Tutte le impostazioni** > **Slot di distribuzione**.

### <a name="monitoring"></a>Monitoraggio
In modalità Basic o Standard è possibile testare la disponibilità degli endpoint HTTP o HTTPS, da un numero massimo di tre posizioni geograficamente distribuite. Un test di monitoraggio ha esito negativo se il codice della risposta HTTP è un errore (4xx o 5xx) o se la risposta richiede più di 30 secondi. Un endpoint è considerato disponibile se il test di monitoraggio ha esito positivo da tutte le posizioni specificate. 

Per ulteriori informazioni, consultare [Procedura: Monitorare lo stato degli endpoint].

## <a name="next-steps"></a>Passaggi successivi
* [Configurare un nome di dominio personalizzato nel servizio app di Azure]
* [Abilitare HTTPS per un'app in Azure App Service]
* [Aumentare le prestazioni di un'app nel Servizio app di Azure]
* [Informazioni di base sul monitoraggio nel Servizio app di Azure]
* [Modificare le impostazioni di applicationHost. config con xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Portale di Azure]: https://portal.azure.com/
[Configurare un nome di dominio personalizzato nel servizio app di Azure]: ./app-service-web-tutorial-custom-domain.md
[Configurare gli ambienti di gestione temporanea nel Servizio app di Azure]: ./deploy-staging-slots.md
[Abilitare HTTPS per un'app in Azure App Service]: ./app-service-web-tutorial-custom-ssl.md
[Procedura: Monitorare lo stato degli endpoint]: https://go.microsoft.com/fwLink/?LinkID=279906
[Informazioni di base sul monitoraggio nel Servizio app di Azure]: ./web-sites-monitor.md
[modalità pipeline]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Aumentare le prestazioni di un'app nel Servizio app di Azure]: ./web-sites-scale.md

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
