---
title: Configurare le impostazioni di utilizzo nel lab per le classi di Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare il numero di utenti per il lab, registrarli al lab, controllare il numero di ore in cui possono usare la macchina virtuale e altro ancora.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: spelluru
ms.openlocfilehash: 30c033b487fe58d017080b02c257502f82338164
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51710041"
---
# <a name="configure-usage-settings-and-policies"></a>Configurare le impostazioni e i criteri di utilizzo
Questo articolo descrive come configurare il numero di utenti per il lab, registrarli al lab, controllare il numero di ore in cui possono usare la macchina virtuale e altro ancora. 


## <a name="specify-the-number-of-users-allowed-into-the-lab"></a>Specificare il numero di utenti consentito nel lab

1. Selezionare **Usage policy** (Criteri di utilizzo). 
2. Nelle impostazioni di **Usage policy** (Criteri di utilizzo) immettere il **numero di utenti** a cui è consentito usare il lab.
3. Selezionare **Salva**. 

    ![Criteri di utilizzo](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="send-registration-link-to-students"></a>Inviare un collegamento di registrazione agli studenti

1. Passare alla visualizzazione **Utenti** selezionando **Utenti** nel menu a sinistra. 
2. Selezionare il riquadro **Get registration link** (Ottieni collegamento registrazione).

    ![Collegamento di registrazione dello studente](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. Nella finestra di dialogo **User registration** (Registrazione utente) selezionare il pulsante **Copia**. Il collegamento viene copiato negli Appunti. Incollarlo in un editor di posta elettronica e inviare un messaggio di posta elettronica allo studente. 

    ![Collegamento di registrazione dello studente](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Nella finestra di dialogo **User registration** (Registrazione utente) selezionare il pulsante **Chiudi**. 
4. Condividere il collegamento di registrazione con uno studente in modo che possa eseguire la registrazione alla classe. Se l'opzione **Limita l'accesso** è abilitata e si ha un elenco di utenti, eseguire le operazioni seguenti:
    1. Selezionare l'**indirizzo e-mail** dell'utente nell'elenco. 
    2. Sarà visualizzata una finestra del programma di posta elettronica predefinito con l'indirizzo **A** inserito. 
    3. Incollare l'**URL di registrazione** copiato in precedenza. 
    4. Inviare il **messaggio**. 

## <a name="view-users-registered-with-the-lab"></a>Visualizzare gli utenti che hanno eseguito la registrazione per il lab

Selezionare **Utenti** nel menu a sinistra per visualizzare l'elenco di utenti registrati al lab. 

![Elenco di utenti registrati al lab](../media/how-to-configure-student-usage/users-list.png)

## <a name="set-quotas-per-user"></a>Impostare le quote per utente

1. Selezionare **Utenti** nel menu a sinistra.
2. Selezionare **Quota per user: unlimited** (Quota per utente: illimitata) sulla barra degli strumenti. 
3. Nella pagina **Quota per user** (Quota per utente), selezionare **Limit the number of hours a user can use a VM** (Limitare il numero di ore in cui un utente può usare una macchina virtuale). 
4. Per **How many hours do you want to give to each user?** (Quante ore si desidera assegnare a ogni utente?), immettere il numero di ore e selezionare **Salva**. 

    ![Numero di ore per utente](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
5. Sulla barra degli strumenti viene visualizzato ora il numero di ore **Quota per user: (Quota per utente:) &lt;numero di ore&gt;**. 

    ![Quota per user (Quota per utente)](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="add-users-to-the-lab"></a>Aggiungere utenti al lab
Se è stata abilitata l'opzione **Limitare l'accesso**, aggiungere gli utenti (indirizzi di posta elettronica) all'elenco.

1. Selezionare **Utenti** nel menu a sinistra.
2. Selezionare **Aggiungi utenti** sulla barra degli strumenti. 
3. Nella pagina **Aggiungi utenti** immettere gli indirizzi di posta elettronica degli utenti in righe separate o in una singola riga, separati da punti e virgola. 

    ![Aggiungere gli indirizzi di posta elettronica degli utenti](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selezionare **Salva**. Gli indirizzi di posta elettronica degli utenti e i relativi stati (registrati o no) saranno visualizzati nell'elenco. 

    ![Elenco utenti](../media/how-to-configure-student-usage/users-list-new.png)

### <a name="add-users-by-uploading-a-csv-file"></a>Aggiungere gli utenti caricando un file CSV
È anche possibile aggiungere gli utenti caricando un file CSV con gli indirizzi di posta elettronica degli utenti.

1. Selezionare **Carica CSV** sulla barra degli strumenti.
2. Selezionare il file CSV con gli indirizzi di posta elettronica degli utenti. Quando si apre il file con Excel, tutti gli indirizzi di posta elettronica devono trovarsi in una colonna. 

## <a name="manage-user-vms"></a>Gestire le macchine virtuali utente
Dopo che gli studenti si sono registrati al servizio Azure Lab Services usando il collegamento di registrazione che gli viene fornito, è possibile visualizzare le VM assegnate agli studenti nella scheda **Macchine virtuali** . 

![Macchine virtuali assegnate agli studenti](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

È possibile eseguire le attività seguenti sulla VM di uno studente: 

- Arrestare una VM se è in esecuzione. 
- Avviare una VM se è stata arrestata. 
- Connettersi alla macchina virtuale. 
- Eliminare la macchina virtuale. 
- Visualizzare il numero di ore in cui gli utenti hanno usato la macchina virtuale. 


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire account lab come amministratore](how-to-manage-lab-accounts.md)
- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)
