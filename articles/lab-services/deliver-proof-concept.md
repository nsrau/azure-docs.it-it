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
ms.openlocfilehash: 13e3f6be851e81b1186d55bb313dd23f1920f007
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616361"
---
# <a name="deliver-a-proof-of-concept"></a>Fornire un modello di prova 

Uno degli scenari principali per Azure DevTest Labs è l'abilitazione di ambienti di sviluppo e test nel cloud. Ecco alcuni esempi:

* Creazione di desktop per sviluppatori nel cloud.
* Configurazione degli ambienti per il test.
* Consentire l'accesso alle macchine virtuali e ad altre risorse di Azure.
* Configurazione di un'area sandbox che consente agli sviluppatori di apprendere e sperimentare.

DevTest Labs fornisce inoltre i criteri di Guardrails e dei costi per consentire all'azienda di offrire "self-service Azure" agli sviluppatori che rispettano i criteri di sicurezza, regolamentazione e conformità aziendali. 

Ogni azienda ha requisiti diversi per il modo in cui Azure DevTest Labs possono essere incorporati correttamente nell'ambiente. Questo articolo descrive i passaggi più comuni che le aziende devono completare per garantire un modello di verifica efficace. Un modello di prova è il primo passo verso una distribuzione end-to-end corretta. 

## <a name="getting-started"></a>Introduzione 

Per iniziare a consegnare un modello di prova. È importante dedicare del tempo a conoscere Azure e DevTest Labs.  Di seguito sono riportate alcune risorse iniziali: 

* [Informazioni sul portale di Azure](https://azure.microsoft.com/features/azure-portal/)
* [Nozioni di base su DevTest Labs](devtest-lab-overview.md)
* [Scenari supportati di DevTest Labs](devtest-lab-guidance-get-started.md)
* [Documentazione di DevTest Labs Enterprise](devtest-lab-guidance-prescriptive-adoption.md)
* [Introduzione alla rete di Azure](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Prerequisiti 

Per completare correttamente un progetto pilota o un modello di prova con DevTest Labs, esistono alcuni prerequisiti: 

* **Sottoscrizione di Azure**: Le aziende hanno spesso un [Enterprise Agreement](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) esistente che consente l'accesso ad Azure e possono usare una sottoscrizione esistente o nuova per DevTest Labs. In alternativa, le aziende possono usare una [sottoscrizione di Visual Studio](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) durante il progetto pilota, sfruttando i vantaggi offerti dai crediti Azure gratuiti. Se nessuna di queste opzioni è disponibile, un'azienda può creare e usare un [account Azure gratuito](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ). Se è presente una Enterprise Agreement, l'uso di una [sottoscrizione di sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) è un'ottima opzione per ottenere l'accesso ai sistemi operativi client Windows 10/Windows 8.1 e a tariffe scontate per i carichi di lavoro di sviluppo e test. 
* **Tenant Azure Active Directory**: Per consentire la gestione degli utenti (ad esempio, l'aggiunta di utenti o l'aggiunta di proprietari del Lab), tali utenti devono far parte del [tenant Azure Active Directory](https://azure.microsoft.com/services/active-directory/) usato nella sottoscrizione di Azure per il progetto pilota. Spesso le aziende impostano l' [identità ibrida](../active-directory/hybrid/whatis-hybrid-identity.md) per consentire agli utenti di usare l'identità locale nel cloud, ma questa operazione non è necessaria per il progetto pilota di DevTest Labs. 

## <a name="scoping-of-the-pilot"></a>Ambito del progetto pilota 

È importante pianificare un progetto pilota prima di avviare l'implementazione. Sapere in anticipo che le risorse non saranno in grado di impostare indefinitamente le aspettative appropriate per gli utenti del progetto pilota. 

> [!IMPORTANT]
> Non è possibile evidenziare abbastanza l'importanza di definire in anticipo l'ambito del progetto pilota e impostare le aspettative.

Rispondere a queste domande chiave prima di avviare il progetto pilota: 

* Che cosa si desidera apprendere e quali sono i risultati positivi per il progetto pilota? 
* Quali carichi di lavoro o scenari verranno trattati nel progetto pilota? È importante definire solo un set ridotto per garantire che il progetto pilota possa essere definito e completato rapidamente. 
* Quali risorse devono essere disponibili nel Lab? Ad esempio: immagini personalizzate, immagini del Marketplace, criteri e topologia di rete. 
* Chi sono gli utenti e i team che saranno interessati al programma pilota per verificare l'esperienza?  
* Qual è la durata del progetto pilota? Scegliere un intervallo di tempo che sia allineato correttamente all'ambito pianificato, ad esempio due settimane o un mese. 
* Al termine del progetto pilota, cosa accadrà con le risorse allocate usate durante il progetto pilota? Si intende rimuovere le risorse pilota? Si potrebbe pensare:
   
   "Se possiamo pianificare il lancio delle macchine virtuali e dei laboratori alla fine del progetto pilota, possiamo impostare una singola sottoscrizione per il progetto pilota ed eseguire tutto il lavoro in questa fase, mentre stiamo risolvendo le domande sull'implementazione della scalabilità in parallelo". 

Esiste una tendenza generale a rendere il progetto pilota "perfetto" in modo che rappresenti in modo identico lo stato finale dopo che il servizio è stato implementato nell'azienda. Si tratta di un presupposto falso. Più ci si avvicina a "perfect" (perfetto), più è necessario completare *prima* di iniziare a usare il progetto pilota. Lo scopo del progetto pilota è prendere le decisioni giuste per la scalabilità verticale e orizzontale del servizio finale. 

L'obiettivo del progetto pilota dovrebbe essere quello di raccogliere i carichi di lavoro e le dipendenze minimi necessari per rispondere alla domanda se Azure DevTest Labs è il servizio giusto per l'azienda. Si consiglia di scegliere il carico di lavoro più semplice con le dipendenze minime per garantire un successo rapido e pulito. Se ciò non è possibile, scegliere un carico di lavoro più rappresentativo che espone potenziali complessità, in modo che l'esito positivo della fase pilota possa essere replicato nella fase di scalabilità orizzontale. 

Nell'esempio seguente viene illustrato un modello di prova con ambito ben definito. 

## <a name="example-proof-of-concept-plan"></a>Esempio: piano di prova del concetto 

Questa sezione illustra un esempio da usare per la definizione dell'ambito di un modello di prova del progetto pilota per DevTest Labs. 

> [!TIP]
> Per ridurre al minimo la possibilità di configurare il progetto in caso di errore, si consiglia vivamente di non ignorare l'esempio descritto in questa sezione. 

### <a name="overview"></a>Panoramica 

Stiamo pianificando di sviluppare un nuovo ambiente in Azure basato su DevTest Labs per i fornitori da usare come ambiente isolato dalla rete aziendale. Per determinare se la soluzione soddisferà i requisiti, verrà sviluppato un modello di prova per convalidare la soluzione end-to-end. Sono stati inclusi diversi fornitori per provare a verificarne l'esperienza. 

### <a name="outcomes"></a>Risultati 

Quando si compila un modello di verifica, ci si concentra innanzitutto sui risultati (cosa si sta tentando di ottenere). Alla fine del modello di verifica, si prevede quanto segue: 

* Soluzione end-to-end funzionante per i fornitori per l'uso di account Guest in Azure Active Directory (Azure AD) per accedere a un ambiente isolato in Azure. L'ambiente contiene le risorse necessarie per la produttività. 
* Tutti i potenziali problemi di blocco che interessano l'utilizzo e l'adozione di una scala più ampia vengono enumerati e riconosciuti.
* I singoli utenti interessati allo sviluppo del modello di verifica hanno una conoscenza corretta di tutto il codice. Comprendono anche le garanzie collaterali e sono sicure in un'adozione più ampia.

### <a name="open-questions-and-prerequisites"></a>Domande e prerequisiti aperti 

* È stata creata una sottoscrizione che è possibile usare per questo progetto? 
* Si dispone di un tenant di Azure AD e di un Azure AD amministratore globale identificato che può fornire assistenza e istruzioni per domande relative a Azure AD? 
* Esiste una posizione in cui collaborare per le persone che lavorano al progetto? 

   * Codice sorgente e script (ad esempio Azure Repos) 
   * Documenti (ad esempio, Microsoft teams o SharePoint)  
   * Conversazioni (ad esempio, Microsoft Teams) 
   * Elementi di lavoro (ad esempio Azure Boards) 
* Quali sono le risorse necessarie per i fornitori? Sono incluse le applicazioni disponibili in rete, in locale nelle macchine virtuali e in altri server richiesti. 
* Le macchine virtuali verranno unite a un dominio in Azure? In tal caso, verrà Azure Active Directory Domain Services (Azure AD DS) o altri elementi? 
* Abbiamo identificato il team o i fornitori che saranno la destinazione del modello di prova? Chi saranno i clienti per l'ambiente?
* Quale area di Azure viene usata per il modello di prova? 
* Si dispone di un elenco di servizi che i fornitori possono usare tramite DevTest Labs oltre a IaaS (VM)? 
* Come si prevede di eseguire il training di fornitori/utenti nell'uso del Lab? 

### <a name="components-of-the-proof-of-concept-solution"></a>Componenti della soluzione di modello di prova 

Si prevede che la soluzione disponga dei componenti seguenti: 

* Diversi team di fornitori utilizzeranno un set di Lab in Azure.
* I Lab sono connessi a un'infrastruttura di rete che supporta i requisiti.
* I fornitori possono accedere ai Lab tramite Azure AD e assegnazioni di ruolo.
* I fornitori hanno la possibilità di connettersi alle risorse. In particolare, una VPN da sito a sito consente di accedere direttamente alle macchine virtuali senza indirizzi IP pubblici.
* Un set di elementi copre il software necessario per i fornitori delle macchine virtuali.

## <a name="additional-planning-and-design-decisions"></a>Decisioni aggiuntive sulla pianificazione e sulla progettazione 

Prima di rilasciare una soluzione DevTest Labs completa, è necessario prendere alcune importanti decisioni di pianificazione e progettazione. L'esperienza di utilizzo di un modello di prova può aiutarti a prendere queste decisioni. Ulteriori considerazioni includono: 

* **Topologia di sottoscrizione**: I requisiti di livello aziendale per le risorse in Azure possono estendersi oltre le [quote disponibili all'interno di una singola sottoscrizione](https://docs.microsoft.com/azure/azure-subscription-service-limits). Questa operazione richiede più sottoscrizioni e/o richieste di servizio di Azure per aumentare i limiti di sottoscrizione iniziali. È importante decidere prima come distribuire le risorse tra le sottoscrizioni. Una risorsa preziosa è la [Guida alla decisione relativa alla sottoscrizione](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) perché è difficile spostare le risorse in un'altra sottoscrizione in un secondo momento. Un Lab, ad esempio, non può essere spostato in un'altra sottoscrizione dopo che è stato creato.  
* **Topologia di rete**: L' [infrastruttura di rete predefinita](../app-service/networking-features.md) creata automaticamente da DevTest Labs potrebbe non essere sufficiente a soddisfare i requisiti e i vincoli per gli utenti aziendali. È comune vedere [reti virtuali connesse di Azure ExpressRoute](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/), [Hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) per la connettività tra sottoscrizioni e anche il [routing forzato](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) per garantire solo la connettività locale. DevTest Labs consente di connettere le reti virtuali esistenti al Lab per abilitarne l'uso quando si creano nuove macchine virtuali nel Lab. 
* **Accesso remoto delle macchine virtuali**: Sono disponibili molte opzioni per accedere in modalità remota alle macchine virtuali disponibili in DevTest Labs. Il modo più semplice consiste nell'usare indirizzi IP pubblici o indirizzi IP pubblici condivisi. Si tratta [di impostazioni disponibili nel Lab](devtest-lab-shared-ip.md). Se queste opzioni non sono sufficienti, è anche possibile usare un gateway di accesso remoto. Questa opzione è illustrata nell' [architettura di riferimento di DevTest Labs Enterprise](devtest-lab-reference-architecture.md) ed è descritta più avanti nella [documentazione di Gateway Desktop remoto di DevTest Labs](configure-lab-remote-desktop-gateway.md). Le aziende possono anche usare ExpressRoute o una VPN da sito a sito per connettere i propri Lab alla rete locale. Questa opzione Abilita le connessioni dirette desktop remoto o SSH alle macchine virtuali in base all'indirizzo IP privato senza esposizione a Internet. 
* **Gestione delle autorizzazioni**: Le due autorizzazioni chiave comunemente usate in DevTest Labs sono [utente proprietario e Lab](devtest-lab-add-devtest-user.md). È importante decidere prima di implementare DevTest Labs su vasta scala, che verrà considerato attendibile per ogni livello di accesso nel Lab. Un modello comune è il proprietario del budget (ad esempio, il responsabile del team) come proprietario del Lab e i membri del team come utenti del Lab. Questo modello consente alla persona (responsabile del team) responsabile del budget di modificare le impostazioni dei criteri e di tenere il team entro il budget.  

## <a name="completing-the-proof-of-concept"></a>Completamento del modello di verifica 

Una volta analizzate le informazioni previste, è possibile completare il progetto pilota. Si tratta del tempo necessario per raccogliere commenti e suggerimenti dagli utenti, determinare se il progetto pilota ha avuto esito positivo e decidere se l'organizzazione andrà avanti in un'implementazione con scalabilità di DevTest Labs nell'azienda. È anche opportuno prendere in considerazione l'automazione della distribuzione di DevTest Labs e delle risorse associate per garantire la coerenza durante l'implementazione della scalabilità. 

## <a name="next-steps"></a>Passaggi successivi 

* [Documentazione di DevTest Labs Enterprise](devtest-lab-guidance-prescriptive-adoption.md)
* [Architettura di riferimento per un'organizzazione](devtest-lab-reference-architecture.md)
* [Scalabilità verticale della distribuzione di DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
* [Orchestrare l'implementazione di Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
