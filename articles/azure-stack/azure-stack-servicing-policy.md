---
title: Azure Stack di manutenzione dei criteri | Documenti Microsoft
description: Informazioni sullo Stack di Azure per la manutenzione dei criteri e come mantenere un sistema integrato in uno stato supportato.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mabrigg
ms.openlocfilehash: 2c10dcf185c62f3672be80ad2e3d049eae82fe6b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-servicing-policy"></a>Azure Stack di manutenzione dei criteri
Questo articolo descrive i criteri di manutenzione per i sistemi Azure Stack integrato e le operazioni da eseguire per mantenere il sistema in uno stato supportato. 

## <a name="update-package-types"></a>Tipi di pacchetto di aggiornamento

Esistono due tipi di pacchetti di aggiornamento per i sistemi integrati; Gli aggiornamenti software Microsoft e gli aggiornamenti specifici per il fornitore dell'hardware (OEM) original equipment manufacturer, ad esempio driver e firmware. Questi aggiornamenti vengono distribuiti come pacchetti di aggiornamento separati dello Stack di Azure e sono gestiti in modo indipendente.

- **Gli aggiornamenti software Microsoft**. Microsoft è responsabile per il ciclo di vita di manutenzione end-to-end per i pacchetti di aggiornamento del software Microsoft. Questi pacchetti possono includere gli ultimi aggiornamenti di sicurezza di Windows Server, non correlato alla sicurezza aggiornamenti e gli aggiornamenti di funzionalità dello Stack di Azure. È possibile scaricare i pacchetti di aggiornamento theses direttamente da Microsoft.
- **Aggiornamenti fornito dal fornitore dell'hardware OEM**. I partner hardware di Azure Stack sono responsabili per l'end-to-end per la manutenzione del ciclo di vita (incluse indicazioni) per il firmware correlati all'hardware e i pacchetti di aggiornamento del driver. Inoltre, i partner hardware di Azure Stack proprietari e gestire linee guida per tutti i software e hardware nell'host del ciclo di vita dell'hardware. Il fornitore dell'hardware OEM ospita tali pacchetti nel proprio sito di download di aggiornamento.

## <a name="update-package-release-cadence"></a>Rilasci del pacchetto di aggiornamento

Microsoft prevede di rilasciare i pacchetti di aggiornamento software a un ritmo mensile. Tuttavia, è possibile avere nessuna o più versioni di aggiornamento in un mese. I fornitori di hardware OEM rilasciare gli aggiornamenti in base alle esigenze.

Un pacchetto di aggiornamento Microsoft è la seguente convenzione di denominazione per identificare facilmente la data di rilascio:

*MajorProductVersion.MinorProductVersion.YYMMDD.BuildNumber*

Ad esempio, un aggiornamento del software Microsoft ha rilasciato il 15 giugno 2017 avrebbe la versione "1.0.170615.1".

## <a name="keep-your-system-under-support"></a>Mantenere il sistema di supporto
Per continuare a ricevere supporto, è necessario mantenere la distribuzione di Azure Stack corrente. I criteri di rinvio degli aggiornamenti sono che Azure Stack deve rimanere nel supporto, deve eseguire la versione rilasciata di recente di aggiornamento o eseguire uno dei due versioni di aggiornamento principale precedente.  Gli aggiornamenti rapidi non vengono considerati le versioni di aggiornamento principale.  Se il cloud di Azure Stack si trova dietro dal *più di due aggiornamenti*, è considerata non conforme e si deve aggiornare almeno la versione minima supportata per ricevere assistenza. 

Ad esempio, se la versione dell'aggiornamento più recente disponibile è 1805 e due pacchetti di aggiornamento precedente sono stati versioni 1804 e 1803, 1803 sia 1804 rimangono in modalità di supporto. Tuttavia, 1802 è più supportata. I criteri vale quando non si verifica alcuna versione per un mese o due. Ad esempio, se la versione corrente è 1805 e si è verificato alcun versione 1804, precedente due pacchetti di aggiornamento di 1803 e 1802 rimarrebbero in modalità di supporto.

I pacchetti di aggiornamento software Microsoft sono non cumulativi e richiedono che il pacchetto di aggiornamento precedente come prerequisito. Se si decide di rinviare gli aggiornamenti di uno o più, prendere in considerazione la fase di esecuzione complessivo se si vuole ottenere la versione più recente. 

La tabella seguente illustra l'esempio aggiornamento pacchetto versioni, i relativi prerequisiti e la versione minima supportata del sistema deve essere in per gestire il supporto. La tabella è basata sulla versione iniziale dei sistemi Azure Stack integrata (build 1708), con il primo aggiornamento pacchetto rilascio (1709) nel mese di settembre 2017. 

| Pacchetto di aggiornamento più recente (*esempio*) | Prerequisito | Versione minima supportata |
| -- | -- | -- |
| 1710 | 1709 | N/D |
| 1711 | 1710 | 1709 |
| 1712 | 1711 | 1710 |
| 1802 | 1712 | 1711 |
| 1803 | 1802 | 1712 |
| 1804 | 1803 | 1802 |
| 1805 | 1804 | 1803 |
| | | 
Nella tabella precedente, non vi è nessuna versione di rilascio 1801.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire gli aggiornamenti in Azure Stack](azure-stack-updates.md)


