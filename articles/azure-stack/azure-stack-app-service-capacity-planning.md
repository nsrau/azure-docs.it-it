---
title: Pianificazione della capacità per i ruoli server di servizio App di Azure in Azure Stack | Microsoft Docs
description: Pianificazione della capacità per i ruoli server di servizio App di Azure in Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: 7cdcd8b7e9814c206255077fae0af2029fab6583
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078114"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Pianificazione della capacità per i ruoli server di servizio App di Azure in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Per configurare una distribuzione pronta di produzione del servizio App di Azure in Azure Stack, è necessario pianificare la capacità che previsto il supporto del sistema.  

Questo articolo offre materiale sussidiario per il numero minimo di istanze di calcolo e di calcolo SKU è consigliabile usare per qualsiasi distribuzione di produzione.

È possibile pianificare la strategia di capacità del servizio App usando le linee guida. Le versioni future di Azure Stack verranno fornite opzioni di disponibilità elevata per il servizio App.

| Ruolo del server del servizio App | Minimo numero consigliato di istanze | Calcolo consigliato SKU|
| --- | --- | --- |
| Controller | 2 | A1 |
| Front-end | 2 | A1 |
| Gestione | 2 | A3 |
| Editore | 2 | A1 |
| Web worker - condiviso | 2 | A1 |
| Web worker - dedicato | 2 per ogni livello | A1 |

## <a name="controller-role"></a>Ruolo controller

**Valore minimo consigliato**: due istanze di Standard A1

Il Controller di servizio App di Azure sperimentato in genere un basso utilizzo della CPU, memoria e risorse di rete. Tuttavia, per la disponibilità elevata, è necessario disporre di due controller. Due controller sono anche il numero massimo di controller consentiti. È possibile creare direttamente dal programma di installazione il secondo Controller di siti Web durante la distribuzione.

## <a name="front-end-role"></a>Ruolo Front-End

**Valore minimo consigliato**: due istanze di Standard A1

A seconda della disponibilità di Web Worker, Front-End instrada le richieste ai Web worker. Per la disponibilità elevata, è necessario disporre di più Front-End e si può avere più di due. Per scopi di pianificazione della capacità, prendere in considerazione che ciascun core è possibile gestire circa 100 richieste al secondo.

## <a name="management-role"></a>Ruolo di gestione

**Valore minimo consigliato**: due istanze di A3 Standard

Il ruolo di gestione del servizio App di Azure è responsabile per l'App del servizio Azure Resource Manager e gli endpoint dell'API, estensioni del portale (amministrazione, tenant, il portale di funzioni) e il servizio dati. Il ruolo del Server di gestione richiede in genere solo su 4 GB di RAM in un ambiente di produzione. Livelli elevati di CPU, tuttavia, verifichi quando vengono eseguite molte attività di gestione (ad esempio la creazione del sito web). Per la disponibilità elevata, è necessario che più di un server assegnato a questo ruolo, e almeno due core per ogni server.

## <a name="publisher-role"></a>Ruolo server di pubblicazione

**Valore minimo consigliato**: due istanze di Standard A1

Se molti utenti pubblicano contemporaneamente, il ruolo del server di pubblicazione potrebbe verificarsi un utilizzo intenso della CPU. Per la disponibilità elevata, rendere disponibile più di un ruolo del server di pubblicazione.  Il server di pubblicazione gestisce solo il traffico FTP/FTPS.

## <a name="web-worker-role"></a>Ruolo Web worker

**Valore minimo consigliato**: due istanze di Standard A1

Per la disponibilità elevata, si deve avere almeno quattro ruoli Web Worker, due per la modalità condiviso e due per ogni livello di ruolo di lavoro dedicato si decida di offrire. Le modalità di calcolo dedicati e condivisi fornire diversi livelli di servizio ai tenant. Potrebbero essere necessarie più Web worker per molti dei clienti sono:

- Usando il livello di ruolo di lavoro in modalità di calcolo dedicati (che è a elevato utilizzo di risorse).
- In esecuzione in modalità di calcolo condivisa.

Dopo che un utente ha creato un piano di servizio per una modalità di calcolo dedicati SKU, il numero di lavori Web specificati nel piano di servizio App non saranno disponibile per gli utenti.

Per fornire funzioni di Azure per gli utenti nel modello di piano di consumo, è necessario distribuire condiviso Web worker.

Nello stabilire il numero di ruoli Web Worker condivisi da usare, esaminare queste considerazioni:

- **Memoria**: la memoria è la risorsa più critica per un ruolo Web Worker. Memoria insufficiente influisce sulle prestazioni del sito web quando viene scambiata memoria virtuale dal disco. Ogni server richiede circa 1,2 GB di RAM per il sistema operativo. RAM superiore a questa soglia è utilizzabile per eseguire i siti web.
- **Percentuale di siti web attivi**: sono in genere attivo circa il 5% delle applicazioni nel servizio App di Azure nella distribuzione di Azure Stack. Tuttavia, la percentuale di applicazioni che sono attivi in qualsiasi momento può essere superiore o inferiore. Con una frequenza dell'applicazione attiva del 5%, il numero massimo di applicazioni da inserire in un servizio App di Azure nella distribuzione di Azure Stack deve essere inferiore a:
  - 20 volte il numero di siti web attivi (5 x 20 = 100).
- **Footprint di memoria medio**: il footprint di memoria medio per le applicazioni osservati negli ambienti di produzione è di circa 70 MB. Usando questa copertura, la memoria allocata in tutti i computer con ruolo Web Worker o VM può essere calcolata come segue:

    *Numero di applicazioni di provisioning eseguito * 70 MB * 5% - (numero di ruoli Web Worker * 1044 MB)*

   Ad esempio, se sono presenti 5.000 applicazioni nell'ambiente in cui è in esecuzione 10 ruoli Web Worker, ogni ruolo Web Worker della macchina virtuale deve avere 7060 MB di RAM:

   0,05 * 5.000 * 70 – (10 * 1044) = 7060 (= circa 7 GB)

   Per informazioni sull'aggiunta di altre istanze del ruolo di lavoro, vedere [aggiunta di altri ruoli di lavoro](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Ruolo file server

Per il ruolo File Server, è possibile usare un file server autonomo per lo sviluppo e test, ad esempio quando si distribuisce il servizio App di Azure in Azure Stack Development Kit è possibile usare questo modello - <https://aka.ms/appsvconmasdkfstemplate>. Per scopi di produzione, è necessario usare Windows File Server preconfigurato o un server di file non Windows preconfigurato.

Negli ambienti di produzione, il ruolo File Server si verifica nel disco con utilizzo intensivo dei / o. Poiché esso vengono ospitati tutti i file di contenuto e dell'applicazioni per siti web dell'utente, è necessario preconfigurare uno dei seguenti valori per questo ruolo:

- un File Server Windows
- un Cluster di File Server Windows
- un file non-Windows server
- un cluster di file non-Windows server
- un dispositivo NAS (Network Attached Storage)

Per altre informazioni, vedere [effettuare il provisioning di un file server](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Passaggi successivi

[Prima di iniziare con il servizio App in Azure Stack](azure-stack-app-service-before-you-get-started.md)
