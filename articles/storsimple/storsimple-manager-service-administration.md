<properties 
   pageTitle="Amministrazione del servizio StorSimple Manager | Microsoft Azure"
	description="Informazioni su come gestire il dispositivo StorSimple mediante il servizio StorSimple Manager nel portale di gestione di Azure."
	services="storsimple"
	documentationCenter=""
	authors="alkohli"
	manager="carolz"
	editor=""/>
<tags 
   ms.service="storsimple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="09/01/2015"
	ms.author="alkohli"/>

# Utilizzare il servizio StorSimple Manager per amministrare il dispositivo StorSimple

## Panoramica

In questo articolo viene descritta l'interfaccia del servizio StorSimple Manager, tra cui la modalità di connessione, le diverse opzioni disponibili e i collegamenti a flussi di lavoro specifici che possono essere eseguiti mediante questa interfaccia. Questa guida è applicabile al dispositivo StorSimple fisico e virtuale.

Una volta letto l'articolo, si sarà in grado di:

- Connettersi al servizio StorSimple Manager
- Passare all'interfaccia utente StorSimple Manager
- Gestire il dispositivo StorSimple tramite il servizio StorSimple Manager


## Connettersi al servizio StorSimple Manager

Il servizio StorSimple Manager viene eseguito in Microsoft Azure ed è connesso a più dispositivi di archiviazione StorSimple. Per gestire questi dispositivi, si utilizza un portale di gestione centrale di Microsoft Azure in esecuzione in un browser. Per connettersi al servizio StorSimple Manager, effettuare le operazioni seguenti.

#### Per connettersi al servizio

1. Accedere a [http://azure.microsoft.com/](http://azure.microsoft.com/)

1. Utilizzando le credenziali dell'account Microsoft, accedere al portale di gestione di Microsoft Azure (in alto a destra del riquadro).

1. Scorrere verso il basso il riquadro di spostamento a sinistra per accedere al servizio StorSimple Manager.


## Passare all'interfaccia utente del servizio StorSimple Manager

Nella tabella seguente è illustrata la gerarchia di spostamento per l'interfaccia utente del servizio StorSimple Manager.

- La pagina di destinazione di **StorSimple Manager** consente di visualizzare le pagine dell'interfaccia utente a livello di servizio, applicabili a tutti i dispositivi di un servizio.

- La pagina **Dispositivi** consente di visualizzare le pagine dell'interfaccia utente a livello di dispositivo, applicabili a un dispositivo specifico.

- La pagina **Contenitori del volume** consente di visualizzare la pagina che mostra tutti i volumi associati a un dispositivo.


#### Gerarchia di spostamento del servizio StorSimple Manager

|Pagina di destinazione|Pagine a livello di servizio|Pagine a livello di dispositivo|Pagine a livello di dispositivo|
|---|---|---|---|
|Servizio StorSimple Manager|Dashboard del servizio|Pagina dashboard||
||Dispositivi →|Monitora| ||Catalogo backup|Contenitori dei volumi→|Volumi| ||Configura (servizio)|Criteri di backup|| ||Processi|Configura (dispositivo)| ||Avvisi|Manutenzione|


## Gestire il dispositivo StorSimple tramite il servizio StorSimple Manager

Nella tabella seguente viene illustrato un riepilogo di tutte le attività di gestione comuni e dei flussi di lavoro complessi che possono essere eseguiti nell'interfaccia utente del servizio StorSimple Manager. Queste attività sono organizzate in base alle pagine dell'interfaccia utente su cui sono state avviate.

Per ulteriori informazioni su ogni flusso di lavoro, scegliere la procedura appropriata nella tabella.

#### Flussi di lavoro di StorSimple Manager

|Se si desidera eseguire questa operazione...|Andare a questa pagina dell'interfaccia utente...|Usare questa procedura.|
|---|---|---|
|Creare un servizio</br>Eliminare un servizio</br>Ottenere la chiave di registrazione del servizio</br>Rigenerare la chiave di registrazione|Servizio StorSimple Manager|[Distribuire un servizio StorSimple Manager](storsimple-manage-service.md)
|Modificare la chiave DEK del servizio</br>Visualizzare i log delle operazioni|Servizio StorSimple Manager → Dashboard|[Utilizzare il dashboard del servizio StorSimple Manager](storsimple-service-dashboard.md)|
|Disattivare un dispositivo</br>Eliminare un dispositivo|Servizio StorSimple Manager → Dispositivi|[Disattivare o eliminare un dispositivo](storsimple-deactivate-and-delete-device.md)|
|Informazioni sul failover del dispositivo e sul ripristino di emergenza</br>Failover a un dispositivo fisico</br>Failover su un dispositivo virtuale</br>Ripristino di emergenza di continuità aziendale (BCDR)|Servizio StorSimple Manager → Dispositivi|[Failover e ripristino di emergenza per il dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md)|
|Elenco di backup per un volume</br>Selezionare un set di backup</br>Eliminare un set di backup|Servizio StorSimple Manager → Catalogo di backup|[Gestire i backup](storsimple-manage-backup-catalog.md)|
|Clonare un volume|Servizio StorSimple Manager → Catalogo di backup|[Clonare un volume](storsimple-clone-volume.md)|
|Ripristinare un set di backup|Servizio StorSimple Manager → Catalogo di backup|[Ripristinare un set di backup](storsimple-restore-from-backup-set.md)|
|Informazioni sugli account di archiviazione</br>Aggiungere un account di archiviazione</br>Modificare un account di archiviazione</br>Eliminare un account di archiviazione</br>Rotazione della chiave degli account di archiviazione|Servizio StorSimple Manager → Configura|[Gestire gli account di archiviazione](storsimple-manage-storage-accounts.md)|
|Informazioni sui modelli di larghezza di banda</br>Aggiungere un modello di larghezza di banda</br>Modificare un modello di larghezza di banda</br>Eliminare un modello di larghezza di banda</br>Utilizzare un modello di larghezza di banda predefinito</br>Creare un modello di larghezza di banda giornata intera che inizia a un'ora specificata|Servizio StorSimple Manager → Configura|[Gestire i modelli di larghezza di banda](storsimple-manage-bandwidth-templates.md)|
|Informazioni sui record di controllo di accesso</br>Creare un record di controllo di accesso</br>Modificare un record di controllo di accesso</br>Eliminare un record di controllo di accesso|Servizio StorSimple Manager → Configura|[Gestire record di controllo di accesso](storsimple-manage-acrs.md)|
|Visualizzare i dettagli dei processi</br>Annullare un processo|Servizio StorSimple Manager → Processi|[Gestire i processi](storsimple-manage-jobs.md)
|Ricevere notifiche di avviso</br>Gestire gli avvisi</br>Esaminare gli avvisi|Servizio StorSimple Manager → Avvisi|[Visualizzare e gestire gli avvisi di StorSimple](storsimple-manage-alerts.md)
|Visualizzare gli iniziatori connessi</br>Trovare il numero di serie del dispositivo</br>Trovare il nome qualificato iSCSI di destinazione|Servizio StorSimple Manager → Dispositivi → Dashboard|[Utilizzare il dashboard del dispositivo StorSimple](storsimple-device-dashboard.md)|
|Creare grafici di monitoraggio|Servizio StorSimple Manager → Dispositivi → Monitora|[Monitoraggio del dispositivo StorSimple](storsimple-monitor-device.md)|
|Aggiungere un contenitore del volume</br>Modificare un contenitore del volume</br>Eliminare un contenitore del volume|Servizio StorSimple Manager → Dispositivi → Contenitori dei volumi|[Gestisci contenitori dei volumi](storsimple-manage-volume-containers.md)|
|Aggiungere un volume</br>Modificare un volume</br>Portare un volume offline</br>Eliminare un volume</br>Monitorare un volume|Servizio StorSimple Manager → Dispositivi → Contenitori dei volumi → Volumi|[Gestire i volumi](storsimple-manage-volumes.md)|
|Modificare le impostazioni del dispositivo</br>Modificare le impostazioni di tempo</br>Modificare le impostazioni DNS.md</br>Configurare le interfacce di rete|Servizio StorSimple Manager → Dispositivi → Configura|[Modificare la configurazione per il dispositivo StorSimple](storsimple-modify-device-config.md)|
|Visualizzare le impostazioni proxy Web|Servizio StorSimple Manager → Dispositivi → Configura|[Configurare il proxy Web per il dispositivo](storsimple-configure-web-proxy.md)|
|Modificare la password amministratore del dispositivo</br>Modificare la password di Gestione snapshot StorSimple|Servizio StorSimple Manager → Dispositivi → Configura|[Modificare le password di StorSimple](storsimple-change-passwords.md)|
|Configurare la gestione remota|Servizio StorSimple Manager → Dispositivi → Configura|[Connettersi in remoto al dispositivo StorSimple](https://msdn.microsoft.com/library/azure/dn772393.aspx)|
|Configurare le impostazioni degli avvisi|Servizio StorSimple Manager → Dispositivi → Configura|[Visualizzare e gestire gli avvisi di StorSimple](storsimple-manage-alerts.md)|
|Configurare CHAP per il dispositivo StorSimple|Servizio StorSimple Manager → Dispositivi → Configura|[Configurare CHAP per il dispositivo StorSimple](storsimple-configure-chap.md)|
|Aggiungere un criterio di backup</br>Aggiungere o modificare una pianificazione</br>Eliminare un criterio di backup</br>Eseguire un backup manuale</br>Creare un criterio di backup personalizzato con più volumi e pianificazioni|Servizio StorSimple Manager → Dispositivi → Criteri di backup|[Gestire i criteri di backup](storsimple-manage-backup-policies.md)|
|Arrestare i controller dei dispositivi</br>Riavviare i controller dei dispositivi</br>Arrestare i controller dei dispositivi</br>Ripristinare i valori predefiniti di fabbrica del dispositivo</br>I valori precedenti sono solo per il dispositivo locale|Servizio StorSimple Manager → Dispositivi → Manutenzione|[Gestire il controller del dispositivo StorSimple](storsimple-manage-device-controller.md)|
|Informazioni sui componenti hardware di StorSimple</br>Monitorare lo stato dell'hardware</br>I valori precedenti sono solo per il dispositivo locale|Servizio StorSimple Manager → Dispositivi → Manutenzione|[Monitorare i componenti hardware](storsimple-monitor-hardware-status.md)|
|Creare un pacchetto di supporto|Servizio StorSimple Manager → Dispositivi → Manutenzione|[Creare e gestire un pacchetto di supporto](storsimple-create-manage-support-package.md)|
|Installare gli aggiornamenti del software|Servizio StorSimple Manager → Dispositivi → Manutenzione|[Aggiornare il dispositivo](storsimple-update-device.md)|

##Passaggi successivi
Se si riscontrano problemi con le operazioni giornaliere del dispositivo StorSimple o con uno qualsiasi dei relativi componenti hardware, vedere:

- [Risoluzione dei problemi relativi a un dispositivo operativo](storsimple-troubleshoot-operational-device.md)
- [Utilizzare gli indicatori LED di monitoraggio di StorSimple](storsimple-monitoring-indicators.md)

Se non è possibile risolvere i problemi ed è necessario creare una richiesta di servizio, vedere:

-  [Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md)

<!---HONumber=September15_HO1-->