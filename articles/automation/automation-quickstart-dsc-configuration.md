---
title: Avvio rapido di Azure - Configurare una VM con Desired State Configuration | Microsoft Docs
description: Questo articolo illustra come iniziare a configurare una VM con Desired State Configuration.
services: automation
ms.subservice: dsc
keywords: dsc, configurazione, automazione
ms.date: 11/06/2018
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: e7fec2bee61844ac294e5463bd5bc88ec3fb5e98
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186079"
---
# <a name="configure-a-vm-with-desired-state-configuration"></a>Configurare una VM con Desired State Configuration

Abilitando Automation DSC (Desired State Configuration) per Azure, è possibile gestire e monitorare le configurazioni dei server Windows e Linux tramite Desired State Configuration (DSC). Le configurazioni che deviano da uno stato desiderato possono essere identificate e corrette automaticamente. Questo articolo di avvio rapido illustra la procedura per abilitare una macchina virtuale Linux e distribuire uno stack LAMP usando State Configuration di Automazione di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione introduttiva, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/).
* Un account di automazione di Azure. Per istruzioni sulla creazione di un account RunAs di Automazione di Azure, vedere [Autenticare runbook con account RunAs di Azure](./manage-runas-account.md).
* Una VM di Azure Resource Manager (non classica) che esegue Red Hat Enterprise Linux, CentOS o Oracle Linux. Per istruzioni sulla creazione di una VM, vedere [Creare la prima macchina virtuale Linux nel portale di Azure](../virtual-machines/linux/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere ad Azure all'indirizzo https://portal.azure.com.

## <a name="enable-a-virtual-machine"></a>Abilitare una macchina virtuale

Ci sono molti metodi diversi per abilitare un computer per la funzionalità State Configuration. Questo articolo di avvio rapido descrive come abilitare la funzionalità per una VM usando un account di Automazione. È possibile ottenere altre informazioni sui diversi metodi per abilitare i computer per State Configuration leggendo [Abilitare i computer per la gestione con State Configuration di Automazione di Azure](./automation-dsc-onboarding.md).

1. Nel riquadro sinistro del portale di Azure selezionare **Account di Automazione**. Se non è visibile nel riquadro sinistro, fare clic su **Tutti i servizi** e cercarlo nella visualizzazione risultante.
1. Nell'elenco selezionare un account di Automazione.
1. Nel riquadro sinistro dell'account di Automazione selezionare **Configurazione dello stato (DSC)** .
2. Fare clic su **Aggiungi** per aprire la pagina di selezione della macchina virtuale.
3. Trovare la macchina virtuale per cui abilitare DSC. È possibile usare il campo di ricerca e le opzioni di filtro per trovare una macchina virtuale specifica.
4. Fare clic sulla macchina virtuale e quindi su **Connetti**
5. Selezionare le impostazioni DSC appropriate per la macchina virtuale. Se è già stata preparata una configurazione, è possibile specificarla come `Node Configuration Name`. È possibile impostare la [modalità di configurazione](/powershell/scripting/dsc/managing-nodes/metaConfig) per controllare il comportamento della configurazione per il computer.
6. Fare clic su **OK**. Mentre l'estensione DSC viene distribuita nella macchina virtuale, lo stato viene visualizzato come `Connecting`.

![Abilitazione di una macchina virtuale di Azure per DSC](./media/automation-quickstart-dsc-configuration/dsc-onboard-azure-vm.png)

## <a name="import-modules"></a>Importare i moduli

I moduli contengono le risorse DSC e molti sono disponibili in [PowerShell Gallery](https://www.powershellgallery.com). Le risorse usate nelle configurazioni devono essere importate nell'account di Automazione prima della compilazione. Per questa esercitazione, è necessario il modulo denominato **nx**.

1. Nel riquadro sinistro dell'account di Automazione selezionare **Raccolta di moduli** in **Risorse condivise**.
1. Cercare il modulo da importare digitandone parte del nome: `nx`.
1. Fare clic sul modulo da importare.
1. Fare clic su **Importa**.

![Importazione di un modulo DSC](./media/automation-quickstart-dsc-configuration/dsc-import-module-nx.png)

## <a name="import-the-configuration"></a>Importare la configurazione

Questa guida introduttiva usa una configurazione DSC che configura il server HTTP Apache, MySQL e PHP nel computer. Vedere [Configurazioni DSC](/powershell/scripting/dsc/configurations/configurations).

In un editor di testo digitare quanto segue e salvare il file in locale con il nome **AMPServer.ps1**.

```powershell-interactive
configuration LAMPServer {
   Import-DSCResource -module "nx"

   Node localhost {

        $requiredPackages = @("httpd","mod_ssl","php","php-mysql","mariadb","mariadb-server")
        $enabledServices = @("httpd","mariadb")

        #Ensure packages are installed
        ForEach ($package in $requiredPackages){
            nxPackage $Package{
                Ensure = "Present"
                Name = $Package
                PackageManager = "yum"
            }
        }

        #Ensure daemons are enabled
        ForEach ($service in $enabledServices){
            nxService $service{
                Enabled = $true
                Name = $service
                Controller = "SystemD"
                State = "running"
            }
        }
   }
}
```

Per importare la configurazione:

1. Nel riquadro sinistro dell'account di Automazione selezionare **Configurazione dello stato (DSC)** e quindi fare clic sulla scheda **Configurazioni**.
2. Fare clic su **+ Aggiungi**.
3. Selezionare il file di configurazione salvato nel passaggio precedente.
4. Fare clic su **OK**.

## <a name="compile-a-configuration"></a>Compilare una configurazione

Una configurazione DSC deve essere compilata in una configurazione nodo (documento MOF) affinché possa essere assegnata a un nodo. La compilazione convalida la configurazione e consente l'input dei valori dei parametri. Per altre informazioni sulla compilazione di una configurazione, vedere [Compilazione di configurazioni in State Configuration](automation-dsc-compile.md).

1. Nel riquadro sinistro dell'account di Automazione selezionare **Configurazione dello stato (DSC)** e quindi fare clic sulla scheda **Configurazioni**.
1. Selezionare la configurazione `LAMPServer`.
1. Nelle opzioni di menu selezionare **Compila** e quindi **Sì**.
1. Nella visualizzazione Configurazione è visibile un nuovo processo di compilazione accodato. Dopo che il processo è stato completato, si è pronti per continuare con il passaggio successivo. Se sono presenti errori, è possibile fare clic sul processo di compilazione per i dettagli.

## <a name="assign-a-node-configuration"></a>Assegnare una configurazione del nodo

È possibile assegnare una configurazione del nodo compilata a un nodo DSC. L'assegnazione applica la configurazione al computer e monitora o corregge automaticamente eventuali deviazioni da tale configurazione.

1. Nel riquadro sinistro dell'account di Automazione selezionare **State Configuration (DSC)** e quindi fare clic sulla scheda **Nodi**.
1. Selezionare il nodo a cui assegnare una configurazione.
1. Fare clic su **Assegna configurazione nodo**
1. Selezionare l'oggetto `LAMPServer.localhost` della configurazione del nodo e fare clic su **OK**. State Configuration ora assegna la configurazione compilata al nodo, il cui stato cambia in `Pending`. Durante il successivo controllo periodico, il nodo recupera la configurazione, la applica e ne segnala lo stato. A seconda delle impostazioni, possono essere necessari fino a 30 minuti prima che il nodo recuperi la configurazione. 
1. Per forzare un controllo immediato, è possibile eseguire il comando seguente in locale sulla macchina virtuale Linux: `sudo /opt/microsoft/dsc/Scripts/PerformRequiredConfigurationChecks.py`

![Assegnazione di una configurazione del nodo](./media/automation-quickstart-dsc-configuration/dsc-assign-node-configuration.png)

## <a name="view-node-status"></a>Visualizzare lo stato del nodo

È possibile visualizzare lo stato di tutti i nodi gestiti da State Configuration nell'account di Automazione. Per visualizzare tali informazioni, scegliere **State Configuration (DSC)** e fare clic sulla scheda **Nodi**. È possibile filtrare la visualizzazione per stato, configurazione del nodo o ricerca del nome.

![Stato del nodo DSC](./media/automation-quickstart-dsc-configuration/dsc-node-status.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata abilitata una VM Linux per State Configuration ed è stata creata una configurazione per uno stack LAMP che è stata quindi distribuita nella VM. Per informazioni su come usare Automation DSC per abilitare la distribuzione continua, passare all'articolo:

> [!div class="nextstepaction"]
> [Configurare la distribuzione continua con Chocolatey](./automation-dsc-cd-chocolatey.md)
