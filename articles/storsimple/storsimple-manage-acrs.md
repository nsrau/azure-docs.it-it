<properties 
   pageTitle="Gestire i record di controllo di accesso in StorSimple | Microsoft Azure"
   description="In questo articolo vengono descritti i record di controllo di accesso (ACR) che consentono di specificare quali host possono connettersi a un volume nel dispositivo StorSimple."
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
   ms.date="05/24/2016"
   ms.author="alkohli" />

# Utilizzare il servizio StorSimple Manager per gestire li record di controllo di accesso

## Panoramica

I record di controllo di accesso (ACR) consentono di specificare quali host possono connettersi a un volume nel dispositivo StorSimple. I record di controllo di accesso vengono impostati su un volume specifico e contengono i nomi completi iSCSI (IQN) degli host. Quando un host prova a connettersi a un volume, il dispositivo controlla il record di controllo di accesso associato a tale volume per l'IQN e, se esiste una corrispondenza, viene stabilita la connessione. Nella sezione dei record di controllo di accesso nella pagina **Configura** vengono visualizzati tutti i record di controllo di accesso insieme agli IQN degli host.

In questa esercitazione vengono illustrate le seguenti attività comuni correlate ai record di controllo di accesso:

- Aggiungere un record di controllo di accesso 
- Modificare un record di controllo di accesso 
- Eliminare un record di controllo di accesso 

> [AZURE.IMPORTANT] 
> 
> - Quando si assegna un record di controllo di accesso a un volume, fare attenzione che nel volume non abbiano effettuato l'accesso più di un host non cluster perché ciò potrebbe danneggiare il volume. 
> - Quando si elimina un record di controllo di accesso da un volume, assicurarsi che l'host corrispondente non acceda al volume perché l'eliminazione potrebbe comportare un'interruzione di lettura/scrittura.

## Aggiungere un record di controllo di accesso

Si utilizza la pagina **Configura** del servizio StorSimple Manager per aggiungere record di controllo di accesso. In genere, un record di controllo di accesso verrà associato a un volume.

Attenersi alla seguente procedura per aggiungere un record di controllo di accesso.

#### Per aggiungere un record di controllo di accesso

1. Nella pagina di destinazione del servizio, selezionare il servizio, fare doppio clic sul nome del servizio, quindi fare clic sulla scheda **Configura**.

2. Nell'elenco tabulare in **Record di controllo di accesso**, fornire un **nome** per il record di controllo di accesso.

3. In **Nome iniziatore iSCSI**, fornire l'IQN dell'host di Windows. Per ottenere l'IQN dell'host di Windows Server, eseguire le operazioni seguenti:

   - Avviare l'iniziatore iSCSI di Microsoft sull’host di Windows.
   - Nella finestra **Proprietà iniziatore iSCSI**, nella scheda **Configurazione**, selezionare e copiare la stringa dal campo **Nome iniziatore**.
   - Incollare la stringa nel campo **Nome iniziatore iSCSI** nella tabella dei record di controllo di accesso nel portale di Azure classico.

4. Fare clic su **Salva** per salvare il record di controllo di accesso appena creato. L'elenco tabulare verrà aggiornato per riflettere questa aggiunta.

## Modificare un record di controllo di accesso

Per modificare record di controllo di accesso, utilizzare la pagina **Configura** nel portale di Azure classico.

> [AZURE.NOTE] È possibile modificare solo i record di controllo di acceso che non sono attualmente in uso. Per modificare un record di controllo di accesso associato a un volume attualmente in uso, è innanzitutto necessario rendere il volume offline.

Seguire questa procedura per modificare un record di controllo di accesso.

#### Per modificare un record di controllo di accesso

1. Nella pagina di destinazione del servizio, selezionare il servizio, fare doppio clic sul nome del servizio, quindi fare clic sulla scheda **Configura**.

2. Nell'elenco tabulare dei record di controllo di accesso, passare il mouse sul record di controllo di accesso che si desidera modificare.

3. Fornire un nuovo nome e/o l'IQN del record di controllo di accesso.

4. Fare clic su **Salva** per salvare il record di controllo di accesso modificato. L'elenco tabulare verrà aggiornato per riflettere questa modifica.

## Eliminare un record di controllo di accesso

Per eliminare record di controllo di accesso, utilizzare la pagina **Configura** nel portale di Azure classico.

> [AZURE.NOTE] È possibile eliminare solo i record di controllo di acceso che non sono attualmente in uso. Per eliminare un record di controllo di accesso associato a un volume attualmente in uso, è innanzitutto necessario rendere il volume offline.

Attenersi alla procedura seguente per eliminare un record di controllo di accesso.

#### Per eliminare un record di controllo di accesso

1. Nella pagina di destinazione del servizio, selezionare il servizio, fare doppio clic sul nome del servizio, quindi fare clic sulla scheda **Configura**.

2. Nell'elenco tabulare dei record di controllo di accesso, passare il mouse sul record di controllo di accesso che si desidera eliminare.

3. Nella colonna all'estrema destra del record di controllo di accesso selezionato, verrà visualizzata un'icona di eliminazione (**x**). Fare clic sull'icona **x** per eliminare il record di controllo di accesso.

4. Quando viene richiesta la conferma, fare clic su **SÌ** per continuare con l'eliminazione. L'elenco tabulare verrà aggiornato per riflettere l'eliminazione.

## Passaggi successivi

- Ulteriori informazioni sulla [gestione di volumi StorSimple](storsimple-manage-volumes.md).

- Ulteriori informazioni sull’[utilizzo del servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
 

<!---HONumber=AcomDC_0525_2016-->