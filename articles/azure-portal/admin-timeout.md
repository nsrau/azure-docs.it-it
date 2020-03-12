---
title: Impostare il timeout di inattività a livello di directory per gli utenti del portale di Azure | Microsoft Docs
description: Gli amministratori possono applicare il tempo massimo di inattività prima che una sessione venga disconnesso. Il criterio di timeout di inattività viene impostato a livello di directory.
services: azure-portal
keywords: impostazioni, timeout
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: e27135d09da7060f2a948e37f6026fe66fbef5b3
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096602"
---
# <a name="set-directory-level-inactivity-timeout"></a>Impostare il timeout di inattività a livello di directory

L'impostazione del timeout di inattività consente di proteggere le risorse da accessi non autorizzati se gli utenti dimenticano di proteggere la propria workstation. Quando un utente rimane inattivo per un periodo di tempo, la sessione di portale di Azure viene disconnesso automaticamente. Gli amministratori del [ruolo amministratore globale](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) possono applicare il tempo massimo di inattività prima che una sessione venga disconnesso. L'impostazione del timeout di inattività si applica a livello di directory. Per ulteriori informazioni sulle directory, vedere [Active Directory Domain Services Overview](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="configure-the-inactive-timeout-setting"></a>Configurare l'impostazione di timeout inattivo

Se si è un amministratore globale e si desidera applicare un'impostazione di timeout di inattività per tutti gli utenti del portale di Azure, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Impostazioni** dall'intestazione di pagina globale.
3. Selezionare il testo del collegamento **Configura timeout a livello di directory**.

    ![Screenshot che mostra le impostazioni del portale con il testo del collegamento evidenziato](./media/admin-timeout/settings.png)

4. Viene aperta una nuova pagina. Nella pagina **Configura timeout inattività a livello di directory** selezionare Abilita timeout di inattività a **livello di directory per il portale di Azure** per attivare l'impostazione.
5. Immettere quindi le **ore** e i **minuti** per l'intervallo di tempo massimo durante il quale un utente può rimanere inattivo prima che la sessione venga disconnesso automaticamente.
6. Selezionare **Applica**.

    ![Screenshot che mostra la pagina per impostare il timeout di inattività a livello di directory](./media/admin-timeout/configure.png)

Per verificare che i criteri di timeout di inattività siano impostati correttamente, selezionare **notifiche** dall'intestazione di pagina globale. Verificare che sia elencata una notifica di esito positivo.

  ![Screenshot che mostra il messaggio di notifica riuscito per il timeout di inattività a livello di directory](./media/admin-timeout/confirmation.png)

L'impostazione ha effetto per le nuove sessioni. Non verrà applicato immediatamente a tutti gli utenti che hanno già eseguito l'accesso.

> [!NOTE]
> Se un amministratore globale ha configurato un'impostazione di timeout a livello di directory, gli utenti possono eseguire l'override dei criteri e impostare la durata di disconnessione inattiva. Tuttavia, l'utente deve scegliere un intervallo di tempo inferiore a quello impostato a livello di directory dall'amministratore globale.
>

## <a name="next-steps"></a>Passaggi successivi

* [Imposta le preferenze di portale di Azure](set-preferences.md)
* [Esportare o eliminare le impostazioni utente](azure-portal-export-delete-settings.md)
* [Attivare un contrasto elevato o cambiare tema](azure-portal-change-theme-high-contrast.md)
