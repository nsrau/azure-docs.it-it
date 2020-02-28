---
title: Domande frequenti sul centro sicurezza di Azure-Domande generali
description: Domande frequenti sul centro sicurezza di Azure, un prodotto che consente di prevenire, rilevare e rispondere alle minacce
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: f9043ae1414b63f25583d52100774f3e87754a74
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661840"
---
# <a name="faq---general-questions-about-azure-security-center"></a>Domande frequenti-domande generali sul centro sicurezza di Azure

## <a name="what-is-azure-security-center"></a>Che cos'è il Centro sicurezza di Azure?
Il Centro sicurezza di Azure consente di prevenire, rilevare e rispondere alle minacce con maggiore visibilità e controllo sulla sicurezza delle risorse. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

Il Centro sicurezza usa il Microsoft Monitoring Agent per raccogliere e archiviare i dati. Per informazioni dettagliate, vedere [raccolta dati nel centro sicurezza di Azure](security-center-enable-data-collection.md).


## <a name="how-do-i-get-azure-security-center"></a>In che modo è possibile accedere al Centro sicurezza di Azure?
Il Centro sicurezza di Azure viene abilitato con la sottoscrizione di Microsoft Azure ed è accessibile dal [portale di Azure](https://azure.microsoft.com/features/azure-portal/). Per accedervi, accedere [al portale](https://portal.azure.com), selezionare **Sfoglia**e scorrere fino al **Centro sicurezza**.


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Quali risorse di Azure vengono monitorate dal Centro sicurezza di Azure?
Il Centro sicurezza di Azure monitora le risorse di Azure seguenti:

* Macchine virtuali (VM) (inclusi i [Servizi cloud](../cloud-services/cloud-services-choose-me.md))
* set di scalabilità di macchine virtuali
* Reti virtuali di Azure
* Contenitori
* Servizio di SQL Azure
* Account di archiviazione di Azure
* App Web di Azure in un [ambiente del servizio app](../app-service/environment/intro.md)
* Soluzioni partner integrate con la sottoscrizione di Azure, ad esempio un Web application firewall, nelle VM e nell'ambiente del servizio app

Inoltre, i computer non Azure (inclusi quelli locali) possono essere monitorati anche dal centro sicurezza di Azure. Sono supportati sia computer [Windows](./quick-onboard-windows-computer.md) che [computer Linux](./quick-onboard-linux-computer.md) .


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Come è possibile visualizzare lo stato di sicurezza corrente delle risorse Azure?
La pagina **Panoramica del Centro sicurezza** Mostra il comportamento di sicurezza complessivo dell'ambiente suddiviso per calcolo, rete, archiviazione & dati e applicazioni. Ogni tipo di risorsa ha un indicatore che mostra le vulnerabilità di sicurezza identificate. Facendo clic su ogni sezione viene visualizzato un elenco di problemi di sicurezza identificati dal Centro sicurezza, insieme a un inventario delle risorse nella sottoscrizione.



## <a name="what-is-a-security-policy"></a>Cosa sono i criteri di sicurezza?
I criteri di sicurezza definiscono il set di controlli consigliati per le risorse all'interno della sottoscrizione specificata. Nel Centro sicurezza di Azure è possibile definire i criteri per le sottoscrizioni di Azure in base ai requisiti di sicurezza della società e al tipo di applicazione o al livello di riservatezza dei dati in ciascuna sottoscrizione.

I criteri di sicurezza abilitati nel Centro sicurezza di Azure determinano il monitoraggio e i suggerimenti per la sicurezza. Per ulteriori informazioni sui criteri di sicurezza, vedere [Monitoraggio dello stato di sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md).


## <a name="who-can-modify-a-security-policy"></a>Chi può modificare i criteri di sicurezza?
Per modificare i criteri di sicurezza, è necessario essere Amministratore della protezione, proprietario o collaboratore di tale sottoscrizione.

Per informazioni su come configurare i criteri di sicurezza, vedere [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md).


## <a name="what-is-a-security-recommendation"></a>Che cos'è un suggerimento per la sicurezza?
Il Centro sicurezza di Azure analizza lo stato di sicurezza delle risorse di Azure. Quando vengono identificate potenziali vulnerabilità di sicurezza, vengono creati i suggerimenti. Tali suggerimenti illustrano in dettaglio il processo di configurazione dei controlli necessari. Alcuni esempi:

* Provisioning di antimalware per identificare e rimuovere il software dannoso
* [Gruppi di sicurezza di rete](../virtual-network/security-overview.md) e regole per controllare il traffico verso le macchine virtuali
* Provisioning di un Web application firewall per la difesa da attacchi diretti alle applicazioni Web
* Distribuzione degli aggiornamenti di sistema mancanti
* Risoluzione delle configurazioni del sistema operativo che non corrispondono alle baseline consigliate

Qui vengono visualizzati solo i suggerimenti abilitati in Criteri di sicurezza.



## <a name="what-triggers-a-security-alert"></a>Che cosa attiva un avviso di sicurezza?
Il Centro sicurezza di Azure raccoglie, analizza e unisce automaticamente i dati di log dalle risorse di Azure, dalla rete e dalle soluzioni dei partner, ad esempio antimalware e firewall. Quando vengono rilevate minacce, viene creato un avviso di sicurezza. Ad esempio, è compreso il rilevamento di:

* Macchine virtuali compromesse in comunicazione con indirizzi IP dannosi noti
* Malware avanzato rilevato mediante i report degli errori di Windows
* Attacchi di forza bruta contro le macchine virtuali
* Avvisi di sicurezza da soluzioni di sicurezza integrata dei partner, ad esempio antimalware o Web application firewall


## Perché sono stati modificati i valori di Punteggio sicuro? <a name="secure-score-faq"></a>
A partire da febbraio 2019, il Centro sicurezza ha regolato il Punteggio di alcune raccomandazioni, in modo da adattarsi meglio alla loro gravità. In seguito a questa modifica, è possibile che vengano apportate modifiche ai valori di Punteggio sicuro complessivi.  Per altre informazioni sul punteggio sicuro, vedere [calcolo del Punteggio sicuro](security-center-secure-score.md).


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Qual è la differenza tra le minacce rilevate e le minacce segnalate da Microsoft Security Response Center e dal Centro sicurezza di Azure?
Microsoft Security Response Center (MSRC) esegue il monitoraggio selettivo della sicurezza della rete e dell'infrastruttura di Azure e riceve informazioni sulle minacce e segnalazioni di violazioni da terzi. Se MSRC rileva che un'entità illegale o non autorizzata ha ottenuto l'accesso a dati del cliente o che l'uso di Azure da parte del cliente non è conforme ai criteri d'uso, un responsabile della sicurezza segnala il problema al cliente. Come notifica viene in genere inviato un messaggio di posta elettronica ai contatti per la sicurezza specificati nel Centro sicurezza di Azure oppure al proprietario della sottoscrizione di Azure se non è specificato nessun contatto per la sicurezza.

Il Centro sicurezza PC è un servizio di Azure che esegue il monitoraggio continuo dell'ambiente Azure del cliente e applica metodi di analisi per rilevare un'ampia gamma di attività potenzialmente dannose. I rilevamenti vengono visualizzati come di avvisi di sicurezza nel dashboard del Centro sicurezza PC.