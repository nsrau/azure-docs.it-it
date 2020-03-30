---
title: Offrire una prova di concetto - Azure DevTest Labs Documenti Microsoft
description: Informazioni su come fornire una prova di concetto in modo che Azure DevTest Labs possa essere incorporato correttamente in un ambiente aziendale.
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
ms.openlocfilehash: ca843213760cee60799568a6f33059c2bd91c835
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75643288"
---
# <a name="deliver-a-proof-of-concept"></a>Offrire una prova di concetto 

Uno degli scenari principali per Azure DevTest Labs è l'abilitazione di ambienti di sviluppo e test nel cloud. Tra gli esempi sono inclusi:

* Creazione di desktop per sviluppatori nel cloud.
* Configurazione degli ambienti per il test.
* Abilitazione dell'accesso alle macchine virtuali e ad altre risorse di Azure.Enabling access to virtual machines and other Azure resources.
* Configurazione di un'area sandbox per gli sviluppatori da apprendere e sperimentare.

DevTest Labs fornisce inoltre i guardrail dei criteri e i controlli dei costi per consentire all'azienda di fornire "Azure self-service" agli sviluppatori che aderiscono ai criteri di sicurezza, normativi e di conformità aziendali. 

Ogni azienda ha requisiti diversi per il modo in cui Azure DevTest Labs può essere incorporato correttamente nel proprio ambiente. In questo articolo vengono descritti i passaggi più comuni che le aziende devono completare per garantire una prova di concetto di successo. Una prova di concetto è il primo passo verso una corretta distribuzione end-to-end. 

## <a name="getting-started"></a>Introduzione 

Per iniziare a fornire una prova di concetto. È importante dedicare un po' di tempo per conoscere Azure e DevTest Labs.  Ecco alcune risorse iniziali: 

* [Informazioni sul portale di AzureUnderstanding the Azure portal](https://azure.microsoft.com/features/azure-portal/)
* [Nozioni di base su DevTest Labs](devtest-lab-overview.md)
* [Scenari supportati da DevTest Labs](devtest-lab-guidance-get-started.md)
* [Documentazione aziendale di DevTest Labs](devtest-lab-guidance-prescriptive-adoption.md)
* [Introduzione alla rete di AzureIntro to Azure networking](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Prerequisiti 

Per completare correttamente un progetto pilota o di un concetto di prova con DevTest Labs, esistono alcuni prerequisiti:To successfully complete a pilot or proof of concept with DevTest Labs, there are a few prerequisites: 

* **Sottoscrizione di Azure:** le aziende hanno spesso un [contratto Enterprise Agreement](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) esistente che consente l'accesso ad Azure e possono usare una sottoscrizione nuova o esistente per DevTest Labs.Azure subscription : Enterprises often have an existing Enterprise Agreement in that enables access to Azure, and they can use an existing or new subscription for DevTest Labs. In alternativa, le aziende possono usare una sottoscrizione di [Visual Studio](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) durante il progetto pilota (sfruttando i crediti gratuiti di Azure). Se nessuna di queste opzioni è disponibile, un'azienda può creare e usare un [account Azure gratuito.](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ) Se è presente un contratto Enterprise Agreement, l'uso di una [sottoscrizione Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) è un'ottima opzione per ottenere l'accesso ai sistemi operativi client Windows 10/Windows 8.1 e alle tariffe scontate per i carichi di lavoro di sviluppo e test. 
* Tenant di **Azure Active Directory:** per abilitare la gestione degli utenti (ad esempio, l'aggiunta di utenti o l'aggiunta di proprietari di lab), tali utenti devono far parte del tenant di [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) usato nella sottoscrizione di Azure per il progetto pilota. Spesso le aziende configurano [l'identità ibrida](../active-directory/hybrid/whatis-hybrid-identity.md) per consentire agli utenti di usare l'identità locale nel cloud, ma questa operazione non è necessaria per il progetto pilota DevTest Labs. 

## <a name="scoping-of-the-pilot"></a>Definizione del progetto pilota 

È importante pianificare un progetto pilota prima di iniziare l'implementazione. Sapere in anticipo che le risorse non rimarranno a tempo indeterminato imposta aspettative appropriate per gli utenti del progetto pilota. 

> [!IMPORTANT]
> Non siamo riusciti a trovarti abbastanza velocemente!

Rispondi a queste domande chiave prima di dare il via al pilota: 

* Cosa vuoi imparare e che aspetto ha il successo per il pilota? 
* Quali carichi di lavoro o scenari verranno trattati nel progetto pilota? È importante definire solo un piccolo set per garantire che l'ambito del pilota possa essere definito e completato rapidamente. 
* Quali risorse devono essere disponibili in laboratorio? Ad esempio: immagini personalizzate, immagini del marketplace, criteri, topologia di rete. 
* Chi sono gli utenti e i team che saranno coinvolti nel progetto pilota per verificare l'esperienza?  
* Qual è la durata del pilota? Scegliere un intervallo di tempo che si allinei bene all'ambito pianificato, ad esempio due settimane o un mese. 
* Al termine del progetto pilota, cosa accadrà con le risorse allocate utilizzate durante il progetto pilota? Avete intenzione di scartare le risorse pilota? Si potrebbe pensare:
   
   "Se possiamo pianificare di buttare via le macchine virtuali e i laboratori alla fine del progetto pilota, possiamo configurare un unico abbonamento per il progetto pilota ed eseguire tutto il nostro lavoro mentre risolviamo le domande di implementazione della scalabilità in parallelo." 

C'è una tendenza generale a rendere il pilota "perfetto" in modo che rappresenti in modo identico ciò che lo stato finale sarà dopo il servizio è stato implementato presso l'azienda. Questa è una falsa supposizione. Più ci si avvicina a "perfetto", più si deve completare *prima* di iniziare il pilota. Lo scopo del progetto pilota è quello di prendere le decisioni giuste sulla scalabilità verticale e l'implementazione del servizio finale. 

Il progetto pilota deve essere incentrato sulla scelta dei carichi di lavoro e delle dipendenze minimi necessari per rispondere alla domanda se Azure DevTest Labs è il servizio giusto per l'azienda. È consigliabile scegliere il carico di lavoro più semplice con le dipendenze minime per garantire un successo rapido e pulito. Se ciò non è possibile, scegliere un carico di lavoro più rappresentativo che esponga potenziali complessità in modo che il successo nella fase pilota possa essere replicato nella fase di scalabilità orizzontale. 

Nell'esempio seguente viene illustrato un concetto di prova con ambito ben definito. 

## <a name="example-proof-of-concept-plan"></a>Esempio: piano proof-of-concept 

In questa sezione viene illustrato un esempio da usare per l'ambito di un modello di prova del progetto pilota per DevTest Labs. 

> [!TIP]
> Per ridurre al minimo la possibilità di impostare il progetto per l'errore, è consigliabile non ignorare l'esempio descritto in questa sezione. 

### <a name="overview"></a>Panoramica 

Stiamo pianificando di sviluppare un nuovo ambiente in Azure basato su DevTest Labs per i fornitori da usare come ambiente isolato dalla rete aziendale. Per determinare se la soluzione soddisferà i requisiti, svilupperemo una prova di concetto per convalidare la soluzione end-to-end. Abbiamo incluso diversi fornitori per provare e verificare l'esperienza. 

### <a name="outcomes"></a>Risultati 

Quando costruiamo una prova di concetto, ci concentriamo prima sui risultati (cosa stiamo cercando di raggiungere). Entro la fine del concetto, ci aspettiamo: 

* A working end-to-end solution for vendors to use guest accounts in Azure Active Directory (Azure AD) to access an isolated environment in Azure. L'ambiente dispone delle risorse necessarie per essere produttive. 
* Tutti i potenziali problemi di blocco che influiscono sull'uso e sull'adozione di scalabilità più ampia vengono enumerati e compresi.
* Le persone coinvolte nello sviluppo della prova di concetto hanno una buona comprensione di tutto il codice. Comprendono anche le garanzie e sono fiduciosi in un'adozione più ampia.

### <a name="open-questions-and-prerequisites"></a>Domande aperte e prerequisiti 

* Abbiamo una sottoscrizione creata che possiamo usare per questo progetto? 
* Sono stati identificati un tenant di Azure AD e un amministratore globale di Azure AD che può fornire assistenza e indicazioni per le domande correlate ad Azure AD? 
* Abbiamo un posto dove collaborare per le persone che lavorano al progetto? 

   * Codice sorgente e script (ad esempio Azure Repos)Source code and scripts (like Azure Repos) 
   * Documenti (come Microsoft Teams o SharePoint)  
   * Conversazioni (come Microsoft Teams) 
   * Elementi di lavoro (ad esempio Bacheche di Azure)Work items (like Azure Boards) 
* Quali sono le risorse necessarie per i fornitori? Sono incluse le applicazioni disponibili nella rete, sia localmente nelle macchine virtuali che in altri server necessari. 
* Le macchine virtuali verranno aggiunte a un dominio in Azure? In caso affermativo, si tratterà di Servizi di dominio Azure Active Directory (Azure AD DS) o di qualcos'altro? 
* Abbiamo identificato il team o i fornitori che saranno l'obiettivo della prova di concetto? Chi saranno i clienti per l'ambiente?
* Quale area di Azure verrà usata per la prova del concetto? 
* È disponibile un elenco di servizi che i fornitori possono utilizzare tramite DevTest Labs oltre a IaaS (VMs)? 
* Come pensiamo di formare fornitori/utenti sull'utilizzo del laboratorio? 

### <a name="components-of-the-proof-of-concept-solution"></a>Componenti della soluzione proof-of-concept 

Ci aspettiamo che la soluzione abbia i seguenti componenti: 

* Diversi team di fornitori utilizzeranno un set di lab in Azure.Various vendor teams will use a set of labs in Azure.
* I lab sono connessi a un'infrastruttura di rete che supporta i requisiti.
* I fornitori hanno accesso ai lab tramite Azure AD e le assegnazioni di ruolo.
* I fornitori hanno un modo per connettersi con successo alle loro risorse. In particolare, una VPN da sito a sito consente l'accesso alle macchine virtuali direttamente senza indirizzi IP pubblici.
* Un set di elementi copre il software necessario necessario per le macchine virtuali.

## <a name="additional-planning-and-design-decisions"></a>Ulteriori decisioni di pianificazione e progettazione 

Prima di rilasciare una soluzione DevTest Labs completa, è necessario prendere alcune importanti decisioni di pianificazione e progettazione. L'esperienza di lavorare su una prova di concetto può aiutarti a prendere queste decisioni. Un'ulteriore considerazione comprende: 

* **Topologia di sottoscrizione:** i requisiti a livello aziendale per le risorse in Azure possono estendersi oltre [le quote disponibili all'interno](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)di una singola sottoscrizione. Ciò richiede più sottoscrizioni di Azure e/o richieste di servizio per aumentare i limiti di sottoscrizione iniziale. È importante decidere in anticipo come distribuire le risorse tra le sottoscrizioni. Una risorsa importante è la [guida alle decisioni](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) relative alla sottoscrizione perché è difficile spostare le risorse in un'altra sottoscrizione in un secondo momento. Ad esempio, un lab non può essere spostato in un'altra sottoscrizione dopo averlo creato.  
* **Topologia di rete:** [l'infrastruttura](../app-service/networking-features.md) di rete predefinita creata automaticamente da DevTest Labs potrebbe non essere sufficiente per soddisfare i requisiti e i vincoli per gli utenti aziendali. È comune vedere [le reti virtuali connesse](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)di Azure ExpressRoute , [hub-and-spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) per la connettività tra sottoscrizioni e persino [il routing forzato](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) per garantire solo la connettività locale. DevTest Labs consente alle reti virtuali esistenti di essere connesse al lab per abilitarne l'uso durante la creazione di nuove macchine virtuali nel lab. 
* **Accesso remoto alle macchine virtuali:** sono disponibili molte opzioni per accedere in remoto alle macchine virtuali situate in DevTest Labs. Il più semplice consiste nell'utilizzare indirizzi IP pubblici o indirizzi IP pubblici condivisi. Queste sono [le impostazioni disponibili nel laboratorio](devtest-lab-shared-ip.md). Se queste opzioni non sono sufficienti, anche l'utilizzo di un gateway di accesso remoto è un'opzione. Questa opzione viene illustrata nell'architettura di [riferimento aziendale DevTest Labs](devtest-lab-reference-architecture.md) e descritta più avanti nella documentazione sul gateway desktop remoto [DevTest Labs](configure-lab-remote-desktop-gateway.md). Le aziende possono anche usare ExpressRoute o una VPN da sito a sito per connettere i laboratori alla rete locale. Questa opzione abilita connessioni remote desktop o SSH dirette alle macchine virtuali in base al relativo indirizzo IP privato senza esposizione a Internet. 
* **Gestione delle autorizzazioni**: le due autorizzazioni chiave comunemente utilizzate in DevTest Labs sono [Proprietario e Utente lab](devtest-lab-add-devtest-user.md). È importante decidere prima di implementare DevTest Labs in generale a cui verrà affidato ogni livello di accesso nel lab. Un modello comune è il proprietario del budget (ad esempio, il responsabile del team) come proprietario del lab e i membri del team come utenti del lab. Questo modello consente alla persona (responsabile del team) responsabile del budget di modificare le impostazioni dei criteri e mantenere il team entro il budget.  

## <a name="completing-the-proof-of-concept"></a>Completamento della prova di concetto 

Dopo che gli apprendimenti previsti sono stati coperti, è il momento di completare il progetto pilota. Questo è il momento di raccogliere feedback dagli utenti, determinare se il progetto pilota ha avuto esito positivo e decidere se l'organizzazione avanzerà su una scala di implementazione di DevTest Labs nell'organizzazione. È anche un ottimo momento per valutare l'automazione della distribuzione di DevTest Labs e delle risorse associate per garantire la coerenza in tutta l'implementazione della scalabilità. 

## <a name="next-steps"></a>Passaggi successivi 

* [Documentazione aziendale di DevTest Labs](devtest-lab-guidance-prescriptive-adoption.md)
* [Architettura di riferimento per un'azienda](devtest-lab-reference-architecture.md)
* [Scalabilità verticale della distribuzione di DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
* [Orchestrare l'implementazione di Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
