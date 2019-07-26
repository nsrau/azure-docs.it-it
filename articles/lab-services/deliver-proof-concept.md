---
title: Fornire un modello di prova-Azure DevTest Labs | Microsoft Docs
description: Scopri come distribuire un modello di verifica in modo che Azure DevTest Labs possa essere incorporato correttamente in un ambiente aziendale.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: takamath
ms.openlocfilehash: 6739679b1687393737dda1ded4265a95c4fce169
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501546"
---
# <a name="delivering-a-proof-of-concept"></a>Distribuzione di un modello di prova 

Uno degli scenari principali per Azure DevTest Labs è l'abilitazione di ambienti di sviluppo e test nel cloud. Ecco alcuni esempi:

* creazione di desktop per sviluppatori nel cloud
* configurazione degli ambienti per il test
* Abilitazione dell'accesso alle macchine virtuali e ad altre risorse di Azure
* configurazione di un'area sandbox che consente agli sviluppatori di apprendere e sperimentare.

DevTest Labs fornisce inoltre i criteri di Guardrails e dei costi per consentire all'azienda di offrire "self-service Azure" agli sviluppatori che rispettano i criteri di sicurezza, regolamentazione e conformità aziendali. 

Ogni azienda ha requisiti diversi per il modo in cui Azure DevTest Labs possono essere incorporati correttamente nell'ambiente. Questo articolo descrive i passaggi più comuni che in genere devono essere completati per garantire un modello di verifica efficace, ovvero il primo passaggio verso una distribuzione end-to-end corretta. 

## <a name="getting-started"></a>Introduzione 

Per iniziare a consegnare un modello di prova. È importante dedicare del tempo a conoscere Azure e DevTest Labs.  Di seguito sono riportate alcune risorse iniziali: 

* [Informazioni sul portale di Azure](https://azure.microsoft.com/features/azure-portal/)
* [Nozioni di base su DevTest Labs](devtest-lab-overview.md)
* [Scenari supportati di DevTest Labs](devtest-lab-guidance-get-started.md)
* [Documentazione di DevTest Labs Enterprise](devtest-lab-guidance-prescriptive-adoption.md)
* [Introduzione alla rete di Azure](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Prerequisiti 

Per completare correttamente un progetto pilota o un modello di prova con DevTest Labs, è necessario disporre di alcuni prerequisiti: 

* **Sottoscrizione di Azure**: Le aziende dispongono spesso di un [Enterprise Agreement](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) esistente che consente l'accesso ad Azure ed è possibile usare una sottoscrizione esistente o nuova per DevTest Labs. In alternativa, è possibile usare una [sottoscrizione di Visual Studio](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) durante il progetto pilota (usando i crediti Azure gratuiti). Se nessuna di queste opzioni è disponibile, è possibile creare e usare un [account Azure gratuito](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ) . Se è presente una Enterprise Agreement, l'uso di una [sottoscrizione di sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) è un'ottima opzione per ottenere l'accesso ai sistemi operativi client Windows 10/Windows 8.1 e a tariffe scontate per i carichi di lavoro di sviluppo e test. 
* **Tenant Azure Active Directory**:  Per consentire la gestione degli utenti (ad esempio, l'aggiunta di utenti o l'aggiunta di proprietari del Lab), tali utenti devono far parte del [tenant Azure Active Directory](https://azure.microsoft.com/services/active-directory/) usato nella sottoscrizione di Azure per il progetto pilota. Spesso le aziende impostano l' [identità ibrida](../active-directory/hybrid/whatis-hybrid-identity.md) per consentire agli utenti di sfruttare le proprie identità locali nel cloud, ma questa operazione non è necessaria per il progetto pilota di DevTest Labs. 

## <a name="scoping-of-the-pilot"></a>Ambito del progetto pilota 

È molto importante pianificare un progetto pilota appropriato prima di avviare l'implementazione. Sapendo in anticipo che le risorse non rimarranno a tempo indefinito, imposta le aspettative appropriate per gli utenti del progetto pilota. 

> [!IMPORTANT]
> Non è possibile mettere in evidenza l'importanza di definire in anticipo l'ambito del progetto pilota e l'impostazione delle aspettative.

Ci sono domande chiave da rispondere prima di avviare il progetto pilota: 

* Cosa si desidera apprendere e quali sono i risultati positivi per il progetto pilota? 
* Quali carichi di lavoro o scenari verranno trattati nel progetto pilota? È importante definire solo un set ridotto per garantire che il progetto pilota possa essere definito e completato rapidamente. 
* Quali risorse devono essere disponibili nel Lab? Ad esempio: immagini personalizzate, immagini del Marketplace, criteri, topologia di rete e così via. 
* Chi sono gli utenti finali/i team che saranno interessati al programma pilota per verificare l'esperienza?  
* Qual è la durata del progetto pilota? Scegliere un intervallo di tempo che sia allineato correttamente all'ambito pianificato, ad esempio due settimane o un mese. 
* Una volta completato il progetto pilota, cosa accadrà con le risorse allocate usate durante il progetto pilota? Si intende rimuovere le risorse pilota?
   
   Se è possibile pianificare la distribuzione delle macchine virtuali e dei Lab alla fine del progetto pilota, è possibile configurare una singola sottoscrizione per il progetto pilota ed eseguire tutte le operazioni in corso mentre si risolvono le domande di implementazione in parallelo. 

Esiste una tendenza generale a rendere il progetto pilota "perfetto" in modo che rappresenti in modo identico lo stato finale dopo che il servizio è stato implementato nell'azienda. Si tratta di un presupposto falso. Il più vicino possibile è "perfetto", più è necessario completare *prima* di iniziare a usare il progetto pilota. Lo scopo del progetto pilota è prendere le decisioni giuste per la scalabilità verticale e orizzontale del servizio finale. 

L'obiettivo del progetto pilota dovrebbe essere quello di raccogliere i carichi di lavoro e le dipendenze minimi necessari per rispondere alla domanda se Azure DevTest Labs è il servizio giusto per l'azienda. È consigliabile scegliere il carico di lavoro più semplice con le dipendenze minime per garantire un successo più rapido e pulito. Se ciò non è possibile, l'opzione migliore successiva consiste nel selezionare un carico di lavoro più rappresentativo che espone potenziali complessità, in modo che il successo nella fase pilota possa essere replicato nella fase di scalabilità orizzontale. 

Nell'esempio seguente viene illustrato un modello di prova con ambito ben definito. 

## <a name="example-proof-of-concept-plan"></a>Esempio: piano di prova del concetto 

Questa sezione illustra un esempio da usare per la definizione dell'ambito di un modello di prova del progetto pilota per DevTest Labs. 

> [!TIP]
> Per ridurre al minimo la possibilità di configurare il progetto in caso di errore, si consiglia vivamente di non ignorare l'esempio descritto in questa sezione. 

### <a name="overview"></a>Panoramica 

Si prevede di sviluppare un nuovo ambiente in Azure basato su DevTest Labs per i fornitori da usare come ambiente isolato dalla rete aziendale. Per determinare se la soluzione soddisferà i requisiti, si svilupperà un modello di prova per convalidare la soluzione end-to-end e sono stati inclusi diversi fornitori per provare e verificare l'esperienza. 

### <a name="outcomes"></a>Risultati 

Quando si compila un modello di verifica, ci si concentra innanzitutto sui risultati (cosa si sta tentando di ottenere). questi sono elencati qui.  Alla fine del modello di verifica, si prevede quanto segue: 

* Soluzione end-to-end funzionante per i fornitori per sfruttare gli account Guest in Azure Active Directory (Azure AD) per accedere a un ambiente isolato in Azure con le risorse necessarie per la produttività. 
* Tutti i potenziali problemi di blocco che influiscano sull'utilizzo e sull'adozione di scalabilità più ampia vengono enumerati e riconosciuti.
* I singoli utenti interessati allo sviluppo del modello di verifica hanno una conoscenza corretta di tutto il codice. Comprendono anche le garanzie collaterali e sono sicure in un'adozione più ampia.

### <a name="open-questions--prerequisites"></a>Apri domande & prerequisiti 

* È stata creata una sottoscrizione che è possibile usare per questo progetto? 
* Si dispone di un tenant di Azure AD e di un Azure AD amministratore globale identificato che può fornire assistenza & Indicazioni per le domande correlate Azure AD? 
* Una posizione in cui collaborare per gli utenti che lavorano al progetto: 

   * Codice sorgente e script (ad esempio Azure Repos) 
   * Documenti (ad esempio teams o SharePoint)  
   * Conversazioni (ad esempio, Microsoft Teams) 
   * Elementi di lavoro (ad esempio Azure Boards) 
* Quali sono le risorse necessarie per i fornitori? Sono incluse sia localmente nelle macchine virtuali che in altri server necessari, ovvero le applicazioni disponibili in rete. 
* Le macchine virtuali verranno unite a un dominio in Azure? In tal caso, verrà Azure AD Domain Services o qualcos'altro? 
* Sono stati identificati i team/fornitori che saranno la destinazione del modello di prova? Chi saranno i clienti per l'ambiente?
* Quale area di Azure viene usata per il modello di prova? 
* Si dispone di un elenco di servizi che i fornitori possono usare tramite DevTest Labs oltre a IaaS (VM)? 
* In che modo è possibile pianificare il training di fornitori/utenti nell'uso del Lab? 

### <a name="proof-of-concept-solution-components"></a>Componenti della soluzione del modello di verifica 

Si prevede che la soluzione disponga dei componenti seguenti: 

* Set di DevTest Labs in Azure per diversi team di fornitori.
* I Lab sono connessi a un'infrastruttura di rete che supporta i requisiti.
* I fornitori possono accedere ai laboratori tramite Azure AD e concedere le assegnazioni di ruolo al Lab.
* Un modo per i fornitori di connettersi correttamente alle risorse. In particolare, una VPN da sito a sito per consentire l'accesso diretto alle macchine virtuali senza indirizzi IP pubblici.
* Set di elementi che coprono il software necessario per i fornitori delle macchine virtuali.

## <a name="additional-planning-and-design-decisions"></a>Decisioni aggiuntive sulla pianificazione e sulla progettazione 

Prima di rilasciare una soluzione DevTest Labs completa, è necessario prendere alcune decisioni importanti di pianificazione e progettazione. L'esperienza di utilizzo di un modello di prova può aiutarti a prendere queste decisioni. Ulteriori considerazioni includono: 

* **Topologia di sottoscrizione**: I requisiti di livello aziendale per le risorse in Azure possono estendersi oltre le [quote disponibili all'interno di una singola sottoscrizione](https://docs.microsoft.com/azure/azure-subscription-service-limits), che richiede più sottoscrizioni e/o richieste di servizio di Azure per aumentare i limiti di sottoscrizione iniziali. È importante decidere come distribuire le risorse tra le sottoscrizioni, una risorsa preziosa è la [Guida alla decisione relativa alla sottoscrizione](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) poiché è difficile spostare le risorse in un'altra sottoscrizione in un secondo momento. Un Lab DevTest, ad esempio, non può essere spostato in un'altra sottoscrizione dopo che è stato creato.  
* **Topologia di rete**: L' [infrastruttura di rete predefinita](../app-service/networking-features.md) creata automaticamente da DevTest Labs potrebbe non essere sufficiente a soddisfare i requisiti e i vincoli per gli utenti aziendali. È comune vedere [ExpressRoute connesso reti virtuali](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/), [Hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) per la connettività tra sottoscrizioni e anche il [routing forzato](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) garantendo solo la connettività locale. DevTest Labs consente di connettere le reti virtuali esistenti al Lab per consentirne l'uso durante la creazione di nuove macchine virtuali nel Lab. 
* **Accesso remoto delle macchine virtuali**: Sono disponibili molte opzioni per accedere in modalità remota alle macchine virtuali disponibili in DevTest Labs. Il modo più semplice consiste nell'usare indirizzi IP pubblici o indirizzi IP pubblici condivisi. si tratta di [impostazioni disponibili nel Lab](devtest-lab-shared-ip.md). Se queste opzioni non sono sufficienti, l'uso di un gateway di accesso remoto è anche un'opzione come illustrato nell' [architettura di riferimento di DevTest Labs Enterprise](devtest-lab-reference-architecture.md) e descritta ulteriormente nella [documentazione di Gateway Desktop remoto di DevTest Labs](configure-lab-remote-desktop-gateway.md). Le aziende possono anche usare Express route o una VPN da sito a sito per connettere i DevTest Lab alla propria rete locale. In questo modo, le connessioni dirette a desktop remoto o SSH alle macchine virtuali vengono abilitate in base all'indirizzo IP privato senza alcuna esposizione a Internet. 
* **Gestione delle autorizzazioni**: Le due autorizzazioni chiave comunemente usate in DevTest Labs sono ["Owner" e "Lab User"](devtest-lab-add-devtest-user.md). È importante decidere prima di implementare DevTest Labs su vasta scala, che verrà considerato attendibile per ogni livello di accesso nel Lab. Un modello comune è il proprietario del budget (ad esempio, il responsabile del team) come proprietario del Lab e i membri del team come utenti del Lab. In questo modo, la persona (responsabile del team) responsabile del budget per modificare le impostazioni dei criteri per tenere il team entro il budget.  

## <a name="completing-the-proof-of-concept"></a>Completamento del modello di verifica 

Una volta che le approvazioni previste sono state analizzate, è giunto il momento di completare il progetto pilota. Questo è il momento per raccogliere commenti e suggerimenti degli utenti, determinare se il progetto pilota ha avuto esito positivo e decidere se l'organizzazione andrà avanti su una scala di distribuzione di DevTest Labs nell'azienda. È anche opportuno prendere in considerazione l'automazione della distribuzione di DevTest Labs e delle risorse associate per garantire la coerenza durante l'implementazione della scalabilità. 

## <a name="next-steps"></a>Fasi successive 

* [Documentazione di DevTest Labs Enterprise](devtest-lab-guidance-prescriptive-adoption.md)
* [Architettura di riferimento per un'organizzazione](devtest-lab-reference-architecture.md)
* [Scalabilità verticale della distribuzione di DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
* [Orchestrare l'implementazione di Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
