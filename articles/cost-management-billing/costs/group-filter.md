---
title: Opzioni di raggruppamento e filtro in Gestione costi di Azure
description: Questo articolo illustra come usare le opzioni di raggruppamento e filtro in Gestione costi di Azure.
author: bandersmsft
ms.author: banders
ms.date: 06/08/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 82e542eafe1578a969c4348fe7562ba1da106e2c
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683403"
---
# <a name="group-and-filter-options-in-cost-analysis"></a>Opzioni di raggruppamento e filtro nell'analisi dei costi

L'analisi dei costi include molte opzioni di raggruppamento e filtro. Questo articolo illustra quando usarle.

Per informazioni sulle opzioni di raggruppamento e filtro, guardare il video sulla [creazione di report per dimensioni e tag di Gestione costi](https://www.youtube.com/watch?v=2Vx7V17zbmk). Per guardare altri video, visitare il [canale YouTube di Gestione costi](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/2Vx7V17zbmk]

## <a name="group-and-filter-properties"></a>Proprietà di raggruppamento e filtro

La tabella seguente contiene alcune delle opzioni di raggruppamento e filtro più comuni disponibili in Analisi dei costi, oltre a indicazioni su quando usarle.

| Proprietà | Utilizzo | Note |
| --- | --- | --- |
| **Zone di disponibilità** | Suddivide i costi di AWS per zona di disponibilità. | Si applica solo agli ambiti e ai gruppi di gestione di AWS. I dati di Azure non includono la zona di disponibilità e vengono visualizzati come **Non applicabile**. |
| **Periodo di fatturazione** | Suddivide i costi del piano con pagamento in base al consumo per il mese in cui sono stati (o saranno) fatturati. | Usare **Periodo di fatturazione** per ottenere una rappresentazione esatta degli addebiti del piano con pagamento in base al consumo fatturati. Includere due giorni aggiuntivi prima e dopo il periodo di fatturazione in caso di filtro in base a un intervallo di date personalizzato. Se ci si limita alle date del periodo di fatturazione esatto non si ottiene una corrispondenza con la fattura. Vengono visualizzati i costi di tutte le fatture del periodo di fatturazione. Usare **ID fattura** per filtrare in base a una fattura specifica. Si applica solo alle sottoscrizioni con pagamento in base al consumo, in quanto le sottoscrizioni con contratto Enterprise o con contratto del cliente Microsoft vengono fatturate in base ai mesi del calendario. Gli account EA/MCA possono usare i mesi di calendario nello strumento di selezione data o la granularità mensile per raggiungere lo stesso obiettivo. |
| **Tipo di addebito** | Suddivide i costi di utilizzo, acquisto, rimborso e prenotazioni inutilizzate. | Gli acquisti di prenotazioni e i rimborsi sono disponibili solo quando si usano i costi effettivi e non i costi ammortizzati. I costi delle prenotazioni inutilizzate sono disponibili solo quando si esaminano i costi ammortizzati. |
| **Reparto** | Suddivide i costi per reparto EA. | Disponibile solo per i gruppi di gestione ed EA. Le sottoscrizioni con pagamento in base al consumo non hanno un reparto e vengono visualizzate come **Non applicabile** o **non assegnate**. |
| **Account di registrazione** | Suddivide i costi per proprietario di account EA. | Disponibile solo per gli account di fatturazione, i reparti e i gruppi di gestione EA. Le sottoscrizioni con pagamento in base al consumo non hanno account di registrazione EA e vengono visualizzate come **Non applicabile** o **non assegnate**. |
| **Frequenza** | Suddivide i costi in base all'utilizzo, una tantum e ricorrenti. | |
| **ID fattura** | Suddivide i costi per fattura emessa. | Gli addebiti non fatturati non hanno ancora un ID fattura e i costi del contratto EA non includono i dettagli della fattura e vengono visualizzati come **Non applicabile**.  |
| **Contatore** | Suddivide i costi per contatore dell'utilizzo. | Gli acquisti e l'utilizzo del Marketplace vengono visualizzati come **Non applicabile**. Vedere **Tipo di addebito** per identificare gli acquisti e **Tipo di autore** per identificare gli addebiti del Marketplace. |
| **Operazione** | Suddivide i costi di AWS per operazione. | Si applica solo agli ambiti e ai gruppi di gestione di AWS. I dati di Azure non includono l'operazione e vengono visualizzati come **Non applicabile**. Usare invece **Contatore**. |
| **Modello di determinazione prezzi** | Suddivide i costi in base all'utilizzo su richiesta, su prenotazione o spot. | Gli acquisti vengono visualizzati come **OnDemand**. Se viene visualizzato **Non applicabile**, raggruppare per **Prenotazione** per determinare se l'utilizzo è su prenotazione o su richiesta e per **Tipo di addebito** per identificare gli acquisti.
| **Provider** | Suddivide i costi in base ad AWS e Azure. | Disponibile solo per i gruppi di gestione. |
| **Tipo di autore** | Suddivide i costi di AWS, Azure e Marketplace. |  |
| **Prenotazione** | Suddivide i costi per prenotazione. | Qualsiasi utilizzo o acquisto non associato a una prenotazione viene visualizzato come **Non applicabile**. Raggruppare per **Tipo di autore** per identificare altri acquisti di Azure, AWS o del Marketplace. |
| **Risorsa** | Suddivide i costi per risorsa. | Gli acquisti vengono visualizzati come **Non applicabile** perché vengono applicati a livello di account di fatturazione EA/con pagamento in base al consumo o a livello di profilo di fatturazione del contratto del cliente Microsoft e non sono associati a una risorsa specifica. Raggruppare per **Tipo di autore** per identificare altri acquisti di Azure, AWS o del Marketplace. |
| **Gruppo di risorse** | Suddivide i costi per gruppo di risorse. | Gli acquisti, le risorse del tenant non associate a sottoscrizioni, le risorse della sottoscrizione non distribuite in un gruppo di risorse e le risorse classiche non hanno un gruppo di risorse e vengono visualizzate come **altri**, **servizi classici**, **$system** o **Non applicabile**. |
| **Tipo di risorsa** | Suddivide i costi per tipo di risorsa. | Gli acquisti e i servizi classici non hanno un tipo di risorsa Azure Resource Manager e vengono visualizzati come **altri**, **servizi classici** o **Non applicabile**. |
| **Posizione risorsa** | Suddivide i costi per località o area. | Gli acquisti e l'utilizzo del Marketplace possono essere visualizzati come **non assegnato**, **sconosciuto**, **non mappato** o **Non applicabile**. |
| **Nome del servizio** o **Categoria del contatore** | Suddivide i costi per servizio di Azure. | Gli acquisti e l'utilizzo del Marketplace vengono visualizzati come **Non applicabile** o **non assegnato**. |
| **Livello di servizio** o **Sottocategoria del contatore** | Suddivide i costi in base alla sottoclassificazione del contatore dell'utilizzo di Azure. | Gli acquisti e l'utilizzo del Marketplace vengono visualizzati come **Non applicabile** o **non assegnato**. |
| **Sottoscrizione** | Suddivide i costi per sottoscrizione di Azure e account AWS collegato. | Gli acquisti e le risorse tenant possono essere visualizzati come **Non applicabile**. |
| **Tag** | Suddivide i costi per valori di tag per una specifica chiave di tag. | I tag non sono disponibili per gli acquisti, le risorse del tenant non associate a sottoscrizioni, le risorse della sottoscrizione non distribuite in un gruppo di risorse o le risorse classiche. Alcuni servizi non includono tag nei dati di utilizzo. Vedere altre informazioni sul [supporto dei tag per ogni tipo di risorsa](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support). |

Per altre informazioni sui termini, vedere [Informazioni sui termini usati nel file su utilizzo e addebiti di Azure](../understand/understand-usage.md).

## <a name="next-steps"></a>Passaggi successivi

- [Avviare l'analisi dei costi](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis).
