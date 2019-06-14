---
title: Registrare i dispositivi personali nella rete dell'organizzazione - Azure Active Directory | Microsoft Docs
description: Informazioni su come registrare il dispositivo personale nella rete dell'organizzazione in modo da poter accedere alle risorse protette dell'organizzazione.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: lizross
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 041c8bb6b4de2bbe2cbeb4c1a89e452239ae57bd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60473678"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Registrare il dispositivo personale nella rete dell'organizzazione
Registrare il dispositivo personale, in genere un telefono o tablet, nella rete dell'organizzazione. Dopo aver registrato il dispositivo, sarà possibile accedere alle risorse limitate della propria organizzazione.

>[!Note]
>Questo articolo usa un dispositivo Windows a scopo dimostrativo, ma è anche possibile registrare i dispositivi che eseguono iOS, Android o macOS.

## <a name="what-happens-when-you-register-your-device"></a>Cosa accade quando si registra il dispositivo
Mentre si registra il dispositivo nella rete dell'organizzazione, si verificheranno le azioni seguenti:

- Windows registra il dispositivo nella rete dell'organizzazione.

- Facoltativamente, in base alle scelte dell'organizzazione, potrebbe essere richiesto di configurare la verifica in due passaggi tramite [Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md) oppure [info di sicurezza](user-help-security-info-overview.md).

- Facoltativamente, in base alle scelte dell'organizzazione, il dispositivo potrebbe essere registrato automaticamente in una soluzione di gestione di dispositivi mobili, ad esempio Microsoft Intune. Per altre informazioni sulla registrazione in Microsoft Intune, vedere [Registrare il dispositivo in Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Si dovrà eseguire il processo di accesso, usando il nome utente e la password per l'account aziendale o dell'istituto di istruzione.

## <a name="to-register-your-windows-device"></a>Per registrare il dispositivo Windows

Seguire questa procedura per registrare il dispositivo personale in rete.

1. Aprire **Impostazioni** e quindi selezionare **Account**.

    ![Account nella schermata Impostazioni](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Selezionare **Accedi all'azienda o all'istituto di istruzione** e quindi selezionare **Connetti** nella schermata **Accedi all'azienda o all'istituto di istruzione**.

    ![Schermata Accedi all'azienda o all'istituto di istruzione con l'opzione Connetti evidenziata](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. Nella schermata **Aggiungi un account aziendale o dell'istituto di istruzione** digitare l'indirizzo di posta elettronica per l'account aziendale o dell'istituto di istruzione e quindi selezionare **Avanti**. Ad esempio: alain@contoso.com.

4. Accedere all'account aziendale o dell'istituto di istruzione e quindi selezionare **Accedi**.

5. Completare il resto del processo di registrazione, inclusi l'approvazione della richiesta di verifica dell'identità (se si usa la verifica in due passaggi) e la configurazione di Windows Hello (se necessario).

## <a name="to-verify-that-youre-registered"></a>Per verificare di essere registrati
È possibile assicurarsi di aver completato la registrazione controllando le impostazioni.

1. Aprire **Impostazioni** e quindi selezionare **Account**.

    ![Account nella schermata Impostazioni](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Selezionare **Accedi all'azienda o all'istituto di istruzione** e verificare che venga visualizzato l'account aziendale o dell'istituto di istruzione.

    ![Schermata Accedi all'azienda o all'istituto di istruzione con l'account contoso connesso](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>Passaggi successivi
Dopo aver registrato il dispositivo personale nella rete dell'organizzazione, dovrebbe essere possibile accedere alla maggior parte delle risorse.

- Se l'organizzazione vuole che il dispositivo aziendale venga aggiunto, vedere [Aggiungere il dispositivo aziendale alla rete dell'organizzazione](user-help-join-device-on-network.md).



