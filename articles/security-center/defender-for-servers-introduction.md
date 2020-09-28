---
title: 'Azure Defender per server: vantaggi e funzionalità'
description: Scopri i vantaggi e le funzionalità di Azure Defender per i server.
author: memildin
ms.author: memildin
ms.date: 9/23/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 5e4244c0526dbac3bb8e9b2d3856eb01e1b2a2b7
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403495"
---
# <a name="introduction-to-azure-defender-for-servers"></a>Introduzione ad Azure Defender per i server

Azure Defender per i server aggiunge funzionalità di rilevamento delle minacce e difese avanzate per i computer Windows e Linux.

Per Windows, Azure Defender si integra con i servizi di Azure per monitorare e proteggere i computer basati su Windows. Il Centro sicurezza presenta gli avvisi e i suggerimenti per la correzione provenienti da tutti questi servizi in un formato di facile utilizzo.

Per Linux, Azure Defender raccoglie i record di controllo dai computer Linux usando **auditd**, uno dei framework di controllo di Linux più comuni. Auditd risiede nel kernel mainline. 


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>Quali sono i vantaggi di Azure Defender per i server?

Le funzionalità di rilevamento e protezione delle minacce fornite con Azure Defender per i server includono:

- **Valutazione della vulnerabilità analisi per le macchine virtuali** : lo scanner di vulnerabilità incluso nel centro sicurezza di Azure è basato su Qualys. 

    Qualys ' scanner è uno degli strumenti principali per l'identificazione in tempo reale delle vulnerabilità nelle macchine virtuali di Azure. Non è necessaria una licenza Qualys, né un account Qualys: tutto viene gestito senza interruzioni all'interno del Centro sicurezza. [Altre informazioni](deploy-vulnerability-assessment-vm.md)

- **Accesso just-in-time (JIT) alla macchina virtuale**  : gli attori minacciano attivamente la ricerca di computer accessibili con porte di gestione aperte, ad esempio RDP o SSH. Tutte le macchine virtuali sono potenziali destinazioni per un attacco. Quando una macchina virtuale viene compromessa correttamente, viene usata come punto di ingresso per attaccare ulteriori risorse all'interno dell'ambiente.

    Quando si Abilita Azure Defender per i server, è possibile usare l'accesso just-in-time alle VM per bloccare il traffico in ingresso verso le macchine virtuali, riducendo l'esposizione agli attacchi offrendo un facile accesso per connettersi alle macchine virtuali quando necessario. [Altre informazioni](just-in-time-explained.md)

- Il monitoraggio dell'integrità dei file **(** FIM), il monitoraggio dell'integrità dei file, noto anche come monitoraggio delle modifiche, esamina i file e i registri del sistema operativo, il software applicativo e altri per le modifiche che potrebbero indicare un attacco. Viene usato un metodo di confronto per determinare se lo stato corrente del file è diverso rispetto all'ultima analisi. È possibile sfruttare questo confronto per determinare se sono state apportate modifiche sospette o valide ai file.

    Quando si Abilita Azure Defender per i server, è possibile usare FIM per convalidare l'integrità dei file di Windows, dei registri di Windows e dei file Linux. [Altre informazioni](security-center-file-integrity-monitoring.md)

- **Adaptive Application Controls (AAC)** : i controlli delle applicazioni adattivi sono una soluzione intelligente e automatizzata per la definizione di Consenti elenchi di applicazioni sicure per i computer.

    Quando sono stati abilitati e configurati i controlli delle applicazioni adattivi, si otterranno avvisi di sicurezza se un'applicazione viene eseguita oltre a quelle definite come sicure. [Altre informazioni](security-center-adaptive-application.md)

- **Adaptive Network hardening (Anh)** : l'applicazione di gruppi di sicurezza di rete (NSG) per filtrare il traffico da e verso le risorse, migliora il comportamento di sicurezza della rete. Tuttavia, è comunque possibile che si verifichino alcuni casi in cui il traffico effettivo che scorre attraverso NSG è un subset delle regole NSG definite. In questi casi, migliorare ulteriormente il comportamento di sicurezza può essere effettuato grazie alla protezione avanzata delle regole NSG, in base ai modelli di traffico effettivi.

    La protezione avanzata della rete adattiva fornisce consigli per rafforzare ulteriormente le regole NSG. Usa un algoritmo di Machine Learning che determina il traffico effettivo, la configurazione attendibile nota, l'Intelligence per le minacce e altri indicatori di compromissione, quindi fornisce consigli per consentire il traffico solo da tuple IP/porte specifiche. [Altre informazioni](security-center-adaptive-network-hardening.md)

- **Integrazione con Microsoft Defender Advanced Threat Protection (ATP) (solo Windows)** : Azure Defender si integra con Microsoft Defender Advanced Threat Protection (ATP). Insieme, le due soluzioni offrono funzionalità di rilevamento e reazione dagli endpoint (EDR) complete. [Altre informazioni](security-center-wdatp.md)

    > [!IMPORTANT]
    > Il sensore di Microsoft Defender ATP viene abilitato automaticamente nei server Windows che usano il Centro sicurezza.

    Quando Microsoft Defender ATP rileva una minaccia, attiva un avviso. L'avviso viene visualizzato nel centro sicurezza. Dal centro sicurezza è anche possibile passare alla console di Microsoft Defender ATP ed eseguire un'indagine dettagliata per individuare l'ambito dell'attacco. Per altre informazioni su Microsoft Defender ATP, vedere [Onboard servers to the Microsoft Defender ATP service](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) (Onboarding dei server nel servizio Microsoft Defender ATP).

- Protezione **avanzata host Docker** : il Centro sicurezza di Azure identifica i contenitori non gestiti ospitati in macchine virtuali IaaS Linux o altri computer Linux che eseguono contenitori docker. Il Centro sicurezza valuta continuamente le configurazioni di questi contenitori. Viene quindi confrontato con il benchmark Docker di Center for Internet Security (CIS). Il Centro sicurezza include l'intero set di regole del benchmark Docker di CIS e avvisa l'utente se i contenitori non soddisfano i controlli. [Altre informazioni](harden-docker-hosts.md)

- Rilevamento di attacchi senza **file (solo Windows)** : gli attacchi senza file inseriscono payload dannosi nella memoria per evitare il rilevamento da parte di tecniche di analisi basate su disco. Il payload del pirata informatico viene quindi mantenuto nella memoria dei processi compromessi ed esegue un'ampia gamma di attività dannose.

  Con il rilevamento degli attacchi senza file, tecnologie automatizzate di analisi forense della memoria identificano i toolkit, le tecniche e i comportamenti degli attacchi senza file. Questa soluzione analizza periodicamente il computer in fase di esecuzione ed estrae informazioni dettagliate direttamente dalla memoria dei processi. Informazioni dettagliate specifiche includono l'identificazione di: 

  - Toolkit ben noti e software di data mining di crittografia 

  - Shellcode, ovvero un piccolo frammento di codice usato in genere come payload nell'exploit di una vulnerabilità software.

  - Eseguibile dannoso inserito nella memoria del processo

  Il rilevamento di attacchi senza file genera avvisi di sicurezza dettagliati contenenti le descrizioni con metadati aggiuntivi del processo, ad esempio l'attività di rete. Questa operazione accelera la valutazione degli avvisi, la correlazione e il tempo di risposta downstream. Questo approccio è complementare alle soluzioni EDR basate su eventi e offre una maggiore copertura del rilevamento.

  Per informazioni dettagliate sugli avvisi di rilevamento degli attacchi senza file, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-windows).

- **Avvisi controllati da Linux e integrazione di log Analytics Agent (solo Linux)** : il sistema controllato è costituito da un sottosistema a livello di kernel, responsabile del monitoraggio delle chiamate di sistema. Filtra le chiamate in base a un set di regole specificato e scrive messaggi per le chiamate in un socket. Il Centro sicurezza integra le funzionalità del pacchetto auditd nell'agente di Log Analytics. Questa integrazione consente la raccolta degli eventi auditd in tutte le distribuzioni Linux supportate, senza alcun prerequisito.

    I record auditd vengono raccolti, arricchiti e aggregati in eventi usando l'agente di Log Analytics per l'agente Linux. Il Centro sicurezza aggiunge continuamente nuove analisi, che usano i segnali di Linux per rilevare comportamenti dannosi in computer Linux locali e nel cloud. In modo simile alle funzionalità di Windows, queste analisi abbracciano processi sospetti, tentativi di accesso ambigui, caricamento del modulo kernel e altre attività. Queste attività possono indicare che un computer è sotto attacco o è stato violato.  

    Per un elenco degli avvisi di Linux, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-linux).


## <a name="simulating-alerts"></a>Simulazione degli avvisi

È possibile simulare gli avvisi scaricando uno dei PlayBook seguenti:

- Per Windows: [PlayBook del Centro sicurezza di Azure: avvisi di sicurezza](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf)

- Per Linux: il [PlayBook del Centro sicurezza di Azure: rilevamenti Linux](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf).




## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come Azure Defender per i server. 

Per i materiali correlati, vedere gli articoli seguenti: 

- Che un avviso venga generato dal Centro sicurezza o ricevuto dal Centro sicurezza da un prodotto di sicurezza diverso, è possibile esportarlo. Per esportare gli avvisi in Azure Sentinel, in qualsiasi soluzione SIEM di terze parti o in qualsiasi altro strumento esterno, seguire le istruzioni riportate in [Esportazione di avvisi in un sistema SIEM](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Abilitare Azure Defender](security-center-pricing.md)