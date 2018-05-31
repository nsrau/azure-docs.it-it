---
title: 'Azure AD Connect: Abilitazione del writeback dei dispositivi | Documentazione Microsoft'
description: Questo documento descrive come abilitare il writeback dei dispositivi usando Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: billmath
ms.openlocfilehash: c813be558df9dc3bdfd9850402b9458f1fdf971a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34353816"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: abilitazione del writeback dei dispositivi
> [!NOTE]
> Una sottoscrizione di Azure AD Premium è necessaria per il writeback dei dispositivi.
> 
> 

Il documento seguente illustra come abilitare la funzionalità di writeback dei dispositivi in Azure AD Connect. Il writeback dei dispositivi viene usato negli scenari seguenti:

* Per abilitare l'accesso condizionale in base ai dispositivi alle applicazione protette tramite ADFS 2012 R2 o versioni successive (trust della relying party).

Questo offre maggiore sicurezza e garantisce che l'accesso alle applicazioni venga concesso solo ai dispositivi attendibili. Per altre informazioni sull'accesso condizionale, vedere [Gestione dei rischi con l'accesso condizionale](../active-directory-conditional-access-azure-portal.md) e [Configurazione dell'accesso condizionale locale usando il servizio Registrazione dispositivo di Azure Active Directory](../active-directory-conditional-access-automatic-device-registration-setup.md).

> [!IMPORTANT]
> <li>I dispositivi devono trovarsi nella stessa foresta degli utenti. Data la necessità di eseguire il writeback dei dispositivi in una singola foresta, attualmente questa funzionalità non supporta una distribuzione con più foreste utente.</li>
> <li>Solo un oggetto di configurazione della registrazione dispositivi può essere aggiunto alla foresta locale di Active Directory. Questa funzionalità non è compatibile con una topologia in cui l'istanza locale di Active Directory è sincronizzata con più directory di Azure AD.</li>> 

## <a name="part-1-install-azure-ad-connect"></a>Parte 1: Installare Azure AD Connect
Installare Azure AD Connect usando le impostazioni personalizzate o rapide È consigliabile iniziare sincronizzando correttamente tutti gli utenti e i gruppi prima di abilitare il writeback dei dispositivi.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>Parte 2: Abilitare il writeback dei dispositivi in Azure AD Connect
1. Eseguire nuovamente l'installazione guidata. Selezionare **Configura le opzioni del dispositivo** dalla pagina Attività aggiuntive e fare clic su **Avanti**. 

    ![Configura le opzioni del dispositivo](./media/active-directory-aadconnect-feature-device-writeback/deviceoptions.png)

    >[!NOTE]
    > Il nuovo Configura le opzioni del dispositivo è disponibile solo nella versione 1.1.819.0 e successive.

2. Nella pagina delle opzioni del dispositivo, selezionare **Configura il writeback dispositivi**. L'opzione **Disabilita il writeback dispositivi** non sarà disponibile finché non viene abilitato il writeback dei dispositivi. Fare clic su **Successivo** per spostarsi alla pagina successiva nella procedura guidata.
    ![Scegliere l'operazione del dispositivo](./media/active-directory-aadconnect-feature-device-writeback/configuredevicewriteback1.png)

3. Nella pagina Writeback, il dominio specificato verrà visualizzato come la Foresta di writeback dei dispositivi predefinita.
   ![Installazione personalizzata - Foresta di destinazione del writeback dei dispositivi](./media/active-directory-aadconnect-feature-device-writeback/writebackforest.png)

4. La pagina **Contenitore di dispositivi** consente di preparare la active directory tramite una delle due opzioni disponibili:

    a. **Fornire le credenziali di amministratore di enterprise**: se le credenziali di amministratore di enterprise vengono fornite per la foresta in cui i dispositivi devono essere riscritti, Azure AD Connect preparerà foresta automaticamente durante la configurazione di writeback dispositivi.

    b. **Scarica lo script di PowerShell**: Azure AD Connect genera automaticamente uno script di PowerShell che può preparare la active directory per il writeback dispositivi. Nel caso in cui non sia possibile fornire le credenziali di amministratore di enterprise in Azure AD Connect, si consiglia di scaricare lo script di PowerShell. Fornire lo script di PowerShell scaricato **CreateDeviceContainer.psq** all'amministratore dell'enterprise della foresta in cui verrà eseguito il writeback dei dispositivi.
    ![Preparare la foresta active directory](./media/active-directory-aadconnect-feature-device-writeback/devicecontainercreds.png)
    
    Per preparare la foresta active directory vengono eseguite le operazioni seguenti:
    * Se non esistono già, crea e configura nuovi contenitori e oggetti in CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn].
    * Se non esistono già, crea e configura nuovi contenitori e oggetti in CN=RegisteredDevices,[domain-dn]. Gli oggetti dispositivo verranno creati in questo contenitore.
    * Imposta le autorizzazioni necessarie nell'account Azure AD Connector per gestire i dispositivi nella propria istanza di Active Directory.
    * Deve essere eseguito in una sola foresta, anche se Azure AD Connect verrà installato in più foreste.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Verificare che i dispositivi siano sincronizzati con Active Directory
Il writeback dei dispositivi dovrebbe funzionare correttamente. Tenere presente che l'esecuzione del writeback degli oggetti dispositivo in Active Directory può richiedere fino a 3 ore.  Per verificare che i dispositivi siano sincronizzati correttamente, al termine dell'esecuzione delle regole di sincronizzazione seguire questa procedura:

1. Avviare il Centro di amministrazione di Active Directory.
2. Espandere RegisteredDevices all'interno del dominio che verrà federato.

   ![Interfaccia di amministrazione di Active Directory - Dispositivi registrati](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)

3. I dispositivi attualmente registrati saranno elencati qui.

   ![Interfaccia di amministrazione di Active Directory - Elenco dei dispositivi registrati](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Abilitare l'accesso condizionale
Per informazioni dettagliate su come abilitare questo scenario, vedere [Configurazione dell'accesso condizionale locale usando il servizio Registrazione dispositivo di Azure Active Directory](../active-directory-conditional-access-automatic-device-registration-setup.md).

## <a name="troubleshooting"></a>risoluzione dei problemi
### <a name="the-writeback-checkbox-is-still-disabled"></a>La casella di controllo del writeback è ancora disabilitata
Se la casella di controllo per il writeback dei dispositivi non è ancora abilitata anche se sono stati seguiti i passaggi precedenti, la procedura seguente consentirà di verificare le caratteristiche dell'installazione guidata prima dell'abilitazione della casella.

Attività iniziali:

* Assicurarsi che almeno una foresta disponga di Windows Server 2012 R2. Il tipo di oggetto del dispositivo deve essere presente.
* Se l'installazione guidata è già in esecuzione, non verranno rilevate tutte le modifiche. In questo caso, completare l'installazione guidata ed eseguirla nuovamente.
* Assicurarsi che l'account specificato nello script di inizializzazione sia effettivamente l'utente corretto usato da Active Directory Connector. A questo scopo, seguire questa procedura:
  * Avviare **Servizio di sincronizzazione**dal menu Start.
  * Aprire la scheda **Connettori** .
  * Trovare il connettore con il tipo Servizi di dominio di Active Directory e selezionarlo.
  * In **Azioni** selezionare **Proprietà**.
  * Passare a **Connetti a Foresta Active Directory**. Verificare che il dominio e il nome utente specificati in questa schermata corrispondano all'account specificato per lo script.
    ![Account connettore in Synchronization Service Manager](./media/active-directory-aadconnect-feature-device-writeback/connectoraccount.png)

Verificare la configurazione in Active Directory:

* Verificare che il servizio Registrazione dispositivo si trovi nel percorso seguente (CN=DeviceRegistrationService,CN=Device Registration Services,CN=Device Registration Configuration,CN=Services,CN=Configuration) nel contesto dei nomi di configurazione.

![Risoluzione dei problemi, DeviceRegistrationService nello spazio dei nomi di configurazione](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot1.png)

* Verificare che sia presente un solo oggetto di configurazione cercando lo spazio dei nomi di configurazione. Se sono presenti più oggetti, eliminare il duplicato.

![Risoluzione dei problemi, cercare gli oggetti duplicati](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot2.png)

* Assicurarsi che nell'oggetto Device Registration Service sia presente l'attributo msDS-DeviceLocation con un valore associato. Cercare la posizione e assicurarsi che sia presente con il tipo di oggetto msDS-DeviceContainer.

![Risoluzione dei problemi, msDS-DeviceLocation](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot3.png)

![Risoluzione dei problemi, classe di oggetti RegisteredDevices](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot4.png)

* Verificare che l'account usato da Active Directory Connector disponga delle autorizzazioni necessarie per il contenitore Dispositivi registrati trovato nel passaggio precedente. Si tratta delle autorizzazioni previste per questo contenitore:

![Risoluzione dei problemi, verificare le autorizzazioni per il contenitore](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot5.png)

* Verificare che l'account Active Directory disponga delle autorizzazioni nell'oggetto CN=Device Registration Configuration,CN=Services,CN=Configuration.

![Risoluzione dei problemi, verificare le autorizzazioni per la configurazione della registrazione dispositivo](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Informazioni aggiuntive
* [Gestione dei rischi con l'accesso condizionale](../active-directory-conditional-access-azure-portal.md)
* [Configurazione dell'accesso condizionale locale usando il servizio Registrazione dispositivo di Azure Active Directory](../active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

