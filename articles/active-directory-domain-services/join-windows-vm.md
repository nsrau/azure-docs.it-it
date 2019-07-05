---
title: 'Azure Active Directory Domain Services: Aggiungere una VM Windows Server a un dominio gestito | Microsoft Docs'
description: Aggiungere una macchina virtuale Windows Server ad Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: 377e253ef595e933f3ccab76bd053e2b416d3a16
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473174"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Aggiungere una macchina virtuale Windows Server a un dominio gestito
Questo articolo illustra come distribuire una macchina virtuale Windows Server usando il portale di Azure. Descrive quindi come aggiungere la macchina virtuale a un dominio gestito di Azure Active Directory Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="step-1-create-a-windows-server-virtual-machine"></a>Passaggio 1: creare una macchina virtuale Windows Server
Per creare una macchina virtuale Windows aggiunta alla rete virtuale in cui è stato abilitato Azure Active Directory Domain Services, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella parte superiore del riquadro sinistro selezionare **Nuovo**.
3. Selezionare **Calcolo** e quindi **Windows Server 2016 Datacenter**.

    ![Collegamento di Windows Server 2016 Datacenter](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. Nel riquadro **Informazioni di base** della procedura guidata configurare le impostazioni di base per la macchina virtuale.

    ![Riquadro Informazioni di base](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > Il nome utente e la password immessi in questa posizione devono appartenere a un account amministratore locale usato per accedere alla macchina virtuale. Scegliere una password complessa per proteggere la macchina virtuale dagli attacchi di forza bruta alle password. Non immettere in questo campo le credenziali di un account utente di dominio.
    >

5. Selezionare una **Dimensione** per la macchina virtuale. Per visualizzare altre dimensioni, selezionare **Visualizza tutto** o modificare il filtro **Tipo di disco supportato**.

    ![Riquadro Scegli una dimensione](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. Nel riquadro **Impostazioni** selezionare la rete virtuale in cui è stato distribuito il dominio gestito di Azure Active Directory Domain Services. Selezionare una subnet diversa da quella in cui è stato distribuito il dominio gestito. Per altre impostazioni, mantenere i valori predefiniti e quindi fare clic su **OK**.

    ![Impostazioni della rete virtuale per la macchina virtuale](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **Selezionare la rete virtuale e la subnet corretta.**
    >
    > Selezionare la rete virtuale in cui è stato distribuito il dominio gestito o una rete virtuale connessa al dominio tramite peering di rete virtuale. Se si seleziona una rete virtuale non connessa, non è possibile aggiungere la macchina virtuale al dominio gestito.
    >
    > È consigliabile distribuire il dominio gestito in una subnet dedicata. Pertanto, non selezionare la subnet in cui è stato abilitato il dominio gestito.

7. Per altre impostazioni, mantenere i valori predefiniti e quindi fare clic su **OK**.
8. Nella pagina **Acquisto** controllare le impostazioni e fare clic su **OK** per distribuire la macchina virtuale.
9. La distribuzione della macchina virtuale viene aggiunta al dashboard del portale di Azure.

    ![Operazione completata](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
10. Al termine della distribuzione, è possibile visualizzare informazioni relative alla macchina virtuale nella pagina **Panoramica**.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-by-using-the-local-administrator-account"></a>Passaggio 2: connettersi alla macchina virtuale Windows Server usando l'account amministratore locale
È ora possibile connettersi alla macchina virtuale Windows Server appena creata per aggiungerla al dominio. Usare le credenziali di amministratore locale specificate durante la creazione della macchina virtuale.

Per connettersi alla macchina virtuale, seguire questa procedura:

1. Nel riquadro **Panoramica** selezionare **Connetti**.  
    Viene creato e scaricato un file Remote Desktop Protocol con estensione rdp.

    ![Connettersi alla macchina virtuale Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Per connettersi alla VM, aprire il file RDP scaricato. Quando richiesto, selezionare **Connetti**.
3. Immettere le **credenziali di amministratore locale** specificate durante la creazione della macchina virtuale, ad esempio *localhost\mahesh*.
4. Se viene visualizzato un avviso relativo a un certificato durante il processo di accesso, selezionare **Sì** o **Continua** per connettersi.

A questo punto si dovrebbe essere connessi alla macchina virtuale Windows appena creata mediante le credenziali di amministratore locale. Il passaggio successivo consiste nell'aggiungere la macchina virtuale al dominio.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-azure-ad-ds-managed-domain"></a>Passaggio 3: aggiungere la macchina virtuale Windows Server al dominio gestito di Azure Active Directory Domain Services
Per aggiungere la macchina virtuale Windows Server al dominio gestito di Azure Active Directory Domain Services, completare questa procedura:

1. Connettersi alla macchina virtuale Windows Server come illustrato nel passaggio 2. Dalla schermata **Start** aprire **Server Manager**.
2. Nel riquadro sinistro della finestra **Server Manager** selezionare **Local Server**.

    ![Aprire la finestra Server Manager nella macchina virtuale](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. In **Proprietà**selezionare **Gruppo di lavoro**.
4. Nella finestra **Proprietà del sistema** selezionare **Modifica** per eseguire l'aggiunta al dominio.

    ![Finestra Proprietà del sistema](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

5. Nella scheda **Dominio** specificare il nome del dominio gestito di Azure Active Directory Domain Services e quindi selezionare **OK**.

    ![Specificare il dominio per l'aggiunta](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

6. Verrà chiesta l'immissione delle credenziali per l'aggiunta al dominio. Usare le credenziali relative a un *utente appartenente al gruppo di amministratori dei controller di dominio di Azure AD*. Solo i membri di questo gruppo hanno i privilegi necessari per aggiungete computer al dominio gestito.

    ![Finestra Sicurezza di Windows in cui specificare le credenziali](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

7. È possibile specificare le credenziali in uno dei modi seguenti:

   * **Formato UPN**: (consigliato) specificare il suffisso UPN (User Principal Name) per l'account utente, come configurato in Azure Active Directory. In questo esempio, il suffisso UPN dell'utente *bob* viene *bob\@domainservicespreview.onmicrosoft.com*.

   * **Formato sAMAccountName**: è possibile specificare il nome account con il formato SAMAccountName. In questo esempio l'utente *bob* deve immettere *CONTOSO100\bob*.

     > [!TIP]
     > **È consigliabile usare il formato UPN per specificare le credenziali.**
     >
     > Se il prefisso UPN dell'utente è troppo lungo (ad esempio *joehasareallylongname*), l'attributo SAMAccountName può essere generato automaticamente. Se a più utenti è associato lo stesso prefisso UPN (ad esempio *bob*) nel tenant di Azure AD, il rispettivo formato SAMAccountName può essere generato automaticamente dal servizio. In questi casi è possibile usare in modo affidabile il formato UPN per accedere al dominio.
     >

8. Dopo aver aggiunto correttamente un dominio, viene visualizzato il seguente messaggio di benvenuto al dominio.

    ![Messaggio di benvenuto al dominio](./media/active-directory-domain-services-admin-guide/join-domain-done.png)

9. Per completare la procedura di aggiunta al dominio, riavviare la macchina virtuale.

## <a name="troubleshoot-joining-a-domain"></a>Risoluzione dei problemi relativi alla procedura di aggiunta a un dominio
### <a name="connectivity-issues"></a>Problemi di connettività
Se la macchina virtuale non riesce a trovare il dominio, provare le procedure seguenti per la risoluzione dei problemi:

* Verificare che la macchina virtuale sia connessa alla stessa rete virtuale in cui è stato abilitato Azure Active Directory Domain Services. In caso contrario, la macchina virtuale non riesce a eseguire la connessione o l'aggiunta al dominio.

* Verificare che la macchina virtuale sia inclusa in una rete virtuale a sua volta connessa alla rete virtuale in cui è stato abilitato Azure Active Directory Domain Services.

* Provare a eseguire il ping del nome di dominio DNS usando il nome del dominio gestito, ad esempio *ping contoso100.com*. Se non è possibile eseguire questa operazione, provare a eseguire il ping degli indirizzi IP per il dominio visualizzati nella pagina in cui è stato abilitato Azure Active Directory Domain Services, ad esempio *ping 10.0.0.4*. Se si riesce a effettuare il ping dell'indirizzo IP ma non del dominio, la configurazione del server DNS potrebbe non essere valida. Controllare che gli indirizzi IP del dominio siano configurati come server DNS per la rete virtuale.

* Provare a scaricare la cache del resolver DNS sulla macchina virtuale (*ipconfig /flushdns*).

Se viene visualizzata una finestra che richiede le credenziali per l'aggiunta al dominio, non sono presenti problemi di connettività.

### <a name="credentials-related-issues"></a>Problemi correlati alle credenziali
Se si verificano problemi con le credenziali e non è possibile completare l'aggiunta al dominio, provare a eseguire i passaggi seguenti per la risoluzione dei problemi:

* Provare a usare il formato UPN per specificare le credenziali. Se sono presenti diversi utenti con lo stesso prefisso UPN nel tenant o se il prefisso UPN è troppo lungo, l'attributo SAMAccountName per l'account può essere generato automaticamente. In questi casi, il formato SAMAccountName per l'account può essere diverso da quello previsto o usato nel dominio locale.

* Provare a usare le credenziali di un account utente appartenente al gruppo *Amministratori di AAD DC*.

* Controllare di avere [abilitato la sincronizzazione password](active-directory-ds-getting-started-password-sync.md) nel dominio gestito.

* Verificare che sia stato usato il nome UPN dell'utente come configurato in Azure AD (ad esempio, *bob\@domainservicespreview.onmicrosoft.com*) per l'accesso.

* Attendere il tempo necessario per consentire il completamento della sincronizzazione delle password, come specificato nella guida introduttiva.

## <a name="related-content"></a>Contenuti correlati
* [Guida introduttiva ad Azure Active Directory Domain Services](create-instance.md)
* [Gestire un dominio di Azure AD Domain Services](manage-domain.md)
