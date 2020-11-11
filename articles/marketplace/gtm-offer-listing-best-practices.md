---
title: Procedure consigliate per la presentazione di offerte - Marketplace commerciale Microsoft
description: Informazioni sulle procedure consigliate per la presentazione go-to market delle offerte di Microsoft AppSource e Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 07/06/2020
ms.openlocfilehash: 3ea6a0035a9f9354be5c14699936c6a07dea1150
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94492090"
---
# <a name="offer-listing-best-practices"></a>Procedure consigliate per le inserzioni di offerte

Questo articolo fornisce suggerimenti per la creazione e il coinvolgimento delle offerte di Marketplace commerciali Microsoft. Le tabelle seguenti illustrano le procedure consigliate per il completamento delle informazioni sull'offerta nel Centro per i partner. Per un'analisi delle prestazioni delle offerte, passare al [dashboard Informazioni dettagliate sul marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/marketplaceinsights) nel Centro per i partner. 

## <a name="online-store-offer-details"></a>Dettagli offerta negozio online

| Impostazione | Procedura consigliata |
|:--- |:--- |  
| Nome offerta | Per le app, fornire un titolo chiaro che includa parole chiave di ricerca per aiutare i clienti a individuare l'offerta. <br> <br> Per i servizi di consulenza, seguire questo formato: [nome offerta: [durata] [tipo di offerta] (ad esempio, contoso: implementazione di 2 settimane) |
| Descrizione dell'offerta | Fornire una descrizione chiara che descriva la proposta di valore dell'offerta nelle prime frasi.  Tenere presente che queste frasi possono essere usate nei risultati dei motori di ricerca. I componenti principali della proposta di valore devono includere: <ul> <li>Descrizione del prodotto o della soluzione. </li> <li> Utente persona che trae vantaggio dal prodotto o dalla soluzione. </li> <li> I clienti hanno bisogno o sono in sofferenza degli indirizzi del prodotto o della soluzione. </li> </ul> <br> Usare il vocabolario standard del settore o la formulazione basata sui vantaggi, quando possibile.  Per vendere un prodotto, non basarsi sulle funzionalità,  ma concentrarsi sul valore che può offrire. <br> <br> Per gli elenchi di servizi di consulenza, indicare chiaramente il servizio Professional fornito. |

> [!IMPORTANT]
> Verificare che il nome dell'offerta e la descrizione dell'offerta siano conformi alle **[linee guida](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general.aspx)** per il marchio e il marchio Microsoft e altre linee guida specifiche per il prodotto, quando si fa riferimento a marchi Microsoft e ai nomi di software, prodotti e servizi Microsoft.

## <a name="online-store-listing-details"></a>Dettagli dell'elenco di negozi online

Le categorie e i settori per un negozio online diverso saranno applicabili ai diversi tipi di offerta.

| Archivio online | Categorie <br>per archivio online | Categorie <br>per archivio online | Settori <br> per AppSource |
| :------------------- |:----------------:|:------:|:-------------:|
| **Tipo di offerta**   |  **Azure Marketplace**  | **AppSource**  |
| App Azure | x | |
| Contenitore | x | |
| Servizi di consulenza | | | x |
| Dynamics 365 Customer Engagement & Power Platform | | X | X |
| Gestione della supply chain & di Dynamics 365 Finance | | X | X | 
| Dynamics 365 Business Central | | X | X |
| Moduli IoT Edge | x | |
| Power BI | | X | X |
| SaaS | X | X | X |
| Macchina virtuale di Azure |  x |    |

### <a name="categories"></a>Categorie

Microsoft AppSource e Azure Marketplace sono negozi online che offrono tipi diversi di soluzioni. Azure Marketplace offre soluzioni IT basate su o per Azure.  Microsoft AppSource offrire soluzioni aziendali, ad esempio applicazioni SaaS del settore, componenti aggiuntivi di Dynamics 365, componenti aggiuntivi Microsoft 365 e app Power Platform.

Le categorie e le sottocategorie sono mappate a ogni negozio online in base al tipo di soluzione. L'offerta verrà pubblicata in Microsoft AppSource o in Azure Marketplace a seconda del tipo di offerta, delle funzionalità di transazione dell'offerta e della selezione di categoria/sottocategoria. 

Selezionare le categorie e le sottocategorie che meglio si allineano con il tipo di soluzione. È possibile selezionare:

* Fino a due categorie, tra cui una categoria primaria e una secondaria (facoltativo).
* Fino a due sottocategorie per ogni categoria primaria e/o secondaria. Se non è selezionata alcuna sottocategoria, l'offerta sarà comunque individuabile solo per la categoria selezionata.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

#### <a name="important-saas-offers-and-microsoft-365-add-ins"></a>IMPORTANTE: offerte SaaS e componenti aggiuntivi Microsoft 365

Per informazioni dettagliate sul modo in cui le funzionalità di transazione possono influire sul modo in cui l'offerta può essere visualizzata e acquistata dai clienti del Marketplace, vedere la pagina relativa al [mercato commerciale](marketplace-commercial-transaction-capabilities-and-considerations.md) . Per le offerte SaaS, la funzionalità di transazione dell'offerta e la selezione delle categorie determinano il negozio online in cui verrà pubblicata l'offerta.


| Offerta SaaS    | Offerta SaaS   | Offerta SaaS  | Offerta SaaS   | Offerta SaaS   | Offerta SaaS   | Offerta SaaS    | Negozio online applicabile| Negozio online applicabile |
|:-------------:|:---:|:--------:|:---------:|:--:|:--:|:---:|:---------------------:|:-------------:|
| Fatturazione a consumo | Microsoft 365 componenti aggiuntivi | Contact me (Contattami) | Transact (almeno 1 piano) | Piano solo privato | Piano solo pubblico | Piani privati & pubblici | AppSource | Azure Marketplace |
|  | X |  |  |  |  |  | X |  |
| X |  |  | X | X |  |  |  | X |
| X |  |  | X |  | X |  |  | X |
| X |  |  | X |  |  | X |  | X<sup>2</sup> |
|  |  |  | X | X |  |  |  | X |
|  |  |  | X |  | X |  | X<sup>1</sup> | X<sup>1</sup> |
|  |  |  | X |  |  | X | X<sup>1</sup> | X<sup>1, 2</sup> |
|  |  | x |  |  |  |  | x<sup>1</sup> | X<sup>1</sup> | 

1. A seconda della categoria/sottocategoria e della selezione del settore
2. Le offerte con piani privati verranno pubblicate nel portale di Azure


### <a name="industries"></a>Settori

La selezione del settore si applica solo alle offerte pubblicate in AppSource e ai servizi di consulenza pubblicati in Azure Marketplace.  Selezionare i settori e/o i verticali se l'offerta soddisfa le esigenze specifiche del settore, evidenziando le competenze specifiche di settore nella descrizione dell'offerta. È possibile selezionare fino a due (2) settori e due (2) verticali per ogni settore selezionato.

>[!Note]
>Per le offerte di servizi di consulenza in Azure Marketplace, non sono disponibili verticali di settore.

| **Settori** |  **Verticali** |
| :------------------- | :----------------|
| **Agricoltura** | |
| **Architettura & costruzione** | |
| **Automobilistico** | |
| **Distribuzione** | Vendita all'ingrosso <br> Spedizione pacchetti & pacco |  
| **Education** | Istruzione superiore <br> Primario & secondario edu/K-12 <br> Librerie & musei |
| **Servizi finanziari** | Settore bancario & mercati finanziari <br> Assicurazioni | 
| **Enti pubblici** |  Difesa & Intelligence <br> Governo civile <br> Sicurezza pubblica & giustizia |
| **Servizi sanitari** | Pagante di integrità <br> Provider di integrità <br> Prodotti farmaceutici | 
| **Ospitalità & viaggio** | Travel & Transportation <br> Alberghi & Leisure <br> Ristoranti & servizi alimentari | 
| **Produzione di risorse &** | & chimico agrochimica <br> Produzione discreta <br> Energia | 
| **Media & comunicazioni** | Media & Entertainment <br> Telecomunicazioni | 
| **Altri settori pubblici** | Selvicoltura & pesca <br> No profit | 
| **Servizi professionali** | Servizi professionali per i partner <br> Legale | 
| **Immobile** | |

Settore solo per Microsoft AppSource:

| **Settore** |  **Verticali** |
| :------------------- | :----------------|
| **Vendita al dettaglio & beni di consumo** | Rivenditori <br> Beni di consumo |

### <a name="applicable-products"></a>Prodotti applicabili

Selezionare i prodotti applicabili con cui funziona l'app per consentire la visualizzazione dell'offerta tra i prodotti selezionati in AppSource.

### <a name="search-keywords"></a>Search Keywords (Parole chiave di ricerca)

Le parole chiave consentono ai clienti di trovare l'offerta durante la ricerca. Identificare le parole chiave di ricerca principali per l'offerta, incorporarle nel riepilogo e nella descrizione dell'offerta, oltre che nella sezione relativa alle parole chiave nei dettagli della presentazione dell'offerta.

## <a name="online-store-marketing-details"></a>Dettagli di marketing dell'archivio online
| Impostazione | Procedura consigliata |
|:--- |:--- |  
| Logo dell'offerta (formato PNG, da 216 × 216 a 350 x 350 px): pagina dei dettagli dell'app | Progettare e ottimizzare il logo per un supporto digitale:<br>Caricare il logo in formato PNG nella pagina dell'inserzione con i dettagli dell'app dell'offerta. Il centro per i partner verrà ridimensionato in modo da configurarne le dimensioni necessarie. |
| Logo dell'offerta (formato PNG, 48 × 48 pixel): pagina di ricerca | Il centro per i partner genererà questo logo dal logo di grandi dimensioni caricato. Facoltativamente, è possibile sostituire questo oggetto con un'immagine diversa in un secondo momento. |
| Documenti "Altre informazioni" | Nella sezione "Altre informazioni" includere risorse di supporto per le vendite e il marketing, ad esempio:<ul><li>White paper</li><li> Brochure</li><li>Elenchi di controllo</li><li> Presentazioni di PowerPoint</li></ul><br>Salvare tutti i file in formato PDF. L'obiettivo di questi documenti deve essere l'informazione, non la vendita ai clienti.<br><br>Nella pagina di destinazione dell'app inserire un collegamento a tutti i documenti e aggiungere parametri dell'URL per tenere traccia dei visitatori e dell'uso delle versioni di valutazione. |
| Video: solo AppSource, servizi di consulenza e offerte SaaS | I video più efficaci comunicano il valore dell'offerta in forma narrativa:<ul> <li> Il protagonista deve essere il cliente e non la società che presenta l'offerta. </li> <li> Il video deve dare risposte riguardo alle sfide e agli obiettivi fondamentali del cliente a cui ci si rivolge. </li> <li> Lunghezza consigliata: da 60 a 90 secondi.</li> <li> Includere parole chiave di ricerca che usano il nome dei video. </li> <li> Valutare l'opportunità di aggiungere altri video, ad esempio per illustrare procedure, fornire un avvio rapido o presentare la testimonianza di un cliente. </li> </ul> |
| Screenshot (1280&nbsp;&times;&nbsp;720) | Aggiungere al massimo cinque screenshot:<br>Includere parole chiave di ricerca nei nomi file. |

## <a name="link-to-your-offer-page-from-your-website"></a>Creare un collegamento alla pagina dell'offerta dal sito Web

Quando si effettua il collegamento dalla notifica di AppSource o Azure Marketplace nel sito all'inserzione nel Marketplace commerciale, è possibile supportare l'analisi avanzata e la creazione di report includendo i parametri di query seguenti alla fine dell'URL:
* **src** : includere l'origine da cui il traffico viene instradato ad AppSource (ad esempio, sito Web, LinkedIn o Facebook).
* **mktcmpid** : ID della campagna di marketing, che può contenere fino a 16 caratteri, costituiti da lettere, numeri, caratteri di sottolineatura e trattini (ad esempio, *blogpost_12* ).

L'URL di esempio seguente contiene entrambi i parametri di query precedenti: `https://appsource.microsoft.com/product/dynamics-365/mscrm.04931187-431c-415d-8777-f7f482ba8095?src=website&mktcmpid=blogpost_12`

Aggiungendo questi parametri all'URL di AppSource, è possibile verificare l'efficacia della campagna nel dashboard di analisi del [Centro per i partner](https://partner.microsoft.com/dashboard/commercial-marketplace/).

## <a name="next-steps"></a>Passaggi successivi

Vedere altre informazioni sui [vantaggi del marketplace commerciale](./gtm-your-marketplace-benefits.md).

Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) per creare e configurare l'offerta.
