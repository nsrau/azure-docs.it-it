---
title: Connettere i dati di EPV di CyberArk ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore dati CyberArk Enterprise Password Vault (EPV) per eseguire il pull dei log in Sentinel di Azure. Visualizzare i dati di CyberArk EPV in cartelle di lavoro, creare avvisi e migliorare l'analisi.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: c375595951eb760d5341db424c5572719b97046a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102982"
---
# <a name="connect-cyberark-enterprise-password-vault-epv-to-azure-sentinel"></a>Connettere CyberArk Enterprise Password Vault (EPV) ad Azure Sentinel

> [!IMPORTANT]
> Il connettore dati CyberArk Enterprise Password Vault (EPV) in Sentinel di Azure è attualmente disponibile in anteprima pubblica. Questa funzionalità viene fornita senza un contratto di servizio. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il connettore syslog di CyberArk consente di connettere facilmente tutti i log della soluzione CyberArk Security con la sentinella di Azure, di visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'integrazione tra CyberArk e Azure Sentinel usa il connettore dati CEF per analizzare e visualizzare correttamente i messaggi syslog di CyberArk.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-cyberark-epv"></a>Configurare e connettere CyberArk EPV

I log EPV di CyberArk vengono inviati dall'insieme di credenziali a un server di inoltrato di log basato su Linux (che esegue rsyslog o syslog-ng) con l'agente di Log Analytics installato, che esporta i log in Sentinel di Azure. Se non si dispone di un server di inoltri di log, vedere [queste istruzioni](connect-cef-agent.md) per ottenerne uno operativo.

1. Nel portale di Azure Sentinel fare clic su **connettori dati** , selezionare **gli eventi CyberArk Enterprise Password Vault (EPV) (anteprima)** e quindi **aprire la pagina del connettore** .

1. Seguire le istruzioni CyberArk EPV per configurare l'invio di dati syslog al server di inoltro di log.

1. Convalidare la connessione e verificare l'inserimento dei dati seguendo [queste istruzioni](connect-cef-verify.md). Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log** , nella sezione **Azure Sentinel** , nella tabella *CommonSecurityLog* .

Per eseguire una query sui log EPV di CyberArk in Log Analytics, immettere nella `CommonSecurityLog` parte superiore della finestra della query.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere i log dell'insieme di credenziali delle password di CyberArk Enterprise a Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
