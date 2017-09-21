---
title: Risoluzione dei problemi di Azure Site Recovery da VMware ad Azure | Microsoft Docs
description: Correggere gli errori durante la replica di macchine virtuali di Azure
services: site-recovery
documentationcenter: 
author: asgang
manager: srinathv
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/24/2017
ms.author: asgang
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: d4d6d273001642ebb8c960333a1c01977e863dbc
ms.contentlocale: it-it
ms.lasthandoff: 09/07/2017

---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Risolvere i problemi di installazione push del servizio Mobility

Questo articolo descrive i problemi comuni che possono verificarsi quando si prova a installare il servizio Mobility di Azure Site Recovery nel server di origine per abilitare la protezione.

## <a name="error-code-95107-protection-could-not-be-enabled"></a>(Codice errore 95107) Non è stato possibile abilitare la protezione
**Codice errore** | **Possibili cause** | **Indicazioni specifiche per l'errore**
--- | --- | ---
95107 </br>**Messaggio:** Push installation of the mobility service to the source machine failed with error code **EP0858**. <br> Either the credentials provided to install the Mobility Service are incorrect or the user account has insufficient privileges (L'installazione push del servizio Mobility nel computer di origine non è riuscita con codice errore EP0858. Le credenziali specificate per installare il servizio Mobility non sono corrette oppure l'account utente ha privilegi insufficienti). | Le credenziali utente specificate per installare il servizio Mobility nel computer di origine non sono corrette. | Verificare che le credenziali utente specificate per il computer di origine nel server di configurazione siano corrette. <br> Per aggiungere o modificare credenziali utente, passare al server di configurazione e selezionare **Cspsconfigtool** > **Gestisci account**. </br> Verificare inoltre i prerequisiti seguenti per completare correttamente l'installazione push.

## <a name="error-code-95015-protection-could-not-be-enabled"></a>(Codice errore 95015) Non è stato possibile abilitare la protezione
**Codice errore** | **Possibili cause** | **Indicazioni specifiche per l'errore**
--- | --- | ---
95105 </br>**Messaggio:** Push installation of the mobility service to the source machine failed with error code **EP0856**. <br> Either **File and Printer Sharing** isn't allowed on the source machine or there are network connectivity problems between the process server and the source machine (L'installazione push del servizio Mobility nel computer di origine non è riuscita con codice errore EP0856. Condivisione file e stampanti non è consentito nel computer di origine oppure ci sono problemi di connettività tra il server di elaborazione e il computer di origine).| La funzionalità **Condivisione file e stampanti** non è abilitata. | Consentire **Condivisione file e stampanti** nel computer di origine in Windows Firewall. Nel computer di origine in **Windows Firewall** > **Consenti app o funzionalità attraverso Windows Firewall** selezionare **Condivisione file e stampanti** per tutti i profili. </br> Verificare inoltre i prerequisiti seguenti per completare correttamente l'installazione push.

## <a name="error-code-95117-protection-could-not-be-enabled"></a>(Codice errore 95117) Non è stato possibile abilitare la protezione
**Codice errore** | **Possibili cause** | **Indicazioni specifiche per l'errore**
--- | --- | ---
95117 </br>**Messaggio:** Push installation of the Mobility Service to the source machine failed with error code **EP0865**. <br> Either the source machine isn't running or there are network connectivity problems between the process server and the source machine (L'installazione push del servizio Mobility nel computer di origine non è riuscita con codice errore EP0865. Il computer di origine non è in esecuzione oppure ci sono problemi di connettività di rete tra il server di elaborazione e il computer di origine). | Problemi di connettività di rete tra il server di elaborazione e il server di origine. | Controllare la connettività tra il server di elaborazione e il server di origine. </br> Verificare inoltre i prerequisiti seguenti per completare correttamente l'installazione push.

## <a name="error-code-95103-protection-could-not-be-enabled"></a>(Codice errore 95103) Non è stato possibile abilitare la protezione
**Codice errore** | **Possibili cause** | **Indicazioni specifiche per l'errore**
--- | --- | ---
95103 </br>**Messaggio:** Push installation of the Mobility Service to the source machine failed with error code **EP0854**. <br> Either Windows Management Instrumentation (WMI) isn't allowed on the source machine or there are network connectivity problems between the process server and the source machine (L'installazione push del servizio Mobility nel computer di origine non è riuscita con codice errore EP0854. La funzionalità Strumentazione gestione Windows non è consentita nel computer di origine oppure ci sono problemi di connettività tra il server di elaborazione e il computer di origine).| La funzionalità Strumentazione gestione Windows è bloccata in Windows Firewall. | Consentire Strumentazione gestione Windows in Windows Firewall. In **Windows Firewall** > **Consenti app o funzionalità attraverso Windows Firewall** selezionare **Strumentazione gestione Windows** per tutti i profili. </br> Verificare inoltre i prerequisiti seguenti per completare correttamente l'installazione push.

## <a name="check-push-installation-logs-for-errors"></a>Verificare la presenza di eventuali errori nei log di installazione push

Nel server di configurazione/elaborazione passare al file PushinstallService, che si trova in <Microsoft Azure Site Recovery Install Location>\home\svsystems\pushinstallsvc\, per individuare l'origine del problema. Per risolvere il problema, seguire la procedura di risoluzione dei problemi seguente.</br>

![Log di installazione push](./media/site-recovery-protection-common-errors/pushinstalllogs.png)

## <a name="push-installation-prerequisites-for-windows"></a>Prerequisiti di installazione push per Windows
### <a name="ensure-that-file-and-printer-sharing-is-enabled"></a>Verificare che **Condivisione file e stampanti** sia abilitato
Consentire **Condivisione file e stampanti** e **Strumentazione gestione Windows** nel computer di origine in Windows Firewall. </br>
#### <a name="if-the-source-machine-is-domain-joined-br"></a>Se il computer di origine è aggiunto a un dominio </br>
Configurare le impostazioni del firewall mediante Console Gestione Criteri di gruppo (GPMC).

1. Accedere al computer di dominio Azure Active Directory come amministratore. Aprire Console Gestione Criteri di gruppo (GPMC.MSC, eseguibile da **Start** > **Esegui**).</br>

2. Se lo strumento Console Gestione Criteri di gruppo non è installato, vedere [installare Console Gestione Criteri di gruppo](https://technet.microsoft.com/library/cc725932.aspx). </br>

3. Nell'albero di Console Gestione Criteri di gruppo fare doppio clic su **Oggetti Criteri di gruppo** nella foresta e nel dominio. Passare a **Criterio dominio predefinito**. </br>

    ![Criterio dominio predefinito](./media/site-recovery-protection-common-errors/gpmc1.png) </br>
</br>
4. Fare clic con il pulsante destro del mouse su **Criterio dominio predefinito** >  e quindi scegliere **Modifica**. Viene visualizzata una nuova finestra **Editor Gestione Criteri di gruppo**. </br>

    ![Editor Gestione Criteri di gruppo](./media/site-recovery-protection-common-errors/gpmc2.png) </br>
</br>
5. In **Editor Gestione Criteri di gruppo** passare a **Configurazione computer** > **Criteri** > **Modelli amministrativi** > **Rete** > **Connessioni di rete** > **Windows Firewall**. </br>

    ![Windows Firewall](./media/site-recovery-protection-common-errors/gpmc3.png) </br>
</br>
6. Abilitare le impostazioni seguenti per **Profilo di dominio** e **Profilo standard**: </br>

    a. Fare doppio clic su **Windows Firewall: consenti eccezione per condivisione file e stampanti in ingresso**. Selezionare **Abilitato** e quindi fare clic su **OK**. </br>

    b. Fare doppio clic su **Windows Firewall: consenti eccezione amministrazione remota in ingresso**. Selezionare **Abilitato** e quindi fare clic su **OK**. </br>

    ![Profilo di dominio](./media/site-recovery-protection-common-errors/gpmc4.png) </br>
</br>

#### <a name="if-the-source-machine-isnt-domain-joined-and-part-of-a-workgroup-br"></a>Se il computer di origine non è aggiunto a un dominio e non fa parte del gruppo di lavoro </br>
Configurare le impostazioni del firewall in un computer remoto (per un gruppo di lavoro).

1. Passare al computer di origine.</br>

2. In **Windows Firewall** > **Consenti app o funzionalità attraverso Windows Firewall** selezionare **Condivisione file e stampanti** per tutti i profili. </br>

3. In **Windows Firewall** > **Consenti app o funzionalità attraverso Windows Firewall** selezionare **Strumentazione gestione Windows** per tutti i profili. </br>

#### <a name="disable-remote-user-account-control"></a>Disabilitare il controllo dell'account utente remoto
Disabilitare la funzionalità Controllo dell'account utente usando la chiave del Registro di sistema per eseguire il push del servizio Mobility.

1. Selezionare **Start** > **Esegui**, digitare *regedit* e premere **INVIO**.

2. Individuare e quindi selezionare questa sottochiave del Registro di sistema: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System

3. Se la voce LocalAccountTokenFilterPolicy del Registro di sistema non esiste, seguire questi passaggi:

    a. Scegliere **Nuovo** dal menu **Modifica** e quindi selezionare **Valore DWORD**.

    b. Digitare *LocalAccountTokenFilterPolicy* e quindi premere **INVIO**.

    c. Fare clic con il pulsante destro del mouse su **LocalAccountTokenFilterPolicy** e quindi scegliere **Modifica**.

    d. Nella casella dati **Valore** digitare *1* e quindi selezionare **OK**.

4. Chiudere l'editor del Registro di sistema.


## <a name="push-installation-prerequisites-for-linux"></a>Prerequisiti di installazione push per Linux

1. Creare un utente root nel server Linux di origine. Usare questo account solo per l'installazione push e gli aggiornamenti.</br>

2. Assicurarsi che il file /etc/hosts nel server Linux di origine contenga le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete. </br>

3. Verificare che nel server di origine Linux siano installati i pacchetti openssh, openssh-server e openssl più recenti. Controllare che la porta 22 SSH sia abilitata e in esecuzione. </br>

4. Controllare che le voci di agenti non aggiornate siano già presenti nel server di origine, disinstallare gli agenti meno recenti e riavviare il server. Reinstallare l'agente. </br>

#### <a name="enable-sftp-subsystem-and-password-authentication-in-the-sshdconfig-file"></a>Abilitare il sottosistema SFTP e l'autenticazione della password nel file sshd_config
1. Nel server di origine accedere come utente ROOT. </br>

2. Nel file /etc/ssh/sshd_config individuare la riga che inizia con "PasswordAuthentication". </br>

3. Rimuovere il commento dalla riga e modificare il valore da "no" a "yes". </br>

    ![PasswordAuthentication](./media/site-recovery-protection-common-errors/linux1.png)

4. Individuare la riga che inizia con "Subsystem" e rimuovere il commento. </br>

    ![Subsystem](./media/site-recovery-protection-common-errors/linux2.png)

5. Salvare le modifiche e riavviare il servizio sshd. </br>


## <a name="push-installation-checks-on-the-configurationprocess-server"></a>Controlli dell'installazione push nel server di configurazione/elaborazione
#### <a name="validate-credentials-for-discovery-and-installation"></a>Convalidare le credenziali per l'individuazione e l'installazione

1. Nel server di configurazione avviare Cspsconfigtool.</br>

    ![WMItestConnect5](./media/site-recovery-protection-common-errors/wmitestconnect5.png) </br>

2. Assicurarsi che l'account usato per la protezione disponga dei diritti di amministratore nel computer di origine. </br>

#### <a name="check-connectivity-between-the-process-server-and-the-source-server"></a>Controllare la connettività tra il server di elaborazione e il server di origine
1. Assicurarsi che il server di elaborazione abbia una connessione Internet.

2. Verificare la connessione WMI tramite wbemtest.exe. </br>

    a. Nel server di elaborazione selezionare **Start** > **Esegui** > **wbemtest.exe**. Viene visualizzata la finestra **Tester di Strumentazione gestione Windows**, mostrata di seguito:</br>

      ![Tester di Strumentazione gestione Windows](./media/site-recovery-protection-common-errors/wmitestconnect1.png) </br>
   </br>
    b. Selezionare **Connetti** e immettere l'IP del server di origine in **Spazio dei nomi**.

    c. Specificare **Utente** e **Password**. Se il computer di origine è aggiunto a un dominio, specificare il nome di dominio insieme al nome utente nel formato *NomeDominio\nomeutente*. Se il computer di origine si trova in un gruppo di lavoro, specificare solo il nome utente.

    d. Selezionare il livello di **Autenticazione** come **Privacy pacchetto**. </br>

      ![Privacy pacchetto](./media/site-recovery-protection-common-errors/wmitestconnect2.png) </br>
   </br>
    e. Selezionare **Connessione**. A questo punto la connessione WMI mostra i dati specificati e viene visualizzata la finestra **Tester di Strumentazione gestione Windows**, illustrata di seguito: </br>

      ![Dati in Tester di Strumentazione gestione Windows](./media/site-recovery-protection-common-errors/wmitestconnect3.png) </br>
   </br>
      Se la connessione WMI non riesce, viene visualizzato un messaggio di errore. Lo screenshot seguente mostra un tentativo non riuscito se nell'app consentita da Windows Firewall non è abilitata l'opzione **Strumentazione gestione Windows/Amministrazione remota**: </br>

    ![Messaggio di errore di Tester di Strumentazione gestione Windows](./media/site-recovery-protection-common-errors/wmitestconnect4.png) </br>
   </br>

3. Verificare la stato WMI e la connettività.</br>

    a. Nel server di configurazione/elaborazione selezionare **Start** > **Esegui** > **wmimgmt.msc** > **Azioni**  >  **Altre azioni** > **Connetti a un altro computer (computer di origine)**. </br>

    b. Immettere le credenziali dell'account usato per la protezione e controllare la connettività. </br>

#### <a name="verify-that-network-shared-folders-of-the-source-machine-are-accessible-from-the-process-server-remotely-by-using-specified-credentials"></a>Verificare che le cartelle condivise di rete del computer di origine siano accessibili dal server di elaborazione in remoto usando le credenziali specificate

  1. Accedere al computer server di elaborazione e aprire Esplora file. Nella barra degli indirizzi digitare *"\\\ip-computer-origine\C$"*. Selezionare **Enter** (Invio). </br>

      ![Esplora file](./media/site-recovery-protection-common-errors/fileshare1.png) </br>

  2. Esplora file chiede le credenziali. Immettere il nome utente e la password e scegliere **OK**.</br>

      * Se il computer di origine è aggiunto a un dominio, immettere il nome di dominio insieme al nome utente nel formato *NomeDominio\nomeutente*.</br>

      * Se il computer di origine si trova in gruppo di lavoro, immettere solo il nome utente. </br>

      ![Finestra di dialogo Credenziali](./media/site-recovery-protection-common-errors/fileshare2.png) </br>

  3. Se la connessione riesce, è possibile visualizzare le cartelle del computer di origine in remoto dal server di elaborazione. </br>

      ![Visualizzazione cartelle](./media/site-recovery-protection-common-errors/fileshare3.png) </br>

> [!NOTE] 
> Se la connessione non riesce, verificare che siano soddisfatti tutti i prerequisiti.
>

Se non si vuole aprire **Strumentazione gestione Windows**, è anche possibile installare il servizio Mobility manualmente nel computer di origine.</br> 

Per altre informazioni, vedere [Installare manualmente il servizio Mobility tramite la GUI](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) e [installazione tramite linee guida di Configuration Manager](site-recovery-install-mobility-service-using-sccm.md). </br>

## <a name="next-steps"></a>Passaggi successivi
- [Abilitare la replica delle macchine virtuali VMware](vmware-walkthrough-enable-replication.md)

