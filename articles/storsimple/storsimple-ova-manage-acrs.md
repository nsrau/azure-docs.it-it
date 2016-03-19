<properties 
   pageTitle="Gestire i record di controllo di accesso per l'array virtuale StorSimple | Microsoft Azure"
   description="In questo articolo viene descritto come gestire i record di controllo di accesso (ACR) che consentono di specificare quali host possono connettersi a un volume nell'array virtuale StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="v-sharos" />

# Usare il servizio StorSimple Manager per gestire i record di controllo di accesso per l'array virtuale StorSimple (anteprima)

## Panoramica

I record di controllo di accesso (ACR) consentono di specificare quali host possono connettersi a un volume nell'array virtuale StorSimple (noto anche come dispositivo virtuale locale StorSimple). I record di controllo di accesso vengono impostati su un volume specifico e contengono i nomi completi iSCSI (IQN) degli host. Quando un host prova a connettersi a un volume, il dispositivo verifica il record di controllo di accesso associato a tale volume per il nome qualificato iSCSI e, se esiste una corrispondenza, viene stabilita la connessione. Nella sezione relativa ai **record di controllo di accesso** della pagina **Configura** vengono visualizzati tutti i record di controllo di accesso insieme ai corrispondenti nomi qualificati iSCSI degli host.

In questa esercitazione vengono illustrate le seguenti attività comuni correlate ai record di controllo di accesso:

- Ottenere il nome qualificato iSCSI
- Aggiungere un record di controllo di accesso 
- Modificare un record di controllo di accesso 
- Eliminare un record di controllo di accesso 

> [AZURE.IMPORTANT] 
> 
> - Quando si assegna un record di controllo di accesso a un volume, fare attenzione che nel volume non abbiano effettuato l'accesso più di un host non cluster perché ciò potrebbe danneggiare il volume. 
> - Quando si elimina un record di controllo di accesso da un volume, assicurarsi che l'host corrispondente non acceda al volume perché l'eliminazione potrebbe comportare un'interruzione di lettura/scrittura.

## Ottenere il nome qualificato iSCSI

Eseguire i passaggi seguenti per ottenere il nome qualificato iSCSI di un host di Windows che esegue Windows Server 2012.

[AZURE.INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## Aggiungere un record di controllo di accesso

Si utilizza la pagina **Configurazione** del servizio StorSimple Manager per aggiungere record di controllo di accesso. In genere, un record di controllo di accesso verrà associato a un volume.

Per informazioni su come associare un record di controllo di accesso a un volume, vedere l'articolo relativo all'[uso del servizio StorSimple Manager per gestire i volumi su un array virtuale StorSimple](storsimple-ova-manage-volumes).

>[AZURE.IMPORTANT] 
> 
>Quando si assegna un record di controllo di accesso a un volume, fare attenzione che nel volume non abbiano effettuato l'accesso più di un host non cluster perché ciò potrebbe danneggiare il volume.
 
Attenersi alla seguente procedura per aggiungere un record di controllo di accesso.

#### Per aggiungere un record di controllo di accesso

1. Nella pagina di destinazione del servizio selezionare il servizio, fare doppio clic sul nome del servizio e quindi fare clic sulla scheda **Configurazione**.

    ![scheda Configurazione](./media/storsimple-ova-manage-acrs/acr1.png)

2. Nell'elenco tabulare in **Record di controllo di accesso**, fornire un **nome** per il record di controllo di accesso.

3. In **Nome iniziatore iSCSI** fornire il nome qualificato iSCSI dell'host di Windows.

4. Fare clic su **Salva** nella parte inferiore della pagina per salvare il record di controllo di accesso appena creato. Viene visualizzato il messaggio di conferma seguente:

    ![messaggio di conferma](./media/storsimple-ova-manage-acrs/acr2.png)

5. Fare clic sull’icona del segno di spunta ![icona del segno di spunta](./media/storsimple-ova-manage-acrs/check-icon.png). L'elenco tabulare verrà aggiornato per riflettere questa aggiunta.

## Modificare un record di controllo di accesso

Per modificare record di controllo di accesso, usare la pagina **Configurazione** nel portale di Azure classico.

> [AZURE.NOTE] È possibile modificare solo i record di controllo di accesso che non sono attualmente in uso. Per modificare un record di controllo di accesso associato a un volume attualmente in uso, è innanzitutto necessario rendere il volume offline.

Seguire questa procedura per modificare un record di controllo di accesso.

#### Per modificare un record di controllo di accesso

1. Nella pagina di destinazione del servizio selezionare il servizio, fare doppio clic sul nome del servizio e quindi fare clic sulla scheda **Configurazione**.

2. Nell'elenco tabulare dei record di controllo di accesso, passare il mouse sul record di controllo di accesso che si desidera modificare.

3. Fornire un nuovo nome e/o l'IQN del record di controllo di accesso.

4. Fare clic su **Salva** nella parte inferiore della pagina per salvare il record di controllo di accesso modificato. Verrà visualizzato un messaggio di conferma.

5. Fare clic sull’icona del segno di spunta ![icona del segno di spunta](./media/storsimple-ova-manage-acrs/check-icon.png). L'elenco tabulare verrà aggiornato per riflettere questa modifica.

## Eliminare un record di controllo di accesso

Per eliminare record di controllo di accesso, usare la pagina **Configurazione** nel portale di Azure classico.

> [AZURE.NOTE] 
> 
> - È possibile eliminare solo i record di controllo di accesso che non sono attualmente in uso. Per eliminare un record di controllo di accesso associato a un volume attualmente in uso, è innanzitutto necessario rendere il volume offline.
> - Quando si elimina un record di controllo di accesso da un volume, assicurarsi che l'host corrispondente non acceda al volume perché l'eliminazione potrebbe comportare un'interruzione di lettura/scrittura.

Attenersi alla procedura seguente per eliminare un record di controllo di accesso.

#### Per eliminare un record di controllo di accesso

1. Nella pagina di destinazione del servizio selezionare il servizio, fare doppio clic sul nome del servizio e quindi fare clic sulla scheda **Configurazione**.

2. Nell'elenco tabulare dei record di controllo di accesso, passare il mouse sul record di controllo di accesso che si desidera eliminare.

3. Nella colonna all'estrema destra del record di controllo di accesso selezionato, verrà visualizzata un'icona di eliminazione (**x**). Fare clic sull'icona **x** per eliminare il record di controllo di accesso. Viene visualizzato il messaggio di conferma seguente:

    ![messaggio di conferma](./media/storsimple-ova-manage-acrs/acr3.png)

5. Fare clic sull’icona del segno di spunta ![icona del segno di spunta](./media/storsimple-ova-manage-acrs/check-icon.png). L'elenco tabulare verrà aggiornato per riflettere l'eliminazione.

## Passaggi successivi

- Altre informazioni sull'[aggiunta di volumi e la configurazione di record di controllo di accesso](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).

<!---HONumber=AcomDC_0224_2016-->