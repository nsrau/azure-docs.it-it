---
title: Pianificazione della capacità per i ruoli server di servizio App di Azure nello Stack di Azure | Documenti Microsoft
description: Pianificazione della capacità per i ruoli server di servizio App di Azure nello Stack di Azure
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: f54481fe59df21b500ee860d1e9a202ed32bdd87
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097149"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Pianificazione della capacità per i ruoli server di servizio App di Azure nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Per configurare una distribuzione pronto di produzione del servizio App di Azure nello Stack di Azure, è necessario pianificare la capacità si prevede che il sistema per il supporto.  

In questo articolo vengono fornite indicazioni per il numero minimo di istanze di calcolo e di calcolo SKU è necessario utilizzare per la distribuzione di produzione.

È possibile pianificare la strategia di capacità di servizio App utilizzano queste linee guida. Le versioni future dello Stack di Azure verranno fornite opzioni di disponibilità elevata per il servizio App.

| Ruolo del server del servizio App | Minimo numero consigliato di istanze | Calcolo consigliato SKU|
| --- | --- | --- |
| Controller | 2 | A1 |
| Front-end | 2 | A1 |
| Gestione | 2 | A3 |
| Editore | 2 | A1 |
| Web worker - condivisi | 2 | A1 |
| Web worker - dedicato | 2 per ogni livello | A1 |

## <a name="controller-role"></a>Ruolo controller

**Valore minimo consigliato**: due istanze di A1 Standard

Il Controller del servizio App di Azure sperimentato in genere un basso utilizzo di CPU, memoria e risorse di rete. Tuttavia, per la disponibilità elevata, è necessario disporre di due controller. Due controller vengono anche il numero massimo di controller consentiti. È possibile creare direttamente dal programma di installazione il secondo Controller di siti Web durante la distribuzione.

## <a name="front-end-role"></a>Front-End e del ruolo

**Valore minimo consigliato**: due istanze di A1 Standard

A seconda della disponibilità di Web Worker, Front-End instrada le richieste ai Web worker. Per la disponibilità elevata, si avranno più di un Front-End e si può disporre di più di due. Per pianificare la capacità, prendere in considerazione che ciascun core è possibile gestire circa 100 richieste al secondo.

## <a name="management-role"></a>Ruolo di gestione

**Valore minimo consigliato**: due istanze di Standard A3

Il ruolo di gestione di Azure App Service è responsabile della gestione risorse di Azure di servizio App e gli endpoint dell'API, estensioni portale (amministrazione, tenant, le funzioni portale) e il servizio dati. Il ruolo Server di gestione richiede in genere solo su 4 GB di RAM in un ambiente di produzione. Tuttavia, è possibile sperimentare livelli di CPU superiori qualora vengano eseguite più attività di gestione (ad esempio la creazione di siti web). Per la disponibilità elevata, è necessario più di un server assegnato a questo ruolo, e almeno due core per server.

## <a name="publisher-role"></a>Ruolo server di pubblicazione

**Valore minimo consigliato**: due istanze di A1 Standard

Se molti utenti contemporaneamente, il ruolo del server di pubblicazione potrebbe verificarsi un utilizzo intenso della CPU. Per la disponibilità elevata, rendere disponibile più di un ruolo di server di pubblicazione.  Il server di pubblicazione gestisce solo il traffico FTP o FTPS.

## <a name="web-worker-role"></a>Ruolo Web worker

**Valore minimo consigliato**: due istanze di A1 Standard

Per la disponibilità elevata, è necessario disporre almeno quattro ruoli Web Worker, due per le modalità condiviso e due per ogni livello di ruolo di lavoro dedicato si intende offrire. Condiviso e le modalità di calcolo dedicato offrono diversi livelli di servizio ai tenant. Potrebbero essere necessari più Web worker se molti dei clienti sono:

- Utilizzo di piani di lavoro in modalità di calcolo dedicato (che sono un numero di risorse).
- In esecuzione in modalità di calcolo condivisa.

Dopo che un utente ha creato un piano di servizio App per una modalità di calcolo dedicato SKU, il numero di lavori Web specificato nel piano di servizio App non saranno disponibile per gli utenti.

Per fornire funzioni di Azure per gli utenti del modello di piano il consumo, è necessario distribuire condiviso Web worker.

Quando si scelgono sul numero di ruoli Web Worker condivisi da utilizzare, esaminare queste considerazioni:

- **Memoria**: la memoria è la risorsa più critica per un ruolo Web Worker. Memoria insufficiente influisce sulle prestazioni del sito web durante lo scambio e memoria virtuale dal disco. Ogni server richiede circa 1,2 GB di RAM per il sistema operativo. RAM sopra di questa soglia può essere utilizzata per eseguire i siti web.
- **Percentuale di siti web attivi**: in genere, circa il 5% delle applicazioni in un servizio App di Azure sulla distribuzione di Azure Stack sono attivo. Tuttavia, la percentuale di applicazioni che sono attive in qualsiasi momento può essere superiore o inferiore. Con un'applicazione attiva pari al 5%, il numero massimo di applicazioni da inserire in un servizio App di Azure sulla distribuzione di Azure Stack deve essere inferiore a:
  - 20 volte il numero di siti web attivi (5 x 20 = 100).
- **Footprint di memoria medio**: il footprint di memoria medio per le applicazioni osservati negli ambienti di produzione è di circa 70 MB. Tramite questo footprint, la memoria allocata in tutti i computer con ruolo Web Worker o VM può essere calcolata come indicato di seguito:

    *Numero di applicazioni di provisioning eseguito * 70 MB * 5%: (numero di ruoli Web Worker * 1044 MB)*

   Ad esempio, se sono presenti 5.000 applicazioni nell'ambiente in cui è in esecuzione 10 ruoli Web Worker, ogni ruolo Web Worker VM dovrà 7060 MB di RAM seguenti:

   5.000 * 70 * 0,05 – (10 * 1044) = 7060 (= circa 7 GB)

   Per informazioni sull'aggiunta di più istanze di lavoro, vedere [aggiunta di più ruoli di lavoro](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Ruolo file server

Per il ruolo File Server, è possibile utilizzare un file server autonomo per lo sviluppo e test, ad esempio durante la distribuzione del servizio App di Azure nel Kit di sviluppo dello Stack Azure è possibile utilizzare questo modello - <https://aka.ms/appsvconmasdkfstemplate>. Ai fini della produzione, è necessario utilizzare un File Server Windows preconfigurato o un server di file non Windows preconfigurato.

Negli ambienti di produzione il ruolo File Server vengono sperimentati i/o su disco elevati. Poiché tramite esso vengono ospitati tutti i file di contenuto e dell'applicazioni per i siti web dell'utente, è necessario preconfigurare uno dei seguenti valori per questo ruolo:

- un File Server Windows
- un Cluster di File Server Windows
- un file server non Windows
- un cluster di file non Windows server
- un dispositivo NAS (Network Attached Storage)

Per altre informazioni, vedere [eseguire il provisioning di un file server](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Passaggi successivi

[Prima di iniziare a con il servizio App nello Stack di Azure](azure-stack-app-service-before-you-get-started.md)
