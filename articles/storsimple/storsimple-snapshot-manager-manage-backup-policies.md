---
title: Criteri di backup di StorSimple Snapshot Manager | Microsoft Docs
description: Viene descritto come utilizzare lo snap-in MMC di Gestione snapshot StorSimple per creare e gestire i criteri di backup che consentono di controllare i backup pianificati.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 7b2df6c8384e78094d80a17d8982b0db9edf73e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75933368"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Utilizzo di Gestione snapshot StorSimple per creare e gestire i criteri di backup
## <a name="overview"></a>Panoramica
Un criterio di backup consente di creare una pianificazione di backup dei dati del volume in locale o nel cloud. Quando si crea un criterio di backup, è inoltre possibile specificare un criterio di conservazione. È possibile conservare un massimo di 64 snapshot. Per altre informazioni sui criteri di backup, vedere [tipi di backup](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) nella [serie StorSimple 8000: una soluzione cloud ibrida](storsimple-overview.md).

In questa esercitazione viene illustrato come:

* Creare un criterio di backup
* Modifica di un criterio di backup
* Eliminare un criterio di backup

## <a name="create-a-backup-policy"></a>Creare un criterio di backup
Utilizzare la procedura seguente per creare un nuovo criterio di backup.

#### <a name="to-create-a-backup-policy"></a>Per creare un criterio di backup
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** fare clic con il pulsante destro del mouse su **Criteri di backup**, quindi scegliere **Crea criteri di backup**.

    ![Creare un criterio di backup](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Viene visualizzata la finestra di dialogo **Crea criteri**.

    ![Creazione di un criterio - scheda Generale](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. Nella scheda **Generale** immettere le informazioni seguenti:

   1. Nella casella di testo **Nome** digitare un nome per i criteri.
   2. Nella casella di testo **Gruppo di volumi** digitare il nome del gruppo di volumi associato ai criteri.
   3. Selezionare **Snapshot locale** o **Snapshot nel cloud**.
   4. Selezionare il numero di snapshot da conservare. Se si seleziona **Tutti**, vengono conservati 64 snapshot (valore massimo consentito).
4. Fare clic sulla scheda **Pianificazione**.

    ![Creazione un criterio - scheda Pianificazione](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. Nella scheda **Pianificazione** immettere le informazioni seguenti:

   1. Selezionare la casella di controllo **Abilita** per pianificare il backup successivo.
   2. In **Impostazioni** selezionare **Una volta**, **Giornaliero**, **Settimanale** o **Mensile**.
   3. Nella casella di testo **Inizio** fare clic sull'icona del calendario e selezionare una data di inizio.
   4. In **Impostazioni avanzate** è possibile selezionare pianificazioni ripetute facoltativamente e una data di fine.
   5. Fare clic su **OK**.

Dopo aver creato i criteri di backup, nel riquadro **Risultati** vengono visualizzate le informazioni seguenti:

* **Nome**: nome dei criteri di backup.
* **Tipo**: snapshot locale o nel cloud.
* **Gruppo di volumi**: gruppo di volumi associato ai criteri.
* **Conservazione**: numero di snapshot da conservare. Il valore massimo è 64.
* **Data creazione**: data di creazione dei criteri specifici.
* **Abilitato**: se il criterio è attualmente attivo (**True** indica che è attivo, **False** indica che non è attivo).

## <a name="edit-a-backup-policy"></a>Modifica di un criterio di backup
Utilizzare la procedura seguente per modificare un criterio di backup esistente.

#### <a name="to-edit-a-backup-policy"></a>Per modificare un criterio di backup
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** fare clic sul nodo **Criteri di backup**. Tutti i criteri di backup vengono visualizzati nel riquadro **Risultati**.
3. Fare clic con il pulsante destro del mouse sui criteri da modificare, quindi scegliere **Modifica**.

    ![Modifica di un criterio di backup](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Quando viene visualizzata la finestra di dialogo **Crea criteri**, immettere le modifiche, quindi fare clic su **OK**.

## <a name="delete-a-backup-policy"></a>Eliminare un criterio di backup
Utilizzare la procedura seguente per eliminare un criterio di backup.

#### <a name="to-delete-a-backup-policy"></a>Per eliminare un criterio di backup
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** fare clic sul nodo **Criteri di backup**. Tutti i criteri di backup vengono visualizzati nel riquadro **Risultati**.
3. Fare clic con il pulsante destro del mouse sui criteri da eliminare, quindi scegliere **Elimina**.
4. Quando viene visualizzato il messaggio di conferma, fare clic su **Sì**.

    ![Eliminazione della conferma dei criteri di backup](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [usare StorSimple Snapshot Manager per amministrare la soluzione di StorSimple](storsimple-snapshot-manager-admin.md).
* Informazioni su come [utilizzare Gestione snapshot StorSimple per visualizzare e gestire i processi di backup](storsimple-snapshot-manager-manage-backup-jobs.md).
