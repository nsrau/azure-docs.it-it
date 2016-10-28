<properties 
   pageTitle="Disattivare ed eliminare un array virtuale StorSimple | Microsoft Azure"
   description="Viene descritto come rimuovere un dispositivo StorSimple dal servizio disattivandolo e poi eliminandolo."
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
   ms.date="06/20/2016"
   ms.author="alkohli" />

# Disattivare ed eliminare un array virtuale StorSimple

## Panoramica

Quando si disattiva un array virtuale StorSimple, si interrompe la connessione tra il dispositivo e il servizio StorSimple Manager corrispondente. La disattivazione è un'operazione permanente e non può essere annullata. Un dispositivo disattivato non può essere registrato di nuovo con il servizio StorSimple Manager.

Può essere necessario disattivare ed eliminare un dispositivo virtuale StorSimple negli scenari seguenti:


- Il dispositivo è in linea e si prevede di eseguirne il failover. Può essere necessario eseguire questa operazione se si prevede di eseguire l'aggiornamento a un dispositivo di maggiori dimensioni. Una volta trasferiti i dati del dispositivo e completato il failover, è possibile eliminare il dispositivo.

- Il dispositivo è offline e si prevede di eseguirne il failover. Questa situazione può verificarsi se, a causa di un'interruzione di corrente nel data center, il dispositivo principale si arresta. Si pianifica di eseguire il failover del dispositivo su un dispositivo secondario. Dopo aver trasferito i dati del dispositivo e completato il failover, è possibile eliminare il dispositivo.

- Si vuole rimuovere il dispositivo e quindi eliminarlo.
 

Quando si disattiva un dispositivo, tutti i dati archiviati localmente non saranno più accessibili. È possibile recuperare solo i dati archiviati nel cloud. Se si pianifica cdi mantenere il dispositivo dopo la disattivazione, prima di effettuare tale operazione è necessario eseguire uno snapshot di tutti i dati nel cloud. In questo modo sarà possibile recuperare tutti i dati in una fase successiva.


In questa esercitazione viene illustrato come:

- Disattivare un dispositivo 
- Eliminare un dispositivo disattivato


## Disattivare un dispositivo

Per disattivare un dispositivo seguire questa procedura.

#### Per disattivare il dispositivo   

1. Passare alla pagina **Dispositivi**. Selezionare il dispositivo da disattivare.

	![Selezione del dispositivo disattivare](./media/storsimple-ova-deactivate-and-delete-device/deactivate1m.png)

3. Nella parte inferiore della pagina fare clic su **Disattiva**.

	![Clic su Disattiva](./media/storsimple-ova-deactivate-and-delete-device/deactivate2m.png)

4. Verrà visualizzato un messaggio di conferma. Fare clic su **Sì** per continuare.

	![Conferma della disattivazione](./media/storsimple-ova-deactivate-and-delete-device/deactivate3m.png)

	Si avvierà il processo di disattivazione e il suo completamento richiederà alcuni minuti.

	![Disattivazione in corso](./media/storsimple-ova-deactivate-and-delete-device/deactivate4m.png)

3. Dopo la disattivazione, l'elenco dei dispositivi verrà aggiornato.

	![Disattivazione completata](./media/storsimple-ova-deactivate-and-delete-device/deactivate5m.png)

	È ora possibile eliminare il dispositivo.

## Eliminare il dispositivo

Per eliminare un dispositivo, è prima necessario disattivarlo. L’eliminazione di un dispositivo lo rimuove dall'elenco dei dispositivi connessi al servizio. Il servizio quindi non può più gestire il dispositivo eliminato. I dati associati al dispositivo rimarranno comunque nel cloud. Tenere presente che questi dati determinano un aumento dei costi.

Completare la procedura seguente per eliminare il dispositivo:

#### Per eliminare il dispositivo 

 1. Alla pagina**dispositivi**di servizio StorSimple Manager, selezionare un dispositivo disattivato che si desidera eliminare.

	![Selezione del dispositivo da eliminare](./media/storsimple-ova-deactivate-and-delete-device/deactivate5m.png)

 2. Nella parte inferiore della pagina fare clic su **Elimina**.
 
	![Clic su Elimina](./media/storsimple-ova-deactivate-and-delete-device/deactivate6m.png)

 3. Verrà richiesto di confermare. Digitare il nome del dispositivo per confermarne l'eliminazione. Si noti che l'eliminazione del dispositivo non eliminerà i dati ad esso associati presenti nel cloud. Fare clic sull’icona del segno di spunta per continuare.
 
	![Conferma dell'eliminazione](./media/storsimple-ova-deactivate-and-delete-device/deactivate7m.png)

 5. L'eliminazione del dispositivo può richiedere alcuni minuti.

	![Eliminazione in corso](./media/storsimple-ova-deactivate-and-delete-device/deactivate8m.png)

 	Dopo l'eliminazione del dispositivo, l'elenco dei dispositivi verrà aggiornato.

	![Eliminazione completata](./media/storsimple-ova-deactivate-and-delete-device/deactivate9m.png)


## Passaggi successivi

- Per ulteriori informazioni sull’utilizzo del servizio StorSimple Manager, vedere l'articolo relativo all'[uso di StorSimple Manager per amministrare l'array virtuale StorSimple](storsimple-ova-manager-service-administration.md). 

<!---HONumber=AcomDC_0622_2016-->