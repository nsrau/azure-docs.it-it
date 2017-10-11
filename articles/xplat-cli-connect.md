---
title: Accedere ad Azure dall'interfaccia della riga di comando | Microsoft Docs
description: Connettersi alla sottoscrizione di Azure dall'interfaccia della riga di comando di Azure per Mac, Linux e Windows
editor: tysonn
manager: timlt
documentationcenter: 
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management
ms.assetid: ed856527-d75e-4e16-93fb-253dafad209d
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: rasquill
"\"/": 
ms.openlocfilehash: 31efab60690b54faf7992251fcd01e307c4464f2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="log-in-to-azure-from-the-azure-cli"></a>Accedere ad Azure dall'interfaccia della riga di comando di Azure
L'interfaccia della riga di comando di Azure è un insieme di comandi multipiattaforma open source per l'uso delle risorse di Azure. Questo articolo illustra diverse procedure che consentono di specificare le credenziali necessarie all'account Azure per connettere l'interfaccia della riga di comando di Azure alla sottoscrizione di Azure:

* Eseguire il comando `azure login` dell'interfaccia della riga di comando per l'autenticazione con Azure Active Directory. Questo metodo consente di accedere ai comandi dell'interfaccia della riga di comando in entrambe le [modalità di comando](#cli-command-modes). Quando si esegue il comando senza opzioni aggiuntive, `azure login` chiede di continuare la procedura di accesso in modo interattivo da un portale Web. Per le opzioni di comando aggiuntive di `azure login`, vedere gli scenari in questo articolo o digitare `azure login --help`.
* Se è solo necessario usare i comandi dell'interfaccia della riga di comando CLI in modalità di gestione del servizio Azure (non consigliati per le distribuzioni più nuove), è possibile scaricare e installare un file di impostazioni di pubblicazione nel computer in uso.

Se l'interfaccia non è stata ancora installata, vedere [Installare l'interfaccia della riga di comando di Azure](cli-install-nodejs.md). Se non si dispone di una sottoscrizione di Azure, è possibile creare un [account gratuito](http://azure.microsoft.com/free/) in pochi minuti.

Per informazioni sulle diverse identità dell'account e sulle sottoscrizioni di Azure, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="scenario-1-azure-login-with-interactive-login"></a>Scenario 1: accesso di Azure con accesso interattivo
Con determinati account l'interfaccia della riga di comando richiede l'esecuzione di `azure login` e la continuazione del processo di accesso con un Web browser da un portale Web, processo detto *accesso interattivo*. Un esempio comune è quando si usa un account aziendale o di un istituto di istruzione (detto anche *account dell'organizzazione*) configurato per richiedere l'autenticazione a più fattori. Usare l'accesso interattivo anche con l'account Microsoft, se si vuole usare i comandi in modalità Resource Manager.

L'accesso interattivo è semplice: digitare `azure login`, senza opzioni, come illustrato nell'esempio seguente:

```
azure login
```                                                                                             

L'output è simile a quello riportato di seguito:

```         
info:    Executing command login
info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate.
```
Copiare il codice offerto nell'output del comando e aprire un browser per http://aka.ms/devicelogin o un'altra pagina, se specificato. È possibile aprire un browser nello stesso computer o in un altro computer o dispositivo. Inserire il codice, poi viene richiesto di inserire il nome utente e la password per l'identità che si desidera utilizzare. Al termine di quel processo, la shell del comando completa la procedura di accesso. Potrebbe essere simile a:

    info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Added subscription Azure Free Trial
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

> [!NOTE]
> Con l'accesso interattivo l'autenticazione e l'autorizzazione vengono eseguite usando Azure Active Directory. Se si usa un'identità dell'account Microsoft, il processo consente di accedere al dominio predefinito di Azure Active Directory. Se è stata effettuata l'iscrizione per un account Azure gratuito, Azure Active Directory ha creato automaticamente un dominio predefinito per l'account.
>
>

## <a name="scenario-2-azure-login-with-a-username-and-password"></a>Scenario 2: accesso di Azure con un nome utente e una password
Quando si vuole usare un account aziendale o dell'istituto di istruzione che non richiede l'autenticazione a più fattori, usare il comando `azure login` con il parametro username (`-u`) per autenticarsi. Sulla riga di comando viene richiesta la password, che, facoltativamente, può anche essere passata come parametro aggiuntivo del comando `azure login`. L'esempio seguente fornisce il nome utente di un account dell'organizzazione:

    azure login -u myUserName@contoso.onmicrosoft.com

Viene chiesto di immettere la password:

    info:    Executing command login
    Password: *********

Quindi viene completato il processo di accesso.

    info:    Added subscription Visual Studio Ultimate with MSDN
    +
    info:    login command OK

Se è la prima volta che si accede con queste credenziali, verrà chiesto di specificare se si desidera memorizzare nella cache un token di autenticazione. Questa richiesta viene visualizzata anche se è stato precedentemente usato il comando `azure logout` (descritto più avanti in questo articolo). Per ignorare la richiesta per gli scenari di automazione, eseguire `azure login` con il parametro `-q`.

## <a name="scenario-3-azure-login-with-a-service-principal"></a>Scenario 3: accesso di Azure con un'entità servizio
Se è stata creata un'entità servizio per un'applicazione Active Directory e l'entità servizio possiede autorizzazioni per la sottoscrizione, è possibile usare il comando `azure login` per autenticare l'entità servizio. In base allo scenario, le credenziali dell'entità servizio possono essere fornite come parametri espliciti del comando `azure login`. Ad esempio, il comando seguente passa il nome dell'entità servizio e l'ID tenant di Active Directory:

    azure login -u https://www.contoso.org/example --service-principal --tenant myTenantID

Viene chiesto di immettere la password. È anche possibile indicare le credenziali usando uno script dell'interfaccia della riga di comando o un codice dell'applicazione CLI oppure usare un certificato per autenticare l'entità servizio in modo non interattivo per gli scenari di automazione. Per dettagli ed esempi, vedere [Autenticazione di un'entità servizio con Gestione risorse di Azure](resource-group-authenticate-service-principal-cli.md).

## <a name="scenario-4-use-a-publish-settings-file"></a>Scenario 4: usare un file di impostazioni di pubblicazione
Se è necessario usare soltanto i comandi dell'interfaccia della riga di comando della modalità Gestione dei servizi di Azure, ad esempio per distribuire le VM di Azure nel modello di distribuzione classica, è possibile connettersi con un file di impostazioni di pubblicazione. Questo metodo installa un certificato nel computer locale che consente di eseguire attività di gestione per fino a quando la sottoscrizione e il certificato sono validi.

* **Per scaricare il file di impostazioni di pubblicazione** per l'account, verificare che l'interfaccia della riga di comando sia in modalità Gestione dei servizi digitando `azure config mode asm`. Quindi, eseguire il comando seguente:

        azure account download

Verrà aperto il browser predefinito e richiesto di accedere al [portale di Azure classico](https://manage.windowsazure.com). Dopo l'accesso, viene scaricato un file `.publishsettings` . Prendere nota del percorso in cui il file viene salvato.

> [!NOTE]
> Se l'account è associato a più tenant Azure Active Directory, è possibile che venga richiesto di selezionare per quale istanza di Active Directory si desidera scaricare un file di impostazioni di pubblicazione.
>
>

Dopo aver effettuato la selezione tramite la pagina di download oppure visitando il portale di Azure classico, il tenant Active Directory selezionato diventerà quello usato per impostazione predefinita nel portale classico e nella pagina di download. Dopo la scelta di un'impostazione predefinita, nella parte superiore della pagina di download viene visualizzato un messaggio che indica di **fare clic per tornare nella pagina di selezione**. Usare il collegamento specificato per tornare nella pagina di selezione.

* **Per importare il file di impostazioni di pubblicazione**, eseguire il comando seguente.

        azure account import <path to your .publishsettings file>

> [!IMPORTANT]
> Dopo l'importazione delle impostazioni di pubblicazione, è opportuno eliminare il file `.publishsettings`. Il file non è più necessario per l'interfaccia della riga di comando di Azure e rappresenta un rischio di sicurezza perché potrebbe essere usato per accedere alla sottoscrizione.
>
>

## <a name="cli-command-modes"></a>Modalità di comando dell'interfaccia della riga di comando
L'interfaccia della riga di comando di Azure offre due modalità di comando per lavorare con le risorse di Azure ciascuna delle quali presenta un set di comandi diverso:

* **Modalità Resource Manager** : per lavorare con le risorse di Azure nel modello di distribuzione di Resource Manager. Per impostare questa modalità, eseguire `azure config mode arm`.
* **Modalità Gestione dei servizi** : per lavorare con le risorse di Azure nel modello di distribuzione classica. Per impostare questa modalità, eseguire `azure config mode asm`.

Alla prima installazione, la versione corrente dell'interfaccia della riga di comando è in modalità Resource Manager.

> [!NOTE]
> La modalità Resource Manager e la modalità Gestione dei servizi si escludono a vicenda, ossia le risorse create in una modalità non possono essere gestite dall'altra.
>
>

## <a name="multiple-subscriptions"></a>Più sottoscrizioni
Se si usano più sottoscrizioni Azure, effettuando l'accesso ad Azure è possibile accedere a tutte le sottoscrizioni associate alle credenziali. Una sola sottoscrizione è selezionata come predefinita e usata dall'interfaccia della riga di comando di Azure per l'esecuzione delle operazioni. È possibile visualizzare le sottoscrizioni, tra cui la sottoscrizione predefinita corrente, usando il comando `azure account list`. Questo comando restituisce informazioni simili alle seguenti:

    info:    Executing command account list
    data:    Name              Id                                    Current
    data:    ----------------  ------------------------------------  -------
    data:    Azure-sub-1       ####################################  true
    data:    Azure-sub-2       ####################################  false

Nell'elenco precedente la colonna **Current** indica la sottoscrizione predefinita corrente Azure-sub-1. Per cambiare sottoscrizione predefinita, utilizzare il comando `azure account set` e specificarne una diversa. Ad esempio:

    azure account set Azure-sub-2

La sottoscrizione predefinita diventerà in questo modo Azure-sub-2.

> [!NOTE]
> La modifica della sottoscrizione predefinita ha effetto immediato ed è una modifica globale. I nuovi comandi dell'interfaccia della riga di comando di Azure, sia che vengano eseguiti dalla stessa istanza della riga di comando o da un'istanza diversa, usano la nuova sottoscrizione predefinita.
>
>

Se si desidera usare una sottoscrizione non predefinita con l'interfaccia della riga di comando di Azure, ma senza cambiare quella attualmente predefinita, è possibile usare l'opzione `--subscription` e specificare il nome della sottoscrizione da usare per l'operazione.

Una volta che si è connessi alla sottoscrizione di Azure, è possibile iniziare a usare i comandi dell'interfaccia della riga di comando di Azure per lavorare con le risorse di Azure.

## <a name="storage-of-cli-settings"></a>Archiviazione delle impostazioni dell'interfaccia della riga di comando
Quando si esegue l'accesso con il comando `azure login` o si importano le impostazioni di pubblicazione, i log e il profilo dell'interfaccia della riga di comando vengono archiviati in una directory `.azure` all'interno della directory `user`. La directory `user` è protetta dal sistema operativo. È tuttavia consigliabile adottare precauzioni aggiuntive per crittografare la propria directory `user`. A tale scopo, è possibile procedere come segue:

* In Windows, modificare le proprietà della directory o utilizzare BitLocker.
* In Mac, abilitare FileVault per la cartella.
* In Ubuntu usare la funzionalità per la crittografia della cartella Home. Altre distribuzioni di Linux offrono funzionalità analoghe.

## <a name="logging-out"></a>Disconnessione
Per disconnettersi, usare il comando seguente:

    azure logout -u <username>

Se le sottoscrizioni associate all'account vengono autenticate solo con Active Directory, dopo la disconnessione le informazioni di sottoscrizione verranno eliminate dal profilo locale. Se però per le sottoscrizioni è stato anche importato un file di impostazioni di pubblicazione, con la disconnessione verranno eliminate dal profilo locale solo le informazioni correlate ad Active Directory.

## <a name="next-steps"></a>Passaggi successivi
* Per l'uso dei comandi dell'interfaccia della riga di comando di Azure, vedere [Comandi dell'interfaccia della riga di comando di Azure in modalità Resource Manager](virtual-machines/azure-cli-arm-commands.md) e [Comandi dell'interfaccia della riga di comando di Azure in modalità Gestione servizi di Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* Per altre informazioni sull'interfaccia della riga di comando di Azure, il download del codice sorgente, la segnalazione dei problemi o la collaborazione al progetto, visitare il [repository GitHub per tale interfaccia](https://github.com/azure/azure-xplat-cli).
* In caso di problemi durante l'uso dell'interfaccia della riga di comando di Azure o di Azure, visitare i [forum di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).
