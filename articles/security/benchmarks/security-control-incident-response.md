---
title: Controllo di sicurezza di Azure-risposta agli eventi imprevisti
description: Risposta agli eventi imprevisti del controllo di sicurezza
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: b027a668403f47e9ffb824179ae54b89cded7a0c
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564231"
---
# <a name="security-control-incident-response"></a>Controllo di sicurezza: risposta agli eventi imprevisti

Proteggi le informazioni dell'organizzazione, così come la sua reputazione, sviluppando e implementando un'infrastruttura di risposta agli eventi imprevisti (ad esempio, piani, ruoli definiti, formazione, comunicazioni, supervisione della gestione) per individuare rapidamente un attacco e quindi contenimento efficace del danno, eliminazione della presenza dell'utente malintenzionato e ripristino dell'integrità della rete e dei sistemi.

## <a name="101-create-an-incident-response-guide"></a>10,1: creare una guida alla risposta agli eventi imprevisti

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 10.1 | 19,1, 19,2, 19,3 | Customer |

Creare una guida alla risposta agli eventi imprevisti per la propria organizzazione. Verificare che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione/gestione degli eventi imprevisti dal rilevamento alla verifica post-evento imprevisto.

Come configurare le automazioni del flusso di lavoro nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Linee guida per la creazione di un processo di risposta agli eventi imprevisti di sicurezza:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia di un evento imprevisto di Microsoft Security Response Center:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: creare una procedura per assegnazione di punteggi e assegnazione di priorità

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 10.2 | 19,8 | Customer |

Il Centro sicurezza assegna una gravità a ogni avviso per facilitare la priorità degli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, nonché sul livello di confidenza causato da un intento dannoso dietro l'attività che ha portato all'avviso.

Inoltre, contrassegnare chiaramente le sottoscrizioni (per es. produzione, non prod) e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure.

## <a name="103-test-security-response-procedures"></a>10,3: testare le procedure di risposta alla sicurezza

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 10.3 | 19 | Customer |

Eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare. Identificare i punti deboli e i gap e rivedere il piano in base alle esigenze.

Fare riferimento alla pubblicazione del NIST: Guida ai programmi di test, formazione e esercizio per i piani IT e le funzionalità:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10,4: specificare i dettagli del contatto per gli eventi imprevisti della sicurezza e configurare le notifiche di avviso &nbsp;per gli eventi

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 10,4 | 19,5 | Customer |

Le informazioni di contatto per gli eventi imprevisti di sicurezza verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che è stato eseguito l'accesso ai dati del cliente da parte di utenti non autorizzati o non autorizzati.  Esaminare gli eventi imprevisti dopo il fatto di garantire la risoluzione dei problemi.

Come impostare il contatto di sicurezza del Centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 10.5 | 19,6 | Customer |

Esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo e continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere la sentinella degli avvisi.

Come configurare l'esportazione continua:

https://docs.microsoft.com/azure/security-center/continuous-export

Come trasmettere gli avvisi in Sentinel di Azure:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

## <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizzare la risposta agli avvisi di sicurezza

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 10,6 | 19 | Customer |

Usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte tramite &quot;app per la logica&quot; su avvisi e raccomandazioni di sicurezza.

Come configurare l'automazione del flusso di lavoro e le app per la logica:

https://docs.microsoft.com/azure/security-center/workflow-automation

## <a name="next-steps"></a>Passaggi successivi

Vedere il controllo di sicurezza successivo: [test di penetrazione e esercizi del Red Team](security-control-penetration-tests-red-team-exercises.md)