---
title: Risoluzione dei problemi della configurazione dello stato desiderato (DSC) di Automazione di Azure
description: Questo articolo contiene informazioni sulla risoluzione dei problemi di configurazione dello stato desiderato (DSC, Desired State Configuration)
services: automation
ms.service: automation
ms.subservice: ''
author: bobbytreed
ms.author: robreed
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ab9a39cfba082ea4c4d1cc6c29764619011d8cb8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231544"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Risolvere i problemi della configurazione dello stato desiderato (DSC)

Questo articolo contiene informazioni sulla risoluzione dei problemi della configurazione dello stato desiderato (DSC, Desired State Configuration).

## <a name="steps-to-troubleshoot-desired-state-configuration-dsc"></a>Steps to troubleshoot Desired State Configuration (DSC)

When you have errors compiling or deploying configurations in Azure State Configuration, here are a few steps to help you diagnose the issue.

1. **Ensure your configuration compiles successfully on your local machine:**  Azure State Configuration is built on PowerShell DSC. You can find the documentation for the DSC language and syntax in the [PowerShell DSC Docs](https://docs.microsoft.com/powershell/scripting/overview).

   By compiling your DSC configuration on your local machine you can discover and resolve common errors, such as:

   - **Missing Modules**
   - **Syntax Errors**
   - **Logic Errors**

2. **View DSC logs on your Node:** If your configuration compiles successfully, but fails when applied to a Node, you can find detailed information in the logs. For information about where to find DSC logs, see [Where are the DSC Event Logs](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

   Furthermore, the [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) can assist you in parsing detailed information from the DSC logs. If you contact support, they will require these logs to diagnose your issue.

   You can install **xDscDiagnostics** on your local machine using the instructions found under [Install the stable version module](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

   To install **xDscDiagnostics** on your Azure machine, you can use [az vm run-command](/cli/azure/vm/run-command) or [Invoke-AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). You can also use the **Run command** option from the portal, by following the steps found in [Run PowerShell scripts in your Windows VM with Run Command](../../virtual-machines/windows/run-command.md).

   For information on using **xDscDiagnostics**, see [Using xDscDiagnostics to analyze DSC logs](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs), as well as [xDscDiagnostics Cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).
3. **Ensure your Nodes and Automation workspace have the required modules:** Desired State Configuration depends on modules installed on the Node.  When using Azure Automation State Configuration, import any required modules into your automation account using the steps listed in [Import Modules](../shared-resources/modules.md#import-modules). Configurations can also have a dependency on specific versions of modules.  For more information, see, [Troubleshoot Modules](shared-resources.md#modules).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Errori comuni durante l'utilizzo di Desired State Configuration (DSC)

### <a name="unsupported-characters"></a>Scenario: A configuration with special characters cannot be deleted from the portal

#### <a name="issue"></a>Problema

When attempting to delete a DSC configuration from the portal, you see the following error:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Causa

This error is a temporary issue that is planned to be resolved.

#### <a name="resolution"></a>Risoluzione

* Use the Az Cmdlet "Remove-AzAutomationDscConfiguration" to delete the configuration.
* The documentation for this cmdlet hasn't been updated yet.  Until then, refer to the documentation for the AzureRM module.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>Scenario: Failed to register Dsc Agent

#### <a name="issue"></a>Problema

When attempting to run `Set-DscLocalConfigurationManager` or another DSC cmdlet you receive the error:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

#### <a name="cause"></a>Causa

This error is normally caused by a firewall, the machine being behind a proxy server, or other network errors.

#### <a name="resolution"></a>Risoluzione

Verify your machine has access to the proper endpoints for Azure Automation DSC and try again. For a list of ports and addresses needed, see [network planning](../automation-dsc-overview.md#network-planning)

### <a name="failed-not-found"></a>Scenario: Lo stato del nodo indica che non è riuscito con errore "Non trovato"

#### <a name="issue"></a>Problema

Un report del nodo indica lo stato **non riuscito** e contiene l'errore seguente:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Causa

Questo errore si verifica in genere quando al nodo viene assegnato un nome di configurazione, ad esempio ABC, anziché un nome di configurazione nodo, ad esempio ABC.WebServer.

#### <a name="resolution"></a>Risoluzione

* Make sure that you're assigning the node with "node configuration name" and not the "configuration name".
* È possibile assegnare a un nodo una configurazione di nodo usando il portale di Azure o un cmdlet di PowerShell.

  * To assign a node configuration to a node using Azure portal, open the **DSC Nodes** page, then select a node and click on **Assign node configuration** button.
  * To assign a node configuration to a node using PowerShell cmdlet, use **Set-AzureRmAutomationDscNode** cmdlet

### <a name="no-mof-files"></a>Scenario: Non sono state prodotte configurazioni di nodo (file MOF) durante la compilazione di una configurazione

#### <a name="issue"></a>Problema

Il processo di compilazione DSC viene sospeso con l'errore seguente:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Causa

Quando l'espressione che segue la parola chiave **Node** nella configurazione DSC restituisce `$null`, non vengono prodotte configurazioni di nodo.

#### <a name="resolution"></a>Risoluzione

una qualsiasi delle soluzioni seguenti consente di correggere il problema:

* Make sure that the expression next to the **Node** keyword in the configuration definition isn't evaluating to $null.
* Se durante la compilazione della configurazione si passano dei dati di configurazione, verificare di specificare i valori previsti necessari per la configurazione da [ConfigurationData](../automation-dsc-compile.md).

### <a name="dsc-in-progress"></a>Scenario: Il report relativo al nodo DSC rimane bloccato nello stato "in corso"

#### <a name="issue"></a>Problema

L'output dell'agente DSC è il seguente:

```error
No instance found with given property values
```

#### <a name="cause"></a>Causa

È stato eseguito l'aggiornamento della versione di WMF e WMI è stato danneggiato.

#### <a name="resolution"></a>Risoluzione

To fix the issue, follow the instructions in the [DSC known issues and limitations](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc) article.

### <a name="issue-using-credential"></a>Scenario: Non è possibile usare le credenziali in una configurazione DSC

#### <a name="issue"></a>Problema

Il processo di compilazione DSC è stato sospeso con l'errore seguente:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Causa

You've used a credential in a configuration but didn’t provide proper **ConfigurationData** to set **PSDscAllowPlainTextPassword** to true for each node configuration.

#### <a name="resolution"></a>Risoluzione

* Make sure to pass in the proper **ConfigurationData** to set **PSDscAllowPlainTextPassword** to true for each node configuration that is mentioned in the configuration. Per altre informazioni, vedere la sezione relativa agli [asset in Automation DSC per Azure](../automation-dsc-compile.md#working-with-assets-in-azure-automation-during-compilation).

### <a name="failure-processing-extension"></a>Scenario: Onboarding from dsc extension, "Failure processing extension" error

#### <a name="issue"></a>Problema

When onboarding using DSC extension, a failure occurs containing the error:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Causa

This error typically occurs when the node is assigned a node configuration name that does not exist in the service.

#### <a name="resolution"></a>Risoluzione

* Make sure that you're assigning the node with a node configuration name that exactly matches the name in the service.
* You can choose to not include the node configuration name, which will result in onboarding the node but not assigning a node configuration

### <a name="failure-linux-temp-noexec"></a>Scenario: Applying a configuration in Linux, a failure occurs with a general error

#### <a name="issue"></a>Problema

When applying a configuration in Linux, a failure occurs containing the error:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Causa

Customers have identified that if the `/tmp` location is set to `noexec`, the current version of DSC will fail to apply configurations.

#### <a name="resolution"></a>Risoluzione

* Remove the `noexec` option from the `/tmp` location.

### <a name="compilation-node-name-overlap"></a>Scenario: Node configuration names that overlap could result in bad release

#### <a name="issue"></a>Problema

If a single configuration script is used to generate multiple node configurations, and some of the node configurations have a name that is a subset of others, an issue in the compilation service could result in assigning the wrong configuration.  This only occurs when using a single script to generate configurations with configuration data per node, and only when the name overlap occurs at the beginning of the string.

Example, if a single configuration script is used to generate configurations based on node data passed as a hashtable using cmdlets, and the node data includes a server named "server" and "1server".

#### <a name="cause"></a>Causa

Known issue with the compilation service.

#### <a name="resolution"></a>Risoluzione

The best workaround would be to compile locally or in a CI/CD pipeline and upload the MOF files directly to the service.  If compilation in the service is a requirement, the next best workaround would be to split the compilation jobs so there is no overlap in names.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/)
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al sito del [supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Richiedi supporto**.
