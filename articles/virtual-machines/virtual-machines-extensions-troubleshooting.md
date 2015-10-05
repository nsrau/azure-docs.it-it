<properties
   pageTitle="Risoluzione degli errori delle estensioni della macchina virtuale di Azure | Microsoft Azure"
   description="Informazioni sulla risoluzione degli errori delle estensioni della macchina virtuale di Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# Risoluzione degli errori delle estensioni della macchina virtuale di Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Questo articolo illustra l’utilizzo del modello di distribuzione Gestione risorse.

## Panoramica dei modelli di Gestione risorse di Azure

I modelli di Gestione risorse di Azure consentono di specificare in modo dichiarativo l'infrastruttura IaaS di Azure in linguaggio JSON definendo le dipendenze tra risorse.


Fare clic sull'articolo [Creazione di modelli di estensione](virtual-machines-extensions-authoring-templates.md) per ulteriori informazioni sulla creazione di modelli per l'utilizzo delle estensioni.

In questo articolo verrà illustrata la risoluzione dei problemi relativi ad alcuni dei più comuni errori delle estensioni della macchina virtuale.

## Visualizzazione dello stato dell’estensione:
I modelli di gestione risorse di Azure possono essere eseguiti da Azure Powershell o Azure CLI. Una volta che il modello viene eseguito, è possibile visualizzare lo stato dell'estensione da Esplora risorse di Azure o dagli strumenti da riga di comando. Di seguito sono riportati alcuni esempi:

Interfaccia della riga di comando di Azure:

      azure vm get-instance-view

Azure Powershell:

      Get-AzureVM -ResourceGroupName $RGName -Name $vmName -Status

Di seguito è riportato l'output di esempio:

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## Risoluzione degli errori delle Estensioni:

### Eseguire nuovamente l'estensione nella macchina virtuale:

Se si eseguono gli script nella macchina virtuale utilizzando l'estensione dello Script personalizzato, è possibile riscontrare in alcuni casi un errore in cui la creazione della macchina virtuale è riuscita ma lo script ha avuto esito negativo. In queste condizioni, il metodo consigliato per risolvere il problema consiste nel rimuovere l'estensione e eseguire nuovamente il modello. Nota: In futuro, questa funzionalità potrebbe essere migliorata in modo da eliminare la necessità di disinstallazione dell'estensione.

#### Rimuovere l'estensione da Azure CLI:

      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

Dove "publsher-name" corrisponde al tipo di estensione dall'output di "azure vm get-instance-view" e nome è il nome della risorsa di estensione dal modello

#### Rimuovere l'estensione da Azure Powershell:

    Remove-AzureVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Una volta rimossa l'estensione, il modello può essere eseguito nuovamente per eseguire gli script nella macchina virtuale.

<!---HONumber=Sept15_HO4-->