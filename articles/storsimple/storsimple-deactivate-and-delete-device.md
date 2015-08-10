<properties 
   pageTitle="Disattivare ed eliminare un dispositivo StorSimple | Microsoft Azure"
   description="Viene descritto come rimuovere dispositivo StorSimple dal servizio disattivandolo per poi eliminarlo."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/09/2015"
   ms.author="v-sharos" />

# Disattivare ed eliminare un dispositivo StorSimple

In questa esercitazione viene illustrato come rimuovere un dispositivo StorSimple dal servizio disattivandolo per poi eliminarlo.

>[AZURE.NOTE]E’ necessario disattivare un dispositivo prima di eliminarlo.

## Disattivare un dispositivo

È possibile accada di voler dismettere un dispositivo. In questo caso, il dispositivo dovrà essere disattivato. Disattivando i server si interrompe la connessione tra il dispositivo e il servizio StorSimple Manager corrispondente.

>[AZURE.WARNING]La disattivazione è un'operazione permanente e non può essere annullata. Un dispositivo disattivato non può essere registrato con il servizio StorSimple Manager a meno che non venga ripristinato con le impostazioni di fabbrica.

Quando si disattiva un dispositivo, tutti i dati archiviati localmente nel dispositivo non saranno più accessibili. Possono essere recuperati solo i dati associati al dispositivo che sono stati archiviati nel cloud. Una volta disattivato, un dispositivo deve essere ripristinato con le impostazioni di fabbrica, prima che possa essere riutilizzato con un servizio esistente o uno nuovo. Il processo di ripristino delle impostazioni di fabbrica elimina tutti i dati archiviati localmente sul dispositivo. Pertanto, è essenziale eseguire uno snapshot nel cloud di tutti i dati prima di disattivare un dispositivo. In questo modo sarà possibile recuperare tutti i dati in una fase successiva.

Per un dispositivo virtuale StorSimple, la disattivazione consente di eliminare la macchina virtuale e le risorse create quando si è stato predisposto. Dopo aver disattivato il dispositivo, non è possibile ripristinarne lo stato precedente. Prima di disattivare un dispositivo virtuale StorSimple, assicurarsi di arrestare o eliminare i client e gli host che dipendono da tale dispositivo virtuale.

### Disattivare ed eliminare i dati

Se si desidera eliminare completamente il dispositivo e non si desidera conservare i dati del dispositivo, eseguire le operazioni seguenti:

1. Prima di disattivare un dispositivo, è necessario eliminare i contenitori del volume (e i volumi) associati al dispositivo. È possibile eliminare i contenitori dei volumi solo dopo aver eliminato i backup associati.

2. Disattivare il dispositivo. Andare a[Procedura per la disattivazione](#steps-to-deactivate)per le istruzioni.

3. Dopo la disattivazione, è possibile eliminare completamente il dispositivo. Andare a[Eliminare un dispositivo](#delete-a-device)per le istruzioni.

### Disattivare e conservare i dati

Se interessati all’eliminazione del dispositivo, ma si desidera conservarne i dati, eseguire le operazioni seguenti:

1. Disattivare il dispositivo. Tutti i contenitori del volume e gli snapshot del dispositivo verranno mantenuti. Andare a[Procedura per la disattivazione](#steps-to-deactivate)per le istruzioni.

2. È ora possibile eseguire il failover dei contenitori del volume e degli snapshot associati. Per le procedure, vedere[Failover e ripristino di emergenza per il dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md)

3. Dopo la disattivazione e failover, è possibile eliminare completamente il dispositivo. Andare a[Eliminare un dispositivo](#delete-a-device)per le istruzioni.

### Passaggi per disattivare

Utilizzare la procedura seguente per disattivare un dispositivo in preparazione per l'eliminazione.

#### Per disattivare un dispositivo

1. Alla pagina **dispositivi**di servizio StorSimple Manager, selezionare il dispositivo che si desidera disattivare e nella parte inferiore della pagina, fare clic su**Disattiva**.

2. Verrà visualizzato un messaggio di conferma. Fare clic su **Sì** per continuare. Si avvierà il processo di disattivazione e il suo completamento richiederà alcuni minuti.

    In un dispositivo virtuale StorSimple, la disattivazione determina le azioni seguenti:

      - Viene rimosso il dispositivo virtuale StorSimple.

      - L'OSDisk e i dischi dati creati per il dispositivo virtuale StorSimple vengono rimossi.

      - Il servizio ospitato e la rete virtuale creata durante il provisioning vengono mantenuti. Se non si utilizzano, è necessario eliminarli manualmente.

      - Vengono mantenuti gli snapshot cloud creati per il dispositivo virtuale StorSimple.

<!--After the device is deactivated, you will need to perform a failover before you can delete it completely. For failover instructions, go to [Failover and disaster recovery for your StorSimple device](storsimple-device-failover-disaster-recovery.md).-->
## Eliminazione di un dispositivo

È possibile eliminare solo i dispositivi che sono stati disattivati. L’eliminazione di un dispositivo lo rimuove dall'elenco dei dispositivi connessi al servizio. Il servizio quindi non può più gestire il dispositivo eliminato.

#### Per eliminare un dispositivo

1. Alla pagina**dispositivi**di servizio StorSimple Manager, selezionare un dispositivo disattivato che si desidera eliminare.

2. Nella parte inferiore della pagina fare clic su **Elimina**.

3. Verrà richiesto di confermare. Fare clic su **Sì** per continuare.

L'eliminazione del dispositivo può richiedere alcuni minuti.

## Passaggi successivi
Per ripristinare il dispositivo disattivato sulle impostazioni predefinite di fabbrica, passare a[Reimposta il dispositivo sulle impostazioni predefinite](https://msdn.microsoft.com/library/dn772373.aspx).

Per assistenza tecnica,[contattare il supporto Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx).

<!---HONumber=July15_HO5-->