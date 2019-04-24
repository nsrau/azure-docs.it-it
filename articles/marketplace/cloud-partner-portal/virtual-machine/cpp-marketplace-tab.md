---
title: Scheda Marketplace della macchina virtuale nel portale Cloud Partner per Azure | Microsoft Docs
description: Descrive la scheda Marketplace usata nella creazione di un'offerta di macchina virtuale in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: b1b62c68ef4e18f4d4d36a78078ad7431717b754
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60332756"
---
# <a name="virtual-machine-marketplace-tab"></a>Scheda Marketplace per una macchina virtuale

La scheda **Marketplace** della pagina **Nuova offerta** consente di offrire ai clienti potenziali informazioni e contratti legali, di vendita e marketing e di gestire i clienti potenziali generati dal marketplace. Questa forma estesa è suddivisa in quattro sezioni: **Cenni preliminari sulla**, **gli elementi di Marketing**, **condurre una gestione**, e **legali**. 

## <a name="overview-section"></a>Sezione Panoramica
In questa sezione immettere le informazioni generali sull'offerta di Azure Marketplace.  Un asterisco (*) alla fine del nome del campo indica che il campo è obbligatorio.

![Sezione Overview (Panoramica) della scheda Marketplace nel modulo Nuova offerta per le macchine virtuali](./media/publishvm_008.png)

La tabella seguente descrive lo scopo e il contenuto di questi campi.

|  **Campo**                |     **Descrizione**                                                          |
|  ---------                |     ---------------                                                          |
| **Titolo**                 | Titolo dell'offerta, spesso corrispondente alla forma estesa e formale del nome. Questo titolo verrà visualizzato in una posizione di estremo rilievo nel marketplace.  La lunghezza massima consentita è di 50 caratteri. |
| **Summary**               | Descrizione breve dello scopo o della funzione della soluzione.  La lunghezza massima consentita è di 100 caratteri. |
| **Long Summary** (Riepilogo lungo)          | Scopo o funzione della soluzione.  La lunghezza massima consentita è di 256 caratteri. |
| **Descrizione**           | Descrizione della soluzione.  La lunghezza massima consentita è di 3000 caratteri. Supporta la formattazione HTML semplice. |
| **Canale rivenditore CSP di Microsoft** | Cloud Solution Provider (CSP) partner canale acconsentire esplicitamente a questo punto è disponibile.  Vedi [Cloud Solution Provider](../../cloud-solution-providers.md) per altre informazioni sul marketing dell'offerta tramite Microsoft CSP partner canali. |
| **Marketing Identifier** (Identificatore di marketing)  | URL univoco da associare a questa offerta. Include in genere il nome dell'organizzazione e della soluzione. La lunghezza massima consentita è di 50 caratteri.  Ad esempio:  <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Preview Subscription IDs** (ID sottoscrizione di anteprima) | Aggiungere da uno a 100 identificatori di sottoscrizione di visualizzatori anteprima. Queste sottoscrizioni consentite avranno accesso all'offerta dopo la pubblicazione, prima che venga attivata. |
| **Collegamenti utili**          | Aggiungere gli URL per la documentazione, le note sulla versione, le domande frequenti e così via. |
| **Suggested Categories (Max 5)** (Categorie suggerite - Max 5) | Selezione multipla di categorie commerciali e tecniche a cui associare l'offerta in modo ottimale.  Il numero massimo consentito è cinque.  |
|  |  |


## <a name="marketing-artifacts-section"></a>Sezione Marketing Artifacts (Artefatti di marketing)

Questa seconda sezione è suddivisa in tre sottosezioni: **Logo**, **Screenshot**, e **video**. I logo sono gli unici artefatti di marketing obbligatori, ma tutti sono vivamente consigliati per catturare meglio l'attenzione dei clienti.

![Sezione Marketing Artifacts (Artefatti di marketing) della scheda Marketplace nel modulo Nuova offerta per le macchine virtuali](./media/publishvm_009.png)

|  **Campo**                |     **Descrizione**                                                          |
|  ---------                |     ---------------                                                          |
| *Logos* (Logo)  |  |
| **Small**                 | Bitmap con estensione ico, 40x40 pixel                                                      |
| **Medium**                | Bitmap con estensione ico, 90x90 pixel                                                      |
| **Large**                 | Bitmap con estensione ico, 115x115 pixel                                                   |
| **Wide** (Largo)                  | Bitmap con estensione ico, 255x115 pixel                                                    |
| **Hero** (Banner)                  | Bitmap, 815x290.  Facoltativo. Una volta caricata, tuttavia, l'icona del banner non può essere eliminata. |
| *Screenshot*  | Facoltativi, ma non più di cinque per SKU. |
| **Nome**                  | Nome o titolo <!-- TODO - max char length? none specified in UI -->                               |
| **Immagine**                 | Acquisizione di schermata, 533x324 pixel                                         |
| *Video*  |  |
| **Nome**                  | Nome o titolo  <!-- TODO - max char length? -->                              |
| **Collegamento**                  | URL del video, ospitato in YouTube o Vimeo                                        |
| **Anteprima**             | Bitmap, 533x324                                                               |
|  |  |


### <a name="logo-guidelines"></a>Linee guida per il logo

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Ogni logo caricato nel portale Cloud Partner deve rispettare le linee guida seguenti:

*  La progettazione di Azure ha una tavolozza dei colori semplice. Nel logo usare un numero ridotto di colori primari e secondari.
*  I colori del tema del portale di Azure sono il bianco e il nero. Evitare pertanto di usare questi colori per lo sfondo dei logo. Usare un colore che faccia risaltare i logo nel portale di Azure. Si consiglia di usare colori primari semplici. Se si usa lo sfondo trasparente, verificare che i logo e il testo non siano bianchi, neri o blu.
*  Non usare uno sfondo sfumato per il logo.
*  Non inserire testo, neppure il nome del marchio o della società, nel logo. L'aspetto del logo deve essere semplice e senza sfumature.
*  Non estendere il logo.

#### <a name="hero-logo"></a>Logo alto

Il logo Hero (Banner) è facoltativo. Una volta caricata, tuttavia, l'icona del banner non può essere eliminata.  L'icona del logo Hero (Banner) deve rispettare le linee guida seguenti:

*  Lo sfondo nero, bianco o trasparente non è consentito per le icone banner.
*  Evitare di usare un colore chiaro per lo sfondo del logo del banner.  Il nome visualizzato dell'editore, il titolo del piano e il riepilogo lungo dell'offerta sono visualizzati con caratteri bianchi e devono risaltare sullo sfondo.
*  Evitare di usare testo mentre si progetta il logo del banner.  Il nome dell'editore, il titolo del piano, il riepilogo lungo dell'offerta e un pulsante di creazione vengono incorporati a livello di codice all'interno dell'icona del banner quando l'offerta viene presentata. 
* Includere un rettangolo vuoto a destra dell'icona del banner, con dimensioni 415x100 pixel e uno scostamento di 370 pixel da sinistra.  

L'icona del banner di esempio riportata di seguito è per il servizio Azure Container.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Icona del banner di esempio per il servizio Azure Container](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Esempio di informazioni di marketing 

L'immagine seguente illustra il modo in cui vengono visualizzate le informazioni di marketing nella pagina del prodotto principale di Microsoft Windows Server.

![Pagina di prodotto di esempio per Microsoft Windows Server](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Sezione Lead Management (Gestione clienti potenziali)
<!-- this all should be referenced in a common location for lead management, not in this file. nothing unique for a vm specifically. -->

La terza sezione consente di raccogliere clienti potenziali generati dalle offerte in Azure Marketplace. Per queste informazioni, questa sezione offre le opzioni di archiviazione seguenti da un elenco a discesa.

* **Nessuno**: impostazione predefinita, non vengono raccolte informazioni sui clienti potenziali.
* Tabella di Azure: le informazioni vengono scritte nella tabella di Azure specificata da una stringa di connessione.
* Dynamics CRM Online: le informazioni vengono scritte nell'istanza di [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) specificata da un URL e da credenziali di autenticazione.
* HTTPS Endpoint (Endpoint HTTPS): le informazioni vengono scritte nell'endpoint HTTPS specificato come payload JSON.
* Marketo: le informazioni vengono scritte nell'istanza [Marketo](https://www.marketo.com/) specificata da ID server, ID Munchkin e ID modulo.
* Salesforce: le informazioni vengono scritte in un database [Salesforce](https://www.salesforce.com/) specificato da un identificatore di oggetto.

Dopo aver pubblicato l'offerta, verrà convalidata la connessione al cliente potenziale e verrà automaticamente inviato un cliente potenziale di test alla destinazione configurata. Le informazioni sul cliente potenziale devono essere gestite in modo continuo e queste impostazioni devono essere aggiornate immediatamente quando si apportano modifiche all'architettura di gestione dei clienti.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Sezione legale

Questa sezione consente di fornire i due documenti legali necessari per ogni offerta: informativa sulla privacy e condizioni per l'utilizzo.

|  **Campo**                |     **Descrizione**                                                          |
|  ---------                |     ---------------                                                          |
| **URL dell'informativa sulla privacy**    | URL dell'informativa sulla privacy pubblicata                                            |
| **Condizioni d'uso**          | Norme in testo normale o HTML semplice.  <!-- TODO - max char length? -->       |
|  |  |

<br/>

Nella scheda successiva, [Support](./cpp-support-tab.md) (Supporto) è possibile specificare le risorse tecniche e di supporto utente della propria offerta.

