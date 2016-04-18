<properties
   pageTitle="Domande frequenti"
   description=""
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# Domande frequenti su Power BI Embedded

1. **Cosa ha annunciato Microsoft su Power BI a BUILD?**

    Microsoft ha annunciato che ora è disponibile l'anteprima pubblica del servizio Microsoft Power BI Embedded.

2.	**Cos'è Microsoft Power BI Embedded?**

    Microsoft Power BI Embedded è un servizio di Azure che consente agli sviluppatori di applicazioni di incorporare report e visualizzazioni straordinarie e completamente interattive nelle app a uso dei clienti senza la necessità di perdere tempo o spendere denaro per costruire controlli personalizzati da zero. È possibile scegliere fra molti tipi di visualizzazioni dei dati predefinite o creare facilmente visualizzazioni personalizzate per soddisfare le esigenze specifiche dell'applicazione. Power BI Embedded è rivolto a fornitori di software indipendenti e sviluppatori di applicazioni che vogliono fornire BI ai loro clienti come parte di un'applicazione più grande.

3.	**Chi potrebbe usare Microsoft Power BI Embedded e perché?**

    Microsoft Power BI Embedded è rivolto agli sviluppatori di applicazioni che vogliono offrire esperienze di visualizzazione dei dati straordinarie e interattive ai loro utenti su qualsiasi dispositivo senza doverle creare loro stessi. Con Power BI Embedded gli sviluppatori possono mostrare visualizzazioni sempre aggiornate mediante **DirectQuery**. Gli sviluppatori possono anche effettuare la distribuzione, la gestione e l'automazione di Power BI a livello di codice con le API ARM di Azure e le API di Power BI. Come il servizio Power BI generico, Power BI Embedded viene dimensionato automaticamente in base all'uso e alle esigenze dell'applicazione. Il servizio Power BI Embedded è a pagamento in base al consumo e prevede un preciso modello di definizione dei prezzi. Per informazioni sui prezzi, vedere [In che modo sono definiti i prezzi del servizio?](#price).

4.	**Qual è la correlazione tra Power BI Embedded e il servizio Power BI?**

    Power BI Embedded e il servizio Power BI sono due offerte separate. Power BI Embedded è caratterizzato da un modello di fatturazione in base al consumo, è distribuito mediante il Portale di Azure ed è progettato in modo da consentire ai fornitori di software indipendenti di incorporare le visualizzazioni dei dati nelle applicazioni affinché i loro clienti possano usarle. Il servizio Power BI viene fatturato e distribuito tramite il portale di Office 365 ed è un'offerta autonoma per uso generico riservata prevalentemente all'uso interno nelle organizzazioni. Per altre informazioni sul servizio Power BI, visitare [www.powerbi.com](www.powerbi.com).

5.	**In che modo Power BI Embedded migliora le app?**

    Le applicazioni sono molto più potenti quando si sfruttano le visualizzazioni dei dati per consentire agli utenti di prendere decisioni consapevoli direttamente nell'applicazione. Power BI Embedded consente di perfezionate le app con visualizzazioni avanzate interattive e sempre aggiornate, migliorando così l'utilità delle app stesse, la soddisfazione e la fedeltà dei clienti e offrendo la possibilità di eseguire analisi contestuali con facilità su qualsiasi dispositivo.

6.	**Esistono regole o limitazioni per l'uso di Power BI Embedded nelle app?**

    È possibile usare il servizio Power BI Embedded all'interno di una propria applicazione solo se quest'ultima (1) aggiunge funzionalità principali e significative al servizio Power BI Embedded anziché essere essenzialmente un prodotto sostitutivo di qualche servizio Power BI e (2) viene fornita esclusivamente a utenti esterni all'azienda. Non è consentito usare il servizio Power BI Embedded in applicazioni aziendali interne.

7.	**Power BI Embedded può essere usato per creare applicazioni interne?**

    No, Power BI Embedded è riservato esclusivamente all'uso esterno e non può essere impiegato in applicazioni aziendali interne. Al fine di poter incorporare contenuti di Power BI da usare in applicazioni aziendali interne, è necessario ricorrere al servizio Power BI, inoltre tutti gli utenti che usano tali contenuti devono essere titolari di una licenza con sottoscrizione utente valida per Power BI gratuito o Power BI Pro. Altre informazioni sull'incorporamento interno mediante il servizio Power BI sono disponibili all'indirizzo [https://dev.powerbi.com](https://dev.powerbi.com).

8.	**Il servizio è disponibile a livello globale?**

    Il servizio Power BI Embedded è disponibile in America del Nord dal momento del nostro annuncio a BUILD 2016 (nel data center degli Stati Uniti centro-meridionali). Renderemo disponibile il servizio negli altri data center di Azure a breve.

9.	**Quale contratto di servizio è disponibile per il servizio?**

    Power BI Embedded è disponibile come servizio di Azure in anteprima senza un contratto di servizio formale. Il contratto di servizio sarà fornito quando il servizio passerà dalla fase di anteprima alla disponibilità generale.

<a name="price"/>
10.	**In che modo vengono definiti i prezzi del servizio?**

    Power BI Embedded è attualmente in versione di anteprima e sarà disponibile gratuitamente fino al 1° maggio 2016. A partire dal 1° maggio 2016 il servizio sarà a pagamento e il prezzo sarà basato sul numero dei rendering. I clienti possono acquistare il servizio tramite due strumenti di licenza principali: Microsoft Online Subscription Program (MOSP) o il programma per contratti multilicenza Enterprise.

    È possibile usare il servizio Power BI Embedded all'interno di una propria applicazione solo se quest'ultima (1) aggiunge funzionalità principali e significative al nostro servizio anziché essere essenzialmente un prodotto sostitutivo di qualche servizio Power BI e (2) viene fornita esclusivamente a utenti esterni all'azienda. Non è consentito usare il servizio Power BI Embedded in applicazioni aziendali interne.

    ![](media\power-bi-embedded-faq\price.png)

11.	**Che cos'è un rendering e come viene fatturato?**

    Un rendering si verifica quando una visualizzazione viene mostrata a un utente finale che invia una query al servizio. Il servizio Power BI tenta di mettere in cache il contenuto visualizzato quando è possibile, al fine di ridurre il numero di rendering addebitati all'applicazione. Alcune azioni dell'utente tuttavia, come l'applicazione di filtri, possono generare l'invio di una query al nostro servizio che equivale a un nuovo rendering.

    Ad esempio, se un utente mostra un report contenente quattro visualizzazioni, ciò equivale a quattro rendering. Se l'utente aggiorna il report e vengono inviate più query al servizio, vengono prodotti altri quattro rendering. Il proprietario del servizio potrà determinare i limiti oltre i quali le query prodotte dagli utenti finali comportano rendering a pagamento per limitare e ridurre i costi in scenari con dati statici.

    L'unità di fatturazione è pari a 1.000 rendering. Per le quantità inferiori a 1.000 rendering, la fatturazione è proporzionale. Ogni cliente riceve 1.000 rendering gratuiti al mese. I clienti che acquistano il servizio tramite contratti multilicenza devono consultare il proprio partner o rivenditore Microsoft per informazioni sui prezzi.

12.	**Sono disponibili strumenti o linee guida che consentono di stimare quanti rendering ci si deve aspettare? Come si fa a sapere quanti rendering sono stati eseguiti?**

    Il Portale di Azure fornirà i dettagli di fatturazione indicando quanti rendering sono stati eseguiti per la sottoscrizione.

13.	**C'è bisogno di una sottoscrizione di Power BI per sviluppare applicazioni con Power BI Embedded? Come iniziare?**

    Uno sviluppatore di applicazioni, in quanto tale, non ha bisogno di una sottoscrizione di Power BI per creare i report e le visualizzazioni da usare in un'applicazione. Sono necessarie una sottoscrizione di Microsoft Azure e l'applicazione gratuita Power BI Desktop.

    Per i dettagli su come usare il servizio Power BI Embedded, vedere la documentazione del nostro servizio.

14.	**Se si dispone di una sottoscrizione di Azure, è possibile usare Power BI Embedded mediante la sottoscrizione esistente?**

    Sì. È possibile usare una sottoscrizione di Azure esistente per ottenere e usare il servizio Microsoft Power BI Embedded.

15.	**L'utente finale dell'applicazione ha bisogno di una licenza Power BI?**

    No. Gli utenti finali dell'applicazione non devono acquistare una sottoscrizione Power BI a parte per accedere alle visualizzazioni dei dati all'interno dell'app. Nel modello di Power BI Embedded al provider dell'applicazione sarà fatturato il servizio in base al consumo registrato di Azure. Vedere la [pagina relativa alla definizione dei prezzi e alle licenze](http://go.microsoft.com/fwlink/?LinkId=760527).

16.	**Come funziona l'autenticazione degli utenti con Power BI Embedded?**

    Il servizio Power BI Embedded usa token di applicazione per l'autenticazione e l'autorizzazione anziché usare l'autenticazione esplicita dell'utente finale. Nel modello con token di applicazione è l'applicazione a gestire l'autenticazione e l'autorizzazione degli utenti finali. Quindi l'applicazione crea e invia i token di applicazione al nostro servizio richiedendo di fatto il rendering del report. Questo modello non richiede che l'applicazione usi Azure Active Directory per l'autenticazione e l'autorizzazione dell'utente, sebbene sia possibile. Per altre informazioni sui token di applicazione, vedere la pagina della documentazione relativa ai [token di applicazione](https://azure.microsoft.com/it-IT/documentation/articles/power-bi-embedded-get-started-sample/#key-flow).

17.	**Quali origini dati sono supportate attualmente con Power BI Embedded?**

    Durante la fase di anteprima pubblica del servizio supporteremo l'accesso alle origini dati su cloud che usano credenziali di base mediante DirectQuery. Perciò le origini dati come i database SQL di Azure, HDInsight Spark e i data warehouse SQL di Azure sono già supportate. Verrà aggiunto il supporto per altre origini dati e altri tipi di accesso nei prossimi mesi. Annunceremo le nuove origini dati supportate sul sito di Power BI dedicato agli sviluppatori all'indirizzo [http://dev.powerbi.com](http://dev.powerbi.com/).

18.	**Come funziona il modello di tenancy per Power BI Embedded?**

    Nel modello di Power BI Embedded non c'è lo specifico requisito che i clienti si trovino in tenant di Azure Active Directory (Azure AD). È possibile scegliere di richiedere o meno Azure AD per i clienti. Di conseguenza, l'architettura dell'applicazione e l'infrastruttura sono gli elementi che determineranno il modello di tenancy richiesto per Power BI Embedded.

    Gli sviluppatori/dipendenti che lavorano all'applicazione dovranno avere un account utente di Azure AD per poter gestire la sottoscrizione di Azure e le raccolte di aree di lavoro mediante il Portale di Azure. Le API a livello di codice che consentono agli sviluppatori di importare report, modificare stringhe di connessione e ottenere URL incorporati usano invece i token di applicazione per l'autenticazione e perciò non richiedono Azure AD. Informazioni dettagliate su come usare le API e il Portale di Azure sono disponibili alla [pagina della documentazione su Power BI Embedded in Azure.com](https://azure.microsoft.com/it-IT/documentation/services/power-bi-embedded/).

19.	**Altre informazioni**

    È possibile visitare la [pagina della documentazione di Power BI Embedded](http://go.microsoft.com/fwlink/?LinkId=760526). Per rimanere aggiornati sul servizio visitare il [blog su Power BI dedicato agli sviluppatori](http://blogs.msdn.com/powerbidev) o il centro di sviluppo dedicato a Power BI all'indirizzo dev.powerbi.com. È anche possibile porre domande a [Stackoverflow](http://stackoverflow.com/questions/tagged/powerbi).

20.	**Come iniziare?**

    Ora è possibile iniziare gratuitamente! Se si dispone di una sottoscrizione di Azure, è possibile ottenere Power BI Embedded direttamente dal Portale di Azure. È anche possibile creare un [account Azure gratuito](https://azure.microsoft.com/free/). Dopo aver ottenuto il servizio Power BI Embedded, è possibile usare direttamente e con facilità le API REST di Power BI o usare l'SDK di programmazione disponibile su [GitHub](http://go.microsoft.com/fwlink/?LinkID=746472) . Sono disponibili esempi di come sfruttare l'SDK di sviluppo.

## Vedere anche

- [Cos'è Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [Introduzione all'anteprima di Microsoft Power BI Embedded](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0406_2016-->