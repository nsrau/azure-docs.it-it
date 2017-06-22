---
title: Guida tecnica alla migrazione di soluzioni EDI di BizTalk Server a Servizi BizTalk | Documentazione Microsoft
description: Eseguire la migrazione da EDI a MABS, Servizi BizTalk di Microsoft Azure
services: biztalk-services
documentationcenter: na
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 61c179fa-3f37-495b-8016-dee7474fd3a6
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 1b70fc3d199d7f1521acb534dafec8fb3e69500e
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---
# <a name="migrating-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>Migrazione di soluzioni EDI di BizTalk Server nei servizi BizTalk: guida tecnica

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Autori: Tim Wieman e Nitin Mehrotra

Revisore: Karthik Bharthy

Scritto con: Servizi BizTalk di Microsoft Azure, versione di febbraio 2014.

## <a name="introduction"></a>Introduzione
Electronic Data Interchange (EDI) è uno dei metodi più diffusi per lo scambio elettronico di dati aziendali, noto anche come transazioni business-to-business o B2B. Il supporto EDI è stato incluso in BizTalk Server per oltre un decennio, a partire dalla versione iniziale di BizTalk Server. Con i servizi BizTalk, Microsoft continua a supportare le soluzioni EDI nella piattaforma Microsoft Azure. Le transazioni B2B sono in genere esterne a un'organizzazione e pertanto è più facile implementarle se sono state implementate in una piattaforma cloud. Microsoft Azure fornisce questa funzionalità tramite i servizi BizTalk.

Mentre alcuni clienti considerano i servizi BizTalk come una piattaforma "vergine" per le nuove soluzioni EDI, molti clienti hanno attualmente soluzioni EDI di BizTalk Server che desiderano migrare in Azure. Poiché EDI dei servizi BizTalk è progettato in base alle stesse entità chiave dell’architettura EDI di BizTalk Server (partner commerciali, entità e contratti), è possibile eseguire la migrazione degli elementi EDI di BizTalk Server nei servizi BizTalk.

In questo documento vengono illustrate alcune differenze correlate alla migrazione di elementi EDI di BizTalk Server nei servizi BizTalk. In questo documento si presuppone si abbia una conoscenza pratica dell'elaborazione EDI di BizTalk Server e dei contratti per partner commerciali. Per ulteriori informazioni su EDI di BizTalk Server, vedere [Gestione dei trading partner mediante BizTalk Server](https://msdn.microsoft.com/library/bb259970.aspx).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>Quale versione di elementi EDI di BizTalk Server può essere migrata ai servizi BizTalk?
Il modulo EDI di BizTalk Server è stato migliorato in modo significativo per BizTalk Server 2010, quando il prodotto è stato rimodellato per includere partner, profili e contratti. I servizi BizTalk utilizzano lo stesso modello per organizzare i partner commerciali e le divisioni aziendali all'interno dei partner. Di conseguenza, la migrazione di elementi EDI da BizTalk Server 2010 e versioni successive ai servizi BizTalk, è un processo molto più semplice. Per eseguire la migrazione di elementi EDI associati alle versioni precedenti a BizTalk Server 2010, è necessario eseguire prima l'aggiornamento a BizTalk Server 2010 e quindi eseguire la migrazione degli elementi EDI nei sevizi BizTalk.

## <a name="scenariosmessage-flow"></a>Scenari e flusso messaggi
Come con BizTalk Server, l’elaborazione EDI nei servizi BizTalk si basa su una soluzione di gestione dei partner commerciali (TPM, Trading Partner Management). La soluzione TPM ha i seguenti componenti chiave:

* I partner commerciali, che rappresentano l'organizzazione in una transazione B2B.
* I profili, che rappresentano divisioni all'interno di un partner commerciale.
* I contratti tra partner commerciali (o contratti), che rappresentano il contratto aziendale tra due partner o profili.

Nella figura seguente vengono illustrate le analogie e le differenze tra una soluzione EDI di BizTalk Server e una soluzione EDI dei servizi BizTalk:

![][EDImessageflow]

Le principali differenze e analogie tra un flusso della soluzione EDI in BizTalk Server e nei servizi BizTalk sono:

* Analogamente a BizTalk Server che usa una pipeline EDIReceive per ricevere un messaggio EDI e una pipeline EDISend per inviare un messaggio EDI, i servizi BizTalk utilizzano un bridge di ricezione EDI per ricevere e un bridge di invio EDI per inviare i messaggi EDI. In BizTalk Server, le pipeline sono associate a un contratto mediante porte di invio o di ricezione. Nei servizi BizTalk, il contratto stesso denota il bridge di invio o di ricezione.
* In BizTalk Server dopo che la pipeline EDIReceive elabora il messaggio EDI, il messaggio viene archiviato in un database SQL Server. La pipeline EDISend preleva quindi il messaggio dal database di SQL Server, lo elabora e lo invia al partner commerciale.
  
    Nei servizi BizTalk, dopo che il bridge di ricezione EDI elabora il messaggio EDI, il messaggio viene instradato a un processo esterno. Il processo esterno può essere eseguito in Microsoft Azure o in locale. Il processo esterno deve indirizzare il messaggio al bridge di invio EDI. Il bridge di invio non esegue implicitamente il pull del messaggio. Dopo l'elaborazione del messaggio, il bridge di invio EDI instrada il messaggio al partner commerciale.

I servizi BizTalk forniscono un'esperienza di configurazione facile da utilizzare in modo da creare e distribuire rapidamente un contratto B2B tra partner commerciali senza configurare istanze di calcolo di Microsoft Azure (ruoli Web o di lavoro), database SQL di Microsoft Azure o account di archiviazione di Microsoft Azure. Scenari più complessi richiedono l'associazione di flussi di lavoro o altre elaborazioni del servizio "ai bordi" di un contratto tra partner commerciali, ovvero prima o dopo l'elaborazione del bridge EDI del contratto tra partner commerciali. In dettaglio, durante l'elaborazione nei servizi BizTalk di un messaggio EDI si verificano le sequenze di eventi seguenti.

1. Un messaggio EDI viene ricevuto dal partner commerciale Fabrikam.  Per la ricezione di messaggi EDI dai partner commerciali, i servizi BizTalk supportano i protocolli di trasporto, quali FTP, SFTP, AS2 e HTTP/S.
2. L'elaborazione di ricezione del contratto tra partner commerciali disassembla il messaggio EDI nel formato XML.  È possibile instradare il messaggio EDI disassemblato (nel formato XML) agli endpoint del bus di servizio, ad esempio un endpoint di inoltro del bus di servizio, un argomento del bus di servizio, una coda del bus di servizio o un bridge dei servizi BizTalk.
3. I messaggi XML disassemblati possono quindi essere ricevuti dall'endpoint per un'altra elaborazione personalizzata.  Questi endpoint possono essere elaborati da un componente locale o da un'istanza di calcolo di Microsoft Azure per elaborare ulteriormente il messaggio in un servizio, ad esempio Windows Workflow (WF) o Windows Communication Foundation (WCF).
4. L’elaborazione di invio del contratto tra partner commerciali quindi assembla il messaggio XML nel formato EDI e lo invia al partner commerciale, ad esempio Contoso.  Per l'invio di messaggi EDI ai partner commerciali, i servizi BizTalk supportano gli stessi protocolli utilizzati per la ricezione di messaggi EDI.

In questo documento vengono inoltre fornite linee guida concettuali per la migrazione di diversi elementi EDI di BizTalk Server nei servizi di BizTalk.

## <a name="sendreceive-ports-to-trading-partners"></a>Porte di invio/ricezione per partner commerciali
In BizTalk Server, si impostano gli indirizzi di ricezione e le porte di ricezione per ricevere i messaggi EDI/XML dai partner commerciali e si impostano le porte di invio per inviare i messaggi EDI/XML al partner commerciale. È quindi possibile collegare tali porte a un contratto tra partner commerciali utilizzando la console di amministrazione BizTalk Server. Nei servizi BizTalk, gli indirizzi in cui si ricevono i messaggi dai partner commerciali e si inviano i messaggi ai partner commerciali vengono configurati come parte del contratto tra partner commerciali stesso (come parte delle impostazioni di trasporto) nel portale dei servizi BizTalk.  Pertanto  nei servizi BizTalk in effetti non esiste un concetto di "porte di invio" e "indirizzi di ricezione". Per altre informazioni, vedere [Creare contratti](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## <a name="pipelines-bridges"></a>Pipeline (bridge)
In EDI di BizTalk Server, le pipeline sono entità di elaborazione dei messaggi che possono anche includere una logica personalizzata per specifiche funzionalità di elaborazione, come richiesto dall'applicazione. Per i servizi BizTalk, l'equivalente sarebbe un bridge EDI. Tuttavia nei servizi BizTalk, per ora i bridge EDI sono "chiusi".  Pertanto, non è possibile aggiungere attività personalizzate a un bridge EDI. Qualsiasi elaborazione personalizzata deve essere effettuata all'esterno del bridge EDI nell'applicazione, prima o dopo l’ingresso del messaggio nel bridge configurato come parte del contratto tra partner commerciali. I bridge EAI possono eseguire un'elaborazione personalizzata. Se si desidera un'elaborazione personalizzata, è possibile utilizzare i bridge EAI prima o dopo che il messaggio viene elaborato dal bridge EDI. Per ulteriori informazioni, vedere [Come includere un codice personalizzato nei bridge](https://msdn.microsoft.com/library/azure/dn232389.aspx).

È possibile inserire un flusso di pubblicazione/sottoscrizione con un codice personalizzato e/o tramite gli argomenti e le code di messaggistica del bus di servizio prima che il contratto tra partner commerciale riceva il messaggio o dopo che il contratto ha elaborato e instradato il messaggio a un endpoint del bus di servizio.

Vedere **Scenari e flusso messaggi** in questo argomento per il modello del flusso messaggi.

## <a name="agreements"></a>Contratti
Se già si conoscono i contratti tra partner commerciali di BizTalk Server 2010 utilizzati per l'elaborazione EDI, i contratti tra partner commerciali dei servizi BizTalk risulteranno familiari. La maggior parte delle impostazioni del contratto sono uguali e utilizzano la stessa terminologia. In alcuni casi, le impostazioni del contratto sono molto più semplici rispetto alle stesse impostazioni di BizTalk Server. I servizi BizTalk di Microsoft Azure supportano il trasporto X12, EDIFACT e AS2.

I servizi BizTalk di Microsoft Azure forniscono inoltre uno strumento di **migrazione dati TPM** per eseguire la migrazione di partner commerciali e contratti dal modulo di partner commerciali di BizTalk Server al portale dei servizi BizTalk. Lo strumento di migrazione dati TPM è disponibile come parte di un pacchetto di strumenti che può essere scaricato dalla pagina [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057). Il pacchetto include inoltre un file Leggimi che contiene le istruzioni per utilizzare lo strumento e le informazioni di base per risoluzione dei problemi dello strumento.

## <a name="schemas"></a>Schemi
I servizi BizTalk forniscono gli schemi EDI che possono essere utilizzati nelle soluzioni dei servizi BizTalk.  Inoltre, gli schemi EDI di BizTalk Server possono essere utilizzati anche con i servizi BizTalk perché il nodo radice dello schema EDI è lo stesso in BizTalk Server e nei servizi BizTalk. Pertanto, sarà possibile prendere direttamente gli schemi EDI di BizTalk Server e utilizzarli nelle soluzioni EDI sviluppate tramite i servizi BizTalk. È anche possibile scaricare gli schemi dalla pagina [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Mappe (trasformazioni)
Le mappe di BizTalk Server sono chiamate trasformazioni nei servizi BizTalk. La migrazione delle mappe da BizTalk Server nei servizi BizTalk potrebbe essere una delle attività più complesse da eseguire (a seconda della complessità della mappa). Lo strumento di mapping utilizzato per i servizi BizTalk è diverso da BizTalk Mapper. Anche se il mapper è essenzialmente lo stesso, il formato della mappa sottostante è diverso. I functoid (chiamati **operazioni di mapping** nei servizi BizTalk) disponibili per gli utenti sono diversi.  In effetti, non è possibile utilizzare direttamente una mappa BizTalk nei servizi BizTalk. Inoltre, non tutti i functoid disponibili in BizTalk Server sono disponibili come operazioni di mapping nei servizi BizTalk.

### <a name="new-transform-operations"></a>Nuove operazioni di trasformazione
Mentre l'elenco di operazioni di mapping di trasformazione disponibili potrebbe apparire piuttosto diverso dal mapper di BizTalk Server, le trasformazioni dei servizi BizTalk includono nuove modalità per eseguire le stesse attività. Ad esempio, le trasformazioni dei servizi BizTalk hanno le **operazioni di elenco** che non sono disponibili in BizTalk Mapper.  Le **operazioni di elenco** consentono di creare e usare un "elenco", dove per elenco si intende un set di elementi (noti anche come "righe") in cui ogni elemento può avere più membri (noti anche come "colonne").  È possibile ordinare l'elenco, selezionare gli elementi in base a una condizione e così via.

Un altro esempio di nuova funzionalità delle trasformazioni dei servizi BizTalk sono le **operazioni di ciclo**.  È difficile creare cicli annidati nel mapper di BizTalk Server.  Di conseguenza, le operazioni di mapping ciclo vengono aggiunte per le trasformazioni dei servizi BizTalk.

Un altro esempio è l’operazione di mapping dell’espressione **If-Then-Else** .  In BizTalk Mapper è possibile eseguire un'operazione if-then-else, ma richiede più functoid per completare un'attività apparentemente semplice.

### <a name="migrating-biztalk-server-maps"></a>Migrazione delle mappe di BizTalk Server
I servizi BizTalk di Microsoft Azure forniscono uno strumento per eseguire la migrazione delle mappe di BizTalk Server alle trasformazioni dei servizi BizTalk. Lo strumento **BTMMigrationTool** è disponibile come parte del pacchetto di **strumenti** fornito con il [download di BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057). Per ulteriori informazioni sullo strumento, vedere [Convertire una mappa BizTalk in una trasformazione dei servizi BizTalk](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

È inoltre possibile esaminare un esempio di Sandro Pereira, MVP di BizTalk, su come eseguire la [migrazione di mappe BizTalk Server a trasformazioni dei servizi BizTalk](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Orchestrazioni
Se è necessario eseguire la migrazione a Microsoft Azure dell’elaborazione di orchestrazioni di BizTalk Server, le orchestrazioni devono essere riscritte poiché Microsoft Azure non supporta l'esecuzione di orchestrazioni di BizTalk Server.  È possibile riscrivere la funzionalità di orchestrazione in un servizio di Windows Workflow Foundation 4.0 (WF4).  Si tratta di una riscrittura completa poiché non esiste attualmente la migrazione dalle orchestrazioni di BizTalk Server a WF4. Di seguito sono riportate alcune risorse per Windows Workflow:

* [*Come integrare un servizio del flusso di lavoro WCF con code e argomenti del bus di servizio*](https://msdn.microsoft.com/library/azure/hh709041.aspx) di Paolo Salvatori. 
* [Sessione *Building apps with Windows Workflow Foundation and Azure*](http://go.microsoft.com/fwlink/p/?LinkId=237314) (Compilazione di app con Windows Workflow Foundation e Azure) estratta dalla conferenza Build 2011.
* [*Centro per sviluppatori Windows Workflow Foundation*](http://go.microsoft.com/fwlink/p/?LinkId=237315) in MSDN.
* [*Documentazione di Windows Workflow Foundation 4 (WF4)*](https://msdn.microsoft.com/library/dd489441.aspx) in MSDN.

## <a name="other-considerations"></a>Altre considerazioni
Di seguito sono riportate alcune informazioni da tenere in considerazione quando si usano i servizi BizTalk.

### <a name="fallback-agreements"></a>Accordi di fallback
L’elaborazione EDI di BizTalk Server costituisce il concetto di "accordo di fallback".  I servizi BizTalk **non** hanno ancora un concetto di accordo di fallback.  Vedere gli argomenti della documentazione di BizTalk [Ruolo degli accordi nell'elaborazione EDI](http://go.microsoft.com/fwlink/p/?LinkId=237317) e [Configurazione delle proprietà dell'accordo globale o di fallback](https://msdn.microsoft.com/library/bb245981.aspx) per informazioni sull'uso di contratti di fallback in BizTalk Server.

### <a name="routing-to-multiple-destinations"></a>Routing a più destinazioni
I bridge dei servizi BizTalk nello stato attuale non supportano il routing dei messaggi a più destinazioni tramite il modello pubblicazione-sottoscrizione. Tuttavia, è possibile instradare i messaggi da un bridge dei servizi BizTalk a un argomento del bus di servizio che può avere più sottoscrizioni per ricevere il messaggio su più endpoint.

## <a name="conclusion"></a>Conclusione
I servizi BizTalk di Microsoft Azure vengono aggiornati a intervalli regolari per aggiungere ulteriori funzionalità e capacità. A ogni aggiornamento, viene offerto il supporto per le funzionalità migliorate in modo da facilitare la creazione di soluzioni end-to-end con i servizi BizTalk e le altre tecnologie Azure.

## <a name="see-also"></a>Vedere anche
[Sviluppo di applicazioni aziendali con Azure](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png

