---
title: Risolvere i problemi del ruolo di lavoro ibrido per runbook di Automazione di Azure | Documentazione Microsoft
description: "Descrive i sintomi, le cause e la risoluzione dei problemi più comuni del ruolo di lavoro ibrido per runbook in Automazione di Azure."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 02c6606e-8924-4328-a196-45630c2255e9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: 9d1ceda5a072f494651a751a25a8ccf66e4c72ef
ms.contentlocale: it-it
ms.lasthandoff: 07/27/2017

---

# <a name="troubleshooting-tips-for-hybrid-runbook-worker"></a>Consigli per la risoluzione dei problemi del ruolo di lavoro ibrido per runbook

Questo articolo illustra come identificare e risolvere gli errori che si possono verificare con i ruoli di lavoro ibridi per runbook in Automazione di Azure.

## <a name="a-runbook-job-terminates-with-a-status-of-suspended"></a>Un processo runbook termina con lo stato Sospeso

Il runbook viene sospeso dopo il terzo tentativo di esecuzione. In alcuni casi il runbook non viene completato correttamente e il relativo messaggio di errore non include informazioni aggiuntive sul motivo. Questo articolo illustra le procedure per risolvere i problemi relativi agli errori di esecuzione del runbook ruolo di lavoro ibrido per runbook.

Se il problema riguardante Azure non è trattato in questo articolo, visitare i forum di Azure su [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). È possibile pubblicare il problema in questi forum o in [@AzureSupport su Twitter](https://twitter.com/AzureSupport). È anche possibile inviare una richiesta di supporto tecnico di Azure selezionando **Ottieni supporto** nel sito del [supporto tecnico di Azure](https://azure.microsoft.com/support/options/).

### <a name="symptom"></a>Sintomo
Si verifica un errore di esecuzione del runbook e viene restituito l'errore "Impossibile eseguire l'azione del processo ''Attiva'. Il processo si è arrestato in modo imprevisto. L'azione del processo è stata tentata 3 volte."

Le cause dell'errore possono essere diverse: 

1. Il ruolo di lavoro ibrido è protetto da proxy o firewall
2. Il computer in cui è in esecuzione il ruolo di lavoro ibrido non soddisfa i [requisiti hardware](automation-offering-get-started.md#hybrid-runbook-worker)  
3. I runbook non possono autenticarsi con le risorse locali

#### <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>Causa 1: il ruolo di lavoro ibrido per runbook è protetto da firewall o proxy
Il computer in cui è in esecuzione il ruolo di lavoro ibrido per runbook è protetto da un firewall o proxy server e l'accesso alla rete in uscita potrebbe non essere consentito o configurato correttamente.

#### <a name="solution"></a>Soluzione
Verificare che il computer abbia accesso in uscita a *.azure-automation.net sulla porta 443. 

#### <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>Causa 2: il computer non soddisfa i requisiti hardware minimi
I computer che eseguono il ruolo di lavoro ibrido per runbook devono soddisfare i requisiti hardware minimi per poter ospitare questa funzionalità. In caso contrario, a seconda dell'utilizzo di risorse di altri processi in background e dei conflitti causati dai runbook durante l'esecuzione, il computer diverrà sovraccarico e causerà ritardi o interruzioni del processo del runbook. 

#### <a name="solution"></a>Soluzione
Verificare che il computer designato per svolgere il ruolo di lavoro ibrido per runbook soddisfi i requisiti hardware minimi.  In caso affermativo, monitorare l'utilizzo della CPU e della memoria per determinare eventuali correlazioni tra le prestazioni dei processi del ruolo di lavoro ibrido per runbook e Windows.  In caso di utilizzo eccessivo della CPU o memoria, potrebbe essere necessario aggiornare o aggiungere altri processori o aumentare la memoria per risolvere il collo di bottiglia della risorsa e quindi l'errore. In alternativa, selezionare una risorsa di calcolo diversa in grado di supportare i requisiti minimi e scalare quando le esigenze del carico di lavoro indicano la necessità di un aumento.         

#### <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>Causa 3: i runbook non possono autenticarsi con le risorse locali

#### <a name="solution"></a>Soluzione
Controllare il registro eventi **Microsoft-SMA** per un evento corrispondente con la descrizione *Processo Win32 terminato con codice [4294967295]*.  La causa dell'errore è che l'autenticazione non è stata configurata nei runbook oppure le credenziali Esegui come non sono state specificate per il gruppo del ruolo di lavoro ibrido.  Per verificare di aver configurato correttamente l'autenticazione per i runbook, vedere [Autorizzazioni per i runbook](automation-hrw-run-runbooks.md#runbook-permissions) .  

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla risoluzione di altri problemi di Automazione, vedere [Risoluzione dei problemi comuni di Automazione di Azure](automation-troubleshooting-automation-errors.md) 
