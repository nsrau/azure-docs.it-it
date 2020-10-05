---
title: Azure Defender per server - Vantaggi e funzionalità
description: Informazioni sui vantaggi e sulle funzionalità di Azure Defender per server.
author: memildin
ms.author: memildin
ms.date: 9/23/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 8757399329f3a9bd9f4d7b914b12b2a0f7e85603
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448290"
---
# <a name="introduction-to-azure-defender-for-servers"></a>Introduzione ad Azure Defender per server

Azure Defender per server aggiunte il rilevamento delle minacce e difese avanzate per i computer Windows e Linux.

Per Windows, Azure Defender si integra con i servizi di Azure per monitorare e proteggere i computer basati su Windows. Il Centro sicurezza presenta gli avvisi e i suggerimenti per la correzione provenienti da tutti questi servizi in un formato di facile utilizzo.

Per Linux, Azure Defender raccoglie record di controllo dai computer Linux usando **auditd**, uno dei framework di controllo di Linux più diffusi. Auditd risiede nel kernel mainline. 


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>Vantaggi di Azure Defender per server

Azure Defender offre funzionalità di rilevamento delle minacce e di protezione, tra cui:

- **Analisi della valutazione delle vulnerabilità per le VM**: il rilevatore di vulnerabilità incluso nel Centro sicurezza di Azure è basato su tecnologia Qualys. 

    Il rilevatore di Qualys è uno degli strumenti migliori per l'identificazione in tempo reale delle vulnerabilità nelle macchine virtuali di Azure. Non è necessaria una licenza Qualys, né un account Qualys: tutto viene gestito senza interruzioni all'interno del Centro sicurezza. [Altre informazioni](deploy-vulnerability-assessment-vm.md)

- **Accesso Just-In-Time (JIT) alla VM**: gli autori di minacce eseguono attivamente ricerche di computer accessibili con porte di gestione aperte, come RDP o SSH. Tutte le macchine virtuali sono obiettivi potenziali per un attacco. Quando una VM viene compromessa, viene usata come punto di ingresso per attaccare altre risorse nell'ambiente.

    Quando si abilita Azure Defender per server, è possibile usare l'accesso Just-In-Time (JIT) alla VM per bloccare il traffico in ingresso verso le VM, in modo da ridurre l'esposizione agli attacchi, offrendo al tempo stesso un accesso facile per connettersi alle macchine virtuali quando necessario. [Altre informazioni](just-in-time-explained.md)

- **Monitoraggio dell'integrità dei file**: Monitoraggio dell'integrità dei file, detto anche monitoraggio delle modifiche, esamina i file e i registri del sistema operativo, il software delle applicazioni e altri elementi alla ricerca di modifiche che potrebbero indicare un attacco. Viene usato un metodo di confronto per determinare se lo stato corrente del file è diverso rispetto all'ultima analisi. È possibile sfruttare questo confronto per determinare se sono state apportate modifiche sospette o valide ai file.

    Quando si abilita Azure Defender per server, è possibile usare Monitoraggio dell'integrità dei file per convalidare l'integrità dei file di Windows, dei registri di Windows e dei file di Linux. [Altre informazioni](security-center-file-integrity-monitoring.md)

- **Controlli applicazioni adattivi**: i controlli applicazioni adattivi sono una soluzione intelligente e automatizzata per definire elenchi di applicazioni note sicure consentite per i computer.

    Dopo l'abilitazione e la configurazione dei controlli applicazioni adattivi, si riceveranno avvisi di sicurezza se viene eseguita un'applicazione non inclusa nell'elenco di applicazioni definite come sicure. [Altre informazioni](security-center-adaptive-application.md)

- **Protezione avanzata adattiva per la rete**: l'applicazione dei gruppi di sicurezza di rete per filtrare il traffico verso e dalle risorse consente di migliorare il comportamento di sicurezza della rete. In alcuni casi, tuttavia, è comunque possibile che il traffico effettivo che attraversa il gruppo di sicurezza di rete corrisponda a un subset delle regole del gruppo di sicurezza di rete definite. In questi casi è possibile migliorare ancora il comportamento di sicurezza applicando la protezione avanzata alle regole del gruppo di sicurezza di rete, in base ai criteri effettivi del traffico.

    Protezione avanzata adattiva per la rete fornisce raccomandazioni per migliorare la protezione avanzata delle regole del gruppo di sicurezza di rete. Usa un algoritmo di Machine Learning che prende in considerazione il traffico effettivo, la configurazione attendibile nota, l'intelligence sulle minacce e altri indicatori di compromissione e quindi fornisce raccomandazioni per consentire il traffico solo da tuple di IP/porta specifiche. [Altre informazioni](security-center-adaptive-network-hardening.md)

- **Integrazione con Microsoft Defender Advanced Threat Protection (solo Windows)** : Azure Defender è integrato con Microsoft Defender Advanced Threat Protection (ATP). Insieme, le due soluzioni offrono funzionalità di rilevamento e reazione dagli endpoint (EDR) complete. [Altre informazioni](security-center-wdatp.md)

    > [!IMPORTANT]
    > Il sensore di Microsoft Defender ATP viene abilitato automaticamente nei server Windows che usano il Centro sicurezza.

    Quando Microsoft Defender ATP rileva una minaccia, attiva un avviso. L'avviso viene ora mostrato nel Centro sicurezza. Dal Centro sicurezza è anche possibile passare alla console di Microsoft Defender ATP ed eseguire un'indagine dettagliata per individuare l'ambito dell'attacco. Per altre informazioni su Microsoft Defender ATP, vedere [Onboard servers to the Microsoft Defender ATP service](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) (Onboarding dei server nel servizio Microsoft Defender ATP).

- **Protezione avanzata per host Docker**: il Centro sicurezza di Azure identifica i contenitori non gestiti ospitati in VM Linux IaaS o in altri computer Linux che eseguono contenitori Docker. Il Centro sicurezza valuta continuamente le configurazioni di questi contenitori. Li confronta quindi con Center for Internet Security (CIS) Docker Benchmark. Il Centro sicurezza include l'intero set di regole di CIS Docker Benchmark e invia avvisi se i contenitori non soddisfano i controlli. [Altre informazioni](harden-docker-hosts.md)

- **Rilevamento di attacco senza file (solo Windows)** : gli attacchi senza file inseriscono payload dannosi nella memoria per evitare il rilevamento da parte delle tecniche di analisi basate su disco. Il payload dell'utente malintenzionato persiste nella memoria dei processi compromessi ed esegue numerose attività dannose.

  Con il rilevamento degli attacchi senza file, tecnologie automatizzate di analisi forense della memoria identificano i toolkit, le tecniche e i comportamenti degli attacchi senza file. Questa soluzione analizza periodicamente il computer in fase di esecuzione ed estrae informazioni dettagliate direttamente dalla memoria dei processi. Le informazioni dettagliate specifiche includono l'identificazione di: 

  - Toolkit noti e software di crypto mining 

  - Shellcode, un piccolo frammento di codice usato in genere come payload nello sfruttamento di una vulnerabilità software

  - Codice eseguibile dannoso inserito nella memoria dei processi

  Il rilevamento di attacchi senza file genera avvisi di sicurezza dettagliati che contengono descrizioni con metadati aggiuntivi sul processo, ad esempio l'attività di rete. Queste informazioni accelerano la valutazione dell'avviso, la correlazione e il tempo di risposta downstream. Questo approccio integra le soluzioni EDR basate su eventi e offre una maggiore copertura del rilevamento.

  Per informazioni dettagliate sugli avvisi di rilevamento degli attacchi senza file, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-windows).

- **Integrazione degli avvisi auditd Linux e dell'agente di Log Analytics (solo Linux)** : il sistema auditd è un sottosistema a livello di kernel, responsabile del monitoraggio delle chiamate di sistema. Filtra le chiamate in base a un set di regole specificato e scrive messaggi per le chiamate in un socket. Il Centro sicurezza integra le funzionalità del pacchetto auditd nell'agente di Log Analytics. Questa integrazione consente la raccolta degli eventi auditd in tutte le distribuzioni Linux supportate, senza alcun prerequisito.

    I record auditd vengono raccolti, arricchiti e aggregati in eventi usando l'agente di Log Analytics per l'agente Linux. Il Centro sicurezza aggiunge continuamente nuove analisi, che usano i segnali di Linux per rilevare comportamenti dannosi in computer Linux locali e nel cloud. In modo simile alle funzionalità di Windows, queste analisi abbracciano processi sospetti, tentativi di accesso ambigui, caricamento del modulo kernel e altre attività. Queste attività possono indicare che un computer è sotto attacco o è stato violato.  

    Per un elenco degli avvisi di Linux, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-linux).


## <a name="simulating-alerts"></a>Simulazione di avvisi

È possibile simulare avvisi scaricando uno dei playbook seguenti:

- Per Windows: [Azure Security Center Playbook: Security Alerts](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf) (Playbook del Centro sicurezza di Azure: Avvisi di sicurezza)

- Per Linux: [Azure Security Center Playbook: Linux Detections](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf) (Playbook del Centro sicurezza di Azure: rilevamenti Linux).




## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni su Azure Defender per server. 

Per i materiali correlati, vedere gli articoli seguenti: 

- Che un avviso venga generato dal Centro sicurezza o ricevuto dal Centro sicurezza da un prodotto di sicurezza diverso, è possibile esportarlo. Per esportare gli avvisi in Azure Sentinel, in qualsiasi soluzione SIEM di terze parti o in qualsiasi altro strumento esterno, seguire le istruzioni riportate in [Esportazione di avvisi in un sistema SIEM](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Abilitare Azure Defender](security-center-pricing.md)