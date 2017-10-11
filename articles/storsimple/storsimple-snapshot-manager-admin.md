---
title: Amministrazione di StorSimple Snapshot Manager | Microsoft Docs
description: "Viene fornita una panoramica e dei collegamenti a ulteriori informazioni sulle attività di amministrazione della soluzione Snapshot StorSimple Manager e sui flussi di lavoro."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: v-sharos
ms.openlocfilehash: a99b3d7336c3dc1a1f249915d6971a49f4b69be6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Usare Gestione Snapshot StorSimple per amministrare la soluzione di StorSimple

## <a name="overview"></a>Panoramica
Gestione Snapshot StorSimple è uno snap-in Microsoft Management Console (MMC) che semplifica la protezione dei dati e la gestione dei backup in un ambiente di Microsoft Azure StorSimple. Con Gestione snapshot StorSimple è possibile gestire i dati di Microsoft Azure StorSimple nel datacenter e nel cloud come una singola soluzione di archiviazione integrata, in modo da semplificare i processi di backup e di ridurre i costi.

La console di gestione centrale di gestione Snapshot StorSimple che consente di creare copie di backup coerenti, in un momento di variabile locale e i dati nella cloud. Ad esempio, è possibile utilizzare la console per:

* Configurare, eseguire il backup ed eliminare volumi.
* Configurare gruppi di volumi per assicurarsi che i dati di cui è stato eseguito il backup siano coerenti con l'applicazione.
* Gestire criteri di backup in modo che il backup dei dati venga eseguito in una pianificazione predeterminata.
* Creare copie indipendenti dei dati, che possono essere archiviate nel cloud e utilizzate per il ripristino di emergenza.

In questo articolo vengono forniti collegamenti alle esercitazioni che descrivono gestione Snapshot StorSimple e come utilizzarlo per il completamento dei flussi di lavoro e attività di amministrazione del sistema.

* Per ulteriori informazioni sull'architettura e i componenti di gestione Snapshot StorSimple, vedere [che cos'è Gestione Snapshot StorSimple?](storsimple-what-is-snapshot-manager.md) 
* Per scaricare gestione Snapshot StorSimple, visitare [la pagina di download di gestione Snapshot StorSimple](https://www.microsoft.com/download/details.aspx?id=44220).
* Per le procedure di distribuzione di gestione Snapshot StorSimple, visitare [Distribuire Gestione snapshot StorSimple](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> Non è possibile usare Gestione Snapshot StorSimple per gestire gli array virtuali di Microsoft Azure StorSimple (noti anche come dispositivi virtuali StorSimple locali).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>Flussi di lavoro e attività di gestione Snapshot StorSimple
È possibile utilizzare Gestione Snapshot StorSimple per monitorare e gestire processi di backup correnti, pianificati e completati. Gestione snapshot StorSimple fornisce inoltre un catalogo di fino a 64 backup completati. È possibile utilizzare il catalogo per individuare e ripristinare volumi o singoli file. 

| SE SI DESIDERA ESEGUIRE QUESTA OPERAZIONE... | UTILIZZARE QUESTA ESERCITAZIONE... |
|:--- |:--- |
| Ulteriori informazioni su StorSimple Snapshot Manager |[Che cos’è Gestione snapshot StorSimple? ](storsimple-what-is-snapshot-manager.md) |
| Installare Gestione Snapshot StorSimple<br>Reinstallare StorSimple Snapshot Manager<br>rimuovere Gestione Snapshot StorSimple |[Distribuire Gestione snapshot StorSimple](storsimple-snapshot-manager-deployment.md) |
| Usare Gestione Snapshot StorSimple menu e funzionalità:<ul><li>Barra dei menu</li><li>Barra degli strumenti</li><li>Riquadro Ambito</li><li>Riquadro Risultati</li><li>Riquadro Azioni</li><li>Navigazione da tastiera e tasti di scelta rapida</li></ul> |[Interfaccia utente di gestione Snapshot StorSimple](storsimple-use-snapshot-manager.md) |
| Utilizzare le funzionalità comuni di MMC incluse in Gestione Snapshot StorSimple:<ul><li>View</li><li>Nuova finestra da qui</li><li>Aggiorna</li><li>Esporta elenco</li><li>Guida</li></ul> |[Utilizzare le azioni del menu MMC di gestione Snapshot StorSimple](storsimple-snapshot-manager-mmc-menu.md) |
| Aggiunta o sostituzione di un dispositivo<br>Collegare un dispositivo<br>Verificare i gruppi di volumi importati<br>Aggiornamento dei dispositivi connessi<br>Autenticazione di un dispositivo<br>Visualizzazione dei dettagli del dispositivo<br>Eliminazione della configurazione di un dispositivo<br>Modifica della password di un dispositivo<br>Sostituzione di un dispositivo guasto<br> |[Utilizzare Gestione Snapshot StorSimple per connettersi e gestire i dispositivi StorSimple](storsimple-snapshot-manager-manage-devices.md) |
| Montare i volumi<br>Visualizzare informazioni sui volumi<br>Eliminare un volume<br>Ripetere la scansione dei volumi<br>Configurare ed eseguire il backup di un volume di base<br>Configura e backup di un volume con mirroring dinamico |[Utilizzare Gestione Snapshot StorSimple per visualizzare e gestire i volumi](storsimple-snapshot-manager-manage-volumes.md) |
| Visualizzazione dei gruppi di volumi<br>Creare un gruppo di volumi<br>Eseguire il backup di un gruppo di volumi<br>Modificare un gruppo di volumi<br>Eliminare un gruppo di volumi |[Utilizzare Gestione Snapshot StorSimple per creare e gestire gruppi di volumi](storsimple-snapshot-manager-manage-volume-groups.md) |
| Creare un criterio di backup <br>Modifica di un criterio di backup<br>Eliminare un criterio di backup |[Utilizzare Gestione Snapshot StorSimple per creare e gestire criteri di backup](storsimple-snapshot-manager-manage-backup-policies.md) |
| Visualizzare e gestire processi di backup pianificati<br>Visualizzare e gestire processi di backup recenti<br>visualizzazione e gestire processi di backup attualmente in esecuzione |[Utilizzo di Gestione snapshot StorSimple per visualizzare e gestire i processi di backup](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Ripristino di un volume<br>Clonazione di un volume o di un gruppo di volumi<br>Eliminazione di un backup<br>Recupero di un file<br>Ripristino del database di StorSimple Snapshot Manager |[Uso di Gestione Snapshot StorSimple per gestire il catalogo di backup](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Passaggi successivi
[Scaricare Gestione snapshot StorSimple](https://www.microsoft.com/download/details.aspx?id=44220).

