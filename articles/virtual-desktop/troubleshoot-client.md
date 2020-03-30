---
title: Risolvere i problemi relativi al client Desktop remoto Di Windows Virtual Desktop - Azure
description: Come risolvere i problemi quando si configurano le connessioni client in un ambiente tenant di Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: e3a240901ffca2c126e2b61eaee0cf287cc31d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127500"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Risolvere i problemi relativi al client Desktop remoto

In questo articolo vengono descritti i problemi comuni con il client Desktop remoto e come risolverli.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Il client Desktop remoto per Windows 7 o Windows 10 si blocca o non può essere aperto

Utilizzare i cmdlet di PowerShell seguenti per pulire i registri dei client fuori banda.

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Passare a **%AppData%RdClientRadc** ed eliminare tutto il contenuto.

Disinstallare e reinstallare il client Desktop remoto per Windows 7 e Windows 10.

## <a name="web-client-wont-open"></a>Il client Web non si apre

In primo luogo, testare la connessione a Internet aprendo un altro sito web nel browser; ad esempio, [www.bing.com](https://www.bing.com).

Utilizzare **nslookup** per verificare che DNS possa risolvere il nome di dominio completo:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Prova a connetterti con un altro client, ad esempio il client Desktop remoto per Windows 7 o Windows 10, e verifica se riesci ad aprire il client Web.

### <a name="opening-another-site-fails"></a>Apertura di un altro sito non riesce

Ciò è in genere causato da problemi di connessione di rete o da un'interruzione della rete. Si consiglia di contattare il supporto di rete.

### <a name="nslookup-cannot-resolve-the-name"></a>Nslookup non è in grado di risolvere il nome

Ciò è in genere causato da problemi di connessione di rete o da un'interruzione della rete. Si consiglia di contattare il supporto di rete.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>Il client non può connettersi, ma altri client della rete possono connettersi

Se il browser inizia a agire o smette di funzionare mentre si utilizza il client Web, segui queste istruzioni per risolverlo:

1. Riavvia il browser.
2. Cancellare i cookie del browser. Vedere [Come eliminare i file cookie in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Cancellare la cache del browser. Vedere [cancellare la cache](https://binged.it/2RKyfdU)del browser per il browser .
4. Aprire il browser in modalità privata.

## <a name="web-client-stops-responding-or-disconnects"></a>Il client Web si blocca o si disconnette

Provare a connettersi utilizzando un altro browser o client.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Anche altri browser e client non funzionano correttamente o non riescono ad aprire

Se i problemi persistono anche dopo aver cambiato browser, il problema potrebbe non riguardare il browser, ma con la rete. Si consiglia di contattare il supporto di rete.

## <a name="web-client-keeps-prompting-for-credentials"></a>Il client Web continua a richiedere le credenziali

Se il client Web continua a richiedere le credenziali, seguire queste istruzioni:

1. Verificare che l'URL del client Web sia corretto.
2. Verificare che le credenziali in uso siano per l'ambiente Desktop virtuale di Windows collegato all'URL.
3. Cancellare i cookie del browser. Per ulteriori informazioni, vedere Come eliminare i [file cookie in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Cancellare la cache del browser. Per ulteriori dettagli, consultate Cancellare la cache del [browser per il browser.](https://binged.it/2RKyfdU)
5. Aprire il browser in modalità privata.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi di Desktop virtuale di Windows e sulle tracce di escalation, vedere [Panoramica della risoluzione dei problemi, feedback e supporto.](troubleshoot-set-up-overview.md)
- Per risolvere i problemi durante la creazione di un tenant e di un pool host in un ambiente Windows Virtual Desktop, vedere Creazione di [pool host e tenant](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in Windows Virtual Desktop, vedere Configurazione della [macchina virtuale dell'host di sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi all'utilizzo di PowerShell con Windows Virtual Desktop, vedere [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Per eseguire un'esercitazione sulla risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi relativi alle distribuzioni](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)di modelli di Resource Manager .