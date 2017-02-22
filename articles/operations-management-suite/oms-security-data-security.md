---
title: Sicurezza dei dati della soluzione Sicurezza e controllo di Operations Management Suite | Documentazione Microsoft
description: Questo documento illustra il modo in cui i dati vengono gestiti e protetti nella soluzione Sicurezza e controllo di Operations Management Suite.
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 9cdf7deb-2a30-4672-b89f-71179ee8326a
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 5001cd47b6ee51967d1286414ccefedd8e7e7813
ms.openlocfilehash: 6af6c10cef317453131197db74a9380518afadf0


---
# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>Sicurezza dei dati della soluzione Sicurezza e controllo di Operations Management Suite
Per aiutare i clienti a evitare, rilevare e rispondere alle minacce, la [Soluzione Sicurezza e controllo di Operations Management Suite (OMS)](operations-management-suite-overview.md) raccoglie ed elabora i dati sulle risorse, tra cui:

* Registri eventi sicurezza
* Tracciamento degli eventi di Windows (ETW)
* Eventi di controllo AppLocker
* Log di Windows Firewall
* Eventi di Advanced Threat Analytics
* Risultati della valutazione baseline
* Risultati di Antimalware Assessment
* Risultati della valutazione di aggiornamenti/patch
* Flussi di Syslog abilitati esplicitamente nell'agente

Microsoft è fortemente impegnata nella protezione della privacy e della sicurezza dei dati. Microsoft è conforme alle più rigorose linee guida sulla sicurezza e sulla conformità in tutte le fasi, dalla codifica all'esecuzione di un servizio.
Questo articolo illustra il modo in cui i dati vengono gestiti e protetti nella soluzione Sicurezza e controllo di OMS.

## <a name="data-sources"></a>Origini dati
La soluzione Sicurezza e controllo di OMS analizza i dati provenienti dalle macchine virtuali e dai computer fisici in cui è installato l'agente OMS. La soluzione Sicurezza e controllo di OMS può raccogliere informazioni di configurazione sugli eventi di sicurezza, ad esempio gli eventi Windows, i log di controllo, i log di IIS e i messaggi syslog. Esempi di tali dati sono: tipo e versione del sistema operativo, processi in esecuzione, nome computer, indirizzi IP, utente connesso e ID tenant.  

## <a name="data-protection"></a>Protezione dati
**Separazione dei dati:**i dati vengono mantenuti separati logicamente in ogni componente del servizio. Tutti i dati vengono contrassegnati in base all'organizzazione. Tale contrassegno persiste per tutto il ciclo di vita dei dati e viene applicato a ogni livello del servizio. 

**Accesso ai dati**: per fornire raccomandazioni sulla sicurezza e analizzare le potenziali minacce alla sicurezza, il personale Microsoft può accedere alle informazioni raccolte o analizzate dai servizi, inclusi i file di dump di arresto anomalo del sistema. Microsoft rispetta le [condizioni di Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e l'[Informativa sulla privacy](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), in cui è specificato che Microsoft non userà i dati del cliente o ne ricaverà informazioni per scopi pubblicitari o commerciali simili. Per fornire raccomandazioni sulla sicurezza e analizzare le potenziali minacce alla sicurezza, il personale Microsoft può accedere alle informazioni raccolte o analizzate dai servizi, inclusi i file di dump di arresto anomalo del sistema. Microsoft userà i dati dei clienti solo se necessari per fornire i servizi di Azure, incluse le finalità compatibili con la fornitura di tali servizi. L'utente mantiene tutti i diritti sui propri dati.

**Uso dei dati**: Microsoft usa modelli e intelligence per le minacce trovati in più tenant per migliorare le funzionalità di prevenzione e rilevamento, in base alle garanzie relative alla privacy descritte nell'[Informativa sulla privacy](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

> [!NOTE]
> La posizione dei dati viene configurata a livello di area di lavoro di OMS, durante la creazione dell'area di lavoro, che fa parte del processo di configurazione iniziale della soluzione Sicurezza e controllo di OMS.
> 
> 

## <a name="see-also"></a>Vedere anche
Questo documento illustra in che modo vengono gestiti e protetti i dati in OMS. Per altre informazioni sulla soluzione Sicurezza e controllo di OMS, vedere:

* [Panoramica di Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Monitoraggio e gestione degli avvisi di sicurezza nella soluzione Operations Management Suite per la sicurezza e il controllo](oms-security-responding-alerts.md)
* [Monitoraggio delle risorse nella soluzione Operations Management Suite per la sicurezza e il controllo](oms-security-monitoring-resources.md)




<!--HONumber=Dec16_HO1-->


