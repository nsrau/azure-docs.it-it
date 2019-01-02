---
title: Risolvere gli errori di autenticazione quando si usa RDP per connettersi a una macchina virtuale di Azure | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 47d3b827099d3a4a7520ac66765d2928795b6e49
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967981"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Risolvere gli errori di autenticazione quando si usa RDP per connettersi a una macchina virtuale di Azure

Questo articolo può essere utile per risolvere gli errori di autenticazione che si verificano quando si usa una connessione RDP (Remote Desktop Protocol) per connettersi a una macchina virtuale di Azure.

## <a name="symptoms"></a>Sintomi

Si acquisisce uno screenshot di una macchina virtuale di Azure che mostra la schermata iniziale e indica che il sistema operativo è in esecuzione. Tuttavia, quando si cerca di connettersi alla macchina virtuale tramite Connessione Desktop remoto, si riceve un messaggio di errore simile a uno dei seguenti.

### <a name="error-message-1"></a>Messaggio di errore 1

**Si è verificato un errore di autenticazione. Impossibile contattare l'autorità di sicurezza locale.**

### <a name="error-message-2"></a>Messaggio di errore 2

**Il computer remoto a cui si sta cercando di connettersi richiede l'autenticazione a livello di rete, ma non è possibile contattare il controller di dominio Windows per eseguire l'autenticazione. Se si è amministratori nel computer remoto, è possibile disabilitare l'autenticazione a livello di rete usando le opzioni della scheda Remoto nella finestra di dialogo Proprietà di sistema.**

### <a name="error-message-3-generic-connection-error"></a>Messaggio di errore 3 (errore di connessione generico)

**Il computer non è in grado di connettersi al computer remoto. Provare a eseguire di nuovo la connessione. Se il problema persiste, contattare il proprietario del computer remoto o l'amministratore di rete.**

## <a name="cause"></a>Causa

Esistono diversi motivi per cui l'autenticazione a livello di rete potrebbe bloccare l'accesso RDP a una macchina virtuale.

### <a name="cause-1"></a>Causa 1

La macchina virtuale non è in grado di comunicare con il controller di dominio. Questo problema potrebbe impedire a una sessione RDP di accedere a una macchina virtuale usando le credenziali del dominio. Dovrebbe comunque essere possibile accedere usando le credenziali di amministratore locale. Il problema può verificarsi nelle situazioni seguenti:

1. Il canale di sicurezza di Active Directory tra questa macchina virtuale e il controller di dominio si è interrotto.

2. La macchina virtuale ha una copia non aggiornata della password dell'account, mentre il controller di dominio ha una copia più recente.

3. Il controller di dominio a cui si sta connettendo la macchina virtuale non è integro.

### <a name="cause-2"></a>Causa 2

Il livello di crittografia della macchina virtuale è superiore a quello usato dal computer client.

### <a name="cause-3"></a>Causa 3

I protocolli TLS 1.0, 1.1 o 1.2 (server) sono disabilitati nella macchina virtuale.

### <a name="cause-4"></a>Causa 4

La macchina virtuale è stata configurata in modo da disabilitare l'accesso mediante le credenziali del dominio e l'autorità di protezione locale non è configurata correttamente.

### <a name="cause-5"></a>Causa 5

La macchina virtuale è stata configurata in modo da accettare solo le connessioni con algoritmi conformi allo standard FIPS (Federal Information Processing Standard). Questa configurazione viene in genere impostata tramite criteri di Active Directory. È una configurazione rara, ma FIPS può essere applicato solo per le connessioni Desktop remoto.

## <a name="before-you-troubleshoot"></a>Operazioni preliminari alla risoluzione dei problemi

### <a name="create-a-backup-snapshot"></a>Creare uno snapshot di backup

Per creare uno snapshot di backup, seguire i passaggi descritti in [Creare uno snapshot di un disco](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Connettersi alla macchina virtuale in modalità remota

Per connettersi alla macchina virtuale in modalità remota, usare uno dei metodi descritti in [Usare gli strumenti remoti per risolvere i problemi delle macchine virtuali di Azure](remote-tools-troubleshoot-azure-vm-issues.md).

### <a name="group-policy-client-service"></a>Servizio client Criteri di gruppo

Se la macchina virtuale è aggiunta a un dominio, arrestare prima di tutto il servizio client Criteri di gruppo per impedire ai criteri di Active Directory di sovrascrivere le modifiche. A tale scopo, usare il comando seguente:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

Una volta risolto il problema, ripristinare la funzionalità che consente alla macchina virtuale di contattare il dominio per recuperare il più recente oggetto Criteri di gruppo dal dominio. A tale scopo, eseguire i comandi seguenti:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Se la modifica viene ripristinata, significa che il problema è causato da un criterio di Active Directory. 

### <a name="workaround"></a>Soluzione alternativa

Per ovviare al problema, eseguire i comandi seguenti nella finestra di comando per disabilitare l'autenticazione a livello di rete:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Quindi riavviare la macchina virtuale.

Per riabilitare l'autenticazione a livello di rete, eseguire il comando seguente e quindi riavviare la macchina virtuale:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="for-domain-joined-vms"></a>Per le macchine virtuali aggiunte a un dominio

Per risolvere questo problema, verificare prima di tutto che la macchina virtuale sia in grado di connettersi a un controller di dominio e che il controller di dominio sia in stato integro e possa gestire le richieste della macchina virtuale.

>[!Note] 
>Per testare l'integrità del controller di dominio è possibile usare un'altra macchina virtuale nella stessa rete virtuale e nella stessa subnet che condividono lo stesso server di accesso.

Connettersi alla macchina virtuale interessata dal problema usando la console seriale, CMD remoto o PowerShell remoto, seguendo i passaggi descritti nella sezione "Connettersi alla macchina virtuale in modalità remota".

Per determinare a quale controller di dominio si sta connettendo la macchina virtuale, eseguire il comando seguente nella console: 

```cmd
set | find /i "LOGONSERVER"
```

Verificare quindi l'integrità del canale protetto tra la macchina virtuale e il controller di dominio. A questo scopo, eseguire il comando seguente in un'istanza di PowerShell con privilegi elevati. Questo comando restituisce un flag booleano che indica se il canale protetto è attivo:

```powershell
Test-ComputerSecureChannel -verbose
```

Se il canale è interrotto, eseguire il comando seguente per ripristinarlo:

```powershell
Test-ComputerSecureChannel -repair
```

Verificare che la password dell'account computer in Active Directory sia aggiornata nella macchina virtuale e nel controller di dominio:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Se la comunicazione fra il controller di dominio e la macchina virtuale è buona ma il controller di dominio non è sufficientemente integro da aprire una sessione RDP, è possibile provare a riavviarlo.

Se il problema di comunicazione con il dominio non si è risolto con i comandi precedenti, è possibile aggiungere di nuovo la macchina virtuale al dominio. A questo scopo, seguire questa procedura:

1. Creare uno script di nome Unjoin.ps1 usando il contenuto seguente, quindi distribuirlo come estensione script personalizzata nel portale di Azure:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Questo script rimuove forzatamente la macchina virtuale dal dominio e la riavvia 10 secondi dopo. Occorre quindi eseguire la pulizia dell'oggetto Computer sul lato del dominio.

2.  Completata la pulizia, aggiungere di nuovo la macchina virtuale al dominio. A questo scopo, creare uno script di nome JoinDomain.ps1 usando il contenuto seguente, quindi distribuirlo come estensione script personalizzata nel portale di Azure: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >La macchina virtuale viene aggiunta al dominio usando le credenziali specificate.

Se il canale di Active Directory è integro, la password del computer è aggiornata e il controller di dominio funziona nel modo previsto, provare la procedura seguente.

Se il problema persiste, controllare se le credenziali del dominio sono disabilitate. A questo scopo, aprire una finestra del prompt dei comandi con privilegi elevati, quindi eseguire il comando seguente per determinare se la macchina virtuale è configurata in modo da disabilitare gli account di dominio per l'accesso alla macchina virtuale:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Se la chiave è impostata su **1**, significa che il server è stato configurato in modo da non consentire le credenziali di dominio. Impostare la chiave su **0**.

### <a name="for-standalone-vms"></a>Per le macchine virtuali autonome

#### <a name="check-minencryptionlevel"></a>Controllare il valore MinEncryptionLevel

In un'istanza CMD eseguire il comando seguente per eseguire una query sul valore di registro **MinEncryptionLevel**:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

In base al valore del Registro di sistema, seguire questa procedura:

* 4 (FIPS): passare a [Controllare le connessioni con algoritmi conformi a FIPS](#fips-compliant).

* 3 (crittografia a 128 bit): impostare la gravità su **2** eseguendo il comando seguente:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (massimo livello di crittografia possibile, come stabilito dal client): è possibile provare a impostare la crittografia sul valore minimo **1** eseguendo il comando seguente:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Riavviare la macchina virtuale per applicare le modifiche al Registro di sistema.

#### <a name="tls-version"></a>Versione di TLS

A seconda del sistema, RDP usa il protocollo TLS 1.0, 1.1 o 1.2 (server). Per stabilire come sono configurati questi protocolli nella macchina virtuale, aprire un'istanza CMD ed eseguire i comandi seguenti:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Se i valori restituiti non corrispondono tutti a **1**, significa che il protocollo è disabilitato. Per abilitare questi protocolli, eseguire i comandi seguenti:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

Per altre versioni del protocollo è possibile eseguire i comandi seguenti:

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> Ottenere la versione x.x di SSH/TLS dai log del sistema operativo guest sugli errori SCHANNEL.

#### <a name="fips-compliant"></a>Controllare le connessioni con algoritmi conformi a FIPS

È possibile configurare Desktop remoto in modo che usi solo connessioni con algoritmi conformi a FIPS. A questo scopo si può usare una chiave del Registro di sistema. Aprire una finestra del prompt dei comandi con privilegi elevati ed eseguire una query sulle chiavi seguenti:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Se il comando restituisce **1**, cambiare il valore del Registro di sistema in **0**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Verificare il valore corrente di MinEncryptionLevel nella macchina virtuale:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Se il comando restituisce **4**, cambiare il valore del Registro di sistema in **2**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Riavviare la macchina virtuale per applicare le modifiche al Registro di sistema.

## <a name="next-steps"></a>Passaggi successivi

[Metodo SetEncryptionLevel della classe Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[Configurare l'autenticazione server e i livelli di crittografia](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Classe Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
