<properties 
   pageTitle="Installare aggiornamenti in un array virtuale StorSimple | Microsoft Azure"
   description="Viene illustrato come usare l'interfaccia utente Web dell'array virtuale StorSimple per applicare aggiornamenti tramite il portale e gli hotfix."
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
   ms.date="06/16/2016"
   ms.author="alkohli" />

# Installare aggiornamenti nell'array virtuale StorSimple

## Panoramica

Questo articolo descrive i passaggi necessari per installare aggiornamenti nell'array virtuale StorSimple utilizzando l'interfaccia utente Web locale. Questa procedura di aggiornamento richiede un massimo di 2 minuti per il completamento. È necessario applicare aggiornamenti software o hotfix per mantenere StorSimple Virtual Array sempre aggiornato.

Tenere presente che l'installazione di un aggiornamento o un hotfix potrebbe riavviare il dispositivo. Dato che l'array virtuale StorSimple è un dispositivo a nodo singolo, gli eventuali I/O in corso vengono interrotti e il dispositivo registra un periodo di inattività.

Prima di applicare un aggiornamento, si consiglia di portare offline i volumi o le condivisioni, prima nell'host e poi nel dispositivo. Questa operazione consente di eliminare qualsiasi rischio di danneggiamento dei dati.

## Usare l'interfaccia utente Web locale 
Per installare l'aggiornamento più recente, è necessario utilizzare l'interfaccia utente Web locale per applicare aggiornamenti e hotfix (al momento l'aggiornamento non può essere installato tramite il portale di Azure classico).

Quando si usa l'interfaccia utente Web locale, è necessario eseguire due passaggi:

- Scaricare l'aggiornamento o l'hotfix
- Installare l'aggiornamento o l'hotfix

### Scaricare l'aggiornamento o l'hotfix

Eseguire i passaggi seguenti per scaricare l'aggiornamento del software da Microsoft Update Catalog.

#### Per scaricare l'aggiornamento o l'hotfix

1. Avviare Internet Explorer e accedere al sito [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Se si usa per la prima volta Microsoft Update Catalog sul computer, fare clic su **Installa** quando viene richiesto di installare il componente aggiuntivo Microsoft Update Catalog.
   
	![Installare il catalogo](./media/storsimple-ova-install-update-01/install-catalog.png)

3. Nella casella di ricerca di Microsoft Update Catalog, immettere il numero dell'hotfix da scaricare riportato nella Knowledge Base (KB). Digitare **3160441** per l'Aggiornamento 0.2 oppure il numero **3160441** per l'Aggiornamento 0.1, quindi fare clic su **Cerca**.

    Viene visualizzato l'elenco degli hotfix, tra cui l'**Aggiornamento 0.1 per l'array virtuale StorSimple**.

    ![Cercare nel catalogo](./media/storsimple-ova-install-update-01/download1.png)

4. Fare clic su **Aggiungi**. L'aggiornamento viene aggiunto al carrello.

5. Fare clic su **Visualizza carrello**.

6. Fare clic su **Download**. Specificare o **selezionare** il percorso locale in cui si desidera salvare i file scaricati. Gli aggiornamenti vengono scaricati nel percorso specificato e inseriti in una sottocartella con lo stesso nome dell'aggiornamento. Inoltre, la cartella può essere copiata in una condivisione di rete raggiungibile dal dispositivo.


### Installare l'aggiornamento o l'hotfix

Prima dell'installazione di un aggiornamento o un hotfix, assicurarsi di avere scaricato l'aggiornamento o l'hotfix in locale o sull'host, altrimenti che siano accessibili tramite una condivisione di rete. Seguire questa procedura per installare l'aggiornamento o l'hotfix.

#### Per installare l'aggiornamento o l'hotfix

1. Nell'interfaccia utente Web locale, accedere a **Manutenzione** > **Aggiornamento software**.

2. In **Percorso del file di aggiornamento**, immettere il nome del file dell'aggiornamento o dell'hotfix. È possibile anche cercare il file di installazione dell'aggiornamento o dell'hotfix, se posizionato in una condivisione di rete. Fare clic su **Apply**.

	![aggiornamento dispositivo](./media/storsimple-ova-install-update-01/update1m.png)

3.  Verrà visualizzato un avviso. Dato che si tratta di un dispositivo a nodo singolo, dopo l'applicazione dell'aggiornamento, il dispositivo si riavvia con un conseguente periodo di inattività. Fare clic sull'icona del segno di spunta

	![aggiornamento dispositivo](./media/storsimple-ova-install-update-01/update4m.png)

4. Si avvia così l'aggiornamento. Dopo l'aggiornamento, il dispositivo si riavvia in automatico. In questo periodo di tempo, l'interfaccia utente locale non è accessibile.

    ![aggiornamento dispositivo](./media/storsimple-ova-install-update-01/update6m.png)

4. Al termine del riavvio, si viene indirizzati alla pagina di accesso. Da qui è possibile verificare la versione del software. Accedere a **Manutenzione** > **Aggiornamento software**. Dovrebbe essere visualizzata la versione del software **10.0.10280.0** per l'Aggiornamento 0.2 oppure **10.0.10279.0** per l'Aggiornamento 0.1.

	> [AZURE.NOTE] Le versioni del software vengono riportate in modo leggermente diverso nell'interfaccia utente Web locale e nel portale di Azure classico. Ad esempio, l'interfaccia utente Web locale riporta **10.0.0.0.10279**, mentre il portale di Azure classico riporta **10.0.10279.0** per la stessa versione.

	![aggiornamento dispositivo](./media/storsimple-ova-install-update-01/update9m.png)

## Passaggi successivi

Scoprire come [amministrare l'array virtuale StorSimple](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0622_2016-->