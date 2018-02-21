---
title: 'Servizi di dominio Azure Active Directory: amministrare un dominio gestito | Documentazione Microsoft'
description: Amministrare i domini gestiti di Servizi di dominio Azure Active Directory
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4fdbc75-3e6b-4e20-8494-5dcc3bf2220a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: maheshu
ms.openlocfilehash: 7ffe3e54b891fd74e14666c136be49132d78590b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Amministrare un dominio gestito di Servizi di dominio Azure Active Directory
Questo articolo illustra come amministrare un dominio gestito di Servizi di dominio Azure Active Directory (AD).

## <a name="before-you-begin"></a>Prima di iniziare
Per eseguire le attività elencate in questo articolo sono necessari gli elementi seguenti:

1. Una **sottoscrizione di Azure**valida.
2. Una **directory di Azure AD** sincronizzata con una directory locale o con una directory solo cloud.
3. **Servizi di dominio Azure AD** devono essere abilitati per la directory di Azure AD. Se non è stato fatto, eseguire tutte le attività descritte nella [guida introduttiva](active-directory-ds-getting-started.md).
4. Una **macchina virtuale aggiunta al dominio** da cui si amministrerà il dominio gestito di Servizi di dominio Azure AD. Se non si dispone di questo tipo di macchina virtuale, seguire tutte le attività descritte nell'articolo [Aggiungere una macchina virtuale Windows Server a un dominio gestito](active-directory-ds-admin-guide-join-windows-vm.md).
5. È necessario disporre delle credenziali di un **account utente appartenente al gruppo 'AAD DC Administrators'** nella directory per poter amministrare il dominio gestito.

<br>

## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Attività amministrative che è possibile eseguire in un dominio gestito
Ai membri del gruppo "AAD DC Administrators" vengono concessi privilegi nel dominio gestito che consentono di eseguire attività quali:

* Aggiungere computer al dominio gestito.
* Configurare l'oggetto Criteri di gruppo predefinito per i contenitori "AADDC Computers" e "AADDC Users" nel dominio gestito.
* Amministrare DNS nel dominio gestito.
* Creare e amministrare unità organizzative (OU) personalizzate nel dominio gestito.
* Ottenere l'accesso amministrativo ai computer aggiunti al dominio gestito.

## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>Privilegi amministrativi non disponibili in un dominio gestito
Il dominio viene gestito da Microsoft, incluse le attività quali applicazione di patch, monitoraggio ed esecuzione di backup. Il dominio è quindi bloccato e non si hanno privilegi per eseguire alcune attività amministrative nel dominio. Ecco alcuni esempi di attività che non è possibile eseguire.

* Non vengono concessi privilegi di amministratore di dominio o amministratore dell'organizzazione per il dominio gestito.
* Non è possibile estendere lo schema del dominio gestito.
* Non è possibile connettersi ai controller di dominio per il dominio gestito usando Desktop remoto.
* Non è possibile aggiungere controller di dominio al dominio gestito.

## <a name="task-1---provision-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>Attività 1: Eseguire il provisioning di una macchina virtuale Windows Server aggiunta a un dominio per amministrare in remoto il dominio gestito
I domini gestiti di Servizi di dominio Azure AD possono essere gestiti con i familiari strumenti di amministrazione di Active Directory, ad esempio il Centro di amministrazione di Active Directory o AD PowerShell. Gli amministratori tenant non hanno i privilegi necessari per connettersi ai controller di dominio nel dominio gestito con Desktop remoto. Di conseguenza, i membri del gruppo "AAD DC Administrators" possono amministrare i domini gestiti in remoto usando gli strumenti di amministrazione di AD da un computer client o Windows Server aggiunto al dominio gestito. Gli strumenti di amministrazione di AD possono essere installati come parte della funzionalità facoltativa Strumenti di amministrazione remota del server in Windows Server e nei computer client aggiunti al dominio gestito.

Il primo passaggio consiste nella configurazione di una macchina virtuale Windows Server aggiunta al dominio gestito. Per istruzioni, vedere l'articolo [Aggiungere una macchina virtuale Windows Server a un dominio gestito](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Amministrare in remoto il dominio gestito da un computer client, ad esempio Windows 10
Le istruzioni in questo articolo usano una macchina virtuale Windows Server per amministrare il dominio gestito da Servizi di dominio Azure AD. A questo scopo è tuttavia anche possibile scegliere di usare una macchina virtuale client di Windows, ad esempio Windows 10.

È possibile [installare gli Strumenti di amministrazione remota del server](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) in una macchina virtuale client Windows seguendo le istruzioni in TechNet.

## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>Attività 2: Installare gli strumenti di amministrazione di Active Directory nella macchina virtuale
Eseguire questa procedura per installare gli strumenti di amministrazione di Active Directory nella macchina virtuale aggiunta al dominio. Per altre [informazioni sull'installazione e l'utilizzo degli strumenti di amministrazione remota del server](https://technet.microsoft.com/library/hh831501.aspx), vedere Technet.

1. Passare al portale di Azure. Fare clic su **Tutte le risorse** nel pannello a sinistra. Individuare e fare clic sulla macchina virtuale creata nell'attività 1.
2. Fare clic su sul pulsante **Connetti** nella scheda Panoramica. Viene creato e scaricato un file Remote Desktop Protocol con estensione rdp.

    ![Connettersi alla macchina virtuale Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Per connettersi alla VM, aprire il file RDP scaricato. Se richiesto, fare clic su **Connetti**. Al prompt di accesso usare le credenziali di un utente appartenente al gruppo "AAD DC Administrators". In questo caso ad esempio, si usa 'bob@domainservicespreview.onmicrosoft.com'. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Fare clic su Sì o Continua per procedere con la connessione.
4. Dalla schermata Start aprire **Server Manager**. Fare clic su **Aggiungi ruoli e funzionalità** nel riquadro centrale della finestra di Server Manager.

    ![Avviare Server Manager nella macchina virtuale](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Nella pagina **Prima di iniziare** dell'**aggiunta guidata ruoli e funzionalità** fare clic su **Avanti**.

    ![Pagina Prima di iniziare](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Nella pagina **Tipo di installazione** lasciare selezionata l'opzione **Installazione basata su ruoli o basata su funzionalità** e fare clic su **Avanti**.

    ![Pagina Tipo di installazione](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Nella pagina **Selezione server** selezionare la macchina virtuale corrente dal pool di server e fare clic su **Avanti**.

    ![Pagina Selezione dei server](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Nella pagina **Ruoli del server** fare clic su **Avanti**. Questa pagina verrà ignorata perché non si stanno installando ruoli nel server.
9. Nella pagina **Funzionalità** fare clic per espandere il nodo **Strumenti di amministrazione remota del server**, quindi fare clic per espandere il nodo **Strumenti di amministrazione ruoli**. Selezionare la funzionalità **Strumenti per Servizi di dominio Active Directory e AD LDS** dall'elenco di strumenti di amministrazione ruoli.

    ![Pagina Funzionalità](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)
10. Nella pagina **Conferma** fare clic su **Installa** per installare la funzionalità Strumenti per Servizi di dominio Active Directory e AD LDS nella macchina virtuale. Dopo aver completato correttamente l'installazione della funzionalità, fare clic su **Chiudi** per uscire dall'**Aggiunta guidata ruoli e funzionalità**.

    ![Pagina di conferma](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)

## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>Attività 3: Eseguire la connessione ed esplorare il dominio gestito
Dopo aver installato gli strumenti di amministrazione di AD nella macchina virtuale aggiunta al dominio sarà possibile usare questi strumenti per amministrare il dominio gestito.

> [!NOTE]
> È necessario essere un membro del gruppo 'AAD DC Administrators' per poter amministrare il dominio gestito.
>
>

1. Dalla schermata Start fare clic su **Strumenti di amministrazione**. Gli strumenti di amministrazione di AD risulteranno installati nella macchina virtuale.

    ![Strumenti di amministrazione installati nel server](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Fare clic su **Centro di amministrazione di Active Directory**.

    ![Centro di amministrazione di Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Per esplorare il dominio, fare clic sul nome di dominio nel riquadro a sinistra, ad esempio 'contoso100.com'. Si noti che due contenitori si chiamano rispettivamente "AADDC Computers" e "AADDC Users".

    ![Centro di amministrazione di Active Directory - Visualizza dominio](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)
4. Fare clic sul contenitore denominato **AADDC Users** per visualizzare tutti gli utenti e i gruppi appartenenti al dominio gestito. In questo contenitore verranno visualizzati gli account utente e gruppi del tenant Azure Active Directory. Si noti che, in questo esempio, nel contenitore sono disponibili un account utente per l'utente 'bob' e un gruppo denominato 'AAD DC Administrators'.

    ![Centro di amministrazione di Active Directory - Utenti del dominio](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)
5. Fare clic sul contenitore denominato **AADDC Computers** per visualizzare i computer aggiunti al dominio gestito. Verrà visualizzata una voce per la macchina virtuale corrente aggiunta al dominio. In questo contenitore 'AADDC computers' saranno visualizzati tutti gli account computer per tutti i computer aggiunti al dominio gestito di Servizi di dominio Azure AD.

    ![Centro di amministrazione di Active Directory - Computer aggiunti al dominio](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Contenuti correlati
* [Guida introduttiva di Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Aggiungere una macchina virtuale Windows Server a un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Distribuire strumenti di amministrazione remota del server](https://technet.microsoft.com/library/hh831501.aspx)
