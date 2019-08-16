---
title: Soluzione VMware di Azure di CloudSimple-monitoraggio dell'attività del cloud privato
description: Descrive le informazioni disponibili sulle attività nella soluzione VMware di Azure in base all'ambiente CloudSimple, inclusi gli avvisi, gli eventi, le attività e il controllo.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ddb3741c987e839fafb8bc222231547988d72f01
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543768"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>Monitora l'attività CloudSimple della soluzione VMware

I log attività di CloudSimple forniscono informazioni dettagliate sulle operazioni eseguite nel portale di CloudSimple.  L'elenco include gli avvisi, gli eventi, le attività e il controllo.  Usare i log attività per determinare chi, quando e quali operazioni sono state eseguite.  I log attività non includono le operazioni di lettura eseguite da un utente.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Accedere al portale di CloudSimple

Accedere al [portale di CloudSimple](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Informazioni sulle attività

Per accedere alle pagine attività, selezionare **attività** dal menu laterale.

![Panoramica della pagina attività](media/activity-page-overview.png)

Per visualizzare i dettagli relativi a una delle attività nella pagina attività, selezionare l'attività. A destra verrà visualizzato un pannello Dettagli. Le azioni nel pannello dipendono dal tipo di attività. Fare clic su **X** per chiudere il pannello.

Fare clic su un'intestazione di colonna per ordinare la visualizzazione.  È possibile filtrare le colonne per valori specifici da visualizzare.  Scaricare il report attività facendo clic sull'icona **Scarica come CSV** .

## <a name="alerts"></a>Avvisi

Gli avvisi sono notifiche di eventuali attività significative nell'ambiente di CloudSimple.  Gli avvisi includono eventi che influiscono sulla fatturazione o sull'accesso utente.

Per confermare gli avvisi e rimuoverli dall'elenco, selezionare uno o più nell'elenco e fare clic su **conferma**.

Per gli avvisi sono disponibili le seguenti colonne di informazioni. Fare clic su **modifica colonne** e selezionare le colonne che si desidera visualizzare.

| Colonna | DESCRIZIONE |
------------ | ------------- |
| Tipo di avviso | Categoria di avviso.|
| Time | Ora in cui si è verificato l'avviso. |
| severity | Importanza dell'avviso.|
| Nome risorsa | Nome assegnato alla risorsa, ad esempio il nome del cloud privato. |
| Tipo di risorsa | Categoria della risorsa: Cloud privato, rack cloud. |
| ID risorsa | Identificatore della risorsa. |
| Descrizione | Descrizione dell'elemento che ha attivato l'avviso. |
| Confermato | Indica se l'avviso è stato riconosciuto. |

## <a name="events"></a>Eventi

Eventi Mostra l'attività dell'utente e del sistema nel portale di CloudSimple. Nella pagina eventi sono elencate le attività associate a una risorsa specifica e la gravità dell'effetto.

Per gli avvisi sono disponibili le seguenti colonne di informazioni. Fare clic su **modifica colonne** e selezionare le colonne che si desidera visualizzare.

| Colonna | DESCRIZIONE |
------------ | ------------- |
| Time | Data e ora in cui si è verificato l'evento. |
| Tipo evento | Codice numerico che identifica l'evento. |
| severity | Gravità dell'evento.|
| Nome risorsa | Nome assegnato alla risorsa, ad esempio il nome del cloud privato. |
| Tipo di risorsa | Categoria della risorsa: Cloud privato, rack cloud. |
| Descrizione | Descrizione dell'elemento che ha attivato l'avviso. |

## <a name="tasks"></a>Attività

Le attività sono attività del cloud privato che dovrebbero richiedere oltre 30 secondi per il completamento. (Le attività che dovrebbero richiedere meno di 30 secondi vengono segnalate solo come eventi). Aprire le pagine attività per tenere traccia dello stato di avanzamento delle attività per il cloud privato.

Per gli avvisi sono disponibili le seguenti colonne di informazioni. Fare clic su **modifica colonne** e selezionare le colonne che si desidera visualizzare.

| Colonna | Descrizione |
------------ | ------------- |
| ID attività | Identificatore univoco per l'attività. |
| Operazione | Azione eseguita dall'attività. |
| Utente | Utente assegnato per completare l'attività. |
| Nome risorsa | Nome assegnato alla risorsa. |
| Tipo di risorsa | Categoria della risorsa: Cloud privato, rack cloud. |
| ID risorsa | Identificatore della risorsa. |
| Start | Ora di inizio dell'attività. |
| Fine | Ora di fine dell'attività. |
| Stato | Stato corrente dell'attività. |
| Tempo trascorso | Tempo richiesto per il completamento dell'attività (se completata) o in corso (se in corso). |
| Descrizione | Descrizione dell'attività. |

## <a name="audit"></a>Audit

I log di controllo tengono traccia delle attività degli utenti. È possibile utilizzare i log di controllo per monitorare l'attività dell'utente per tutti gli utenti.

Per gli avvisi sono disponibili le seguenti colonne di informazioni. Fare clic su **modifica colonne** e selezionare le colonne che si desidera visualizzare.

| Colonna | Descrizione |
------------ | ------------- |
| Time | Ora della voce di controllo. |
| Operazione | Azione eseguita dall'attività. |
| Utente | Utente assegnato all'attività. |
| Nome risorsa | Nome assegnato alla risorsa. |
| Tipo di risorsa | Categoria della risorsa: Cloud privato, rack cloud. |
| ID risorsa | Identificatore della risorsa. |
| Risultato | Risultato dell'attività, ad esempio **Success**. |
| Tempo impiegato | Tempo necessario per completare l'attività. |
| DESCRIZIONE | Descrizione dell'azione. |

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare macchine virtuali VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Altre informazioni sui [cloud privati](cloudsimple-private-cloud.md)
