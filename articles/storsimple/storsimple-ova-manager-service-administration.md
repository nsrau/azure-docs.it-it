---
title: Amministrazione dell'array virtuale StorSimple Manager | Microsoft Docs
description: Informazioni su come gestire l'array virtuale StorSimple locale mediante il servizio StorSimple Manager nel portale di Azure classico.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2016
ms.author: alkohli

---
# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-virtual-array"></a>Usare il servizio StorSimple Manager per amministrare StorSimple Virtual Array
![flusso del processo di installazione](./media/storsimple-ova-manager-service-administration/manage4.png)

## <a name="overview"></a>Panoramica
Questo articolo descrive l'interfaccia del servizio StorSimple Manager, inclusa la modalità di connessione e le diverse opzioni disponibili. Fornisce inoltre collegamenti ai flussi di lavoro specifici che possono essere eseguiti mediante questa interfaccia. 

Dopo aver letto l'articolo, l'utente sarà in grado di:

* Connettersi al servizio StorSimple Manager
* Passare all'interfaccia utente StorSimple Manager
* Amministrare l'array virtuale StorSimple tramite il servizio StorSimple Manager

> [!NOTE]
> Per visualizzare le opzioni di gestione disponibili per il dispositivo StorSimple serie 8000, vedere [Utilizzare il servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-manager-service"></a>Connettersi al servizio StorSimple Manager
Il servizio StorSimple Manager viene eseguito in Microsoft Azure ed è connesso a più array virtuali StorSimple. Per gestire questi dispositivi, si utilizza un portale centrale di Microsoft Azure classico in esecuzione in un browser. Per connettersi al servizio StorSimple Manager, effettuare le operazioni seguenti.

#### <a name="to-connect-to-the-service"></a>Per connettersi al servizio
1. Visualizzare la pagina [https://manage.windowsazure.com/](https://manage.windowsazure.com/).
2. Utilizzando le credenziali dell'account Microsoft, accedere al portale di Microsoft Azure classico (in alto a destra del riquadro).
3. Scorrere verso il basso il riquadro di spostamento a sinistra per accedere al servizio StorSimple Manager.
   
    ![scorrere per individuare il servizio](./media/storsimple-ova-manager-service-administration/admin-scroll.png)

## <a name="navigate-the-storsimple-manager-service-ui"></a>Esplorare l'interfaccia utente del servizio StorSimple Manager
Nella tabella seguente è illustrata la gerarchia di spostamento per l'interfaccia utente del servizio StorSimple Manager.

* La pagina di destinazione di **StorSimple Manager** consente di visualizzare le pagine dell'interfaccia utente a livello di servizio, applicabili a tutti gli array virtuali di un servizio.
* La pagina **Dispositivi** consente di visualizzare le pagine dell'interfaccia utente a livello di dispositivo, applicabili a un array virtuale specifico.

#### <a name="storsimple-manager-service-navigational-hierarchy"></a>Gerarchia di spostamento del servizio StorSimple Manager
| Pagina di destinazione | Pagine a livello di servizio | Pagine a livello di dispositivo |
| --- | --- | --- |
| Servizio StorSimple Manager |Dashboard (servizio) |Dashboard (dispositivo) |
| Dispositivi → |Monitoraggio | |
| Catalogo di backup |Condivisioni (file server) o  </br>volumi (server iSCSI) | |
| Configura (servizio) |Configura (dispositivo) | |
| Processi |Manutenzione | |
| Avvisi | | |

## <a name="use-the-storsimple-manager-service-to-perform-management-tasks"></a>Usare il servizio StorSimple Manager per eseguire attività di gestione
Nella tabella seguente viene illustrato un riepilogo di tutte le attività di gestione comuni e dei flussi di lavoro complessi che possono essere eseguiti nell'interfaccia utente del servizio StorSimple Manager. Queste attività sono organizzate in base alle pagine dell'interfaccia utente su cui sono state avviate.

Per ulteriori informazioni su ogni flusso di lavoro, scegliere la procedura appropriata nella tabella.

#### <a name="storsimple-manager-workflows"></a>Flussi di lavoro di StorSimple Manager
| Se si desidera eseguire questa operazione... | Andare a questa pagina dell'interfaccia utente... | Seguire questa procedura |
| --- | --- | --- |
| Creare un servizio</br>Eliminare un servizio</br>Ottenere la chiave di registrazione del servizio</br>Rigenerare la chiave di registrazione del servizio |Servizio StorSimple Manager |[Distribuire il servizio StorSimple Manager](storsimple-ova-manage-service.md) |
| Modificare la chiave DEK del servizio</br>Visualizzare i log delle operazioni |Servizio StorSimple Manager → Dashboard |[Usare il dashboard del servizio StorSimple](storsimple-ova-service-dashboard.md) |
| Disattivare un array virtuale</br>Eliminare un array virtuale |Servizio StorSimple Manager → Dispositivi |[Disattivare o eliminare un array virtuale](storsimple-ova-deactivate-and-delete-device.md) |
| Ripristino di emergenza e failover del dispositivo</br>Prerequisiti di failover</br>Failover in un dispositivo virtuale</br>Ripristino di emergenza di continuità aziendale (BCDR)</br>Errori durante il ripristino di emergenza |Servizio StorSimple Manager → Dispositivi |[Ripristino di emergenza e failover del dispositivo per l'array virtuale StorSimple](storsimple-ova-failover-dr.md) |
| Backup di condivisioni e volumi</br>Creazione di un backup manuale</br>Modificare la pianificazione dei backup</br>Visualizzare i backup esistenti |Servizio StorSimple Manager → Catalogo di backup |[Backup dell'array virtuale StorSimple](storsimple-ova-backup.md) |
| Ripristinare condivisioni da un set di backup</br>Ripristinare volumi da un set di backup</br>Ripristino a livello di elementi (solo file server) |Servizio StorSimple Manager → Catalogo di backup |[Ripristinare da un backup dell'array virtuale StorSimple](storsimple-ova-restore.md) |
| Informazioni sugli account di archiviazione</br>Aggiungere un account di archiviazione</br>Modificare un account di archiviazione</br>Eliminare un account di archiviazione |Servizio StorSimple Manager → Configura |[Gestire un account di archiviazione per l'array virtuale StorSimple](storsimple-ova-manage-storage-accounts.md) |
| Informazioni sui record di controllo di accesso</br>Aggiungere o modificare un record di controllo di accesso </br>Eliminare un record di controllo di accesso |Servizio StorSimple Manager → Configura |[Gestire record di controllo di accesso per l'array virtuale StorSimple](storsimple-ova-manage-acrs.md) |
| Visualizza i dettagli dei processi |Servizio StorSimple Manager → Processi |[Gestire processi di array virtuali StorSimple](storsimple-ova-manage-jobs.md) |
| Configurare le impostazioni degli avvisi</br>Ricevere notifiche di avviso</br>Gestisci avvisi</br>Esaminare gli avvisi |Servizio StorSimple Manager → Avvisi |[Visualizzare e gestire gli avvisi per l'array virtuale StorSimple](storsimple-ova-manage-alerts.md) |
| Modificare la password dell’amministratore del dispositivo |Servizio StorSimple Manager → Dispositivi → Configura |[Modificare la password amministratore del dispositivo dell'array virtuale StorSimple](storsimple-ova-change-device-admin-password.md) |
| Installare gli aggiornamenti del software |Servizio StorSimple Manager → Dispositivi → Manutenzione |[Aggiornare l'array virtuale](storsimple-ova-install-update-01.md) |

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

<!--HONumber=Oct16_HO2-->


