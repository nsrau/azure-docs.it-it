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
ms.openlocfilehash: 47f3e8ab14ecd50e958c57ba4c8f9f098fd5bb7b
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52868362"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Governance dell'infrastruttura di Azure DevTest Labs - Gestire i costi e la proprietà
Costi e proprietà sono problemi primari quando si considera la creazione di ambienti di test e sviluppo. In questa sezione sono riportate informazioni che consentono di ottimizzare i costi e allineare la proprietà al proprio ambiente.

## <a name="optimize-for-cost"></a>Ottimizzare in base ai costi

### <a name="question"></a>Domanda
Come è possibile ottimizzare in base ai costi all'interno dell'ambiente DevTest Labs?

### <a name="answer"></a>Risposta
Esistono numerose funzionalità predefinite di DevTest Labs che consentono di ottimizzare in termini di costi. Leggere gli articoli [su gestione dei costi, soglie](devtest-lab-configure-cost-management.md) [e criteri](devtest-lab-set-lab-policy.md) per limitare le attività degli utenti. 

Quando si usa DevTest Labs per carichi di lavoro di test e sviluppo, è possibile prendere in considerazione l'utilizzo del [vantaggio della sottoscrizione Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/), come parte del Contratto Enterprise. In alternativa, per i clienti con pagamento in base al consumo è possibile prendere in considerazione l'[offerta Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/).

Questo approccio offre numerosi vantaggi:

- Speciali tariffe di Sviluppo/test inferiori per macchine virtuali Windows, Servizi cloud, HDInsight, Servizio app e App per la logica
- Straordinarie tariffe per il Contratto Enterprise (EA) in altri servizi di Azure
- Accesso a immagini di Sviluppo/test esclusive nella raccolta, tra cui Windows 8.1 e Windows 10
 
Solo i titolari di sottoscrizioni attive di Visual Studio (sottoscrizioni standard, sottoscrizioni cloud annuali e sottoscrizioni cloud mensili) possono usare le risorse di Azure in esecuzione nell'ambito di una sottoscrizione dell'offerta Sviluppo/test Enterprise. Gli utenti finali tuttavia possono accedere all'applicazione per fornire commenti e o eseguire test di accettazione. L'uso delle risorse nell'ambito di questa sottoscrizione è limitato allo sviluppo e al test delle applicazioni e non sono previsti tempi di attività garantiti.

Se si decide di usufruire dell'offerta Sviluppo/test, si noti che questo vantaggio è riservato esclusivamente allo sviluppo e test delle proprie applicazioni. L'utilizzo entro i limiti della sottoscrizione non prevede un contratto di servizio con copertura finanziaria, fatta eccezione per l'uso di Azure DevOps e HockeyApp.

## <a name="define-a-role-based-access-across-your-organization"></a>Definire un accesso in base al ruolo all'interno dell'organizzazione
### <a name="question"></a>Domanda
Come definisco il controllo degli accessi in base al ruolo per gli ambienti DevTest Labs al fine di garantire che l'IT possa governare mentre gli sviluppatori/i test possono svolgere il proprio lavoro? 

### <a name="answer"></a>Risposta
Esiste un criterio ampio, tuttavia i dettagli dipendono dalla propria organizzazione.

L'IT centrale dovrebbe possedere solo ciò che è necessario e consentire ai team di progetto e di applicazioni di disporre del livello di controllo necessario. In genere significa che l'IT centrale possiede la sottoscrizione e gestisce le funzioni IT di base come le configurazioni di rete. Il set di **proprietari** per una sottoscrizione deve essere di piccole dimensioni. Questi proprietari possono nominare proprietari aggiuntivi se necessario oppure applicare criteri a livello di sottoscrizione, ad esempio "Nessun IP pubblico".

Un sottoinsieme di utenti potrebbe richiedere l'accesso a una sottoscrizione, ad esempio il supporto di Livello 1 o 2. In questo caso, si consiglia di concedere a questi utenti l'accesso **collaboratore** in modo che possano gestire le risorse, ma non fornire l'accesso utente o modificare i criteri.

La risorsa di DevTest Labs deve appartenere a proprietari vicini al team di progetto/applicazione. Questo perché comprendono le loro esigenze in termini di computer e software necessari. Nella maggior parte delle organizzazioni, il proprietario della risorsa di DevTest Labs è in genere il responsabile del progetto/sviluppo. Questo proprietario può gestire gli utenti e i criteri all'interno dell'ambiente lab, nonché tutte le macchine virtuali nell'ambiente di DevTest Labs.

I membri del team di progetto/applicazione devono essere aggiunti al ruolo utenti di DevTest Labs. Questi utenti possono creare macchine virtuali (in linea con i criteri a livello di sottoscrizione e lab). Possono anche gestire le proprie macchine virtuali. Non possono gestire le macchine virtuali che appartengono ad altri utenti.

Per altre informazioni, consultare la documentazione [Scaffold Azure enterprise: governance prescrittiva per le sottoscrizioni](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


## <a name="next-steps"></a>Passaggi successivi
Vedere [Criteri e conformità aziendali](devtest-lab-guidance-governance-policy-compliance.md).
