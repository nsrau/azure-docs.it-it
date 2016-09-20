<properties
   pageTitle="Domande frequenti"
   description="Domande frequenti su Power BI Embedded"
   services="power-bi-embedded"
   documentationCenter=""
   authors="mgblythe"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/05/2016"
   ms.author="mblythe"/>

# Domande frequenti su Power BI Embedded

## Qual è l'annuncio più recente relativo a Power BI Embedded?

Il servizio Microsoft Power BI Embedded è ora disponibile a livello generale con Contratto di servizio Standard di disponibilità generale. Per altre informazioni su questa versione, vedere [What's new in Power BI Embedded](power-bi-embedded-whats-new.md) (Novità di Power BI Embedded).

## Cos'è Microsoft Power BI Embedded?

Microsoft Power BI Embedded è ora disponibile a livello generale. Power BI Embedded è un servizio di Azure che consente agli sviluppatori di applicazioni di incorporare report e visualizzazioni straordinarie e completamente interattive nelle app a uso dei clienti senza la necessità di perdere tempo o spendere denaro per costruire controlli personalizzati da zero. Power BI Embedded è ora disponibile con un Contratto di servizio in 9 data center in tutto il mondo. Sono state anche migliorate le funzionalità del servizio, ad esempio il supporto per la sicurezza dei dati tramite la sicurezza a livello di riga in Power BI Embedded per i filtri avanzati. È stato inoltre semplificato e aggiornato il modello tariffario di Power BI Embedded. Per altre informazioni, vedere [Power BI Embedded](https://azure.microsoft.com/services/power-bi-embedded/).

## Chi potrebbe usare Microsoft Power BI Embedded e perché?

Microsoft Power BI Embedded è rivolto agli sviluppatori di applicazioni che vogliono offrire esperienze di visualizzazione dei dati straordinarie e interattive ai loro utenti su qualsiasi dispositivo senza doverle creare loro stessi. Con Power BI Embedded gli sviluppatori possono mostrare visualizzazioni sempre aggiornate mediante DirectQuery. Gli sviluppatori possono anche effettuare la distribuzione e gestire l'automazione di Power BI a livello di codice con le API di Azure Resource Manager e le API di Power BI. Come il servizio Power BI generico, Power BI Embedded viene dimensionato automaticamente in base all'uso e alle esigenze dell'applicazione. Il servizio Power BI Embedded è a pagamento in base al consumo e prevede un preciso modello di definizione dei prezzi.

## Qual è la correlazione tra Power BI Embedded e il servizio Power BI?

Power BI Embedded e il servizio Power BI sono due offerte separate. Power BI Embedded è caratterizzato da un modello di fatturazione in base al consumo, è distribuito mediante il Portale di Azure ed è progettato in modo da consentire ai fornitori di software indipendenti di incorporare le visualizzazioni dei dati nelle applicazioni affinché i loro clienti possano usarle. Il servizio Power BI viene fatturato e distribuito tramite il portale di Office 365 ed è un'offerta autonoma per uso generico riservata prevalentemente all'uso interno nelle organizzazioni. Per altre informazioni sul servizio Power BI, vedere [www.powerbi.com](https://powerbi.microsoft.com).

## In che modo Power BI Embedded migliora le app?

Le applicazioni sono molto più potenti quando è possibile sfruttare visualizzazioni dei dati accattivanti e interattive per consentire agli utenti di prendere decisioni consapevoli direttamente nell'applicazione. Power BI Embedded consente di perfezionate le app con visualizzazioni avanzate interattive e sempre aggiornate, migliorando così l'utilità delle app stesse, la soddisfazione e la fedeltà dei clienti e offrendo la possibilità di eseguire analisi contestuali con facilità su qualsiasi dispositivo.

## Esistono regole o limitazioni per l'uso di Power BI Embedded nelle app?

Power BI Embedded è destinato alle applicazioni fornite per l'uso da parte di terzi. Se si vuole usare il servizio Power BI Embedded per creare un'applicazione aziendale interna, ogni utente interno dovrà avere una licenza di sottoscrizione utente per Power BI Pro e all'organizzazione verranno addebitati i costi per l'utilizzo del servizio Power BI Embedded in aggiunta alle tariffe della licenza di sottoscrizione utente per Power BI Pro. Per evitare che si debbano pagare sia le tariffe della licenza di sottoscrizione utente per Power BI Pro sia i costi per l'utilizzo di Power BI Embedded per le applicazioni interne, il servizio Power BI offre funzionalità di incorporamento di contenuti al di fuori di Power BI Embedded senza costi aggiuntivi per gli utenti che hanno una licenza di sottoscrizione utente per Power BI (dev.powerbi.com).

## Power BI Embedded può essere usato per creare applicazioni interne?

No, Power BI Embedded è riservato esclusivamente all'uso esterno e non deve essere impiegato in applicazioni aziendali interne. Al fine di poter incorporare contenuti di Power BI da usare in applicazioni aziendali interne, è necessario ricorrere al servizio Power BI, inoltre tutti gli utenti che usano tali contenuti devono essere titolari di una licenza con sottoscrizione utente valida per Power BI gratuito o Power BI Pro. Per altre informazioni su come incorporare applicazioni interne nel servizio Power BI, vedere [https://dev.powerbi.com](https://dev.powerbi.com).

## Il servizio è disponibile a livello globale?

Il servizio Power BI Embedded è attualmente disponibile nella maggior parte dei data center. Le informazioni più recenti sulla disponibilità sono disponibili [qui](https://azure.microsoft.com/status/).

## Quale contratto di servizio è disponibile per il servizio?

Power BI Embedded con Contratto di servizio di Azure Standard. Per altre informazioni, vedere [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

## In che modo vengono definiti i prezzi del servizio?

Per informazioni sui prezzi, vedere [Prezzi di Power BI Embedded](http://go.microsoft.com/fwlink/?LinkId=760527).

## Che cos'è un rendering e come viene fatturato?

>[AZURE.NOTE] Durante la fase di anteprima di Power BI Embedded sono stati applicati prezzi scontati per rendering, ma in seguito ai commenti e suggerimenti degli utenti questo modello verrà sospeso a favore di un calcolo dei prezzi per sessione. La transizione dai prezzi per rendering a quelli per sessione avverrà il 1° settembre 2016.

Un rendering è un elemento visivo visualizzato a un utente finale come conseguenza di una query al servizio. Se ad esempio un utente visualizza un report che contiene 4 oggetti visivi, vengono prodotti 4 rendering. Se l'utente aggiorna il report e al servizio vengono inviate più query, vengono prodotti altri 4 rendering. Il proprietario del servizio potrà determinare i limiti oltre i quali le query prodotte dagli utenti finali comportano rendering a pagamento per limitare e ridurre i costi in scenari con dati statici.

L'unità di fatturazione è pari a 1.000 rendering. Per le quantità inferiori a 1.000 rendering, la fatturazione è proporzionale. Ogni cliente riceve 1.000 rendering gratuiti al mese. I clienti che acquistano il servizio tramite contratti multilicenza devono consultare il proprio partner o rivenditore Microsoft per informazioni sui prezzi.

## Che cos'è una sessione di report e come viene fatturata?

Una sessione è un set di interazioni tra un utente finale e un report di Power BI Embedded. Ogni volta che un report di Power BI Embedded viene visualizzato da un utente, viene avviata una sessione, che viene addebitata al titolare della sottoscrizione. Le sessioni vengono fatturate a un prezzo fisso, indipendentemente dal numero di elementi visivi in un report o dalla frequenza di aggiornamento del contenuto del report. Una sessione termina quando l'utente chiude il report o quando si verifica il timeout della sessione dopo un'ora.

## Sono disponibili strumenti o linee guida che consentono di stimare quanti rendering/sessione ci si deve aspettare? Come si fa a sapere quanti rendering sono stati eseguiti?

Il Portale di Azure fornirà i dettagli di fatturazione indicando quanti rendering/sessioni di report sono stati eseguiti per la sottoscrizione.

## C'è bisogno di una sottoscrizione di Power BI per sviluppare applicazioni con Power BI Embedded? Come iniziare?

Uno sviluppatore di applicazioni, in quanto tale, non ha bisogno di una sottoscrizione di Power BI per creare i report e le visualizzazioni da usare in un'applicazione. Sono necessarie una sottoscrizione di Microsoft Azure e l'applicazione gratuita Power BI Desktop.

Per i dettagli su come usare il servizio Power BI Embedded, vedere la documentazione del nostro servizio.

## Se si ha una sottoscrizione di Azure, è possibile usare Power BI Embedded mediante la sottoscrizione esistente?

Sì. È possibile usare una sottoscrizione di Azure esistente per ottenere e usare il servizio Microsoft Power BI Embedded.

## L'utente finale dell'applicazione ha bisogno di una licenza Power BI?

No. Gli utenti finali dell'applicazione non devono acquistare una sottoscrizione Power BI a parte per accedere alle visualizzazioni dei dati all'interno dell'app. Nel modello di Power BI Embedded all'utente, in quanto provider dell'applicazione, sarà fatturato il servizio in base al consumo registrato di Azure. Vedere [Prezzi di Power BI Embedded](http://go.microsoft.com/fwlink/?LinkId=760527).

## Come funziona l'autenticazione degli utenti con Power BI Embedded?

Il servizio Power BI Embedded usa token dell'app per l'autenticazione e l'autorizzazione anziché usare l'autenticazione esplicita dell'utente finale. Nel modello con token di applicazione è l'applicazione a gestire l'autenticazione e l'autorizzazione degli utenti finali. Quando necessario, quindi, l'app crea

e invia i token dell'app al servizio richiedendo di fatto il rendering del report. Questo modello non richiede che l'applicazione usi Azure Active Directory per l'autenticazione e l'autorizzazione dell'utente, sebbene sia possibile. Altre informazioni sui token dell'app sono disponibili [qui](power-bi-embedded-app-token-flow.md). È stata anche introdotta la funzionalità sicurezza a livello di riga in Power BI Embedded per scenari con filtri di sicurezza avanzati.

## Quali origini dati sono supportate attualmente con Power BI Embedded?

Verrà supportato l'accesso alle origini dati su cloud che usano credenziali di base mediante DirectQuery. Le origini dati come i database SQL di Azure e i data warehouse SQL di Azure sono quindi già supportate. Verrà aggiunto il supporto per altre origini dati e altri tipi di accesso nei prossimi mesi. Le nuove origini dati supportate verranno annunciate sul forum di Power BI dedicato agli sviluppatori all'indirizzo [https://dev.powerbi.com](https://dev.powerbi.com/).

## Come funziona il modello di tenancy per Power BI Embedded?

Il modello Power BI Embedded non include alcun requisito specifico relativo alla necessità che i clienti si trovino nei tenant di Azure AD. È possibile scegliere di richiedere o meno Azure AD per i clienti. Di conseguenza, l'architettura dell'applicazione e l'infrastruttura sono gli elementi che determineranno il modello di tenancy richiesto per Power BI Embedded.

Gli sviluppatori/dipendenti che lavorano all'applicazione dovranno avere un account utente di Azure AD quando devono gestire la sottoscrizione di Azure e le raccolte di aree di lavoro mediante il portale di Azure. Le API a livello di codice consentono agli sviluppatori di importare report, modificare stringhe di connessione e ottenere URL incorporati usano invece i token dell'app per l'autenticazione e perciò non richiedono Azure AD. Per informazioni dettagliate su come usare le API e il portale di Azure, vedere la [documentazione su Power BI Embedded](https://azure.microsoft.com/documentation/services/power-bi-embedded/) in Azure.com.

## Altre informazioni

È possibile visitare la [pagina della documentazione su Power BI Embedded](http://go.microsoft.com/fwlink/?LinkId=760526). Per rimanere aggiornati sul servizio, visitare il [blog su Power BI dedicato agli sviluppatori](http://blogs.msdn.com/powerbidev) o il centro per sviluppatori di Power BI all'indirizzo dev.powerbi.com. È anche possibile porre domande su [Stackoverflow](http://stackoverflow.com/questions/tagged/powerbi).

## Come iniziare?

Ora è possibile iniziare gratuitamente! Se si dispone di una sottoscrizione di Azure, è possibile ottenere Power BI Embedded direttamente dal Portale di Azure. È anche possibile creare un [account di Azure gratuito](https://azure.microsoft.com/free/). Dopo aver ottenuto il servizio Power BI Embedded, è possibile usare direttamente e con facilità le API REST di Power BI o usare l'SDK di programmazione disponibile su [GitHub](http://go.microsoft.com/fwlink/?LinkID=746472). Sono disponibili esempi di come sfruttare l'SDK di sviluppo.

## Vedere anche

- [Cos'è Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [Introduzione a Microsoft Power BI Embedded](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0907_2016-->