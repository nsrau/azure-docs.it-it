---
title: 'Azure AD Connect: Abilitazione del writeback dei dispositivi | Documentazione Microsoft'
description: Questo documento descrive come abilitare il writeback dei dispositivi usando Azure AD Connect
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: billmath
ms.openlocfilehash: 9c0ff3394dac12bdcac9d618832566ef0d3a6609
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
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
1. Installare Azure AD Connect usando le impostazioni personalizzate o rapide È consigliabile iniziare sincronizzando correttamente tutti gli utenti e i gruppi prima di abilitare il writeback dei dispositivi.

## <a name="part-2-prepare-active-directory"></a>Parte 2: Preparare Active Directory
Per preparare il writeback dei dispositivi, seguire questa procedura.

1. Dal computer in cui è installato Azure AD, avviare PowerShell con privilegi elevati.
2. Se il modulo Active Directory PowerShell NON è installato, installare Strumenti di amministrazione remota del server, che contiene il modulo AD PowerShell e dsacls.exe, necessario per eseguire lo script.  Eseguire il comando seguente:
  
   ``` powershell
   Add-WindowsFeature RSAT-AD-Tools
   ```

3. Se il modulo di Azure Active Directory per PowerShell NON è installato, scaricarlo e installarlo da [Modulo di Azure Active Directory per Windows PowerShell (versione a 64 bit)](http://go.microsoft.com/fwlink/p/?linkid=236297). Questo componente presenta una dipendenza dall'Assistente per l'accesso, che viene installato con Azure AD Connect.  
4. Con le credenziali di amministratore dell'organizzazione, eseguire i comandi seguenti e quindi uscire da PowerShell.
   
   ``` powershell
   Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'
   ```

   ``` powershell
   Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}
   ```

Le credenziali di amministratore dell'organizzazione sono obbligatorie, perché è necessario apportare modifiche allo spazio dei nomi di configurazione. Le autorizzazioni di un amministratore di dominio non sono sufficienti.

![PowerShell per l'abilitazione del writeback dei dispositivi](./media/active-directory-aadconnect-feature-device-writeback/powershell.png)


Descrizione:

* Se non esistono già, crea e configura nuovi contenitori e oggetti in CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn].
* Se non esistono già, crea e configura nuovi contenitori e oggetti in CN=RegisteredDevices,[domain-dn]. Gli oggetti dispositivo verranno creati in questo contenitore.
* Imposta le autorizzazioni necessarie nell'account Azure AD Connector per gestire i dispositivi nella propria istanza di Active Directory.
* Deve essere eseguito in una sola foresta, anche se Azure AD Connect verrà installato in più foreste.

Parametri

* DomainName: dominio di Active Directory in cui verranno creati gli oggetti dispositivo. Nota: tutti i dispositivi per una foresta Active Directory specifica verranno creati in un singolo dominio.
* AdConnectorAccount: account Active Directory che verrà usato da Azure AD Connect per gestire gli oggetti nella directory. Si tratta dell'account usato dalla sincronizzazione di Azure AD Connect per connettersi ad Active Directory. Se è stata eseguita l'installazione usando le impostazioni rapide, è l'account con prefisso MSOL_.

## <a name="part-3-enable-device-writeback-in-azure-ad-connect"></a>Parte 3: Abilitare il writeback dei dispositivi in Azure AD Connect
Per abilitare il writeback dei dispositivi in Azure AD Connect, seguire questa procedura.

1. Eseguire nuovamente l'installazione guidata. Selezionare **Personalizzazione delle opzioni di sincronizzazione** dalla pagina Attività aggiuntive e fare clic su **Avanti**.
   ![Installazione personalizzata - Personalizzazione delle opzioni di sincronizzazione](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback2.png)
2. Nella pagina Funzionalità facoltative l'opzione Writeback dispositivi non sarà più disabilitata. Si noti che se le procedure di preparazione di Azure AD Connect non vengono completate, l'opzione Writeback dispositivi risulterà disabilitata nella pagina Funzionalità facoltative. Selezionare la casella per il writeback dei dispositivi e fare clic su **Avanti**. Se la casella di controllo risulta ancora disattivata, vedere la sezione [Risoluzione dei problemi](#the-writeback-checkbox-is-still-disabled).
   ![Installazione personalizzata - Funzionalità facoltative di writeback dei dispositivi](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback3.png)
3. Nella pagina Writeback, il dominio specificato verrà visualizzato come la Foresta di writeback dei dispositivi predefinita.
   ![Installazione personalizzata - Foresta di destinazione del writeback dei dispositivi](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback4.png)
4. Completare l'installazione guidata senza ulteriori modifiche di configurazione. Se necessario fare riferimento a [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md)

## <a name="enable-conditional-access"></a>Abilitare l'accesso condizionale
Per informazioni dettagliate su come abilitare questo scenario, vedere [Configurazione dell'accesso condizionale locale usando il servizio Registrazione dispositivo di Azure Active Directory](../active-directory-conditional-access-automatic-device-registration-setup.md).

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Verificare che i dispositivi siano sincronizzati con Active Directory
Il writeback dei dispositivi dovrebbe funzionare correttamente. Tenere presente che l'esecuzione del writeback degli oggetti dispositivo in Active Directory può richiedere fino a 3 ore.  Per verificare che i dispositivi siano sincronizzati correttamente, al termine dell'esecuzione delle regole di sincronizzazione seguire questa procedura:

1. Avviare il Centro di amministrazione di Active Directory.
2. Espandere RegisteredDevices all'interno del dominio che verrà federato.
   ![Interfaccia di amministrazione di Active Directory - Dispositivi registrati](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)
3. I dispositivi attualmente registrati saranno visualizzati in questo elenco.
   ![Interfaccia di amministrazione di Active Directory - Elenco dei dispositivi registrati](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

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

