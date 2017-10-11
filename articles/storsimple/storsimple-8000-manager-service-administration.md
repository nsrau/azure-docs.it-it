---
title: Amministrazione del servizio Gestione dispositivi StorSimple | Microsoft Docs
description: Informazioni su come gestire il dispositivo StorSimple tramite il servizio Gestione dispositivi StorSimple nel portale di Azure.
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: alkohli
ms.openlocfilehash: 0e7d7f44a70278a7777ba6c32c8e546074953fdc
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Usare il servizio Gestione dispositivi StorSimple per amministrare il dispositivo StorSimple

## <a name="overview"></a>Panoramica

Questo articolo descrive l'interfaccia del servizio Gestione dispositivi StorSimple, inclusa la modalità di connessione, le diverse opzioni disponibili e i collegamenti a flussi di lavoro specifici che possono essere eseguiti tramite questa interfaccia. Questa guida è applicabile al dispositivo StorSimple fisico e virtuale.

Una volta letto l'articolo, si sarà in grado di:

* Connettersi al servizio Gestione dispositivi StorSimple
* Gestire il dispositivo StorSimple tramite il servizio Gestione dispositivi StorSimple

## <a name="connect-to-storsimple-device-manager-service"></a>Connettersi al servizio Gestione dispositivi StorSimple

Il servizio Gestione dispositivi StorSimple viene eseguito in Microsoft Azure e si connette a più dispositivi StorSimple. Per gestire questi dispositivi, si usa un portale di Microsoft Azure centrale in esecuzione in un browser. Per connettersi al servizio Gestione dispositivi StorSimple, effettuare le operazioni seguenti.

#### <a name="to-connect-to-the-service"></a>Per connettersi al servizio
1. Passare a [https://portal.azure.com/](https://portal.azure.com/).
2. Usando le credenziali dell'account Microsoft, accedere al portale di Microsoft Azure (in alto a destra del riquadro).
3. Scorrere verso il basso il riquadro di spostamento sinistro per accedere al servizio Gestione dispositivi StorSimple.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Gestire il dispositivo StorSimple tramite il servizio Gestione dispositivi StorSimple

La tabella seguente illustra un riepilogo di tutte le attività di gestione comuni e dei flussi di lavoro complessi che possono essere eseguiti nell'interfaccia utente del servizio Gestione dispositivi StorSimple. Queste attività sono organizzate in base ai pannelli dell'interfaccia utente in cui vengono avviate.

Per ulteriori informazioni su ogni flusso di lavoro, scegliere la procedura appropriata nella tabella.

#### <a name="storsimple-device-manager-workflows"></a>Flussi di lavoro di Gestione dispositivi StorSimple

| Se si desidera eseguire questa operazione... | Usare questa procedura. |
| --- | --- |
| Creare un servizio</br>Eliminare un servizio</br>Ottenere la chiave di registrazione del servizio</br>Rigenerare la chiave di registrazione |[Distribuire il servizio Gestione dispositivi StorSimple](storsimple-8000-manage-service.md) |
| Visualizzare i log attività |[Usare il riepilogo del servizio Gestione dispositivi StorSimple](storsimple-8000-service-dashboard.md) |
| Modificare la chiave DEK del servizio</br>Visualizzare i log delle operazioni |[Usare il dashboard del servizio Gestione dispositivi StorSimple](storsimple-8000-service-dashboard.md) |
| Disattivare un dispositivo</br>Eliminare un dispositivo |[Disattivare o eliminare un dispositivo](storsimple-8000-deactivate-and-delete-device.md) |
| Informazioni sul failover del dispositivo e sul ripristino di emergenza</br>Failover in un dispositivo fisico</br>Failover in un dispositivo virtuale</br>Ripristino di emergenza di continuità aziendale (BCDR) |[Failover e ripristino di emergenza per il dispositivo StorSimple](storsimple-8000-device-failover-disaster-recovery.md) |
| Elenco di backup per un volume</br>Selezionare un set di backup</br>Eliminare un set di backup |[Gestire i backup](storsimple-8000-manage-backup-catalog.md) |
| Clonare un volume |[Clonare un volume](storsimple-8000-clone-volume-u2.md) |
| Ripristinare un set di backup |[Ripristinare un set di backup](storsimple-8000-restore-from-backup-set-u2.md) |
| Informazioni sugli account di archiviazione</br>Aggiungere un account di archiviazione</br>Modificare un account di archiviazione</br>Eliminare un account di archiviazione</br>Rotazione delle chiavi degli account di archiviazione |[Gestire gli account di archiviazione](storsimple-8000-manage-storage-accounts.md) |
| Informazioni sui modelli di larghezza di banda</br>Aggiunta di un modello di larghezza di banda</br>Modifica di un modello di larghezza di banda</br>Eliminazione di un modello di larghezza di banda</br>Utilizzo di un modello di larghezza di banda predefinito</br>Creazione di un modello di larghezza di banda giornaliero che viene avviato a un determinato orario |[Gestire i modelli di larghezza di banda](storsimple-8000-manage-bandwidth-templates.md) |
| Informazioni sui record di controllo di accesso</br>Creare un record di controllo di accesso</br>Modificare un record di controllo di accesso</br>Eliminare un record di controllo di accesso |[Gestire record di controllo di accesso](storsimple-8000-manage-acrs.md) |
| Visualizzare i dettagli dei processi</br>Annullare un processo |[Gestire i processi](storsimple-8000-manage-jobs-u2.md) |
| Ricevere notifiche di avviso</br>Gestisci avvisi</br>Esaminare gli avvisi |[Visualizzare e gestire gli avvisi di StorSimple](storsimple-8000-manage-alerts.md) |
| Creare grafici di monitoraggio |[Monitoraggio del dispositivo StorSimple](storsimple-monitor-device.md) |
| Aggiungere un contenitore del volume</br>Modificare un contenitore di volumi</br>Eliminare un contenitore di volumi |[Gestisci contenitori dei volumi](storsimple-8000-manage-volume-containers.md) |
| Aggiungere un volume</br>Modificare un volume</br>Portare un volume offline</br>Eliminare un volume</br>Monitorare un volume |[Gestire i volumi](storsimple-8000-manage-volumes-u2.md) |
| Modificare le impostazioni del dispositivo</br>Modificare le impostazioni di tempo</br>Modificare le impostazioni DNS.md</br>Configurare le interfacce di rete |[Modificare la configurazione per il dispositivo StorSimple](storsimple-8000-modify-device-config.md) |
| Visualizzare le impostazioni del proxy web |[Configurare il proxy Web per il dispositivo](storsimple-8000-configure-web-proxy.md) |
| Modificare la password amministratore del dispositivo</br>Modificare la password di Gestione snapshot StorSimple |[Modificare le password di StorSimple](storsimple-8000-change-passwords.md) |
| Configurare la gestione remota |[Connettersi in remoto al dispositivo StorSimple](storsimple-8000-remote-connect.md) |
| Configurare le impostazioni degli avvisi |[Visualizzare e gestire gli avvisi di StorSimple](storsimple-8000-manage-alerts.md) |
| Configurare CHAP per il dispositivo StorSimple |[Configurare CHAP per il dispositivo StorSimple](storsimple-configure-chap.md) |
| Aggiungere un criterio di backup</br>Aggiungere o modificare una pianificazione</br>Eliminare un criterio di backup</br>Creazione di un backup manuale</br>Creare un criterio di backup personalizzato con più volumi e pianificazioni |[Gestire i criteri di backup](storsimple-8000-manage-backup-policies-u2.md) |
| Arrestare i controller dei dispositivi</br>Riavviare i controller dei dispositivi</br>Arrestare i controller dei dispositivi</br>Ripristinare le impostazioni predefinite del dispositivo</br>(I valori precedenti sono solo per il dispositivo locale) |[Gestire il controller del dispositivo StorSimple](storsimple-8000-manage-device-controller.md) |
| Informazioni sui componenti hardware di StorSimple</br>Monitorare lo stato dell'hardware</br>(I valori precedenti sono solo per il dispositivo locale) |[Monitorare i componenti hardware](storsimple-8000-monitor-hardware-status.md) |
| Creare un pacchetto di supporto |[Creare e gestire un pacchetto di supporto](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Installare gli aggiornamenti del software |[Aggiornare il dispositivo](storsimple-update-device.md) |

## <a name="next-steps"></a>Passaggi successivi

Se si riscontrano problemi con le operazioni giornaliere del dispositivo StorSimple o con uno qualsiasi dei relativi componenti hardware, vedere:

* [Risoluzione dei problemi con l'utilizzo di strumenti di diagnostica](storsimple-8000-diagnostics.md)
* [Utilizzare gli indicatori LED di monitoraggio di StorSimple](storsimple-monitoring-indicators.md)

Se non è possibile risolvere i problemi ed è necessario creare una richiesta di servizio, fare riferimento a [Contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md).

