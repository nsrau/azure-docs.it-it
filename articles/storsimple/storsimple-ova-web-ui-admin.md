<properties 
   pageTitle="Amministrazione dell'interfaccia utente Web dell'array virtuale StorSimple | Microsoft Azure"
   description="Viene illustrato come eseguire attività di amministrazione di base del dispositivo tramite l'interfaccia utente Web dell'array virtuale StorSimple."
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
   ms.date="04/07/2016"
   ms.author="alkohli" />

# Usare l'interfaccia utente Web per amministrare StorSimple Virtual Array

![flusso del processo di installazione](./media/storsimple-ova-web-ui-admin/manage4.png)

## Panoramica

Le esercitazioni in questo articolo si applicano a Microsoft Azure StorSimple Virtual Array (noto anche come dispositivo virtuale locale StorSimple) che esegue la versione di disponibilità generale (GA) di marzo 2016. Questo articolo illustra una parte dei flussi di lavoro e delle attività di gestione complessi eseguibili sull'array virtuale StorSimple. È possibile gestire StorSimple Virtual Array usando l'interfaccia utente del servizio StorSimple Manager (interfaccia utente del portale) e l'interfaccia utente Web locale per il dispositivo. Questo articolo è incentrato sulle attività che è possibile eseguire con l'interfaccia utente Web.

L'articolo include le esercitazioni seguenti:

- Ottenere la chiave DEK del servizio
- Risolvere i problemi relativi agli errori di installazione dell'interfaccia utente Web
- Generare un pacchetto di log
- Arrestare o riavviare il dispositivo

## Ottenere la chiave DEK del servizio

Una chiave DEK del servizio viene generata quando si registra il primo dispositivo con il servizio StorSimple Manager. Questa chiave viene richiesta con la chiave di registrazione del servizio per registrare altri dispositivi con il servizio StorSimple Manager.

Se la chiave DEK del servizio è stata smarrita ed è necessario recuperarla, eseguire i passaggi seguenti nell'interfaccia utente Web locale del dispositivo registrato con il servizio.

#### Per ottenere la chiave DEK del servizio

1. Connettersi all'interfaccia utente Web locale. Passare a **Configurazione** > **Impostazioni cloud**.
  

2. Nella parte inferiore della pagina fare clic su **Ottieni chiave DEK del servizio**. Viene visualizzata una chiave. Copiare e salvare questa chiave.
  	
	![ottenere la chiave DEK del servizio 1](./media/storsimple-ova-web-ui-admin/image27.png)
   


## Risolvere i problemi relativi agli errori di installazione dell'interfaccia utente Web

In alcuni casi, quando si configura il dispositivo tramite l'interfaccia utente Web locale, è possibile riscontrare alcuni errori. Per diagnosticare e risolvere questi errori, è possibile eseguire i test di diagnostica.

#### Per eseguire i test di diagnostica

1. Nell'interfaccia utente Web locale passare a **Risoluzione dei problemi** > **Test diagnostici**.

    ![eseguire diagnostica 1](./media/storsimple-ova-web-ui-admin/image29.png)

2. Nella parte inferiore della pagina fare clic su **Esegui test diagnostici**. Si avviano così i test per diagnosticare eventuali problemi con la rete, il dispositivo, il proxy Web, l'ora o le impostazioni cloud. Si riceve una notifica in cui si comunica che il dispositivo sta eseguendo dei test.

3. Al termine dei test, vengono visualizzati i risultati. L'esempio seguente mostra i risultati dei test di diagnostica. Notare che le impostazioni del proxy Web non sono state configurate in questo dispositivo, quindi il test del proxy Web non è stato eseguito. Tutti gli altri test per le impostazioni di rete, il server DNS e le impostazioni ora sono stati completati correttamente.

    ![eseguire diagnostica 2](./media/storsimple-ova-web-ui-admin/image30.png)

## Generare un pacchetto di log

Un pacchetto di log è costituito da tutti i log rilevanti utili al supporto tecnico Microsoft nella risoluzione dei problemi del dispositivo. In questa versione, un pacchetto di log può essere generato tramite l'interfaccia utente Web locale.

#### Per generare il pacchetto di log

1. Nell'interfaccia utente Web locale passare a **Risoluzione dei problemi** > **Log di sistema**.

    ![generare pacchetto di log 1](./media/storsimple-ova-web-ui-admin/image31.png)

2. Nella parte inferiore della pagina fare clic su **Crea pacchetto di log**. Viene creato un pacchetto di log del sistema. L'operazione richiede alcuni minuti.

    ![generare pacchetto di log 2](./media/storsimple-ova-web-ui-admin/image32.png)

    Se il pacchetto è stato creato correttamente, si riceve una notifica e la pagina viene aggiornata per indicare l'ora e la data di creazione del pacchetto.

    ![generare pacchetto di log 3](./media/storsimple-ova-web-ui-admin/image33.png)

3. Fare clic su **Scarica pacchetto di log**. Un pacchetto compresso viene scaricato nel sistema.

    ![generare pacchetto di log 4](./media/storsimple-ova-web-ui-admin/image34.png)

4. È possibile decomprimere il pacchetto di log scaricato e visualizzare i file di log del sistema.

## Arrestare e riavviare il dispositivo

È possibile arrestare o riavviare il dispositivo virtuale tramite l'interfaccia utente Web locale. Prima di riavviare, si consiglia di portare offline i volumi o le condivisioni sull'host e quindi sul dispositivo. Questa operazione consente di eliminare qualsiasi rischio di danneggiamento dei dati.

#### Per arrestare il dispositivo virtuale

1. Nell'interfaccia utente Web locale passare a **Manutenzione** > **Impostazioni di risparmio energia**.

2. Nella parte inferiore della pagina fare clic su **Arresto**.

    ![arresto del dispositivo 1](./media/storsimple-ova-web-ui-admin/image36.png)

3. Verrà visualizzato un avviso per segnalare che l'arresto del dispositivo interromperà ogni IO in corso, causando un periodo di inattività. Fare clic sull’icona del segno di spunta ![icona del segno di spunta](./media/storsimple-ova-web-ui-admin/image3.png).

    ![avviso di arresto del dispositivo](./media/storsimple-ova-web-ui-admin/image37.png)

    Si riceve una notifica in cui si comunica che l'arresto è stato avviato.

    ![arresto del dispositivo avviato](./media/storsimple-ova-web-ui-admin/image38.png)

    Il dispositivo viene ora arrestato. Se si desidera avviare il dispositivo, è necessario farlo tramite la console di gestione Hyper-V.

#### Per riavviare il dispositivo virtuale

1. Nell'interfaccia utente Web locale passare a **Manutenzione** > **Impostazioni di risparmio energia**.

2. Nella parte inferiore della pagina fare clic su **Riavvia**.

    ![riavvio del dispositivo](./media/storsimple-ova-web-ui-admin/image36.png)

3. Viene visualizzato un avviso in cui si informa che il riavvio del dispositivo interromperà ogni IO in corso, causando un tempo di inattività. Fare clic sull’icona del segno di spunta ![icona del segno di spunta](./media/storsimple-ova-web-ui-admin/image3.png).

    ![avviso di riavvio](./media/storsimple-ova-web-ui-admin/image37.png)

    Si riceve una notifica in cui si comunica che il riavvio è iniziato.

    ![riavvio iniziato](./media/storsimple-ova-web-ui-admin/image39.png)

    Durante il riavvio, si perde la connessione all'interfaccia utente. È possibile monitorare il riavvio aggiornando periodicamente l'interfaccia utente. In alternativa, è possibile monitorare lo stato di riavvio del dispositivo tramite la console di gestione Hyper-V.

## Passaggi successivi

Informazioni su come [Utilizzare il servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0413_2016-->