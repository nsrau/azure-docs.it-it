---
title: Amministrazione dell'array virtuale Microsoft Azure StorSimple Manager | Documentazione Microsoft
description: Informazioni su come gestire l'array virtuale StorSimple locale mediante il servizio Gestione servizi StorSimple nel portale di Azure.
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: a74a160eae88a2d03460a1346479c333d8f9d524
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Usare il servizio Gestione dispositivi StorSimple per amministrare l'array virtuale StorSimple
![flusso del processo di installazione](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Panoramica
Questo articolo descrive l'interfaccia del servizio Gestione dispositivi StorSimple, inclusa la modalità di connessione e le diverse opzioni disponibili. Fornisce inoltre collegamenti ai flussi di lavoro specifici che possono essere eseguiti mediante questa interfaccia.

Dopo aver letto l'articolo, l'utente sarà in grado di:

* Connettersi al servizio Gestione dispositivi StorSimple
* Passare all'interfaccia utente di Gestione dispositivi StorSimple
* Amministrare l'array virtuale StorSimple tramite il servizio Gestione dispositivi StorSimple

> [!NOTE]
> Per visualizzare le opzioni di gestione disponibili per il dispositivo StorSimple serie 8000, vedere [Utilizzare il servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Connettersi al servizio Gestione dispositivi StorSimple
Il servizio Gestione dispositivi StorSimple viene eseguito in Microsoft Azure ed è connesso a più array virtuali StorSimple. Per gestire questi dispositivi, si usa un portale di Microsoft Azure centrale in esecuzione in un browser. Per connettersi al servizio Gestione dispositivi StorSimple, effettuare le operazioni seguenti.

#### <a name="to-connect-to-the-service"></a>Per connettersi al servizio
1. Passare a [https://ms.portal.azure.com](https://ms.portal.azure.com).
2. Usando le credenziali dell'account Microsoft, accedere al portale di Microsoft Azure (in alto a destra del riquadro).
3. Passare a Esplora --> 'Filtro' in Gestione dispositivi StorSimple per visualizzare tutti i gestori di dispositivo in una determinata sottoscrizione.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Usare il servizio Gestione dispositivi StorSimple per eseguire attività di gestione
La tabella seguente illustra un riepilogo di tutte le attività di gestione comuni e dei flussi di lavoro complessi che possono essere eseguiti nel pannello di riepilogo del servizio Gestione dispositivi StorSimple. Queste attività sono organizzate in base ai pannelli in cui vengono avviate.

Per ulteriori informazioni su ogni flusso di lavoro, scegliere la procedura appropriata nella tabella.

#### <a name="storsimple-device-manager-workflows"></a>Flussi di lavoro di Gestione dispositivi StorSimple
| Se si desidera eseguire questa operazione... | Seguire questa procedura |
| --- | --- | --- |
| Creare un servizio</br>Eliminare un servizio</br>Ottenere la chiave di registrazione del servizio</br>Rigenerare la chiave di registrazione del servizio |[Distribuire il servizio Gestione dispositivi StorSimple](storsimple-virtual-array-manage-service.md) |
| Visualizzare i log attività |[Usare il riepilogo servizio StorSimple](storsimple-virtual-array-service-summary.md) |
| Disattivare un array virtuale</br>Eliminare un array virtuale |[Disattivare o eliminare un array virtuale](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Ripristino di emergenza e failover del dispositivo</br>Prerequisiti di failover</br>Ripristino di emergenza di continuità aziendale (BCDR)</br>Errori durante il ripristino di emergenza |[Ripristino di emergenza e failover del dispositivo per l'array virtuale StorSimple](storsimple-virtual-array-failover-dr.md) |
| Backup di condivisioni e volumi</br>Creazione di un backup manuale</br>Modificare la pianificazione dei backup</br>Visualizzare i backup esistenti |[Backup dell'array virtuale StorSimple](storsimple-virtual-array-backup.md) |
| Clonare condivisioni da un set di backup</br>Clonare volumi da un set di backup</br>Ripristino a livello di elementi (solo file server) |[Clonare da un backup dell'array virtuale StorSimple](storsimple-virtual-array-clone.md) |
| Informazioni sugli account di archiviazione</br>Aggiungere un account di archiviazione</br>Modificare un account di archiviazione</br>Eliminare un account di archiviazione |[Gestire un account di archiviazione per l'array virtuale StorSimple](storsimple-virtual-array-manage-storage-accounts.md) |
| Informazioni sui record di controllo di accesso</br>Aggiungere o modificare un record di controllo di accesso </br>Eliminare un record di controllo di accesso |[Gestire record di controllo di accesso per l'array virtuale StorSimple](storsimple-virtual-array-manage-acrs.md) |
| Visualizza i dettagli dei processi |[Gestire processi di array virtuali StorSimple](storsimple-virtual-array-manage-jobs.md) |
| Configurare le impostazioni degli avvisi</br>Ricevere notifiche di avviso</br>Gestisci avvisi</br>Esaminare gli avvisi |[Visualizzare e gestire gli avvisi per l'array virtuale StorSimple](storsimple-virtual-array-manage-alerts.md) |
| Modificare la password dell’amministratore del dispositivo |[Modificare la password amministratore del dispositivo dell'array virtuale StorSimple](storsimple-virtual-array-change-device-admin-password.md) |
| Installare gli aggiornamenti del software |[Aggiornare l'array virtuale](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> È necessario usare l' [interfaccia utente Web locale](storsimple-ova-web-ui-admin.md) per le attività seguenti:
> 
> * [Recuperare la chiave DEK del servizio](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Creare un pacchetto di supporto](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Arrestare e riavviare l'array virtuale](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sull'interfaccia utente Web e sul suo uso, vedere [Usare l'interfaccia utente Web per amministrare l'array virtuale StorSimple (anteprima)](storsimple-ova-web-ui-admin.md).

