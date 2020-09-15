---
title: 'Domande frequenti: SRE e codifica | Microsoft Docs'
titleSuffix: Azure
description: 'Domande frequenti: informazioni sulla relazione tra SRE e codice'
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: b8865fdd53f4947b17a3621a128fc83f3d93d3e2
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90089072"
---
# <a name="frequently-asked-questions-do-i-need-to-know-how-to-code-to-get-involved-with-sre"></a>Domande frequenti: è necessario avere informazioni su come scrivere codice per collaborare con SRE?

Quando gli utenti stanno prendendo in considerazione la partecipazione a SRE e ai team che si occupano dell'introduzione delle procedure SRE, una domanda comune è "è necessario sapere come scrivere codice?"

Risposta breve: Sì. 

Ma la risposta completa è leggermente più sfumata. Si osserveranno tre posizioni in cui il codice entra in gioco in progettazione dell'affidabilità del sito insieme al livello di esperienza di codifica richiesto per ciascuna di esse. Questo elenco non è completo, ma questi scenari sono alcuni dei casi d'uso più comuni.

## <a name="scenario-1-removing-toil-through-automation"></a>Scenario 1: rimozione della fatica tramite automazione

I tecnici di affidabilità del sito e altri utenti che usano le procedure SRE tentano laddove possibile di rimuovere le fatiche. "Fatica" significa una cosa specifica in SRE. Il lavoro si riferisce alle operazioni eseguite da un uomo che ha determinate caratteristiche. La fatica non ha un valore di riscatto a lungo termine. Non fa avanzare il servizio in modo significativo. È spesso ripetitiva e in gran parte manuale (sebbene possa essere automatizzata). Man mano che il servizio o il sistema cresce di dimensione, è probabile che anche il numero di richieste del sistema aumenti in modo proporzionale e necessiti di maggior lavoro manuale.

Ad esempio, se un servizio richiede al team SRE di reimpostare un elemento ogni settimana o di eseguire manualmente il provisioning di nuovi account e spazio su disco oppure di riavviarlo ripetutamente manualmente, si tratta di un carico operativo. L'esecuzione di queste azioni non ha migliorato il servizio in modo persistente a lungo termine. È probabile che queste azioni debbano essere ripetute più volte.

Gli SRE odiano la fatica. Lavorano per eliminarla ogni volta che è possibile e appropriato. Questo è il caso in cui entra in gioco l'automazione in SRE. Se queste richieste possono essere gestite automaticamente, questo consente di liberare il team per lavorare su elementi più gratificanti e interessati.

*Esperienza di scrittura del codice*: l'automazione richiede una certa esperienza di scrittura del codice, ma non richiede competenze complete per la progettazione del software. Se è possibile scrivere script di piccole dimensioni, ad esempio in PowerShell o in una shell Bourne, o anche se si crea un oggetto [https://docs.microsoft.com/azure/logic-apps/logic-apps-overview](Azure Logic app) con un codice qualsiasi, questa app può comunque contribuire a eliminare la fatica.

## <a name="scenario-2-control-through-apisdomain-specific-languages-dslstemplates"></a>Scenario 2: controllo tramite API/linguaggi specifici del dominio (DSLs)/templates

Sebbene non sia strettamente necessario per il lavoro SRE, la possibilità di controllare gli ambienti tramite API, DSLs e modelli (in particolare gli ambienti cloud) consente a SRE di scalare il lavoro. L'infrastruttura di provisioning/deprovisioning, la configurazione del monitoraggio e l'integrazione di diversi servizi diventano molto più efficienti tramite la codifica.

*Esperienza di codifica*: come lo scenario precedente, questo richiede una certa esperienza di scrittura del codice, ma non richiede competenze complete per la progettazione del software. Oltre agli script e alle app per la logica indicati in precedenza, [https://docs.microsoft.com/azure/azure-resource-manager/templates/overview](Azure Resource Manager Templates) può essere usato anche con un'esperienza di codifica minima.

## <a name="scenario-3-fixing-the-code"></a>Scenario 3: correzione del codice

I tecnici di affidabilità del sito hanno l'aspetto di migliorare l'affidabilità di un sistema. Questo obiettivo a volte richiede l'esecuzione di ricerche nel codice sorgente di un sistema, la determinazione del problema e spesso la restituzione di una correzione alla codebase. Sebbene il livello di sofisticazione di questo lavoro possa variare notevolmente a seconda della situazione, in questi casi l'esperienza di codifica è un requisito preciso.

*Esperienza di scrittura del codice*: in questo scenario è spesso necessaria un'esperienza di progettazione software completa.


## <a name="next-steps"></a>Passaggi successivi

Vuoi saperne di più sulla progettazione dell'affidabilità del sito e sul lavoro con basso codice? Vedere l' [Hub di progettazione dell'affidabilità del sito](../index.yml), la documentazione del prodotto collegata sopra.
