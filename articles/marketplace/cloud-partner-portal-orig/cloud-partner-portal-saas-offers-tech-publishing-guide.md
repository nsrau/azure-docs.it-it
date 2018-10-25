---
title: Guida tecnica alla pubblicazione di applicazioni SaaS | Microsoft Docs
description: Descrive come pubblicare le offerte di applicazioni SaaS nel marketplace appropriato.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 9d73040f11549741643d96913c42df49594b8d41
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807268"
---
<a name="saas-application-technical-publishing-guide"></a>Guida tecnica alla pubblicazione di applicazioni SaaS
===========================================

Benvenuti nella Guida tecnica alla pubblicazione di applicazioni SaaS. Questa guida è stata pensata per aiutare gli editori potenziali e quelli già operativi a presentare i propri servizi e applicazioni in AppSource o Azure Marketplace sfruttando l'offerta di applicazioni SaaS.

Per una panoramica di tutte le altre offerte in Marketplace, fare riferimento alla [Guida alla pubblicazione per Azure Marketplace](https://aka.ms/sellerguide).


<a name="what-are-pre-requisites-for-publishing-a-saas-app"></a>Quali sono i prerequisiti per la pubblicazione di un'app SaaS?
-------------------------------------------------

Il portale di pubblicazione offre l'accesso in base al ruolo al portale, per consentire a più agli utenti di collaborare alla pubblicazione di un'offerta. Per altre informazioni, vedere [Manage Users](./cloud-partner-portal-manage-users.md) (Gestire gli utenti). 

Prima di poter pubblicare un'offerta per conto dell'account di un editore, uno degli utenti con ruolo *Proprietario* deve accettare di essere conforme alle [Condizioni per l'utilizzo](https://azure.microsoft.com/support/legal/website-terms-of-use/), all'[Informativa sulla privacy di Microsoft](http://www.microsoft.com/privacystatement/default.aspx) e al [Contratto del programma Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


<a name="creating-an-offer"></a>Creazione di un'offerta
-----------------

Questa sezione descrive il processo di creazione di una nuova offerta di app SaaS.

![Panoramica dell'offerta SaaS](media/cpp-creating-saas-offers/saas-offer-overview.png)

L'offerta di app SaaS è costituita da cinque sezioni, descritte nella tabella seguente:

| **Sezione dell'offerta**  | **Descrizione**                                                                                                     |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------|
| Impostazioni dell'offerta     | Consente di definire un nome univoco e un ID per l'app SaaS                                                                         |
| Informazioni tecniche     | Consente di configurare il tipo di soluzione SaaS e di specificare i dettagli della connessione per l'applicazione                            |
| Test drive         | Sezione facoltativa che consente di definire un servizio che permette ai clienti di testare l'offerta prima di acquistarla. |
| Dati da visualizzare nella vetrina | Contiene le sezioni di marketing, note legali, gestione lead e degli elenchi:   <br/> - La sezione Marketing consente di immettere la descrizione e i logo necessari per l'offerta da visualizzare correttamente nel portale utente del marketplace.  <br/> - La sezione Gestione lead consente di specificare il percorso a cui reindirizzare i lead dell'utente finale generati nel portale dell'utente finale di Azure Marketplace.  <br/> - La sezione Note legali consente di inserire la documentazione legale dell'informativa sulla privacy e delle condizioni per l'utilizzo.  |
| Contatto            | Consente di immettere le informazioni di contatto del supporto dell'offerta.                                                                  |
|  |  |


### <a name="creating-a-new-offer"></a>Creazione di una nuova offerta

Dopo aver eseguito l'accesso al portale Cloud Partner, selezionare la voce **Nuova offerta** nella barra dei menu a sinistra che visualizza le offerte disponibili. La figura seguente mostra un esempio delle offerte:

![Nuova offerta SaaS](media/cpp-creating-saas-offers/saas-new-offer.png)

Selezionare l'offerta per cui si è stati approvati dall'elenco. Viene aperto un nuovo modulo di offerta.

![Nuovo modulo di offerta SaaS](media/cpp-creating-saas-offers/saas-new-offer-2.png)

La tabella seguente descrive i campi dell'offerta:

| **Campi offerta** | **Descrizione**                                                                                            |
|------------------|----------------------------------------------------------------------------------------------------------- |
| Offer ID (ID offerta)         | Un identificatore univoco dell'offerta in un profilo di pubblicazione. Questo ID sarà visibile negli URL dei prodotti e nei report di fatturazione. Può essere composto solo da caratteri alfanumerici minuscoli o trattini (-). L'ID non può terminare con un trattino e può contenere massimo 50 caratteri. Si noti che questo campo è bloccato dopo la pubblicazione dell'offerta. Ad esempio, se l'editore Contoso pubblica un'offerta con ID offerta sample-vm, in Azure Marketplace l'offerta viene visualizzata come [https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview](https://azuremarketplace.microsoft.com/) |
| Publisher ID (ID editore)     | L'ID editore rappresenta il proprio identificatore univoco in Marketplace. È consigliabile associare a tutte le proprie offerte il proprio ID editore. L'ID editore non può essere modificato dopo aver salvato l'offerta.                                                                                       |
| NOME             | Nome visualizzato dell'offerta. Nome che verrà visualizzato in Azure Marketplace e nel portale di Azure. Può contenere massimo 50 caratteri. Le linee guida prevedono qui l'inclusione di un nome di marchio riconoscibile per il prodotto. Non includere il nome della società, a meno che non corrisponda al nome con cui viene commercializzato. Se si sta proponendo questa offerta sul proprio sito Web, assicurarsi che il nome corrisponda esattamente a quello con cui viene visualizzato nel sito Web.             |
|  |  |

Fare clic su **Salva** per salvare le voci immesse. La sezione seguente descrive come aggiungere i piani all'offerta.


### <a name="technical-information"></a>Informazioni tecniche

La sezione Informazioni tecniche consente di immettere le informazioni seguenti:

![Informazioni tecniche dell'offerta SaaS](media/cpp-creating-saas-offers/saas-new-offer-technical-info.png)

La decisione più importante riguarda la possibilità di avere un elenco di SaaS o di abilitare la commercializzazione. Se si usa un elenco di SaaS, è necessario selezionare una delle opzioni seguenti:

-   Gratuito - Specificare un URL dell'applicazione SaaS da cui gli utenti possono ottenere l'app.
-   Versione di valutazione gratuita - Specificare un URL dell'applicazione SaaS in cui i clienti possono eseguire una versione di valutazione prima di aderire all'offerta.
-   Contatta - Applicabile solo se è presente un sistema di gestione lead connesso, questa opzione consente ai clienti di chiedere di essere contattati e di condividere un lead.

Per abilitare la commercializzazione tramite le transazioni Microsoft per un'app SaaS disponibile in Azure Marketplace, selezionare **Sell through Azure** (Vendi tramite Azure).  
Per altre informazioni sulla connessione dell'app SaaS, vedere [SaaS - Sell through Azure](./cloud-partner-portal-saas-offer-subscriptions.md) (SaaS - Vendere tramite Azure).


### <a name="test-drive"></a>Test drive

La creazione di una versione di valutazione per i clienti è il modo migliore per garantire loro la validità dell'eventuale acquisto. Tra le opzioni di valutazione disponibili, Test drive è la più efficace per la generazione di lead di qualità elevata e una maggior conversione dei lead.

Offre ai clienti una versione di valutazione pratica e autoguidata delle funzionalità e dei vantaggi principali del prodotto, provati in uno scenario di implementazione reale.

![Test drive dell'offerta SaaS](media/cpp-creating-saas-offers/saas-new-offer-test-drive.png)


#### <a name="how-a-test-drive-works"></a>Funzionamento di un test drive

Un cliente potenziale ricerca e individua l'applicazione in Marketplace. Il cliente effettua l'accesso e accetta le condizioni per l'utilizzo. Il cliente riceve quindi un ambiente preconfigurato per provare il prodotto per un determinato numero di ore, mentre si riceve un lead altamente qualificato di cui eseguire il follow-up.

![Test drive dell'offerta SaaS 2](media/cpp-creating-saas-offers/saas-new-offer-test-drive-2.png)

Indipendentemente dalla complessità dell'applicazione, il test drive Microsoft consente al cliente di provare il prodotto in un ambiente reale. Sono disponibili tre tipi di test drive, ognuno basato sul tipo di prodotto, scenario e marketplace.

-   **Azure Resource Manager** - Un test drive ARM di Azure è un modello di distribuzione che contiene tutte le risorse di Azure che includono una soluzione compilata dall'editore. I prodotti che rientrano in questo tipo di test drive sono quelli che usano solo risorse di Azure.
-   **App per la logica** - Un test drive App per la logica è un modello di distribuzione progettato per includere tutte le architetture di soluzioni complesse. Tutte le applicazioni di Dynamics e i prodotti personalizzati dovrebbero usare questo tipo di test drive.
-   **Power BI** - Un test drive Power BI è costituito da un collegamento incorporato a un dashboard personalizzato. È consigliabile usare questo tipo di test drive per tutti i prodotti per cui si vuole offrire la valutazione di un oggetto visivo di Power BI interattivo. È sufficiente caricare l'URL di Power BI incorporato.

I passaggi principali della pubblicazione per l'aggiunta di un test drive sono i seguenti:

1.  Definire lo scenario del test drive
2.  Creare e/o modificare il modello di Resource Manager
3.  Eseguire i singoli passaggi per creare manualmente il test drive
4.  Ripubblicare l'offerta

Per altre informazioni, vedere [Test drive](./what-is-test-drive.md).


### <a name="storefront-details"></a>Dati da visualizzare nella vetrina

Per le app SaaS è necessario che le prime due sezioni del riepilogo e della descrizione descrivano l'app.

![Dati da visualizzare nella vetrina dell'offerta SaaS](media/cpp-creating-saas-offers/saas-new-offer-storefront-details.png)

La tabella seguente descrive i dati da visualizzare nella vetrina dell'offerta:

| **Campi offerta**        | **Descrizione**                                                                                          |
|-------------------------| ---------------------------------------------------------------------------------------------------------|
| Riepilogo offerta           | Riepilogo della proposta di valore dell'offerta. Verrà visualizzato nella pagina di ricerca dell'offerta. La lunghezza massima è 100 caratteri.   |
| Descrizione dell'offerta       | Descrizione che verrà visualizzata nella pagina dei dettagli dell'applicazione. È possibile immettere un massimo di 1300 caratteri *Nota* questo campo accetta contenuto HTML con tag come &ltp\>, &lth1\>, &lth2\>, &ltli\> e così via, che consente di rendere più presentabile il contenuto. Il team del portale di pubblicazione sta lavorando per aggiungere una funzionalità che consenta all'utente di visualizzare un'anteprima dei dati da visualizzare nella vetrina, in modo da rendere sempre il contenuto più presentabile. Nel frattempo, per scoprire come verrà visualizzata la descrizione, è possibile usare qualsiasi strumento online HTML in tempo reale, ad esempio http://htmledit.squarefree.com. |
| Settori              | Selezionare i settori che corrispondono meglio all'offerta. Se l'app rientra in più settori, è possibile selezionare un massimo di due settori. |
| Suggested Categories (Categorie suggerite)    | Selezionare le categorie che corrispondono meglio all'offerta. È possibile selezionare un massimo di tre categorie.     |
| Versione dell'applicazione     | Immettere il numero di versione dell'applicazione                                                                |
| Parole chiave di ricerca (al massimo 3) | Immettere un massimo di tre parole chiave di ricerca che i clienti possono usare per trovare l'applicazione nel sito Web della vetrina del marketplace. |
|  |  |


### <a name="marketing-artifacts"></a>Artefatti di marketing

La sezione degli artefatti di marketing consente di definire gli asset di marketing di Azure Marketplace come logo, video, screenshot e documenti:

![Artefatti di marketing dell'offerta SaaS](media/cpp-creating-saas-offers/saas-new-offer-marketing-artifacts.png)

La tabella seguente descrive i campi di marketing:

| **Campi offerta** | **Descrizione**                                                                                                          |
|------------------| ------------------------------------------------------------------------------------------------------------------------ |
| Loghi            | Per un'app SaaS di tipo **Sell through Azure** (Vendi tramite Azure), immettere le immagini di tutti i logo. Per un elenco, sono necessari solo due logo. Usare le linee guida seguenti per ogni logo caricato nel portale Cloud Partner:     <br/> - Nel logo usare un numero ridotto di colori primari e secondari. La progettazione di Azure ha una tavolozza dei colori semplice.     <br/> - Evitare di usare i colori nero e bianco per lo sfondo del logo. I colori del tema del portale di Azure sono il bianco e il nero. Usare un colore che faccia risaltare il logo nel portale di Azure. Si consiglia di usare colori primari semplici. Se si usa lo sfondo trasparente, assicurarsi che i logo e il testo non siano bianchi, neri o blu.     <br/> - Non usare uno sfondo sfumato nel logo.     <br/> - Evitare di inserire testo, incluso il nome del marchio o della società, nel logo. L'aspetto del logo deve essere semplice e senza sfumature.    <br/> - L'immagine del logo non può essere estesa.                   |
| Video           | Consente di aggiungere collegamenti dei video dell'offerta. È possibile usare collegamenti a video di YouTube e/o Vimeo che vengono visualizzati insieme all'offerta ai clienti. È necessario anche immettere un'immagine di anteprima del video, con un'immagine png di 1280 x 720 pixel. È possibile avere un massimo di quattro video per ogni offerta. |
| Documenti        | Consente di aggiungere documenti di marketing all'offerta. Tutti i documenti devono essere in formato PDF ed è possibile avere un massimo di tre documenti per ogni offerta.                                                                                                                                                      |
| Screenshots (Schermate)      | Consente di aggiungere screenshot all'offerta. È possibile aggiungere all'offerta un massimo di cinque screenshot. La dimensione massima delle immagini è 1280 x 720 pixel.                                                                                                                                             |
| Collegamenti utili     | Consente di aggiungere URL esterni all'offerta per puntare a diagrammi delle architetture o altri siti Web che possono essere utili al cliente.                                                                                                                                                              |
|  |  |

L'immagine seguente mostra come vengono visualizzate le informazioni nei risultati di ricerca di Marketplace:

![Informazioni sull'editore marketing dell'offerta SaaS](media/cpp-creating-saas-offers/saas-new-offer-marketing-publisher-info.png)

L'immagine seguente mostra come viene visualizzata l'offerta in Marketplace dopo che l'utente ha fatto clic sul riquadro dell'offerta:

![Informazioni sull'editore marketing dell'offerta SaaS 2](media/cpp-creating-saas-offers/saas-new-offer-marketing-publisher-info-2.png)


### <a name="lead-management-and-legal-information"></a>Gestione lead e Note legali

La sezione Note legali consente di specificare la documentazione legale dell'offerta.
Per ogni offerta di applicazioni SaaS è necessario fornire due documenti legali: Informativa sulla privacy e Condizioni per l'utilizzo. Per altre informazioni, vedere  
[Configurare lead relativi ai clienti](./cloud-partner-portal-get-customer-leads.md).

![Informazioni legali di marketing dell'offerta SaaS](media/cpp-creating-saas-offers/saas-new-offer-legal-info.png)

La tabella seguente descrive le proprietà della sezione Note legali:

| **Campi offerta**   | **Descrizione**                                                                                 |
|--------------------| ----------------------------------------------------------------------------------------------- |
| URL dell'Informativa sulla privacy | URL dell'Informativa sulla privacy della società.                                                       |
| Condizioni per l'utilizzo       | Le condizioni per l'utilizzo dell'offerta. Digitare o copiare e incollare le condizioni per l'utilizzo qui. Poiché l'utilizzo di HTML di base è consentito, questo campo accetta contenuti HTML con tag come &ltp\>, &lth1\>, &lth2\>, &ltli\> e così via. *Importante* È consigliabile rivedere e visualizzare in anteprima il codice HTML creato in un browser prima di inviare l'offerta. |
|  |  |


### <a name="contact-information"></a>Informazioni di contatto

In questa sezione vengono immessi i contatti di supporto della società che saranno responsabili del supporto dei clienti che hanno aderito all'offerta.
Sono presenti tre aree principali: Engineering Contact (Contatto tecnico), Contatto per il supporto e URL supporto:

![Informazioni di contatto per il marketing dell'offerta SaaS](media/cpp-creating-saas-offers/saas-new-offer-contact-info.png)


| **Contatto**         | **Descrizione**                                                                                                                          |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| Engineering Contact (Contatto tecnico) | Specificare il nome, l'indirizzo di posta elettronica e il numero di telefono di un contatto tecnico che Microsoft può contattare per eventuali problemi di supporto e aziendali. |
| Contatto per il supporto     | Specificare il nome, l'indirizzo di posta elettronica, il numero di telefono e l'URL che i clienti possono contattare in caso di richieste di supporto.                  |
|  |  |


Quando l'offerta è pronta e si sceglie Pubblica, l'offerta passa alla fase di certificazione. L'app SaaS viene testata manualmente verificando l'URL in caso di elenco o gli endpoint se è stata selezionata l'opzione Sell through Azure (Vendi tramite Azure). Durante questa approvazione manuale, viene anche stabilita la vetrina in cui visualizzare l'app (AppSource, Azure Marketplace o entrambe).

<a name="updating-the-offer"></a>Aggiornamento dell'offerta
------------------

Sono disponibili diversi aggiornamenti che è possibile eseguire per l'offerta dopo che è stata pubblicata e attivata. Qualsiasi modifica apportata alla nuova versione dell'offerta deve essere salvata e ripubblicata per essere riportata in Marketplace.

<a name="deleting-an-existing-offer"></a>Eliminazione di un'offerta esistente
--------------------------

È possibile che si decida di rimuovere l'offerta da Marketplace. Rimozione offerta assicura che i nuovi clienti non possano più acquistare o distribuire l'offerta, ma non ha alcun effetto sui clienti esistenti. Risoluzione offerta è il processo di risoluzione del servizio e/o del contratto di licenza tra l'utente e i clienti esistenti.

Le indicazioni e i criteri relativi alla rimozione e alla risoluzione dell'offerta sono regolati dal Contratto per la pubblicazione in Microsoft Marketplace (vedere la sezione 7) e dai Criteri di partecipazione (vedere la sezione 6.2). Questa sezione descrive i diversi scenari di eliminazione supportati e le operazioni necessarie da intraprendere.

### <a name="delete-the-live-offer"></a>Eliminare l'offerta attiva

Esistono diversi aspetti che devono essere presi in considerazione nel caso di una richiesta di rimozione di un'offerta attiva. Per ottenere informazioni dal team di supporto su come rimuovere un'offerta attiva da Azure Marketplace, seguire questa procedura:

1.  Generare un ticket di supporto usando questo collegamento
2.  Nell'elenco Tipo di problema selezionare **Gestione delle offerte** e nell'elenco Categoria selezionare **Modifica un'offerta e/o SKU già in produzione**.
3.  Inviare la richiesta

Il team di supporto guiderà l'utente nel processo di eliminazione dell'offerta.

> [!NOTE] 
> L'eliminazione di un'offerta non ha effetti sugli acquisti dell'offerta in corso. Gli acquisti dei clienti continueranno a funzionare.
Tuttavia, l'offerta non sarà disponibile per eventuali nuovi acquisti dopo che l'eliminazione è stata completata.
