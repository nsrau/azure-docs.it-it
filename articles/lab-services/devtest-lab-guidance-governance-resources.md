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
ms.date: 10/03/2018
ms.author: spelluru
ms.openlocfilehash: 7835674536c43fcb694ad8eaeb18aebb89de5fad
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52262075"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Governance dell'infrastruttura di Azure DevTest Labs - Risorse
Questo articolo descrive l'allineamento e la gestione delle risorse di DevTest Labs all'interno dell'organizzazione. 

## <a name="align-within-an-azure-subscription"></a>Allineare all'interno di una sottoscrizione di Azure 

### <a name="question"></a>Domanda
Come si esegue l'allineamento delle risorse di DevTest Labs all'interno di una sottoscrizione di Azure?

### <a name="answer"></a>Risposta
Prima che un'organizzazione inizi a usare Azure per lo sviluppo generico di applicazioni, i responsabili della pianificazione IT devono rivedere la procedura per introdurre la funzionalità nell'ambito del loro portfolio globale di servizi. Le aree per la revisione devono riguardare gli aspetti seguenti:

- Come misurare il costo associato al ciclo di sviluppo dell'applicazione?
- In che modo l'organizzazione allinea l'offerta di servizio proposta ai criteri di sicurezza aziendali? 
- È necessaria la segmentazione per separare gli ambienti di sviluppo e produzione? 
- Quali controlli vengono introdotti per semplificare a lungo termine la gestione, la stabilità e la crescita?

La **prima procedura consigliata** consiste nell'esaminare la tassonomia Azure delle organizzazioni, in cui vengono definite le divisioni tra le sottoscrizioni di sviluppo e produzione. Nel diagramma seguente la tassonomia consigliata consente una separazione logica degli ambienti di sviluppo/test e produzione. Con questo approccio, un'organizzazione può introdurre codici di fatturazione per tenere traccia dei costi associati a ogni ambiente separatamente. Per altre informazioni, vedere [Governance prescrittiva per le sottoscrizioni](/azure/architecture/cloud-adoption/appendix/azure-scaffold). È anche possibile usare [tag di Azure](../azure-resource-manager/resource-group-using-tags.md) per organizzare le risorse ai fini di monitoraggio e fatturazione.

La **seconda procedura consigliata** consiste nell'abilitare la sottoscrizione di DevTest nel portale di Azure Enterprise. Consente a un'organizzazione di eseguire i sistemi operativi client che non sono in genere disponibili in una sottoscrizione di Azure Enterprise. Usare quindi il software aziendale, con pagamento per le sole risorse di calcolo, senza preoccuparsi delle licenze. In questo modo la fatturazione per i servizi designati, incluse le immagini delle raccolte in IaaS, ad esempio Microsoft SQL Server, è basata esclusivamente sulle risorse consumate. Sono disponibili informazioni dettagliate sulla sottoscrizione di Azure DevTest [qui](https://azure.microsoft.com/offers/ms-azr-0148p/) per i clienti Enterprise Agreement (EA) e [qui](https://azure.microsoft.com/offers/ms-azr-0023p/) per i clienti con pagamento in base al consumo.

![Allineamento di risorse alle sottoscrizioni](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Questo modello conferisce a un'organizzazione la flessibilità per distribuire Azure DevTest Labs su larga scala. Un'organizzazione può supportare centinaia di lab per varie business unit con 100-1000 macchine virtuali in esecuzione in parallelo. Eleva la nozione di una soluzione lab aziendale centralizzata in grado di condividere gli stessi principi di gestione della configurazione e i controlli di sicurezza.

Questo modello garantisce anche che l'organizzazione non raggiunga i limiti definiti per le risorse associate alla propria sottoscrizione di Azure. Per informazioni dettagliate sui limiti della sottoscrizione e dei servizi, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md). Il processo di provisioning di DevTest Labs può usare un numero elevato di gruppi di risorse. È possibile richiedere l'aumento dei limiti tramite una richiesta di supporto nella sottoscrizione di Azure DevTest. Le risorse all'interno della sottoscrizione di produzione non sono influenzate dall'aumento nell'uso della sottoscrizione di sviluppo. Per altre informazioni sul ridimensionamento di DevTest Labs, vedere [Ridimensionare i limiti e le quote in DevTest Labs](devtest-lab-scale-lab.md).

Un limite comune a livello di sottoscrizione che deve essere considerato è la modalità di allocazione dell'assegnazione di intervalli IP di rete per supportare le sottoscrizioni di sviluppo e produzione. Queste assegnazioni devono tenere conto della crescita nel tempo (presupponendo la connettività locale o un'altra topologia di rete che richiede all'azienda di gestire i relativi stack di rete anziché impiegare per impostazione predefinita l'implementazione di Azure). La procedura consigliata è quella di avere poche reti virtuali con un prefisso elevato di indirizzi IP assegnato e diviso tra molte subnet di grandi dimensioni anziché avere più reti virtuali con subnet ridotte. Con 10 sottoscrizioni è ad esempio possibile definire 10 reti virtuali (una per ogni sottoscrizione). Tutti i lab che non richiedono l'isolamento possono condividere la stessa subnet nella rete virtuale della sottoscrizione.

## <a name="maintain-naming-conventions"></a>Gestire le convenzioni di denominazione

### <a name="question"></a>Domanda
Come è possibile gestire una convenzione di denominazione nell'ambiente di DevTest Labs?

### <a name="answer"></a>Risposta
È possibile estendere le convenzioni di denominazione correnti dell'organizzazione alle operazioni di Azure e renderle coerenti in tutto l'ambiente DevTest Labs.

In fase di distribuzione di DevTest Labs è consigliabile avere criteri iniziali specifici. Questi criteri vengono distribuiti in base a uno script centrale e a modelli JSON per garantire la coerenza. I criteri di denominazione possono essere implementati tramite i criteri di Azure applicati a livello di sottoscrizione. Per esempi JSON per Criteri di Azure, vedere [Esempi di Criteri di Azure](../azure-policy/json-samples.md).

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Numero di utenti per lab e lab per organizzazione

### <a name="question"></a>Domanda 
Come è possibile determinare il rapporto tra utenti per lab e numero complessivo di lab necessari all'interno di un'organizzazione?

### <a name="answer"></a>Risposta
È consigliabile che le business unit e i gruppi di sviluppo dello stesso progetto di sviluppo siano associati allo stesso lab. In questo modo è possibile applicare a entrambi i gruppi gli stessi tipi di criteri, immagini e criteri di arresto. 

Potrebbe anche essere necessario considerare i limiti geografici. Gli sviluppatori nell'area degli Stati Uniti nordorientali potrebbero ad esempio usare un lab sottoposto a provisioning nell'area degli Stati Uniti orientali 2. E gli sviluppatori a Dallas, in Texas, e a Denver, in Colorado, potrebbero essere indirizzati all'uso di una risorsa negli Stati Uniti centro-meridionali. In caso di un'attività collaborativa con una terza parte esterna, quest'ultima potrebbe essere assegnata a un lab non usato dagli sviluppatori interni. 

È anche possibile usare un lab per un progetto specifico all'interno dei progetti di Azure DevOps. In questo caso, applicare la sicurezza tramite un gruppo di Azure Active Directory specificato, che consente l'accesso a entrambi i set di risorse. La rete virtuale assegnata al lab può essere un altro limite al consolidamento degli utenti.

## <a name="deletion-of-resources"></a>Eliminazione di risorse

### <a name="question"></a>Domanda
Come è possibile impedire l'eliminazione delle risorse all'interno di un lab?

### <a name="answer"></a>Risposta
È consigliabile impostare le autorizzazioni appropriate a livello di lab in modo che solo gli utenti autorizzati possano eliminare le risorse o modificare i criteri del lab. Gli sviluppatori devono trovarsi all'interno del gruppo di **utenti DevTest Labs**. Il responsabile degli sviluppatori o dell'infrastruttura deve essere il **proprietario di DevTest Labs**. È consigliabile avere solo due proprietari di lab. Questi criteri si estendono al repository di codice per evitare il danneggiamento. Gli utenti dei lab sono autorizzati a usare risorse, ma non possono aggiornarne i criteri. Vedere l'articolo seguente che elenca i ruoli e i diritti di ogni gruppo incorporato all'interno di un lab: [Aggiungere proprietari e utenti in Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>Spostare un lab in un altro gruppo di risorse 

### <a name="question"></a>Domanda
È supportato il trasferimento di un lab in un altro gruppo di risorse?

### <a name="answer"></a>Risposta
Sì. Passare alla pagina del gruppo di risorse dalla home page del lab. Selezionare **Sposta** sulla barra degli strumenti e fare clic sul lab che si intende spostare in un gruppo di risorse diverso. Quando si crea un lab, viene automaticamente creato un gruppo di risorse. È comunque possibile spostare il lab in un gruppo di risorse diverso che segue le convenzioni di denominazione dell'organizzazione. 

## <a name="next-steps"></a>Passaggi successivi
Vedere [Manage cost and ownership](devtest-lab-guidance-governance-cost-ownership.md) (Gestire costi e proprietà).
