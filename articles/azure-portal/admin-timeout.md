---
title: Impostare il timeout di inattività a livello di directory per gli utenti del portale di Azure. Documenti Microsoft
description: Gli amministratori possono imporre il tempo massimo di inattività prima della disschetta di una sessione. Il criterio di timeout di inattività viene impostato a livello di directory.
services: azure-portal
keywords: impostazioni, timeout
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: e27135d09da7060f2a948e37f6026fe66fbef5b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096602"
---
# <a name="set-directory-level-inactivity-timeout"></a>Impostare il timeout di inattività a livello di directorySet directory-level inactivity timeout

L'impostazione del timeout di inattività consente di proteggere le risorse da accessi non autorizzati se gli utenti dimenticano di proteggere la propria workstation. Quando un utente è rimasto inattivo per un certo periodo di tempo, la sessione del portale di Azure viene disconnessa automaticamente. Gli amministratori con il [ruolo di amministratore globale](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) possono imporre il tempo massimo di inattività prima della dissociazione di una sessione. L'impostazione del timeout di inattività si applica a livello di directory. Per ulteriori informazioni sulle directory, vedere [Panoramica](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)di Servizi di dominio Active Directory .

## <a name="configure-the-inactive-timeout-setting"></a>Configurare l'impostazione di timeout inattivo

Se si è un amministratore globale e si vuole applicare un'impostazione di timeout di inattività per tutti gli utenti del portale di Azure, eseguire la procedura seguente:If you're a Global Administrator, and you want to enforce an idle timeout setting for all users of the Azure portal, follow these steps:

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Selezionare **Impostazioni** nell'intestazione di pagina globale.
3. Selezionare il testo del collegamento **Configurare il timeout**a livello di directory .

    ![Schermata che mostra le impostazioni del portale con il testo del collegamento evidenziato](./media/admin-timeout/settings.png)

4. Viene aperta una nuova pagina. Nella pagina **Configura timeout di inattività** a livello di directory selezionare **Abilita timeout di inattività a livello** di directory per il portale di Azure per attivare l'impostazione.
5. Immettere quindi le **ore** e i **minuti** per il tempo massimo di inattività di un utente prima che la sessione venga disconnessa automaticamente.
6. Selezionare **Applica**.

    ![Screenshot della pagina che mostra la pagina per impostare il timeout di inattività a livello di directory](./media/admin-timeout/configure.png)

Per verificare che il criterio di timeout di inattività sia impostato correttamente, selezionare **Notifiche** nell'intestazione di pagina globale. Verificare che sia elencata una notifica di esito positivo.

  ![Schermata che mostra il messaggio di notifica riuscito per il timeout di inattività a livello di directory](./media/admin-timeout/confirmation.png)

L'impostazione ha effetto per le nuove sessioni. Non verrà applicato immediatamente agli utenti che hanno già effettuato l'accesso.

> [!NOTE]
> Se un amministratore globale ha configurato un'impostazione di timeout a livello di directory, gli utenti possono ignorare il criterio e impostare la propria durata di disconnessione inattiva. Tuttavia, l'utente deve scegliere un intervallo di tempo inferiore a quello impostato a livello di directory dall'amministratore globale.
>

## <a name="next-steps"></a>Passaggi successivi

* [Impostare le preferenze del portale di Azure](set-preferences.md)
* [Esportare o eliminare le impostazioni utente](azure-portal-export-delete-settings.md)
* [Attivare un contrasto elevato o cambiare tema](azure-portal-change-theme-high-contrast.md)
