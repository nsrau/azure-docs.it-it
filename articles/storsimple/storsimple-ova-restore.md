<properties
   pageTitle="Ripristinare da un backup dell'array virtuale StorSimple"
   description="Scoprire di più su come ripristinare un backup di StorSimple Virtual Array."
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
   ms.date="06/07/2016"
   ms.author="alkohli"/>

# Ripristinare da un backup dell'array virtuale StorSimple

## Panoramica 

Le informazioni contenute in questo articolo si applicano solo a Microsoft Azure StorSimple Virtual Array (noto anche come dispositivo virtuale locale StorSimple o dispositivo virtuale StorSimple) che esegue la versione di disponibilità generale (GA) di marzo 2016 o una versione successiva. Questo articolo illustra passo per passo come ripristinare da un set di backup di condivisioni o volumi sull'array virtuale StorSimple. L'articolo illustra anche come funziona il ripristino a livello di elemento su StorSimple Virtual Array configurato come file server.


## Ripristinare condivisioni da un set di backup


**Prima di tentare di ripristinare le condivisioni, assicurarsi di avere spazio sufficiente sul dispositivo per completare questa operazione.** Per ripristinare da un backup, nel [portale di Azure classico](https://manage.windowsazure.com/), eseguire i passaggi seguenti.

#### Per ripristinare una condivisione

1.  Passare a **Catalogo backup**. Filtrare per dispositivo e intervallo di tempo appropriati per ricercare i backup. Fare clic sull'icona del segno di spunta ![](./media/storsimple-ova-restore/image1.png) per eseguire la query.


1.  Nell'elenco dei set di backup visualizzati, fare clic su un backup specifico per selezionarlo. Espandere il backup per visualizzare le varie condivisioni riportate sotto. Fare clic sulla condivisione che si desidera ripristinare per selezionarla.

2.  Nella parte inferiore della pagina fare clic su **Ripristina come nuovo**.

3.  Viene avviata la procedura guidata **Ripristina come nuova condivisione**. Nella pagina **Specificare nome e percorso**:


	1.  Verificare il nome del dispositivo di origine. Deve essere il dispositivo contenente la condivisione che si desidera ripristinare. La selezione del dispositivo è disabilitata. Per selezionare un dispositivo di origine diverso, è necessario uscire dalla procedura guidata e selezionare di nuovo il set di backup.

	2.  Fornire un nome per la condivisione. Il nome della condivisione deve contenere da 3 a 127 caratteri.

	3.  Esaminare le dimensioni, il tipo e le autorizzazioni associate alla condivisione che si sta cercando di ripristinare. Sarà possibile modificare le proprietà di condivisione tramite Esplora risorse dopo il completamento del ripristino.

	4.  Fare clic sull’icona del segno di spunta ![](./media/storsimple-ova-restore/image1.png).

		![](./media/storsimple-ova-restore/image9.png)

1.  Al completamento del processo di ripristino, verrà avviato il ripristino e un'altra notifica verrà visualizzata. Per monitorare lo stato del ripristino, fare clic su **Visualizza processo**. Viene visualizzata la pagina **Processi**.

2.  È possibile monitorare lo stato del processo di ripristino. Quando il ripristino è stato completato al 100%, tornare alla pagina **Condivisioni** nel dispositivo.

3.  È ora possibile visualizzare la nuova condivisione ripristinata nell'elenco delle condivisioni nel dispositivo. Notare che il ripristino riporta allo stesso tipo di condivisione. Una condivisione a livelli viene ripristinata come condivisione a livelli e una condivisione aggiunta in locale come una condivisione aggiunta in locale.

La configurazione del dispositivo è ora completa e si conosce il modo in cui eseguire il backup o il ripristino di una condivisione.


## Ripristinare volumi da un set di backup


Per ripristinare da un backup, nel portale di Azure classico, eseguire i passaggi seguenti. L'operazione di ripristino consente di ripristinare il backup in un nuovo volume nello stesso dispositivo virtuale. Non è possibile ripristinare in un dispositivo diverso.

#### Per ripristinare un volume

1.  Passare a **Catalogo backup**. Filtrare per dispositivo e intervallo di tempo appropriati per ricercare i backup. Fare clic sull'icona del segno di spunta ![](./media/storsimple-ova-restore/image1.png) per eseguire la query.

2.  Nell'elenco dei set di backup visualizzati, fare clic su un backup specifico per selezionarlo. Espandere il backup per visualizzare i diversi volumi riportati sotto. Selezionare il volume da ripristinare.

5.  Nella parte inferiore della pagina fare clic su **Ripristina come nuovo**. Viene avviata la procedura guidata per il **ripristino come nuovo volume**.

1.  Nella pagina **Specificare nome e percorso**:


	1.  Verificare il nome del dispositivo di origine. Deve essere il dispositivo contenente il volume che si desidera ripristinare. La selezione del dispositivo non è disponibile. Per selezionare un dispositivo di origine diverso, è necessario uscire dalla procedura guidata e selezionare di nuovo il set di backup.

	2.  Fornire un nome per il volume da ripristinare come nuovo volume. Il nome del volume deve contenere da 3 a 127 caratteri.

	3.  Fare clic sull'icona a forma di freccia.

		![](./media/storsimple-ova-restore/image12.png)

1.  Nella pagina **Specificare gli host che possono usare questo volume** selezionare i record di controllo di accesso appropriati nell'elenco a discesa.

	![](./media/storsimple-ova-restore/image13.png)

1.  Fare clic sull’icona del segno di spunta ![](./media/storsimple-ova-restore/image1.png). Si avvia così un processo di ripristino e viene visualizzata la notifica seguente in cui si indica che il processo è in corso.

2.  Al completamento del processo di ripristino, verrà avviato il ripristino e un'altra notifica verrà visualizzata. Per monitorare lo stato del ripristino, fare clic su **Visualizza processo**. Viene visualizzata la pagina **Processi**.

3.  È possibile monitorare lo stato del processo di ripristino. Tornare alla pagina **Volumi** nel dispositivo.

4.  È ora possibile visualizzare il nuovo volume ripristinato nell'elenco dei volumi nel dispositivo. Si noti che il ripristino riporta allo stesso tipo di volume. Un volume a livelli viene ripristinato come volume a livelli e un volume aggiunto in locale come un volume aggiunto in locale.

5.  Quando viene visualizzato nell'elenco dei volumi online, il volume è disponibile per l'uso. Aggiornare l'elenco delle destinazioni nella finestra Proprietà iniziatore iSCSI nell'host dell'iniziatore iSCSI. Una nuova destinazione contenente il nome del volume ripristinato verrà visualizzata come "inattiva" nella colonna dello stato.

6.  Selezionare la destinazione e fare clic su **Connetti**. Dopo avere connesso il dispositivo, lo stato deve essere modificato in **Connesso**.

7.  Nella finestra **Gestione disco**, i volumi montati verranno visualizzati come indicato nella figura seguente. Fare clic con il pulsante destro del mouse sul volume individuato (fare clic sul nome del disco) e fare clic su **Online**.

> [AZURE.IMPORTANT] Quando si tenta di ripristinare un volume o una condivisione da un set di backup, anche se il processo di ripristino non riesce, è comunque possibile creare nel portale un volume o una condivisione di destinazione. È importante eliminare questo volume o questa condivisione di destinazione nel portale per ridurre al minimo eventuali problemi futuri causati da questo elemento.

## Ripristino a livello di elemento (ILR)

Questa versione introduce il ripristino a livello di elemento (ILR) su un dispositivo virtuale StorSimple configurato come file server. La funzionalità consente di eseguire il ripristino granulare di file e cartelle da un backup cloud di tutte le condivisioni nel dispositivo StorSimple. Gli utenti possono recuperare i file eliminati dai backup recenti con un modello self-service.

Ogni condivisione dispone di una cartella *.backups* contenente i backup più recenti. L'utente può passare al backup desiderato, copiare i file e le cartelle rilevanti dal backup, quindi ripristinarli. In questo modo, si eliminano le chiamate agli amministratori per il ripristino di file dai backup.

1.  Quando si esegue un ILR, è possibile visualizzare i backup con Esplora risorse. Fare clic sulla condivisione specifica per la quale si desidera visualizzare il backup. Viene visualizzata una cartella *.backups* creata sotto la condivisione che archivia tutti i backup. Espandere la cartella *.backups* per visualizzare i backup. La cartella mostra così in dettaglio l'intera gerarchia di backup. Questa visualizzazione viene creata su richiesta e generalmente bastano pochi secondi per completarne la creazione.

	Le ultime 5 copie di backup vengono visualizzate in questo modo e possono essere utilizzate per eseguire un ripristino a livello di elemento. Le 5 copie di backup più recenti includono sia i backup pianificati predefiniti sia i backup manuali.

	
	-   **Backup pianificati** denominati &lt;Device name&gt;DailySchedule-YYYYMMDD-HHMMSS-UTC.

	-   **Backup manuali** denominati Ad-hoc-YYYYMMDD-HHMMSS-UTC.
	
		![](./media/storsimple-ova-restore/image14.png)

1.  Identificare il backup contenente la versione più recente del file eliminato. Anche se il nome della cartella contiene un timestamp UTC in ognuno dei casi precedenti, l'ora in cui è stata creata la cartella è l'ora effettiva del dispositivo all'avvio del backup. Usare il timestamp della cartella per individuare e identificare i backup.

2.  Individuare la cartella o il file che si desidera ripristinare nel backup identificato nel passaggio precedente. Notare che è possibile visualizzare solo i file o le cartelle per cui si dispone delle autorizzazioni necessarie. Se non si riesce ad accedere a determinati file o cartelle, è necessario contattare un amministratore della condivisione in grado di usare Esplora risorse per modificare le autorizzazioni di condivisione e consentire l'accesso al file o alla cartella specifici. Preferibilmente, l'amministratore della condivisione deve corrispondere a un gruppo utenti anziché a un utente singolo.

3.  Copiare il file o la cartella nella condivisione appropriata nel file server StorSimple.

![video\_icon](./media/storsimple-ova-restore/video_icon.png) **Video disponibile**

Guardare il video per scoprire come è possibile creare condivisioni, eseguire il backup di condivisioni e il ripristino dei dati in StorSimple Virtual Array.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## Passaggi successivi

Informazioni su come [Usare l'interfaccia utente Web per amministrare StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0629_2016-->