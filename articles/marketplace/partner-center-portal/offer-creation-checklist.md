---
title: Elenco di controllo per la creazione dell'offerta SaaS - Commercial Marketplace for Azure
description: I dettagli che puoi fornire nel processo di creazione dell'offerta SaaS. - Mercato commerciale per Azure
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 60111b6a23c76314383c5f95be3eb4b38f90d3e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281341"
---
# <a name="saas-offer-creation-checklist"></a>Elenco di controllo per la creazione di offerte SaaS

Il processo di creazione dell'offerta SaaS ti porterà attraverso più pagine. Ecco i dettagli che puoi fornire in ogni pagina, con link per saperne di più su ogni elemento.

Gli elementi che è necessario fornire o specificare sono indicati di seguito. Alcune aree sono facoltative o dispongono di valori predefiniti, che è possibile modificare in base alle si desidera. Non è necessario lavorare su queste sezioni nell'ordine elencato di seguito.

| **Elemento**    | **Scopo**  |
| :---------- | :-------------------|
| [**Nuova offerta Modale**](#new-offer-modal) | Le raccolte offrono informazioni sull'identità.  |
| [Pagina Impostazione offerta](#offer-setup-page) | Consente di scegliere di utilizzare le funzionalità principali e scegliere come vendere l'offerta tramite Microsoft.  |
| [Pagina delle proprietà](#properties-page) | Definisci le categorie e i settori utilizzati per raggruppare la tua offerta sui marketplace, i contratti legali che supportano la tua offerta e la versione dell'app. |
| [Pagina di inserzione dell'offerta](#offer-listing-page) | Definire i dettagli dell'offerta da visualizzare nel marketplace, incluse le descrizioni dell'offerta e le risorse di marketing. |
| [Anteprima pagina](#preview-page) | Definisci un pubblico di anteprima limitato per il rilascio dell'offerta prima di pubblicare l'offerta in diretta ai segmenti di pubblico del marketplace più ampi. |
| [Pagina di configurazione tecnica dell'offerta](#technical-configuration-page)  | Disponibile solo se si sceglie di vendere l'offerta tramite Microsoft. Definire i dettagli tecnici (percorso URL, webhook, ID tenant e ID app) usati per connettersi all'offerta. |
| [**Nuovo piano Modale**](#plan-identity-modal) | Raccoglie informazioni sull'identità del piano.  |
| [Pagina Disspagina di elenco del piano](#plan-listing-page)  | Disponibile solo se si sceglie di vendere l'offerta tramite Microsoft. Definire i dettagli utilizzati per elencare il piano nel marketplace.  |
| [Pagina Pianificare la & dei prezzi](#plan-pricing--availability-page)  | Disponibile solo se si sceglie di vendere l'offerta tramite Microsoft.  Raccoglie le caratteristiche aziendali (modello di determinazione dei prezzi), il pubblico e la disponibilità di mercato per ogni piano (versione) dell'offerta.  |
| [Pagina elenco unità di test](#test-drive-listing-page)  | Disponibile solo se si sceglie di offrire un test drive per l'offerta. Definire i dettagli utilizzati per elencare il test drive nel marketplace.  |
| Pagina di configurazione tecnica del test Drive  | Disponibile solo se si sceglie di offrire un test drive per l'offerta. Definire i dettagli tecnici per la dimostrazione (o "test drive") che consentirà ai clienti di provare l'offerta prima di impegnarsi ad acquistarla.  |
| [Pagina di revisione e pubblicazione](#review-and-publish-page)  | Selezionare le modifiche che si desidera pubblicare, visualizzare lo stato di ogni pagina e fornire note al team di certificazione.  |


## <a name="new-offer-modal"></a>Nuova offerta modale 

Le prime informazioni che ti verrà chiesto di fornire sono un ID e un alias per la tua offerta. 

| **Nome campo**    | **Note**   |  
| :---------------- | :-----------| 
| Offer ID (ID offerta)  | Obbligatorio, non può essere modificato dopo la creazione. Massimo 50 caratteri e deve essere costituito solo da caratteri minuscoli, alfanumerici, trattini o caratteri di sottolineatura. |
| Alias dell'offerta  | Obbligatorio. |

## <a name="offer-setup-page"></a>Pagina di configurazione dell'offerta

La pagina di configurazione dell'offerta è dove è possibile optare per diversi canali e movimenti di vendita, così come dichiarare l'uso di funzionalità chiave, come test drive e lead dei clienti. 

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------|  
| Vuoi vendere tramite Microsoft?  | Obbligatorio. Valore predefinito: Sì |
| Come vuoi che i potenziali clienti interagiscano con l'offerta? (Invito all'azione)  | Obbligatorio se non vendere tramite Microsoft. Impostazione predefinita: Versione di prova gratuita, Opzioni: "Ottieni ora", "Prova gratuita", "Contattami". |
| URL della versione di valutazione  | Obbligatorio se è selezionata l'opzione "Prova gratuita", in quanto il modo in cui i clienti devono interagire con l'elenco delle offerte. |
| URL offerta  | Obbligatorio se è selezionata l'opzione "Ottieni ora", in quanto il modo in cui i clienti devono interagire con l'offerta |
| Canali  | Facoltativa. Impostazione predefinita: non ha attivato il canale CSP (rivenditore).  |
| Test drive | Facoltativa. Impostazione predefinita: nessun test drive abilitato.  |
| Type of Test Drive (Tipo di test drive) | Obbligatorio se abilitato un test drive. Impostazione predefinita: nessuno selezionato. Opzioni: Azure Resource Manager, Dynamics 365 for Business Central, Dynamics 365 for Customer Engagement, Dynamics 365 for Operations, App per la logica, Power BI.  |
| Gestione lead - connettersi a un sistema CRM | Obbligatorio se si vende tramite Microsoft o se l'inserzione offre offerte come "Contattami". Impostazione predefinita: nessun sistema CRM connesso. Opzioni CRM: tabella di Azure, BLOB di Azure, Dynamics CRM online, endpoint HTTPs, Marketo, Salesforce  |

## <a name="properties-page"></a>Pagina Proprietà

La pagina delle proprietà consente di definire le categorie e i settori utilizzati per raggruppare l'offerta nei marketplace, i contratti legali che supportano l'offerta e la versione dell'app. Assicurati di fornire dettagli completi e precisi sulla tua offerta in questa pagina, in modo che venga visualizzata in modo appropriato e offerta al giusto set di clienti. 

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------|  
| Categoria e sottocategoria | Richiesto 1 e massimo 3. Impostazione predefinita: nessuno selezionato. |
| Industrie e sottoindustrie | Facoltativa. max 2 L1 Industries e max 2 sottoindustrie all'interno di ogni settore L1, Default: Nessuno selezionato |
| Versione dell'app  | Facoltativa. Impostazione predefinita: Nessuna. |
| Usa contratto standard  | Facoltativa. Impostazione predefinita: non selezionata.  | |
| Condizioni per l'utilizzo  | Obbligatorio se Non è selezionato Contratto standard.  |

## <a name="offer-listing-page"></a>Pagina di presentazione dell'offerta

La pagina di presentazione è dove fornisci il testo e le immagini che i clienti vedono quando visualizzano la presentazione della tua offerta nel marketplace. 

| **Nome campo**    | **Note**   |
| :---------------- | :-----------| 
| Nome  | Obbligatorio, massimo 50 caratteri. |
| Riepilogo  | Obbligatorio, massimo 100 caratteri. | 
| Descrizione  | Obbligatorio, massimo 3000 caratteri. |
| Getting Started Instructions (Istruzioni per le attività iniziali)  | Obbligatorio, massimo 3000 caratteri. |
| Getting Started Instructions (Istruzioni per le attività iniziali)  | Obbligatorio, massimo 3000 caratteri. |
| Search Keywords (Parole chiave di ricerca)  | Facoltativo, consigliato, massimo 3 parole chiave. |
| URL dell'informativa sulla privacy  | Obbligatorio. |
| URL dei materiali di marketing del programma CSP  | Facoltativa. |
| Link utili Titolo - URL  | Facoltativa. |
| Titolo documenti di supporto - File  | Obbligatorio, min 1 e max 3. Deve essere in formato PDF. |
| Screenshots (Schermate)  | Obbligatorio, min 1 screenshot e max 5; quattro o più raccomandati. Deve essere 1280 X 720 in formato PNG. |
| Loghi del negozio (Piccolo, Medio, Grande, Ampio, Eroe)  | Piccolo (48 X 48) e Grande (216 X 216) richiesto; altre dimensioni opzionali ma consigliate: Medio (90 x 90), Ampio (255 x 115), Eroe (815 x 290). Deve essere in formato PNG. |
| Nome dei video , URL e miniatura  | Facoltativo, consigliato, max 4 video. La miniatura deve essere 1280 x 720 in formato PNG. Il video deve essere ospitato su YouTube o Vimeo. |
| Contatti (Programma CSP, Ingegneria, Supporto)  | Necessità di contatto tecnico e di supporto (nome, e-mail e numero di telefono); CSP Programma contatto facoltativo ma consigliato. |
| URL del supporto  | Obbligatorio. |

## <a name="preview-page"></a>Pagina Anteprima

La pagina di anteprima è dove si specifica il pubblico di avere accesso all'anteprima dell'offerta, per verificare che l'offerta soddisfi tutte le vostre esigenze prima di andare in diretta. 

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------| 
| E-mail AAD/MSA - descrizione | Obbligatorio, min 1 e max 10 se immesso manualmente o fino a 20 se si carica un file CSV. |

## <a name="technical-configuration-page"></a>Pagina di configurazione tecnica 

La pagina di configurazione tecnica consente di specificare i dettagli tecnici utilizzati da Microsoft per connettersi all'offerta. Questa pagina non è visibile all'utente se si è deciso di non vendere tramite Microsoft.

| **Nome campo**    | **Note**   |  
| :---------------- | :-----------| 
| Landing Page URL (URL della pagina di destinazione) | Obbligatorio se si vende tramite Microsoft. |
| Collegamento webhook | Obbligatorio se si vende tramite Microsoft. |
| ID del tenant di Azure AD | Obbligatorio se si vende tramite Microsoft. |
| ID dell'app di Azure AD | Obbligatorio se si vende tramite Microsoft. |

## <a name="plan-identity-modal"></a>Pianificare l'identità modalePlan identity modal

Le prime informazioni che ti viene chiesto di fornire sono un nome e un ID per il tuo piano. Questa pagina non è visibile all'utente se si è deciso di non vendere tramite Microsoft.

| **Nome campo**    | **Note**   |  
| :---------------- | :-----------| 
| ID piano  | Obbligatorio se si vende tramite Microsoft. Non può essere modificato dopo la creazione. Massimo 50 caratteri e deve essere costituito solo da caratteri minuscoli, alfanumerici, trattini o caratteri di sottolineatura. |
| Plan Name  | Obbligatorio se si vende tramite Microsoft. Deve essere univoco in tutti i piani dell'offerta. Massimo 50 caratteri. |

## <a name="plan-listing-page"></a>Pagina di pianificazione dell'elenco

La pagina di presentazione del piano consente di fornire il testo da visualizzare ai clienti quando visualizza il piano nel marketplace. Questa pagina non è visibile all'utente se si è deciso di non vendere tramite Microsoft.

| **Nome campo**    | **Note**   |  
| :---------------- | :-----------| 
| Descrizione del piano   | Obbligatorio se si vende tramite Microsoft. Massimo 500 caratteri. | |

## <a name="plan-pricing--availability-page"></a>Pagina Pianificare la disponibilità del & dei prezzi

La pagina prezzi e disponibilità del piano consente di definire le caratteristiche aziendali, il pubblico e la disponibilità del mercato per ogni piano (versione) dell'offerta. Questa pagina non è visibile all'utente se si è deciso di non vendere tramite Microsoft.

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------| 
| Disponibilità del mercato  | Obbligatorio, min 1 e max 141. |
| Modello di prezzi  | Obbligatorio. Impostazione predefinita: Tariffa fissa. Opzioni: Tariffa fissa, per utente. |
| Posti minimi e massimi  | Facoltativo, disponibile solo se è selezionato il modello di determinazione dei prezzi basato su postazioni. |
| Termine di fatturazione  | Obbligatorio. Impostazione predefinita: Mensile. Opzioni: Mensile, Annuale. |
| Price  | USD al mese richiesto, se è selezionata la durata della fatturazione mensile; o USD all'anno se viene selezionato il termine di fatturazione annuale. |
| Pianificare il pubblico  | Facoltativa. Impostazione predefinita: piano pubblico. Opzioni: Pubblico, Privato per ID tenant |
| Destinatari di piani con restrizioni (ID tenant e descrizione)  | Obbligatorio se il piano privato è selezionato. ID tenant min 1 e max 10 se immessi manualmente. Max 20000 se importazione di file CSV. |

## <a name="test-drive-listing-page"></a>Pagina di presentazione di Test Drive

Disponibile solo se si sceglie di offrire un test drive per l'offerta. Definire i dettagli utilizzati per elencare il test drive nel marketplace.

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------| 
| Descrizione  | Obbligatorio. |
| Nome manuale dell'utente : file  | Obbligatorio, max 1 doc. Deve essere in formato PDF. |
| Nome del video, URL e miniatura  | Facoltativo, consigliato. La miniatura deve essere 533 x 324 in formato JPGP o PNG. Il video deve essere ospitato su YouTube o Vimeo. |

## <a name="review-and-publish-page"></a>Pagina di revisione e pubblicazione

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------| 
| Note per la certificazione  | Facoltativa. |

## <a name="next-steps"></a>Passaggi successivi

- [Creare una nuova offerta SaaS](./create-new-saas-offer.md)
