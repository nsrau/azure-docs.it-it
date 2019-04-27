---
title: Governance dell'infrastruttura di Azure DevTest Labs
description: Questo articolo include indicazioni per la governance dell'infrastruttura di Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: e02400ef940efdf42370fbdc1da75bdc7062a8ef
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127362"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Governance dell'infrastruttura di Azure DevTest Labs: criteri e conformità aziendali
Questo articolo include indicazioni sulla governance dei criteri e della conformità aziendali dell'infrastruttura di Azure DevTest Labs. 

## <a name="public-vs-private-artifact-repository"></a>Differenza tra repository di artefatti pubblico e privato

### <a name="question"></a>Domanda
Quando è consigliabile che un'organizzazione usi un repository di artefatti pubblico invece di uno privato in DevTest Labs?

### <a name="answer"></a>Risposta
Il [repository di artefatti pubblico](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) offre un set iniziale di pacchetti software di uso più frequente. È utile nella distribuzione rapida perché non richiede che si dedichi tempo a riprodurre gli strumenti di sviluppo e i componenti aggiuntivi di uso comune. È possibile scegliere di distribuire il proprio repository privato. È possibile usare parallelamente un repository pubblico e uno privato. È anche possibile scegliere di disabilitare il repository pubblico. I criteri per distribuire un repository privato dovrebbero basarsi sulle domande e le considerazioni seguenti:

- Un requisito dell'organizzazione è la necessità di avere software con licenza aziendale come parte dell'offerta di DevTest Labs? Se la risposta è affermativa, è consigliabile creare un repository privato.
- L'organizzazione sviluppa software personalizzato che prevede un'operazione specifica, che è essenziale nell'ambito dell'intero processo di provisioning? Se la risposta è affermativa, è consigliabile distribuire un repository privato.
- Se i criteri di governance dell'organizzazione richiedono l'isolamento e i repository esterni non sono sotto la gestione della configurazione diretta dall'organizzazione, è consigliabile distribuire un repository di artefatti privato. Nell'ambito di questo processo è possibile creare una copia del repository pubblico e integrarla nel repository privato. Si può successivamente disabilitare il repository pubblico in modo che nessun altro all'interno dell'organizzazione possa accedervi più. Questo approccio fa in modo che tutti gli utenti all'interno dell'organizzazione dispongano di un singolo repository che è approvato dall'organizzazione e che riduce al minimo la deviazione della configurazione.

### <a name="single-repository-or-multiple-repositories"></a>Repository singolo o più repository 

### <a name="question"></a>Domanda
È consigliabile per un'organizzazione pianificare un singolo repository o consentire più repository?

### <a name="answer"></a>Risposta
Nell'ambito della strategia di gestione della configurazione della governance complessiva dell'organizzazione è consigliabile usare un repository centralizzato. Quando si usano più repository, questi possono diventare con il tempo sili di software non gestito. Con un repository centrale più team possono usare gli artefatti disponibili nel repository per i propri progetti. L'utilizzo di un repository centrale aumenta la standardizzazione, la sicurezza, la facilità di gestione ed elimina la duplicazione dei lavori richiesti. La centralizzazione prevede le azioni seguenti come procedure consigliate per la gestione a lungo termine e la sostenibilità:

- Associare Azure Repos con lo stesso tenant di Azure Active Directory usato dalla sottoscrizione di Azure per l'autenticazione e l'autorizzazione.
- In Azure Active Directory creare un gruppo denominato **Tutti gli sviluppatori di DevTest Labs** che viene gestito centralmente. È opportuno inserire in questo gruppo tutti gli sviluppatori che contribuiscono allo sviluppo degli artefatti.
- Lo stesso gruppo di Azure Active Directory può essere usato per consentire l'accesso al repository di Azure Repos e al lab.
- In Azure Repos è consigliabile usare la funzionalità di branching o di creazione di una copia tramite fork per separare un repository per ambiente di sviluppo dal repository di produzione primario. Il contenuto viene aggiunto al ramo principale solo con una richiesta pull dopo un adeguato esame del codice. Dopo che il revisore del codice approva la modifica, un responsabile sviluppatori, che è responsabile della manutenzione del ramo principale, unisce il codice aggiornato. 

## <a name="corporate-security-policies"></a>Criteri di sicurezza aziendali

### <a name="question"></a>Domanda
In che modo un'organizzazione può garantire l'applicazione di criteri di sicurezza aziendali?

### <a name="answer"></a>Risposta
Un'organizzazione può ottenere questo risultato eseguendo queste azioni:

1. Sviluppo e pubblicazione di criteri di sicurezza completi. I criteri enunciano le regole di utilizzo accettabile associate all'uso di software, asset cloud. Definiscono anche le condizioni che violano i criteri. 
2. Sviluppo di un'immagine personalizzata, di artefatti personalizzati e di un processo di distribuzione che consente l'orchestrazione nel campo della sicurezza definito con Active Directory. Questo approccio applica limiti aziendali e imposta un set comune di controlli nell'ambiente. Questi sono controlli nell'ambiente che uno sviluppatore può prendere in considerazione mentre sviluppa e segue un ciclo di vita di sviluppo sicuro come parte del processo complessivo. L'obiettivo è anche di offrire un ambiente non troppo restrittivo che rischia altrimenti di ostacolare lo sviluppo, bensì di offrire un set ragionevole di controlli. I criteri di gruppo nell'unità organizzativa che contiene macchine virtuali lab possono essere un sottoinsieme dei criteri di gruppo totali che si trovano nell'ambiente di produzione. Possono altrimenti rappresentare un set aggiuntivo per attenuare adeguatamente eventuali rischi identificati.

## <a name="data-integrity"></a>Integrità dei dati

### <a name="question"></a>Domanda
In che modo un'organizzazione può garantire l'integrità dei dati per assicurare che gli sviluppatori in remoto non rimuovano codice o introducano malware o software non approvato?

### <a name="answer"></a>Risposta
Esistono diversi livelli di controllo per attenuare la minaccia che può sorgere da consulenti esterni, terzisti o dipendenti che collaborano in DevTest Labs in remoto. 

Come indicato in precedenza, il primo passaggio deve avere criteri di utilizzo accettabile redatti e definiti che descrivono chiaramente le conseguenze quando un utente li viola. 

Il primo livello di controlli per l'accesso remoto consiste nell'applicare criteri di accesso remoto tramite una connessione VPN che non è direttamente connessa al lab. 

Il secondo livello di controlli consiste nell'applicare un set di oggetti criteri di gruppo che impediscono le operazioni di copia e incolla tramite un desktop remoto. È possibile implementare criteri di rete per non consentire servizi in uscita dall'ambiente, ad esempio i servizi FTP e RDP esterni all'ambiente. Il routing definito dall'utente può fare in modo che tutto il traffico di rete di Azure venga instradato di nuovo in locale, ma il routing può non considerare tutti gli URL che possono consentire il caricamento di dati a meno che non siano controllati tramite proxy per analizzare il contenuto e le sessioni. Gli indirizzi IP pubblici possono essere limitati all'interno della rete virtuale che supporta DevTest Labs per non consentire il bridging di una risorsa di rete esterna.

Lo stesso tipo di limitazioni infine deve essere applicato in tutta l'organizzazione, che dovrebbe anche considerare tutti i possibili metodi di supporti rimovibili o URL esterni in grado di accettare un post di contenuto. Consultare i propri esperti di sicurezza per esaminare e implementare i criteri di sicurezza. Per altre raccomandazioni, vedere il sito sulla [Sicurezza informatica di Microsoft](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).


## <a name="next-steps"></a>Passaggi successivi
Vedere [Migrazione e integrazione delle applicazioni](devtest-lab-guidance-governance-application-migration-integration.md).
