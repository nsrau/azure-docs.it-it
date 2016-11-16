---
title: Proteggere le risorse cloud con Azure MFA e ADFS
description: "Questa è la pagina su Multi-Factor Authentication di Azure in cui viene descritto come iniziare a utilizzare questa tipologia di autenticazione di Azure nel cloud."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 0927fc67-8090-4fdd-913a-b3cfed3fbe77
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/14/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 20aefd2c94bca03653f11a91c5ade173cb6da9bf


---
# <a name="securing-cloud-resources-with-azure-multifactor-authentication-and-ad-fs"></a>Protezione delle risorse cloud con Azure Multi-Factor Authentication e AD FS
Se l'organizzazione è federata con Azure Active Directory, usare Azure Multi-Factor Authentication o Active Directory Federation Services per proteggere le risorse accessibili da Azure AD. Usare le procedure seguenti per proteggere le risorse di Azure Active Directory con Azure Multi-Factor Authentication o Active Directory Federation Services.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Proteggere le risorse Azure AD con ADFS
Per proteggere le risorse cloud, prima è necessario abilitare un account per gli utenti e quindi impostare una regola attestazioni. Seguire questa procedura per eseguire i passaggi:

1. Usare la procedura descritta in [Attivare l'autenticazione a più fattori per gli utenti](multi-factor-authentication-get-started-cloud.md#turn-on-multi-factor-authentication-for-users) per abilitare un account.
2. Avviare la console di gestione di ADFS.
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)
3. Passare ad **Attendibilità componente** e fare clic con il pulsante destro del mouse sull'attendibilità della relying party. Selezionare **Modifica regole attestazione...**
4. Fare clic su **Aggiungi regola...**
5. Nell'elenco a discesa selezionare **Inviare attestazioni mediante una regola personalizzata** e fare clic su **Avanti**.
6. Immettere un nome per la regola attestazione.
7. In Regola personalizzata: aggiungere quanto segue:
   
    ```
    => issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");
    ```
   
    Attestazione corrispondente:
   
    ```
    <saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
    <saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
    </saml:Attribute>
    ```
8. Fare clic su **OK** e su **Fine**. Chiudere la console di gestione di ADFS.

Gli utenti possono quindi completare l'accesso utilizzando il metodo locale (ad esempio smart card).

## <a name="trusted-ips-for-federated-users"></a>Indirizzi IP attendibili per utenti federati
Gli indirizzi IP attendibili consentono agli amministratori di ignorare la verifica in due passaggi per specifici indirizzi IP o utenti federati con richieste provenienti dalla propria rete Intranet. Le sezioni seguenti descrivono come configurare gli indirizzi IP attendibili di Azure Multi-Factor Authentication con utenti federati e ignorare la verifica in due passaggi quando una richiesta proviene da una Intranet di utenti federati. Questo avviene configurando ADFS in modo da applicare la funzione di pass-through o filtro a un modello di attestazione in ingresso con il tipo di attestazione All'interno della rete aziendale.

Questo esempio usa Office 365 per l'attendibilità del componente.

### <a name="configure-the-ad-fs-claims-rules"></a>Configurare le regole attestazioni di ADFS
Per prima cosa è necessario configurare le attestazioni ADFS. Si creeranno due regole attestazioni, una per il tipo di attestazione All'interno della rete aziendale e un'altra per mantenere gli utenti connessi.

1. Aprire il componente di gestione di ADFS.
2. A sinistra selezionare **Attendibilità componente**.
3. Fare clic con il pulsante destro del mouse su **Piattaforma delle identità di Microsoft Office 365** e selezionare **Modifica regole attestazione...**
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. In Regole di trasformazione rilascio fare clic su **Aggiungi regola.**
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. Nell'Aggiunta guidata regole attestazione di trasformazione selezionare **Applicare la funzione di pass-through o di filtro a un'attestazione in ingresso** dall'elenco a discesa e fare clic su **Avanti**.
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. Nella casella Nome regola attestazione assegnare un nome alla regola. Ad esempio: InternoReteAziend.
7. Nell'elenco a discesa accanto a Tipo di attestazione in ingresso selezionare **All'interno della rete aziendale**.
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. Fare clic su **Fine**.
9. In Regole di trasformazione rilascio fare clic su **Aggiungi regola**.
10. Nell'Aggiunta guidata regole attestazione di trasformazione selezionare **Inviare attestazioni mediante una regola personalizzata** dall'elenco a discesa e fare clic su **Avanti**.
11. Nella casella sotto a Nome regola attestazione: specificare di *mantenere gli utenti connessi*.
12. Nella casella Regola personalizzata immettere:
    
        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. Fare clic su **Fine**.
14. Fare clic su **Apply**.
15. Fare clic su **OK**.
16. Chiudere Gestione ADFS.

### <a name="configure-azure-multifactor-authentication-trusted-ips-with-federated-users"></a>Configurare gli indirizzi IP attendibili di Azure Multi-Factor Authentication con utenti federati
Ora che le attestazioni sono configurate, è possibile procedere alla configurazione degli indirizzi IP attendibili.

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com).
2. A sinistra fare clic su **Active Directory**.
3. In Directory selezionare la directory in cui impostare gli indirizzi IP attendibili.
4. Nella directory selezionata fare clic su **Configura**.
5. Nella sezione Multi-Factor Authentication fare clic su **Gestisci impostazioni del servizio**.
6. Nella pagina Impostazioni servizio selezionare **Ignora l'autenticazione a più fattori per le richieste provenienti da utenti federati nella Intranet** in Indirizzi IP attendibili.
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. Fare clic su **save**.
8. Dopo che gli aggiornamenti sono stati applicati, fare clic su **Chiudi**.

La procedura è terminata. A questo punto, gli utenti federati di Office 365 dovrebbero usare solo MFA quando una richiesta proviene dall'esterno della Intranet aziendale.




<!--HONumber=Nov16_HO2-->


