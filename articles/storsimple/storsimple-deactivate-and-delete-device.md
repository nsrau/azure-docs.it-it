---
title: Disattivare ed eliminare un dispositivo StorSimple | Microsoft Docs
description: Viene descritto come rimuovere un dispositivo StorSimple dal servizio disattivandolo e poi eliminandolo.
services: storsimple
documentationcenter: ''
author: SharS
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/01/2016
ms.author: anoobbacker

---
# Disattivare ed eliminare un dispositivo StorSimple
## Panoramica
È possibile mettere fuori servizio un dispositivo StorSimple, ad esempio se si sta sostituendo o aggiornando il dispositivo o se non si usa più StorSimple. In questo caso, è necessario disattivare il dispositivo prima di eliminarlo. Disattivando i server si interrompe la connessione tra il dispositivo e il servizio StorSimple Manager corrispondente. Questa esercitazione illustra come rimuovere un dispositivo StorSimple dal servizio disattivandolo per poi eliminarlo.

Quando si disattiva un dispositivo, tutti i dati archiviati localmente nel dispositivo non saranno più accessibili. Possono essere recuperati solo i dati associati al dispositivo che sono stati archiviati nel cloud.

> [!WARNING]
> La disattivazione è un'operazione permanente e non può essere annullata. Un dispositivo disattivato non può essere registrato con il servizio StorSimple Manager a meno che non venga ripristinato con le impostazioni di fabbrica.
> 
> Il processo di ripristino delle impostazioni di fabbrica elimina tutti i dati archiviati localmente sul dispositivo. Pertanto, è essenziale eseguire uno snapshot nel cloud di tutti i dati prima di disattivare un dispositivo. In questo modo sarà possibile recuperare tutti i dati in una fase successiva.
> 
> 

In questa esercitazione viene illustrato come:

* Disattivare un dispositivo ed eliminare i dati
* Disattivare un dispositivo e conservare i dati

Spiega anche come funzionano la disattivazione e l'eliminazione su un dispositivo virtuale StorSimple.

> [!NOTE]
> Prima di disattivare un dispositivo fisico o virtuale StorSimple, assicurarsi di arrestare o eliminare i client e gli host che dipendono da tale dispositivo.
> 
> 

## Disattivare ed eliminare i dati
Se si vuole eliminare completamente il dispositivo senza conservarne i dati, seguire questa procedura.

#### Per disattivare il dispositivo ed eliminare i dati
1. Prima di disattivare un dispositivo, è necessario eliminare i contenitori del volume (e i volumi) associati al dispositivo. È possibile eliminare i contenitori dei volumi solo dopo aver eliminato i backup associati.
2. Disattivare il dispositivo come segue:
   
   1. Alla pagina **dispositivi**di servizio StorSimple Manager, selezionare il dispositivo che si desidera disattivare e nella parte inferiore della pagina, fare clic su**Disattiva**.
   2. Verrà visualizzato un messaggio di conferma. Fare clic su **Sì** per continuare. Si avvierà il processo di disattivazione e il suo completamento richiederà alcuni minuti.
3. Dopo la disattivazione, è possibile eliminare completamente il dispositivo. L’eliminazione di un dispositivo lo rimuove dall'elenco dei dispositivi connessi al servizio. Il servizio quindi non può più gestire il dispositivo eliminato. Completare la procedura seguente per eliminare il dispositivo:
   
   1. Alla pagina**dispositivi**di servizio StorSimple Manager, selezionare un dispositivo disattivato che si desidera eliminare.
   2. Nella parte inferiore della pagina fare clic su **Elimina**.
   3. Verrà richiesto di confermare. Fare clic su **Sì** per continuare.
      
      L'eliminazione del dispositivo può richiedere alcuni minuti.

## Disattivare e conservare i dati
Se si è interessati all'eliminazione del dispositivo ma si vuole conservarne i dati, seguire questa procedura.

#### Per disattivare un dispositivo e conservare i dati
1. Disattivare il dispositivo. Tutti i contenitori del volume e gli snapshot del dispositivo verranno mantenuti.
   
   1. Alla pagina **dispositivi**di servizio StorSimple Manager, selezionare il dispositivo che si desidera disattivare e nella parte inferiore della pagina, fare clic su**Disattiva**.
   2. Verrà visualizzato un messaggio di conferma. Fare clic su **Sì** per continuare. Si avvierà il processo di disattivazione e il suo completamento richiederà alcuni minuti.
2. È ora possibile eseguire il failover dei contenitori del volume e degli snapshot associati. Per le procedure, vedere[Failover e ripristino di emergenza per il dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md)
3. Dopo la disattivazione e failover, è possibile eliminare completamente il dispositivo. L’eliminazione di un dispositivo lo rimuove dall'elenco dei dispositivi connessi al servizio. Il servizio quindi non può più gestire il dispositivo eliminato. Completare la procedura seguente per eliminare il dispositivo:
   
   1. Alla pagina**dispositivi**di servizio StorSimple Manager, selezionare un dispositivo disattivato che si desidera eliminare.
   2. Nella parte inferiore della pagina fare clic su **Elimina**.
   3. Verrà richiesto di confermare. Fare clic su **Sì** per continuare.
      
      L'eliminazione del dispositivo può richiedere alcuni minuti.

## Disattivare ed eliminare un dispositivo virtuale
Per un dispositivo virtuale StorSimple, la disattivazione dealloca la macchina virtuale. È quindi possibile eliminare la macchina virtuale e le risorse create quando è stato effettuato il provisioning. Dopo aver disattivato il dispositivo, non è possibile ripristinarne lo stato precedente.

La disattivazione comporta le seguenti azioni:

* Viene rimosso il dispositivo virtuale StorSimple.
* L'OSDisk e i dischi dati creati per il dispositivo virtuale StorSimple vengono rimossi.
* Il servizio ospitato e la rete virtuale creata durante il provisioning vengono mantenuti. Se non si utilizzano, è necessario eliminarli manualmente.
* Vengono mantenuti gli snapshot cloud creati per il dispositivo virtuale StorSimple.

## Passaggi successivi
* Per ripristinare il dispositivo disattivato sulle impostazioni predefinite di fabbrica, passare a[Reimposta il dispositivo sulle impostazioni predefinite](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Per assistenza tecnica,[contattare il supporto Microsoft](storsimple-contact-microsoft-support.md).
* Per ulteriori informazioni sull’utilizzo del servizio StorSimple Manager, passare a[utilizzare il servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0608_2016-->