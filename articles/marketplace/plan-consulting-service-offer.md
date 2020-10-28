---
title: Come pianificare un'offerta del servizio di consulenza per Microsoft Commercial Marketplace
description: Come pianificare una nuova offerta del servizio di consulenza per Microsoft AppSource o Azure Marketplace tramite il programma Commercial Marketplace nel centro per i partner Microsoft.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: dfc812771f0eeb8dcb0d95bdad1e76ed0b12297e
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754436"
---
# <a name="how-to-plan-a-consulting-service-offer-in-the-commercial-marketplace"></a>Come pianificare un'offerta del servizio di consulenza nel Marketplace commerciale

Questo articolo presenta le diverse opzioni e i requisiti per la pubblicazione di offerte di servizi di consulenza nel Marketplace commerciale Microsoft. Leggere questo articolo per preparare l'offerta per la pubblicazione con il centro per i partner.

## <a name="whats-a-consulting-service"></a>Che cos'è un servizio di consulenza?

I servizi di consulenza sono impegni dei clienti virtuali o di persone che supportano ed estendono l'utilizzo dei prodotti Microsoft da parte dei clienti. Grazie a un servizio di consulenza, i clienti possono valutare, valutare e distribuire le soluzioni che si basano sugli obiettivi aziendali. Si scelgono l'ambito, la durata e la struttura dei prezzi (a prezzo fisso o gratuito) del servizio.

Le offerte del servizio di consulenza sono pubblicate come elenchi di **contatti** . Ciò significa che i clienti contatteranno direttamente le informazioni fornite nell'inserzione. Sebbene Microsoft ospiti l'elenco nel Marketplace commerciale, è possibile gestire tutte le interazioni con i clienti. L'utente è responsabile dell'evasione del servizio, della fatturazione, della fatturazione e della raccolta dal cliente.

## <a name="primary-products-and-online-stores"></a>Prodotti primari e negozi online

Ogni offerta del servizio di consulenza dovrebbe concentrarsi su un prodotto Microsoft, denominato **prodotto primario** . Il prodotto principale selezionato nel centro per i partner determina se l'offerta verrà pubblicata in Microsoft AppSource o in Azure Marketplace.

* Se il prodotto principale del servizio di consulenza è Azure, l'offerta verrà elencata in Azure Marketplace.
* Se il prodotto principale non è Azure, l'offerta sarà elencata in AppSource.

Per ulteriori informazioni sulle differenze tra AppSource e Azure Marketplace, vedere la pagina relativa ai [negozi online di Marketplace commerciali](./overview.md#commercial-marketplace-online-stores).

## <a name="eligibility-requirements"></a>Requisiti di idoneità

Per dimostrare ai clienti le proprie competenze in un campo, è necessario soddisfare una serie di requisiti di idoneità prima di pubblicare un'offerta di servizio di consulenza. I requisiti dipendono dal prodotto alla base dell'offerta. L'elenco completo dei requisiti di idoneità per ogni prodotto primario è nei [criteri di certificazione per i servizi di consulenza](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services).

> [!NOTE]
> Per alcuni prodotti primari, è necessario disporre di una competenza Microsoft Gold o Silver nell'area della soluzione. Per altre informazioni, vedere [Microsoft Partner Network le competenze](https://partner.microsoft.com/membership/competencies).

## <a name="service-type-and-duration"></a>Tipo di servizio e durata

Il Marketplace commerciale supporta cinque tipi di servizi di consulenza:

* **Valutazione** : valutazione dell'ambiente di un cliente per determinare l'applicabilità di una soluzione e per stimare il costo e la sequenza temporale della relativa implementazione.
* **Briefing** : Introduzione a una soluzione o a un servizio che usa Framework, demo ed esempi di clienti.
* **Implementazione** : un'installazione completa che produce una soluzione completamente funzionante.
* Modello **di prova** : implementazione con ambito limitato per determinare se una soluzione soddisfa i requisiti del cliente.
* **Workshop** : impegno interattivo condotto sulle sedi del cliente. Può comportare training, briefing, valutazioni o demo basate sui dati o sull'ambiente del cliente.

Il servizio deve avere una durata fissa e predeterminata di un massimo di 10 settimane. La durata del servizio deve essere resa esplicita nell'elenco delle offerte.

## <a name="customer-leads"></a>Clienti potenziali

Per raccogliere informazioni sui clienti, è necessario connettere l'offerta al sistema CRM (Customer Relationship Management). Al cliente verrà chiesta l'autorizzazione per condividere le informazioni. Queste informazioni sui clienti, insieme al nome dell'offerta, all'ID e al negozio online in cui è stata trovata l'offerta, verranno inviate al sistema CRM configurato. Il Marketplace commerciale supporta diversi tipi di sistemi CRM, oltre all'opzione per l'uso di una tabella di Azure o la configurazione di un endpoint HTTPS usando Power Automate.

È possibile aggiungere o modificare una connessione CRM in qualsiasi momento durante o dopo la creazione dell'offerta. Per istruzioni dettagliate, vedere [Lead dei clienti dall'offerta del Marketplace commerciale](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="offer-listing-details"></a>Dettagli elenco offerte

Quando si crea l'offerta del servizio di consulenza nel centro per i partner, si immetteranno testo, immagini, documenti e altri dettagli dell'offerta. Questo è ciò che i clienti visualizzeranno quando scoprono l'offerta in AppSource o in Azure Marketplace. Vedere l'esempio seguente:

![Viene illustrato come viene visualizzata un'offerta del servizio di consulenza negli archivi online](./media/example-consulting-service.png)

**Descrizioni di chiamata**

1. Logo
2. Tipo di servizio
3. Competenze
4. Aree della soluzione (Azure Marketplace)/prodotti (AppSource)
5. Settori
6. Paese/Area geografica
7. Nome offerta
8. Riepilogo risultati ricerca
9. Descrizione
10. Screenshot/video
11. Documenti di supporto

> [!NOTE]
> Se si distribuisce il servizio in una lingua diversa dall'inglese, l'elenco di offerte può trovarsi in tale lingua, ma la descrizione deve iniziare o terminare con la frase inglese "questo servizio è disponibile nella &lt; lingua del contenuto dell'offerta>". È anche possibile fornire documenti di supporto in una lingua diversa da quella usata nell'elenco dei dettagli dell'offerta.

Per semplificare la creazione dell'offerta, è possibile preparare alcuni di questi elementi in anticipo. Se non diversamente specificato, sono necessari gli elementi seguenti.

**Nome** : questo nome verrà visualizzato come titolo dell'inserzione dell'offerta nel Marketplace commerciale. Il nome può essere un marchio registrato. Non può contenere Emoji (a meno che non si tratti del marchio e dei simboli di copyright) e deve essere limitato a 50 caratteri. Il nome deve includere la durata e il tipo di servizio dell'offerta per ottimizzare l'ottimizzazione del motore di ricerca (SEO). Il formato richiesto è *Nome: durata + tipo* . Non includere il nome della società, a meno che non sia anche il nome del prodotto. Ecco alcuni esempi:

|Non dire |Pronunciare la frase  |
|---|---|
|Introduzione ad Azure IoT nel settore della produzione |IoT nel settore della produzione: valutazione di 2 giorni |
|Workshop sui toaster intelligenti |Tostapane intelligenti: workshop di 1 settimana |
|SQL Server della migrazione PoC di contoso |Migrazione a SQL: modello di verifica di 3 giorni |

**Riepilogo risultati ricerca** : descrivere lo scopo o l'obiettivo dell'offerta in caratteri di 200 o meno. Questo riepilogo viene usato nei risultati della ricerca nell'elenco del Marketplace commerciale. Non deve essere identico al titolo. È consigliabile includere le parole chiave SEO principali.

**Descrizione** : questa descrizione verrà visualizzata nell'elenco del Marketplace commerciale. Si consiglia di includere una proposta di valore, i vantaggi principali, la base utente desiderata e qualsiasi associazione di categoria o settore.

Quando si scrive la descrizione, attenersi a questi criteri, in base al tipo di servizio:

|Tipo di servizio |Descrizione requisiti |
|---|---|
|Valutazione |Includere un'agenda dettagliata per le valutazioni più lunga di un giorno e articolare il risultato finale che il cliente può aspettarsi. |
|Briefing |Descrizione del risultato finale che il cliente può aspettarsi.|
|Implementazione |Includere un'agenda dettagliata per le implementazioni più lunga di un giorno e descrivere le modifiche di progettazione, gli artefatti tecnici o altri artefatti che un cliente può aspettarsi come risultato dell'engagement. |
|Modello di verifica |Descrivere le modifiche di progettazione, gli artefatti tecnici o altri artefatti che un cliente può aspettarsi come risultato dell'engagement. |
|Workshop |Includere un'agenda dettagliata giornaliera, settimanale o mensile, a seconda della durata dell'offerta. Descrivere in modo articolato gli obiettivi di apprendimento o altri risultati finali del workshop. |

Ecco alcuni suggerimenti per scrivere la descrizione:

* Descrivere chiaramente il valore dell'offerta nelle prime frasi includendo:
    * Tipo di utente che ottiene vantaggi dall'offerta.
    * Esigenze del cliente o problemi risolti con l'offerta.
* Ricordare che le prime frasi possono essere visualizzate nei risultati di ricerca.
* Se il prezzo dell'offerta è stimato, spiegare quali variabili determineranno il prezzo finale.
* Usare il vocabolario specifico del settore.

È possibile usare i tag HTML per formattare la descrizione. È possibile immettere fino a 2.000 caratteri di testo in questa casella, inclusi i tag HTML e gli spazi. Per informazioni sulla formattazione HTML, vedere [tag HTML supportati nelle descrizioni delle offerte del Marketplace commerciale](./supported-html-tags.md).

**Parole chiave di ricerca** (facoltativo): fornire fino a tre parole chiave di ricerca che i clienti possono usare per trovare l'offerta negli archivi online. Non è necessario includere il **nome** dell'offerta e la **Descrizione** .

**Durata** : l'offerta del servizio di consulenza deve avere una durata predeterminata di un massimo di 10 settimane.

**Informazioni di contatto** : nel centro per i partner verrà richiesto di specificare il nome, l'indirizzo di posta elettronica e il numero di telefono di due persone nell'azienda. è possibile essere uno dei due contatti. Queste informazioni verranno usate per comunicare con l'utente sulla propria offerta. Queste informazioni non vengono visualizzate ai clienti, ma possono essere fornite ai partner Cloud Solution Provider (CSP).

**Documenti di supporto** : caricare almeno uno e un massimo di tre documenti PDF rivolte ai clienti che forniscono informazioni sull'offerta. Ad esempio, possono essere white paper o opuscoli.

**Supporti del Marketplace-Logos** : fornire un file PNG per il logo di grandi dimensioni. Il centro per i partner lo userà per creare un logo di piccole dimensioni. Facoltativamente, è possibile sostituire il logo di piccole dimensioni con un'immagine diversa in un secondo momento.

* Grande (da 216 x 216 a 350 x 350 px, obbligatorio)
* Piccolo (48 x 48 px, facoltativo)

Il logo di grandi dimensioni viene visualizzato nella pagina di presentazione dell'offerta in Azure Marketplace o in AppSource. Il logo piccolo viene visualizzato nei risultati della ricerca di Azure Marketplace o nella pagina principale e nei risultati della ricerca di AppSource.

Seguire queste linee guida per i logo:

* Assicurarsi che l'immagine non sia sfocata o allungata.
* La progettazione di Azure ha una tavolozza dei colori semplice. Limitare il numero di colori primari e secondari nel logo.
* Se si usa uno sfondo trasparente, verificare che i logo e il testo non siano bianchi, neri o blu.
* Evitare gradienti nel logo o nello sfondo. Non inserire testo, nemmeno il nome del marchio o della società, sul logo.

**Supporti Marketplace: schermate** : aggiungere almeno una e fino a cinque immagini che dimostrano l'offerta. Tutte le immagini devono avere una dimensione di 1280 x 720 pixel e in. Formato PNG.

**Supporti del Marketplace-video** (facoltativo): è possibile aggiungere fino a quattro video che illustrano l'offerta. I video devono essere ospitati in YouTube o Vimeo e avere un'anteprima (file PNG 1280 x 720).

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'offerta di servizio di consulenza nel Marketplace commerciale](./create-consulting-service-offer.md)
* [Procedure consigliate per le inserzioni di offerte](./gtm-offer-listing-best-practices.md)
