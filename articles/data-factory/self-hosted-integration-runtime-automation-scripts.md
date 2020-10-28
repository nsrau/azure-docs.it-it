---
title: Automatizzare l'installazione del runtime di integrazione self-hosted con gli script PowerShell locali
description: Informazioni su come automatizzare l'installazione del runtime di integrazione self-hosted in computer locali.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 05/09/2020
ms.openlocfilehash: 36414c975e97dbaa7d8747da98c31eeb12fbc206
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636970"
---
# <a name="automating-self-hosted-integration-runtime-installation-using-local-powershell-scripts"></a>Automatizzare l'installazione del runtime di integrazione self-hosted con gli script PowerShell locali
Per automatizzare l'installazione del runtime di integrazione self-hosted in computer locali (diversi dalle macchine virtuali di Azure in cui è possibile usare il modello di Resource Manager), è possibile usare script PowerShell locali. Questo articolo illustra due script che è possibile usare.

## <a name="prerequisites"></a>Prerequisiti

* Avviare PowerShell nel computer locale. Per eseguire gli script, è necessario scegliere **Esegui come amministratore** .
* [Scaricare](https://www.microsoft.com/download/details.aspx?id=39717) il software del runtime di integrazione self-hosted. Copiare il percorso in cui si trova il file scaricato. 
* Per registrare il runtime di integrazione self-hosted, è necessaria anche una **chiave di autenticazione** .
* Per automatizzare gli aggiornamenti manuali, è necessario avere un runtime di integrazione self-hosted preconfigurato.

## <a name="scripts-introduction"></a>Introduzione agli script 

> [!NOTE]
> Questi script vengono creati con l'[utilità della riga di comando documentata](./create-self-hosted-integration-runtime.md#set-up-an-existing-self-hosted-ir-via-local-powershell) nel runtime di integrazione self-hosted. Se necessario, è possibile personalizzare questi script in base a specifiche esigenze di automazione.
> Gli script devono essere applicati per singolo nodo, quindi assicurarsi di eseguirli in tutti i nodi in caso di configurazione a disponibilità elevata (due o più nodi).

* Per automatizzare l'installazione: Installare e registrare un nuovo nodo del runtime di integrazione self-hosted con **[InstallGatewayOnLocalMachine. ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/InstallGatewayOnLocalMachine.ps1)** : è possibile usare lo script per installare il nodo del runtime di integrazione self-hosted e registrarlo con una chiave di autenticazione. Lo script accetta due argomenti. Il **primo** consente di specificare il percorso del [runtime di integrazione self-hosted](https://www.microsoft.com/download/details.aspx?id=39717) in un disco locale, mentre il **secondo** consente di specificare la **chiave di autenticazione** per la registrazione del nodo del runtime di integrazione self-hosted.

* Per automatizzare gli aggiornamenti manuali: Aggiornare il nodo del runtime di integrazione self-hosted con una versione specifica o alla versione più recente di **[script-update-gateway. ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/script-update-gateway.ps1)** : questo aggiornamento è supportato anche nel caso in cui sia stato disattivato l'aggiornamento automatico o si voglia avere maggiore controllo sugli aggiornamenti. È possibile usare lo script per aggiornare il nodo del runtime di integrazione self-hosted alla versione più recente o a una versione successiva specificata (il downgrade non funziona). Accetta un argomento per specificare il numero di versione, ad esempio -version 3.13.6942.1. Se non viene specificata alcuna versione, il runtime di integrazione self-hosted viene sempre aggiornato alla versione più recente disponibile nei [download](https://www.microsoft.com/download/details.aspx?id=39717).
    > [!NOTE]
    > È possibile specificare solo le ultime tre versioni. Normalmente si usa per aggiornare un nodo esistente alla versione più recente. **PRESUPPONE LA PRESENZA DI UN RUNTIME DI INTEGRAZIONE SELF-HOST REGISTRATO** . 

## <a name="usage-examples"></a>Esempi di utilizzo

### <a name="for-automating-setup"></a>Per automatizzare l'installazione
1. Scaricare il runtime di integrazione self-hosted da [questa posizione](https://www.microsoft.com/download/details.aspx?id=39717). 
1. Specificare il percorso in cui si trova il file MSI SHIR scaricato (file di installazione). Ad esempio, se il percorso è *C:\Users\nome_utente\Downloads\IntegrationRuntime_4.7.7368.1.msi* , è possibile usare la riga di comando di PowerShell di esempio seguente per questa attività:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\InstallGatewayOnLocalMachine.ps1 -path "C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi" -authKey "[key]"
   ```

    > [!NOTE]
    > Sostituire [key] con la chiave di autenticazione per registrare il runtime di integrazione.
    > Sostituire "username" con il proprio nome utente.
    > Specificare il percorso del file "InstallGatewayOnLocalMachine.ps1" durante l'esecuzione dello script. In questo esempio è stato archiviato sul desktop.

1. Se nel computer è presente un solo runtime di integrazione self-hosted preinstallato, lo script lo disinstalla automaticamente e ne configura uno nuovo. Verrà visualizzata la finestra seguente: ![Configurazione del runtime di integrazione](media/self-hosted-integration-runtime-automation-scripts/integration-runtime-configure.png)

1. Al termine dell'installazione e della registrazione della chiave, nell'istanza locale di PowerShell verranno visualizzati i messaggi *Succeed to install gateway* (Installazione del gateway riuscita) e *Succeed to register gateway* (Registrazione del gateway riuscita).
        [![Risultato dell'esecuzione dello script 1](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png#lightbox)

### <a name="for-automating-manual-updates"></a>Per automatizzare gli aggiornamenti manuali
Questo script viene usato per aggiornare/installare e registrare la versione più recente del runtime di integrazione self-hosted. Lo script esegue automaticamente i passaggi seguenti:
1. Verifica la versione corrente del runtime di integrazione self-hosted
2. Ottiene la versione più recente o la versione specificata dall'argomento
3. Se è presente una versione più recente rispetto a quella versione corrente:
    * scarica il file MSI del runtime di integrazione self-hosted
    * lo aggiorna

Per usare questo script, è possibile seguire questa riga di comando di esempio:
* Scaricare e installare il gateway più recente:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1
   ```    
* Scaricare e installare il gateway della versione specificata:
   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1 -version 3.13.6942.1
   ``` 
   Se la versione corrente è già quella più recente, verrà visualizzato il risultato seguente, per indicare che non è richiesto alcun aggiornamento.   
    [![Risultato dell'esecuzione dello script 2](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png#lightbox)