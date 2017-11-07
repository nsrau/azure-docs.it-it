---
title: Guida per i venditori di Microsoft Azure Marketplace | Microsoft Docs
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
ms.robots: NOINDEX, NOFOLLOW
ms.openlocfilehash: c78708687fbb5716e3e8d62967013310d6ccc735
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-marketplace-seller-guide"></a>Guida per i venditori di Azure Marketplace

Benvenuti alla Guida per i venditori di Azure Marketplace. Questa guida è destinata agli utenti aziendali e ai responsabili di prodotto presso i fornitori di software indipendenti (ISV) che desiderano vendere le loro immagini certificate della macchina virtuale di Azure a professionisti IT e sviluppatori. Con clienti Azure in tutto il mondo, il [Marketplace](https://azuremarketplace.microsoft.com/) può offrire grande copertura ed esposizione per i prodotti.


> [!NOTE]
> Se si è interessati a vendere i prodotti software-as-a-service (SaaS) finiti agli utenti aziendali, è possibile esaminare le opzioni per elencarli in [AppSource](https://appsource.microsoft.com).

Al termine di questa guida, si saprà dove trovare altre informazioni più dettagliate sui seguenti argomenti:

- Cos'è Azure Marketplace?
- Come determinare se il prodotto soddisfa il Marketplace?
- Quali sono i vantaggi della vendita sul Marketplace?
- Quali sono i prerequisiti (tecnici e non tecnici) per la vendita sul Marketplace?
- Come è possibile creare dei dischi rigidi virtuali (VHD) compatibili con Azure?
- Come fare richiesta e registrarsi come venditore?
- Come si crea e si pubblica un'offerta?
- Come iniziare a vendere e trovare le risorse disponibili?
- Che tipo di creazione di report e informazioni dettagliate fornisce il Marketplace?
- Dove è possibile ottenere la guida e il supporto tecnico?

Di seguito sono riportati i requisiti iniziali.

## <a name="whats-the-azure-marketplace"></a>Cos'è Azure Marketplace?

Azure Marketplace è un marketplace online per applicazioni e servizi che consente ai fornitori di software indipendenti (ISV), dalle startup alle grandi imprese, di offrire le proprie soluzioni a clienti di Azure in tutto il mondo. Azure Marketplace consente di distribuire e vendere, in qualità di publisher di Azure, l'immagine innovativa della macchina virtuale ad altri sviluppatori, ISV e professionisti IT che vogliono sviluppare rapidamente applicazioni basate su cloud e soluzioni per dispositivi mobili in Azure. Il Marketplace supporta una gamma di offerte, da applicazioni analitiche dei dati end-to-end con elaborazione dei dati, archiviazione dei dati e analisi dei livelli ad app di e-commerce a più livelli (dati, servizio e Internet).

I clienti cloud affrontano numerose sfide durante la ricerca delle soluzioni da adattare alle proprie esigenze. Il Marketplace fornisce un modo per risolvere questi problemi e collega i clienti con soluzioni ISV innovative, come illustrato nella tabella seguente:

| Esigenze del cliente | Soluzione di Azure Marketplace |
| --- | --- |
| Richiede la funzionalità della piattaforma cloud aggiuntiva per soddisfare le esigenze aziendali e tecniche | Offre un crescente portfolio di applicazioni complementari e di servizi in Azure |
| Risulta difficile scoprire l'applicazione o il servizio corretti | Offre un'unica risorsa completa per individuare, cercare e acquistare applicazioni e servizi |
| È necessario un meccanismo di distribuzione scalabile per i servizi e le applicazioni di terze parti | Consente la creazione e la configurazione di distribuzioni scalabili per servizi e applicazioni di terze parti |
| Richiede nuove applicazioni e servizi da integrare e su cui lavorare con le soluzioni esistenti | Integra facilmente servizi e applicazioni di terze parti con soluzioni esistenti in Azure |

Il Marketplace offre ai clienti globali qualità, scelta e forza dell'ecosistema del partner Azure. I vantaggi principali includono:

- Posizione centralizzata per le offerte basate su Azure da Microsoft e partner.
- Oltre 5.000 offerte.
- Esperienza integrata sulla piattaforma.
- Configurazione, distribuzione e gestione semplificate.

## <a name="is-the-marketplace-right-for-my-business"></a>Marketplace è adatto alla mia azienda?

A questo punto è lecito chiedersi se Azure Marketplace sia la scelta adatta per l'azienda. In tal caso, cosa se ne può ricavare? Il Marketplace consente di creare nuove opportunità di vendita per l'utente:

- **Vendere in nuovi mercati con un portfolio ampliato di soluzioni in Azure**. Si tratta dell'opportunità di usare upselling e cross-selling delle offerte del Marketplace con i servizi di Azure disponibili per i clienti Microsoft Online Subscription Program (MOSP) e Microsoft Enterprise Agreement. È possibile incorporare facilmente offerte del Marketplace nella soluzione dei clienti e nello scenario di Azure.
- **Migliorare il valore aziendale e aumentare le dimensioni dell'offerta con account di clienti nuovi ed esistenti**. Il Marketplace può aiutare ad aumentare la dimensione dell'offerta, a risolvere i punti deboli del cliente durante lo spostamento dei carichi di lavoro nel cloud e ad aumentare la redditività dell'offerta. È possibile aumentare il valore dell'azienda vendendo soluzioni complete e risolvendo le falle della piattaforma cloud di Azure per soddisfare le richieste del cliente.
- **Ricorrere a una più vasta gamma di potenziali clienti vendendo applicazioni e servizi e del Marketplace**. Il Marketplace può rendere più semplice trovare e mantenere i nuovi clienti. Molte aziende oggi hanno bisogno di carichi di lavoro di transizione al cloud e si adattano ad ambienti di infrastruttura in continua evoluzione. È possibile fornire servizi e applicazioni adeguati per aiutare i clienti a colmare il divario.
- **Integrare ed estendere la funzionalità Azure raggruppando le offerte con servizi di Azure Marketplace**. Marketplace consente di creare conversazioni basate su scenario con i clienti. È anche possibile risolvere le falle specifiche della piattaforma e le esigenze del cliente parlando delle soluzioni end-to-end. Infine, con la vendita dei pacchetti di soluzioni, è possibile utilizzare l'ecosistema della piattaforma Azure per risolvere numerosi problemi del cliente e aumentare le vendite.

## <a name="what39s-the-customer-base-for-the-marketplace"></a>Qual è la base clienti per il Marketplace?

I clienti Marketplace sono diversificati. Inoltre, Azure dispone di una delle basi di clienti in più rapida crescita di tutti i provider di cloud. È possibile accedere a professionisti IT e sviluppatori che lavorano per aziende, che vanno da start-up a imprese, in vari settori e sia pubbliche che private.

## <a name="how-does-the-marketplace-work"></a>Come funziona Marketplace?

È piuttosto semplice. Dopo l&#39;approvazione, si crea un'immagine di macchina virtuale certificata di Azure e la si pubblica nel Marketplace. I clienti di Azure possano trovare, acquistare e distribuire il prodotto in pochi minuti. Ancora meglio, i clienti distribuiscono la soluzione in modo affidabile. Sono coscienti del fatto che l'ambiente è configurato correttamente in Azure e l'infrastruttura è pronta ad avviarsi in pochi minuti.

Il portale Cloud Partner è l'hub per la creazione dell'offerta nel Marketplace. Le immagini della macchina virtuale sono preconfigurate e dispongono di un sistema operativo e di una o più applicazioni completamente installate. Per certificare l&#39;immagine in modo che sia pronta per la pubblicazione, è necessario soddisfare determinati requisiti. Saranno discussi nella sezione successiva.


## <a name="whats-next"></a>Passaggi successivi

È lecito chiedersi se Azure Marketplace sia la realmente scelta adatta per il prodotto. Quindi come iniziare? Questa sezione è dedicata esclusivamente all'introduzione e all'esecuzione in Marketplace (figura 1): 
* Acquisendo la certificazione Microsoft Azure.
* Ottenendo l'approvazione alla vendita del prodotto.
* Creando l'offerta nel portale Cloud Partner. 

![Processo per la vendita in Azure Marketplace](./media/cloud-partner-portal-seller-guide/processforselling.png)

Figura 1: Processo per la vendita in Azure Marketplace

Verranno innanzitutto soddisfatti una serie di prerequisiti tecnici e non e si preparerà l'immagine della macchina virtuale. Si sceglierà il prodotto e si procederà alla registrazione come venditori. Infine, si aggiungeranno contenuti di marketing che verranno inviati per la pubblicazione. Sarà possibile riesaminare l'offerta in un'anteprima/ambiente di gestione temporanea prima di attivarlo sul Marketplace.

La prima volta che si crea un'offerta per Azure Marketplace, è consigliabile eseguire una pianificazione su quattro settimane per l'avvio di base. Se possibile, eseguire la compilazione sei settimane prima dell'avvio dell'offerta per concedere più tempo ad attività su supporti di memorizzazione e pubblicazione.

## <a name="how-do-i-become-azure-certified"></a>Come ottenere la certificazione Azure?

Il primo passaggio nella creazione dell'offerta per Azure Marketplace è quello di ottenere la certificazione Azure. Ovvero: la compilazione delle informazioni aziendali, l'accettazione dei criteri di partecipazione, il download degli strumenti necessari e la compilazione dei componenti tecnici (figura 2).

![Requisiti per ottenere la certificazione Azure](./media/cloud-partner-portal-seller-guide/azurecertified.png)

Figura 2: Requisiti per ottenere la certificazione Azure

### <a name="technical-prerequisites"></a>Prerequisiti tecnici

Rivedere attentamente e soddisfare tutti i prerequisiti tecnici prima del lancio. È necessario accedere a Windows o Linux e collegare i dischi rigidi virtuali compatibili con Azure agli strumenti di test.

È consigliabile sviluppare il disco rigido virtuale Azure nel cloud usando Remote Desktop Protocol. Tuttavia, se è l&#39;unica opzione, è possibile scaricare un disco rigido virtuale e svilupparlo utilizzando l'[infrastruttura locale](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-on-premise).

Per altre informazioni, vedere la pagina relativa ai [prerequisiti tecnici](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites).

### <a name="nontechnical-prerequisites"></a>Prerequisiti non tecnici

Per diventare parte del Marketplace, è necessario soddisfare alcuni prerequisiti non tecnici. In primo luogo, consultare e accettare le condizioni per le [Politiche di partecipazione a Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/). Il software e i servizi offerti nel Marketplace devono soddisfare almeno uno dei requisiti seguenti:

- **Esecuzione in Azure**. La funzione principale del software o del servizio deve essere la possibilità di esecuzione in Microsoft Azure.
- **Distribuzione in Azure**. È necessario descrivere nelle informazioni di presentazione il modo in cui il software o il servizio viene distribuito in Azure.
- **Integrazione o estensione di un altro servizio di Azure**. È necessario indicare le informazioni di presentazione:
  - Il servizio Azure mediante il quale avviene l'integrazione o l'estensione del software o del servizio
  - Il modo in cui il software o il servizio si integra o si estende con il servizio Azure

È necessario inoltre soddisfare questi requisiti aziendali, come descritto nelle Politiche di partecipazione a Microsoft Azure Marketplace:

- La società (o la sua affiliata) deve avere sede in uno dei paesi di origine della vendita supportati dal Marketplace.
- Il prodotto deve essere concesso in licenza in modo da essere compatibile con i modelli di fatturazione supportati dal Marketplace.
- L'utente è responsabile di rendere il supporto tecnico disponibile per i clienti in una modalità ragionevole dal punto di vista commerciale, che sia gratuitamente, a pagamento o tramite il supporto della community.
- È necessario concedere in licenza il software e le dipendenze del software di terze parti.
- I contenuti devono soddisfare determinati criteri affinché l'offerta venga inserita in [azure.microsoft.com](../../C:/Users/Lisa.Rosenberger/Desktop/azure.microsoft.com) e nel portale di Azure.

Infine, è necessario accettare le [Condizioni per l'Utilizzo del Sito Web di Microsoft Azure](https://azure.microsoft.com/support/legal/website-terms-of-use/), l'[Informativa sulla privacy di Microsoft](http://www.microsoft.com/privacystatement/default.aspx) e il [Contratto del Programma Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/). 

Per un elenco di domande frequenti, vedere [Domande frequenti su Azure Marketplace](https://azure.microsoft.com/marketplace/faq/).


### <a name="azure-certification"></a>Certificazione di Azure

Ottenere lo stato di _Certificato Azure_ rappresenta il completamento del processo di avvio. Questo stato infonde fiducia nei clienti, che saranno certi che i propri professionisti IT e sviluppatori stiano acquistando soluzioni di qualità eseguite o compilate con la tecnologia Azure da un partner affidabile. Le soluzioni certificate Azure comprendono:

- Consultazione globale.
- Determinazione della compatibilità con la piattaforma di Azure.
- Conformità di sicurezza dell'immagine online.
- Nessun virus o malware.
- Modelli di fatturazione supportati.

## <a name="how-do-i-nominate-my-product-and-get-approved"></a>Come scegliere il prodotto e ottenere l'approvazione?

A questo punto è necessario ottenere l'approvazione per la vendita del prodotto nel Marketplace (figura 3). Microsoft facilita la scelta del prodotto, il completamento del processo di pubblicazione e la registrazione come venditore.

![approvazione alla vendita in Azure Marketplace](./media/cloud-partner-portal-seller-guide/gettingapprovedsteps.png)

Figura 3: Procedura per l'approvazione alla vendita in Azure Marketplace

Il primo passo verso l'approvazione è quello di [scegliere](https://createopportunity.azurewebsites.net/) il prodotto prima della registrazione e della pubblicazione. L'approvazione può richiedere _fino a tre giorni lavorativi_.

Al momento dell'approvazione, verrà visualizzato quanto segue:

- Ricezione di un messaggio di posta elettronica con un codice promozionale con cui si rinuncia alla tassa di applicazione di $99 destinata al centro per sviluppatori e a un profilo nel Portale per Cloud Partner.
- Pre-approvazioni tecniche per lo stato di Certificato Azure, insieme all'opzione per la creazione di un'offerta e per la certificazione del disco rigido virtuale. Prima di poter creare l'offerta, è necessario che l'applicazione Centro per sviluppatori sia approvata.
- Istruzioni per l'accesso al portale per Cloud Partner e una panoramica del processo di pubblicazione.
- Idoneità per una chiamata con il team di avvio di Microsoft per scoprire il processo nel dettaglio e porre domande.
- Possibilità di pubblicare una seconda offerta. Le offerte pubblicate per la seconda volta non richiedono l'approvazione. È possibile visitare direttamente il portale per Cloud Partner , ma le macchine virtuali devono ancora essere certificate tramite il processo di pubblicazione.
- Guida per la richiesta di assistenza con la pubblicazione. Le domande devono essere rivolte al [link di supporto](https://support.microsoft.com/en-us/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfName=productselection&prid=16230&ccsid=636282352448485256) dell'editore del Marketplace.

Infine, [registrare l'account](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration) come venditore Microsoft. L'approvazione e la consultazione possono richiedere _fino a due settimane_, pertanto usare questo lasso di tempo per creare l'offerta di Azure Marketplace nel portale per Cloud Partner.

## <a name="how-do-i-publish-my-offer-on-the-azure-marketplace"></a>Come posso pubblicare un'offerta su Azure Marketplace?

Si è ora pronti per certificare l'immagine della macchina virtuale e pubblicare l'offerta. A tale scopo, si userà il portale per Cloud Partner. È possibile considerare il portale per Cloud Partner come hub per pubblicare e gestire la soluzione. In pratica, è sufficiente caricare il disco rigido virtuale, aggiungere i dettagli SKU e i contenuti di marketing e inviare la proposta per certificazione e revisione. È possibile visualizzare l'anteprima dell'offerta e vedere l'aspetto prima della pubblicazione in Marketplace.

## <a name="what-about-best-practices"></a>Quali sono le procedure consigliate?

Ecco alcuni strumenti e procedure consigliate che possono avere un impatto significativo sulle attività di venditore sul Marketplace.

### <a name="azure-test-drives"></a>Test drive di Azure

[I test drive di Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/test-drives?page=1) sono un ottimo modo per presentare il prodotto ai potenziali clienti e dare loro la possibilità di provare prima dell'acquisto. I test drive consentono di migliorare la conversione e generare lead.

Dopo aver fornito le informazioni di contatto, i clienti possono accedere ai test drive predefiniti. Si tratta di una prova pratica autoguidata delle funzionalità principali e dei vantaggi del prodotto in uno scenario reale.

Attualmente, la pubblicazione di test drive per il prodotto è disponibile solo nel portale di pubblicazione classico. Altre informazioni sono disponibili accedendo alla documentazione su [come pubblicare un nuovo test drive](https://github.com/Azure/AzureTestDrive/wiki).

Altre informazioni sui [test drive di Azure](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

### <a name="go-to-market-checklist"></a>Elenco di controllo per la commercializzazione

Altre informazioni sui nostri [programmi di commercializzazione](https://partner.microsoft.com/go-to-market/) che possono aiutare ad espandere la copertura globale dell'organizzazione. È anche possibile usare le risorse presso il [Partner Marketing Center](http://smartpartnermarketing.microsoft.com/isv).

Prima del lancio, è consigliabile eseguire alcuni passaggi per ottenere una rapida adesione dell'offerta sul Marketplace. Utilizzare questo elenco di controllo per determinare se si è pronti:

- **Ho annunciato che I&#39;offerta è disponibile sul Marketplace** pubblicando un articolo di blog, inviando messaggi di posta elettronica o rilasciando una conferenza stampa.
- **Sto promuovendo l&#39;offerta sul sito Web personale** , indirizzando i clienti sull'offerta sul Marketplace.
- **Ho pubblicato un test drive** in modo che i clienti possano scoprire l'offerta in esecuzione su Azure nella pausa caffè.
- **Ho attivato la generazione dei lead su richiesta** in modo che ogni volta che un cliente fa clic per distribuire l'applicazione, io ricevo le informazioni di contatto e il nome.
- **Mi sono connesso con il mio gestore partner** presso Microsoft (se si dispone di uno) per scoprire altre opportunità.

## <a name="what-about-reports"></a>Informazioni sui report

Il Marketplace offre report sugli ordini, sull'utilizzo e sui clienti accessibili tramite il [portale Publisher Reporting](https://reports.azure.com)del Marketplace. Oltre a informazioni e analisi utili, i dati non elaborati sono forniti in una tabella navigabile e possono essere scaricati come file con estensione XLS o CSV.

[Questo video](https://player.vimeo.com/video/200859918) offre un assaggio delle funzionalità e dei vantaggi dei report, tra cui:

- Tipi di report: snapshot di riepilogo di ordini, utilizzo e tendenze dei clienti nella home page.
- Ordini dettagliati, utilizzo e dati dei clienti.
- Gli ordini e l'utilizzo sono visualizzati come riepilogo mensile o come visualizzazione delle tendenze su un periodo di sei mesi.
- Numerosi dati dettagliati visualizzati in modo standard.
- Utilizzo/ordini da:
  - Market
  - Canale
  - Offerte di tendenza
  - Tipo di licenza del Marketplace

I report dettagliati mostrano le informazioni sul cliente, ad esempio il nome della società e la località geografica, oltre al codice postale, quindi è possibile confrontare i propri clienti. L'elenco seguente comprende gli attributi specifici che forniamo in relazione ai clienti:

- Rivenditore
- FirstName
- LastName
- Email
- CompanyName
- TransactionDate
- SubscriptionName
- AzureSubscriptionId
- CloudInstanceName
- OrderCount
- CustomerCountryRegion
- CustomerCity
- CustomerCommunicationCulture
- CustomerZipCode

Si offrono anche corsi di formazione tramite la documentazione della guida, un glossario e una demo registrata. In caso di necessità di aiuto o supporto con i report, è possibile aprire un [ticket di supporto](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=636233723471685249).

Benvenuto alla community di venditori ISV e di scoprire l'offerta.

---
