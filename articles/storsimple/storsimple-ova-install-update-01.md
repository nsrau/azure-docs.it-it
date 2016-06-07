<properties 
   pageTitle="Installare l'aggiornamento 0.1 all'array virtuale StorSimple | Microsoft Azure"
   description="Viene illustrato come usare l'interfaccia utente Web dell'array virtuale StorSimple per applicare l’aggiornamento 0.1 tramite il metodo del portale e degli hotfix."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/24/2016"
   ms.author="alkohli" />

# Installare l'aggiornamento 0.1 sull'array virtuale StorSimple

## Panoramica

Questo articolo descrive i passaggi necessari per installare l'aggiornamento 0.1 nell'array virtuale StorSimple. Descrive le procedure di aggiornamento tramite il portale di Azure classico e tramite l'interfaccia utente Web locale. Ogni procedura di aggiornamento richiede un massimo di 2 minuti per il completamento.

Generalmente è necessario applicare aggiornamenti software o hotfix per mantenere l'array virtuale StorSimple sempre aggiornato. In generale, si consiglia di installare gli aggiornamenti tramite il portale di Azure classico. Tuttavia, nei casi in cui il portale non sia disponibile, è possibile usare l'interfaccia utente Web locale per applicare hotfix o aggiornamenti.

Tenere presente che l'installazione di un aggiornamento o un hotfix potrebbe riavviare il dispositivo. Dato che l'array virtuale StorSimple è un dispositivo a nodo singolo, vengono interrotti tutti gli IO in corso e il dispositivo registra un periodo di inattività.

Prima di applicare un aggiornamento, si consiglia di portare offline i volumi o le condivisioni, prima nell'host e poi nel dispositivo. Questa operazione consente di eliminare qualsiasi rischio di danneggiamento dei dati.

## Usare il portale di Azure classico

Si consiglia di installare gli aggiornamenti tramite il portale di Azure classico. La procedura del portale richiede all'utente di analizzare, scaricare e installare gli aggiornamenti. Seguire questa procedura per installare l'aggiornamento o l'hotfix.

#### Per installare gli aggiornamenti tramite il portale di Azure classico

1. Nella pagina **Dispositivi**, selezionare il dispositivo in cui si desidera installare gli aggiornamenti.

2. Passare a **Dispositivi** > **Manutenzione** > **Aggiornamenti software**.

3. Se sono disponibili aggiornamenti software, verrà visualizzato un messaggio. Per controllare la disponibilità di aggiornamenti, fare clic su **Verifica aggiornamenti** nella parte inferiore della pagina.

	![Verificare gli aggiornamenti](./media/storsimple-ova-install-update-01/aupdate1m.png)

4. Fare clic su **Scarica aggiornamenti** nella parte inferiore della pagina. Una finestra di dialogo comunica all'utente che l'aggiornamento è problematico. Dato che l'array virtuale StorSimple è un dispositivo a nodo singolo, si riavvierà dopo l'aggiornamento. Tutti gli IO in corso verranno interrotti. Fare clic sull'icona di segno di spunta per avviare il processo di download degli aggiornamenti disponibili.

	![Confermare il download degli aggiornamenti](./media/storsimple-ova-install-update-01/aupdate3m.png)

	Al completamento del download degli aggiornamenti, l'utente riceverà comunicazione.

	![Scaricare gli aggiornamenti](./media/storsimple-ova-install-update-01/aupdate5m.png)

5. Nella parte inferiore della pagina fare clic su **Installa aggiornamenti** per avviare l'aggiornamento del dispositivo. Verrà visualizzata nuovamente la finestra di dialogo. Fare clic sull'icona di segno di spunta per avviare il processo di installazione degli aggiornamenti disponibili.
 
 	![Confermare l'installazione degli aggiornamenti](./media/storsimple-ova-install-update-01/aupdate6m.png)
 
 
	Al termine della creazione del processo, l'utente riceverà comunicazione.

	![Installare gli aggiornamenti](./media/storsimple-ova-install-update-01/aupdate8m.png)
	
6. Fare clic sul collegamento **Visualizza processo** per aprire la pagina **Processi** e monitorare lo stato dell'installazione. È possibile fare clic su **Dettagli** in qualsiasi momento per ottenere informazioni dettagliate sul processo di aggiornamento.

	![Monitorare gli aggiornamenti](./media/storsimple-ova-install-update-01/aupdate9m.png)

6. Al termine dell'installazione (indicata dallo stato del processo al 100%), andare a **Dispositivi** > **Manutenzione** > **Aggiornamenti software**. La versione del software visualizzata dovrebbe essere **10.0.10279.0**.

	![Verificare gli aggiornamenti](./media/storsimple-ova-install-update-01/aupdate13m.png)

## Usare l'interfaccia utente Web locale 

Usare l'interfaccia utente Web locale per applicare gli aggiornamenti quando il portale di Azure classico non è disponibile. Quando si usa l'interfaccia utente Web locale, è necessario eseguire due passaggi:

- Scaricare l'aggiornamento o l'hotfix
- Installare l'aggiornamento o l'hotfix

### Scaricare l'aggiornamento o l'hotfix

Eseguire i passaggi seguenti per scaricare l'aggiornamento del software da Microsoft Update Catalog.

#### Per scaricare l'aggiornamento o l'hotfix

1. Avviare Internet Explorer e accedere al sito [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Se si usa per la prima volta Microsoft Update Catalog sul computer, fare clic su **Installa** quando viene richiesto di installare il componente aggiuntivo Microsoft Update Catalog.
   
	![Installare il catalogo](./media/storsimple-ova-install-update-01/install-catalog.png)

3. Nella casella di ricerca di Microsoft Update Catalog, immettere il numero della Knowledge Base (KB) dell'hotfix da scaricare, ad esempio **3160441**, quindi fare clic su **Ricerca**.

    Viene visualizzato l'elenco degli hotfix, tra cui l'**aggiornamento 0.1 per l'array virtuale di Microsoft Azure StorSimple**.

    ![Cercare nel catalogo](./media/storsimple-ova-install-update-01/download1.png)

4. Fare clic su **Aggiungi**. L'aggiornamento viene aggiunto al carrello.

5. Fare clic su **Visualizza carrello**.

6. Fare clic su **Download**. Specificare o **sfogliare** il percorso locale in cui si desidera salvare i file scaricati. Gli aggiornamenti vengono scaricati nel percorso specificato e inseriti in una sottocartella con lo stesso nome dell'aggiornamento. Inoltre, la cartella può essere copiata in una condivisione di rete raggiungibile dal dispositivo.


### Installare l'aggiornamento o l'hotfix

Prima dell'installazione di un aggiornamento o un hotfix, assicurarsi di avere scaricato l'aggiornamento o l'hotfix in locale o sull'host, altrimenti che siano accessibili tramite una condivisione di rete. Seguire questa procedura per installare l'aggiornamento o l'hotfix.

#### Per installare l'aggiornamento o l'hotfix

1. Nell'interfaccia utente Web locale, andare a **Manutenzione** > **Aggiornamento software**.

2. In **Percorso del file di aggiornamento**, immettere il nome del file per l'aggiornamento o l'hotfix. È possibile anche cercare il file di installazione dell'aggiornamento o dell'hotfix, se posizionato in una condivisione di rete. Fare clic su **Apply**.

	![aggiornamento dispositivo](./media/storsimple-ova-install-update-01/update1m.png)

3.  Verrà visualizzato un avviso. Dato che si tratta di un dispositivo a nodo singolo, dopo l'applicazione dell'aggiornamento, il dispositivo si riavvia con un conseguente periodo di inattività. Fare clic sull'icona del segno di spunta

	![aggiornamento dispositivo](./media/storsimple-ova-install-update-01/update4m.png)

4. Si avvia così l'aggiornamento. Dopo l'aggiornamento, il dispositivo si riavvia in automatico. In questo periodo di tempo, l'interfaccia utente locale non è accessibile.

    ![aggiornamento dispositivo](./media/storsimple-ova-install-update-01/update6m.png)

4. Al termine del riavvio, si viene indirizzati alla pagina di accesso. Da qui è possibile verificare la versione del software. Andare a **Manutenzione** > **Aggiornamento software**. Il numero di build deve essere **10.0.0.0.10279**.

	> [AZURE.NOTE] Le versioni del software vengono riportate in modo leggermente diverso nell'interfaccia utente Web locale e nel portale di Azure classico. L'interfaccia utente Web locale riporta **10.0.0.0.10279**, mentre il portale di Azure classico riporta **10.0.10279.0** per la stessa versione.

	![aggiornamento dispositivo](./media/storsimple-ova-install-update-01/update9m.png)

## Passaggi successivi

Altre informazioni sull'[amministrazione dell'array virtuale StorSimple](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0525_2016-->