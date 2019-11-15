---
title: Avvio rapido - Connettere i computer ad Azure con Azure Arc per server - Portale
description: Questo argomento di avvio rapido illustra come connettere i computer ad Azure con Azure Arc per server dal portale
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: automazione di Azure, DSC, powershell, configurazione dello stato desiderato, gestione aggiornamenti, rilevamento modifiche, inventario, runbook, python, grafico, ibrido, onboarding
ms.date: 08/25/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 2ae7c8545286baebc83077276e356cd2e41f0dc3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73668672"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---portal"></a>Guida introduttiva: Connettere i computer ad Azure con Azure Arc per server - Portale

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Esaminare i client supportati e la configurazione di rete necessaria nella [Panoramica di Azure Arc per server](overview.md).

## <a name="generate-the-agent-install-script-using-the-azure-portal"></a>Generare lo script di installazione dell'agente con il portale di Azure

1. Avviare [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal)
1. Fare clic su **Aggiungi**
1. Seguire la procedura guidata per il completamento
1. Nell'ultima pagina è stato generato uno script che è possibile copiare (o scaricare).

Lo script deve essere eseguito nel computer di destinazione che si vuole connettere. Scarica l'agente, lo installa e connette il computer come un'unica operazione.

Nei server non Azure che si vuole gestire:

1. Accedere al server (tramite SSH, RDP o comunicazione remota di PowerShell)
1. Avviare una shell: bash in Linux, PowerShell come amministratore in Windows
1. Incollare lo script dal portale ed eseguirlo nel server per connettersi ad Azure.
1. L'autenticazione predefinita per eseguire l'onboarding di un singolo server è *interattiva* tramite l'"accesso dispositivo" di Azure. Quando si esegue lo script, viene visualizzato un messaggio simile a:

  ```none
  To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B3V3NLWRF to authenticate.
  ```
  
   Aprire un browser e immettere il codice per eseguire l'autenticazione. Non è necessario che il browser sia in esecuzione nel server in cui si effettua l'onboarding, potrebbe trovarsi in un altro computer, ad esempio il computer portatile.

1. Per eseguire l'autenticazione in modo non interattivo, seguire la procedura descritta in [Creare un'entità servizio](quickstart-onboard-powershell.md#create-a-service-principal-for-onboarding-at-scale) e modificare lo script generato dal portale.

> [!NOTE]
> Se si utilizza Internet Explorer sul server quando si esegue l'accesso per la prima volta, verrà visualizzato un errore. È possibile riaprire il browser e ripetere l'operazione.

## <a name="execute-the-script-on-target-nodes"></a>Eseguire lo script nei nodi di destinazione

Accedere a ogni nodo ed eseguire lo script generato dal portale. Al termine dell'esecuzione dello script, passare al portale di Azure per verificare che il server sia stato connesso correttamente.

![Onboarding eseguito correttamente](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>Eseguire la pulizia

Per disconnettere un computer da Azure Arc per server, è necessario eseguire due passaggi.

1. Selezionare il computer alla voce [Portale](https://aka.ms/hybridmachineportal), fare clic sui puntini di sospensione (`...`) e selezionare **Eliminare**.
1. Disinstallare l'agente dal computer.

   In Windows è possibile usare il pannello di controllo "App e funzionalità" per disinstallare l'agente.
  
  ![App e funzionalità](./media/quickstart-onboard/apps-and-features.png)

   Per uno script per la disinstallazione, è possibile usare l'esempio seguente, che recupera **PackageId** e disinstalla l'agente con `msiexec /X`.

   Nella chiave del Registro di sistema `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` trovare **PackageId**. È quindi possibile disinstallare l'agente usando `msiexec`.

   L'esempio seguente dimostra come disinstallare l'agente.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /Quiet /X "$($_.PsChildName)"}
   ```

   In Linux eseguire il comando seguente per disinstallare l'agente.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Assegnare un criterio ai computer connessi](../../governance/policy/assign-policy-portal.md)