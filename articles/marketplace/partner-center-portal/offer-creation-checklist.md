---
title: Elenco di controllo per la creazione di offerte SaaS-Marketplace commerciale per Azure
description: I dettagli che è possibile fornire nel processo di creazione dell'offerta SaaS. -Marketplace commerciale per Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 66c28039f9126ed9e3f56c3ac15b1b3d82279b64
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036366"
---
# <a name="saas-offer-creation-checklist"></a>Elenco di controllo per la creazione di offerte SaaS

Il processo di creazione dell'offerta SaaS consentirà di eseguire più pagine. Di seguito sono riportati i dettagli che è possibile fornire in ogni pagina, con collegamenti per ulteriori informazioni su ogni elemento.

Gli elementi necessari per fornire o specificare sono indicati di seguito. Alcune aree sono facoltative o sono disponibili valori predefiniti, che è possibile modificare nel modo desiderato. Non è necessario lavorare a queste sezioni nell'ordine indicato.

| **Elemento**    | **Scopo**  |
| :---------- | :-------------------|
| [**Nuova offerta modale**](#new-offer-modal) | Raccoglie le informazioni sull'identità dell'offerta.  |
| [Pagina installazione offerta](#offer-setup-page) | Consente di acconsentire esplicitamente a usare le funzionalità principali e scegliere come vendere l'offerta tramite Microsoft.  |
| [Pagina delle proprietà](#properties-page) | Definire le categorie e i settori usati per raggruppare l'offerta nei Marketplace, i contratti legali che supportano l'offerta e la versione dell'app. |
| [Pagina di inserzione dell'offerta](#offer-listing-page) | Definire i dettagli dell'offerta da visualizzare nel Marketplace, incluse le descrizioni dell'offerta e degli asset di marketing. |
| [Pagina anteprima](#preview-page) | Definire un gruppo di destinatari di anteprima limitato per il rilascio dell'offerta prima di pubblicare l'offerta in un gruppo più ampio di destinatari del Marketplace. |
| [Pagina di configurazione tecnica offerta](#technical-configuration-page)  | Disponibile solo se si sceglie di vendere l'offerta tramite Microsoft. Definire i dettagli tecnici (percorso URL, webhook, ID tenant e ID app) usati per connettersi all'offerta. |
| [**Nuovo piano modale**](#plan-identity-modal) | Raccoglie le informazioni sull'identità del piano.  |
| [Pagina di elenco del piano](#plan-listing-page)  | Disponibile solo se si sceglie di vendere l'offerta tramite Microsoft. Definire i dettagli usati per elencare il piano nel Marketplace.  |
| [Pianificare i prezzi & pagina di disponibilità](#plan-pricing--availability-page)  | Disponibile solo se si sceglie di vendere l'offerta tramite Microsoft.  Raccoglie le caratteristiche di business (modello di determinazione dei prezzi), i destinatari e la disponibilità del mercato per ogni piano (versione) dell'offerta.  |
| [Pagina di elenco di test drive](#test-drive-listing-page)  | Disponibile solo se si sceglie di offrire un test drive per l'offerta. Definire i dettagli usati per elencare i test drive nel Marketplace.  |
| Pagina configurazione tecnica test drive  | Disponibile solo se si sceglie di offrire un test drive per l'offerta. Definire i dettagli tecnici per la dimostrazione (o "test drive") che consentirà ai clienti di provare l'offerta prima di eseguire il commit per acquistarla.  |
| [Pagina Revisione e pubblicazione](#review-and-publish-page)  | Selezionare le modifiche che si desidera pubblicare, visualizzare lo stato di ogni pagina e fornire note al team di certificazione.  |


## <a name="new-offer-modal"></a>Nuova offerta modale 

Le prime informazioni che verrà richiesto di fornire sono un ID e un alias per l'offerta. 

| **Nome campo**    | **Note**   |  
| :---------------- | :-----------| 
| ID offerta  | Obbligatorio, non può essere modificato dopo la creazione. Massimo 50 caratteri e devono essere costituiti solo da lettere minuscole, caratteri alfanumerici, trattini o caratteri di sottolineatura. |
| Alias offerta  | Richiesto. |

## <a name="offer-setup-page"></a>Pagina installazione offerta

Nella pagina installazione offerta è possibile scegliere tra diversi canali e vendere i movimenti, nonché dichiarare l'utilizzo di funzionalità chiave, ad esempio test drive e lead clienti. 

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------|  
| Si vuole vendere tramite Microsoft?  | Richiesto. Valore predefinito: Sì |
| In che modo si desidera che i potenziali clienti interagiscano con l'elenco delle offerte? (Chiamata all'azione)  | Obbligatorio se non si vendono tramite Microsoft. Valore predefinito: Versione di valutazione gratuita, opzioni: "Richiedi ora", "versione di valutazione gratuita", "contattami". |
| URL della versione di valutazione  | Obbligatorio se è selezionata l'opzione "versione di valutazione gratuita", come il modo in cui i clienti devono interagire con l'elenco delle offerte. |
| URL offerta  | Obbligatorio se è selezionata l'opzione "Ottieni ora", poiché il modo in cui i clienti devono interagire con l'elenco delle offerte |
| Canali  | facoltativo. Valore predefinito: Non è stato scelto il canale CSP (Reseller).  |
| Test Drive | facoltativo. Valore predefinito: Nessuna test drive abilitata.  |
| Type of Test Drive (Tipo di test drive) | Obbligatorio se è abilitato un test drive. Valore predefinito: Nessuna selezione. Opzioni: Azure Resource Manager, Dynamics 365 for business Central, Dynamics 365 per Customer Engagement, Dynamics 365 per Operations, app per la logica, Power BI.  |
| Gestione dei lead: connettersi a un sistema CRM | Obbligatorio se si vendono tramite Microsoft o se le offerte sono elencate come "Contattatemi". Impostazione predefinita: non è connesso alcun sistema CRM. Opzioni CRM: Tabella di Azure, BLOB di Azure, Dynamics CRM Online, HTTPs ' endpoint, Marketo, Salesforce  |

## <a name="properties-page"></a>Pagina Proprietà

La pagina delle proprietà consente di definire le categorie e i settori usati per raggruppare l'offerta nei Marketplace, i contratti legali che supportano l'offerta e la versione dell'app. Assicurarsi di fornire dettagli completi e precisi sull'offerta in questa pagina, in modo che vengano visualizzati in modo appropriato e offerti al set di clienti corretto. 

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------|  
| Categoria e sottocategoria | Obbligatorio 1 e max 3. Valore predefinito: Nessuna selezione. |
| Settori e sottosettori | facoltativo. Max 2 industrie L1 e max 2 sottosettori in ogni settore L1, valore predefinito: Nessuna selezione |
| Versione dell'app  | facoltativo. Valore predefinito: No. |
| USA contratto standard  | facoltativo. Impostazione predefinita: non selezionata.  | |
| Condizioni per l'utilizzo  | Obbligatorio se il contratto standard non è selezionato.  |

## <a name="offer-listing-page"></a>Pagina di inserzione dell'offerta

Nella pagina di presentazione viene fornito il testo e le immagini che i clienti visualizzano durante la visualizzazione dell'inserzione dell'offerta nel Marketplace. 

| **Nome campo**    | **Note**   |
| :---------------- | :-----------| 
| Name  | Obbligatorio, massimo 50 caratteri. |
| Riepilogo  | Obbligatorio, massimo 100 caratteri. | 
| DESCRIZIONE  | Obbligatorio, massimo 3000 caratteri. |
| Getting Started Instructions (Istruzioni per le attività iniziali)  | Obbligatorio, massimo 3000 caratteri. |
| Getting Started Instructions (Istruzioni per le attività iniziali)  | Obbligatorio, massimo 3000 caratteri. |
| Search Keywords (Parole chiave di ricerca)  | Parole chiave facoltative, consigliate e max 3. |
| URL informativa sulla privacy  | Richiesto. |
| URL dei materiali di marketing del programma CSP  | facoltativo. |
| Titolo dei collegamenti utili + URL  | facoltativo. |
| Titolo e file di supporto per documenti  | Obbligatorio, minimo 1 e massimo 3. Deve essere un formato di file PDF. |
| Screenshot  | Obbligatorio, minimo 1 screenshot e max 5; quattro o più consigliati. Deve essere 1280 X 720 in formato PNG. |
| Logo dello Store (Small, medium, large, Wide, Hero)  | Small (48 X 48) e Large (216 X 216) obbligatorio; altre dimensioni facoltative ma consigliate: Media (90 x 90), Wide (255 x 115), Hero (815 x 290). Deve essere in formato PNG. |
| Nome video + URL + anteprima  | Video facoltativi, consigliati, massimo 4. L'anteprima deve essere 1280 x 720 in formato PNG. Il video deve essere ospitato in YouTube o Vimeo. |
| Contatti (programma CSP, progettazione, supporto)  | È necessario un contatto tecnico e di supporto (nome, indirizzo di posta elettronica e numero di telefono); Il contatto del programma CSP è facoltativo ma consigliato. |
| URL del supporto  | Richiesto. |

## <a name="preview-page"></a>Pagina anteprima

La pagina di anteprima consente di specificare i destinatari per accedere all'anteprima dell'offerta, per verificare che l'offerta soddisfi tutti i requisiti prima che diventi disponibile. 

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------| 
| Posta elettronica e descrizione di AAD/MSA | Obbligatorio, min 1 e Max 10 se immesso manualmente o fino a 20 se si carica un file CSV. |

## <a name="technical-configuration-page"></a>Pagina configurazione tecnica 

Nella pagina configurazione tecnica è possibile specificare i dettagli tecnici utilizzati da Microsoft per connettersi all'offerta. Questa pagina non è visibile se si è deciso di non vendere tramite Microsoft.

| **Nome campo**    | **Note**   |  
| :---------------- | :-----------| 
| Landing Page URL (URL della pagina di destinazione) | Obbligatorio se si vendono tramite Microsoft. |
| Webhook di connessione | Obbligatorio se si vendono tramite Microsoft. |
| ID del tenant di Azure AD | Obbligatorio se si vendono tramite Microsoft. |
| ID dell'app di Azure AD | Obbligatorio se si vendono tramite Microsoft. |

## <a name="plan-identity-modal"></a>Modalità di identità del piano

Le prime informazioni da fornire sono un nome e un ID per il piano. Questa pagina non è visibile se si è deciso di non vendere tramite Microsoft.

| **Nome campo**    | **Note**   |  
| :---------------- | :-----------| 
| ID piano  | Obbligatorio se si vendono tramite Microsoft. Non può essere modificato dopo la creazione. Massimo 50 caratteri e devono essere costituiti solo da lettere minuscole, caratteri alfanumerici, trattini o caratteri di sottolineatura. |
| Nome piano  | Obbligatorio se si vendono tramite Microsoft. Deve essere univoco in tutti i piani dell'offerta. Massimo 50 caratteri. |

## <a name="plan-listing-page"></a>Pagina di elenco del piano

La pagina di presentazione del piano è la posizione in cui si fornisce il testo per i clienti da visualizzare quando si Visualizza il piano nel Marketplace. Questa pagina non è visibile se si è deciso di non vendere tramite Microsoft.

| **Nome campo**    | **Note**   |  
| :---------------- | :-----------| 
| Descrizione piano   | Obbligatorio se si vendono tramite Microsoft. Massimo 500 caratteri. | |

## <a name="plan-pricing--availability-page"></a>Pianificare i prezzi & pagina di disponibilità

Nella pagina piano prezzi e disponibilità è possibile definire le caratteristiche aziendali, i destinatari e la disponibilità del mercato per ogni piano (versione) dell'offerta. Questa pagina non è visibile se si è deciso di non vendere tramite Microsoft.

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------| 
| Disponibilità del mercato  | Obbligatorio, minimo 1 e massimo 141. |
| Modello di determinazione prezzi  | Richiesto. Valore predefinito: Frequenza fissa. Opzioni: Frequenza fissa, per utente. |
| Numero minimo e massimo di postazioni  | Facoltativo, disponibile solo se è selezionato il modello di determinazione prezzi basato sul posto. |
| Termine fatturazione  | Richiesto. Valore predefinito: Mensile. Opzioni: Mensile, annuale. |
| Prezzo  | USD necessari al mese, se è stato selezionato un termine di fatturazione mensile; o USD all'anno se è stato selezionato un termine di fatturazione annuale. |
| Pianificare i destinatari  | facoltativo. Valore predefinito: Piano pubblico. Opzioni: Pubblico, privato in base all'ID tenant |
| Destinatari del piano limitato (ID tenant + Descrizione)  | Obbligatorio se è stato selezionato un piano privato. Min 1 e Max 10 ID tenant se immessi manualmente. Max 20000 se importazione file CSV. |

## <a name="test-drive-listing-page"></a>Pagina di elenco di test drive

Disponibile solo se si sceglie di offrire un test drive per l'offerta. Definire i dettagli usati per elencare i test drive nel Marketplace.

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------| 
| Descrizione  | Richiesto. |
| Nome manuale utente + file  | Obbligatorio, massimo 1 doc. Deve essere in formato PDF. |
| Nome del video, URL + anteprima  | Facoltativo, consigliato. L'anteprima deve essere 533 x 324 in formato JPGP o PNG. Il video deve essere ospitato in YouTube o Vimeo. |

## <a name="review-and-publish-page"></a>Pagina Revisione e pubblicazione

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------| 
| Note per la certificazione  | facoltativo. |

## <a name="next-steps"></a>Passaggi successivi

- [Creare una nuova offerta SaaS](./create-new-saas-offer.md)
