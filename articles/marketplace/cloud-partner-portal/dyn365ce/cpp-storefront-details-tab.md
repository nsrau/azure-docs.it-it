---
title: Scheda Storefront Details (Dettagli vetrina) dell'offerta Dynamics 365 for Customer Engagement - Azure Marketplace | Microsoft Docs
description: Usare la scheda Storefront Details (Dettagli vetrina) per specificare le informazioni di vendita e marketing per un'offerta Dynamics 365 for Customer Engagement.
services: Dynamics 365 for Customer Engagement, Azure, Marketplace, Cloud Partner Portal, AppSource
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
ms.date: 01/03/2019
ms.author: pbutlerm
ms.openlocfilehash: 62220104329e36fbb2e6f7d3b5b48bcaeaba3660
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082079"
---
# <a name="dynamics-365-for-customer-engagement-storefront-details-tab"></a>Scheda Storefront Details (Dettagli vetrina) di Dynamics 365 for Customer Engagement

Usare la scheda **Storefront Details** (Dettagli vetrina) per fornire informazioni e artefatti di vendita e marketing.  Questa scheda contiene le sei sezioni seguenti: **Offer Details** (Dettagli dell'offerta), **Listing Details** (Dettagli dell'inserzione), **Marketing Artifacts** (Artefatti di marketing), **Legal** (Note legali), **Customer Support** (Assistenza clienti), **Lead Management** (Gestione clienti potenziali).  Un asterisco (*) alla fine del nome del campo indica che il campo è obbligatorio.


## <a name="offer-details-section"></a>Sezione Offer Details (Dettagli dell'offerta)

In questa sezione si inseriscono il riepilogo e la descrizione dell'offerta.  Queste informazioni saranno visualizzate in posizione prominente nel marketplace.
 
![Sezione dettagli offerta della scheda Storefront Details (Dettagli vetrina)](./media/storefront-tab-offer-details.png)

La tabella seguente descrive i campi della scheda: 

|    **Campo**          |  **Descrizione**               |
|  ---------------      |  ---------------               |
|  Riepilogo offerta        | Riepilogo della proposta di valore dell'offerta. Verrà visualizzato nella pagina di ricerca dell'offerta. Il formato è testo normale con un massimo di 100 caratteri. |
|  Descrizione dell'offerta    | Descrizione che verrà visualizzata nella pagina dei dettagli dell'app. Il formato è HTML semplice (inclusi i tag p, em, ul, li, ol e header) con un massimo di 1.300 caratteri.  |
|  |  | 


## <a name="listing-details-section"></a>Sezione Listing Details (Dettagli dell'inserzione)

In questa sezione si forniscono dettagli sull'inserzione nel marketplace.

![Sezione dettagli inserzione della scheda Storefront Details (Dettagli vetrina)](./media/storefront-tab-listing-details.png)

La tabella seguente descrive i campi della scheda: 

|    **Campo**          |  **Descrizione**               |
|  ---------------      |  ---------------               |
|  Settori           | Selezionare i settori (massimo due) che corrispondono meglio all'app. Se nessuno è applicabile, è possibile non selezionare opzioni.  |
|  Categorie           | Selezionare le categorie rilevanti per l'app. Scegliere da una a tre opzioni.  |
|  Tipo di app             | Selezionare il tipo di versione di valutazione abilitato per l'app in AppSource. **Free** (Gratuito) indica che l'app può essere usata senza alcun costo aggiuntivo; **Trial**  (Versione di valutazione)indica che i clienti possono provare l'app per un breve periodo senza alcun costo aggiuntivo e **Contact me** (Contattami) indica che i clienti possono richiedere una versione di valutazione dell'app in AppSource.  |
| Help link for your app (Collegamento alla Guida per l'app) | URL per la documentazione online o altre risorse di assistenza per l'app.  |
| Supported countries/regions (Paesi supportati) | Una finestra di dialogo popup consente di selezionare le aree geografiche in cui è disponibile l'app.  La distribuzione in tutto il mondo è l'impostazione predefinita. |
| Lingue supportate   | Selezionare le lingue supportate dall'app. Attualmente sono supportate solo alcune opzioni per la lingua.  Se l'app supporta lingue aggiuntive non incluse in questo elenco, continuare a pubblicare l'offerta e segnalare la situazione scrivendo all'indirizzo appsource@microsoft.com.  |
| Versione dell'app           | Numero di versione per l'app    |
| Data di rilascio dell'app      | Data di rilascio/pubblicazione dell'app |
| Prodotti interrelati con l'app | Prodotti o servizi con cui l'app interagisce. È possibile elencare fino a tre prodotti. Per elencare un prodotto, fare clic su **+New** (Nuovo) e immettere il nome di un prodotto nella casella di testo visualizzata.  |
| Search Keywords (Parole chiave di ricerca)       | Set di parole chiave associate all'app, con un massimo di tre parole chiave.  AppSource consente al cliente di eseguire la ricerca in base a parole chiave.  Scegliere parole generiche e comuni che gli utenti useranno probabilmente per cercare l'app.  |
|  Hide key (Chiave nascosta)             | Chiave segreta che viene combinata con l'URL dell'offerta per consentire di visualizzare un'anteprima dell'offerta prima che venga pubblicata.  Questa chiave *non* è una password.  Può contenere solo caratteri alfanumerici.  |
|  |  |


## <a name="marketing-artifacts-section"></a>Sezione Marketing Artifacts (Artefatti di marketing)

In questa sezione si fornisce il materiale di marketing: immagini del logo, video di istruzioni o promozionali, documentazione del prodotto e screenshot dell'interfaccia utente.

![Sezione artefatti di marketing della scheda Storefront Details (Dettagli vetrina)](./media/storefront-tab-market-artifacts.png)

La tabella seguente descrive i campi della scheda: 

|      Campo            |    DESCRIZIONE                 |
|  ---------------      |  ---------------               |
| Logo dell'offerta (formato PNG, 48x48) | Caricare un'immagine da visualizzare nella pagina di ricerca dell'app. Il formato è PNG e le dimensioni devono essere 48x48 pixel. |
| Logo dell'offerta (formato PNG 216x216) | Caricare un'immagine da visualizzare nella pagina dei dettagli dell'app. Il formato è PNG e le dimensioni devono essere 216x216 pixel. |
| Video                | Caricare un massimo di quattro video promozionali o di istruzioni per l'app.  Per ogni video, inserire nome del video, URL (solo YouTube o Vimeo) e anteprima associata in formato PNG e di dimensioni 1280x720 pixel.  | 
| Documenti             | Caricare un massimo di tre documenti promozionali o di istruzioni sul prodotto in formato PDF.  Per ogni documento, specificare un nome di documento.  |
| Screenshots (Schermate)           | Caricare un massimo di cinque immagini dell'interfaccia utente dell'app in formato PNG e di dimensioni 1280x720 pixel.  Questi screenshot che verranno visualizzati nella pagina dei dettagli per l'app di AppSource. |
|  |  |


## <a name="legal-section"></a>Sezione legale

In questa sezione si forniscono l'*informativa sulla privacy* e le *condizioni per l'utilizzo* dell'app.

![Sezione legale della scheda Storefront Details (Dettagli vetrina)](./media/storefront-tab-legal.png)

La tabella seguente descrive i campi della scheda: 

|      Campo            |    DESCRIZIONE                 |
|  ---------------      |  ---------------               |
| URL dell'Informativa sulla privacy    | URL dell'informativa sulla privacy online per l'app.  |
| Condizioni per l'utilizzo          | Condizioni per l'utilizzo dell'app in testo normale.  Per ottenere la versione di valutazione dell'app, i clienti di AppSource devono accettare tali condizioni. |
|  |  |


## <a name="customer-support-section"></a>Sezione Customer support (Assistenza clienti)

In questa sezione, si fornirà l'**URL del supporto** per l'app.


## <a name="lead-management-section"></a>Sezione Lead Management (Gestione clienti potenziali)

In questa sezione si specifica il meccanismo per gestire i clienti potenziali generati dall'inserzione dell'offerta.  In genere i clienti potenziali vengono archiviati in sistemi CRM (Customer Relationship Management).  Sono supportate le destinazioni seguenti: **None** (Nessuna), **Azure Table** (Tabella di Azure), **Dynamics CRM Online**, **HTTPS Endpoint** (Endpoint HTTPS), **Marketo** e **Salesforce** .  Per altre informazioni, vedere [Ottenere lead](../../cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads.md).


## <a name="next-steps"></a>Passaggi successivi

Nel passaggio successivo, si forniranno informazioni sul supporto tecnico e l'assistenza clienti nella [scheda Contacts (Contatti)](./cpp-contacts-tab.md). 
