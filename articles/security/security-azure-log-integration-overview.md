<properties
   pageTitle="Introduzione all'integrazione dei log di Microsoft Azure | Microsoft Azure"
   description="Informazioni sull'integrazione dei log di Azure, le funzionalità principali e il funzionamento."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# Introduzione all'integrazione dei log di Microsoft Azure (Anteprima)

Informazioni sull'integrazione dei log di Azure, le funzionalità principali e il funzionamento.

## Overview

I servizi sia PaaS (Platform as a Service, piattaforma distribuita come servizio) che IaaS (Infrastructure as a Service, infrastruttura distribuita come servizio) ospitati in Azure generano una grande quantità di dati nei log di sicurezza. Questi log contengono dati importanti che possono fornire informazioni utili e approfondite sulle violazioni dei criteri, le minacce interne ed esterne, i problemi di conformità alle normative e le anomalie della rete, dell'host e dell'attività dell'utente.

L'integrazione dei log di Azure consente di integrare log non elaborati delle risorse di Azure nei sistemi di gestione di informazioni ed eventi di sicurezza locali. L'integrazione dei log di Azure raccoglie i dati della Diagnostica di Azure dalle macchie virtuali Windows *(WAD)* e Linux *(LAD)* e anche i dati diagnostici dalle soluzioni dei partner come Web Application Firewall (WAF). Questa integrazione fornisce un dashboard unificato per tutti gli asset, locali o su cloud, consentendo di aggregare, correlare, analizzare e inviare avvisi per gli eventi di sicurezza.

![Integrazione dei log di Azure][1]

## Quali log è possibile integrare?

Azure produce registrazioni complete per ogni servizio di Azure. I log sono classificati in due tipi principali:

- **Log di gestione/controllo**, che offrono visibilità sulle operazioni CREATE, UPDATE e DELETE di Azure Resource Manager. I log di controllo di Azure sono un esempio di questo tipo di log.
- **Log del piano dati**, che offrono visibilità sugli eventi generati come parte dell'uso di una risorsa di Azure. Esempi di questi tipi di log sono rappresentati dal registro di sistema di Windows, dai log di sicurezza e delle applicazioni di una macchina virtuale.

L'integrazione dei log di Azure supporta attualmente l'integrazione dei log di controllo di Azure, dei log delle macchine virtuali e degli avvisi del Centro sicurezza di Azure.

## Passaggi successivi

In questo documento è stata presentata l'integrazione dei log di Azure. Per altre informazioni sull'integrazione dei log di Azure e sui tipi di log supportati, vedere gli argomenti seguenti:

- [Integrazione dei log di Microsoft Azure per i log di Azure (Anteprima)](https://www.microsoft.com/download/details.aspx?id=53324) – Area download per informazioni dettagliate, requisiti di sistema e istruzioni di installazione sull'integrazione dei log di Azure.
- [Introduzione all'integrazione dei log di Azure](security-azure-log-integration-get-started.md) - Questa esercitazione illustra come installare l'integrazione dei log di Azure e integrare i log dall'archiviazione di Azure, i log di controllo di Azure e gli avvisi del Centro sicurezza.
- [Passaggi per la configurazione dei partner](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – Questo post di blog mostra come configurare l'integrazione dei log di Azure per lavorare con le soluzioni partner Splunk, HP ArcSight e IBM QRadar.
- [Domande frequenti sull'integrazione dei log di Azure](security-azure-log-integration-faq.md) - Queste domande frequenti riguardano l'integrazione dei log di Azure.
- [Integrazione degli avvisi del Centro sicurezza con l'integrazione dei log di Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) - Questo documento mostra come sincronizzare gli avvisi del Centro sicurezza di Azure, insieme agli eventi di sicurezza delle macchine virtuali raccolti da Diagnostica di Azure e dai log di controllo di Azure, con la propria soluzione SIEM o di analisi dei log.
- [Nuove funzionalità per Diagnostica di Azure e i log di controllo di Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) - Questo post di blog presenta i log di controllo di Azure e altre funzionalità che consentono di ottenere informazioni dettagliate sulle operazioni delle risorse di Azure.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

<!---HONumber=AcomDC_0824_2016-->