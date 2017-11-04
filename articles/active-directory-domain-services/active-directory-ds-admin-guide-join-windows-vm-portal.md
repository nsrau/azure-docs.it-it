---
title: Azure Active Directory Domain Services - Aggiungere una macchina virtuale Windows Server a un dominio gestito | Documentazione Microsoft
description: Aggiungere una macchina virtuale Windows Server ai Servizi di dominio Azure AD
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: 5f661dba2e647ac905e7d84927fdbf6dbc76094f
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Aggiungere una macchina virtuale Windows Server a un dominio gestito
Questo articolo illustra come distribuire una macchina virtuale di Windows Server usando il portale di Azure. Descrive quindi come aggiungere la macchina virtuale a un dominio gestito di Azure AD Domain Services.

## <a name="step-1-create-the-windows-server-virtual-machine"></a>Passaggio 1: Creare la macchina virtuale Windows Server
Seguire questa procedura per creare una macchina virtuale Windows aggiunta alla rete virtuale in cui sono stati abilitati i Servizi di dominio Azure AD.

1. Accedere al portale di Azure all'indirizzo [http://portal.azure.com](http://portal.azure.com).
2. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.
3. Selezionare **Calcolo** e quindi **Windows Server 2016 Datacenter**.

    ![Selezionare l'immagine](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)
4. Configurare le impostazioni di base per la macchina virtuale nella pagina **Informazioni di base** della procedura guidata.

    ![Configurare le impostazioni di base per la macchina virtuale](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > Il nome utente e la password immessi in questa posizione devono appartenere a un account amministratore locale usato per accedere alla macchina virtuale. Scegliere una password complessa per proteggere la macchina virtuale dagli attacchi di forza bruta alle password. Non immettere in questo campo le credenziali di un account utente di dominio.
    >

5. Selezionare una **Dimensione** per la macchina virtuale. Per visualizzare altre dimensioni, selezionare **Visualizza tutto** o modificare il filtro **Supported disk type** (Tipo di disco supportato).

    ![Selezionare la dimensione per la macchina virtuale](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. Nella pagina **Impostazioni** della procedura guidata selezionare la rete virtuale in cui viene distribuito il dominio gestito di Azure AD Domain Services. Selezionare una subnet diversa da quella in cui viene distribuito il dominio gestito. Mantenere i valori predefiniti per le altre impostazioni e fare clic su **OK**.

    ![Selezionare una rete virtuale per la macchina virtuale](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **Selezionare la rete virtuale e la subnet corretta.**
    > Selezionare la rete virtuale in cui è distribuito il dominio gestito o una rete virtuale connessa al dominio tramite peering di rete virtuale. Se si seleziona una rete virtuale non connessa, non è possibile aggiungere la macchina virtuale al dominio gestito.
    > È consigliabile distribuire il dominio gestito in una subnet dedicata. Pertanto, non selezionare la subnet in cui è stato abilitato il dominio gestito.

7. Nella pagina **Acquisto** controllare le impostazioni e fare clic su **OK** per distribuire la macchina virtuale.
8. La distribuzione della macchina virtuale viene aggiunta al dashboard del portale di Azure.

    ![Operazione completata](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
9. Al termine della distribuzione, è possibile visualizzare informazioni relative alla macchina virtuale nella pagina **Panoramica**.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>Passaggio 2: Connettersi alla macchina virtuale Windows Server usando l'account amministratore locale
È ora possibile connettersi alla macchina virtuale Windows Server appena creata per aggiungerla al dominio. Usare le credenziali di amministratore locale specificate durante la creazione della macchina virtuale.

Seguire questa procedura per connettersi alla macchina virtuale.

1. Fare clic su sul pulsante **Connetti** nella pagina **Panoramica**. Viene creato e scaricato un file Remote Desktop Protocol con estensione rdp.

    ![Connettersi alla macchina virtuale Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
2. Per connettersi alla VM, aprire il file RDP scaricato. Se richiesto, fare clic su **Connetti**.
3. Al prompt di accesso digitare le **credenziali di amministratore locale** specificate durante la creazione della macchina virtuale, come 'localhost\mahesh' in questo esempio.
4. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Fare clic su Sì o Continua per procedere con la connessione.

A questo punto si dovrebbe essere connessi alla macchina virtuale Windows appena creata mediante le credenziali di amministratore locale. Il passaggio successivo consiste nell'aggiungere la macchina virtuale al dominio.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>Passaggio 3: Aggiungere la macchina virtuale Windows Server al dominio gestito dai Servizi di dominio Azure AD
Seguire questa procedura per aggiungere la macchina virtuale Windows Server al dominio gestito da Servizi di dominio Azure AD.

1. Connettersi a Windows Server come illustrato nel passaggio 2 precedente. Dalla schermata Start aprire **Server Manager**.
2. Fare clic su **Server locale** nel riquadro sinistro della finestra di Server Manager.

    ![Avviare Server Manager nella macchina virtuale](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)
3. Fare clic su **GRUPPO DI LAVORO** nella sezione **PROPRIETÀ**. Nella pagina **Proprietà del sistema** fare clic su **Modifica** per eseguire l'aggiunta al dominio.

    ![Pagina Proprietà di sistema](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)
4. Specificare il nome del dominio gestito di Azure AD Domain Services nella casella di testo **Dominio** e fare clic su **OK**.

    ![Specificare il dominio per l'aggiunta](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)
5. Verrà richiesta l'immissione delle credenziali per l'aggiunta al dominio. Assicurarsi di **specificare le credenziali per un utente appartenente al gruppo di amministratori dei controller di dominio di Azure AD** . Solo i membri di questo gruppo hanno i privilegi necessari per aggiungete computer al dominio gestito.

    ![Specificare le credenziali per l'aggiunta a un dominio](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)
6. È possibile specificare le credenziali in uno dei modi seguenti:

   * **Formato UPN: (consigliato)** specificare il suffisso UPN per l'account utente, come configurato in Azure AD. In questo esempio il suffisso UPN dell'utente "bob" è "bob@domainservicespreview.onmicrosoft.com".
   * **Formato SAMAccountName:** è possibile specificare il nome account con il formato SAMAccountName. In questo esempio l'utente 'bob' deve immettere 'CONTOSO100\bob'.

     > [!TIP]
     > **È consigliabile usare il formato UPN per specificare le credenziali.**
     > Se il prefisso UPN dell'utente è troppo lungo (ad esempio "joereallylongnameuser"), l'attributo SAMAccountName può essere generato automaticamente. Se a più utenti è associato lo stesso prefisso UPN (ad esempio "bob") nel tenant di Azure AD, il formato SAMAccountName relativo può essere generato automaticamente dal servizio. In questi casi è possibile usare in modo affidabile il formato UPN per accedere al dominio.
     >

7. Dopo l'aggiunta al dominio verrà visualizzato il messaggio di benvenuto seguente. Riavviare la macchina virtuale per completare l'operazione di aggiunta al dominio.

    ![Messaggio di benvenuto al dominio](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## <a name="troubleshooting-domain-join"></a>Risoluzione dei problemi di aggiunta al dominio
### <a name="connectivity-issues"></a>Problemi di connettività
Se la macchina virtuale non riesce a trovare il dominio, vedere le procedure seguenti per la risoluzione dei problemi:

* Assicurarsi che la macchina virtuale sia connessa alla stessa rete virtuale in cui sono stati abilitati i Servizi di dominio. In caso contrario, la macchina virtuale non riuscirà a connettersi al dominio e quindi non potrà essere aggiunta al dominio.
* Assicurarsi che la macchina virtuale sia inclusa in una rete virtuale a sua volta connessa alla rete virtuale in cui è stato abilitato Azure AD Domain Services.
* Provare a eseguire il ping del dominio usando il nome del dominio gestito, ad esempio 'ping contoso100.com'. Se non è possibile eseguire questa operazione, provare a eseguire il ping degli indirizzi IP per il dominio visualizzati nella pagina in cui sono stati abilitati i Servizi di dominio Azure AD, ad esempio 'ping 10.0.0.4'. Se si riesce a effettuare il ping dell'indirizzo IP ma non del dominio, è possibile che la configurazione del server DNS non sia valida. Controllare se gli indirizzi IP del dominio sono configurati come server DNS per la rete virtuale.
* Provare a scaricare la cache del resolver DNS sulla macchina virtuale ('ipconfig /flushdns').

Se viene visualizzata la finestra di dialogo che richiede le credenziali per l'aggiunta al dominio, non sono presenti problemi di connettività.

### <a name="credentials-related-issues"></a>Problemi correlati alle credenziali
Se si verificano problemi con le credenziali e non è possibile completare l'aggiunta al dominio, seguire questa procedura.

* Provare a usare il formato UPN per specificare le credenziali. Se sono presenti più utenti con lo stesso prefisso UPN nel tenant o se il prefisso UPN è troppo lungo, l'attributo SAMAccountName per l'account può essere generato automaticamente. Di conseguenza, il formato SAMAccountName per l'account può essere diverso da quello previsto o usato nel dominio locale.
* Provare a usare le credenziali di un account utente appartenente al gruppo 'Amministratori di AAD DC'.
* Assicurarsi di avere [abilitato la sincronizzazione delle password](active-directory-ds-getting-started-password-sync.md) secondo i passaggi descritti nella Guida introduttiva.
* Assicurarsi di usare l'UPN dell'utente come configurato in Azure AD, ad esempio "bob@domainservicespreview.onmicrosoft.com" per eseguire l'accesso.
* Assicurarsi di avere atteso per il tempo necessario per consentire il completamento della sincronizzazione delle password, come specificato nella Guida introduttiva.

## <a name="related-content"></a>Contenuti correlati
* [Guida introduttiva di Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Amministrare un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-administer-domain.md)
