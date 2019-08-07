---
title: Creazione di tenant e pool host nel desktop virtuale di Windows-Azure
description: Come risolvere i problemi quando si configura un tenant e una macchina virtuale host della sessione in un ambiente desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: helohr
ms.openlocfilehash: 4e5c5f14042f7059f3d802a5e72cbf5c6a126614
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816330"
---
# <a name="tenant-and-host-pool-creation"></a>Creazione di pool di host e tenant

Usare questo articolo per risolvere i problemi che si verificano durante la configurazione delle macchine virtuali (VM) host sessione desktop virtuale di Windows.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

Al momento non vengono accettati casi di supporto mentre Desktop virtuale Windows è in anteprima. Visitare la pagina [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per discutere del servizio Desktop virtuale Windows con il team del prodotto e i membri attivi della community.

## <a name="vms-are-not-joined-to-the-domain"></a>Le macchine virtuali non sono unite in join al dominio

Se si verificano problemi durante l'aggiunta di macchine virtuali al dominio, seguire queste istruzioni.

- Aggiungere la VM manualmente usando il processo in [aggiungere una macchina virtuale Windows Server a un dominio gestito](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal) o usando il [modello di aggiunta al dominio](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Provare a eseguire il ping del nome di dominio dalla riga di comando nella macchina virtuale.
- Esaminare l'elenco dei messaggi di errore di aggiunta a un dominio nella [sezione risoluzione dei problemi di aggiunta al dominio](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)

### <a name="error-incorrect-credentials"></a>Errore: Credenziali non corrette

**Causa:** Si è verificato un errore di digitazione quando sono state immesse le credenziali nelle correzioni dell'interfaccia del modello Azure Resource Manager.

**Difficoltà** Per correggere le credenziali, seguire queste istruzioni.

1. Aggiungere manualmente le VM a un dominio.
2. Ridistribuire una volta che le credenziali sono state confermate. Vedere [creare un pool di host con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).
3. Aggiungere macchine virtuali a un dominio usando un modello con un join di una [macchina virtuale Windows esistente al dominio di Active Directory](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Errore: Timeout in attesa dell'input dell'utente

**Causa:** L'account usato per completare l'aggiunta a un dominio può avere multi-factor authentication.

**Difficoltà** Per completare l'aggiunta a un dominio, seguire queste istruzioni.

1. Rimuovere temporaneamente l'autenticazione a più fattori per l'account.
2. Usare un account del servizio.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Errore: L'account usato durante il provisioning non ha le autorizzazioni necessarie per completare l'operazione

**Causa:** L'account usato non dispone delle autorizzazioni per aggiungere macchine virtuali al dominio a causa della conformità e delle normative.

**Difficoltà** Seguire queste istruzioni.

1. Utilizzare un account membro del gruppo Administrators.
2. Concedere le autorizzazioni necessarie all'account in uso.

### <a name="error-domain-name-doesnt-resolve"></a>Errore: Il nome di dominio non viene risolto

**Causa 1:** Le macchine virtuali si trovano in un gruppo di risorse non associato alla rete virtuale (VNET) in cui si trova il dominio.

**Correzione 1:** Creare il peering VNET tra il VNET in cui è stato effettuato il provisioning delle macchine virtuali e il VNET in cui è in esecuzione il controller di dominio (DC). Vedere [creare un peering di rete virtuale-Gestione risorse, sottoscrizioni diverse](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions).

**Causa 2:** Quando si usa AadService (AADS), le voci DNS non sono state impostate.

**Fix 2:** Per impostare i servizi del dominio, vedere [Enable Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Il caricatore di avvio di Windows Virtual Desktop Agent e Windows Virtual Desktop non sono installati

Il modo consigliato per eseguire il provisioning di macchine virtuali consiste nell'usare il modello di Azure Resource Manager creare ed effettuare il provisioning del **pool di host** Il modello installa automaticamente l'agente desktop virtuale di Windows e il caricatore di avvio dell'agente desktop virtuale di Windows.

Seguire queste istruzioni per confermare che i componenti sono installati e per verificare la presenza di messaggi di errore.

1. Verificare che i due componenti siano installati tramite il **Pannello** > di controllo**programmi** > programmi**e funzionalità**. Se l' **agente desktop virtuale di Windows** e il **caricatore di avvio di Windows Virtual Desktop Agent** non sono visibili, non vengono installati nella macchina virtuale.
2. Aprire **Esplora file** e passare a **C:\Windows\Temp\scriptlogs.log**. Se il file è mancante, significa che non è stato possibile eseguire PowerShell DSC che ha installato i due componenti nel contesto di sicurezza fornito.
3. Se il file **C:\Windows\Temp\scriptlogs.log** è presente, aprirlo e verificare la presenza di messaggi di errore.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>Errore: Il caricatore di avvio di Windows Virtual Desktop Agent e Windows Virtual Desktop Agent risulta mancante. Manca anche C:\Windows\Temp\scriptlogs.log

**Causa 1:** Le credenziali fornite durante l'input per il modello di Azure Resource Manager non sono corrette oppure le autorizzazioni sono insufficienti.

**Correzione 1:** Aggiungere manualmente i componenti mancanti alle macchine virtuali usando [Crea un pool di host con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

**Causa 2:** PowerShell DSC è stato in grado di essere avviato ed eseguito, ma non è stato possibile completarlo perché non è in grado di accedere a desktop virtuale Windows e ottenere le informazioni necessarie.

**Fix 2:** Confermare gli elementi nell'elenco seguente.

- Assicurarsi che l'account non disponga dell'autenticazione a più fattori.
- Verificare che il nome del tenant sia accurato e che il tenant esista nel desktop virtuale di Windows.
- Verificare che l'account disponga almeno delle autorizzazioni di collaboratore Servizi Desktop remoto.

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>Errore: Autenticazione non riuscita. errore in C:\Windows\Temp\scriptlogs.log

**Causa:** PowerShell DSC è stato in grado di eseguire ma non è riuscito a connettersi al desktop virtuale di Windows.

**Difficoltà** Confermare gli elementi nell'elenco seguente.

- Registrare manualmente le VM con il servizio desktop virtuale di Windows.
- Verificare che l'account utilizzato per la connessione a desktop virtuale Windows disponga delle autorizzazioni per il tenant per la creazione di pool host.
- Verificare che l'account non disponga dell'autenticazione a più fattori.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>L'agente desktop virtuale Windows non viene registrato con il servizio desktop virtuale di Windows

Quando l'agente desktop virtuale di Windows viene installato per la prima volta nelle VM host sessione (manualmente o tramite il modello di Azure Resource Manager e PowerShell DSC), fornisce un token di registrazione. Nella sezione seguente vengono illustrati i problemi relativi alla risoluzione dei problemi applicabili all'agente desktop virtuale di Windows e al token.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Errore: Lo stato archiviato nel cmdlet Get-RdsSessionHost Mostra lo stato non disponibile

![Il cmdlet Get-RdsSessionHost Mostra lo stato come non disponibile.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Causa:** L'agente non è in grado di eseguire l'aggiornamento a una nuova versione.

**Difficoltà** Seguire queste istruzioni per aggiornare manualmente l'agente.

1. Scaricare una nuova versione dell'agente nella macchina virtuale host sessione.
2. Avviare Gestione attività e, nella scheda servizio, arrestare il servizio RDAgentBootLoader.
3. Eseguire il programma di installazione per la nuova versione dell'agente desktop virtuale di Windows.
4. Quando viene richiesto il token di registrazione, rimuovere la voce INVALID_TOKEN e fare clic su Next (un nuovo token non è obbligatorio).
5. Completare l'installazione guidata di.
6. Aprire Task Manager e avviare il servizio RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Errore:  La voce del registro di sistema di Windows Virtual Desktop Agent è stata registrata con un valore pari a 0

**Causa:** Il token di registrazione è scaduto o è stato generato con un valore di scadenza pari a 999999.

**Difficoltà** Per correggere l'errore del registro di sistema di Agent, seguire queste istruzioni.

1. Se è già presente un token di registrazione, rimuoverlo con Remove-RDSRegistrationInfo.
2. Generare un nuovo token con RDS-NewRegistrationInfo.
3. Verificare che il parametro-ExpriationHours sia impostato su 72 (il valore massimo è 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Errore: L'agente desktop virtuale Windows non segnala un heartbeat durante l'esecuzione di Get-RdsSessionHost

**Causa 1:** Il servizio RDAgentBootLoader è stato arrestato.

**Correzione 1:** Avviare Gestione attività e, se la scheda servizio segnala uno stato arrestato per il servizio RDAgentBootLoader, avviare il servizio.

**Causa 2:** La porta 443 può essere chiusa.

**Fix 2:** Seguire queste istruzioni per aprire la porta 443.

1. Verificare che la porta 443 sia aperta scaricando lo strumento PSPing da [Sysinternal Tools](https://docs.microsoft.com/sysinternals/downloads/psping).
2. Installare PSPing nella macchina virtuale host sessione in cui è in esecuzione l'agente.
3. Aprire il prompt dei comandi come amministratore ed eseguire il comando seguente:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Verificare che PSPing abbia ricevuto le informazioni dalla RDBroker:

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

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Risoluzione dei problemi relativi allo stack side-by-side di desktop virtuale di Windows

Lo stack side-by-side di desktop virtuale Windows viene installato automaticamente con Windows Server 2019. Utilizzare Microsoft Installer (MSI) per installare lo stack affiancato in Microsoft Windows Server 2016 o Windows Server 2012 R2. Per Microsoft Windows 10, lo stack side-by-side di desktop virtuale di Windows è abilitato con **enablesxstackrs. ps1**.

Esistono tre modi principali per installare o abilitare lo stack affiancato nelle macchine virtuali del pool host della sessione:

- Con il Azure Resource Manager creare ed effettuare il provisioning di un nuovo modello di **pool host per desktop virtuali Windows**
- Con l'inclusione e l'abilitazione nell'immagine master
- Installato o abilitato manualmente in ogni macchina virtuale (o con estensioni/PowerShell)

Se si verificano problemi con lo stack affiancato di desktop virtuali Windows, digitare il comando **qwinsta** dal prompt dei comandi per verificare che lo stack affiancato sia installato o abilitato.

L'output di **qwinsta** elenca **RDP-SxS** nell'output se lo stack affiancato viene installato e abilitato.

![Stack side-by-side installato o abilitato con qwinsta elencato come RDP-SxS nell'output.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Esaminare le voci del registro di sistema elencate di seguito e verificare che i relativi valori corrispondano. Se mancano le chiavi del registro di sistema o i valori non corrispondono, seguire le istruzioni in [creare un pool di host con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) per informazioni su come reinstallare lo stack affiancato.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Errore: O_REVERSE_CONNECT_STACK_FAILURE

![Codice di errore O_REVERSE_CONNECT_STACK_FAILURE.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Causa:** Lo stack affiancato non è installato nella macchina virtuale host sessione.

**Difficoltà** Seguire queste istruzioni per installare lo stack affiancato nella macchina virtuale host sessione.

1. Usare Remote Desktop Protocol (RDP) per accedere direttamente alla macchina virtuale host sessione come amministratore locale.
2. Scaricare e importare [il modulo PowerShell per desktop virtuale Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell, se non è già stato fatto.
3. Installare lo stack affiancato usando [creare un pool di host con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Come correggere lo stack side-by-side di un desktop virtuale di Windows che funziona correttamente

Ci sono circostanze note che possono causare un malfunzionamento dello stack side-by-Side:

- Non segue l'ordine corretto dei passaggi per abilitare lo stack affiancato
- Aggiornamento automatico a Windows 10 Enhanced Versatile Disk (EVD)
- Manca il ruolo host sessione Desktop remoto (RDSH)
- Esecuzione di enablesxsstackrc. ps1 più volte
- Esecuzione di enablesxsstackrc. ps1 in un account che non dispone di privilegi di amministratore locale

Le istruzioni riportate in questa sezione consentono di disinstallare lo stack side-by-side di desktop virtuale di Windows. Dopo aver disinstallato lo stack affiancato, passare a "registrare la macchina virtuale con il pool host del desktop virtuale Windows" in [creare un pool host con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) per reinstallare lo stack affiancato.

La macchina virtuale usata per eseguire la correzione deve trovarsi nella stessa subnet e dominio della macchina virtuale con lo stack side-by-side non funzionante.

Seguire queste istruzioni per eseguire il monitoraggio e l'aggiornamento dalla stessa subnet e dominio:

1. Connettersi con Remote Desktop Protocol Standard (RDP) alla macchina virtuale da cui verrà applicata la correzione.
2. Scaricare PsExec da https://docs.microsoft.com/sysinternals/downloads/psexec.
3. Decomprimere il file scaricato.
4. Avviare il prompt dei comandi come amministratore locale.
5. Passare alla cartella in cui PsExec è stato decompresso.
6. Al prompt dei comandi usare il comando seguente:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname è il nome del computer della macchina virtuale con lo stack side-by-side non funzionante.

7. Per accettare il contratto di licenza PsExec, fare clic su Accetto.

    ![Screenshot del contratto di licenza software.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Questa finestra di dialogo verrà visualizzata solo la prima volta che si esegue PsExec.

8. Dopo che la sessione del prompt dei comandi si apre nella macchina virtuale con lo stack side-by-side non funzionante, eseguire qwinsta e verificare che sia disponibile una voce denominata RDP-SxS. In caso contrario, uno stack affiancato non è presente nella macchina virtuale in modo che il problema non sia associato allo stack affiancato.

    ![Prompt dei comandi dell'amministratore](media/AdministratorCommandPrompt.png)

9. Eseguire il comando seguente, che elenca i componenti Microsoft installati nella macchina virtuale con lo stack side-by-side non funzionante.

    ```cmd
        wmic product get name
    ```

10. Eseguire il comando seguente con i nomi di prodotto del passaggio precedente.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Disinstallare tutti i prodotti che iniziano con "Desktop remoto".

12. Dopo aver disinstallato tutti i componenti desktop virtuali di Windows, seguire le istruzioni per il sistema operativo in uso:

13. Se il sistema operativo è Windows Server, riavviare la macchina virtuale con lo stack side-by-side non funzionante (con portale di Azure o dallo strumento PsExec).

Se il sistema operativo è Microsoft Windows 10, continuare con le istruzioni seguenti:

14. Dalla macchina virtuale che esegue PsExec, aprire Esplora file e copiare disablesxsstackrc. ps1 nell'unità di sistema della macchina virtuale con lo stack side-by-side non funzionante.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname è il nome del computer della macchina virtuale con lo stack side-by-side non funzionante.

15. Procedura consigliata: dallo strumento PsExec avviare PowerShell e passare alla cartella del passaggio precedente ed eseguire disablesxsstackrc. ps1. In alternativa, è possibile eseguire i cmdlet seguenti:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Al termine dell'esecuzione dei cmdlet, riavviare la macchina virtuale con lo stack side-by-side non funzionante.

## <a name="remote-licensing-model-is-not-configured"></a>Il modello di gestione licenze remota non è configurato

Se si accede a una multisessione Enterprise di Windows 10 con un account amministrativo, è possibile che venga visualizzata una notifica che indica che la modalità di gestione licenze Desktop remoto non è configurata, Servizi Desktop remoto smetterà di funzionare tra X giorni. Nel server Gestore connessione usare Server Manager per specificare la modalità di gestione licenze Desktop remoto ". Se viene visualizzato questo messaggio, significa che è necessario configurare manualmente la modalità di gestione licenze per **ogni utente**.

Per configurare manualmente la modalità di gestione licenze:  

1. Passare alla casella di ricerca del **menu Start** , quindi individuare e aprire **gpedit. msc** per accedere all'editor di criteri di gruppo locale. 
2. Passare a **configurazione** > computer**modelli amministrativi** > **componenti di Windows** **Servizi Desktop remoto** host sessione Desktop remoto >  >  >  **Gestione delle licenze**. 
3. Selezionare **imposta la modalità di gestione licenze Desktop remoto** e modificarla in per **utente**.

Attualmente si esaminano i problemi di timeout del periodo di notifica e di tolleranza e si prevede di risolverli in un aggiornamento futuro. 

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi relativi a desktop virtuale Windows e alle tracce di escalation, vedere [panoramica sulla risoluzione dei problemi, commenti e suggerimenti e supporto](troubleshoot-set-up-overview.md).
- Per risolvere i problemi durante la creazione di un tenant e di un pool host in un ambiente desktop virtuale Windows, vedere [creazione di tenant e pool host](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in desktop virtuale di Windows, vedere [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi alle connessioni client di desktop virtuali Windows, vedere [Desktop remoto connessioni client](troubleshoot-client-connection.md).
- Per risolvere i problemi relativi all'uso di PowerShell con desktop virtuale di Windows, vedere [PowerShell per desktop virtuale di Windows](troubleshoot-powershell.md).
- Per ulteriori informazioni sul servizio di anteprima, vedere [ambiente Windows Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Per eseguire un'esercitazione di risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi relativi alle](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)distribuzioni gestione risorse modello.
- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).