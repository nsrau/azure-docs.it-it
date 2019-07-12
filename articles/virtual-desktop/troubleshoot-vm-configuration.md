---
title: Creazione del pool di tenant e l'host di Desktop virtuale Windows - Azure
description: Come risolvere i problemi quando si configura un tenant e la sessione host macchina virtuale (VM) in un ambiente di Desktop virtuale Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: v-chjenk
ms.openlocfilehash: 96a9d8fc7495ea473b0a3250b34251afc5f30c13
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786716"
---
# <a name="tenant-and-host-pool-creation"></a>Creazione di pool di host e tenant

Usare questo articolo per risolvere i problemi che si verificano quando si configurano le macchine virtuali di host sessione Desktop virtuale di Windows (VM).

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

Al momento non vengono accettati casi di supporto mentre Desktop virtuale Windows è in anteprima. Visitare la pagina [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per discutere del servizio Desktop virtuale Windows con il team del prodotto e i membri attivi della community.

## <a name="vms-are-not-joined-to-the-domain"></a>Le macchine virtuali non vengono aggiunti al dominio

Seguire queste istruzioni se si verificano problemi di aggiunta di macchine virtuali al dominio.

- Aggiungere la macchina virtuale manualmente usando la procedura nel [aggiungere una macchina virtuale Windows Server a un dominio gestito](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal) o tramite il [modello di dominio di unione](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Provare il ping del nome di dominio dalla riga di comando nella macchina virtuale.
- Esaminare l'elenco dei messaggi di errore di aggiunta dominio nelle [risoluzione dei problemi dei messaggi di errore di dominio Join](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Errore: Credenziali non corrette

**Causa:** Si è verificato un errore di digitazione quando sono state immesse le credenziali in Azure Resource Manager corregge interfaccia del modello.

**Fix:** Seguire queste istruzioni per correggere le credenziali.

1. Aggiungere manualmente le macchine virtuali a un dominio.
2. Ridistribuire una volta che le credenziali sono state confermate. Visualizzare [creare un pool di host con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).
3. Aggiungere le macchine virtuali a un dominio usando un modello con [aggiunge una macchina virtuale Windows esistente al dominio AD](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Errore: Timeout in attesa di input dell'utente

**Causa:** L'account usato per completare l'aggiunta al dominio potrebbe essere multi-factor authentication (MFA).

**Fix:** Seguire queste istruzioni per completare l'aggiunta al dominio.

1. Rimuovere temporaneamente MFA per l'account.
2. Usare un account del servizio.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Errore: L'account usato durante il provisioning non dispone delle autorizzazioni per completare l'operazione

**Causa:** L'account utilizzato non dispone delle autorizzazioni per aggiungere le macchine virtuali al dominio a causa di conformità e normative.

**Fix:** Seguire queste istruzioni.

1. Usare un account membro del gruppo Administrators.
2. Concedere le autorizzazioni necessarie per l'account usato.

### <a name="error-domain-name-doesnt-resolve"></a>Errore: Nome di dominio non viene risolto

**Causa 1:** Le macchine virtuali sono in un gruppo di risorse che non è associato con la rete virtuale (VNET) in cui si trova il dominio.

**Fix 1:** Creare un peering tra la rete virtuale in cui ha effettuato il provisioning di macchine virtuali e la rete virtuale in cui viene eseguito il controller di dominio (DC) reti. Visualizzare [creare un peering di rete virtuale - Resource Manager, sottoscrizioni diverse](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions).

**Causa 2:** Quando si usa AadService (AADS), le voci DNS non sono state impostate.

**Fix 2:** Per impostare i servizi di dominio, vedere [abilitare Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Agente di Desktop virtuali Windows e Windows caricatore di avvio di Desktop virtuali non sono installati

Il metodo consigliato per effettuare il provisioning di macchine virtuali è tramite Azure Resource Manager **crea ed effettuare il provisioning di Windows Desktop virtuale ospitare pool** modello. Il modello viene installato automaticamente l'agente di Desktop virtuali Windows e Windows Virtual Desktop agente Boot Loader.

Seguire queste istruzioni per verificare che siano installati i componenti e verificare la presenza di messaggi di errore.

1. Verificare che i due componenti vengono installati con la verifica **Pannello di controllo** > **programmi** > **programmi e funzionalità**. Se **agente di Windows Virtual Desktop** e **Windows Virtual Desktop agente Boot Loader** non sono visibili, non sono installati nella macchina virtuale.
2. Aprire **Esplora File** e passare alla **C:\Windows\Temp\scriptlogs.log**. Se il file è mancante, indica che il DSC di PowerShell installati i due componenti non è stato in grado di eseguire nel contesto di sicurezza fornito.
3. Se il file **C:\Windows\Temp\scriptlogs.log** è presente, aprirlo e verificare la presenza di messaggi di errore.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>Errore: L'agente di Desktop virtuali Windows e Windows Virtual Desktop agente Boot Loader risultano mancanti. C:\Windows\Temp\scriptlogs.log anche è mancante

**Causa 1:** Credenziali fornite durante l'input per il modello di Azure Resource Manager non sono corrette o le autorizzazioni sono insufficienti.

**Fix 1:** Aggiungere manualmente i componenti mancanti per le macchine virtuali usando [creare un pool di host con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

**Causa 2:** PowerShell DSC è stato in grado di avviare ed eseguire ma non è stato completato perché non è possibile accedere al Desktop virtuale Windows e ottenere le informazioni necessarie.

**Fix 2:** Verificare gli elementi nell'elenco seguente.

- Assicurarsi che l'account non ha l'autenticazione a più fattori.
- Verificare che il nome del tenant è accurato e che il tenant esiste già nel Desktop virtuale Windows.
- Confermare l'account disponga almeno delle autorizzazioni di collaboratore di servizi desktop remoto.

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>Errore: Autenticazione non riuscita, errore nella C:\Windows\Temp\scriptlogs.log

**Causa:** PowerShell DSC è stato in grado di eseguire, ma non è stato possibile connettersi al Desktop virtuale Windows.

**Fix:** Verificare gli elementi nell'elenco seguente.

- Registrare manualmente le macchine virtuali con il servizio di Windows Desktop virtuale.
- Confermare l'account usato per la connessione al Desktop virtuale di Windows disponga delle autorizzazioni per il tenant per creare pool di host.
- Confermare l'account non dispone di autenticazione a più fattori.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows Virtual Desktop agente non registra con il servizio di Desktop virtuale Windows

Quando l'agente di Desktop virtuali di Windows viene installato prima di tutto nella sessione di ospitare le macchine virtuali (manualmente o tramite il modello Azure Resource Manager e PowerShell DSC), fornisce un token di registrazione. La sezione seguente illustra la risoluzione dei problemi applicabili all'agente di Desktop virtuali di Windows e il token.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Errore: Lo stato archiviato nel cmdlet Get-RdsSessionHost Mostra lo stato come non disponibile

![Cmdlet Get-RdsSessionHost Mostra lo stato come non disponibile.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Causa:** L'agente non è in grado di aggiornarsi automaticamente a una nuova versione.

**Fix:** Seguire queste istruzioni per aggiornare manualmente l'agente.

1. Scaricare una nuova versione dell'agente nella macchina virtuale host sessione.
2. Avviare Gestione attività e, nella scheda del servizio, arrestare il servizio RDAgentBootLoader.
3. Eseguire il programma di installazione per la nuova versione dell'agente di Desktop virtuali di Windows.
4. Quando viene richiesto il token di registrazione, rimuovere la voce INVALID_TOKEN e premere Avanti (un nuovo token non è obbligatorio).
5. Completare l'installazione guidata.
6. Aprire Task Manager e avviare il servizio RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Errore:  Voce del Registro di sistema dell'agente di Desktop virtuale Windows IsRegistered Mostra un valore pari a 0

**Causa:** Token di registrazione è scaduto o è stato generato con il valore di scadenza di 999999.

**Fix:** Seguire queste istruzioni per correggere l'agente di errore del Registro di sistema.

1. Se esiste già un token di registrazione, rimuoverlo con Remove-RDSRegistrationInfo.
2. Generare nuovi token con NewRegistrationInfo di servizi desktop remoto.
3. Verificare che il parametro - ExpriationHours è impostato su 72 (valore massimo è 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Errore: Agente di Windows Desktop virtuale non è segnala un heartbeat quando si esegue Get-RdsSessionHost

**Causa 1:** Servizio RDAgentBootLoader è stato arrestato.

**Fix 1:** Avviare Gestione attività e, se la scheda servizio Segnala stato di arresto per il servizio RDAgentBootLoader, avviare il servizio.

**Causa 2:** La porta 443 potrebbe venire chiusa.

**Fix 2:** Seguire queste istruzioni per aprire la porta 443.

1. Verificare la porta 443 sia aperta scaricando lo strumento PSPing [Sysinternal strumenti](https://docs.microsoft.com/sysinternals/downloads/psping).
2. Installare PSPing nell'host della sessione in cui viene eseguito l'agente della macchina virtuale.
3. Aprire il prompt dei comandi come amministratore ed eseguire il comando seguente:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Verificare che tali informazioni PSPing ricevuto nuovamente dal RDBroker:

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Risoluzione dei problemi con lo stack side-by-side di Desktop virtuale Windows

Lo stack side-by-side di Desktop virtuale di Windows viene installato automaticamente con Windows Server 2019. Usare Microsoft Installer (MSI) per installare lo stack side-by-side in Microsoft Windows Server 2016 o Windows Server 2012 R2. Per Microsoft Windows 10, lo stack side-by-side di Desktop virtuale Windows è abilitato con **enablesxstackrs.ps1**.

Esistono tre modi principali, lo stack side-by-side Ottiene installato o abilitato nel pool di host sessione macchine virtuali:

- Con Azure Resource Manager **crea ed effettuare il provisioning nuovo Desktop virtuale di Windows host pool** modello
- Viene incluso e abilitata nell'immagine master
- Installato o abilitato manualmente in ogni macchina virtuale (o con le estensioni/PowerShell)

Se si verificano problemi con lo stack side-by-side di Desktop virtuale Windows, digitare il **qwinsta** comando dal prompt dei comandi per verificare che lo stack side-by-side è installato o abilitato.

L'output del **qwinsta** elencherà **rdp-sxs** nell'output se lo stack side-by-side è installato e abilitato.

![Stack side-by-side è installato o abilitato con qwinsta elencato come rdp-sxs nell'output.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Esaminare le voci del Registro di sistema elencate di seguito e confermare che i relativi valori corrispondano. Se le chiavi del Registro di sistema sono mancante o i valori non corrispondono, seguire le istruzioni in [creare un pool di host con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) sulla reinstallazione dello stack side-by-side.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-oreverseconnectstackfailure"></a>Errore: O_REVERSE_CONNECT_STACK_FAILURE

![Codice di errore O_REVERSE_CONNECT_STACK_FAILURE.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Causa:** Lo stack side-by-side non è installato nella macchina virtuale host sessione.

**Fix:** Seguire queste istruzioni per installare lo stack side-by-side in macchina virtuale host sessione.

1. Usare Remote Desktop Protocol (RDP) per ottenere direttamente nella macchina virtuale host sessione come amministratore locale.
2. Scaricare e importare [modulo di PowerShell di Desktop virtuali di Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell se non è già presente.
3. Installare side-by-side stack tramite [creare un pool di host con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Come risolvere uno stack side-by-side di Desktop virtuale Windows che non viene eseguita correttamente

Esistono noti circostanze che possono causare malfunzionamenti stack side-by-side:

- Non seguendo l'ordine corretto dei passaggi per abilitare lo stack side-by-side
- Aggiornamento automatico per Windows 10 avanzata Versatile disco (EVD)
- Manca il ruolo Host sessione Desktop remoto (RDSH)
- Esecuzione enablesxsstackrc.ps1 più volte
- Esecuzione enablesxsstackrc.ps1 in un account che non dispone di privilegi di amministratore locale

Le istruzioni riportate in questa sezione consentono di disinstallare lo stack side-by-side di Desktop virtuale Windows. Una volta disinstallato stack side-by-side, passare a "Registrare la macchina virtuale con il pool di host di Desktop virtuali di Windows" [creare un pool di host con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) reinstallare lo stack side-by-side.

La macchina virtuale consente di eseguire monitoraggio e aggiornamento deve essere nello stesso dominio e subnet della macchina virtuale con lo stack side-by-side non funziona correttamente.

Seguire queste istruzioni per eseguire monitoraggio e aggiornamento dalla stessa subnet e dominio:

1. Connettersi con standard Remote Desktop Protocol (RDP) alla macchina virtuale da in cui verrà applicata correzione.
2. Scaricato PsExec da https://docs.microsoft.com/sysinternals/downloads/psexec.
3. Decomprimere il file scaricato.
4. Avviare il prompt dei comandi come amministratore locale.
5. Passare alla cartella in cui è stato decompresso PsExec.
6. Dal prompt dei comandi, usare il comando seguente:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname è il nome del computer della macchina virtuale con lo stack side-by-side non funziona correttamente.

7. Accettare il contratto di licenza di PsExec, fare clic su Accetto.

    ![Schermata di contratto di licenza software.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Questa finestra di dialogo verrà visualizzata solo la prima volta che viene eseguito PsExec.

8. Quando si apre la sessione di prompt dei comandi nella macchina virtuale con lo stack side-by-side non funziona correttamente, eseguire qwinsta e verificare che sia disponibile una voce denominata rdp-sxs. In caso contrario uno stack side-by-side non è presente nella macchina virtuale in modo che il problema non è collegato allo stack side-by-side.

    ![Prompt dei comandi di amministratore](media/AdministratorCommandPrompt.png)

9. Eseguire il comando seguente, che consente di elencare i componenti di Microsoft installati nella macchina virtuale con lo stack side-by-side non funziona correttamente.

    ```cmd
        wmic product get name
    ```

10. Eseguire il comando seguente con i nomi di prodotto dal passaggio precedente.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Disinstallare tutti i prodotti che iniziano con "Desktop remoto".

12. Dopo che sono stati disinstallati tutti i componenti di Windows Desktop virtuale, seguire le istruzioni per il sistema operativo:

13. Se il sistema operativo è Windows Server, riavviare la macchina virtuale con lo stack side-by-side non funziona correttamente (in con il portale di Azure o tramite lo strumento PsExec).

Se il sistema operativo Microsoft Windows 10, continuare con le istruzioni riportate di seguito:

14. Dalla macchina virtuale in esecuzione di PsExec, aprire Esplora File e copiare disablesxsstackrc.ps1 all'unità del sistema della macchina virtuale con lo stack side-by-side particolare.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname è il nome del computer della macchina virtuale con lo stack side-by-side non funziona correttamente.

15. Il processo consigliato: dallo strumento di PsExec, avviare PowerShell e passare alla cartella del passaggio precedente ed eseguire disablesxsstackrc.ps1. In alternativa, è possibile eseguire i cmdlet seguenti:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Quando i cmdlet vengono eseguiti in esecuzione, riavviare la macchina virtuale con lo stack side-by-side non funziona correttamente.

## <a name="remote-licensing-model-is-not-configured"></a>Modello di gestione licenze remoto non è configurato

Se si accede alla multi-sessione di Windows 10 Enterprise utilizzando un account amministratore, è possibile ricevere una notifica con la dicitura "modalità di gestione licenze Desktop remoto non è configurata, Servizi Desktop remoto smetterà di funzionare in X giorni. Nel server Gestore connessione, usare Server Manager per specificare la modalità gestione licenze Desktop remoto." Se viene visualizzato questo messaggio, che significa che è necessario configurare manualmente la modalità gestione licenze per **per ogni utente**.

Per configurare manualmente la modalità gestione licenze:  

1. Andare alla **dal menu Start** casella di ricerca, quindi trovare e aprire **gpedit. msc** per accedere all'editor Criteri di gruppo locali. 
2. Passare a **configurazione Computer** > **modelli amministrativi** > **i componenti di Windows**  >   **Servizi Desktop remoto** > **Host sessione Desktop remoto** > **licenze**. 
3. Selezionare **impostare la modalità gestione licenze Desktop remoto** e impostarlo su **per ogni utente**.

È attualmente sta analizzando i problemi di timeout di notifica e periodo di prova e pianificare la risoluzione in un aggiornamento futuro. 

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi di Desktop virtuale Windows e le tracce di escalation dei blocchi, vedere [risoluzione dei problemi di supporto, commenti e suggerimenti e panoramica](troubleshoot-set-up-overview.md).
- Per risolvere i problemi durante la creazione di un pool di tenant e l'host in un ambiente di Desktop virtuale Windows, vedere [la creazione di pool di Tenant e l'host](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in Windows Desktop virtuale, vedere [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi con le connessioni client di Desktop virtuale Windows, vedere [le connessioni client Desktop remoto](troubleshoot-client-connection.md).
- Per risolvere i problemi quando si usa PowerShell con Windows Desktop virtuale, vedere [Windows PowerShell di Desktop virtuale](troubleshoot-powershell.md).
- Per altre informazioni sul servizio di anteprima, vedere [ambiente di anteprima di Desktop Windows](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Per eseguire un'esercitazione di risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi di distribuzioni di modelli di Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).