---
title: 'Azure Active Directory Domain Services: Criteri password | Microsoft Docs'
description: Comprendere i criteri password nei domini gestiti
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: ergreenl
ms.openlocfilehash: 513cccb8b83eb4a69df1bc6172f1f02485215e35
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58086004"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Criteri password e di blocco dell'account nei domini gestiti
Questo articolo illustra i criteri password predefiniti in un dominio gestito, oltre al modo in cui è possibile configurarli.

## <a name="fine-grained-password-policies-fgpp"></a>Criteri granulari per le password (FGPP)
Usare i criteri granulari per le password per specificare molti criteri password in un singolo dominio. Ciò consente di applicare restrizioni diverse per i criteri di blocco degli account e password a set diversi di utenti in un dominio. Ad esempio, è possibile applicare impostazioni di password restrittive ad account con privilegi.

È possibile configurare le seguenti impostazioni di password usando i criteri granulari per le password:
* Lunghezza minima password
* Cronologia delle password
* Le password devono soddisfare i requisiti di complessità
* Validità minima password
* Validità massima password
* Criteri di blocco account
    * Durata del blocco account
    * Numero di tentativi di accesso non riusciti consentiti
    * Reimpostare il numero di tentativi di accesso non riusciti dopo


## <a name="default-fine-grained-password-policy-settings-on-a-managed-domain"></a>Impostazioni dei criteri granulari per le password predefiniti in un dominio gestito
Lo screenshot seguente illustra i criteri granulari per le password predefiniti, configurati in un dominio gestito di Azure AD Domain Services.

![Criteri granulari per le password predefiniti](./media/how-to/default-fgpp.png)

> [!NOTE]
> Non è possibile modificare o eliminare i criteri granulari per le password predefiniti. I membri del gruppo "AAD DC Administrators" possono creare criteri granulari per le password personalizzati e configurarli in modo da sostituire i criteri incorporati predefiniti (cioè, avere la precedenza).
>
>

## <a name="password-policy-settings"></a>Impostazioni criteri password
In un dominio gestito, per impostazione predefinita sono configurati i criteri password seguenti:
* Lunghezza minima password (caratteri): 7
* Validità massima della password (durata): 90 giorni
* Le password devono soddisfare i requisiti di complessità

### <a name="account-lockout-settings"></a>Impostazioni blocco account
In un dominio gestito, per impostazione predefinita sono configurati i criteri di blocco account seguenti:
* Durata del blocco account: 30
* Numero di tentativi di accesso non riusciti consentiti: 5
* Reimpostare il numero di tentativi di accesso non riusciti dopo: 30 minuti

Di fatto, gli account degli utenti vengono bloccati per 30 minuti se vengono inserite 5 password non valide in 2 minuti. Gli account vengono sbloccati automaticamente dopo 30 minuti.


## <a name="create-a-custom-fine-grained-password-policy-fgpp-on-a-managed-domain"></a>Creare criteri granulari per le password predefiniti personalizzati in un dominio gestito
È possibile creare criteri granulari per le password personalizzati e applicarli a gruppi specifici nel dominio gestito. Questa configurazione sostituisce efficacemente i criteri predefiniti configurati per il dominio gestito.

> [!TIP]
> Solo i membri del gruppo **"AAD DC Administrators"** hanno le autorizzazioni per creare criteri granulari per le password personalizzati.
>
>

È anche possibile creare criteri granulari per le password personalizzati e applicarli a qualsiasi unità organizzativa creata nel dominio gestito.

È possibile configurare un criterio granulare per le password personalizzato per i motivi seguenti:
* Per impostare criteri di blocco degli account differenti.
* Per configurare un'impostazione di durata password predefinita per il dominio gestito.

Per creare un criterio granulare per le password personalizzato in un dominio gestito:
1. Accedere alla macchina virtuale di Windows usata per amministrare il dominio gestito. Se non è disponibile, seguire le istruzioni per [amministrare un dominio gestito](active-directory-ds-admin-guide-administer-domain.md)
2. Avviare il **Centro di amministrazione di Active Directory** sulla macchina virtuale.
3. Fare clic sul nome di dominio, ad esempio "contoso100.com".
4. Fare doppio clic su **Sistema** per aprire il contenitore di sistema.
5. Fare doppio clic su **Contenitore Impostazioni password**.
6. Verranno visualizzati i criteri granulari per le password incorporati predefiniti per il dominio gestito denominato **AADDSSTFPSO**, che non è possibile modificare. È tuttavia possibile creare nuovi criteri personalizzati che sostituiscano quelli predefiniti.
7. Nel pannello **Attività** a destra, fare clic su **Nuovo** e quindi su **Impostazioni password**.
8. Nella finestra di dialogo **Create Password Settings** (Crea impostazioni password) specificare le impostazioni della password personalizzata da applicare come parte dei criteri granulari personalizzati. Ricordarsi di impostare in modo appropriato la precedenza per sostituire il criterio granulare per la password predefinito.

   ![Creare criteri granulari per le password predefiniti](./media/how-to/custom-fgpp.png)

   > [!TIP]
   > **Ricordarsi di deselezionare l'opzione Protect from accidental deletion (Proteggi da eliminazione accidentale).** Se questa opzione è selezionata, non è possibile salvare i criteri granulari per le password.
   >
   >

9. In **Directly Applies To** (Si applica direttamente a), fare clic sul pulsante **Add** (Aggiungi). Nella finestra di dialogo **Seleziona utenti o gruppi** fare clic sul pulsante **Località**.

   ![Selezionare Utenti e gruppi](./media/how-to/fgpp-applies-to.png)

10. Nella finestra di dialogo **Località** espandere il nome di dominio e fare clic su **AADDC Users** (Utenti AADDC). È ora possibile selezionare un gruppo dall'unità organizzativa degli utenti predefinita, a cui applicare i criteri granulari.

    ![Selezionare l'unità organizzativa a cui appartiene tale gruppo](./media/how-to/fgpp-container.png)

11. Digitare il nome del gruppo, quindi scegliere il pulsante **Controlla nomi** per verificare che il gruppo esista.

    ![Selezionare il gruppo a cui applicare i criteri granulari per le password](./media/how-to/fgpp-apply-group.png)

12. Il nome del gruppo viene visualizzato nella sezione **Directly Applies To**. Fare clic sul pulsante **OK** per salvare le modifiche apportate.

    ![Criteri granulari per le password non applicati](./media/how-to/fgpp-applied.png)

> [!TIP]
> **Per applicare i criteri password personalizzati per gli account utente in un'unità organizzativa personalizzata:** i criteri granulari per le password possono essere applicati solo ai gruppi. Per configurare i criteri password personalizzati solo per gli utenti da un'unità organizzativa personalizzata, creare un gruppo che includa gli utenti in tale unità organizzativa.
>
>

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni sui criteri granulari per le password di Active Directory](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Configurare i criteri granulari per le password usando il centro di amministrazione di AD](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)
