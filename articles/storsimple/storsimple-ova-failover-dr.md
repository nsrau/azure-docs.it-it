<properties
   pageTitle="Ripristino di emergenza e failover del dispositivo per StorSimple Virtual Array"
   description="Scoprire di più su come eseguire il failover di StorSimple Virtual Array."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/01/2016"
   ms.author="alkohli"/>

# Ripristino di emergenza e failover del dispositivo per StorSimple Virtual Array


## Panoramica

Questo articolo illustra il ripristino di emergenza per Microsoft Azure StorSimple Virtual Array (anche noto come dispositivo virtuale locale StorSimple) inclusi i passaggi dettagliati necessari per eseguire il failover in un altro dispositivo virtuale in caso di emergenza. Un failover consente di migrare i dati da un dispositivo di *origine* nel data center a un altro dispositivo di *destinazione* situato nella stessa posizione geografica o in una diversa. Il failover del dispositivo interessa tutto il dispositivo. Durante il failover, la proprietà dei dati del cloud passa dal dispositivo di origine al dispositivo di destinazione.

Il failover del dispositivo viene gestito tramite la funzionalità di ripristino di emergenza e viene inizializzato nella pagina **Dispositivi**. In questa pagina sono riportati tutti i dispositivi StorSimple connessi al servizio StorSimple Manager. Per ogni dispositivo, vengono visualizzati il nome descrittivo, lo stato, la capacità sottoposta a provisioning e massima, il tipo e il modello.

![](./media/storsimple-ova-failover-dr/image16.png)

Il contenuto di questo articolo si applica solo a StorSimple Virtual Array. Per eseguire il failover di un dispositivo serie 8000, andare a [Failover e ripristino di emergenza per il dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md).


## Che cos'è il ripristino di emergenza?

In uno scenario di ripristino di emergenza, il dispositivo principale smette di funzionare. In questo caso, è possibile spostare i dati del cloud associati al dispositivo guasto in un altro dispositivo utilizzando il dispositivo principale come *origine* e specificando l’altro dispositivo come *destinazione*. Questo processo viene definito *failover*. Durante il failover, tutti i volumi e le condivisioni del dispositivo di origine cambiano proprietà e vengono trasferiti al dispositivo di destinazione. Non è consentito filtrare i dati.

Il ripristino di emergenza è progettato come un ripristino completo del dispositivo tramite la suddivisione in livelli e il rilevamento basati su mappa termica. Una mappa termica viene definita assegnando un valore termico ai dati in base a modelli di lettura e scrittura. Questa mappa termica suddivide in livelli i blocchi di dati con valore termico più basso prima nel cloud, mantenendo i blocchi di dati con valore termico più alto (più usati) a livello locale. Durante un ripristino di emergenza, la mappa termica viene usata per ripristinare e riattivare i dati dal cloud. Il dispositivo recupera tutti i volumi o le condivisioni dal backup più recente (come determinato internamente) ed esegue un ripristino a partire da quel backup. L'intero processo di ripristino di emergenza viene gestito dal dispositivo.


## Prerequisiti per il failover del dispositivo


### Prerequisiti

Per qualsiasi failover del dispositivo, devono essere soddisfatti i prerequisiti seguenti:

- Il dispositivo di origine deve essere in stato **Disattivato**.

- Il dispositivo di destinazione deve essere visualizzato come **Attivo** nel portale di Azure classico. È necessario eseguire il provisioning di un dispositivo virtuale di destinazione della stessa capacità o superiore. È quindi necessario usare l'interfaccia utente Web locale per configurare e registrare correttamente il dispositivo virtuale.

	> [AZURE.IMPORTANT]
	> 
	> Non si deve tentare di configurare il dispositivo virtuale registrato tramite il servizio facendo clic su **installazione dispositivo completata**. Non si deve eseguire nessuna configurazione del dispositivo tramite questo servizio.

- Il dispositivo di origine e quello di destinazione devono essere dello stesso tipo. Il failover di un dispositivo virtuale configurato come file server può essere eseguito solo su un altro file server. Lo stesso vale per un server iSCSI.

- Per il ripristino di emergenza di un file server, si consiglia di aggiungere il dispositivo di destinazione allo stesso dominio del dispositivo di origine in modo da risolvere automaticamente le autorizzazioni di condivisione. In questa versione è supportato solo il failover su un dispositivo di destinazione nello stesso dominio.

### Altre considerazioni

- Si consiglia di portare offline tutti i volumi o tutte le condivisioni nel dispositivo di origine.

- Se si tratta di un failover pianificato, si consiglia di eseguire un backup del dispositivo, quindi di procedere con il failover per ridurre al minimo la perdita dei dati. Se si tratta di un failover non pianificato, si usa il backup più recente per ripristinare il dispositivo.

- I dispositivi di destinazione disponibili per il ripristino di emergenza sono dispositivi dotati di capacità uguale o superiore rispetto al dispositivo di origine. I dispositivi connessi al servizio che non soddisfano i criteri di spazio sufficiente non saranno disponibili come dispositivi di destinazione.

### Verifiche preliminari per il ripristino di emergenza

Prima di iniziare il ripristino di emergenza, vengono eseguite delle verifiche preliminari sul dispositivo. Queste verifiche favoriscono l'assenza di errori nel momento in cui si avvia il ripristino di emergenza. Le verifiche preliminari includono:

- Convalida dell'account di archiviazione

- Controllo della connettività del cloud in Azure

- Controllo dello spazio disponibile nel dispositivo di destinazione

- Verifica della validità di nomi relativi a record di controllo di accesso, di IQN (lunghezza non superiore a 220 caratteri) e di password CHAP (lunghezza di 12 e 16 caratteri) associati ai volumi di un dispositivo di origine del server iSCSI

Se una qualsiasi delle verifiche preliminari riportate sopra non va a buon fine, non è possibile procedere con il ripristino di emergenza. È necessario risolvere questi problemi prima di riprovare il ripristino di emergenza.

Se il ripristino di emergenza è stato completato correttamente, la proprietà dei dati cloud sul dispositivo di origine viene trasferita al dispositivo di destinazione. Il dispositivo di origine non è quindi più disponibile nel portale. L'accesso a tutti i volumi o tutte le condivisioni sul dispositivo di origine è bloccato e il dispositivo di destinazione diventa attivo.

> [AZURE.IMPORTANT]
> 
> Anche se il dispositivo non è più disponibile, la macchina virtuale sottoposta a provisioning nel sistema host usa ancora risorse. Se il ripristino di emergenza è stato completato correttamente, è possibile eliminare questa macchina virtuale dal sistema host.

## Eseguire il failover su un dispositivo virtuale

Si consiglia di disporre di un dispositivo virtuale StorSimple sottoposto a provisioning, configurato tramite l'interfaccia utente Web locale e registrato con il servizio StorSimple Manager prima di eseguire questa procedura.


> [AZURE.IMPORTANT]
> 
> Non è consentito eseguire il failover da un dispositivo StorSimple serie 8000 a un dispositivo virtuale.

Eseguire i passaggi seguenti per ripristinare il dispositivo su un dispositivo virtuale StorSimple di destinazione.

1. Portare i volumi o le condivisioni offline nell'host. Vedere le istruzioni specifiche del sistema operativo sull'host per portare offline i volumi o le condivisioni. Se non ancora offline, è necessario portare offline tutti i volumi o tutte le condivisioni nel dispositivo andando a **Dispositivi > Condivisioni** (o **Dispositivo > Volumi**). Selezionare una condivisione o un volume e fare clic su **Porta offline** nella parte inferiore della pagina. Alla richiesta di conferma fare clic su **Sì**. Ripetere questo processo per tutte le condivisioni o per tutti i volumi nel dispositivo.

2. Nella pagina **Dispositivi**, selezionare il dispositivo di origine per il failover e fare clic su **Disattiva**. Verrà richiesto di confermare. La disattivazione del dispositivo è un processo permanente che non può essere annullato. Viene inoltre ricordato di portare offline le condivisioni o i volumi nell'host.

	![](./media/storsimple-ova-failover-dr/image18.png)

3. Dopo la conferma, viene avviata la disattivazione. Se la disattivazione è stata completata correttamente, se ne riceve notifica.

	![](./media/storsimple-ova-failover-dr/image19.png)

4. Nella pagina **Dispositivi**, lo stato del dispositivo viene modificato con **Disattivato**.

	![](./media/storsimple-ova-failover-dr/image20.png)

5. Selezionare il dispositivo disattivato e, nella parte inferiore della pagina, fare clic su **Failover**.

6. Nella procedura guidata di conferma failover che viene visualizzata, procedere nel modo seguente:

    1. Nell'elenco a discesa dei dispositivi disponibili, scegliere un **Dispositivo di destinazione.** Solo i dispositivi dotati di capacità sufficiente vengono visualizzati nell'elenco a discesa.

    2. Esaminare i dettagli associati al dispositivo di origine, ad esempio il nome del dispositivo, la capacità totale e i nomi delle condivisioni da sottoporre a failover.

		![](./media/storsimple-ova-failover-dr/image21.png)

7. Selezionare **Accetto che il failover è un'operazione definitiva e che al termine del failover il dispositivo di origine verrà eliminato**.

8. Fare clic sull’icona del segno di spunta ![](./media/storsimple-ova-failover-dr/image1.png).


9. Viene avviato un processo di failover e se ne riceve notifica. Fare clic su **Visualizza processo** per monitorare il failover.

	![](./media/storsimple-ova-failover-dr/image22.png)

10. Nella pagina **Processi**, viene visualizzato un processo di failover creato per il dispositivo di origine. Questo processo esegue le verifiche preliminari per il ripristino di emergenza.

	![](./media/storsimple-ova-failover-dr/image23.png)

 	Se le verifiche preliminari per il ripristino di emergenza hanno esito positivo, il processo di failover distribuisce i processi di ripristino per ogni condivisione o volume presente nel dispositivo di origine.

	![](./media/storsimple-ova-failover-dr/image24.png)

11. Dopo aver completato il failover, andare alla pagina **Dispositivi**.

	a. Selezionare il dispositivo virtuale StorSimple utilizzato come dispositivo di destinazione per il processo di failover.

	b. Andare alla pagina **Condivisioni** (o **Volumi** se si tratta di server iSCSI). Tutte le condivisioni (volumi) del dispositivo precedente ora devono essere elencate.
 	
	![](./media/storsimple-ova-failover-dr/image25.png)

![](./media/storsimple-ova-failover-dr/video_icon.png) **Video disponibile**

Questo video mostra come è possibile eseguire il failover di un dispositivo virtuale locale StorSimple su un altro dispositivo virtuale.

> [AZURE.VIDEO storsimple-virtual-array-disaster-recovery]

## Ripristino di emergenza di continuità aziendale (BCDR)

Uno scenario di ripristino di emergenza di continuità aziendale (BCDR) si verifica quando l'intero data center di Azure smette di funzionare. Può influire sul servizio StorSimple Manager e sui dispositivi StorSimple associati.

Se sono presenti dispositivi StorSimple registrati prima del verificarsi di un'emergenza, questi dispositivi StorSimple rischiano di dover essere eliminati. Dopo l'emergenza, è possibile ricreare e configurare i dispositivi.

## Errori durante il ripristino di emergenza

**Interruzione della connettività cloud durante il ripristino di emergenza**

Se la connettività cloud viene interrotta dopo l'avvio del ripristino di emergenza e prima del completamento del ripristino del dispositivo, il ripristino di emergenza non va a buon fine e se ne riceve notifica. Il dispositivo di destinazione usato per il ripristino di emergenza viene quindi contrassegnato come *Inutilizzabile.* Lo stesso dispositivo di destinazione non può essere quindi usato per ripristini di emergenza futuri.

**Nessun dispositivo di destinazione compatibile**

Se i dispositivi di destinazione disponibili non dispongono di spazio sufficiente, viene visualizzato un errore relativo all'assenza di dispositivi di destinazione compatibili.

**Errori nelle verifiche preliminari**

Se non viene soddisfatta una delle verifiche preliminari, vengono visualizzati degli errori relativi alle verifiche preliminari.

## Passaggi successivi

Scoprire di più su come [amministrare StorSimple Virtual Array tramite l'interfaccia utente Web locale](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0302_2016-->