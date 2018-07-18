---
title: Migrazione di un profilo di rete CDN di Azure da Standard Verizon a Premium Verizon | Microsoft Docs
description: Informazioni sui dettagli della migrazione di un profilo da Standard Verizon a Premium Verizon.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: 074dbb094e7ae2cd1f1719016928bd7348da3949
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2018
ms.locfileid: "36958056"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migrare un profilo di rete CDN di Azure da Standard Verizon a Premium Verizon

Quando si crea un profilo di rete CDN (Content Delivery Network) di Azure per gestire gli endpoint, le rete CDN di Azure offre quattro diversi prodotti tra cui è possibile scegliere. Per informazioni sui diversi prodotti e sulle funzionalità disponibili, vedere [Confronto tra funzionalità dei prodotti della rete CDN di Azure](cdn-features.md).

Se è stato creato un profilo di **rete CDN di Azure Standard da Verizon** e lo si sta usando per gestire gli endpoint di rete CDN, è possibile aggiornarlo a un profilo di **rete CDN di Azure Premium da Verizon**. Durante l'aggiornamento gli endpoint di rete CDN e i dati saranno mantenuti. 

> [!IMPORTANT]
> Dopo che è stato eseguito l'aggiornamento a un profilo di **rete CDN di Azure Premium da Verizon**, è possibile in un secondo momento riconvertirlo in un profilo di **rete CDN di Azure Standard da Verizon**.
> 

Per aggiornare un profilo di **rete CDN di Azure Standard da Verizon**, contattare il [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Confronto dei profili
I profili di **rete CDN di Azure Premium da Verizon** presentano le differenze chiave seguenti rispetto ai profili di **rete CDN Standard da Verizon**:
- Per determinate funzionalità della Rete CDN di Azure, quali la [compressione](cdn-improve-performance.md), le [regole di caching](cdn-caching-rules.md) e il [filtro della replica geografica](cdn-restrict-access-by-country.md) non è possibile usare l'interfaccia di rete CDN di Azure; è necessario usare il portale di Verizon attraverso il pulsante **Gestisci**.
- API: diversamente da Standard Verizon, non è possibile usare l'API per controllare le funzionalità a cui si accede dal portale di Premium Verizon. Tuttavia è possibile usare l'API per controllare funzionalità comuni quali la creazione/eliminazione di un endpoint, la pulizia/caricamento di asset memorizzati nella cache e l'abilitazione/disabilitazione di un dominio personalizzato.
- Prezzi: Premium Verizon ha una diversa struttura di prezzi per il trasferimento di dati rispetto a Standard Verizon. Per altre informazioni, vedere [Prezzi della rete per la distribuzione di contenuti](https://azure.microsoft.com/pricing/details/cdn/).

I profili di **rete CDN di Azure Premium da Verizon** presentano le funzionalità aggiuntive seguenti:
- [Autenticazione dei token](cdn-token-auth.md): consente agli utenti di ottenere e usare un token per rendere sicure le risorse.
- [Motore regole](cdn-rules-engine.md): consente di personalizzare la modalità di gestione delle richieste HTTP.
- Strumenti di analisi avanzata:
   - [Analisi dettagliata HTTP](cdn-advanced-http-reports.md)
   - [Analisi delle prestazioni dell'analisi di flusso in dispositivi perimetrali](cdn-edge-performance.md)
   - [Analisi in tempo reale](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul motore regole, vedere [Informazioni di riferimento sul motore regole della rete CDN di Azure](cdn-rules-engine-reference.md).

