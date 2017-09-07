---
title: Risoluzione dei problemi di Azure Site Recovery da VMware ad Azure | Microsoft Docs
description: Risoluzione degli errori durante la replica di macchine virtuali di Azure
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
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 1e2452ccc6d3f1859a39e1ee09cdde32f53767ba
ms.contentlocale: it-it
ms.lasthandoff: 08/28/2017

---
# <a name="troubleshooting-mobility-service-push-install-issues"></a>Risoluzione dei problemi di installazione push del servizio Mobility

Questo articolo descrive in dettaglio i problemi comuni che si verificano durante l'installazione del servizio Mobility nel server di origine per l'abilitazione della protezione.

## <a name="error-code-95107-protection-could-not-be-enabled"></a>(Codice di errore 95107) Non è stato possibile abilitare la protezione.
**Codice errore** | **Possibili cause** | **Indicazioni specifiche per l'errore**
--- | --- | ---
95107 </br>***Messaggio:*** Push installation of the mobility service to the source machine failed with error code ***EP0858*** (L'installazione Push del servizio di mobilità nella macchina virtuale di origine non è riuscita con codice errore EP0858). <br> Either that the credentials provided to install mobility service is incorrect or the user account has insufficient privileges (Le credenziali specificate per installare il servizio di mobilità non sono corrette oppure l'account utente non dispone di privilegi sufficienti) | Le credenziali utente specificate per installare il servizio di mobilità nel computer di origine non sono corrette | Verificare le credenziali utente specificate per il computer di origine nel server di configurazione siano corrette. <br> Per aggiungere o modificare le credenziali dell'utente passare al server di configurazione > icona Cspsconfigtool > Gestisci account. </br> Assicurarsi anche di selezionare i prerequisiti indicati di seguito per completare correttamente l'installazione push.

## <a name="error-code-95015-protection-could-not-be-enabled"></a>(Codice di errore 95015) Non è stato possibile abilitare la protezione.
**Codice errore** | **Possibili cause** | **Indicazioni specifiche per l'errore**
--- | --- | ---
95105 </br>***Messaggio:*** Push installation of the mobility service to the source machine failed with error code ***EP0856*** (L'installazione Push del servizio di mobilità nella macchina virtuale di origine non è riuscita con codice errore EP0856). <br> Either “File and Printer Sharing” not allowed on the source machine, or there are network connectivity issues between the process server and the source machine ("Condivisione file e stampanti" non è consentito nel computer di origine oppure ci sono problemi di connettività di rete tra il server di elaborazione e il computer di origine)| Condivisione file e stampanti non è abilitato | Consentire "Condivisione file e stampanti" sul computer di origine in Windows Firewall, passare al computer di origine > nelle impostazioni di Windows Firewall > "Consenti app o funzionalità attraverso Firewall" > selezionare "Condivisione file e stampanti per tutti i profili". </br> Assicurarsi anche di selezionare i prerequisiti indicati di seguito per completare correttamente l'installazione push.

## <a name="error-code-95117-protection-could-not-be-enabled"></a>(Codice di errore 95117) Non è stato possibile abilitare la protezione.
**Codice errore** | **Possibili cause** | **Indicazioni specifiche per l'errore**
--- | --- | ---
95117 </br>***Messaggio:*** Push installation of the mobility service to the source machine failed with error code ***EP0865*** (L'installazione Push del servizio di mobilità nella macchina virtuale di origine non è riuscita con codice errore EP0865). <br> Either the source machine is not running, or there are network connectivity issues between the process server and the source machine (Il computer di origine non è in esecuzione oppure ci sono problemi di connettività di rete tra il server di elaborazione e il computer di origine) | Connettività di rete tra i server di elaborazione e il server di origine | Controllare la connettività di rete tra i server di elaborazione e il server di origine. </br> Assicurarsi anche di selezionare i prerequisiti indicati di seguito per completare correttamente l'installazione push.

## <a name="error-code-95103-protection-could-not-be-enabled"></a>(Codice di errore 95103) Non è stato possibile abilitare la protezione.
**Codice errore** | **Possibili cause** | **Indicazioni specifiche per l'errore**
--- | --- | ---
95103 </br>***Messaggio:*** Push installation of the mobility service to the source machine failed with error code ***EP0854*** (L'installazione Push del servizio di mobilità nella macchina virtuale di origine non è riuscita con codice errore EP0854). <br> Either “Windows Management Instrumentation (WMI)” is not allowed on the source machine, or there are network connectivity issues between the process server and the source machine ("Strumentazione gestione Windows (WMI)" non è consentito nel computer di origine oppure ci sono problemi di connettività di rete tra il server di elaborazione e il computer di origine)| Strumentazione gestione Windows (WMI) è bloccato in Windows Firewall | Consentire Strumentazione gestione Windows (WMI) in Windows Firewall. In Impostazioni di Windows Firewall > "Consenti app o funzionalità attraverso Windows Firewall" > "selezionare WMI per tutti i profili". </br> Assicurarsi anche di selezionare i prerequisiti indicati di seguito per completare correttamente l'installazione push.

## <a name="check-push-install-logs-for-errors"></a>Controllare gli errori nei registri di installazione push

Nel server di configurazione o del processo, passare al file 'PushinstallService' che si trova in <Microsoft Azure Site Recovery Install Location>\home\svsystems\pushinstallsvc\ per scoprire l'origine del problema e usare sotto la procedura di risoluzione dei problemi per risolvere il problema.</br>
![pushiinstalllogs](./media/site-recovery-protection-common-errors/pushinstalllogs.png)

## <a name="push-install-pre-requisites-for-windows"></a>Prerequisiti per l'installazione push per Windows
### <a name="ensure-file-and-printer-sharing-is-enabled"></a>Verificare che "Condivisione file e stampanti" sia abilitato
Consentire "Condivisione file e stampanti" e "Strumentazione gestione Windows" sul computer di origine in Windows Firewall </br>
#### <a name="if-source-machine-is-domain-joined-br"></a>Se il computer di origine è aggiunto a un dominio: </br>
Configurare le impostazioni del firewall mediante la Console Gestione Criteri di gruppo (GPMC).
1. Accedere al computer di dominio di Active Directory come amministratore e aprire Console Gestione Criteri di gruppo, ovvero GPMC.MSC, eseguire da un Avvio > Esegui.</br>
3. Se la Console Gestione Criteri di gruppo non è installata, selezionare il collegamento per [installare la Console Gestione Criteri di gruppo](https://technet.microsoft.com/library/cc725932.aspx) </br>
4. Nell'albero della Console Gestione Criteri di gruppo fare doppio clic su Oggetti Criteri di gruppo nella foresta e nel dominio e passare alla "Default Domain Policy" (Criterio di dominio predefinito). </br>
![gpmc1](./media/site-recovery-protection-common-errors/gpmc1.png) </br>
</br>
5. Fare clic con il pulsante destro del mouse su "Default Domain Policy" (Criterio di dominio predefinito) > Modifica > verrà aperta una nuova finestra "Editor Gestione Criteri di gruppo ". </br>
![gpmc2](./media/site-recovery-protection-common-errors/gpmc2.png) </br>
</br>
6. Nell'Editor Gestione Criteri di gruppo passare a Configurazione computer > Criteri > Modelli amministrativi > Rete > Connessioni di rete > Windows Firewall. </br>
![gpmc3](./media/site-recovery-protection-common-errors/gpmc3.png) </br>
</br>
7. Abilitare le impostazioni seguenti per il profilo di dominio e il profilo standard </br>
a) Fare doppio clic sull'eccezione "Windows Firewall: consenti eccezione per condivisione file e stampanti in ingresso". Selezionare Abilitato e fare clic su OK. </br>
a) Fare doppio clic sull'eccezione "Windows Firewall: consenti eccezione amministrazione remota in ingresso". Selezionare Abilitato e fare clic su OK. </br>
![gpmc4](./media/site-recovery-protection-common-errors/gpmc4.png) </br>
</br>

###### <a name="if-source-machine-is-not-domain-joined-and-part-of-workgroup-br"></a>Se il computer di origine non è aggiunto a un dominio e non fa parte del gruppo di lavoro </br>
Configurare le impostazioni del firewall nel computer remoto per il gruppo di lavoro:
1. Passare al computer di origine,</br>
2. in Impostazioni di Windows Firewall > "Consenti app o funzionalità attraverso Windows Firewall" > selezionare "Condivisione file e stampanti per tutti i profili". </br>
3. In Impostazioni di Windows Firewall > "Consenti app o funzionalità attraverso Windows Firewall" > "selezionare WMI per tutti i profili". </br>

#### <a name="disable-remote-user-account-control-uac"></a>Disabilitare il controllo dell'account utente remoto, ovvero UAC
Disabilitare il Controllo dell'account utente con la chiave del registro per effettuare il push del servizio di mobilità.
1. Fare clic su Avvia > Esegui > digitare regedit > INVIO
2. Individuare e selezionare la seguente sottochiave del registro: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
3. Se la voce del registro LocalAccountTokenFilterPolicy non esiste, seguire questa procedura:
4. Dal menu Modifica > Nuovo > fare clic su valore DWORD.
5. Digitare LocalAccountTokenFilterPolicy e quindi premere INVIO.
6. Fare clic con il pulsante destro del mouse su LocalAccountTokenFilterPolicy e quindi fare clic su Modifica.
7. Nella casella dati Valore digitare 1 e quindi fare clic su OK.
8. Uscire da Editor del Registro di sistema.


## <a name="push-install-pre-requisites-for-linux"></a>Prerequisiti per l'installazione push per Linux:

1. Creare un utente root nel server Linux di origine. Usare questo account solo per l'installazione push e per gli aggiornamenti.</br>
2. Assicurarsi che il file /etc/hosts nel server Linux di origine contenga le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete. </br>
3. Verificare i pacchetti di openssh, openssh-server e openssl più recenti siano installati nel server di origine Linux. </br>
Controllare che la porta 22 SSH sia abilitata e in esecuzione. </br>
4. Controllare che le voci non aggiornate di agenti siano già presenti nel server di origine, disinstallare gli agenti precedenti e riavviare il server, reinstallare l'agente. </br>

#### <a name="enable-sftp-subsystem-and-password-authentication-in-the-sshdconfig-file"></a>Abilitare il sottosistema SFTP e l'autenticazione della password nel file sshd_config
1. Nel server di origine accedere come utente ROOT. </br>
2. Nel file /etc/ssh/sshd_config</br> trovare la riga che inizia con PasswordAuthentication. </br>
3. d.   Rimuovere il commento dalla riga e modificare il valore da"no" a "sì". </br>
![Linux1](./media/site-recovery-protection-common-errors/linux1.png)
4. Trovare la riga che inizia con "Subsystem" e rimuovere il commento. </br>
![Linux2](./media/site-recovery-protection-common-errors/linux2.png)
5. Salvare le modifiche e riavviare il servizio sshd. </br>

## <a name="push-installation-checks-on-configurationprocess-server"></a>Controlli dell'installazione push nel server di configurazione o del processo.
#### <a name="validate-credentials-for-discovery-and-installation"></a>Convalidare le credenziali per l'individuazione e l'installazione

1. Nel server di configurazione avviare Cspsconfigtool</br>
![WMItestConnect5](./media/site-recovery-protection-common-errors/wmitestconnect5.png) </br>

2. Assicurarsi che l'account usato per la protezione disponga dei diritti di amministratore nel computer di origine. </br>

#### <a name="check-connectivity-between-process-server-and-source-server"></a>Controllare la connettività tra i server di elaborazione e il server di origine
1. Verificare che il server di elaborazione disponga di una connessione Internet.
2. Verificare la connessione WMI tramite wbemtest.exe. </br>
Nel server di elaborazione fare clic su Avvia > Esegui > wbemtest.exe > verrà aperta la finestra Tester di Strumentazione gestione Windows come mostrato.</br>
   ![WMItestConnect1](./media/site-recovery-protection-common-errors/wmitestconnect1.png) </br>
   </br>
Fare clic su Connetti > immettere l'indirizzo IP del server di origine in Spazio dei nomi, inserire il nome utente e la password. Se il computer di origine è aggiunto a un dominio, specificare il nome di dominio insieme al nome utente, ad esempio "nomedominio\nomeutente". Se il computer di origine si trova in gruppo di lavoro, specificare solo il nome utente. Selezionare il livello di autenticazione come Riservatezza pacchetto. </br>
![WMItestConnect2](./media/site-recovery-protection-common-errors/wmitestconnect2.png) </br>
   </br>
   Fare clic su Connetti. A questo punto la connessione WMI deve essere completata con i dati forniti e viene visualizzata la finestra di Tester di Strumentazione gestione Windows come mostrato di seguito: </br>
   ![WMItestConnect3](./media/site-recovery-protection-common-errors/wmitestconnect3.png) </br>
</br>
   Se la connessione WMI non è riuscita verrà visualizzato un messaggio di errore. La seguente schermata mostra un tentativo non riuscito se WMI/amministrazione remota non è abilitato tra le app consentite di Windows Firewall. </br>
   ![WMItestConnect4](./media/site-recovery-protection-common-errors/wmitestconnect4.png) </br>
</br>

3. Verificare la stato WMI e la connettività.</br>
Nel server di configurazione o di elaborazione </br>
fare clic su Avvia > Esegui > wmimgmt.msc > Azioni > More Actions (Altre azioni) > connettersi a un altro computer, a un computer di origine. </br>
Immettere le credenziali dell'account usato per la protezione e controllare la connettività. </br>

#### <a name="verify-network-shared-folders-of-source-machine-is-accessible-from-process-server-ps-remotely-using-specified-credentials"></a>Verificare che le cartelle condivise in rete del computer di origine siano accessibili dal server di elaborazione in modalità remota con le credenziali specificate.
  1. Accedere al computer del server di elaborazione, aprire Esplora file > nella barra degli indirizzi digitare > "\\\source-machine-ip\C$" > fare clic su Invio. </br>
  ![Fileshare1](./media/site-recovery-protection-common-errors/fileshare1.png) </br>
  2. Esplora file richiederà le credenziali. Immettere il nome utente e la password > fare clic su OK.</br>
   Se il computer di origine è aggiunto a un dominio, immettere il nome di dominio insieme al nome utente ad esempio "nomedominio\nomeutente".</br>
   Se il computer di origine si trova in gruppo di lavoro, specificare solo il "nome utente". </br>
  ![Fileshare2](./media/site-recovery-protection-common-errors/fileshare2.png) </br>
  3. Se la connessione viene completata correttamente, è possibile visualizzare le cartelle del computer di origine in modalità remota dal server di elaborazione </br>
  ![Fileshare3](./media/site-recovery-protection-common-errors/fileshare3.png) </br>

> [!NOTE] 
> Se la connessione ha esito negativo, verificare che tutti i prerequisiti siano soddisfatti.
>

Se non si desidera aprire "Strumentazione gestione Windows", è anche possibile installare il servizio di mobilità manualmente nel computer di origine.</br> [Installare manualmente il Servizio Mobility tramite la GUI](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) </br>
[Installazione tramite Configuration manager](site-recovery-install-mobility-service-using-sccm.md) </br>

## <a name="next-steps"></a>Passaggi successivi
- [Abilitare la replica delle macchine virtuali VMware](vmware-walkthrough-enable-replication.md)

