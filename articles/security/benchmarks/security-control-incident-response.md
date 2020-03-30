---
title: Azure Security Control - Incident Response
description: Risposta agli incidenti di controllo di sicurezza
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: fb3560aa2d3fbf48ab63c4da4d3a8d69cb677209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934408"
---
# <a name="security-control-incident-response"></a>Controllo di sicurezza: risposta agli incidenti

Proteggere le informazioni dell'organizzazione, così come la sua reputazione, sviluppando e implementando un'infrastruttura di risposta agli incidenti (ad esempio, piani, ruoli definiti, formazione, comunicazioni, supervisione della gestione) per scoprire rapidamente un attacco e quindi contenente efficacemente il danno, sradicando la presenza dell'aggressore e ripristinando l'integrità della rete e dei sistemi.

## <a name="101-create-an-incident-response-guide"></a>10.1: Creare una guida alla risposta agli incidenti

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Customer |

Creare una guida alla risposta agli incidenti per l'organizzazione. Assicurarsi che siano presenti piani di risposta agli incidenti scritti che definiscono tutti i ruoli del personale, nonché fasi di gestione/gestione degli incidenti dal rilevamento alla revisione post-incidente.

Come configurare le automazioni del flusso di lavoro all'interno del Centro sicurezza di Azure:How to configure Workflow Automations within Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Linee guida per creare un processo di risposta agli incidenti di sicurezza:Guidance on building your own security incident response process:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia di Microsoft Security Response Center di un incidente:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Il cliente può anche sfruttare la Guida alla gestione degli incidenti di sicurezza del COMPUTER del NIST per facilitare la creazione del proprio piano di risposta agli incidenti:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Creare una procedura di assegnazione dei punteggi degli eventi imprevisti e di assegnazione delle priorità

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 10.2 | 19.8 | Customer |

Il Centro sicurezza assegna una gravità a ogni avviso per consentire di assegnare una priorità a quali avvisi devono essere esaminati per primi. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o sull'analita utilizzato per emettere l'avviso, nonché il livello di probabilità che vi sia stato un intento dannoso dietro l'attività che ha portato all'avviso.

Inoltre, contrassegnare chiaramente gli abbonamenti (per ex. produzione, non-prod) e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure.

## <a name="103-test-security-response-procedures"></a>10.3: Testare le procedure di risposta alla sicurezza

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 10.3 | 19 | Customer |

Condurre esercizi per testare le capacità di risposta agli incidenti dei sistemi a una cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

Fare riferimento alla pubblicazione del NIST: Guida ai programmi di test, formazione ed eserciziper piani e funzionalità IT:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornire i dettagli di contatto degli eventi imprevisti di sicurezza e configurare le notifiche di avviso per gli incidenti di sicurezza

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 10.4 | 19.5 | Customer |

Le informazioni di contatto relative agli eventi imprevisti di protezione verranno utilizzate da Microsoft per contattare l'utente se microsoft Security Response Center (MSRC) rileva che i dati del cliente sono stati utilizzati da una parte non legale o non autorizzata.  Esaminare gli incidenti dopo il fatto per assicurarsi che i problemi vengano risolti.

Come impostare il contatto di sicurezza del centro di sicurezza di Azure:How to set the Azure Security Center Security Contact:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporare avvisi di sicurezza nel sistema di risposta agli incidenti

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 10.5 | 19.6 | Customer |

Esportare gli avvisi e i suggerimenti del Centro sicurezza di Azure usando la funzionalità di esportazione continua. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi Sentinel.You may use the Azure Security Center data connector to stream the alerts Sentinel.

Come configurare l'esportazione continua:

https://docs.microsoft.com/azure/security-center/continuous-export

Come trasmettere gli avvisi in Azure Sentinel:How to stream alerts into Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizzare la risposta agli avvisi di sicurezza

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 10.6 | 19 | Customer |

Usare la funzionalità Automazione flusso di lavoro &quot;nel&quot; Centro sicurezza di Azure per attivare automaticamente le risposte tramite app per la logica in avvisi e suggerimenti per la sicurezza.

Come configurare l'automazione del flusso di lavoro e le app per la logica:How to configure Workflow Automation and Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

## <a name="next-steps"></a>Passaggi successivi

Vedere il controllo di sicurezza successivo: [Test di penetrazione ed Esercizi del team rosso](security-control-penetration-tests-red-team-exercises.md)