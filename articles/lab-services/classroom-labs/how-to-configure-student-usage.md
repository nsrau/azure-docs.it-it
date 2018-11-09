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
ms.date: 10/01/2018
ms.author: spelluru
ms.openlocfilehash: a91d5826f52b2beb05f2d9a08f3646bd776b294e
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142767"
---
# <a name="configure-usage-settings-and-policies"></a>Configurare le impostazioni e i criteri di utilizzo
Questo articolo descrive come configurare il numero di utenti per il lab, registrarli al lab, controllare il numero di ore in cui possono usare la macchina virtuale e altro ancora. 


## <a name="specify-the-number-of-users-allowed-into-the-lab"></a>Specificare il numero di utenti consentito nel lab

1. Selezionare **Usage policy** (Criteri di utilizzo). 
2. Nelle impostazioni di **Usage policy** (Criteri di utilizzo) immettere il **numero di utenti** a cui è consentito usare il lab.
3. Selezionare **Salva**. 

    ![Criteri di utilizzo](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="send-registration-link-to-users"></a>Inviare un collegamento di registrazione agli utenti

1. Passare alla visualizzazione **Dashboard**. 
2. Selezionare il riquadro **User registration** (Registrazione utente).

    ![Collegamento di registrazione dello studente](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. Nella finestra di dialogo **User registration** (Registrazione utente) selezionare il pulsante **Copia**. Il collegamento viene copiato negli Appunti. Incollarlo in un editor di posta elettronica e inviare un messaggio di posta elettronica allo studente. Gli studenti possono usare il collegamento per passare alla pagina che li assiste nella registrazione al laboratorio. 

    ![Collegamento di registrazione dello studente](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Nella finestra di dialogo **User registration** (Registrazione utente) selezionare il pulsante **Chiudi**. 

## <a name="view-users-registered-with-the-lab"></a>Visualizzare gli utenti che hanno eseguito la registrazione per il lab

Selezionare **Utenti** nel menu a sinistra per visualizzare l'elenco di utenti registrati al lab. 

![Elenco di utenti registrati al lab](../media/how-to-configure-student-usage/users-list.png)

## <a name="set-quotas-per-user"></a>Impostare le quote per utente

1. Selezionare **Impostazioni utente** nel menu a sinistra.
2. Selezionare il riquadro **Quota per user** (Quota per utente). 
3. Selezionare **Limit the number of hours a user can use a VM** (Limitare il numero di ore in cui un utente può usare una macchina virtuale). 
4. Per **How many hours do you want to give to each user?** (Quante ore si desidera assegnare a ogni utente?), immettere il numero di ore e selezionare **Salva**. 

    ![Numero di ore per utente](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
5. Visualizzare il numero di ore nel riquadro **Quota per user** (Quota per utente) ora. 

    ![Quota per user (Quota per utente)](../media/how-to-configure-student-usage/quota-per-user.png)

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
Introduzione alla configurazione di un lab usando Azure Lab Services:

- [Configurare un lab per le classi](how-to-manage-classroom-labs.md)
- [Configurare un lab](../tutorial-create-custom-lab.md)
