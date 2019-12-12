---
title: Abilitazione di un gruppo pilota per la reimpostazione della password self-service di Azure AD
description: In questa esercitazione si abiliterà la reimpostazione della password self-service di Azure AD per un gruppo di utenti pilota
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 08/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecdde4ef12c6991fad53f2286ee462fec31606ae
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846284"
---
# <a name="tutorial-complete-an-azure-ad-self-service-password-reset-pilot-roll-out"></a>Esercitazione: Completare l'implementazione di un gruppo pilota per la reimpostazione della password self-service di Azure AD

In questa esercitazione verrà abilitata un'implementazione pilota di reimpostazione della password self-service di Azure AD nell'organizzazione e la soluzione verrà testata con un account non amministratore.

È importante che qualsiasi test di reimpostazione della password self-service venga eseguito con account non amministratore. Microsoft gestisce i criteri di reimpostazione della password per gli account amministratore e richiede l'uso di metodi di autenticazione più avanzati. Questi criteri non consentono l'uso di domande e risposte di sicurezza e richiedono l'uso di due metodi per la reimpostazione.

> [!div class="checklist"]
> * Abilitare la reimpostazione self-service delle password
> * Testare la reimpostazione della password self-service come utente

## <a name="prerequisites"></a>Prerequisiti

* Un account amministratore globale

## <a name="enable-self-service-password-reset"></a>Abilitare la reimpostazione self-service delle password

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore globale.
1. Passare ad **Azure Active Directory** e selezionare **Reimpostazione password**.
1. Iniziare con un gruppo pilota abilitando la reimpostazione della password self-service per un subset di utenti nell'organizzazione.
   * Nella pagina **Proprietà** scegliere **Selezionato** per l'opzione **Reimpostazione password self-service abilitata** e selezionare un gruppo pilota.
      * Solo i membri di un gruppo specifico di Azure AD scelti dall'amministratore possono usare la reimpostazione della password self-service. È consigliabile definire un gruppo di utenti e usare questa impostazione nella distribuzione di questa funzionalità per un modello di verifica. È supportato l'annidamento dei gruppi di sicurezza.
      * Verificare che gli utenti nel gruppo scelto abbiano le licenze appropriate.
   * Fare clic su **Save** (Salva).
1. Nella pagina **Metodi di autenticazione**
   * Impostare **Numero di metodi da reimpostare** su **1**
   * Scegliere i **Metodi disponibili per gli utenti** che l'organizzazione vuole consentire. Per questa esercitazione, selezionare le caselle per abilitare **Posta elettronica**, **Telefono cellulare**, **Telefono ufficio**, **Notifica dell'app per dispositivi mobili** e **Codice dell'app per dispositivi mobili**.
   * Fare clic su **Save** (Salva).
1. Nella pagina **Registrazione**
   * Selezionare **Sì** per **Richiedere agli utenti di registrarsi all'accesso**.
   * Impostare **Numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione** su **180**.
   * Fare clic su **Save** (Salva).
1. Nella pagina **Notifiche**
   * Impostare l'opzione **Notificare agli utenti le reimpostazioni delle password** su **Sì**.
   * Impostare **Notificare agli amministratori quando altri amministratori reimpostano le proprie password** su **Sì**.
1. Nella pagina **Personalizzazione**
   * Microsoft consiglia di impostare **Personalizza collegamento al supporto tecnico** su **Sì** e specificare un indirizzo di posta elettronica o l'URL di una pagina Web in cui gli utenti possono ottenere ulteriore assistenza dall'organizzazione nel campo **Indirizzo di posta elettronica o URL del supporto tecnico**.
   * Per questa esercitazione, l'opzione **Personalizza collegamento al supporto tecnico** rimarrà impostata su **No**.

La reimpostazione della password self-service è ora configurata per gli utenti del cloud nel gruppo pilota.

## <a name="test-sspr-as-a-user"></a>Testare la reimpostazione della password self-service come utente

Eseguire il test usando un utente di prova non amministratore, membro del gruppo pilota. **Se si usa un account a cui sono assegnati ruoli di amministratore, i metodi di autenticazione e il numero potrebbero essere diversi rispetto a quelli selezionati, perché è Microsoft che gestisce i criteri per gli amministratori.**

1. Aprire una nuova finestra del browser in modalità InPrivate o in incognito.
1. Eseguire la registrazione per la reimpostazione della password self-service per un utente di prova usando il portale di registrazione all'indirizzo [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
1. Usare lo stesso utente di prova per passare al portale di reimpostazione della password self-service [https://aka.ms/sspr](https://aka.ms/sspr) e provare a reimpostare la password usando le informazioni specificate nel passaggio precedente.
1. Dovrebbe essere possibile reimpostare correttamente la password.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si decide di non volere più usare le funzionalità configurate nell'ambito di questa esercitazione, apportare la modifica seguente.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare ad **Azure Active Directory** e selezionare **Reimpostazione password**.
1. Nella pagina **Proprietà** scegliere **Nessuno** per l'opzione **Reimpostazione password self-service abilitata**.
1. Fare clic su **Save** (Salva).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata abilitata la reimpostazione della password self-service di Azure AD. Continuare con l'esercitazione successiva per vedere come un'infrastruttura di Active Directory Domain Services in locale può essere integrata nell'esperienza di reimpostazione della password self-service.

> [!div class="nextstepaction"]
> [Enable SSPR on-premises writeback integration](tutorial-enable-writeback.md) (Abilitare l'integrazione di writeback in locale della reimpostazione della password self-service)
