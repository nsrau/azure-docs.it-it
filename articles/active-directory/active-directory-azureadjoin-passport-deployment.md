---
title: Abilitare Microsoft Windows Hello for Business nell&quot;organizzazione | Documentazione Microsoft
description: Istruzioni per la distribuzione per abilitare Microsoft Passport all&quot;interno dell&quot;organizzazione.
services: active-directory
documentationcenter: 
keywords: configurare Microsoft Passport, distribuzione di Microsoft Windows Hello for Business
author: MarkusVi
manager: femila
tags: azure-classic-portal
ms.assetid: 7dbbe3c6-1cd7-429c-a9b2-115fcbc02416
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 6f08fcdb4ec6dd124760cff54c522d998d5a0327
ms.lasthandoff: 12/28/2016


---
# <a name="enable-microsoft-windows-hello-for-business-in-your-organization"></a>Abilitare Microsoft Windows Hello for Business nell'organizzazione
Dopo [aver connesso i dispositivi appartenenti a un dominio di Windows 10 ad Azure Active Directory](active-directory-azureadjoin-devices-group-policy.md), seguire questa procedura per abilitare Microsoft Windows Hello for Business nell'organizzazione:

1. Distribuire System Center Configuration Manager  
2. Configurare le impostazioni dei criteri
3. Configurare il profilo certificato  

## <a name="deploy-system-center-configuration-manager"></a>Distribuire System Center Configuration Manager
Per distribuire certificati utente in base alle chiavi di Windows Hello for Business, è necessario quanto segue:

* **System Center Configuration Manager Current Branch**: è necessario installare la versione 1606 o successiva. Per altre informazioni, vedere la [documentazione di System Center Configuration Manager](https://technet.microsoft.com/library/mt346023.aspx) e il [blog del team di System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
* **Infrastruttura a chiave pubblica (PKI)**: per abilitare Microsoft Windows Hello for Business usando i certificati utente, è necessario avere un'infrastruttura PKI. Se non si dispone di un'infrastruttura di questo tipo o non si desidera usarla per i certificati utente, è possibile distribuire un nuovo controller di dominio che abbia installato Windows Server 2016 build 10551 (o versione successiva). Seguire i passaggi per [installare un controller di dominio di replica in un dominio esistente](https://technet.microsoft.com/library/jj574134.aspx) o per [installare una nuova foresta di Active Directory, se si crea un nuovo ambiente](https://technet.microsoft.com/library/jj574166). I file con estensione iso sono disponibili per il download in [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true).

## <a name="configure-policy-settings"></a>Configurare le impostazioni dei criteri
Per configurare le impostazioni dei criteri di Microsoft Windows Hello for Business, sono disponibili due opzioni:

* Criteri di gruppo in Active Directory 
* System Center Configuration Manager 

Usare Criteri di gruppo in Active Directory è il metodo consigliato per configurare le impostazioni dei criteri di Microsoft Windows Hello for Business. 

Usare System Center Configuration Manager è il metodo preferito quando lo si usa anche per distribuire i certificati. Questo scenario:

* Assicura la compatibilità con gli scenari di distribuzione più recenti
* Richiede Windows 10 versione 1607 o successiva sul lato client.

### <a name="configure-microsoft-windows-hello-for-business-via-group-policy-in-active-directory"></a>Configurare Microsoft Windows Hello for Business tramite Criteri di gruppo in Active Directory
**Passaggi**:

1. Aprire Server Manager e passare a **Strumenti** > **Gestione Criteri di gruppo**.
2. Da Gestione Criteri di gruppo passare al nodo corrispondente al dominio in cui abilitare la funzionalità di aggiunta ad Azure AD.
3. Fare clic con il pulsante destro del mouse su **Oggetti Criteri di gruppo** e scegliere **Nuovo**. Assegnare un nome all'oggetto Criteri di gruppo, ad esempio Abilita Windows Hello for Business. Fare clic su **OK**.
4. Fare clic con il pulsante destro del mouse sul nuovo oggetto Criteri di gruppo e scegliere **Modifica**.
5. Passare a **Configurazione computer** > **Criteri** > **Modelli amministrativi** > **Componenti di Windows** > **Windows Hello for Business**.
6. Fare doppio clic su **Abilita Windows Hello for Business** e selezionare **Modifica**.
7. Selezionare il pulsante di opzione **Abilitato** e fare clic su **Applica**. Fare clic su **OK**.
8. È ora possibile collegare l'oggetto Criteri di gruppo a una posizione di propria scelta. Per abilitare questo oggetto per tutti i dispositivi appartenenti a un dominio di Windows 10 nell'organizzazione, collegarlo al dominio. ad esempio:
   * Una specifica unità organizzativa in Active Directory in cui saranno posizionati i computer Windows 10 aggiunti a un dominio.
   * Uno specifico gruppo di sicurezza contenente i computer appartenenti a un dominio di Windows 10 che verranno registrati automaticamente in Azure AD.

### <a name="configure-windows-hello-for-business-using-system-center-configuration-manager"></a>Configurare Windows Hello for Business mediante System Center Configuration Manager
**Passaggi**:

1. Aprire **System Center Configuration Manager**, passare ad **Asset e conformità > Impostazioni di conformità > Accesso risorse aziendali > Profili Windows Hello for Business**.
   
    ![Configurare Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/01.png)
2. Nella barra degli strumenti nella parte superiore fare clic su **Crea profilo Windows Hello for Business**.
   
    ![Configurare Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/02.png)
3. Nella finestra di dialogo **Generale** seguire questa procedura:
   
    ![Configurare Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/03.png)
   
    a. Nella casella di testo **Nome** digitare un nome per il profilo, ad esempio **Il mio profilo WHfB**.
   
    b. Fare clic su **Avanti**.
4. Nella finestra di dialogo **Piattaforme supportate** selezionare le piattaforme che saranno fornite con questo profilo Windows Hello for Business e fare clic su **Avanti**.
   
    ![Configurare Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/04.png)
5. Nella finestra di dialogo **Impostazioni** seguire questa procedura:
   
    ![Configurare Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/05.png)
   
    a. Per **Configura Windows Hello for Business** selezionare **Attivato**.
   
    b. Per **Usa Trusted Platform Module (TPM)** selezionare **Richiesto**. 
   
    c. Per **Metodo di autenticazione **selezionare **Basata su certificati**.
   
    d. Fare clic su **Avanti**.
6. Nella finestra di dialogo **Riepilogo** fare clic su **Avanti**.
7. Nella finestra di dialogo **Completamento** fare clic su **Chiudi**.
8. Nel barra degli strumenti in alto fare clic su **Distribuisci**.
   
    ![Configurare Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/06.png)

## <a name="configure-the-certificate-profile"></a>Configurare il profilo certificato
Se si usa l'autenticazione basata su certificato per l'autenticazione locale, è necessario configurare e distribuire un profilo del certificato. Questa attività richiede di configurare un server NDES e il ruolo del sito del punto di registrazione certificati in System Center Configuration Manager. Per altre informazioni, vedere [Prerequisiti per i profili certificato in Configuration Manager](https://technet.microsoft.com/library/dn261205.aspx).

1. Aprire **System Center Configuration Manager**, quindi passare ad **Asset e conformità > Impostazioni di conformità > Accesso risorse aziendali > Profili certificato**.
2. Selezionare un modello dotato di utilizzo chiavi avanzato (EKU) per l'accesso con smart card.

Nella pagina **Registrazione SCEP** del profilo certificato è necessario scegliere **Installa in Passport for Work oppure genera errore** come **Provider di archiviazione chiavi**.

## <a name="next-steps"></a>Passaggi successivi
* [Windows 10 per le aziende: modalità d'uso dei dispositivi di lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticazione delle identità senza password con Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)


