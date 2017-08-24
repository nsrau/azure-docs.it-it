---
title: Distribuire l'estensione Pannello di accesso di Azure per Internet Explorer con un criterio di gruppo | Documentazione Microsoft
description: Come usare Criteri di gruppo per distribuire il componente aggiuntivo di Internet Explorer per il portale App personali.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 7c2d49c8-5be0-4e7e-abac-332f9dfda736
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/02/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 53ce95811bbeb306ae986fda91d3387db4e71998
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Come distribuire l'estensione Pannello di accesso per Internet Explorer con Criteri di gruppo
Questa esercitazione illustra come usare Criteri di gruppo per installare l'estensione Pannello di accesso per Internet Explorer nei computer degli utenti in modalità remota. Questa estensione è necessaria per gli utenti di Internet Explorer che devono eseguire l'accesso ad app configurate con l' [accesso Single Sign-On basato su password](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

Si consiglia agli amministratori di automatizzare la distribuzione di questa estensione. In caso contrario, gli utenti devono scaricare e installare l'estensione in autonomia, un'operazione soggetta a errori e che richiede autorizzazioni di amministratore. Questa esercitazione descrive un metodo per automatizzare le distribuzioni software tramite Criteri di gruppo. [Altre informazioni su Criteri di gruppo](https://technet.microsoft.com/windowsserver/bb310732.aspx)

L'estensione Pannello di accesso è disponibile per [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) e [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998). In entrambi i casi l'installazione non richiede autorizzazioni di amministratore.

## <a name="prerequisites"></a>Prerequisiti
* [Servizi di dominio Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)è già stato configurato e i computer degli utenti sono stati aggiunti al dominio.
* Per modificare l'oggetto Criteri di gruppo è necessaria l'autorizzazione "Modifica impostazione". Questa autorizzazione è assegnata per impostazione predefinita ai membri dei gruppi di sicurezza seguenti: Domain Administrators, Enterprise Administrators e Group Policy Creator Owners. [Altre informazioni.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Passaggio 1: Creare il punto di distribuzione
Per prima cosa, salvare il pacchetto del programma di installazione in un percorso di rete a cui possono accedere i computer in cui si vuole installare l'estensione in modalità remota. A questo scopo, seguire questa procedura:

1. Eseguire l'accesso al server come amministratore
2. Nella finestra **Server Manager** passare a **Servizi file e archiviazione**.
   
    ![Apertura di Servizi file e archiviazione](./media/active-directory-saas-ie-group-policy/files-services.png)
3. Passare alla scheda **Condivisioni** . Fare clic su **Attività** > **Nuova condivisione...**
   
    ![Apertura di Servizi file e archiviazione](./media/active-directory-saas-ie-group-policy/shares.png)
4. Completare la **Creazione guidata nuova condivisione** e impostare le autorizzazioni per assicurarsi che i computer degli utenti possano accedervi. [Altre informazioni sulle condivisioni.](https://technet.microsoft.com/library/cc753175.aspx)
5. Scaricare il pacchetto di Microsoft Windows Installer (file MSI) seguente: [Access Panel Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access Panel Extension.msi)
6. Copiare il pacchetto del programma di installazione nel percorso desiderato nella condivisione.
   
    ![Copiare il file MSI nella condivisione.](./media/active-directory-saas-ie-group-policy/copy-package.png)
7. Verificare che i computer client possano accedere al pacchetto del programma di installazione dalla condivisione. 

## <a name="step-2-create-the-group-policy-object"></a>Passaggio 2: Creare l'oggetto Criteri di gruppo
1. Accedere al server che ospita l'installazione di Servizi di dominio Active Directory.
2. In Server Manager passare a **Strumenti** > **Gestione Criteri di gruppo**.
   
    ![Passare a Strumenti > Gestione Criteri di gruppo](./media/active-directory-saas-ie-group-policy/tools-gpm.png)
3. Nel riquadro sinistro della finestra **Gestione Criteri di gruppo** visualizzare la gerarchia della propria unità organizzativa e determinare il livello in base al quale verranno applicati i criteri di gruppo. Ad esempio, è possibile decidere di selezionare un'unità organizzativa di piccole dimensioni per eseguire la distribuzione per un numero limitato di utenti ai fini del test oppure scegliere un'unità organizzativa di primo livello per eseguire la distribuzione a livello dell'intera organizzazione.
   
   > [!NOTE]
   > Per creare o modificare le unità organizzative, tornare a Server Manager e passare a **Strumenti** > **Utenti e computer di Active Directory**.
   > 
   > 
4. Dopo aver selezionato un'unità organizzativa, fare clic con il pulsante destro del mouse su di essa e scegliere **Crea un oggetto Criteri di gruppo in questo dominio e crea qui un collegamento...**
   
    ![Creare un nuovo oggetto Criteri di gruppo](./media/active-directory-saas-ie-group-policy/create-gpo.png)
5. Nella finestra **Nuovo oggetto Criteri di gruppo** digitare un nome per il nuovo oggetto Criteri di gruppo.
   
    ![Assegnare un nome al nuovo oggetto Criteri di gruppo](./media/active-directory-saas-ie-group-policy/name-gpo.png)
6. Fare clic con il pulsante destro del mouse sull'oggetto Criteri di gruppo creato e scegliere **Modifica**.
   
    ![Modificare il nuovo oggetto Criteri di gruppo](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

## <a name="step-3-assign-the-installation-package"></a>Passaggio 3: Assegnare il pacchetto di installazione
1. Determinare se l'estensione dovrà essere distribuita in base a una **Configurazione computer** o una **Configurazione utente**. Con una [Configurazione computer](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), l'estensione viene installata nel computer indipendentemente dall'utente che vi accede. Con una [Configurazione utente](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), l'estensione viene installata per l'utente, indipendentemente dal computer a cui accede.
2. Nel riquadro sinistro della finestra **Editor Gestione Criteri di gruppo** passare a uno dei percorsi di cartella seguenti, a seconda del tipo di configurazione scelto:
   
   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`
3. Fare clic con il pulsante destro del mouse su **Installazione software** e quindi scegliere **Nuovo** > **Pacchetto...**
   
    ![Creare un nuovo pacchetto di installazione software](./media/active-directory-saas-ie-group-policy/new-package.png)
4. Passare alla cartella condivisa che contiene il pacchetto del programma di installazione scaricato nel [Passaggio 1: Creare il punto di distribuzione](#step-1-create-the-distribution-point), selezionare il file MSI e quindi fare clic su **Apri**.
   
   > [!IMPORTANT]
   > Se la condivisione si trova nello stesso server, verificare che l'accesso al file MSI avvenga tramite il percorso di rete e non tramite il percorso locale.
   > 
   > 
   
    ![Selezionare il pacchetto di installazione dalla cartella condivisa.](./media/active-directory-saas-ie-group-policy/select-package.png)
5. Nella finestra **Installazione applicazione** selezionare **Assegnata** per il metodo di distribuzione in uso. Fare quindi clic su **OK**.
   
    ![Selezionare Assegnata e quindi fare clic su OK.](./media/active-directory-saas-ie-group-policy/deployment-method.png)

L'estensione è stata distribuita nell'unità organizzativa selezionata. [Altre informazioni su Criteri di gruppo Installazione software](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Passaggio 4: Abilitare automaticamente l'estensione per Internet Explorer
Dopo aver eseguito il programma di installazione, è necessario abilitare in modo esplicito ogni estensione per Internet Explorer prima di poterla usare. Per abilitare l'estensione Pannello di accesso tramite Criteri di gruppo, seguire questa procedura:

1. Nella finestra **Editor Gestione Criteri di gruppo** passare a uno dei percorsi di cartella seguenti, a seconda del tipo di configurazione scelto in [Passaggio 3: Assegnare il pacchetto di installazione](#step-3-assign-the-installation-package):
   
   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
2. Fare clic con il pulsante destro del mouse su **Elenco componenti aggiuntivi** e scegliere **Modifica**.
    ![Modifica dell'elenco componenti aggiuntivi.](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)
3. Nella finestra **Elenco componenti aggiuntivi** selezionare **Abilitati**. Nella sezione **Opzioni** fare clic su **Mostra...**.
   
    ![Fare clic su Abilita e quindi su Mostra...](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)
4. Nella finestra **Visualizzazione contenuto** seguire questa procedura:
   
   1. Per la prima colonna (campo **Nome valore**) copiare e incollare l'ID classe seguente: `{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   2. Per la seconda colonna (campo **Valore**) copiare e incollare il valore seguente: `1`
   3. Fare clic su **OK** per chiudere la finestra di dialogo **Visualizzazione contenuto**.
      
      ![Compilare i valori come specificato sopra.](./media/active-directory-saas-ie-group-policy/show-contents.png)
5. Fare clic su **OK** per applicare le modifiche e chiudere la finestra **Elenco componenti aggiuntivi**.

L'estensione dovrebbe ora essere abilitata per i computer nell'unità organizzativa selezionata. [Altre informazioni sull'uso di Criteri di gruppo per abilitare o disabilitare i componenti aggiuntivi di Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Passaggio 5 (facoltativo): disabilitare la richiesta "Memorizza password"
Quando gli utenti accedono a siti Web utilizzando l'estensione del Pannello di accesso, Internet Explorer potrebbe visualizzare la seguente richiesta "Si desidera memorizzare la password?"

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

Se si desidera impedire agli utenti la visualizzazione del messaggio, attenersi alla procedura seguente per impedire al completamento automatico di ricordare le password:

1. Nella finestra **Editor Gestione criteri di gruppo** , passare al percorso elencato di seguito. Questa impostazione di configurazione è disponibile solo in **Configurazione utente**.
   
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
2. Individuare l'impostazione denominata **Attiva Completamento automatico per nomi utente e password nei moduli**.
   
   > [!NOTE]
   > Le versioni precedenti di Active Directory possono elencare questa impostazione con il nome **Non consentire al completamento automatico di salvare le password**. La configurazione dell'impostazione differisce da quella descritta in questa esercitazione.
   > 
   > 
   
    ![Ricordarsi di controllarlo nelle Impostazioni utente.](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)
3. Fare clic con il pulsante destro sull'impostazione precedente e selezionare **Modifica**.
4. Nella finestra denominata **Attiva Completamento automatico per nomi utente e password nei moduli** selezionare **Disabilitato**.
   
    ![Selezionare Disabilita](./media/active-directory-saas-ie-group-policy/disable-passwords.png)
5. Fare clic su **OK** per applicare queste modifiche e chiudere la finestra.

Gli utenti non saranno più in grado di archiviare le credenziali o di utilizzare il completamento automatico per accedere alle credenziali archiviate in precedenza. Tuttavia, questo criterio consente agli utenti di continuare a utilizzare il completamento automatico per altri tipi di campi dei moduli, ad esempio i campi di ricerca.

> [!WARNING]
> Se questo criterio è abilitato dopo che gli utenti hanno scelto di memorizzare alcune credenziali, questo criterio *non* cancellerà le credenziali che sono già state archiviate.
> 
> 

## <a name="step-6-testing-the-deployment"></a>Passaggio 6: Test della distribuzione
Per verificare la corretta distribuzione dell'estensione, seguire questa procedura:

1. Se per la distribuzione è stata selezionata l'opzione **Configurazione computer**, accedere a un computer client appartenente all'unità organizzativa selezionata in [Passaggio 2: Creare l'oggetto Criteri di gruppo](#step-2-create-the-group-policy-object). Se invece è stata selezionata l'opzione **Configurazione utente**, assicurarsi di eseguire l'accesso con un nome utente appartenente all'unità organizzativa.
2. Potrebbero essere necessari più accessi prima che le modifiche a Criteri di gruppo risultino completamente aggiornate nel computer. Per forzare l'aggiornamento, aprire una finestra del **prompt dei comandi** ed eseguire il comando seguente: `gpupdate /force`
3. Per avviare l'installazione, è necessario riavviare il computer. L'avvio potrebbe impiegare molto più tempo del normale mentre è in corso l'installazione dell'estensione.
4. Dopo aver riavviato, aprire **Internet Explorer**. Nell'angolo in alto a destra della finestra fare clic su **Strumenti** (l'icona a forma di ingranaggio) e quindi selezionare **Gestione componenti aggiuntivi**.
   
    ![Passare a Strumenti > Gestione componenti aggiuntivi](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)
5. Nella finestra **Gestione componenti aggiuntivi** verificare che **Estensione Pannello di accesso** sia installata e che lo **Stato** sia impostato su **Abilitato**.
   
    ![Verificare che l'estensione Pannello di accesso sia installata e abilitata.](./media/active-directory-saas-ie-group-policy/verify-install.png)

## <a name="related-articles"></a>Articoli correlati
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Risoluzione dei problemi dell’estensione del pannello di accesso per Internet Explorer](active-directory-saas-ie-troubleshooting.md)


