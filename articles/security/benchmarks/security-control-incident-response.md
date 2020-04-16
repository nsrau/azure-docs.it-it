---
title: Azure Security Control - Incident Response
description: Risposta agli incidenti del controllo di sicurezza di AzureAzure Security Control Incident Response
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 993793d21e6253188dfc199d8701cbe117503517
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408426"
---
# <a name="security-control-incident-response"></a>Controllo di sicurezza: risposta agli incidenti

Proteggere le informazioni dell'organizzazione, nonché la sua reputazione, sviluppando e implementando un'infrastruttura di risposta agli incidenti (ad esempio, piani, ruoli definiti, formazione, comunicazioni, supervisione della gestione) per scoprire rapidamente un attacco e quindi contenere efficacemente il danno, sradicare la presenza dell'utente malintenzionato e ripristinare l'integrità della rete e dei sistemi.

## <a name="101-create-an-incident-response-guide"></a>10.1: Creare una guida alla risposta agli incidenti

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Customer |

Creare una guida alla risposta agli incidenti per l'organizzazione. Assicurarsi che siano presenti piani di risposta agli incidenti scritti che definiscono tutti i ruoli del personale, nonché fasi di gestione/gestione degli incidenti dal rilevamento alla revisione post-incidente.  

- [Linee guida per creare un processo di risposta agli incidenti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di Microsoft Security Response Center di un incidente](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Sfrutta la Guida alla gestione degli incidenti di sicurezza del computer del NIST per aiutare nella creazione del tuo piano di risposta agli incidenti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Creare una procedura di assegnazione dei punteggi degli eventi imprevisti e di assegnazione delle priorità

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 10.2 | 19.8 | Customer |

Il Centro sicurezza assegna una gravità a ogni avviso per consentire di assegnare una priorità a quali avvisi devono essere esaminati per primi. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o sull'analita utilizzato per emettere l'avviso, nonché il livello di probabilità che vi sia stato un intento dannoso dietro l'attività che ha portato all'avviso. 

Inoltre, contrassegnare chiaramente gli abbonamenti (per ex. produzione, non-prod) usando i tag e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure, in particolare quelle che elaborano dati sensibili.  È responsabilità dell'utente assegnare priorità alla correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [Usare tag per organizzare le risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="103-test-security-response-procedures"></a>10.3: Testare le procedure di risposta alla sicurezza

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 10.3 | 19 | Customer |

Eseguire esercizi per testare le funzionalità di risposta agli incidenti dei sistemi a cadenza regolare per proteggere le risorse di Azure.Conduct exercises to test your systems' incident response capabilities on a regular cadeence to help protect your Azure resources. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Pubblicazione del NIST - Guida ai programmi di test, formazione ed eserciziper piani e capacità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornire i dettagli di contatto degli eventi imprevisti di sicurezza e configurare le notifiche di avviso per gli incidenti di sicurezza

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 10.4 | 19.5 | Customer |

Le informazioni di contatto relative agli eventi imprevisti di protezione verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che i dati sono stati utilizzati da una parte illegale o non autorizzata. Esaminare gli incidenti dopo il fatto per assicurarsi che i problemi vengano risolti.

- [Come impostare il contatto di sicurezza del centro di sicurezza di AzureHow to set the Azure Security Center Security Contact](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporare avvisi di sicurezza nel sistema di risposta agli incidenti

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 10.5 | 19.6 | Customer |

Esportare gli avvisi e i suggerimenti del Centro sicurezza di Azure usando la funzionalità di esportazione continua per identificare i rischi per le risorse di Azure.Export your Azure Security Center alerts and recommendations using the Continuous Export feature to help identify risks to Azure resources. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.You may use the Azure Security Center data connector to stream the alerts to Azure Sentinel.

- [Come configurare l'esportazione continua](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Come trasmettere gli avvisi in Azure SentinelHow to stream alerts into Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizzare la risposta agli avvisi di sicurezza

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 10.6 | 19 | Customer |

Usare la funzionalità automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite "App per la logica" negli avvisi e suggerimenti per la sicurezza delle risorse di Azure.Use the Workflow Automation feature in Azure Security Center to automatically trigger responses via "Logic Apps" on security alerts and recommendations to protect your Azure resources.

- [Come configurare l'automazione del flusso di lavoro e le app per la logicaHow to configure Workflow Automation and Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)


## <a name="next-steps"></a>Passaggi successivi

- Vedere il prossimo controllo di sicurezza: [test di penetrazione e esercizi del team rosso](security-control-penetration-tests-red-team-exercises.md)