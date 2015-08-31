<properties 
   pageTitle="Utilizzo delle azioni del menu MMC in Gestione snapshot StorSimple | Microsoft Azure"
   description="Viene descritto come utilizzare le azioni del menu di Microsoft Management Console (MMC) in Gestione snapshot StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2015"
   ms.author="v-sharos" />

# Utilizzo delle azioni del menu MMC in Gestione snapshot StorSimple

## Panoramica

In Gestione snapshot StorSimple, le azioni seguenti verranno visualizzate elencate in tutti i menu delle azioni e in tutte le variazioni del riquadro **Azioni**.

- Visualizza
- Nuova finestra da qui 
- Aggiorna 
- Esporta elenco 
- Guida 

Queste azioni fanno parte di Microsoft Management Console (MMC) e non sono specifiche di Gestione snapshot StorSimple.

In questa esercitazione vengono descritte tali azioni e viene illustrato come utilizzare ciascuna di esse in Gestione snapshot StorSimple.

## Visualizza

È possibile utilizzare l’opzione **Visualizza** per modificare la visualizzazione del riquadro **Risultati** e quella della finestra della console.

#### Per modificare la visualizzazione del riquadro Risultati

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.

2. Nel riquadro **Ambito**, fare clic con il pulsante destro del mouse su qualsiasi nodo oppure espandere il nodo e fare clic con il pulsante destro del mouse su un elemento nel riquadro **Risultati**, quindi fare clic sull’opzione **Visualizza**.

3. Per aggiungere o rimuovere le colonne visualizzate nel riquadro **Risultati**, fare clic su **Aggiungi/Rimuovi colonne**. Viene visualizzata la finestra di dialogo **Aggiungi/Rimuovi colonne**.

    ![Aggiunta o rimozione delle colonne dal riquadro Risultati](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png)

4. Compilare il modulo come segue:

    - Selezionare gli elementi dall’elenco delle colonne **Disponibili** e fare clic su **Aggiungi** per aggiungerle all’elenco **Colonne visualizzate**. 

    - Fare clic sugli elementi nell’elenco **Colonne visualizzate**, quindi fare clic su **Rimuovi** per rimuoverle dall'elenco.

    - Selezionare un elemento nell’elenco delle colonne **Visualizzate** e fare clic su **Sposta su** o **Sposta giù** per spostare l'elemento verso l'alto o verso il basso nell'elenco.

    - Fare clic su **Ripristina impostazioni predefinite** per tornare alla configurazione predefinita del riquadro **Risultati**.

5. Al termine delle selezioni, fare clic su **OK**.

#### Per modificare la visualizzazione della finestra della console

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.

2. Nel riquadro **Ambito**, fare clic con il pulsante destro del mouse su qualsiasi nodo, fare clic su **Visualizza**, quindi fare clic su **Personalizza**. Viene visualizzata la finestra di dialogo **Personalizza**.

    ![Personalizzazione della finestra della console](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png)

3. Selezionare o deselezionare le caselle di controllo per mostrare o nascondere gli elementi nella finestra della console. Al termine delle selezioni, fare clic su **OK**.

## Nuova finestra da qui

È possibile utilizzare l’opzione **Nuova finestra da qui** per aprire una nuova finestra della console.

#### Per aprire una nuova finestra della console

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.

2. Nel riquadro **Ambito**, fare clic con il pulsante destro del mouse su qualsiasi nodo, quindi fare clic su **Nuova finestra da qui**.

    Viene visualizzata una nuova finestra, che mostra solo l'ambito selezionato. Ad esempio, se si fa clic con il pulsante destro del mouse sul nodo **Criteri di backup**, nella nuova finestra verrà visualizzato solo il nodo **Criteri di Backup** del riquadro **Ambito** e un elenco dei criteri di backup definiti nel riquadro **Risultati**. Vedere l'esempio seguente.

    ![Nuova finestra da qui](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png)
 
## Aggiorna

È possibile utilizzare l’azione **Aggiorna** per aggiornare la finestra della console.

#### Per aggiornare la finestra della console

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.

2. Nel riquadro **Ambito**, fare clic con il pulsante destro del mouse su qualsiasi nodo oppure espandere il nodo e fare clic con il pulsante destro del mouse su un elemento nel riquadro **Risultati**, quindi fare clic su **Aggiorna**.

## Esporta elenco

È possibile utilizzare l’azione **Esporta elenco** per salvare un elenco in un file con valori delimitati da virgole (CSV). Ad esempio, è possibile esportare l'elenco dei criteri di backup o il catalogo di backup. È quindi possibile importare il file CSV in un’applicazione foglio di calcolo per l'analisi.

#### Per salvare un elenco in un file con valori delimitati da virgole (CSV)

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager. 

2. Nel riquadro **Ambito**, fare clic con il pulsante destro del mouse su qualsiasi nodo oppure espandere il nodo e fare clic con il pulsante destro del mouse su un elemento nel riquadro **Risultati**, quindi fare clic su **Esporta elenco**.

3. Viene visualizzata la finestra di dialogo **Esporta elenco**. Compilare il modulo come segue:

    1. Nella casella **Nome file** digitare un nome per il file CSV o fare clic sulla freccia per effettuare una selezione dall'elenco a discesa.

    2. Nella casella **Salva come**, fare clic sulla freccia e selezionare un tipo di file dall'elenco a discesa.

    3. Per salvare solo gli elementi selezionati, selezionare le righe, quindi fare clic sulla casella di controllo **Salva solo le righe selezionate**. Per salvare tutti gli elenchi esportati, deselezionare la casella di controllo **Salva solo le righe selezionate**.

    4. Fare clic su **Save**.

    ![Esportazione dell’elenco come file con valori delimitati da virgole](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png)
 
## Guida

È possibile utilizzare il menu **Guida** per visualizzare la guida disponibile online per Gestione snapshot StorSimple e MMC.

#### Per visualizzare la guida disponibile online

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.

2. Nel riquadro **Ambito**, fare clic con il pulsante destro del mouse su qualsiasi nodo oppure espandere il nodo e fare clic con il pulsante destro del mouse su un elemento nel riquadro **Risultati**, quindi fare clic su **Guida**.

## Passaggi successivi

[Ulteriori informazioni sull'interfaccia utente di Gestione snapshot StorSimple](storsimple-use-snapshot-manager.md)

<!---HONumber=August15_HO8-->