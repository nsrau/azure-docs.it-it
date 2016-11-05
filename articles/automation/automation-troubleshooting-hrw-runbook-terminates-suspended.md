---
title: 'Ruolo di lavoro ibrido per runbook: un processo runbook termina con lo stato Sospeso | Microsoft Docs'
description: Sintomi, cause e soluzioni per l'errore di terminazione del processo ruolo di lavoro ibrido per runbook.
services: automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: tysonn

ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/17/2016
ms.author: magoedte

---
# Ruolo di lavoro ibrido per runbook: un processo runbook termina con lo stato Sospeso
## Riepilogo
Il runbook viene sospeso dopo il terzo tentativo di esecuzione. In alcuni casi il runbook non viene completato correttamente e il relativo messaggio di errore non include informazioni aggiuntive sul motivo. Questo articolo illustra le procedure per risolvere i problemi relativi agli errori di esecuzione del runbook ruolo di lavoro ibrido per runbook.

Se l'articolo non tratta il problema riguardante Azure, visitare i forum di Azure su [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). È possibile pubblicare il problema in questi forum o in [@AzureSupport su Twitter](https://twitter.com/AzureSupport). È anche possibile inviare una richiesta di supporto tecnico di Azure selezionando **Ottieni supporto** nel sito del [supporto tecnico di Azure](https://azure.microsoft.com/support/options/).

## Sintomo
Si verifica un errore di esecuzione del runbook e viene restituito l'errore "Impossibile eseguire l'azione del processo ''Attiva'. Il processo si è arrestato in modo imprevisto. L'azione del processo è stata tentata 3 volte."

## Causa
Le cause dell'errore possono essere diverse:

1. Il ruolo di lavoro ibrido è protetto da proxy o firewall
2. Il computer in cui è in esecuzione il ruolo di lavoro ibrido non soddisfa i [requisiti](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-requirements) hardware minimi
3. I runbook non possono autenticarsi con le risorse locali

## Causa 1: il ruolo di lavoro ibrido per runbook è protetto da firewall o proxy
Il computer in cui è in esecuzione il ruolo di lavoro ibrido per runbook è protetto da un firewall o proxy server e l'accesso alla rete in uscita potrebbe non essere consentito o configurato correttamente.

### Soluzione
Verificare che il computer abbia accesso in uscita a *. cloudapp.net sulle porte 443, 9354 e da 30199 a 30000.

## Causa 2: il computer non soddisfa i requisiti hardware minimi
I computer che eseguono il ruolo di lavoro ibrido per runbook devono soddisfare i requisiti hardware minimi per poter ospitare questa funzionalità. In caso contrario, a seconda dell'utilizzo di risorse di altri processi in background e dei conflitti causati dai runbook durante l'esecuzione, il computer diverrà sovraccarico e causerà ritardi o interruzioni del processo del runbook.

### Soluzione
Verificare che il computer designato per svolgere il ruolo di lavoro ibrido per runbook soddisfi i requisiti hardware minimi. In caso affermativo, monitorare l'utilizzo della CPU e della memoria per determinare eventuali correlazioni tra le prestazioni dei processi del ruolo di lavoro ibrido per runbook e Windows. In caso di utilizzo eccessivo della CPU o memoria, potrebbe essere necessario aggiornare o aggiungere altri processori o aumentare la memoria per risolvere il collo di bottiglia della risorsa e quindi l'errore. In alternativa, selezionare una risorsa di calcolo diversa in grado di supportare i requisiti minimi e scalare quando le esigenze del carico di lavoro indicano la necessità di un aumento.

## Causa 3: i runbook non possono autenticarsi con le risorse locali
### Soluzione
Controllare il registro eventi **Microsoft-SMA** per un evento corrispondente con la descrizione *Processo Win32 terminato con codice [4294967295]*. La causa dell'errore è che l'autenticazione non è stata configurata nei runbook oppure le credenziali Esegui come non sono state specificate per il gruppo del ruolo di lavoro ibrido. Per verificare di aver configurato correttamente l'autenticazione per i runbook, vedere [Autorizzazioni per i runbook](automation-hybrid-runbook-worker.md#runbook-permissions).

<!---HONumber=AcomDC_0817_2016-->