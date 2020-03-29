---
title: Prezzi dei livelli del Centro sicurezza di Azure
description: Azure Security Center is offered in two tiers - Free and Standard. Questa pagina mostra come eseguire l'aggiornamento da Gratuito a Standard.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2019
ms.author: memildin
ms.openlocfilehash: 60a88e667918533f2c507846fa75b0e036ba5262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921284"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>Eseguire l'aggiornamento al livello Standard per una maggiore sicurezza
Il Centro sicurezza di Azure offre la gestione unificata della sicurezza e la protezione avanzata dalle minacce per carichi di lavoro in esecuzione in Azure, in locale e in altri cloud. Offre visibilità e controllo su carichi di lavoro cloud ibridi, difese attive che riducono l'esposizione a minacce e il rilevamento intelligente che consente di tenere il passo con gli attacchi informatici in rapida evoluzione.

## <a name="pricing-tiers"></a>Piani tariffari
Il Centro sicurezza è disponibile in due livelli:

- Il livello **Gratuito** è abilitato in tutte le sottoscrizioni di Azure dopo aver visitato il dashboard del Centro sicurezza di Azure nel portale di Azure per la prima volta o se abilitato a livello di codice tramite API. Il livello gratuito offre criteri di sicurezza, valutazione continua della sicurezza e consigli di sicurezza utilizzabili che consentono di proteggere le risorse di Azure.The free tier provides security policy, continuous security assessment, and actionable security recommendations to help you protect your Azure resources.
- Il livello **Standard** estende le funzionalità del livello Gratuito ai carichi di lavoro in esecuzione in cloud privati e pubblici, offrendo la gestione unificata della sicurezza e la protezione dalle minacce nei carichi di lavoro cloud ibridi. Il livello standard aggiunge anche funzionalità di protezione dalle minacce, che utilizzano l'analisi comportamentale integrata e l'apprendimento automatico per identificare gli attacchi e gli exploit zero-day, i controlli di accesso e applicazione per ridurre l'esposizione ad attacchi di rete e malware e altro ancora. Inoltre, il livello standard aggiunge l'analisi delle vulnerabilità per le macchine virtuali. Puoi provare il livello standard gratuitamente. Lo standard del Centro sicurezza supporta le risorse di Azure, tra cui macchine virtuali, set di scalabilità di macchine virtuali, servizio app, server SQL e account di archiviazione. Se si dispone dello standard del Centro sicurezza di Azure, è possibile rifiutare esplicitamente il supporto in base al tipo di risorsa. 

La maggior parte delle valutazioni di sicurezza del livello gratuito per le macchine virtuali, nonché molti degli avvisi di sicurezza del livello standard, richiedono l'installazione della funzionalità Microsoft Monitoring Agent (MMA). È possibile abilitare il provisioning automatico nel Centro sicurezza per distribuire automaticamente l'agente per le macchine virtuali di Azure.You can enable Auto Provision on Security Center to automatically deploy the agent for your Azure VMs.

## <a name="try-standard-tier-free-for-30-days"></a>Prova il livello standard gratuitamente per 30 giorni
Il livello standard è gratuito per i primi 30 giorni. Al termine dei 30 giorni, se si sceglie di continuare a usare il servizio, i costi inizieranno a essere addebitati automaticamente.

È possibile aggiornare un'intera sottoscrizione di Azure al livello standard, ereditato da tutte le risorse all'interno della sottoscrizione.

Per ottenere il livello standard:

1. Selezionare **Impostazioni di & dei prezzi** nel menu principale del Centro **sicurezza.**
2. Selezionare la sottoscrizione che si desidera aggiornare allo standard.
3. Selezionare **Piano prezzi**.
4. Selezionare **Standard** per eseguire l'aggiornamento.
5. Fare clic su **Salva**.

[![Prezzi del Centro sicurezza](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> Per abilitare tutte le funzionalità del Centro sicurezza, è necessario applicare il piano tariffario standard alla sottoscrizione contenente le macchine virtuali applicabili. La configurazione dei prezzi per un'area di lavoro non consente l'accesso alle macchine virtuali just-in-time, i controlli adattivi delle applicazioni e i rilevamenti di rete per le risorse di Azure.Configuring pricing for a workspace does not enable just-in-time VM access, adaptive application controls, and network detections for Azure resources.
>

## <a name="why-upgrade-to-standard"></a>Perché eseguire l'aggiornamento allo standard?
Il Centro sicurezza offre maggiore sicurezza e protezione dalle minacce per i carichi di lavoro di cloud ibridi, tra cui:

- **Sicurezza ibrida**: per ottenere una visualizzazione unificata della sicurezza in tutti i carichi di lavoro locali e cloud; applicare i criteri di sicurezza e la valutazione continua della sicurezza dei carichi di lavoro cloud ibridi per garantire la conformità con gli standard di sicurezza; Raccogliere, cercare e analizzare i dati di sicurezza da più origini, inclusi firewall e altre soluzioni partner.
- Avvisi di **sicurezza:** utilizza l'analisi avanzata e Microsoft Intelligent Security Graph per ottenere un vantaggio sull'evoluzione degli attacchi informatici. Sfrutta i vantaggi delle analisi comportamentali predefinite e di apprendimento automatico per identificare gli attacchi e gli exploit zero-day. Monitora le reti, i computer e i servizi cloud per rilevare attacchi imminenti e attività post violazione. Semplifica l'investigazione con strumenti interattivi e intelligence per le minacce contestuale.
- **Analisi delle vulnerabilità per le macchine virtuali:** consente di distribuire facilmente uno scanner a tutte le macchine virtuali che fornisce la soluzione più avanzata del settore per la gestione delle vulnerabilità. Visualizzare, analizzare e correggere i risultati direttamente all'interno del Centro sicurezza. 
- **Controlli di accesso e applicazione:** blocca il malware e altre applicazioni indesiderate applicando consigli di whitelisting basati sull'apprendimento automatico adattati ai tuoi carichi di lavoro specifici. Ridurre la superficie di attacco di rete con accesso just-in-time e controllato alle porte di gestione nelle macchine virtuali di Azure.Reduce the network attack surface with just-in-time, controlled access to management ports on Azure VMs. Questo riduce drasticamente l'esposizione alla forza bruta e altri attacchi di rete.
- **Funzionalità di sicurezza dei contenitori:** traggono vantaggio dalla gestione delle vulnerabilità e dalla protezione dalle minacce in tempo reale negli ambienti containerizzati. Quando si abilita la risorsa di registri contenitore, potrebbero essere necessarie fino a 12 ore fino a quando tutte le funzionalità sono abilitate.


## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati presentati i prezzi del Centro sicurezza. Per altre informazioni sulla sicurezza e la protezione avanzate dalle minacce del livello Standard, vedere:

- [Protezione dalle minacce nel Centro sicurezza di Azure](threat-protection.md)
- [Controllo dell'accesso alle macchine virtuali just-in-time](security-center-just-in-time.md)
- [Panoramica della sicurezza dei contenitori](container-security.md)
- [Dettagli sui prezzi nella tua valuta preferita e in base alla tua regione](https://azure.microsoft.com/pricing/details/security-center/)