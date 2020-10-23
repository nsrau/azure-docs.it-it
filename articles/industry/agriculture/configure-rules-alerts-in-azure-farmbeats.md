---
title: Configurare regole e gestire avvisi
description: Viene descritto come configurare le regole e gestire gli avvisi in FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 5e6a5d414c341f482c3fddf95a2f8bb8e55a3ca2
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168546"
---
# <a name="configure-rules-and-manage-alerts"></a>Configurare regole e gestire avvisi

Azure FarmBeats consente di creare regole basate sulla logica di business, oltre ai dati dei sensori che passano dai sensori e dai dispositivi distribuiti nella farm. Le regole attivano gli avvisi nel sistema ogni volta che i valori dei sensori superano un valore soglia. Visualizzando e analizzando gli avvisi creati dopo i valori di soglia, è possibile agire rapidamente su eventuali problemi e ottenere le soluzioni necessarie.

## <a name="create-rule"></a>Creare una regola

1. Nella home page passare a **Rules (regole**).
2. Selezionare **nuova regola**. Verrà visualizzata la finestra nuova regola.

    ![Schermata che evidenzia il pulsante nuova regola e la nuova sezione regola.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. Immettere il **nome della regola** e la **Descrizione della regola** , quindi selezionare una farm dal menu a discesa **Seleziona Farm** .
4. Digitare il nome della farm per selezionare la sezione Farm e **condizioni** visualizzata nella stessa finestra.  

    ![Screenshot che evidenzia la sezione delle condizioni.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. In **condizioni**, immettere i valori per **misura**, **operatore** e **valore**.
6. Digitare il nome della misura nel menu a discesa **misura** .
7. Selezionare **+ Aggiungi condizione** per aggiungere altre condizioni alla regola.
8. Selezionare il **livello di gravità**.
9. In **azione**, attivare l'interruttore **abilitata** per la posta elettronica per abilitare gli avvisi di posta elettronica.

    ![Screenshot che mostra l'opzione abilitata per la posta elettronica.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. Immettere gli **indirizzi** di posta elettronica a cui si desidera inviare l'avviso di posta elettronica, insieme all' **oggetto del messaggio** di posta elettronica e alle **Note aggiuntive**.  
11. Per abilitare o disabilitare la regola, nello **stato della regola**attivare l'interruttore **attivato** .
    È possibile visualizzare il numero di dispositivi che saranno interessati dalla regola.
12. Selezionare **applica** per creare la regola.

## <a name="view-rule"></a>Visualizza regola

Nella pagina della **Farm** viene visualizzato l'elenco delle regole disponibili. Selezionare un **nome di regola**. In una finestra vengono visualizzati i dettagli seguenti applicabili per la regola selezionata:
 - Nome regola
 - Collegamento alla farm a cui è associata la regola
 - Data creazione
 - Data ultimo aggiornamento
 - Livello di gravità
 - Stato regola
 - Elenco di condizioni  
 - Numero di dispositivi interessati dalla regola

    ![Screenshot che mostra la schermata dei dettagli della regola.](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>Modificare una regola

Per modificare una regola, attenersi alla seguente procedura:

1. Nella home page selezionare **regole** dal menu di spostamento a sinistra.
   Viene visualizzata la finestra regole.
2. Selezionare la regola per la quale si desidera modificare.

    ![Screenshot che mostra la regola selezionata.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. Selezionare **modifica** dalla barra delle azioni. verrà visualizzata la finestra **Modifica regola** .

    ![Screenshot che mostra la schermata Modifica regola.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. Modificare il **nome della regola**e la **Descrizione della regola** , quindi selezionare una farm dal menu a discesa **Seleziona Farm** .
5. Digitare il nome della farm per selezionare la farm e le **condizioni** vengono visualizzate nella stessa finestra.  
6. In **condizioni**modificare **misura**, **operatore** e **valore**.
7. Digitare il nome della misura nel menu a discesa **misura** .
8. Selezionare **+ Aggiungi condizione** per aggiungere/modificare le condizioni alle regole.

    ![Schermata che evidenzia il pulsante Aggiungi condizione.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  Selezionare il **livello di gravità**.  
10. In **azione**, attivare l'interruttore **abilitata** per la posta elettronica per abilitare gli avvisi di posta elettronica.
11. Modificare gli **indirizzi di posta** elettronica a cui si vuole inviare l'avviso di posta elettronica, insieme all' **oggetto del messaggio** di posta elettronica e alle **Note aggiuntive**.  
12. Per abilitare o disabilitare la regola, nello **stato della regola**attivare l'interruttore **attivato** .
È possibile visualizzare il numero di dispositivi che saranno interessati da questa regola.
13. Selezionare **applica** per modificare la regola.

## <a name="change-rule-status"></a>Modificare lo stato della regola

Per modificare lo stato di una regola, attenersi alla seguente procedura:

1. Nella home page selezionare **regole** dal menu di spostamento a sinistra. Viene visualizzata la finestra regole.
2. Selezionare la regola per cui si desidera modificare lo stato.

    ![Screenshot che mostra il pulsante Cambia stato.](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. Selezionare **modifica stato** dalla barra delle azioni. Viene visualizzata la finestra **modifica stato** .

    ![Screenshot che mostra la schermata di modifica dello stato.](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. Modificare lo stato della regola utilizzando il pulsante **Cambia stato** .
   È possibile visualizzare il numero di dispositivi che saranno interessati dalla regola.
4. Selezionare **applica** per modificare lo stato della regola.

## <a name="delete-rule"></a>Eliminare una regola

Per eliminare una regola, attenersi alla seguente procedura:

1. Nella home page selezionare **regole** dal menu di spostamento a sinistra. Viene visualizzata la finestra regole.
2. Selezionare la regola per la quale si desidera eliminare.

    ![Schermata che evidenzia il pulsante Elimina.](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. Selezionare **Elimina** dalla barra delle azioni.

    ![Progetto FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. Verrà visualizzata la finestra di dialogo **Elimina regola** . Selezionare **Elimina**.
