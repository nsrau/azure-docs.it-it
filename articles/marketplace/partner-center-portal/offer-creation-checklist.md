---
title: Elenco di controllo per la creazione di un'offerta SaaS in Microsoft Commercial Marketplace
description: I dettagli che è possibile fornire nel processo di creazione dell'offerta SaaS sono disponibili nel centro per i partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 11c1c307d00b9347081a313308ad2467086ec208
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327396"
---
# <a name="saas-offer-creation-checklist-in-partner-center"></a>Elenco di controllo per la creazione di un'offerta SaaS nel centro

Il processo di creazione dell'offerta SaaS Visualizza più pagine.  Questo articolo descrive i dettagli che è possibile fornire in ogni pagina, con collegamenti per ottenere ulteriori informazioni su ogni elemento.

> [!NOTE]
> Se si sta creando un'offerta SaaS transazionale, assicurarsi di implementare l'integrazione con le [API di evasione Saas](./pc-saas-fulfillment-apis.md).  L'integrazione con le API è l'unico modo per garantire il corretto funzionamento della transazione nel Marketplace. È anche necessario assicurarsi che l'app usi Azure AD autenticazione con Single Sign-on (SSO). Vedere [Azure ad e le offerte SaaS transazionali nel Marketplace commerciale](../azure-ad-saas.md).

Gli elementi che è necessario fornire o specificare sono indicati di seguito.  Alcune aree sono facoltative o prevedono valori predefiniti che è possibile modificare come si desidera.  Non è necessario esaminare le sezioni nell'ordine indicato.

| **Elemento**    | **Scopo**  |
| :---------- | :-------------------|
| [**Finestra modale Nuova offerta**](#new-offer-modal) | Raccoglie le informazioni relative all'identità dell'offerta.  |
| [Pagina Configurazione dell'offerta](#offer-setup-page) | Consente di acconsentire esplicitamente all'uso delle funzionalità chiave e di scegliere come vendere l'offerta tramite Microsoft.  |
| [Pagina Proprietà](#properties-page) | Consente di definire le categorie e i settori usati per raggruppare l'offerta nei marketplace, i contratti legali che supportano l'offerta e la versione dell'app. |
| [Pagina Presentazione dell'offerta](#offer-listing-page) | Consente di definire i dettagli dell'offerta da visualizzare nel marketplace, tra cui le descrizioni dell'offerta e gli asset di marketing.|
| [Pagina Anteprima](#preview-page) | Consente di definire un numero di destinatari limitato per l'anteprima per il rilascio dell'offerta prima di pubblicarla e renderla disponibile a un pubblico del marketplace più ampio.|
| [Pagina configurazione tecnica](#technical-configuration-page)  |  Disponibile solo se si sceglie di vendere l'offerta tramite Microsoft.  Definire i dettagli tecnici (URL della pagina di destinazione, URL del webhook di connessione, ID tenant Azure AD e ID app Azure AD) usati da Marketplace per connettersi all'offerta.  Questi parametri sono necessari per integrarsi correttamente con l'evasione SaaS e le API per la fatturazione a consumo del Marketplace.|
| [**Finestra modale Nuovo piano**](#plan-identity-modal) | Raccoglie le informazioni relative all'identità del piano.  |
| [Pagina Elenco piani](#plan-listing-page)  | Disponibile solo se si sceglie di vendere l'offerta tramite Microsoft. Consente di definire i dettagli usati per la presentazione del piano nel marketplace.  |
| [Pagina Plan Pricing & Availability](#plan-pricing--availability-page) (Disponibilità e prezzi del piano)  | Disponibile solo se si sceglie di vendere l'offerta tramite Microsoft.  Raccoglie le caratteristiche di business (modello di determinazione prezzi), i destinatari e la disponibilità di mercato per ogni piano (versione) dell'offerta.  |
| [Pagina Presentazione test drive](#test-drive-listing-page)  | Disponibile solo se si sceglie di offrire un test drive per l'offerta. Consente di definire i dettagli usati per la presentazione del test drive nel marketplace.  |
| Pagina Configurazione tecnica del test drive  | Disponibile solo se si sceglie di offrire un test drive per l'offerta. Consente di definire i dettagli tecnici per la dimostrazione (o "test drive") che consentirà ai clienti di provare l'offerta prima di impegnarsi ad acquistarla.  |
| [Pagina Rivedi e pubblica](#review-and-publish-page)  | Consente di selezionare le modifiche da pubblicare, visualizzare lo stato di ogni pagina e specificare note per il team di certificazione.  |

## <a name="new-offer-modal"></a>Finestra modale Nuova offerta

Le prime informazioni che è necessario fornire sono un ID e un alias per l'offerta.

| **Nome campo**    | **Note**   |  
| :---------------- | :-----------| 
| ID offerta  | Obbligatorio. Non può essere modificato dopo la creazione. Deve essere composto da un massimo di 50 caratteri e contenere solo caratteri alfanumerici minuscoli, trattini o caratteri di sottolineatura. |
| Alias offerta  | Obbligatorio. |

## <a name="offer-setup-page"></a>Pagina Configurazione dell'offerta

Nella pagina di configurazione dell'offerta è possibile acconsentire esplicitamente all'uso di diversi canali e proposte di vendita, nonché dichiarare l'uso di funzionalità chiave, ad esempio test drive e lead clienti. 

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------|  
| Vendere tramite Microsoft?  | Obbligatorio. Predefinito: Sì |
| Come si vuole che i potenziali clienti interagiscano con questa offerta? (Invito all'azione)  | Obbligatorio se non si vende tramite Microsoft. Predefinito: Versione di prova gratuita. Opzioni: "Scarica ora", "Versione di prova gratuita", "Contatta". |
| URL della versione di valutazione  | Obbligatorio se è selezionata l'opzione "Versione di prova gratuita" come modo in cui i clienti devono interagire con la presentazione dell'offerta. |
| URL dell'offerta  | Obbligatorio se è selezionata l'opzione "Scarica ora" come modo in cui i clienti devono interagire con la presentazione dell'offerta. |
| Canali  | facoltativo. Predefinito: nessun consenso esplicito per il canale CSP (rivenditore).  |
| Test drive | facoltativo. Predefinito: nessun test drive abilitato.  |
| Tipo di test drive | Obbligatorio se è abilitato un test drive. Predefinito: nessuna selezione. Opzioni: Azure Resource Manager, Dynamics 365 for Business Central, Dynamics 365 for Customer Engagement, Dynamics 365 for Operations, App per la logica, Power BI.  |
| Customer leads - connect to a CRM system (Lead cliente - connessione a un sistema CRM) | Obbligatorio se si vende tramite Microsoft o se la presentazione dell'offerta prevede l'opzione "Contatta". Impostazione predefinita: nessun sistema CRM connesso. Opzioni CRM: Tabella di Azure, BLOB di Azure, Dynamics CRM Online, Endpoint HTTPS, Marketo, Salesforce  |

## <a name="properties-page"></a>Pagina Proprietà

La pagina Proprietà consente di definire le categorie e i settori usati per raggruppare l'offerta nei marketplace, i contratti legali che supportano l'offerta e la versione dell'app. Assicurarsi di fornire dettagli completi e accurati sull'offerta in questa pagina, affinché l'offerta venga visualizzata in modo appropriato e presentata al set di clienti adeguato. 

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------|  
| Categoria e sottocategoria | Obbligatorio, minimo 1 valore e massimo 3. Predefinito: nessuna selezione. |
| Industries and subindustries (Settori e sottosettori) | facoltativo. Massimo 2 settori di livello 1 e 2 sottosettori in ogni settore di livello 1. Impostazione predefinita: nessuna selezione. |
| Versione dell'app  | facoltativo. Predefinito: No. |
| Use Standard Contract (Usa contratto standard)  | Facoltativa. Impostazione predefinita: nessuna selezione.  | |
| Condizioni per l'utilizzo  | Obbligatorio se non è selezionato il contratto standard.  |

## <a name="offer-listing-page"></a>Pagina Presentazione dell'offerta

Nella pagina di presentazione dell'offerta è possibile fornire il testo e le immagini che i clienti vedranno quando visualizzano la presentazione dell'offerta nel marketplace. 

| **Nome campo**    | **Note**   |
| :---------------- | :-----------| 
| Nome  | Obbligatorio, massimo 50 caratteri. |
| Summary  | Obbligatorio, massimo 100 caratteri. | 
| Descrizione  | Obbligatorio, massimo 3000 caratteri. |
| Getting Started Instructions (Istruzioni per le attività iniziali)  | Obbligatorio, massimo 3000 caratteri. |
| Getting Started Instructions (Istruzioni per le attività iniziali)  | Obbligatorio, massimo 3000 caratteri. |
| Search Keywords (Parole chiave di ricerca)  | Facoltativo, consigliato, massimo 3 parole chiave. |
| URL dell'informativa sulla privacy  | Obbligatorio. |
| URL Materiale marketing del programma CSP  | facoltativo. |
| Collegamenti utili (titolo + URL)  | facoltativo. |
| Documenti di supporto (titolo + file)  | Obbligatorio, minimo 1 e massimo 3. Il formato di file deve essere PDF. |
| Screenshots (Schermate)  | Obbligatorio, minimo 1 screenshot e massimo 5, consigliati quattro o più. Il formato deve essere PNG con dimensioni 1280 x 720. |
| Logo dello Store (piccolo, medio, grande, largo)  | Small (48 X 48) e Large (216 X 216) obbligatorio; altre dimensioni facoltative ma consigliate: media (90 x 90), Wide (255 x 115). Deve essere in. Formato PNG. |
| Video (nome + URL + anteprima)  | Facoltativo, consigliato, massimo 4 video. L'anteprima deve essere in formato PNG con dimensioni 1280 x 720. Il video deve essere ospitato in YouTube o Vimeo. |
| Contatti (programma CSP, tecnico, supporto)  | Contatti tecnico e di supporto obbligatori (nome, indirizzo di posta elettronica e numero di telefono), contatto del programma CSP facoltativo ma consigliato. |
| URL del supporto  | Obbligatorio. |

## <a name="preview-page"></a>Pagina Anteprima

La pagina di anteprima consente di specificare i destinatari che possono accedere all'anteprima dell'offerta, al fine di verificare che l'offerta soddisfi tutti i requisiti prima di pubblicarla. 

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------| 
| AAD/account del servizio gestito (indirizzo di posta elettronica + descrizione) | Obbligatorio, minimo 1 e massimo 10 in caso di immissione manuale o fino a 20 se si carica un file CSV. |

## <a name="technical-configuration-page"></a>Pagina Configurazione tecnica 

Nella pagina relativa alla configurazione tecnica è possibile specificare i dettagli tecnici usati da Microsoft per connettersi all'offerta. Questa pagina non è visibile se si è deciso di non vendere tramite Microsoft.

> [!NOTE]
> Per le offerte transazionali, è necessario creare una pagina di destinazione e l'app deve usare Azure AD autenticazione con Single Sign-on (SSO). Per ulteriori informazioni, vedere la pagina relativa alle [offerte SaaS Azure ad e transazionali nel Marketplace commerciale](../azure-ad-saas.md).

| **Nome campo**    | **Note**   |  
| :---------------- | :-----------| 
| Landing Page URL (URL della pagina di destinazione) | Obbligatorio se si vende tramite Microsoft. |
| Webhook connessione | Obbligatorio se si vende tramite Microsoft. |
| ID del tenant di Azure AD | Obbligatorio se si vende tramite Microsoft. |
| ID dell'app di Azure AD | Obbligatorio se si vende tramite Microsoft. |

## <a name="plan-identity-modal"></a>Finestra modale Plan identity (Identità piano)

Le prime informazioni che è necessario fornire sono un nome e un ID per il piano. Questa pagina non è visibile se si è deciso di non vendere tramite Microsoft.

| **Nome campo**    | **Note**   |  
| :---------------- | :-----------| 
| ID piano  | Obbligatorio se si vende tramite Microsoft. Non può essere modificato dopo la creazione. Deve essere composto da un massimo di 50 caratteri e contenere solo caratteri alfanumerici minuscoli, trattini o caratteri di sottolineatura. |
| Nome piano  | Obbligatorio se si vende tramite Microsoft. Deve essere univoco tra tutti i piani nell'offerta. Massimo 50 caratteri. |

## <a name="plan-listing-page"></a>Pagina Elenco piani

Nella pagina di presentazione dei piani è possibile fornire il testo che i clienti vedranno quando visualizzano il piano nel marketplace. Questa pagina non è visibile se si è deciso di non vendere tramite Microsoft.

| **Nome campo**    | **Note**   |  
| :---------------- | :-----------| 
| Descrizione piano   | Obbligatorio se si vende tramite Microsoft. Massimo 500 caratteri. | |

## <a name="plan-pricing--availability-page"></a>Pagina Plan Pricing & Availability (Disponibilità e prezzi del piano)

Nella pagina relativa a prezzi e disponibilità del piano è possibile definire le caratteristiche di business, i destinatari e la disponibilità di mercato per ogni piano (versione) dell'offerta. Questa pagina non è visibile se si è deciso di non vendere tramite Microsoft.

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------| 
| Disponibilità sul mercato  | Obbligatorio, minimo 1 e massimo 141. |
| Modello di prezzi  | Obbligatorio. Predefinito: Tariffa fissa. Opzioni: Tariffa fissa, Per utente. |
| Minimum and maximum seats (Postazioni minime e massime)  | Facoltativo, disponibile solo se è selezionato il modello di determinazione prezzi basato sulle postazioni. |
| Periodo di fatturazione  | Obbligatorio. Predefinito: Mensile. Opzioni: Mensile, Annuale. |
| Prezzo  | Importo richiesto al mese, se è stato selezionato il periodo di fatturazione mensile oppure importo all'anno se è stato selezionato il periodo di fatturazione annuale. |
| Destinatari del piano  | Facoltativa. Predefinito: piano pubblico. Opzioni: Pubblico, Privato in base a ID tenant |
| Restricted Plan Audience (Destinatari limitati piano) (ID tenant + descrizione)  | Obbligatorio se è stato selezionato un piano privato. Minimo 1 e massimo 10 ID tenant in caso di immissione manuale. Massimo 20000 se si importa un file CSV. |

## <a name="test-drive-listing-page"></a>Pagina Presentazione test drive

Disponibile solo se si sceglie di offrire un test drive per l'offerta. Consente di definire i dettagli usati per la presentazione del test drive nel marketplace.

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------| 
| Descrizione  | Obbligatorio. |
| Manuale dell'utente (nome + file)  | Obbligatorio, massimo 1 documento. Il formato deve essere PDF. |
| Nome video (URL + anteprima)  | Facoltativo, consigliato. L'anteprima deve essere in formato JPGP o PNG con dimensioni 533 x 324. Il video deve essere ospitato in YouTube o Vimeo. |

## <a name="review-and-publish-page"></a>Pagina Rivedi e pubblica

| **Nome campo**    | **Note**   | 
| :---------------- | :-----------| 
| Note per la certificazione  | Facoltativa. |

## <a name="next-steps"></a>Passaggi successivi

- [Creare una nuova offerta SaaS](./create-new-saas-offer.md)
