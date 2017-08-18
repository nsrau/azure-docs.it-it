---
title: "Domande frequenti relative ai problemi di disponibilità di applicazioni e servizi per Servizi cloud di Microsoft Azure| Microsoft Docs"
description: "Questo articolo elenca le domande frequenti relative alla disponibilità di applicazioni e servizi per Servizi cloud di Microsoft Azure."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 8aa00757ff11ed9086cd178e4c7190b93ee62701
ms.contentlocale: it-it
ms.lasthandoff: 06/15/2017

---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemi di disponibilità di applicazioni e servizi per Servizi cloud di Azure: domande frequenti

Questo articolo include le domande frequenti relative ai problemi di disponibilità di applicazioni e servizi per [Servizi cloud di Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Per informazioni sulle dimensioni, vedere la pagina [Dimensioni dei servizi cloud](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Il ruolo è stato riciclato. È stato implementato un aggiornamento per il servizio cloud?
Circa una volta al mese, Microsoft rilascia una nuova versione del sistema operativo guest per VM PaaS di Microsoft Azure. Il sistema operativo guest rappresenta solo uno di tali aggiornamenti nella pipeline. Una versione può essere influenzata da molti altri fattori. Inoltre, Azure è in esecuzione su centinaia di migliaia di computer. Ciò significa che è impossibile prevedere una data e un'ora esatte per il riavvio dei ruoli. Il feed RSS di aggiornamento del sistema operativo guest viene aggiornato con le informazioni più recenti disponibili, ma è necessario tenere presente che gli orari segnalati sono approssimativi. Microsoft è consapevole del fatto che questo rappresenti un problema per i clienti e sta lavorando a un piano per limitare i riavvii o impostare una tempistica precisa.

Per informazioni complete sugli aggiornamenti recenti del sistema operativo guest vedere [Rilasci del sistema operativo guest Azure e matrice di compatibilità dell'SDK](cloud-services-guestos-update-matrix.md).

Per informazioni utili sui riavvii e collegamenti ad altri dettagli tecnici degli aggiornamenti dei sistemi operativi guest e host, vedere il post di blog MSDN [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) (Riavvii delle istanze dei ruoli a causa di aggiornamenti del sistema operativo).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Perché la prima richiesta al servizio cloud dopo che il servizio è rimasto inattivo per un po' di tempo impiega più del solito?
Quando il server Web riceve la prima richiesta, prima di tutto ricompila il codice e quindi elabora la richiesta. Per questo motivo la prima richiesta richiede più tempo rispetto alle altre. Per impostazione predefinita, il pool di app viene arrestato in caso di inattività dell'utente. Il pool di app viene inoltre riciclato per impostazione predefinita ogni 1.740 minuti (29 ore).

I pool di applicazioni IIS (Internet Information Services) possono essere riciclati periodicamente per evitare stati non stabili che possono portare a blocchi, arresti anomali delle applicazioni o perdite di memoria.

I documenti seguenti sono utili per comprendere e attenuare questo problema:
* [Fixing slow initial load for IIS](http://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis) (Correzione del problema di caricamento iniziale lento per IIS)
* [IIS 7.5 web application first request after app-pool recycle very slow](http://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow) (La prima richiesta all'applicazione Web IIS 7.5 dopo il riciclo del pool di app è molto lenta)

Se si vuole modificare il comportamento predefinito di IIS, è necessario usare le attività di avvio, poiché se si applicano manualmente le modifiche alle istanze del ruolo Web, queste potrebbero andare perse.

Per altre informazioni, vedere [Come configurare ed eseguire attività di avvio per un servizio cloud](cloud-services-startup-tasks.md).

