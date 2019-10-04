---
title: Web Analytics
description: .
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: a6b2a7981452b47a3992b6c0efee286878e143fb
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962892"
---
<a name="web-analytics"></a>Web Analytics
=============

Questo articolo contiene le istruzioni su come apprendere e usare Web Analytics per promuovere il proprio business. Attualmente questa scheda Informazioni dettagliate è disponibile per qualsiasi offerta AppSource.

Ora che è stata creata e pubblicata l'offerta, il passaggio successivo è tenere traccia e misurarne il\' successo. Con **Web Analytics** è ora possibile esaminare con precisione il successo di ogni offerta nel marketplace. Per iniziare, passare alla pagina Informazioni dettagliate a sinistra nel portale Cloud Partner per visualizzare la nuova scheda di analisi.

![Pagina di Web Analytics](./media/si-getting-started/WebAnalytics1.png)

Verrà visualizzato un dashboard avanzato per l'ID editore compilato con Microsoft Power BI ed è possibile esaminare i dati di ogni offerta, aggiornati quotidianamente.

<a name="microsoft-campaigns"></a>**Campagne Microsoft**
-----------------------

Per promuovere le offerte e tenere traccia della relativa crescita, è stato abilitato l'utilizzo delle **campagne Microsoft** nel portale Cloud Partner. Le campagne sono una nuova funzione supportata per il marketplace che permette di monitorare i diversi canali che inviano i clienti alla pagina dei dettagli dell'app specifica.

### <a name="how-to-make-a-campaign"></a>**Come creare una campagna**

Il modo più semplice per descrivere le campagne è che si aggiunge una parola/un termine personalizzato all'URL che accede alla pagina dei dettagli dell'app personale nel marketplace. Google, Bing, LinkedIn e molti altri siti invitano a creare un annuncio, un collegamento dal loro sito al sito desiderato.

In generale, questa azione è volta a indirizzare nuovi clienti verso il proprio prodotto ed è essenziale misurare il successo ottenuto da ogni canale in questa ottica. Qui entrano in gioco le campagne.

Per generare la propria campagna, sono disponibili due modi.

1. Aggiungere all'URL il parametro di query **mktcmpid** che descrive qual è la campagna e da quale pagina/evento provengono questi clienti.

Ad esempio, è possibile usare: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign>

1. (Avanzate): Usare uno degli ID di campagna generici supportati nell'URL. Per assicurare flessibilità con i tag di riferimento aggiuntivi che è necessario usare, viene supportata la convenzione per riconoscere automaticamente questi tag aggiuntivi:
    
    1. **utm\_campaign**
    2. **utm\_source**
    3. **ref**
    4. **src**

Ad esempio, è possibile usare: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign>

È possibile scegliere una combinazione di più di questi ID campagna per identificare ulteriormente più origini del traffico per la campagna, come la risorsa da cui proviene il cliente (e-mail, blog, social media e così via).

Ad esempio:

1. Referrer newsletter: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter>
2. Referrer LinkedIn: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn>

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Come garantire che le campagne passino attraverso tutte le pagine desiderate**

Uno scenario possibile è quello in cui le campagne usano una pagina intermedia a cui viene indirizzato il traffico, da cui i clienti vengono inviati al marketplace. È importante passare attraverso gli ID campagna iniziali fino all'URL finale di invio al marketplace.

Di seguito è fornito un esempio:

1. Un dipendente di marketing acquista annunci da Google per indirizzare il traffico verso la pagina di destinazione dell'azienda <https://contoso.com>. Questa pagina di destinazione contiene un collegamento per \"provare il prodotto\" che rimanda a <https://appsource.com>.
2. Un utente fa clic sull'annuncio e accede alla pagina di destinazione dell'azienda.
    1.  URL del referrer = google.com
    2.  URL della pagina di destinazione = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
3. L'utente fa clic sul collegamento per \"provare il prodotto\" e accede ad AppSource.
    1. URL del referrer = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
    2. URL della pagina di destinazione (**assicurarsi che questo URL includa utm\_campaign e utm\_source aggiunto a questo URL**) = [https://appsource.microsoft.com/product/dynamics-365/contoso.offername? **utm\_ campaign=MyCampaignAdName&utm\_ source=MySourceAdName**](https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName)

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Come valutare il successo di una campagna
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Visite alle pagine per campagna**

![Web Analytics 2](./media/si-getting-started/WebAnalytics2.png)

Rappresenta la ripartizione di ciascuna visita giornaliera alle pagine in base alla campagna di provenienza.

### <a name="conversion-rate-by-campaign"></a>**Tasso di conversione per campagna**

![Web Analytics 3](./media/si-getting-started/WebAnalytics3.png)

Così come viene indicato il tasso di conversione dell'offerta completa, in questo grafico è possibile esaminare i dati in maggiore dettaglio sul successo delle diverse campagne. Questo grafico consente di identificare la provenienza delle campagne con un tasso di conversione più elevato.

### <a name="distribution-by-campaign"></a>**Distribuzione per campagna**

![Web Analytics 4](./media/si-getting-started/WebAnalytics4.png)

Analogamente al modo in cui vengono esaminati i domini dei clienti, questo grafico consente di esaminare la distribuzione dei dati per ogni campagna con cui gli utenti accedono al marketplace. \_NoCampaign indica che il cliente non disponeva di un ID campagna nell'URL quando ha eseguito l'accesso al marketplace.

<a name="next-steps"></a>**Passaggi successivi**
--------------

Ora che si ha la possibilità di monitorare il successo delle proprie offerte, è consigliabile creare campagne personalizzate.

Condividere eventuali domande o richieste di funzionalità tramite Commenti e suggerimenti nell'angolo in alto a destra.

![Commenti e suggerimenti nel portale Cloud Partner](./media/si-getting-started/WebAnalytics5.png)
