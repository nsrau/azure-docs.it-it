---
title: 'Azure Active Directory Domain Services: aggiungere una macchina virtuale RHEL a un dominio gestito | Documentazione Microsoft'
description: Aggiungere una macchina virtuale Red Hat Enterprise Linux a Servizi di dominio Azure AD
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 87291c47-1280-43f8-8fb2-da1bd61a4942
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: e345d39259f44fdd78a0f041ce823051da80b27f


---
# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Aggiungere una macchina virtuale di Red Hat Enterprise Linux 7 a un dominio gestito
Questo articolo illustra come aggiungere una macchina virtuale di Red Hat Enterprise Linux (RHEL) 7 a un dominio gestito di Servizi di dominio Azure AD.

## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Eseguire il provisioning di una macchina virtuale di Red Hat Enterprise Linux
Eseguire questa procedura per effettuare il provisioning di una macchina virtuale RHEL 7 con il portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
   
    ![Dashboard del portale di Azure](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)
2. Fare clic su **Nuovo** nel riquadro a sinistra e digitare **Red Hat** nella barra di ricerca, come illustrato nello screenshot seguente. Nei risultati della ricerca vengono visualizzate le voci per Red Hat Enterprise Linux. Fare clic su **Red Hat Enterprise Linux 7.2**.
   
    ![Selezionare RHEL nei risultati](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)
3. I risultati della ricerca nel riquadro **Tutto** dovrebbero elencare l'immagine di Red Hat Enterprise Linux 7.2. Fare clic su **Red Hat Enterprise Linux 7.2** per visualizzare altre informazioni sull'immagine della macchina virtuale.
   
    ![Selezionare RHEL nei risultati](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)
4. Nel riquadro **Red Hat Enterprise Linux 7.2** dovrebbero essere visualizzate altre informazioni sull'immagine della macchina virtuale. Nell'elenco a discesa **Selezionare un modello di distribuzione** selezionare **Classica**. Quindi, fare clic sul pulsante **Crea** .
   
    ![Visualizzare i dettagli dell'immagine](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)
5. Nel riquadro **Crea macchina virtuale** immettere il **Nome host** per la nuova macchina virtuale. Specificare anche il nome utente di un amministratore locale nel campo **Nome utente** e la relativa **Password**. Si può anche scegliere di usare una chiave SSH per l'autenticazione dell'utente amministratore locale. Selezionare anche un **Piano tariffario** per la macchina virtuale.
   
    ![Creare una macchina virtuale: dettagli di base](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)
6. Fare clic su **Configurazione facoltativa**. Nel riquadro **Configurazione facoltativa** fare clic su **Rete**.
   
    ![Creare una macchina virtuale: configurare una rete virtuale](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-configure-vnet.png)
7. Verrà visualizzato un riquadro denominato **Rete**. Nel riquadro **Rete** fare clic su **Rete virtuale** per selezionare la rete virtuale in cui deve essere distribuita la macchina virtuale Linux. Verrà visualizzato il riquadro **Rete virtuale** . Nel riquadro **Rete virtuale** scegliere l'opzione **Usa una rete virtuale esistente**. Quindi selezionare la rete virtuale in cui è disponibile Servizi di dominio Azure AD. In questo esempio viene selezionata la rete virtuale 'MyPreviewVNet'.
   
    ![Creare una macchina virtuale: selezionare una rete virtuale](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)
8. Nel riquadro **Configurazione facoltativa** fare clic sul pulsante **OK**.
   
    ![Creare una macchina virtuale: rete virtuale selezionata](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)
9. A questo punto è possibile creare la macchina virtuale. Nel riquadro **Crea macchina virtuale** fare clic sul pulsante **Crea**.
   
    ![Creare una macchina virtuale: sistema pronto](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm.png)
10. Dovrebbe iniziare la distribuzione della nuova macchina virtuale basata sull'immagine di RHEL 7.2.
    
    ![Creare una macchina virtuale: distribuzione avviata](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)
11. Dopo alcuni minuti, la macchina virtuale dovrebbe essere distribuita correttamente e pronta per l'uso.
    
    ![Creare una macchina virtuale: distribuzione completata](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)

## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Connettersi in remoto alla macchina virtuale Linux di cui è stato appena effettuato il provisioning
Il provisioning della macchina virtuale RHEL 7.2 è stato eseguito in Azure. L'attività successiva prevede la connessione remota alla macchina virtuale.

**Connettersi alla macchina virtuale RHEL 7.2** Seguire le istruzioni contenute nell'articolo che illustra [come accedere a una macchina virtuale che esegue Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

I passaggi rimanenti presuppongono l'uso di PuTTY come client SSH per connettersi alla macchina virtuale RHEL. Per altre informazioni, vedere la [pagina di download di PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Aprire il programma PuTTY.
2. Immettere il **Nome host** per la macchina virtuale RHEL appena creata. In questo esempio il nome della macchina virtuale è 'contoso-rhel.cloudapp.net'. Se non si è certi del nome host della macchina virtuale, vedere il dashboard della macchina virtuale nel portale di Azure.
   
    ![Connessione PuTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)
3. Accedere alla macchina virtuale usando le credenziali di amministratore locale specificate durante la creazione della macchina virtuale. In questo esempio viene usato l'account di amministratore locale "mahesh".
   
    ![Accesso PuTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)

## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Installare i pacchetti necessari nella macchina virtuale Linux
Dopo la connessione alla macchina virtuale, l'attività successiva consiste nell'installare nella macchina virtuale i pacchetti necessari per l'aggiunta a un dominio. Eseguire la procedura seguente:

1. **Installare realmd:** il pacchetto realmd viene usato per l'aggiunta a un dominio. Nel terminale PuTTY digitare il comando seguente:
   
    sudo yum install realmd
   
    ![Installare realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)
   
    Dopo alcuni minuti, il pacchetto realmd dovrebbe essere installato nella macchina virtuale.
   
    ![realmd installato](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)
2. **Installare sssd:** il pacchetto realmd dipende da sssd per l'esecuzione di operazioni di aggiunta a un dominio. Nel terminale PuTTY digitare il comando seguente:
   
    sudo yum install sssd
   
    ![Installare sssd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)
   
    Dopo alcuni minuti, il pacchetto sssd dovrebbe essere installato nella macchina virtuale.
   
    ![realmd installato](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)
3. **Installare kerberos**. A tale scopo, nel terminale PuTTY digitare il comando seguente:
   
    sudo yum install krb5-workstation krb5-libs
   
    ![Installare kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)
   
    Dopo alcuni minuti, il pacchetto realmd dovrebbe essere installato nella macchina virtuale.
   
    ![Kerberos installato](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)

## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Aggiungere la macchina virtuale Linux al dominio gestito
Ora che i pacchetti sono installati nella macchina virtuale Linux, l'attività successiva consiste nell'aggiungere la macchina virtuale al dominio gestito.

1. Individuare il dominio gestito di Servizi di dominio AAD. Nel terminale PuTTY digitare il comando seguente:
   
    sudo realm discover CONTOSO100.COM
   
    ![realm discover](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)
   
    Se **realm discover** non riesce a trovare il dominio gestito, verificare che il dominio sia raggiungibile dalla macchina virtuale (provare a eseguire il ping). Verificare anche che la macchina virtuale sia stata effettivamente distribuita nella stessa rete virtuale in cui è disponibile il dominio gestito.
2. Inizializzare kerberos. Nel terminale PuTTY digitare il comando seguente. Verificare che l'utente specificato appartenga al gruppo AAD DC Administrators. Solo questi utenti possono aggiungere computer al dominio gestito.
   
    kinit bob@CONTOSO100.COM
   
    ![kinit ](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)
   
    Verificare che il nome di dominio sia scritto in lettere maiuscole; in caso contrario kinit avrà esito negativo.
3. Aggiungere il computer al dominio. Nel terminale PuTTY digitare il comando seguente. Specificare lo stesso utente "kinit" specificato nel passaggio precedente.
   
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
   
    ![Aggiunta dell'area di autenticazione](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

Quando il computer viene aggiunto correttamente al dominio gestito, dovrebbe essere visualizzato un messaggio che indica che il computer è stato registrato correttamente nell'area di autenticazione.

## <a name="verify-domain-join"></a>Verificare l'aggiunta a un dominio
È possibile verificare rapidamente se il computer è stato aggiunto correttamente al dominio gestito. Connettersi alla macchina virtuale RHEL appena aggiunta a un dominio usando SSH e un account utente di dominio e quindi verificando se l'account utente viene risolto correttamente.

1. Nel terminale PuTTY digitare il comando seguente per connettere la macchina virtuale RHEL appena aggiunta a un dominio con SSH. Usare un account di dominio appartenente al dominio gestito, in questo caso 'bob@CONTOSO100.COM'.
   
    ssh -l bob@CONTOSO100.COM contoso-rhel.cloudapp.net
2. Nel terminale PuTTY digitare il comando seguente per vedere se la home directory dell'utente è stata inizializzata correttamente.
   
    pwd
3. Nel terminale PuTTY digitare il comando seguente per vedere se i membri del gruppo sono stati risolti correttamente.
   
    id

Di seguito è riportato un esempio dell'output di questi comandi:

![Verificare l'aggiunta a un dominio](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)

## <a name="troubleshooting-domain-join"></a>Risoluzione dei problemi di aggiunta al dominio
Vedere l'articolo [Risoluzione dei problemi dell'aggiunta a un dominio](active-directory-ds-admin-guide-join-windows-vm.md#troubleshooting-domain-join) .

## <a name="related-content"></a>Contenuti correlati
* [Guida introduttiva di Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Aggiungere una macchina virtuale Windows Server a un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Come accedere a una macchina virtuale che esegue Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Installazione di Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 - Windows Integration Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)




<!--HONumber=Dec16_HO4-->


