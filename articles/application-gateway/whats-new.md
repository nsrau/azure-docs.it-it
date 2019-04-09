---
title: Novità del gateway applicazione di Azure
description: Informazioni sulle novità del gateway applicazione di Azure, tra cui le note sulla versione più aggiornate, i problemi noti, le correzioni di bug, le funzionalità deprecate e le modifiche imminenti.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/1/2019
ms.author: victorh
ms.openlocfilehash: f686c8ac53db2d128cf5bb20f252c547348e5ac7
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863101"
---
# <a name="whats-new-in-azure-application-gateway"></a>Novità del gateway applicazione di Azure

Il gateway applicazione di Azure viene aggiornato regolarmente. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate

## <a name="new-features"></a>Nuove funzionalità

|Funzionalità  |DESCRIZIONE  |Data di aggiunta  |
|---------|---------|---------|
|CRUD/riscritture intestazioni     |È ora possibile riscrivere le intestazioni HTTP. Per istruzioni dettagliate, vedere [Esercitazione: Creare un gateway applicazione e riscrivere intestazioni HTTP](tutorial-http-header-rewrite-powershell.md).|Dicembre 2018|
|Configurazione di WAF ed elenco di esclusione     |Sono state aggiunte più opzioni per configurare la soluzione WAF e ridurre i falsi positivi. Per altre informazioni, vedere [Limiti delle dimensioni di richiesta di Web application firewall ed elenchi di esclusione](application-gateway-waf-configuration.md).|Dicembre 2018|
|Ridimensionamento automatico, ridondanza delle zone, anteprima del supporto di indirizzi IP virtuali     |Con la versione 2 dello SKU, sono stati introdotti diversi miglioramenti, come il ridimensionamento automatico, le prestazioni più elevate e altro ancora. Per altre informazioni, vedere [Cos'è il gateway applicazione di Azure?](overview.md#autoscaling-public-preview)|Settembre 2018|
|Svuotamento delle connessioni     |Lo svuotamento delle connessioni consente di rimuovere correttamente i membri dai pool di back-end. Per altre informazioni, vedere [Svuotamento delle connessioni](overview.md#connection-draining).|Settembre 2018|
|Pagine di errore personalizzate     |Con le pagine di errore personalizzate, è possibile creare una pagina di errore nello stesso formato delle altre parti dei siti Web. Per abilitare questa funzionalità, vedere[Creare pagine di errore personalizzate del gateway applicazione](custom-error.md).|Settembre 2018|
|Miglioramenti delle metriche     |Con le metriche migliorate, è possibile ottenere un quadro più completo dello stato del gateway applicazione. Per abilitare le metriche nel gateway applicazione, vedere [Integrità back-end, log di diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md).|Giugno 2018|

## <a name="known-issues"></a>Problemi noti

- [Problemi noti della versione 2 dello SKU](application-gateway-autoscaling-zone-redundant.md#known-issues-and-limitations)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul gateway applicazione di Azure, vedere [Cos'è il gateway applicazione di Azure?](overview.md)