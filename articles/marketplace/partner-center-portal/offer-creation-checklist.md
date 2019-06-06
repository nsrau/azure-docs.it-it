---
title: "Elenco di controllo: creazione dell'offerta commerciale Marketplace per Azure"
description: I dettagli è possibile specificare nel processo di creazione offerta. -Commerciale Marketplace per Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 904058c2c98c8ded2ea9c91e8aa7ec595aa49b05
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481445"
---
# <a name="offer-creation-checklist"></a>Elenco di controllo per la creazione di offerte

Il processo di creazione offerta verrà visualizzata tra le diverse pagine. Ecco i dettagli che è possibile specificare in ogni pagina con collegamenti per ulteriori informazioni su ogni elemento.

Viene richiesto di fornire o specificare gli elementi sono riportati di seguito. Alcune aree sono facoltative o sono forniti valori predefiniti, che è possibile modificare in base alle esigenze. Non è necessario lavorare su queste sezioni nell'ordine indicato di seguito.

| **Elemento**    | **Scopo**  |
| :---------- | :-------------------|
| [**Nuova offerta modale**](#new-offer-modal) | Raccoglie informazioni sull'identità dell'offerta.  |
| [Pagina di installazione dell'offerta](#offer-setup-page) | Consente di acconsentire esplicitamente a usare le funzionalità principali e scegliere la modalità per la vendita dell'offerta tramite il Microsoft.  |
| [Pagina delle proprietà](#properties-page) | Definire le categorie e settori utilizzati per raggruppare l'offerta nei Marketplace, i contratti legali che supportano l'offerta e la versione dell'app. |
| [Pagina di inserzione dell'offerta](#offer-listing-page) | Definire i dettagli dell'offerta deve essere visualizzato nel marketplace, incluse le descrizioni dell'offerta e risorse di marketing. |
| [Pagina anteprima](#preview-page) | Definire un numero limitato di utenti di anteprima per il rilascio dell'offerta prima della pubblicazione dell'offerta in tempo reale per gli utenti più ampi a marketplace. |
| [Pagina Configurazione tecnici dell'offerta](#technical-configuration-page)  | È disponibile solo se si seleziona per la vendita dell'offerta tramite il Microsoft. Definire i dettagli tecnici (percorso dell'URL webhook, ID tenant e ID app) utilizzati per la connessione per l'offerta. |
| [**Nuovo piano modale**](#plan-identity-modal) | Raccoglie le informazioni di identità del piano.  |
| [Pianificare la pagina di presentazione](#plan-listing-page)  | È disponibile solo se si seleziona per la vendita dell'offerta tramite il Microsoft. Definisci i dettagli consente di elencare il piano in marketplace.  |
| [Prezzi del piano & pagina disponibilità](#plan-pricing--availability-page)  | È disponibile solo se si seleziona per la vendita dell'offerta tramite il Microsoft.  Raccoglie le caratteristiche di business (modello di determinazione prezzi), la disponibilità di destinatari e sul mercato per ogni piano (versione) dell'offerta.  |
| [Pagina di presentazione di test unità](#test-drive-listing-page)  | È disponibile solo se si sceglie di offrire una test drive per l'offerta. Definisci i dettagli usati all'elenco il test drive del Marketplace.  |
| Pagina di configurazione di tecniche di test unità  | È disponibile solo se si sceglie di offrire una test drive per l'offerta. Definire i dettagli tecnici per la dimostrazione (o "prova") che consentiranno ai clienti di provare l'offerta prima del commit per acquistarlo.  |
| [Rivedere e pagina di pubblicazione](#review-and-publish-page)  | Selezionare le modifiche che si desidera pubblicare, visualizzare lo stato di ogni pagina e fornire note al team di certificazione.  |


## <a name="new-offer-modal"></a>Nuova offerta modale 

Il primo verrà richiesto di fornire le informazioni sono un nome e un ID dell'offerta. 

| **Nome campo**    | **Note**   |  
| :---------------- | :-----------| 
| Offer ID (ID offerta)  | Obbligatorio, non può essere modificato dopo la creazione. Numero massimo di 50 caratteri e deve contenere solo caratteri alfanumerici minuscoli, trattini o caratteri di sottolineatura. |
| Nome offerta  | Richiesto. |

## <a name="offer-setup-page"></a>Pagina di installazione dell'offerta

La pagina di installazione offerta è in cui è possibile acconsentire esplicitamente diversi canali e i movimenti di vendita, nonché dichiarare che comporta l'uso delle funzionalità chiave, ad esempio versione di test e dei clienti. 

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------|  
| Vuoi vendere tramite Microsoft?  | Richiesto. Predefinito: Yes |
| Specificare la modalità potenziali clienti per interagire con l'offerta riporti? (Invito all'azione)  | Obbligatorio se non di vendita Microsoft. Predefinito: Versione di valutazione gratuita, le opzioni: "Scarica adesso", "Versione di valutazione gratuita", "Contact me." |
| URL della versione di valutazione  | Obbligatorio se è selezionata la "Versione di valutazione gratuita", come i clienti modo devono interagire con l'elenco di offerta. |
| URL dell'offerta  | Obbligatorio se "Ottenere adesso" è selezionata, come i clienti modo devono interagire con l'elenco di offerta |
| Canali  | facoltativo. Predefinito: Non è acconsentito esplicitamente nel canale di CSP (rivenditore).  |
| Test drive | facoltativo. Predefinito: Nessuna versione di test abilitata.  |
| Type of Test Drive (Tipo di test drive) | Obbligatorio se è abilitata una test drive. Predefinito: Nessun elemento selezionato. Opzioni: Gestione risorse di Azure, Dynamics 365 per centrali di Business, Dynamics 365 for Customer Engagement, Dynamics 365 per le app per la logica, operazioni di Power BI.  |
| Gestione dei lead: connettersi a un sistema CRM | Obbligatorio se vendere attraverso Microsoft o elenco sono disponibili come "Contact me." Valore predefinito: nessun sistema CRM connesso. Opzioni di CRM: Tabelle di Azure, blob di Azure, Dynamics CRM online, endpoint degli HTTPs, Marketo, Salesforce  |

## <a name="properties-page"></a>Pagina Proprietà

La pagina delle proprietà viene usata per definire le categorie e settori utilizzati per raggruppare l'offerta nei Marketplace, i contratti legali che supportano l'offerta e la versione dell'app. Assicurarsi di fornire informazioni dettagliate complete e accurate sull'offerta in questa pagina, in modo che viene visualizzato in modo appropriato e disponibili per il set corretto di clienti. 

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------|  
| Categoria e sottocategoria | Richiesto 3 1, quello massimo. Predefinito: Nessun elemento selezionato. |
| Settori e subindustries | facoltativo. numero massimo di 2 L1 settori e numero massimo di 2 subindustries all'interno di ogni settore L1, impostazione predefinita: Nessuna selezione |
| Versione dell'app  | facoltativo. Predefinito: No. |
| Utilizzare il contratto Standard  | facoltativo. Valore predefinito: non è selezionata.  | |
| Condizioni per l'utilizzo  | Obbligatorio se il contratto Standard non è selezionato.  |

## <a name="offer-listing-page"></a>Pagina di inserzione dell'offerta

Pagina di presentazione è possibile fornire il testo e immagini che i clienti riscontrano quando si visualizza l'inserzione dell'offerta nel marketplace. 

| **Nome campo**    | **Note**   |
| :---------------- | :-----------| 
| NOME  | Obbligatoria, numero massimi di 50 caratteri. |
| Riepilogo  | Obbligatoria, numero massimi di 100 caratteri. | 
| Descrizione  | Obbligatoria, numero massimi di 3000 caratteri. |
| Getting Started Instructions (Istruzioni per le attività iniziali)  | Obbligatoria, numero massimi di 3000 caratteri. |
| Getting Started Instructions (Istruzioni per le attività iniziali)  | Obbligatoria, numero massimi di 3000 caratteri. |
| Search Keywords (Parole chiave di ricerca)  | Facoltativo, consigliata, numero massimo di 3 le parole chiave. |
| Privacy policy URL  | Richiesto. |
| URL materiali di Marketing programma CSP  | facoltativo. |
| Collegamenti utili Title e URL  | facoltativo. |
| Supporto documenta titolo + File  | Obbligatorio, 1 min e max 3. Deve essere formato di file PDF. |
| Screenshots (Schermate)  | Obbligatorio, schermata 1 min e max 5; quattro o più consigliati. Deve essere in formato PNG di 1280 X 720. |
| Logo di Store (Small, Medium, Large, Wide, Hero)  | Small (48 X 48) e Large (216x216) richiesto. altre dimensioni facoltativi ma consigliato: Medium (90 x 90), a livello (255 x 115), Hero (815 x 290). Deve essere nel formato PNG. |
| Nome video + URL anteprima  | Facoltativo, consigliata, numero massimo di 4 video. L'anteprima deve essere 1280 x 720 nel formato PNG. Video deve essere ospitato in YouTube o Vimeo. |
| Contatti (programma CSP, progettazione, supporto)  | Progettazione e contatto del supporto tecnico necessario (nome, indirizzo di posta elettronica e numero di telefono); Contattare il programma CSP facoltativo ma consigliato. |
| URL del supporto  | Richiesto. |

## <a name="preview-page"></a>Pagina anteprima

Pagina di anteprima è possibile specificare i destinatari di accedere all'anteprima di offerta, verificare che l'offerta soddisfi tutti i requisiti prima che venga attivato. 

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------| 
| Posta elettronica AAD/MSA + descrizione | Richiesto, 1 min e max 10 se inserite manualmente oppure fino a 20 se caricare un file CSV. |

## <a name="technical-configuration-page"></a>Pagina Configurazione tecnici 

La pagina di configurazione tecnica è possibile specificare i dettagli tecnici usati da Microsoft per la connessione per l'offerta. Questa pagina non è visibile all'utente se è stato deciso di non Vendi tramite Microsoft.

| **Nome campo**    | **Note**   |  
| :---------------- | :-----------| 
| Landing Page URL (URL della pagina di destinazione) | Obbligatorio se vendere tramite Microsoft. |
| Webhook di connessione | Obbligatorio se vendere tramite Microsoft. |
| ID del tenant di Azure AD | Obbligatorio se vendere tramite Microsoft. |
| ID dell'app di Azure AD | Obbligatorio se vendere tramite Microsoft. |

## <a name="plan-identity-modal"></a>Piano di identità modale

Il primo viene richiesto di fornire le informazioni sono un nome e un ID per il piano. Questa pagina non è visibile all'utente se si è deciso di non Vendi tramite Microsoft.

| **Nome campo**    | **Note**   |  
| :---------------- | :-----------| 
| ID del piano  | Obbligatorio se vendere tramite Microsoft. Non può essere modificato dopo la creazione. Numero massimo di 50 caratteri e deve contenere solo caratteri alfanumerici minuscoli, trattini o caratteri di sottolineatura. |
| Nome del piano  | Obbligatorio se vendere tramite Microsoft. Deve essere univoco in tutti i piani dell'offerta. Massimo 50 caratteri. |

## <a name="plan-listing-page"></a>Pianificare la pagina di presentazione

Il piano di pagina di presentazione è possibile fornire il testo per i clienti per vedere quando si visualizza il piano in marketplace. Questa pagina non è visibile all'utente se è stato deciso di non Vendi tramite Microsoft.

| **Nome campo**    | **Note**   |  
| :---------------- | :-----------| 
| Descrizione piano   | Obbligatorio se vendere tramite Microsoft. Numero massimo 500 caratteri. | |

## <a name="plan-pricing--availability-page"></a>Pagina del piano di prezzi e disponibilità

La pagina dei prezzi e disponibilità di piano viene usata per definire le caratteristiche di business, destinatari e la disponibilità di mercato per ogni piano (versione) dell'offerta. Questa pagina non è visibile all'utente se è stato deciso di non Vendi tramite Microsoft.

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------| 
| Disponibilità di immissione sul mercato  | Obbligatorio, 1 min e max 141. |
| Modello di prezzi  | Richiesto. Predefinito: Tariffa fissa. Opzioni: Tariffa fissa per ogni utente. |
| Valore minimo e massime postazioni  | Facoltativo, è disponibile solo se basati sulle postazioni piano tariffario selezionato. |
| Periodo di fatturazione  | Richiesto. Predefinito: Mensile. Opzioni: Mensile, annuale. |
| Prezzo  | Obbligatorio USD al mese, se la fatturazione mensile termine selezionato. o USD all'anno se annuali billing termine selezionato. |
| Piano di destinatari  | facoltativo. Predefinito: Piano pubblico. Opzioni: Pubblico, privato dall'ID tenant |
| Destinatari di pianificare con restrizioni (ID tenant + descrizione)  | Obbligatorio se piano privato selezionato. 1 min e max 10 tenant ID se immessi manualmente. Max 20000 se l'importazione di file CSV. |

## <a name="test-drive-listing-page"></a>Pagina di presentazione di test unità

È disponibile solo se si sceglie di offrire una test drive per l'offerta. Definisci i dettagli usati all'elenco il test drive del Marketplace.

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------| 
| Descrizione  | Richiesto. |
| Nome di manuali utente e file  | Obbligatoria, numero massimo di 1 documento. Devono essere in formato PDF. |
| Nome video, URL + anteprima  | Facoltativo, consigliata. L'anteprima deve essere 533, 324 in formato JPGP o PNG. Video deve essere ospitato in YouTube o Vimeo. |

## <a name="review-and-publish-page"></a>Rivedere e pagina di pubblicazione

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------| 
| Note per la certificazione  | facoltativo. |

## <a name="next-steps"></a>Passaggi successivi

- [Creare una nuova offerta SaaS](./create-new-saas-offer.md)
