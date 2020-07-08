---
title: StorSimple Snapshot Manager azioni del menu MMC | Microsoft Docs
description: Viene descritto come utilizzare lo standard delle azioni di menu di Microsoft Management Console (MMC) in Gestione snapshot StorSimple.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: c177adaa6fe0808d6719a8e87669d7f2fa4fb9cf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85508827"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Utilizzo delle azioni del menu MMC in Gestione snapshot StorSimple

## <a name="overview"></a>Panoramica
In Gestione snapshot StorSimple, le azioni seguenti verranno visualizzate elencate in tutti i menu delle azioni e in tutte le variazioni del riquadro **Azioni** .

* Visualizza
* Nuova finestra da qui 
* Aggiorna 
* Esporta elenco 
* Help 

Queste azioni fanno parte di Microsoft Management Console (MMC) e non sono specifiche di Gestione snapshot StorSimple. In questa esercitazione vengono descritte tali azioni e viene illustrato come utilizzare ciascuna di esse in Gestione snapshot StorSimple.

## <a name="view"></a>Visualizza
È possibile usare l'opzione **Visualizza** per modificare la visualizzazione del riquadro **Risultati** e quella della finestra della console. 

#### <a name="to-change-the-results-pane-view"></a>Per modificare la visualizzazione del riquadro Risultati
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** fare clic con il pulsante destro del mouse su un nodo oppure espandere il nodo e fare clic con il pulsante destro del mouse su un elemento nel riquadro **Risultati**, quindi scegliere **Visualizza**. 
3. Per aggiungere o rimuovere colonne visualizzate nel riquadro **Risultati**, fare clic su **Aggiungi/rimuovi colonne**. Viene visualizzata la finestra di dialogo **Aggiungi/rimuovi colonne**.
   
    ![Aggiunta o rimozione delle colonne dal riquadro Risultati](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Compilare il modulo come segue:
   
   * Selezionare gli elementi dall'elenco delle colonne **Disponibili** e fare clic su **Aggiungi** per aggiungerle all'elenco **Colonne visualizzate**. 
   * Fare clic su elementi nell'elenco **Colonne visualizzate**, quindi fare clic su **Rimuovi** per rimuoverle dall'elenco. 
   * Selezionare un elemento nell'elenco delle colonne **Visualizzate** e fare clic su **Sposta su** o **Sposta giù** per spostare l'elemento verso l'alto o verso il basso nell'elenco. 
   * Fare clic su **Ripristina impostazioni predefinite** per tornare alla configurazione predefinita del riquadro **Risultati**. 
5. Al termine delle selezioni, fare clic su **OK**. 

#### <a name="to-change-the-console-window-view"></a>Per modificare la visualizzazione della finestra della console
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** fare clic con il pulsante destro del mouse su **Visualizza**, quindi scegliere **Personalizza**. Viene visualizzata la finestra di dialogo **Personalizza**.
   
    ![Personalizzazione della finestra della console](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Selezionare o deselezionare le caselle di controllo per mostrare o nascondere gli elementi nella finestra della console. Al termine delle selezioni, fare clic su **OK**.

## <a name="new-window-from-here"></a>Nuova finestra da qui
È possibile usare l'opzione **Nuova finestra in questa posizione** per aprire una nuova finestra della console.

#### <a name="to-open-a-new-console-window"></a>Per aprire una nuova finestra della console
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** fare clic con il pulsante destro del mouse su un nodo, quindi scegliere **Nuova finestra in questa posizione**. 
   
    Viene visualizzata una nuova finestra, che mostra solo l'ambito selezionato. Se ad esempio si fa clic con il pulsante destro del mouse sul nodo **Criteri di backup**, nella nuova finestra vengono mostrati solo il nodo **Criteri di backup** nel riquadro **Ambito** e un elenco di criteri di backup definiti nel riquadro **Risultati**. Vedere l'esempio seguente.
   
    ![Nuova finestra da qui](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Aggiorna
L'azione **Aggiorna** consente di aggiornare la finestra della console.

#### <a name="to-update-the-console-window"></a>Per aggiornare la finestra della console
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** fare clic con il pulsante destro del mouse su un nodo oppure espandere il nodo e fare clic con il pulsante destro del mouse su un elemento nel riquadro **Risultati**, quindi scegliere **Aggiorna**. 

## <a name="export-list"></a>Esporta elenco
È possibile usare l'azione **Esporta elenco** per salvare un elenco in un file con valori delimitati da virgole (CSV). Ad esempio, è possibile esportare l'elenco dei criteri di backup o il catalogo di backup. È quindi possibile importare il file CSV in un’applicazione foglio di calcolo per l'analisi.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Per salvare un elenco in un file con valori delimitati da virgole (CSV)
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager. 
2. Nel riquadro **Ambito** fare clic con il pulsante destro del mouse su un nodo oppure espandere il nodo e fare clic con il pulsante destro del mouse su un elemento nel riquadro **Risultati**, quindi scegliere **Esporta elenco**. 
3. Viene visualizzata la finestra di dialogo **Esporta elenco**. Compilare il modulo come segue: 
   
   1. Nella casella **Nome file** digitare un nome per file CSV oppure fare clic sulla freccia per selezionarne uno nell'elenco a discesa.
   2. Nella casella **Salva come** fare clic sulla freccia e selezionare un tipo di file nell'elenco a discesa.
   3. Per salvare solo elementi selezionati, selezionare le righe, quindi selezionare la casella di controllo **Salva solo righe selezionate**. Per salvare tutti gli elenchi esportati, deselezionare la casella di controllo **Salva solo le righe selezionate** .
   4. Fare clic su **Salva**.
      
      ![Esportazione dell’elenco come file con valori delimitati da virgole](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Help
È possibile utilizzare il menu **Guida** per visualizzare la guida disponibile online per Gestione snapshot StorSimple e MMC.

#### <a name="to-view-available-online-help"></a>Per visualizzare la guida disponibile online
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** fare clic con il pulsante destro del mouse su un nodo oppure espandere il nodo e fare clic con il pulsante destro del mouse su un elemento nel riquadro **Risultati**, quindi scegliere **?**. 

## <a name="next-steps"></a>Passaggi successivi
* Ulteriori informazioni sull' [interfaccia utente di Gestione snapshot StorSimple](storsimple-use-snapshot-manager.md).
* Ulteriori informazioni sull’ [utilizzo di StorSimple Snapshot Manager per amministrare la soluzione di StorSimple](storsimple-snapshot-manager-admin.md).

