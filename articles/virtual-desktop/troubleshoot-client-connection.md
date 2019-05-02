---
title: Connessioni client Desktop remoto in Windows Desktop virtuale - Azure
description: Come risolvere i problemi durante la configurazione di connessioni client in un ambiente di tenant di Desktop virtuale Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: c5a67e22c301a2afc73a46a6def9a514426c497f
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928048"
---
# <a name="remote-desktop-client-connections"></a>Connessioni client Desktop remoto

Usare questo articolo per risolvere i problemi con le connessioni client di Desktop virtuale Windows.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

Al momento non vengono accettati casi di supporto mentre Desktop virtuale Windows è in anteprima. Visitare la pagina [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per discutere del servizio Desktop virtuale Windows con il team del prodotto e i membri attivi della community.

## <a name="you-cant-open-a-web-client"></a>Non è possibile aprire un client web

Verificare che sia presente la connettività internet, aprire un altro sito web. ad esempio, [www.Bing.com](https://www.bing.com).

Uso **nslookup** per verificare DNS può risolvere il FQDN:

    ```cmd
    nslookup rdweb.wvd.microsoft.com
    ```

Provare a connettersi con un altro client, ad esempio il client di Desktop remoto per Windows 7 o Windows 10 e controllo verificare se è possibile aprire il client web.

### <a name="error-opening-another-site-fails"></a>Errore: Apertura di un altro sito non riesce

**Causa:** Problemi di rete e/o interruzioni.

**Fix:** Contattare il supporto di rete.

### <a name="error-nslookup-cannot-resolve-the-name"></a>Errore: Nslookup non è possibile risolvere il nome

**Causa:** Problemi di rete e/o interruzioni.

**Fix:** Contattare il supporto di rete

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Errore: Non è possibile connettersi, ma possono connettersi ad altri client

**Causa:** Il browser non si comporta come previsto e arrestate funzionante.

**Fix:** Seguire queste istruzioni per risolvere i problemi del browser.

1. Riavviare il browser.
2. Cookie del browser non crittografato. Visualizzare [come eliminare i file di cookie in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Cache del browser non crittografato. Visualizzare [cancellare la cache del browser per il browser](https://binged.it/2RKyfdU).
4. Apri browser in modalità privata.

## <a name="web-client-stops-responding-or-disconnects"></a>Client Web non risponde o si disconnette

Provare a connettersi usando un altro browser o client.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Errore: Altri browser e i client anche malfunzionamento o non aprirsi

**Causa:** Problemi di rete e/o di operazione del sistema o interruzioni

**Fix:** Team di contattare il supporto tecnico.

## <a name="web-client-keeps-prompting-for-credentials"></a>Client Web mantiene vengono richieste le credenziali

Se il client Web mantiene vengono richieste le credenziali, seguire queste istruzioni.

1. Confermare il che URL del client web sia corretto.
2. Verificare che le credenziali per l'ambiente di Desktop virtuale di Windows associato all'URL.
3. Cookie del browser non crittografato. Visualizzare [come eliminare i file di cookie in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Cache del browser non crittografato. Visualizzare [cancellare la cache del browser per il browser](https://binged.it/2RKyfdU).
5. Apri browser in modalità privata.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Client Desktop remoto per Windows 7 o Windows 10 non risponde o non può essere aperto

Usare i cmdlet di PowerShell seguenti per pulire i registri di out-of-band (OOB) client.

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Passare a **%AppData%\RdClientRadc** ed eliminare tutto il contenuto.

Disinstallare e reinstallare il client Desktop remoto per Windows 7 e Windows 10.

## <a name="troubleshooting-end-user-connectivity"></a>Risoluzione dei problemi di connettività dell'utente finale

In alcuni casi gli utenti possono accedere ai propri feed e le risorse locali, ma ancora problemi di prestazioni che ne impediscono l'accesso a risorse remote, disponibilità o configurazione. In questi casi, l'utente riceve messaggi simili ai seguenti:

![Messaggio di errore di connessione Desktop remoto.](media/eb76b666808bddb611448dfb621152ce.png)

![Impossibile connettersi al messaggio di errore di gateway.](media/a8fbb9910d4672147335550affe58481.png)

Seguire queste istruzioni sulla risoluzione dei problemi generale per i codici di errore di connessione client.

1. Confermare il nome utente e ora quando si è verificato problema.
2. Aprire **PowerShell** e stabilire una connessione al tenant di Desktop virtuale Windows in cui è stato segnalato il problema.
3. Conferma connessione per il tenant corretto con **Get-RdsTenant.**
4. Se necessario, impostare il tenant con un gruppo contesto **Set-RdsContext – TenantGroupt\<TenantGroup\>**.
5. Usando **Get-RdsHostPool** e **Get-RdsSessionHost** cmdlet, verificare che la risoluzione dei problemi viene eseguita nel pool di host corretto.
6. Eseguire il comando seguente per ottenere un elenco di tutte le attività non riuscite della connessione di tipo per l'intervallo di tempo specificato:

    ```cmd
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

7. Usando il **ActivityId** dall'output del cmdlet precedente, eseguire il comando seguente:

    ```
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

8. Il comando restituisce un output simile all'output indicato di seguito. Uso **ErrorCodeSymbolic** e **ErrorMessage** per risolvere la causa radice.

    ```
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-oaddusertogroupfailed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32errornosuchmember"></a>Errore: O_ADD_USER_TO_GROUP_FAILED / non è stato possibile aggiungere l'utente = ≤username≥ al gruppo = utenti Desktop remoto. Motivo: Win32.ERROR_NO_SUCH_MEMBER

**Causa:** Macchina virtuale non è stato aggiunto al dominio in cui è oggetto utente.

**Fix:** Aggiungere macchine Virtuali al dominio corretto. Visualizzare [aggiungere una macchina virtuale Windows Server a un dominio gestito](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).

### <a name="error-nslookup-cannot-resolve-the-name"></a>Errore: Nslookup non è possibile risolvere il nome

**Causa:** Problemi di rete o interruzioni.

**Fix:** Contattare il supporto di rete

### <a name="error-connectionfailedclientprotocolerror"></a>Errore: ConnectionFailedClientProtocolError

**Causa:** Le macchine virtuali che l'utente sta provando a connettersi a non vengono aggiunti a un dominio.

**Fix:** Aggiungere tutte le macchine virtuali che fanno parte di un pool di host al controller di dominio.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Utente si connette, ma viene visualizzato nulla (alcun feed)

Un utente può avviare i client Desktop remoto ed è in grado di eseguire l'autenticazione, tuttavia l'utente non sono disponibili tutte le icone nell'individuazione web feed.

Verificare che l'utente segnala i problemi sia stato assegnato ai gruppi di applicazioni mediante la riga di comando:

```cmd
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Verificare che l'utente esegue l'accesso con le credenziali corrette.

Se viene utilizzato il client web, verificare che non siano presenti problemi di credenziali memorizzate nella cache.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi di Desktop virtuale Windows e le tracce di escalation dei blocchi, vedere [risoluzione dei problemi di supporto, commenti e suggerimenti e panoramica](troubleshoot-set-up-overview.md).
- Per risolvere i problemi durante la creazione di un pool di tenant e l'host in un ambiente di Desktop virtuale Windows, vedere [la creazione di pool di Tenant e l'host](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in Windows Desktop virtuale, vedere [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi quando si usa PowerShell con Windows Desktop virtuale, vedere [Windows PowerShell di Desktop virtuale](troubleshoot-powershell.md).
- Per altre informazioni sul servizio di anteprima, vedere [ambiente di anteprima di Desktop Windows](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?).
- Per eseguire un'esercitazione di risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi di distribuzioni di modelli di Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).