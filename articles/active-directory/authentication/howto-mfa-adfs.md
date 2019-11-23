---
title: Secure resources with Azure MFA and ADFS - Azure Active Directory
description: Questa è la pagina su Multi-Factor Authentication di Azure in cui viene descritto come iniziare a utilizzare questa tipologia di autenticazione di Azure nel cloud.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20e9f0b9a993d2a8af9b9d91399fdd49f9a06eaa
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381839"
---
# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>Protezione delle risorse cloud con Azure Multi-Factor Authentication e AD FS

Se l'organizzazione è federata con Azure Active Directory, usare Azure Multi-Factor Authentication o Active Directory Federation Services (AD FS) per proteggere le risorse accessibili da Azure AD. Usare le procedure seguenti per proteggere le risorse di Azure Active Directory con Azure Multi-Factor Authentication o Active Directory Federation Services.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Proteggere le risorse Azure AD con ADFS

Per proteggere le risorse cloud, configurare una regola attestazioni in modo che Active Directory Federation Services generi l'attestazione multipleauthn quando un utente esegue correttamente la verifica in due passaggi. Questa attestazione viene passata ad Azure AD. Seguire questa procedura per eseguire i passaggi:

1. Aprire il componente di gestione di ADFS.
2. A sinistra selezionare **Attendibilità componente**.
3. Fare clic con il pulsante destro del mouse su **Piattaforma delle identità di Microsoft Office 365** e selezionare **Modifica regole attestazione**.

   ![ADFS Console - Relying Party Trusts](./media/howto-mfa-adfs/trustedip1.png)

4. In Regole di trasformazione rilascio fare clic su **Aggiungi regola**.

   ![Editing Issuance Transform Rules](./media/howto-mfa-adfs/trustedip2.png)

5. Nell'Aggiunta guidata regole attestazione di trasformazione selezionare **Applicare la funzione di pass-through o di filtro a un'attestazione in ingresso** dall'elenco a discesa e fare clic su **Avanti**.

   ![Aggiunta guidata regole attestazione di trasformazione](./media/howto-mfa-adfs/trustedip3.png)

6. Assegnare un nome alla regola. 
7. Selezionare **Riferimenti dei metodi di autenticazione** come Tipo di attestazione in ingresso.
8. Selezionare **Pass-through di tutti i valori attestazione**.
    ![Aggiunta guidata regole attestazione di trasformazione](./media/howto-mfa-adfs/configurewizard.png)
9. Fare clic su **Finish** (Fine). Chiudere la console di gestione di ADFS.

## <a name="trusted-ips-for-federated-users"></a>Indirizzi IP attendibili per utenti federati

Gli indirizzi IP attendibili consentono agli amministratori di ignorare la verifica in due passaggi per specifici indirizzi IP o utenti federati con richieste provenienti dalla propria rete Intranet. Le sezioni seguenti descrivono come configurare gli indirizzi IP attendibili di Azure Multi-Factor Authentication con utenti federati e ignorare la verifica in due passaggi quando una richiesta proviene da una Intranet di utenti federati. Questo avviene configurando ADFS in modo da applicare la funzione di pass-through o filtro a un modello di attestazione in ingresso con il tipo di attestazione All'interno della rete aziendale.

Questo esempio usa Office 365 per l'attendibilità del componente.

### <a name="configure-the-ad-fs-claims-rules"></a>Configurare le regole attestazioni di ADFS

Per prima cosa è necessario configurare le attestazioni ADFS. Creare due regole attestazioni, una per il tipo di attestazione All'interno della rete aziendale e un'altra per mantenere gli utenti connessi.

1. Aprire il componente di gestione di ADFS.
2. A sinistra selezionare **Attendibilità componente**.
3. Right-click on **Microsoft Office 365 Identity Platform** and select **Edit Claim Rules…** 
   ![ADFS Console - Edit Claim Rules](./media/howto-mfa-adfs/trustedip1.png)
4. On Issuance Transform Rules, click **Add Rule.** 
   ![Adding a Claim Rule](./media/howto-mfa-adfs/trustedip2.png)
5. Nell'Aggiunta guidata regole attestazione di trasformazione selezionare **Applicare la funzione di pass-through o di filtro a un'attestazione in ingresso** dall'elenco a discesa e fare clic su **Avanti**.
   ![Aggiunta guidata regole attestazione di trasformazione](./media/howto-mfa-adfs/trustedip3.png)
6. Nella casella Nome regola attestazione assegnare un nome alla regola. Ad esempio: InternoReteAziend.
7. Nell'elenco a discesa accanto a Tipo di attestazione in ingresso selezionare **All'interno della rete aziendale**.
   ![Adding Inside Corporate Network claim](./media/howto-mfa-adfs/trustedip4.png)
8. Fare clic su **Finish** (Fine).
9. In Regole di trasformazione rilascio fare clic su **Aggiungi regola**.
10. Nell'Aggiunta guidata regole attestazione di trasformazione selezionare **Inviare attestazioni mediante una regola personalizzata** dall'elenco a discesa e fare clic su **Avanti**.
11. Nella casella sotto a Nome regola attestazione: specificare di *mantenere gli utenti connessi*.
12. Nella casella Regola personalizzata immettere:

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Create custom claim to keep users signed in](./media/howto-mfa-adfs/trustedip5.png)
13. Fare clic su **Finish** (Fine).
14. Fare clic su **Apply**.
15. Fare clic su **OK**.
16. Chiudere Gestione ADFS.

### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>Configurare gli indirizzi IP attendibili di Azure Multi-Factor Authentication con utenti federati

Ora che le attestazioni sono configurate, è possibile procedere alla configurazione degli indirizzi IP attendibili.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Select **Azure Active Directory** > **Conditional Access** > **Named locations**.
3. From the **Conditional Access - Named locations** blade, select **Configure MFA trusted IPs**

   ![Azure AD Conditional Access named locations Configure MFA trusted IPs](./media/howto-mfa-adfs/trustedip6.png)

4. In **Indirizzi IP attendibili** nella pagina Impostazioni servizio selezionare **Ignora l'autenticazione a più fattori per le richieste provenienti da utenti federati nella Intranet**.  
5. Fare clic su **save**.

La procedura è terminata. A questo punto, gli utenti federati di Office 365 dovrebbero usare solo MFA quando una richiesta proviene dall'esterno della Intranet aziendale.
