---
title: Gestire Servizi cloud di Azure usando Automazione di Azure | Documentazione Microsoft
description: Informazioni su come è possibile usare il servizio Automazione di Azure per gestire i Servizi cloud di Azure su vasta scala.
services: cloud-services, automation
author: jodoglevy
manager: timlt
editor: ''
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: 482fcf7d100a90d9527f510382c5dafb4f67adfa
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72439065"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Gestione dei Servizi cloud di Azure mediante Automazione di Azure
Questa guida fornisce un'introduzione al servizio Automazione di Azure e ne illustra l'utilizzo per semplificare la gestione dei Servizi cloud di Azure.

## <a name="what-is-azure-automation"></a>Che cos'è Automazione di Azure?
[Automazione di Azure](https://azure.microsoft.com/services/automation/) è un servizio di Azure che consente di semplificare la gestione del cloud tramite l'automazione dei processi. Usando Automazione di Azure, è possibile automatizzare attività a esecuzione prolungata, manuali, soggette a errori e ripetute frequentemente per migliorare l'affidabilità, l'efficienza e i tempi di esecuzione.

Automazione di Azure offre un motore di esecuzione del flusso di lavoro a elevata disponibilità e affidabilità che garantisce la scalabilità necessaria per rispondere alle esigenze aziendali in base alla crescita dell'organizzazione. In Automazione di Azure i processi possono essere attivati manualmente, da sistemi di terze parti o a intervalli pianificati in modo che le attività abbiano luogo esattamente quando necessario.

Il servizio permette di ridurre i costi operativi e di liberare risorse dello staff IT/DevOp consentendo loro di concentrarsi su attività a valore aggiunto grazie al trasferimento delle attività di gestione del cloud all'esecuzione automatica di Automazione di Azure.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Come può Automazione di Azure aiutare nella gestione dei Servizi cloud di Azure?
I Servizi cloud di Azure possono essere gestiti in Automazione di Azure usando i cmdlet di PowerShell disponibili negli [strumenti di Azure PowerShell](/powershell/). I cmdlet di PowerShell per i Servizi cloud sono predefiniti in Automazione di Azure, per consentire l'esecuzione di tutte le attività di gestione dei servizi cloud dall'interno del servizio. È anche possibile abbinare tali cmdlet in Automazione di Azure ai cmdlet per altri servizi di Azure per automatizzare attività complesse in tutti i servizi di Azure e nei sistemi di terze parti.

Gli esempi che usano Automazione di Azure per gestire i Servizi cloud di Azure includono:

* [Distribuzione continua di un servizio cloud ogni volta che il file cscfg o cspkg viene aggiornato nell'archivio BLOB di Azure](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Riavvio di istanze del servizio cloud in parallelo, un dominio di aggiornamento alla volta](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Fasi successive
A questo punto, dopo aver appreso le nozioni di base di Automazione di Azure e come può essere usato per gestire i Servizi cloud di Azure, seguire i collegamenti seguenti per altre informazioni su Automazione di Azure.

* [Panoramica di Automazione di Azure](../automation/automation-intro.md)
* [Il primo runbook](../automation/automation-first-runbook-graphical.md)
