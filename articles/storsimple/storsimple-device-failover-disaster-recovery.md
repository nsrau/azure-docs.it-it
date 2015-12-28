<properties 
   pageTitle="Failover e ripristino di emergenza per StorSimple | Microsoft Azure"
   description="Informazioni su come eseguire il failover del dispositivo StorSimple su se stesso, su un altro dispositivo fisico o su un dispositivo virtuale."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/14/2015"
   ms.author="alkohli" />

# Failover e ripristino di emergenza per il dispositivo StorSimple

## Panoramica

Questa esercitazione descrive i passaggi necessari per eseguire il failover di un dispositivo StorSimple in caso di emergenza. Un failover consentirà di migrare i dati da un dispositivo di origine nel centro dati a un altro dispositivo fisico o virtuale che si trova nella stessa o in un’altra area geografica.

Il failover del dispositivo viene gestito tramite la funzionalità di ripristino di emergenza e viene avviato dalla pagina Dispositivi. In questa pagina sono riportati tutti i dispositivi StorSimple connessi al servizio StorSimple Manager. Per ogni dispositivo, vengono visualizzati nome descrittivo, stato, capacità fornita e massima, tipo e modello.

![Pagina Dispositivi](./media/storsimple-device-failover-disaster-recovery/IC740972.png)

Le indicazioni fornite in questa esercitazione si applicano ai dispositivi fisici e virtuali StorSimple in tutte le versioni del software.



## Ripristino di emergenza (DR) e failover del dispositivo

In uno scenario di ripristino di emergenza, il dispositivo principale smette di funzionare. In questo caso, è possibile spostare i dati del cloud associati al dispositivo guasto in un altro dispositivo utilizzando il dispositivo principale come *origine* e specificando l’altro dispositivo come *destinazione*. È possibile selezionare uno o più contenitori di volumi per eseguire la migrazione al dispositivo di destinazione. Questo processo viene definito *failover*. Durante il failover, i contenitori di volumi del dispositivo di origine cambiano proprietà e vengono trasferiti al dispositivo di destinazione.

## Considerazioni per il failover del dispositivo

In caso di emergenza, è possibile eseguire il failover del dispositivo StorSimple:

- In un dispositivo fisico 
- Nel dispositivo stesso
- In un dispositivo virtuale

Per qualsiasi tipo di failover del dispositivo, tenere presente quanto segue:

- Per il ripristino di emergenza è necessario che tutti i volumi all'interno dei contenitori di volumi siano offline e che i contenitori di volumi siano associati a uno snapshot nel cloud. 
- I dispositivi di destinazione disponibili per il ripristino di emergenza sono dispositivi con spazio sufficiente ad accogliere i contenitori di volumi selezionati. 
- I dispositivi connessi al servizio che non soddisfano i criteri di spazio sufficiente non saranno disponibili come dispositivi di destinazione.

#### Failover del dispositivo in tutte le versioni del software

Un servizio StorSimple Manager in una distribuzione potrebbe avere più dispositivi, fisici e virtuali, che eseguono diverse versioni del software. In base alla versione del software, i tipi di volume nei dispositivi possono essere diversi. Ad esempio, un dispositivo che esegue l’Aggiornamento 2 o versione successiva avrebbe volumi aggiunti in locale e a livelli (con archiviazione in un sottoinsieme a livelli). Un dispositivo che esegue un pre-aggiornamento 2 d'altra parte può avere volumi di archiviazione a livelli.

Utilizzare la seguente tabella per determinare se è possibile eseguire il failover a un altro dispositivo che esegue una versione diversa del software e il comportamento dei tipi di volume durante il ripristino di emergenza.

| Failover da | Consentito per il dispositivo fisico | Consentito per il dispositivo virtuale |
|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------|
| Aggiornamento 2 a pre-aggiornamento 1 (versione, 0.1, 0.2, 0.3) | No | No |
| Aggiornamento 2 a Aggiornamento 1 (1, 1.1, 1.2) | Sì <br></br>se utilizza volumi aggiunti localmente o a livelli o una combinazione dei due, il failover dei volumi viene sempre eseguito come un volume a livelli. | Sì<br></br>se utilizza volumi aggiunti in locale, il failover viene eseguito come volume a livelli. |
| Aggiornamento 2 a Aggiornamento 2 (versione successiva) | Sì<br></br>se utilizza volumi aggiunti in locale o a livelli o una combinazione dei due, il failover viene sempre eseguito come il tipo di volume iniziale: volume a livelli come volume a livelli e volume aggiunto localmente come volume aggiunto localmente. | Sì<br></br>se utilizza volumi aggiunti in locale, il failover viene eseguito come volume a livelli. |

## Failover su un altro dispositivo fisico

Eseguire i passaggi seguenti per ripristinare il dispositivo su un dispositivo fisico di destinazione.

1. Verificare che il contenitore del volume di cui si desidera eseguire il failover sia associato alle snapshot cloud.

1. Nella pagina **Dispositivi** fare clic sulla scheda **Contenitori di volumi**.

1. Selezionare un contenitore di volumi di cui si desidera eseguire il failover a un altro dispositivo. Fare clic sul contenitore di volumi per visualizzare l'elenco dei volumi presenti nel contenitore. Selezionare un volume e fare clic su **Offline** per passare il volume offline. Ripetere questo processo per tutti i volumi nel contenitore di volumi.

1. Ripetere il passaggio precedente per tutti i contenitori di volumi di cui si desidera eseguire il failover a un altro dispositivo.

1. Nella pagina **Dispositivi**, fare clic su **Failover**.

1. Nella procedura guidata visualizzata, sotto **Scegli contenitore di volumi per il failover**:

	1. Nell'elenco dei contenitori di volumi, selezionare i contenitori di volumi di cui si desidera eseguire il failover.

		>[AZURE.NOTE]**Vengono visualizzati solo i contenitori di volumi con gli snapshot del cloud e i volumi offline associati.**

	1. In **Scegli un dispositivo di destinazione** per i volumi dei contenitori selezionati, selezionare un dispositivo di destinazione dall'elenco a discesa dei dispositivi disponibili. Solo i dispositivi dotati di capacità disponibile vengono visualizzati nell'elenco a discesa.

	1. Infine, esaminare tutte le impostazioni di failover in **Conferma failover**. Fare clic sull'icona del segno di spunta ![Icona del segno di spunta](./media/storsimple-device-failover-disaster-recovery/IC740895.png).

1. Dopo aver completato il failover, andare alla pagina **Dispositivi**.

	1. Selezionare il dispositivo utilizzato come dispositivo di destinazione per il processo di failover.

	1. Andare alla pagina **Contenitori di volumi**. Dovrebbero essere elencati tutti i contenitori di volumi, insieme ai volumi del dispositivo precedente.

## Failover con un solo dispositivo

Se si dispone di un solo dispositivo ed è necessario eseguire un failover, eseguire i passaggi seguenti.

1. Acquisire snapshot nel cloud di tutti i volumi nel dispositivo.

1. Ripristinare le impostazioni predefinite del dispositivo. Seguire le istruzioni dettagliate in [come ripristinare le impostazioni predefinite di un dispositivo StorSimple](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

1. Configurare il dispositivo e registrarlo nuovamente con il servizio StorSimple Manager.

1. Nella pagina **Dispositivi**, il dispositivo precedente dovrà essere visualizzato come **Offline**. Il dispositivo appena registrato deve essere visualizzato come **Online**.

1. Per il nuovo dispositivo, completare innanzitutto la configurazione minima del dispositivo.
												
	>[AZURE.IMPORTANT]**Se non viene prima completata la configurazione minima, il ripristino di emergenza avrà esito negativo a causa di un bug nell'implementazione corrente. Questo problema verrà risolto in una versione successiva.**

1. Selezionare il dispositivo precedente (stato offline) e fare clic su **Failover**. Nella procedura guidata che viene visualizzata, eseguire il failover di questo dispositivo e specificare come dispositivo di destinazione il dispositivo appena registrato. Per istruzioni dettagliate, fare riferimento a [Failover su un altro dispositivo fisico](#fail-over-to-another-physical-device).

1. Verrà creato un processo di ripristino del dispositivo che è possibile monitorare da **Processi**.

1. Al termine il processo, accedere al nuovo dispositivo e andare alla pagina **Contenitori di volumi**. Tutti i contenitori di volumi del dispositivo precedente dovrebbero essere stati migrati al nuovo dispositivo.

## Failover su un dispositivo virtuale StorSimple

Prima di eseguire questa procedura, è necessario disporre di un dispositivo StorSimple virtuale creato e configurato. Se esegue l'Aggiornamento 2, considerare l'utilizzo di un dispositivo virtuale 8020 per il ripristino di emergenza che dispone di 64 TB e utilizza l'archiviazione Premium.
 
Eseguire i passaggi seguenti per ripristinare il dispositivo su un dispositivo virtuale StorSimple di destinazione.

1. Verificare che il contenitore del volume di cui si desidera eseguire il failover sia associato alle snapshot cloud.

1. Nella pagina **Dispositivi** fare clic sulla scheda **Contenitori di volumi**.

1. Selezionare un contenitore di volumi di cui si desidera eseguire il failover a un altro dispositivo. Fare clic sul contenitore di volumi per visualizzare l'elenco dei volumi presenti nel contenitore. Selezionare un volume e fare clic su **Offline** per passare il volume offline. Ripetere questo processo per tutti i volumi nel contenitore di volumi.

1. Ripetere il passaggio precedente per tutti i contenitori di volumi di cui si desidera eseguire il failover a un altro dispositivo.

1. Nella pagina **Dispositivi**, fare clic su **Failover**.

1. Nella procedura guidata visualizzata, sotto **Scegli contenitore di volumi per il failover**, completare le operazioni seguenti:
													
	a. Nell'elenco dei contenitori di volumi, selezionare i contenitori di volumi di cui si desidera eseguire il failover.

	>[AZURE.NOTE]**Vengono visualizzati solo i contenitori di volumi con gli snapshot del cloud e i volumi offline associati.**

	b. In **Scegli un dispositivo di destinazione per i volumi dei contenitori selezionati**, selezionare un dispositivo virtuale dall'elenco a discesa dei dispositivi disponibili. Solo i dispositivi dotati di capacità sufficiente vengono visualizzati nell'elenco a discesa.
	

1. Infine, esaminare tutte le impostazioni di failover in **Conferma failover**. Fare clic sull'icona del segno di spunta ![Icona del segno di spunta](./media/storsimple-device-failover-disaster-recovery/IC740895.png).

1. Dopo aver completato il failover, andare alla pagina **Dispositivi**.
													
	a. Selezionare il dispositivo virtuale StorSimple utilizzato come dispositivo di destinazione per il processo di failover.
	
	b. Andare alla pagina **Contenitori di volumi**. Dovrebbero essere elencati tutti i contenitori di volumi, insieme ai volumi del dispositivo precedente.

![Video disponibile](./media/storsimple-device-failover-disaster-recovery/Video_icon.png) **Video disponibile**

Per guardare un video che illustra come è possibile ripristinare un dispositivo fisico di cui si è eseguito il failover in un dispositivo virtuale nel cloud, fare clic [qui](http://azure.microsoft.com/documentation/videos/storsimple-and-disaster-recovery/).

## Ripristino di emergenza di continuità aziendale (BCDR)

Uno scenario di ripristino di emergenza di continuità aziendale (BCDR) si verifica quando l'intero data center di Azure smette di funzionare. Può influire sul servizio StorSimple Manager e sui dispositivi StorSimple associati.

Se sono presenti dispositivi StorSimple registrati prima del verificarsi di un problema grave, per questi dispositivi StorSimple potrebbe essere necessario eseguire una reimpostazione di fabbrica. Dopo l'emergenza, il dispositivo StorSimple verrà visualizzato come offline. Il dispositivo StorSimple deve essere eliminato dal portale e deve essere eseguita una reimpostazione di fabbrica, seguita da una registrazione aggiornata.


## Passaggi successivi

Dopo aver eseguito il failover, potrebbe essere necessario:

- [Disattivare il dispositivo StorSimple](storsimple-deactivate-and-delete-device.md#deactivate-a-device)
- [Eliminare il dispositivo StorSimple](storsimple-deactivate-and-delete-device.md#delete-a-device)

Per informazioni sull’utilizzo del servizio StorSimple Manager, passare a[Utilizzare il servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
 

<!---HONumber=AcomDC_1217_2015-->