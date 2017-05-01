---
title: Guida per i venditori di Microsoft Azure Marketplace | Documentazione Microsoft
description: "Questa guida è destinata agli utenti aziendali e ai responsabili di prodotto presso i fornitori di software indipendenti (ISV) che desiderano vendere le loro immagini certificate della macchina virtuale di Azure a professionisti IT e sviluppatori."
documentationcenter: 
author: rupeshazure
manager: hamidm
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: rupesk@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: aa6ae2b7e6ad0c6f91503d026ec8fd6933b6cfbb
ms.lasthandoff: 04/12/2017


---
# <a name="azure-marketplace-sellers-guide"></a>Guida per i venditori di Azure Marketplace

Introduzione alla guida per i venditori di Microsoft Azure Marketplace. Questa guida è destinata agli utenti aziendali e ai responsabili di prodotto presso i fornitori di software indipendenti (ISV) che desiderano vendere le loro immagini certificate della macchina virtuale di Azure a professionisti IT e sviluppatori. Con clienti Azure in tutto il mondo, l'[Azure Marketplace](https://azuremarketplace.microsoft.com/) può offrire grande copertura ed esposizione per i prodotti.


> [!NOTE]
> Se si è interessati a vendere i prodotti software-as-a-service (SaaS) finiti agli utenti aziendali, è possibile esaminare le opzioni per elencarli in [AppSource](https://appsource.microsoft.com).

Al termine di questa guida, si saprà dove trovare altre informazioni più dettagliate sui seguenti elementi:

- Cos'è Azure Marketplace?
- Come determinare se il prodotto soddisfa l'Azure Marketplace?
- Quali sono i vantaggi della vendita su Azure Marketplace?
- Quali sono i prerequisiti (tecnici e non tecnici) per la vendita in Azure Marketplace?
- Come è possibile creare dei dischi rigidi virtuali (VHD) compatibili con Azure?
- Come fare richiesta e registrarsi come venditore?
- Come si crea e si pubblica un'offerta?
- Come iniziare a vendere e trovare le risorse disponibili?
- Che tipo di creazione di report e informazioni dettagliate fornisce Azure Marketplace?
- Dove è possibile ottenere la guida e il supporto tecnico?

Ecco come procedere.

## <a name="whats-the-azure-marketplace"></a>Cos'è Azure marketplace?

Azure Marketplace è un marketplace online per applicazioni e servizi che consente ai fornitori di software indipendenti (ISV), dalle startup alle grandi imprese, di offrire le proprie soluzioni a clienti di Azure in tutto il mondo. Azure Marketplace consente di distribuire e vendere, in qualità di publisher di Azure, l'immagine innovativa della macchina virtuale ad altri sviluppatori, ISV e professionisti IT che vogliono sviluppare rapidamente applicazioni basate su cloud e soluzioni per dispositivi mobili in Azure. Azure Marketplace supporta una gamma di offerte, quali applicazioni analitiche dei dati end-to-end con elaborazione dei dati, archiviazione dei dati e analisi dei livelli o app di e-commerce a più livelli (dati, servizio e Internet).

I clienti cloud affrontano numerose sfide durante la ricerca delle soluzioni più appropriate da adattare alle proprie esigenze. Azure Marketplace fornisce un meccanismo per risolvere questi problemi e collega i clienti con soluzioni ISV innovative, come illustrato nella tabella seguente.

| Esigenze del cliente | Soluzione di Azure Marketplace |
| --- | --- |
| Richiede la funzionalità della piattaforma cloud aggiuntiva per soddisfare le esigenze aziendali e tecniche | Offre un crescente portfolio di applicazioni complementari e di servizi in Azure |
| Risulta difficile scoprire l'applicazione o il servizio corretti | Offre un'unica risorsa completa per individuare, cercare e acquistare applicazioni e servizi |
| È necessario un meccanismo di distribuzione scalabile per i servizi e le applicazioni di terze parti | Consente la creazione e la configurazione di distribuzioni scalabili per servizi e applicazioni di terze parti |
| Richiede nuove applicazioni e servizi da integrare e su cui lavorare con le soluzioni esistenti | Integra facilmente servizi e applicazioni di terze parti con soluzioni esistenti in Azure |

Azure Marketplace offre ai clienti globali qualità, scelta e forza dell'ecosistema del partner Azure. Ecco alcuni vantaggi chiave:

- Posizione centralizzata per le offerte basate su Azure da Microsoft e partner
- Oltre 5.000 offerte
- Esperienza integrata sulla piattaforma
- Configurazione, distribuzione e gestione semplificate

## <a name="is-the-azure-marketplace-right-for-my-business"></a>Azure Marketplace è adatto alla mia azienda?

A questo punto è lecito chiedersi se Azure Marketplace sia la scelta adatta per l'azienda e, se lo è, cosa se ne può ricavare? Azure Marketplace consente di creare nuove opportunità di vendita per offrire ai clienti software e servizi disponibili in Azure:

- **Vendere in nuovi mercati con un portfolio ampliato di soluzioni in Azure**: si tratta dell'opportunità di usare upselling e cross-selling delle offerte del Marketplace con i servizi di Azure disponibili per i clienti Microsoft Online Subscription Program (MOSP) e Microsoft Enterprise Agreement. È possibile incorporare facilmente offerte del Marketplace nella soluzione dei clienti e nello scenario di Azure.
- **Migliorare il valore aziendale e aumentare le dimensioni dell'offerta con account di clienti nuovi ed esistenti**: Azure Marketplace può aiutare ad aumentare la dimensione dell'offerta, a risolvere i punti deboli del cliente durante lo spostamento dei carichi di lavoro nel cloud e ad aumentare la redditività dell'offerta. In breve, è possibile aumentare il valore dell'azienda vendendo soluzioni complete e risolvendo le falle della piattaforma cloud di Azure per soddisfare le richieste del cliente.
- **Ricorrere a una più vasta gamma di potenziali clienti vendendo applicazioni e servizi e del Marketplace**: Azure Marketplace può rendere più semplice trovare e mantenere i nuovi clienti. Molte aziende oggi hanno bisogno di carichi di lavoro di transizione al cloud e si adattano ad ambienti di infrastruttura in continua evoluzione. È possibile fornire servizi e applicazioni adeguati per aiutare i clienti a colmare il divario.
- **Integrare ed estendere la funzionalità di Azure raggruppando le offerte con i servizi di Azure**: Azure Marketplace può aiutare nelle conversazioni con i clienti basate sullo scenario, favorendo una connessione reale e concreta. È anche possibile risolvere le falle specifiche della piattaforma e le esigenze del cliente parlando delle soluzioni end-to-end (E2E) importanti. Infine, con la vendita dei pacchetti di soluzioni, è possibile ottenere il meglio dall'ecosistema della piattaforma Azure per risolvere qualsiasi problema del cliente e aumentare le vendite.

## <a name="what39s-the-customer-base-for-the-azure-marketplace"></a>Qual è la base clienti per Azure Marketplace?

I clienti di Azure Marketplace sono di tipo diverso. Poiché Azure vanta una delle basi clienti in più rapida crescita di tutti i provider di cloud, sarà possibile accedere a professionisti IT e sviluppatori per situazioni quasi infinite: da aziende giovani a realtà affermate, in tutti i settori, nel pubblico e nel privato.

## <a name="how-does-the-azure-marketplace-work"></a>Come funziona Azure Marketplace?

È piuttosto semplice: dopo l'approvazione, si crea un'immagine di macchina virtuale certificata di Azure e la si pubblica in Azure Marketplace, dove i clienti di Azure possano trovare, acquistare e distribuire il prodotto in pochi minuti. Ancora meglio, i clienti distribuiscono la soluzione in modo affidabile, coscienti del fatto che l'ambiente è configurato correttamente in Azure e l'infrastruttura è pronta ad avviarsi in pochi minuti.

Il portale Cloud Partner (CPP) è l'hub per la creazione dell'offerta in Azure Marketplace. Le immagini della macchina virtuale sono preconfigurate e dispongono di un sistema operativo e di una o più applicazioni completamente installate. Per certificare l'immagine in modo che sia pronta per la pubblicazione, è necessario soddisfare determinati requisiti, che verranno descritti nella sezione successiva di questa guida.


## <a name="whats-next"></a>Passaggi successivi

Se tutti i passaggi della guida sono stati eseguiti, è possibile che ci si chieda: &quot;Penso che Azure Marketplace _rappresenti veramente_ il massimo per il mio prodotto, ma ora come iniziare?&quot; Questo interrogativo viene trattato nella sezione seguente. Di seguito vengono fornite informazioni su come iniziare con Azure Marketplace: ottenere la certificazione Azure, l'approvazione per vendere il prodotto e creare l'offerta in CPP (figura 1).

![Processo per la vendita in Azure Marketplace](./media/cloud-partner-portal-seller-guide/processforselling.png)

Figura 1: Processo per la vendita in Azure Marketplace

In un altro livello, verranno soddisfatti una serie di prerequisiti tecnici e non e si preparerà l'immagine della macchina virtuale. Si sceglierà il prodotto e si procederà alla registrazione come venditori. Infine, si aggiungeranno contenuti di marketing che verranno inviati per la pubblicazione. Sarà possibile riesaminare l'offerta in un'anteprima/ambiente di gestione temporanea prima di attivarlo su Azure Marketplace.

La prima volta che si crea un'offerta per Azure Marketplace, è consigliabile eseguire una pianificazione su _quattro settimane_ per l'avvio di base. Se possibile, eseguire la compilazione _sei settimane_ prima dell'avvio dell'offerta per concedere più tempo allo sviluppo di considerazioni su supporti e pubblicazione.

## <a name="how-do-i-become-azure-certified"></a>Come ottenere la certificazione Azure?

Il primo passaggio nella creazione dell'offerta per Azure Marketplace è quello di ottenere la certificazione Azure. Ovvero: la compilazione delle informazioni aziendali, l'accettazione dei criteri di partecipazione, il download degli strumenti necessari e la compilazione dei componenti tecnici (figura 2).

![Requisiti per ottenere la certificazione Azure](./media/cloud-partner-portal-seller-guide/azurecertified.png)

Figura 2: Requisiti per ottenere la certificazione Azure

### <a name="technical-prerequisites"></a>Prerequisiti tecnici

Assicurarsi di rivedere attentamente e soddisfare tutti i prerequisiti tecnici prima del lancio dell'offerta. È necessario accedere a Windows o Linux, nonché collegare i dischi rigidi virtuali compatibili con Azure agli strumenti di test.

Sebbene sia possibile sviluppare i dischi rigidi virtuali di Azure nel cloud o in locale, si consiglia di sviluppare il disco rigido virtuale di Azure direttamente nel cloud tramite Remote Desktop Protocol. Tuttavia, come ultima risorsa, è possibile scaricare un disco rigido virtuale e svilupparlo tramite [l'infrastruttura locale](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-on-premise).

Per altre informazioni, vedere la pagina relativa ai [prerequisiti tecnici](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites).

### <a name="non-technical-prerequisites"></a>Prerequisiti non tecnici

Per diventare parte di Azure Marketplace, è necessario soddisfare alcuni prerequisiti non tecnici. In primo luogo, consultare e accettare le condizioni per le [Politiche di partecipazione a Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/). Si noti che il software e i servizi offerti in Azure Marketplace devono soddisfare almeno uno dei requisiti seguenti:

- **Esecuzione in Microsoft Azure**: la funzione principale del software o del servizio deve essere la possibilità di esecuzione in Microsoft Azure.
- **Distribuzione in Microsoft Azure**: è necessario descrivere nelle informazioni di presentazione il modo in cui il software o il servizio viene distribuito in Microsoft Azure.
- **Integrazione con o estensione di un servizio di Microsoft Azure**: è necessario indicare nelle informazioni di presentazione _quale_ servizio di Azure si integra o si estende con il software o il servizio e la _modalità_ di tale integrazione o estensione.

È necessario anche soddisfare questi requisiti aziendali, come descritto nelle Politiche di partecipazione a Microsoft Azure Marketplace:

- La società (o la sua affiliata) deve avere sede in uno dei paesi di origine della vendita supportati da Azure Marketplace.
- Il prodotto deve essere concesso in licenza in modo da essere compatibile con i modelli di fatturazione supportati da Azure Marketplace.
- L'utente è responsabile di rendere il supporto tecnico disponibile per i clienti in una modalità ragionevole dal punto di vista commerciale, che sia gratuitamente, a pagamento o tramite il supporto della community.
- È necessario concedere in licenza il software e le dipendenze del software di terze parti.
- È necessario offrire contenuti che soddisfino i criteri affinché l'offerta venga inserita in [azure.microsoft.com](../../C:/Users/Lisa.Rosenberger/Desktop/azure.microsoft.com) e nel portale di gestione di Azure.

Infine, è necessario accettare le [Condizioni per l'Utilizzo del Sito Web di Microsoft Azure](https://azure.microsoft.com/support/legal/website-terms-of-use/), l'[Informativa sulla privacy di Microsoft](http://www.microsoft.com/privacystatement/default.aspx) e il [Contratto del Programma Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/). 

Per un elenco di domande frequenti, vedere [Domande frequenti su Azure Marketplace](https://azure.microsoft.com/marketplace/faq/).

### <a name="virtual-machine-image-preparation"></a>Preparazione dell'immagine della macchina virtuale

Dopo avere esaminato tutti i prerequisiti e completato le attività necessarie, ora è possibile procedere alla creazione dell'offerta di un'immagine di macchina virtuale, come illustrato in dettaglio nella [Guida alla pubblicazione di un'immagine di macchina virtuale](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation).

Questa guida consente di preparare il disco rigido virtuale che si desidera distribuire in Azure Marketplace, che diventerà la base dello SKU. 

> [!NOTE]
> Il processo varia a seconda che si stia offrendo uno SKU basato su Linux o su Windows.

> [!NOTE]
>  Questo processo può essere eseguito parallelamente alla [creazione e registrazione](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration) descritte di seguito.

### <a name="azure-certification"></a>Certificazione di Azure

Ottenere lo stato di _Certificato Azure_ rappresenta il completamento del processo di avvio. Questo stato infonde fiducia nei clienti, che saranno certi che i propri professionisti IT e sviluppatori stiano acquistando soluzioni di qualità eseguite o compilate con la tecnologia Azure da un partner affidabile. Le soluzioni certificate Azure comprendono:

- Consultazione globale
- Determinazione della compatibilità con la piattaforma di Azure
- Conformità di sicurezza dell'immagine online
- Nessun virus o malware
- Modello di fatturazione supportato

## <a name="how-do-i-nominate-my-product-and-get-approved"></a>Come scegliere il prodotto e ottenere l'approvazione?

A questo punto è necessario ottenere l'approvazione per la vendita del prodotto in Azure Marketplace (figura 3). Microsoft facilita la scelta del prodotto, il completamento del processo di pubblicazione e la registrazione come venditore.

![approvazione alla vendita in Azure Marketplace](./media/cloud-partner-portal-seller-guide/gettingapprovedsteps.png)

Figura 3: Procedura per l'approvazione alla vendita in Azure Marketplace

Il primo passo verso l'approvazione è quello di [scegliere](https://createopportunity.azurewebsites.net/) il prodotto prima della registrazione e della pubblicazione. Tenere presente che l'approvazione può richiedere _fino a tre giorni lavorativi_.

Al momento dell'approvazione, verrà visualizzato quanto segue:

- Ricezione di un messaggio di posta elettronica con un codice promozionale con cui si rinuncia alla tassa di applicazione di $99 destinata al centro per sviluppatori e a un profilo nel CPP.
- Pre-approvazioni tecniche per lo stato di Certificato Azure, insieme all'opzione per la creazione di un'offerta e per la certificazione del disco rigido virtuale. Prima di poter creare l'offerta, è necessario che l'applicazione Centro per sviluppatori sia approvata.
- Istruzioni per la pubblicazione dei dettagli dell'account del portale e una panoramica del processo di pubblicazione.
- Idoneità per una chiamata con il team di avvio di Microsoft per scoprire il processo nel dettaglio e porre domande.
- Possibilità di pubblicare una seconda offerta. Le offerte pubblicate per la seconda volta non richiedono l'approvazione. È possibile visitare direttamente il CPP, ma le macchine virtuali devono ancora essere certificate tramite il processo di pubblicazione.
- Guida per la richiesta di assistenza con la pubblicazione. Le domande devono essere rivolte al [link di supporto](https://support.microsoft.com/getsupport?wf=0&amp;tenant=ClassicCommercial&amp;oaspworkflow=start_1.0.0.0&amp;locale=en-us&amp;supportregion=en-us&amp;pesid=15635&amp;ccsid=636233723471685249) dell'editore del Marketplace.

Infine, [registrare l'account](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration) come venditore Microsoft. L'approvazione e la consultazione possono richiedere _fino a due settimane_, pertanto usare questo lasso di tempo per creare l'offerta di Azure Marketplace nel CPP.

## <a name="how-do-i-create-my-offer-in-the-cloud-partner-portal"></a>Come creare l'offerta nel portale Cloud Partner?

Si è ora pronti per creare l'offerta e certificare l'immagine della macchina virtuale. A tale scopo, si userà il portale Cloud Partner (figura 4). È possibile considerare il CPP come hub per [pubblicare e gestire la soluzione](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-getting-started).

![nuova offerta tramite il portale Cloud Partner](./media/cloud-partner-portal-seller-guide/newoffersetup.png)

Figura 4: Impostazione di una nuova offerta tramite il portale Cloud Partner

## <a name="what-about-best-practices"></a>Quali sono le procedure consigliate?

Per poter sfruttare al massimo la possibilità di essere un venditore in Azure Marketplace, abbiamo sviluppato alcuni strumenti e procedure consigliate che possono avere un impatto significativo e che non richiedono molto tempo.

## <a name="azure-test-drives"></a>Test drive di Azure

[I test drive di Azure](https://azure.microsoft.com/marketplace/test-drives/) sono un ottimo modo per presentare il prodotto ai potenziali clienti e dare loro la possibilità di provare prima dell'acquisto. Questo consente di migliorare la conversione e generare lead altamente qualificati.

Dopo aver fornito le informazioni di contatto, i clienti possono accedere all'esperienza di test drive predefinita: una versione di valutazione pratica e autonoma delle funzionalità principali del prodotto e dei vantaggi dimostrati in uno scenario di implementazione nel mondo reale. In questo modo, i test drive di Azure consentono di dare vita al prodotto, proprio davanti agli occhi dei clienti.

Attualmente, la pubblicazione di test drive per il prodotto è disponibile solo nel portale di pubblicazione classico. È possibile trovare documentazione su come eseguire questa operazione accedendo [qui](https://github.com/Azure/AzureTestDrive/wiki) in merito alle procedure per _pubblicare una nuovo test drive_.

Altre informazioni sui test drive di Azure sono reperibili [qui](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

## <a name="go-to-market-checklist"></a>Elenco di controllo per la commercializzazione

Altre informazioni sui nostri [programmi di commercializzazione](https://partner.microsoft.com/go-to-market/) che possono aiutare ad espandere la copertura globale dell'organizzazione. È anche possibile usare le risorse presso il [Partner Marketing Center](http://smartpartnermarketing.microsoft.com/isv).

Prima del lancio, è consigliabile eseguire alcuni passaggi per ottenere una rapida adesione dell'offerta su Azure Marketplace. Scoprire se si è pronti per proporre sul mercato l'offerta con un veloce elenco di controllo:

- **Ho annunciato che l'offerta è disponibile in Azure Marketplace** pubblicando un articolo di blog, inviando messaggi di posta elettronica o rilasciando una conferenza stampa.
- **Sto promuovendo l'offerta sul sito Web personale** , indirizzando i clienti sull'offerta in Azure Marketplace, dove è possibile imparare, esplorare, provare la versione di valutazione, acquistare e distribuire.
- **Ho pubblicato un test drive** in modo che i clienti possano scoprire l'offerta in esecuzione su Azure nella pausa caffè.
- **Ho attivato la generazione dei lead su richiesta** in modo che ogni volta che un cliente fa clic per distribuire l'applicazione, io ricevo le informazioni di contatto e il nome.
- **Mi sono connesso con il mio gestore partner** presso Microsoft (se si dispone di uno) per scoprire altre opportunità.

## <a name="what-about-reports"></a>Informazioni sui report

Azure Marketplace offre report sugli ordini, sull'utilizzo e sui clienti accessibili tramite il [portale Publisher Reporting](https://reports.azure.com)di Azure Marketplace (figura 5). I dati non elaborati sono forniti in una tabella navigabile e possono essere scaricati come file con estensione XLS o CSV.

![Accesso ai report tramite il portale Publisher Reporting](./media/cloud-partner-portal-seller-guide/accessingreports.png)

Figura 5: Accesso ai report tramite il portale Publisher Reporting

Questo [video](https://player.vimeo.com/video/200859918) offre un assaggio delle funzionalità e dei vantaggi dei report, tra cui:

- Tipi di report: snapshot di riepilogo di ordini, utilizzo e tendenze dei clienti nella home page
- Ordini dettagliati, utilizzo e dati dei clienti
- Gli ordini e l'utilizzo sono visualizzati come riepilogo mensile o come visualizzazione delle tendenze su un periodo di sei mesi
- Numerosi dati dettagliati visualizzati in modo standard
- Utilizzo/ordini da:

-
  - Market
  - Canale
  - Offerte di tendenza
  - Tipo di licenza del Marketplace

I report dettagliati mostrano le informazioni sul cliente, ad esempio il nome della società, le informazioni di contatto e la posizione geografica, nonché il codice postale, pertanto è possibile contattare i propri venditori. L'elenco seguente comprende gli attributi specifici che forniamo in relazione ai clienti:

- Rivenditore
- FirstName
- LastName
- Email
- CompanyName
- TransactionDate
- SubscriptionName
- AzureSubscriptionId
- CloudInstanceName
- Numero di ordini
- CustomerCountryRegion
- CustomerCity
- CustomerCommunicationCulture
- CustomerZipCode

Si offrono anche corsi di formazione tramite la documentazione della guida, un glossario e una demo registrata. In caso di necessità di aiuto o supporto con i report, è possibile aprire un [ticket di supporto](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=636233723471685249).

