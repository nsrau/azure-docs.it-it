---
title: Migliorare il comportamento di sicurezza con il Centro sicurezza di Azure | Microsoft Docs
description: Questo articolo illustra come migliorare il comportamento di sicurezza tramite il monitoraggio delle risorse nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 57d47712fe20fac0bf370a9a100a07a37d7aa753
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996677"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Migliorare il comportamento di sicurezza con il Centro sicurezza di Azure
Questo articolo fornisce informazioni utili per migliorare il comportamento di sicurezza. Descrive come usare le funzionalità di monitoraggio nel Centro sicurezza di Azure per verificare che la sicurezza delle risorse sia elevata e monitorare la conformità ai criteri.

## <a name="how-do-you-strengthen-your-security-posture"></a>Come migliorare il comportamento di sicurezza?
Spesso si pensa al monitoraggio come l'osservazione e l'attesa di un evento al fine di reagire alla situazione verificatasi. Migliorare il comportamento di sicurezza significa dotarsi di una strategia proattiva per il controllo delle risorse che ha lo scopo di identificare i sistemi non conformi agli standard o alle procedure consigliate dell'organizzazione.

Dopo avere abilitato i [criteri di sicurezza](tutorial-security-policy.md) per le risorse di una sottoscrizione, il Centro sicurezza analizza la sicurezza delle risorse per identificare le potenziali vulnerabilità. Le informazioni sulla configurazione di rete sono disponibili immediatamente. A seconda del numero di macchine virtuali e computer con l'agente installato, potrebbe essere necessaria almeno un'ora per raccogliere informazioni sulle macchine virtuali e la configurazione dei computer, ad esempio lo stato di aggiornamento della sicurezza e la configurazione del sistema operativo. È possibile visualizzare un elenco completo dei problemi e dei modi esistenti per rafforzare la rete e correggere i rischi nel riquadro **Raccomandazioni**.

Lo stato di sicurezza delle risorse ed eventuali problemi sono visualizzabili in base al tipo di risorsa:

- Per monitorare lo stato di sicurezza delle risorse del computer e delle app e ricevere consigli per migliorare la protezione, vedere [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-protection.md)
- Per monitorare le risorse di rete, ad esempio le macchine virtuali, i gruppi di sicurezza di rete e gli endpoint, e ricevere consigli per migliorare la sicurezza, vedere [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md) per altre informazioni. 
- Per monitorare i dati e le risorse di archiviazione, ad esempio i server SQL e gli account di archiviazione, e ricevere consigli per migliorare la sicurezza, vedere [Protezione del servizio SQL di Azure e dei dati nel Centro sicurezza di Azure](security-center-sql-service-recommendations.md) per altre informazioni. 
- Per monitorare le risorse di identità e di accesso, tra cui le autorizzazioni MFA e per gli account, e ricevere consigli per migliorare la sicurezza, vedere [Monitoraggio dell'identità e dell'accesso nel Centro sicurezza di Azure](security-center-identity-access.md) per altre informazioni. 
- Per monitorare l'accesso just-in-time alle risorse, vedere [gestire l'accesso alle macchine virtuali usando JIT (just-in-Time](security-center-just-in-time.md) ) per ulteriori informazioni. 


Per altre informazioni su come applicare le raccomandazioni, leggere l'articolo sull'[implementazione delle raccomandazioni sulla sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).



![Riquadro Integrità sicurezza delle risorse](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Vedere anche
Questo articolo descrive come usare le funzionalità di monitoraggio nel Centro sicurezza di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md): Informazioni su come configurare le impostazioni di sicurezza nel Centro sicurezza di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): Informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): Informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): Domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/): Post di blog sulla sicurezza e sulla conformità di Azure.
