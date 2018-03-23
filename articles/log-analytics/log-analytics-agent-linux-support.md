---
title: Risoluzione dei problemi relativi all'agente Linux di Azure Log Analytics | Microsoft Docs
description: Vengono descritti i sintomi, le cause e la risoluzione dei problemi più comuni con l'agente Linux di Log Analytics.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: magoedte
ms.openlocfilehash: 80d7e39b284554ebfa8cac4488e1663b3e3648e8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="how-to-troubleshoot-issues-with-the-linux-agent-for-log-analytics"></a>Come risolvere i problemi relativi all'agente Linux per Log Analytics

Questo articolo illustra come identificare e risolvere gli errori che si possono verificare con l'agente Linux di Log Analytics.

## <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>Problema: Impossibile stabilire la connessione tramite proxy a Log Analytics

### <a name="probable-causes"></a>Possibili cause
* Il proxy specificato durante l'onboarding è errato
* Gli endpoint dei servizi Log Analytics e Automazione di Azure non sono inclusi nell'elenco elementi consentiti nel data center 

### <a name="resolutions"></a>Soluzioni
1. Eseguire di nuovo l'onboarding nel servizio Log Analytics con l'agente di Operations Management Suite per Linux usando il comando seguente con l'opzione `-v` abilitata. In questo modo l'output dettagliato dell'agente si connette tramite proxy al servizio OMS. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Vedere la sezione [Aggiornare le impostazioni proxy](log-analytics-agent-manage.md#update-proxy-settings) per verificare di aver configurato correttamente l'agente per la comunicazione tramite un server proxy.    
* Controllare che gli endpoint di servizio di Log Analytics seguenti siano presenti nell'elenco elementi consentiti:

    |Risorsa agente| Porte | Direzione |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | Porta 443| In ingresso e in uscita |  
    |*.oms.opinsights.azure.com | Porta 443| In ingresso e in uscita |  
    |*.blob.core.windows.net | Porta 443| In ingresso e in uscita |  
    |*.azure-automation.net | Porta 443| In ingresso e in uscita | 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problema: Viene visualizzato un errore 403 durante il tentativo di onboarding

### <a name="probable-causes"></a>Possibili cause
* Data e ora nel server Linux non sono corrette 
* L'ID e la chiave dell'area di lavoro usati non sono corretti

### <a name="resolution"></a>Risoluzione

1. Controllare l'ora nel server Linux con il comando date. Se l'ora è sfasata di +/- 15 minuti rispetto all'ora corrente, l'onboarding ha esito negativo. Per risolvere il problema, aggiornare la data e/o il fuso orario del server Linux. 
2. Verificare di avere installato la versione più recente dell'agente OMS per Linux.  Ora la versione più recente invia una notifica all'utente se la differenza di tempo causa l'errore di onboarding.
3. Eseguire di nuovo l'onboarding usando l'ID area di lavoro corretto e la chiave dell'area di lavoro seguendo le istruzioni di installazione presentate anteriormente in questo argomento.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problema: Viene visualizzato un errore 404 o 500 nel file di log subito dopo l'onboarding
Si tratta di un problema noto che si verifica durante il primo caricamento dei dati di Linux in un'area di lavoro di Log Analytics. Questo non influisce sui dati inviati o sull'esperienza d'uso del servizio.

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problema: Non vengono visualizzati dati nel portale di Azure

### <a name="probable-causes"></a>Possibili cause

- L'onboarding nel servizio Log Analytics ha avuto esito negativo
- La connessione al servizio Log Analytics è bloccata
- Viene eseguito il backup dei dati dell'agente OMS per Linux

### <a name="resolutions"></a>Soluzioni
1. Controllare che l'onboarding del servizio Log Analytics sia avvenuto correttamente verificando la presenza del file seguente: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Eseguire di nuovo l'onboarding usando le istruzioni della riga di comando `omsadmin.sh`
3. Se si usa un proxy, vedere i passaggi di risoluzione del proxy indicati in precedenza.
4. In alcuni casi, quando l'agente di Operations Management Suite per Linux non può comunicare con il servizio, i dati dell'agente vengono inseriti in una coda fino a raggiungere le dimensioni intere del buffer, ovvero 50 MB. L'agente OMS per Linux deve essere riavviato usando il comando seguente: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Il problema è stato risolto nell'agente versione 1.1.0-28 e versioni successive.

