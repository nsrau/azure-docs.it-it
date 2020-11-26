---
title: Record DNS del Data Center di Azure usati da automazione di Azure | Microsoft Docs
description: Questo articolo fornisce i record DNS richiesti dalle funzionalità di automazione di Azure quando si limita la comunicazione a una specifica area di Azure che ospita l'account di automazione.
services: automation
ms.subservice: process-automation
ms.date: 11/25/2020
ms.topic: conceptual
ms.openlocfilehash: b4b8f48afc75c0a96937575bdad5bb884d0cb4d8
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183720"
---
# <a name="dns-records-for-azure-regions-used-by-azure-automation"></a>Record DNS per le aree di Azure usate da automazione di Azure

Il servizio [automazione di Azure](../automation-intro.md) usa una serie di record DNS per le funzionalità di connessione al servizio. Se è disponibile un account di Automazione definito per un'area specifica, è possibile limitare la comunicazione a tale data center regionale. Potrebbe essere necessario essere a conoscenza di questi record per consentire il funzionamento delle funzionalità di automazione seguenti quando è ospitato dietro un firewall:

* Ruolo di lavoro ibrido per runbook
* Configurazione dello stato
* Webhook

>[!NOTE]
>La registrazione del ruolo di lavoro ibrido per Runbook di Linux avrà esito negativo con i nuovi record a meno che non si tratti di una versione 1.6.10.2 È necessario eseguire l'aggiornamento a una versione più recente dell' [agente di log Analytics per Linux](../../azure-monitor/platform/agent-linux.md) per consentire al computer di ricevere una versione aggiornata del ruolo di lavoro e di usare questi nuovi record. I computer esistenti continueranno a funzionare senza problemi.  

## <a name="dns-records-per-region"></a>Record DNS per area

La tabella seguente indica il record DNS per ogni area.

>[!NOTE]
>Mentre l'elenco di record DNS di automazione fornito qui è stato ritirato, rimane comunque funzionante per consentire la migrazione dei nuovi record elencati in [supporto del collegamento privato](#support-for-private-link) ed evitare errori nei processi di automazione.

| **Area** | **Record DNS** |
| --- | --- |
| Australia centrale |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Australia orientale |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Australia sud-orientale |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Canada centrale |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| India centrale |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Stati Uniti orientali 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Giappone orientale |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Europa settentrionale |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Stati Uniti centro-meridionali |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Asia sud-orientale |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Regno Unito meridionale | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| Stati Uniti centro-occidentali | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Europa occidentale |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Stati Uniti occidentali 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

### <a name="support-for-private-link"></a>Supporto per il collegamento privato

Per supportare il [collegamento privato](../../private-link/private-link-overview.md) in automazione di Azure, i record DNS per ogni data center supportato sono stati aggiornati. Invece degli URL specifici dell'area, gli URL sono specifici dell'account di automazione.

| **Area** | **Record DNS** |
| --- | --- |
| Stati Uniti centro-occidentali |`https://<accountId>.webhook.wcus.azure-automation.net`<br>`https://<accountId>.agentsvc.wcus.azure-automation.net`<br>`https://<accountId>.jrds.wcus.azure-automation.net` |
| Stati Uniti occidentali |`https://<accountId>.webhook.wus.azure-automation.net`<br>`https://<accountId>.agentsvc.wus.azure-automation.net`<br>`https://<accountId>.jrds.wus.azure-automation.net` |
| Stati Uniti occidentali 2 |`https://<accountId>.webhook.wus2.azure-automation.net`<br>`https://<accountId>.agentsvc.wus2.azure-automation.net`<br>`https://<accountId>.jrds.wus2.azure-automation.net` |
| Stati Uniti centrali |`https://<accountId>.webhook.cus.azure-automation.net`<br>`https://<accountId>.agentsvc.cus.azure-automation.net`<br>`https://<accountId>.jrds.cus.azure-automation.net` |
| Stati Uniti centro-meridionali |`https://<accountId>.webhook.scus.azure-automation.net`<br>`https://<accountId>.agentsvc.scus.azure-automation.net`<br>`https://<accountId>.jrds.scus.azure-automation.net` |
| Stati Uniti centro-settentrionali |`https://<accountId>.webhook.ncus.azure-automation.net`<br>`https://<accountId>.agentsvc.ncus.azure-automation.net`<br>`https://<accountId>.jrds.ncus.azure-automation.net` |
| Stati Uniti orientali |`https://<accountId>.webhook.eus.azure-automation.net`<br>`https://<accountId>.agentsvc.eus.azure-automation.net`<br>`https://<accountId>.jrds.eus.azure-automation.net` |
| Stati Uniti orientali 2 |`https://<accountId>.webhook.eus2.azure-automation.net`<br>`https://<accountId>.agentsvc.eus2.azure-automation.net`<br>`https://<accountId>.jrds.eus2.azure-automation.net` |
| Canada centrale |`https://<accountId>.webhook.cc.azure-automation.net`<br>`https://<accountId>.agentsvc.cc.azure-automation.net`<br>`https://<accountId>.jrds.cc.azure-automation.net` |
| Europa occidentale |`https://<accountId>.webhook.we.azure-automation.net`<br>`https://<accountId>.agentsvc.we.azure-automation.net`<br>`https://<accountId>.jrds.we.azure-automation.net` |
| Europa settentrionale |`https://<accountId>.webhook.ne.azure-automation.net`<br>`https://<accountId>.agentsvc.ne.azure-automation.net`<br>`https://<accountId>.jrds.ne.azure-automation.net` |
| Asia sud-orientale |`https://<accountId>.webhook.sea.azure-automation.net`<br>`https://<accountId>.agentsvc.sea.azure-automation.net`<br>`https://<accountId>.jrds.sea.azure-automation.net` |
| Asia orientale |`https://<accountId>.webhook.ea.azure-automation.net`<br>`https://<accountId>.agentsvc.ea.azure-automation.net`<br>`https://<accountId>.jrds.ea.azure-automation.net` |
| India centrale |`https://<accountId>.webhook.cid.azure-automation.net`<br>`https://<accountId>.agentsvc.cid.azure-automation.net`<br>`https://<accountId>.jrds.cid.azure-automation.net` |
| Giappone orientale |`https://<accountId>.webhook.jpe.azure-automation.net`<br>`https://<accountId>.agentsvc.jpe.azure-automation.net`<br>`https://<accountId>.jrds.jpe.azure-automation.net` |
| Corea centrale |`https://<accountId>.webhook.kc.azure-automation.net`<br>`https://<accountId>.agentsvc.kc.azure-automation.net`<br>`https://<accountId>.jrds.kc.azure-automation.net` |
| Australia sud-orientale |`https://<accountId>.webhook.ase.azure-automation.net`<br>`https://<accountId>.agentsvc.ase.azure-automation.net`<br>`https://<accountId>.jrds.ase.azure-automation.net` |
| Australia orientale |`https://<accountId>.webhook.ae.azure-automation.net`<br>`https://<accountId>.agentsvc.ae.azure-automation.net`<br>`https://<accountId>.jrds.ae.azure-automation.net` |
| Australia centrale |`https://<accountId>.webhook.ac.azure-automation.net`<br>`https://<accountId>.agentsvc.ac.azure-automation.net`<br>`https://<accountId>.jrds.ac.azure-automation.net` |
| Regno Unito meridionale |`https://<accountId>.webhook.uks.azure-automation.net`<br>`https://<accountId>.agentsvc.uks.azure-automation.net`<br>`https://<accountId>.jrds.uks.azure-automation.net` |
| Francia centrale |`https://<accountId>.webhook.fc.azure-automation.net`<br>`https://<accountId>.agentsvc.fc.azure-automation.net`<br>`https://<accountId>.jrds.fc.azure-automation.net` |
| Sudafrica settentrionale |`https://<accountId>.webhook.san.azure-automation.net`<br>`https://<accountId>.agentsvc.san.azure-automation.net`<br>`https://<accountId>.jrds.san.azure-automation.net` |
| Brasile meridionale |`https://<accountId>.webhook.brs.azure-automation.net`<br>`https://<accountId>.agentsvc.brs.azure-automation.net`<br>`https://<accountId>.jrds.brs.azure-automation.net` |
| Cina settentrionale |`https://<accountId>.webhook.bjb.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjb.azure-automation.cn`<br>`https://<accountId>.jrds.bjb.azure-automation.cn` |
| Cina settentrionale 2 |`https://<accountId>.webhook.bjs2.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjs2.azure-automation.cn`<br>`https://<accountId>.jrds.bjs2.azure-automation.cn` |
| Cina orientale 2 |`https://<accountId>.webhook.sha2.azure-automation.cn`<br>`https://<accountId>.agentsvc.sha2.azure-automation.cn`<br>`https://<accountId>.jrds.sha2.azure-automation.cn` |
| US Gov Virginia |`https://<accountId>.webhook.usge.azure-automation.us`<br>`https://<accountId>.agentsvc.usge.azure-automation.us`<br>`https://<accountId>.jrds.usge.azure-automation.us` |
| US Gov Texas |`https://<accountId>.webhook.ussc.azure-automation.us`<br>`https://<accountId>.agentsvc.ussc.azure-automation.us`<br>`https://<accountId>.jrds.ussc.azure-automation.us` |
| US Gov Arizona |`https://<accountId>.webhook.phx.azure-automation.us`<br>`https://<accountId>.agentsvc.phx.azure-automation.us`<br>`https://<accountId>.jrds.phx.azure-automation.us` |

Sostituire `<accountId>` nel record DNS con GUID che rappresenta l'ID dell'account di automazione dall' **URL** del valore. È possibile ottenere l'ID richiesto dalle **chiavi** in **impostazioni account** nel portale di Azure.

![Pagina chiave primaria dell'account di automazione](./media/automation-region-dns-records/automation-account-keys.png)

Copiare il valore dopo *accounts/* dal campo **URL** - `https://<GUID>.agentsvc.<region>.azure-automation.net/accounts/<GUID>`

> [!NOTE]
> Tutti i record DNS webhook e agentservice sono stati aggiornati con i nuovi record DNS di stile per il supporto del collegamento privato. Per i record DNS JRDS, sono supportati sia i record DNS vecchi che quelli nuovi. Se non si usa un collegamento privato, verranno visualizzati i record DNS di stile obsoleti, mentre quelli che usano il collegamento privato vedranno un nuovo stile di record DNS.

È consigliabile usare gli indirizzi elencati quando si definiscono [eccezioni](../automation-runbook-execution.md#exceptions). Per un elenco degli indirizzi IP dell'area anziché dei nomi delle aree, scaricare il file JSON dall'area download Microsoft per gli ambienti cloud seguenti:

* [Intervalli IP e tag di servizio di Azure-pubblico di Azure](https://www.microsoft.com/download/details.aspx?id=56519)
* [Intervalli IP di Azure e tag del servizio-Azure per enti pubblici](https://www.microsoft.com/download/details.aspx?id=57063)
* [Intervalli IP e tag di servizio di Azure-Azure Germania](https://www.microsoft.com/download/details.aspx?id=57064)
* [Intervalli IP e tag di servizio di Azure-Azure Cina-Viani 21](https://www.microsoft.com/download/details.aspx?id=57062)

Il file degli indirizzi IP elenca gli intervalli di indirizzi IP usati nei data center di Microsoft Azure. Il file include gli intervalli di calcolo, SQL e archiviazione e riflette gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP. I nuovi intervalli riportati nel file non vengono usati nei data center per almeno una settimana.

È consigliabile scaricare il nuovo file degli indirizzi IP ogni settimana. e aggiornare quindi il sito per identificare correttamente i servizi in esecuzione in Azure.

> [!NOTE]
> Se si usa ExpressRoute, ricordare che il file degli indirizzi IP viene usato per aggiornare l'annuncio BGP (Border Gateway Protocol) dello spazio di Azure la prima settimana del mese.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come risolvere i problemi relativi ai ruoli di lavoro ibridi per runbook, vedere [Risolvere i problemi dei ruoli di lavoro ibridi per runbook](../troubleshoot/hybrid-runbook-worker.md#general).

* Per informazioni su come risolvere i problemi relativi alla configurazione dello stato, vedere [risoluzione dei problemi di configurazione dello stato](../troubleshoot/desired-state-configuration.md).