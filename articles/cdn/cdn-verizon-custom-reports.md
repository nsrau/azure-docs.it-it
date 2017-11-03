---
title: Report personalizzati di Verizon | Microsoft Docs
description: "È possibile visualizzare i modelli di utilizzo per la rete CDN usando i report seguenti: Larghezza di banda, Dati trasferiti, Riscontri, Stati della cache, Percentuale riscontri cache, Dati trasferiti IPv4/IPv6."
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: v-deasim
ms.openlocfilehash: 8df9fd46fe3ce8d87e7ad5377a21a0bc7a458d2b
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2017
---
# <a name="custom-reports-from-verizon"></a>Report personalizzati di Verizon

[!INCLUDE[cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Usando i report personalizzati di Verizon tramite il portale di gestione per i profili Verizon, è possibile definire i tipi di dati da raccogliere per i report relativi ai CNAME perimetrali.


## <a name="accessing-verizon-custom-reports"></a>Accesso ai report personalizzati di Verizon
1. Dal pannello del profilo della rete CDN fare clic sul pulsante **Gestisci** .
   
    ![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    Si aprirà il portale di gestione della rete CDN.
2. Passare il puntatore sulla scheda **Analisi** e quindi sul riquadro a comparsa **Report personalizzati**. Fare clic su **Edge CNAMEs** (CNAME perimetrali).
   
    ![Portale di gestione della rete CDN, menu Custom reports (Report personalizzati)](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Report personalizzato Edge CNAMEs (CNAME perimetrali)
Il report personalizzato Edge CNAMEs (CNAME perimetrali) offre statistiche di riscontri e trasferimento dati per i CNAME perimetrali nei quali è stata abilitata la registrazione di report personalizzati. I CNAME perimetrali sono costituiti da nomi host di endpoint della rete CDN di Azure e da eventuali nomi host del dominio personalizzato associato. 

La registrazione dei dati per il report personalizzato inizia un'ora dopo aver abilitato la funzionalità di creazione di report personalizzati per il CNAME perimetrale. I dati possono essere visualizzati generando un report Edge CNAMEs (CNAME perimetrali) per una piattaforma specifica o per tutte le piattaforme. La copertura di questo report è limitata ai CNAME perimetrali per i quali sono stati raccolti dati per il report personalizzato durante il periodo di tempo specificato. Il report Edge CNAMEs (CNAME perimetrali) è costituito da un grafico e una tabella di dati per i primi 10 CNAME in base alla metrica definita nell'opzione Metrics (Metriche). 

Generare un report personalizzato definendo le opzioni di report seguenti:

- Metrics (Metriche): sono supportate le opzioni seguenti:

   - Hits (Riscontri): indica il numero totale di richieste indirizzate a un CNAME perimetrale in cui è abilitata la funzionalità di creazione di report personalizzati. Questa metrica non include il codice di stato restituito al client.

   - Data Transferred (Dati trasferiti): indica la quantità totale di dati trasferiti dai server perimetrali ai client HTTP (ad esempio i Web browser) per le richieste indirizzate a un CNAME perimetrale in cui è abilitata la funzionalità di creazione di report personalizzati. La quantità di dati trasferiti viene calcolata aggiungendo le intestazioni di risposta HTTP al corpo della risposta. La quantità di dati trasferiti per ogni asset è quindi maggiore delle dimensioni file effettive.

- Groupings (Raggruppamenti): determina il tipo di statistiche visualizzate sotto il grafico a barre. Sono supportate le opzioni seguenti:

   - HTTP Response Codes (Codici di risposta HTTP): organizza le statistiche in base al codice di risposta HTTP restituito dal client, ad esempio 200, 403 e così via. 

   - Cache Status: (Stato cache): organizza le statistiche in base allo stato della cache.


Per impostare le date per il report, è possibile selezionare un intervallo predefinito dall'elenco a discesa, ad esempio **Today** (Oggi) o **This Week** (Questa settimana), oppure selezionare **Custom** (Personalizzato) e immettere manualmente un intervallo di date facendo clic sulle icone del calendario. 

Dopo aver selezionato l'intervallo di date, fare clic su **Go** (Vai) per generare il report.

È possibile esportare i dati in formato Excel facendo clic sul simbolo di Excel a destra del pulsante **Go** (Vai).

![Report CNAME](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Campi del report personalizzato Edge CNAMEs (CNAME perimetrali)

| Campo                     | Descrizione   |
|---------------------------|---------------|
| 2xx                       | Indica il numero totale di richieste o dati trasferiti (MB) per il CNAME perimetrale che restituisce un codice di stato HTTP 2xx, ad esempio 200 OK. |
| 3xx                       | Indica il numero totale di richieste o dati trasferiti (MB) per il CNAME perimetrale che restituisce un codice di stato HTTP 3xx, ad esempio 302 - Trovato oppure 304 - Non modificato. |
| 4xx                       | Indica il numero totale di richieste o dati trasferiti (MB) per il CNAME perimetrale che restituisce un codice di stato HTTP 4xx, ad esempio 400 - Richiesta non valida, 403 - Accesso negato oppure 404 Non trovato. |
| 5xx                       | Indica il numero totale di richieste o dati trasferiti (MB) per il CNAME perimetrale che restituisce un codice di stato HTTP 5xx, ad esempio 500 - Errore interno del server oppure 502 - Gateway non valido. |
| Cache Hit % (Percentuale riscontri cache)               | Indica la percentuale di richieste inseribili nella cache fornite direttamente dalla cache al richiedente. |
| Riscontri cache                | Indica il numero totale di richieste o dati trasferiti (MB) per il CNAME perimetrale che restituisce un riscontro nella cache, ad esempio TCP_EXPIRED_HIT, TCP_HIT o TCP_PARTIAL_HIT. Un riscontro nella cache si verifica quando viene trovata una versione del contenuto richiesto memorizzata nella cache. |
| Data Transferred (MB) (Dati trasferiti - MB)     | Indica la quantità totale di dati trasferiti (MB) dai server perimetrali ai client HTTP (Web browser) per il CNAME perimetrale. La quantità di dati trasferiti viene calcolata aggiungendo le intestazioni di risposta HTTP al corpo della risposta. La quantità di dati trasferiti per ogni asset è quindi maggiore delle dimensioni file effettive. |
| Descrizione               | Identifica un CNAME perimetrale in base al nome host |
| Riscontri                      | Indica il numero totale di richieste inviate al CNAME perimetrale |
| Misses (Mancati riscontri)                    | Indica il numero totale di richieste o dati trasferiti (MB) per il CNAME perimetrale che restituisce un mancato riscontro nella cache, ad esempio TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS o TCP_MISS. Un mancato riscontro nella cache si verifica quando il contenuto richiesto non è stato memorizzato nella cache del server perimetrale che ha soddisfatto la richiesta. | 
| No Cache (Nessuna cache)                  | Indica il numero totale di richieste o dati trasferiti (MB) per il CNAME perimetrale che restituisce un codice di stato cache CONFIG_NOCACHE.  |
| Altre                     | Indica il numero totale di richieste o dati trasferiti (MB) per il CNAME perimetrale che restituisce un codice di stato HTTP che non rientra nell'intervallo 2xx - 5xx. |
| Piattaforma                  | Indica la piattaforma che gestisce il traffico del CNAME perimetrale. |
| Unassigned (Non assegnato)               | Indica il numero totale di richieste o dati trasferiti (MB) per il CNAME perimetrale per il quale non è stato registrato il codice di stato cache o il codice di stato HTTP.  |
| Uncacheable (Non inseribile nella cache)               | Indica il numero totale di richieste o dati trasferiti (MB) per il CNAME perimetrale che restituisce un codice di stato cache UNCACHEABLE.  |


## <a name="considerations"></a>Considerazioni
I report possono essere generati solo negli ultimi 18 mesi.

