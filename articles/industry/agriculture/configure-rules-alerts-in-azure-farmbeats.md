---
title: Configurare regole e gestire avvisi
description: Descrive come configurare le regole e gestire gli avvisi in FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 02373da114b6dc4663cd3ffc9a0780ac6a7a9e23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482985"
---
# <a name="configure-rules-and-manage-alerts"></a>Configurare regole e gestire avvisi

Azure FarmBeats consente di creare regole basate sulla logica di business, oltre ai dati dei sensori che scorrono dai sensori e dai dispositivi distribuiti nella farm. Le regole attivano avvisi nel sistema ogni volta che i valori del sensore superano un valore soglia. Visualizzando e analizzando gli avvisi creati dopo i valori di soglia, è possibile agire rapidamente su qualsiasi problema e ottenere le soluzioni necessarie.

## <a name="create-rule"></a>Creare una regola

1. Nella home page, vai a **Regole**.
2. Selezionare **Nuova regola**. Viene visualizzata la finestra Nuova regola.

    ![Progetto FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. Immettere il nome della **regola** e **la descrizione della regola,** quindi selezionare una farm dal menu a discesa **Seleziona farm.**
4. Digitare il nome della farm per selezionare la sezione della farm e la sezione **Condizioni** viene visualizzata nella stessa finestra.  

    ![Progetto FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. In **Condizioni**immettere i valori per **Misura**, **Operatore** e **Valore**.
6. Digitare il nome della misura nel menu a discesa **Misura.**
7. Per aggiungere altre condizioni alla regola, **selezionare Aggiungi condizione.**
8. Selezionare il **livello di gravità**.
9. In **Azione**, attivare il pulsante di **attivazione/disattivazione Email abilitato** per abilitare gli avvisi tramite posta elettronica.

    ![Progetto FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. Immettere **gli indirizzi e-mail** a cui si desidera inviare l'avviso e-mail, insieme all'oggetto **e-mail** e **alle note aggiuntive**.  
11. In **Stato regola**, attivare il pulsante di attivazione/disattivazione per abilitare o disabilitare la regola. **Enabled**
    È possibile visualizzare il numero di dispositivi che saranno interessati dalla regola.
12. Selezionare **Applica** per creare la regola.

## <a name="view-rule"></a>Regola di visualizzazione

Nella pagina **Farm** viene visualizzato l'elenco delle regole disponibili. Selezionare un **Nome regola**. In una finestra vengono visualizzati i seguenti dettagli applicabili per la regola selezionata:
 - Nome regola
 - Collegamento alla farm a cui è associata la regola
 - Data creazione
 - Data ultimo aggiornamento
 - Livello di gravità
 - Stato della regola
 - Elenco delle condizioni  
 - Numero di dispositivi interessati dalla regola

    ![Progetto FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>Modificare una regola

Per modificare una regola, attenersi alla seguente procedura:

1. Nella home page selezionare **Regole** nel menu di spostamento a sinistra.
   Viene visualizzata la finestra delle regole.
2. Selezionare la regola per la quale si desidera modificare.

    ![Progetto FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. Selezionare **Modifica** dalla barra delle azioni, viene visualizzata la finestra **Modifica regola.**

    ![Progetto FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. Modificare il nome della **regola**e **la descrizione della regola,** quindi selezionare una farm dal menu a discesa **Seleziona farm** .
5. Digitare il nome della farm per selezionare la farm e **Condizioni** viene visualizzato nella stessa finestra.  
6. In **Condizioni**modificare **Misura**, **Operatore** e **Valore**.
7. Digitare il nome della misura nel menu a discesa **Misura.**
8. Per aggiungere/modificare le condizioni alle regole, selezionare **Aggiungi condizione.**

    ![Progetto FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  Selezionare il **livello di gravità**.  
10. In **Azione**, attivare il pulsante di **attivazione/disattivazione Email abilitato** per abilitare gli avvisi tramite posta elettronica.
11. Modificare gli **indirizzi di posta elettronica** a cui si desidera inviare l'avviso e-mail, insieme all'oggetto **e-mail** e **alle note aggiuntive**.  
12. In **Stato regola**, attivare il pulsante di attivazione/disattivazione per abilitare o disabilitare la regola. **Enabled**
È possibile visualizzare il numero di dispositivi interessati da questa regola.
13. Selezionare **Applica** per modificare la regola.

## <a name="change-rule-status"></a>Modificare lo stato della regola

Per modificare lo stato di una regola, attenersi alla seguente procedura:

1. Nella home page selezionare **Regole** nel menu di spostamento a sinistra. Viene visualizzata la finestra delle regole.
2. Selezionare la regola per la quale si desidera modificare lo stato.

    ![Progetto FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. Selezionare **Cambia stato** dalla barra delle azioni. Viene visualizzata la finestra **Modifica stato.**

    ![Progetto FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. Modificare lo stato della regola utilizzando il pulsante di attivazione/disattivazione **dello stato.**
   È possibile visualizzare il numero di dispositivi che saranno interessati dalla regola.
4. Selezionare **Applica** per modificare lo stato della regola.

## <a name="delete-rule"></a>Eliminare una regola

Per eliminare una regola, attenersi alla seguente procedura:

1. Nella home page selezionare **Regole** nel menu di spostamento a sinistra. Viene visualizzata la finestra delle regole.
2. Selezionare la regola per la quale si desidera eliminare.

    ![Progetto FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. Selezionare **Elimina** dalla barra delle azioni.

    ![Progetto FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. Viene visualizzata la finestra di dialogo **Elimina regola.** Selezionare **Elimina**.
