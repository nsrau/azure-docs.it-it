---
title: Risolvere i problemi relativi al client Desktop remoto in Desktop virtuale Windows - Azure
description: Come risolvere i problemi quando si configurano le connessioni client in un ambiente tenant di Desktop virtuale Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0efff05f84bf02908dfc8b966b80539d309f491d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87059140"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Risolvere i problemi del client Desktop remoto

Questo articolo descrive i problemi comuni relativi al client Desktop remoto e come risolverli.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Il client Desktop remoto per Windows 7 o Windows 10 smette di rispondere o non può essere aperto

A partire dalla versione 1.2.790, è possibile reimpostare i dati utente dalla pagina Informazioni o usando un comando.

Usare il comando seguente per rimuovere i dati utente, ripristinare le impostazioni predefinite e annullare l'iscrizione da tutte le aree di lavoro.

```cmd
msrdcw.exe /reset [/f]
```

Se si usa una versione precedente del client Desktop remoto, è consigliabile disinstallare e reinstallare il client.

## <a name="web-client-wont-open"></a>Il client Web non si apre

Per prima cosa, testare la connessione Internet aprendo un altro sito Web nel browser; ad esempio, [www.bing.com](https://www.bing.com).

Usare **nslookup** per confermare che DNS è in grado di risolvere il nome di dominio completo:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Provare a connettersi con un altro client, ad esempio il client Desktop remoto per Windows 7 o Windows 10, e verificare se è possibile aprire il client Web.

### <a name="opening-another-site-fails"></a>Impossibile aprire un altro sito

Ciò è in genere causato da problemi di connessione di rete o da un'interruzione di rete. È consigliabile contattare il supporto di rete.

### <a name="nslookup-cannot-resolve-the-name"></a>Nslookup non riesce a risolvere il nome

Ciò è in genere causato da problemi di connessione di rete o da un'interruzione di rete. È consigliabile contattare il supporto di rete.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>Il client non è in grado di connettersi, ma altri client nella rete possono connettersi

Se il browser inizia a funzionare o smette di funzionare mentre si usa il client Web, seguire queste istruzioni per risolvere i problemi:

1. Riavviare il browser.
2. Cancellare i cookie del browser. Consultare [Come eliminare i file dei cookie in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Cancellare la cache del browser. Consultare [Cancellare la cache del browser](https://binged.it/2RKyfdU).
4. Aprire il browser in modalità privata.

## <a name="web-client-does-not-show-my-resources"></a>Il client Web non mostra le risorse personali

Prima di tutto, controllare l'account Azure Active Directory in uso. Se è già stato effettuato l'accesso con un account di Azure Active Directory diverso da quello che si vuole usare per desktop virtuale Windows, è necessario firmare o usare una finestra del browser privata.

Se si usa la versione dell'autunno 2019 di Desktop virtuale Windows, usare il collegamento client Web in [questo articolo](./virtual-desktop-fall-2019/connect-web-2019.md) per connettersi alle risorse.

## <a name="web-client-stops-responding-or-disconnects"></a>Il client Web smette di rispondere o si disconnette

Provare a connettersi usando un altro browser o client.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Anche altri browser e client non funzionano correttamente o non vengono aperti

Se i problemi persistono anche dopo aver cambiato i browser, il problema potrebbe non essere il browser, ma la rete. È consigliabile contattare il supporto di rete.

## <a name="web-client-keeps-prompting-for-credentials"></a>Il client Web continua a richiedere le credenziali

Se il client Web continua a richiedere le credenziali, seguire queste istruzioni:

1. Verificare che l'URL del client Web sia corretto.
2. Verificare che le credenziali usate siano per l'ambiente di Desktop virtuale Windows associato all'URL.
3. Cancellare i cookie del browser. Per altre informazioni, consultare [Come eliminare i file dei cookie in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Cancellare la cache del browser. Per altre informazioni, consultare [Cancellare la cache del browser](https://binged.it/2RKyfdU).
5. Aprire il browser in modalità privata.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi relativi a Desktop virtuale Windows e alle tracce di escalation, consultare [Panoramica della risoluzione dei problemi, feedback e supporto](troubleshoot-set-up-overview.md).
- Per risolvere i problemi durante la creazione di un ambiente Desktop virtuale Windows e di un pool di host in un ambiente Desktop virtuale Windows, consultare [Creazione di ambiente e pool di host](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in Desktop virtuale Windows, consultare [Configurazione di macchine virtuali nell'host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi all'uso di PowerShell con Desktop virtuale di Windows, consultare [PowerShell con Desktop virtuale Windows](troubleshoot-powershell.md).
- Per eseguire un'esercitazione di risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi delle distribuzioni dei modelli di Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
