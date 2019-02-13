---
title: Dettagli vetrina per un'offerta di app Power BI - Azure Marketplace | Microsoft Docs
description: Configurare i campi dei dettagli vetrina per un'offerta di app Power BI per Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
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
ms.date: 01/30/2019
ms.author: pbutlerm
ms.openlocfilehash: ffc0d8e9e41cf4d202b4040ce3f09e7180101c8b
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665842"
---
# <a name="power-bi-apps-storefront-details-tab"></a>Scheda Storefront Details (Dettagli vetrina) delle app Power BI

Usare la scheda **Storefront Details** (Dettagli vetrina) della finestra **New Offer** (Nuova offerta) per offrire ai clienti potenziali informazioni legali, di vendita e di marketing. Questa scheda consente di specificare anche come gestire i clienti potenziali generati dal marketplace. Questo lungo modulo è suddiviso in sei sezioni: **Offer Details** (Dettagli dell'offerta), **Listing Details** (Dettagli dell'inserzione), **Marketing Artifacts** (Artefatti di marketing), **Legal** (Note legali), **Customer Support** (Assistenza clienti) e **Lead Management** (Gestione clienti potenziali).  Un asterisco (*) accodato all'etichetta del campo indica che si tratta di un campo obbligatorio.


## <a name="offer-details-section"></a>Sezione Offer Details (Dettagli dell'offerta)

In questa sezione immettere le informazioni generali sull'offerta di AppSource.

![Sezione dettagli offerta della scheda Storefront Details (Dettagli vetrina)](./media/offer-details-section.png)

La tabella seguente descrive il nome e lo scopo di questi campi.

|   Campo               |   DESCRIZIONE                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------|
| **Offer Summary** (Riepilogo offerta)     | Breve scopo dell'app. La lunghezza massima consentita è di 100 caratteri.                             |
| **Offer Description**(Descrizione dell'offerta) | Descrizione dell'app. La lunghezza massima consentita è di 3000 caratteri. Supporta la formattazione HTML semplice. |
|                       |                                                                                         |


## <a name="listing-details-section"></a>Sezione Listing Details (Dettagli dell'inserzione)

Questa seconda sezione fornisce ulteriore contesto per l'app: in quali settori viene in genere usata, qual è la migliore categoria applicabile, i prodotti compatibili e i termini di ricerca associati.

![Sezione dettagli inserzione della scheda Storefront Details (Dettagli vetrina)](./media/listing-details-section.png)

 La tabella seguente descrive il nome e lo scopo di questi campi.
 
|   Campo                                  |   DESCRIZIONE                                                        |
| --------------                           | ---------------------                                                |
| **Settori**                           | Selezionare il settore che corrisponde meglio all'app. Se l'app rientra in più settori, è possibile lasciare vuoto il campo.      |
| **Categories** (Categorie)                           | Selezionare le categorie rilevanti per l'app. Selezionare al massimo tre categorie.     |
| **Help link for your app** (Collegamento alla Guida per l'app)               | URL di una pagina con la Guida dell'app           |
| **Products your app works with (Max 3)** (Prodotti interrelati con l'app - al massimo 3) | Elenca i prodotti specifici con cui l'app è compatibile. È possibile elencare fino a 3 prodotti. Per elencare un prodotto, fare clic sul segno più (accanto a Nuovo). Viene creato un nuovo campo di testo aperto in cui è possibile immettere il nome di un prodotto interrelato con l'app.      |
| **Search keywords (Max 3)** (Parole chiave di ricerca - al massimo 3)              | AppSource consente al cliente di eseguire la ricerca in base a parole chiave. È possibile immettere il set di parole chiave con cui l'applicazione verrà visualizzata ai clienti. Ad esempio se l'applicazione è un'"app di invio e-mail personale" alcune parole chiave potrebbero essere e-mail, posta elettronica, app di posta elettronica. Scegliere parole che gli utenti useranno probabilmente per cercare l'app nella casella di ricerca di AppSource. |
|  |  |


## <a name="marketing-artifacts-section"></a>Sezione Marketing Artifacts (Artefatti di marketing)

Questa terza sezione consente di caricare materiale di marketing e informazioni personalizzate distintive dell'azienda.  È suddivisa in quattro sottosezioni: **Logos** (Logo), **Videos** (Video), **Documents** (Documenti) e **Screenshots** (Schermate). Logo e schermate sono gli unici artefatti di marketing obbligatori, ma tutti sono vivamente consigliati per catturare meglio l'attenzione dei clienti.

![Sezione artefatti di marketing della scheda Storefront Details (Dettagli vetrina)](./media/marketing-artifacts-section.png)

 
|    Campo                             |    DESCRIZIONE                                                    |
|   -----------                        |    -------------                                                  |
| *Logos* (Logo)                              |                                                                   |
| **Offer logo (.png format, 48x48)** (Logo dell'offerta - formato PNG, 48x48)   | Visualizzato in AppSource nella panoramica dell'app o nei risultati dell'app dopo il completamento di una ricerca. È supportato solo il formato PNG, con una risoluzione di 48px\*48px.  |
| **Offer logo (png format, 216x216)** (Logo dell'offerta - formato PNG 216x216) | Visualizzato in AppSource nella pagina dei dettagli dell'app.  È supportato solo il formato PNG, con una risoluzione di 216px\*216px.  |
| *Video*                             |                                                                   |
| **Nome**                             | Nome o titolo dell'app                                          |
| **URL**                              | URL del video ospitato in YouTube o Vimeo                              |
| **Anteprima**                        | Immagine di anteprima dell'app.  È supportato solo il formato PNG, con una risoluzione di 1280px\*720px.   |
| *Documents* (Documenti)                          | Facoltativo, ma è possibile caricare un massimo di tre documenti. I documenti caricati qui verranno visualizzati in AppSource sotto "Learn more" (Altre informazioni).  |
| **Nome**                             | Nome o titolo del documento di supporto                              |
| **File**                             | Il documento caricato deve essere in formato PDF                             |
| *Screenshot*                        | Facoltativo, ma non più di cinque screenshot.                        |
| **Nome**                             | Nome o titolo dello screenshot                                       |
| **Immagine**                            | Caricare l'acquisizione di schermata in formato PNG con una risoluzione di 1280px\*720px  | 
|   |   |


### <a name="logo-guidelines"></a>Linee guida per il logo

Ogni logo caricato nel [portale Cloud Partner](https://cloudpartner.azure.com) deve rispettare le linee guida seguenti:

- Non usare uno sfondo sfumato per il logo.
- Non inserire testo nel logo, neanche il nome del marchio o della società. L'aspetto del logo deve essere semplice e senza sfumature.
- Non estendere il logo.


## <a name="legal-section"></a>Sezione legale

La quarta sezione consente di fornire i due documenti legali necessari per ogni offerta: informativa sulla privacy e condizioni per l'utilizzo.

![Sezione legale della scheda Storefront Details (Dettagli vetrina)](./media/legal-section.png)

|   Campo                |   DESCRIZIONE                           |
|------------------------|--------------------------------------   |
| **URL dell'informativa sulla privacy** | URL dell'informativa sulla privacy pubblicata       |
| **Condizioni d'uso**       | Norme in testo normale o HTML semplice     |
|  |  |


## <a name="customer-support-section"></a>Sezione Customer support (Assistenza clienti)

Fornire l'**URL del supporto** per la pagina dell'assistenza clienti online.  È consigliabile che la pagina dell'assistenza online offra ai clienti più opzioni di contatto, ad esempio telefono, posta elettronica e chat in tempo reale. 


## <a name="lead-management-section"></a>Sezione Lead Management (Gestione clienti potenziali)

L'ultima sezione consente di raccogliere i clienti potenziali generati dalle offerte di AppSource. Per queste informazioni, questa sezione offre le opzioni di archiviazione seguenti da un elenco a discesa.

|    Campo               |   Clienti potenziali destinatari                               |
|------------------------|--------------------------------------            |
|  **Nessuno**              | I clienti potenziali non vengono raccolti (impostazione predefinita).  |
| **Azure Blob (deprecated)** (BLOB di Azure - deprecata) | Un [BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview), specificato da un nome di contenitore e una stringa di connessione.  Questa scelta è deprecata. Usare invece una **tabella di Azure**.  |
| **Tabella di Azure**        | Una [tabella di Azure](https://docs.microsoft.com/azure/cosmos-db/table-storage-overview) specificata da una stringa di connessione  |
| **Dynamics CRM Online** | Un'istanza di [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) specificata da un URL e da credenziali di autenticazione |
| **Endpoint HTTPS**     | L'endpoint HTTPS specificato come payload JSON   |
| **Marketo**            | Un'istanza [Marketo](https://www.marketo.com/) specificata da ID server, ID Munchkin e ID modulo   |
| **Salesforce**         | Un database [Salesforce](https://www.salesforce.com/) specificato da un identificatore di oggetto |
|  |  |

Dopo aver pubblicato l'offerta, verrà convalidata la connessione al cliente potenziale e verrà automaticamente inviato un cliente potenziale di test alla destinazione specificata. Le informazioni sul cliente potenziale devono essere gestite in modo continuo e queste impostazioni devono essere aggiornate immediatamente in modo da riflettere l'architettura di gestione dei clienti corrente.


## <a name="next-steps"></a>Passaggi successivi

Nella scheda successiva, [Contacts](./cpp-contacts-tab.md) (Contatti) è possibile specificare le risorse tecniche e di supporto utente della propria offerta.
