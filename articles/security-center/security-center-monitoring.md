---
title: Migliorare il comportamento di sicurezza con il Centro sicurezza di Azure | Microsoft Docs
description: Questo articolo illustra come migliorare il comportamento di sicurezza tramite il monitoraggio delle risorse nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 2827461ee78be8d8976ec23e59d489213eb94e31
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52316022"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Migliorare il comportamento di sicurezza con il Centro sicurezza di Azure
Questo articolo fornisce informazioni utili per migliorare il comportamento di sicurezza. Descrive come usare le funzionalità di monitoraggio nel Centro sicurezza di Azure per verificare che la sicurezza delle risorse sia elevata e monitorare la conformità ai criteri.

## <a name="how-do-you-strengthen-your-security-posture"></a>Come migliorare il comportamento di sicurezza?
Spesso si pensa al monitoraggio come l'osservazione e l'attesa di un evento al fine di reagire alla situazione verificatasi. Migliorare il comportamento di sicurezza significa dotarsi di una strategia proattiva per il controllo delle risorse che ha lo scopo di identificare i sistemi non conformi agli standard o alle procedure consigliate dell'organizzazione.

Dopo avere abilitato i [criteri di sicurezza](security-center-azure-policy.md) per le risorse di una sottoscrizione, il Centro sicurezza analizza la sicurezza delle risorse per identificare le potenziali vulnerabilità. Le informazioni sulla configurazione di rete sono disponibili immediatamente. A seconda del numero di macchine virtuali e computer con l'agente installato, potrebbe essere necessaria almeno un'ora per raccogliere informazioni sulle macchine virtuali e la configurazione dei computer, ad esempio lo stato di aggiornamento della sicurezza e la configurazione del sistema operativo. È possibile visualizzare un elenco completo dei problemi e dei modi esistenti per rafforzare la rete e correggere i rischi nel riquadro **Raccomandazioni**.

Lo stato di sicurezza delle risorse ed eventuali problemi sono visualizzabili in base al tipo di risorsa:

- Per monitorare lo stato di sicurezza delle risorse del computer e delle app e ricevere consigli per migliorare la protezione, vedere [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-protection.md)
- Per monitorare le risorse di rete, ad esempio le macchine virtuali, i gruppi di sicurezza di rete e gli endpoint, e ricevere consigli per migliorare la sicurezza, vedere [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md) per altre informazioni. 
- Per monitorare i dati e le risorse di archiviazione, ad esempio i server SQL e gli account di archiviazione, e ricevere consigli per migliorare la sicurezza, vedere [Protezione del servizio SQL di Azure e dei dati nel Centro sicurezza di Azure](security-center-sql-service-recommendations.md) per altre informazioni. 
- Per monitorare le risorse di identità e di accesso, tra cui le autorizzazioni MFA e per gli account, e ricevere consigli per migliorare la sicurezza, vedere [Monitoraggio dell'identità e dell'accesso nel Centro sicurezza di Azure](security-center-identity-access.md) per altre informazioni. 
- Per monitorare l'accesso Just-In-Time alle risorse, vedere [Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time)](security-center-just-in-time.md) per altre informazioni. 


Per altre informazioni su come applicare le raccomandazioni, leggere l'articolo sull'[implementazione delle raccomandazioni sulla sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).



![Riquadro Integrità sicurezza delle risorse](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Vedere anche 
Questo articolo descrive come usare le funzionalità di monitoraggio nel Centro sicurezza di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-azure-policy.md): informazioni su come configurare le impostazioni di sicurezza nel Centro sicurezza di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare l'integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure.
