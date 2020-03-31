---
title: Monitorare l'attività del cloud privato
titleSuffix: Azure VMware Solution by CloudSimple
description: Descrive le informazioni disponibili sull'attività nell'ambiente Azure VMware Solution by CloudSimple, inclusi avvisi, eventi, attività e controllo.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1228f3a54e02d4fe7a5133e2bfba55c38e34718a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019673"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>Monitorare l'attività di VMware da CloudSimple

I log attività CloudSimple forniscono informazioni dettagliate sulle operazioni eseguite nel portale CloudSimple.CloudSimple activity logs provide an insight into operations done on CloudSimple portal.  L'elenco include avvisi, eventi, attività e controllo.  Utilizzare i log attività per determinare chi, quando e quali operazioni sono state eseguite.  I log attività non includono operazioni di lettura eseguite da un utente.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di [https://portal.azure.com](https://portal.azure.com)Azure all'indirizzo .

## <a name="access-the-cloudsimple-portal"></a>Accedere al portale di CloudSimple

Accedere al [portale CloudSimple](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Informazioni sull'attività

Per accedere alle pagine Attività, selezionare **Attività** nel menu laterale.

![Panoramica della pagina Attività](media/activity-page-overview.png)

Per visualizzare i dettagli relativi a qualsiasi attività nella pagina dell'attività, selezionare l'attività. A destra si apre un pannello dei dettagli. Le azioni nel pannello dipendono dal tipo di attività. Fare clic su **X** per chiudere il pannello.

Fare clic su un'intestazione di colonna per ordinare la visualizzazione.  È possibile filtrare le colonne per valori specifici da visualizzare.  Scarica il rapporto attività facendo clic sull'icona **Scarica come CSV.**

## <a name="alerts"></a>Avvisi

Gli avvisi sono notifiche di qualsiasi attività significativa nell'ambiente CloudSimple.Alerts are notifications of any significant activity in your CloudSimple environment.  Gli avvisi includono eventi che influiscono sulla fatturazione o sull'accesso degli utenti.

Per confermare gli avvisi e rimuoverli dall'elenco, selezionarne uno o più dall'elenco e fare clic su **Conferma**.

Le seguenti colonne di informazioni sono disponibili per gli avvisi. Fare clic su **Modifica colonne** e selezionare le colonne che si desidera visualizzare.

| Colonna | Descrizione |
------------ | ------------- |
| Tipo di avviso | Categoria di avviso.|
| Tempo | Ora in cui si è verificato l'avviso. |
| Gravità | Significato dell'avviso.|
| Nome risorsa | Nome assegnato alla risorsa, ad esempio il nome del cloud privato. |
| Tipo di risorsa | Categoria di risorse: Private Cloud, Cloud Rack. |
| ID risorsa | Identificatore della risorsa. |
| Descrizione | Descrizione di ciò che ha attivato l'avviso. |
| Confermato | Indica se l'avviso viene riconosciuto. |

## <a name="events"></a>Events

Gli eventi mostrano l'attività dell'utente e del sistema nel portale CloudSimple. La pagina Eventi elenca l'attività associata a una risorsa specifica e la gravità dell'impatto.

Le seguenti colonne di informazioni sono disponibili per gli avvisi. Fare clic su **Modifica colonne** e selezionare le colonne che si desidera visualizzare.

| Colonna | Descrizione |
------------ | ------------- |
| Tempo | Data e ora in cui si è verificato l'evento. |
| Tipo evento | Codice numerico che identifica l'evento. |
| Gravità | Gravità dell'evento.|
| Nome risorsa | Nome assegnato alla risorsa, ad esempio il nome del cloud privato. |
| Tipo di risorsa | Categoria di risorse: Private Cloud, Cloud Rack. |
| Descrizione | Descrizione di ciò che ha attivato l'avviso. |

## <a name="tasks"></a>Attività

Le attività sono attività di private cloud che dovrebbero richiedere 30 secondi o più per essere completate. Le attività che dovrebbero richiedere meno di 30 secondi vengono segnalate solo come eventi. Aprire le pagine Attività per tenere traccia dello stato di avanzamento delle attività per il cloud privato.

Le seguenti colonne di informazioni sono disponibili per gli avvisi. Fare clic su **Modifica colonne** e selezionare le colonne che si desidera visualizzare.

| Colonna | Descrizione |
------------ | ------------- |
| ID attività | Identificatore univoco dell'attività. |
| Operazione | Azione eseguita dall'attività. |
| Utente | Utente assegnato per completare l'attività. |
| Nome risorsa | Nome assegnato alla risorsa. |
| Tipo di risorsa | Categoria di risorse: Private Cloud, Cloud Rack. |
| ID risorsa | Identificatore della risorsa. |
| Inizia | Ora di inizio dell'attività. |
| Fine | Ora di fine dell'attività. |
| Stato | Stato corrente dell'attività. |
| Tempo trascorso | Tempo impiegato dall'attività per completare (se completata) o sta attualmente impiegando (se in corso). |
| Descrizione | Descrizione attività. |

## <a name="audit"></a>Audit

I log di controllo tengono traccia dell'attività degli utenti. È possibile utilizzare i registri di controllo per monitorare l'attività degli utenti per tutti gli utenti.

Le seguenti colonne di informazioni sono disponibili per gli avvisi. Fare clic su **Modifica colonne** e selezionare le colonne che si desidera visualizzare.

| Colonna | Descrizione |
------------ | ------------- |
| Tempo | Ora della voce di controllo. |
| Operazione | Azione eseguita dall'attività. |
| Utente | Utente assegnato all'attività. |
| Nome risorsa | Nome assegnato alla risorsa. |
| Tipo di risorsa | Categoria di risorse: Private Cloud, Cloud Rack. |
| ID risorsa | Identificatore della risorsa. |
| Risultato | Risultato dell'attività, ad esempio **Operazione riuscita**. |
| Tempo impiegato | È ora di completare l'attività. |
| Descrizione | Descrizione dell'azione. |

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare VM di VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* Ulteriori informazioni sui [cloud privati](cloudsimple-private-cloud.md)
