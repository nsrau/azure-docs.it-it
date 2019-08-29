---
title: Gestire Gestione API di Azure usando Automazione di Azure
description: Informazioni su come è possibile usare il servizio Automazione di Azure per gestire Gestione API di Azure.
services: api-management, automation
documentationcenter: ''
author: vladvino
manager: eamono
editor: ''
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: a472e00f9ecab8a5ffa6b19e4fe9a5f8b5ee5b95
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072051"
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Gestione di Gestione API di Azure usando Automazione di Azure
Questa guida fornisce un'introduzione al servizio Automazione di Azure e ne illustra l'uso per semplificare la gestione di Gestione API di Azure.

## <a name="what-is-azure-automation"></a>Informazioni su Automazione di Azure
[Automazione di Azure](https://azure.microsoft.com/services/automation/) è un servizio di Azure che consente di semplificare la gestione del cloud tramite l'automazione dei processi. Usando Automazione di Azure, è possibile automatizzare attività manuali, ripetute, a esecuzione prolungata e soggette a errori per migliorare l'affidabilità, l'efficienza e i tempi di esecuzione dell'organizzazione.

Automazione di Azure offre un motore di esecuzione del flusso di lavoro a elevata disponibilità e affidabilità che garantisce la scalabilità necessaria per rispondere alle esigenze aziendali. In Automazione di Azure i processi possono essere attivati manualmente, da sistemi di terze parti o a intervalli pianificati in modo che le attività abbiano luogo esattamente quando necessario.

Il servizio consente di ridurre i costi operativi e di liberare risorse dello staff IT e DevOp consentendo loro di concentrarsi su attività a valore aggiunto grazie al trasferimento delle attività di gestione del cloud all'esecuzione automatica di Automazione di Azure.

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>In che modo Gestione API di Azure può essere gestito con Automazione di Azure?
Gestione API può essere gestito in Automazione di Azure tramite l' [API cmdlet di Windows PowerShell per Gestione API di Azure](https://docs.microsoft.com/powershell/module/az.apimanagement). All'interno di Automazione di Azure è possibile scrivere gli script dei flussi di lavoro di PowerShell per eseguire molte delle attività di Gestione API mediante i cmdlet. È anche possibile abbinare tali cmdlet in Automazione di Azure ai cmdlet per altri servizi di Azure per automatizzare attività complesse in tutti i servizi di Azure e nei sistemi di terze parti.

Di seguito sono riportati alcuni esempi di utilizzo di Gestione API con Powershell:

* [Esempi di Azure PowerShell per Gestione API](https://docs.microsoft.com/azure/api-management/powershell-samples)

## <a name="next-steps"></a>Fasi successive
A questo punto, dopo aver appreso le nozioni di base di Automazione di Azure e del modo in cui può essere usato per gestire Gestione API di Azure, usare i collegamenti seguenti per altre informazioni su Automazione di Azure.

* Vedere l' [esercitazione iniziale](../automation/automation-first-runbook-graphical.md)di Automazione di Azure.

