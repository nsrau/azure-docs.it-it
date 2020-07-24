---
title: Connettere i dati del sistema di gestione degli attacchi illusoria ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati del sistema di gestione degli attacchi illusoria ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: ea7953be25473357f7ed572fa8b76076edc6f75a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87038118"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>Connettere il sistema di gestione degli attacchi illusoria ad Azure Sentinel

> [!IMPORTANT]
> Il connettore dati del sistema di gestione degli attacchi illusoria in Sentinel di Azure è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo illustra come connettere il [sistema di gestione degli attacchi illusoria](https://www.illusivenetworks.com/technology/platform/attack-detection-system) ad Azure Sentinel. Il connettore dati del sistema di gestione degli attacchi illusoria consente di condividere i dati di analisi della superficie di attacco di illusoria con Azure Sentinel e visualizzare queste informazioni nei dashboard dedicati che offrono informazioni dettagliate sul rischio della superficie di attacco dell'organizzazione (Dashboard ASM) e tenere traccia del movimento laterale non autorizzato nella rete dell'organizzazione (Dashboard ADS).

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>Inviare i log del sistema di gestione degli attacchi illusoria all'agente syslog  

Configurare il sistema di gestione degli attacchi per l'invio dei messaggi syslog in formato CEF all'area di lavoro di Azure tramite l'agente syslog.

1. Accedere alla console di illusoria e passare a Settings->Reporting.

1. Trovare i server syslog.

1. Fornire le informazioni seguenti:
   - Nome host: indirizzo IP dell'agente syslog di Linux o nome host FQDN
   - Porta: 514
   - Protocollo: TCP
   - Messaggi di controllo: inviare messaggi di controllo al server

1. Per aggiungere il server syslog, fare clic su Aggiungi.

1. Per usare lo schema pertinente nei **log** del sistema di gestione degli attacchi illusoria, cercare **CommonSecurityLog**.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere il sistema di gestione degli attacchi illusoria ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
