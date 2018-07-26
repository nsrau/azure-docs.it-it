---
title: Risoluzione dei problemi di Azure Cloud Shell | Microsoft Docs
description: Risoluzione dei problemi di Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/03/2018
ms.author: damaerte
ms.openlocfilehash: 21bc0633a9cc607325b48998791cb12631ecd0d7
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856488"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Risoluzione dei problemi e limitazioni di Azure Cloud Shell

Le soluzioni note per i problemi in Azure Cloud Shell includono:

## <a name="general-troubleshooting"></a>Risoluzione dei problemi generali

### <a name="early-timeouts-in-firefox"></a>Timeout anticipati in FireFox
- **Dettagli**: Cloud Shell utilizza una connessione WebSocket aperta per passare input/output al browser. FireFox ha criteri predefiniti che possono chiudere la connessione WebSocket in modo anomalo causando timeout anticipati in Cloud Shell.
- **Risoluzione**: aprire FireFox e digitare "about:config" nella casella URL per accedere alla schermata corrispondente. Cercare "network.websocket.timeout.ping.request" e modificare il valore da 0 a 10.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Finestra di dialogo archiviazione - Errore: 403 RequestDisallowedByPolicy
- **Dettagli**: quando si crea un account di archiviazione tramite Azure Cloud Shell, il processo non riesce a causa di criteri di Azure inseriti dall'amministratore. Il messaggio di errore includerà il messaggio seguente:`The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Risoluzione**: contattare l'amministratore di Azure per rimuovere o aggiornare i criteri di Azure che impediscono di creare l'archiviazione.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Finestra di dialogo archiviazione - Errore: 400 DisallowedOperation
 - **Dettagli**: quando si usa una sottoscrizione di Azure Active Directory, non è possibile creare la risorsa di archiviazione.
 - **Risoluzione**: usare una sottoscrizione di Azure in grado di creare le risorse di archiviazione. Le sottoscrizioni di Azure Active Directory non sono in grado di creare le risorse di Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Output del terminale - Errore: impossibile effettuare la connessione del terminale: non è possibile stabilire una connessione websocket. Premere `Enter` per ristabilire la connessione.
 - **Dettagli**: Cloud Shell richiede la possibilità di stabilire una connessione websocket all'infrastruttura di Cloud Shell.
 - **Risoluzione**: verificare di aver configurato le impostazioni di rete per abilitare l'invio di richieste https e di richieste websocket ai domini in *.console.azure.com.

## <a name="bash-troubleshooting"></a>Risoluzione dei problemi di Bash

### <a name="cannot-run-the-docker-daemon"></a>Non è possibile eseguire il daemon Docker

- **Dettagli**: Cloud Shell prevede l'uso di un contenitore per ospitare l'ambiente della shell, pertanto l'esecuzione del daemon non è consentita.
- **Risoluzione**: usare [docker-machine](https://docs.docker.com/machine/overview/), installato per impostazione predefinita, per gestire i contenitori Docker da un host Docker remoto.

## <a name="powershell-troubleshooting"></a>Risoluzione dei problemi di PowerShell

### <a name="gui-applications-are-not-supported"></a>Le applicazioni GUI non sono supportate

- **Dettagli**: se un utente avvia un'applicazione GUI, il prompt non ricompare. Ad esempio, quando un utente clona un repository GitHub privato abilitato a due fattori di autenticazione, viene visualizzata una finestra di dialogo per completare l'autenticazione a due fattori.  
- **Risoluzione**: chiudere e riaprire la shell.

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help online non apre la pagina della Guida

- **Dettagli**: se un utente digita `Get-Help Find-Module -online`, visualizza un messaggio di errore, ad esempio:`Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
- **Risoluzione**: copiare l'url e aprirlo manualmente nella finestra del browser.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Risoluzione dei problemi di gestione remota delle macchine virtuali di Azure

- **Dettagli**: a causa delle impostazioni predefinite di Windows Firewall per la gestione remota Windows, all'utente può comparire l'errore seguente: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Risoluzione**: eseguire `Enable-AzureRmVMPSRemoting` per abilitare tutti gli aspetti della comunicazione remota di PowerShell nel computer di destinazione.
 

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir` memorizza il risultato nell'unità Azure

- **Dettagli**: il risultato di `dir` viene memorizzato nell'unità Azure.
- **Risoluzione**: dopo aver creato o rimosso una risorsa nella visualizzazione unità Azure, eseguire `dir -force` per aggiornare.

## <a name="general-limitations"></a>Limitazioni generali
Di seguito vengono descritte le limitazioni note di Azure Cloud Shell:

### <a name="system-state-and-persistence"></a>Persistenza e stato del sistema

Il computer che distribuisce la sessione Cloud Shell è temporaneo e viene riciclato dopo 20 minuti di inattività della sessione. Cloud Shell richiede che sia montata una condivisione file di Azure. La sottoscrizione, quindi, deve essere in grado di configurare le risorse di archiviazione per accedere a Cloud Shell. Altre considerazioni di cui tenere conto:

* Con l'archiviazione montata vengono rese persistenti soltanto le modifiche apportate all'interno della directory `clouddrive`. In Bash anche la directory `$Home` è permanente.
* Le condivisioni file di Azure possono essere implementate solo dall'interno dell'[area assegnata](persisting-shell-storage.md#mount-a-new-clouddrive).
  * In Bash, eseguire `env` per trovare l'area geografica impostata come `ACC_LOCATION`.
* File di Azure supporta solo account di archiviazione con ridondanza locale e account di archiviazione con ridondanza geografica.

### <a name="browser-support"></a>Supporto browser

Cloud Shell supporta le versioni più recenti di Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox e Apple Safari. Safari in modalità privata non è supportato.

### <a name="copy-and-paste"></a>Copiare e incollare

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Per un determinato utente, può essere attiva una sola shell

Gli utenti possono avviare solo un tipo di shell contemporaneamente, **Bash** o **PowerShell**. Tuttavia, si possono avere più istanze di Bash o PowerShell in esecuzione contemporaneamente. Lo scambio tra Bash o PowerShell ha come conseguenza il riavvio di Cloud Shell e le sessioni esistenti vengono terminate.

### <a name="usage-limits"></a>Limiti di consumo

Cloud Shell è pensato per l'uso interattivo e qualsiasi sessione non interattiva in esecuzione prolungata viene quindi interrotta senza preavviso.

## <a name="bash-limitations"></a>Limitazioni Bash

### <a name="user-permissions"></a>Autorizzazioni utente

Le autorizzazioni sono impostate come utenti normali senza accesso SUDO. Qualsiasi installazione esterna alla directory `$Home` non è permanente.

### <a name="editing-bashrc"></a>Modifica di .bashrc

Fare attenzione quando si modifica il file con estensione bashrc, poiché questa operazione può provocare errori imprevisti in Cloud Shell.

## <a name="powershell-limitations"></a>Limitazioni PowerShell

### <a name="azuread-module-name"></a>Nome modulo `AzureAD`

Il nome del modulo `AzureAD` è attualmente `AzureAD.Standard.Preview`. Il modulo fornisce la stessa funzionalità.

### <a name="sqlserver-module-functionality"></a>Funzionalità del modulo `SqlServer`

Il modulo `SqlServer` incluso in Cloud Shell include solo il supporto delle versioni precedenti di PowerShell Core. In particolare, `Invoke-SqlCmd` non è ancora disponibile.

### <a name="default-file-location-when-created-from-azure-drive"></a>Percorso file predefinito quando creato dall'unità Azure:

Usando dei cmdlet di PowerShell, gli utenti non possono creare i file sotto l'unità Azure. Quando gli utenti creano nuovi file con altri strumenti, ad esempio vim o nano, i file vengono salvati nella cartella `$HOME` per impostazione predefinita. 

### <a name="gui-applications-are-not-supported"></a>Le applicazioni GUI non sono supportate

Se l'utente esegue un comando che determina la generazione di una finestra di dialogo di Windows, come `Connect-AzureAD` o `Connect-AzureRmAccount`, viene visualizzato un messaggio di errore, ad esempio: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

### <a name="tab-completion-crashes-psreadline"></a>Il completamento scheda arresta PSReadline in modo anomalo

Se la proprietà EditMode dell'utente in PSReadline è impostata su Emacs, l'utente tenta di visualizzare tutte le possibilità tramite il completamento scheda e le dimensioni della finestra sono troppo piccole per visualizzare tutte le possibilità, per cui PSReadline si arresterà in modo anomalo.

### <a name="large-gap-after-displaying-progress-bar"></a>Ampio spazio vuoto dopo aver visualizzato l'indicatore di stato

Se l'utente esegue un'azione che visualizza un indicatore di stato, come il completamento di una scheda nell'unità `Azure:`, è possibile che il cursore non sia impostato correttamente e che venga visualizzato uno spazio vuoto dove prima era presente l'indicatore di stato.

### <a name="random-characters-appear-inline"></a>Inline vengono visualizzati caratteri casuali

La sequenza di posizione del cursore, ad esempio `5;13R`, può essere visualizzata nell'input dell'utente.  Questi caratteri possono essere rimossi manualmente.

## <a name="personal-data-in-cloud-shell"></a>Dati personali in Cloud Shell

Azure Cloud Shell tratta i dati personali dell'utente con molta cautela: i dati acquisiti e archiviati mediante il servizio Azure Cloud Shell vengono usati per offrire un'esperienza predefinita all'utente, considerando ad esempio l'ultima shell usata, le dimensioni e il tipo di carattere preferiti e i dettagli della condivisione dei file che supportano il clouddrive. Se si desidera esportare o eliminare i dati è possibile fare riferimento alle istruzioni seguenti.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Esportazione
Al fine di **esportare** le impostazioni dell'utente che Cloud Shell salva, ad esempio shell preferita, dimensione e tipo di carattere, eseguire i comandi seguenti.

1. Avviare Bash in Cloud Shell
2. Eseguire i comandi seguenti:
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
```

### <a name="delete"></a>Delete
Al fine di **eliminare** le impostazioni dell'utente che Cloud Shell salva, ad esempio shell preferita, dimensione e tipo di carattere, eseguire i comandi seguenti. Al successivo avvio di Cloud Shell verrà richiesto di caricare di nuovo una condivisione file. 

L'attuale condivisione di File di Azure non verrà eliminata se si eliminano le impostazioni dell'utente; passare a File di Azure per completare l'operazione.

1. Avviare Bash in Cloud Shell
2. Eseguire i comandi seguenti:
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
```
