---
title: Gestore dell'estensione DSC (Desired State Configuration) di Azure | Microsoft Docs
description: Caricare e applicare una configurazione DSC PowerShell in una macchina virtuale di Azure tramite l'estensione DSC
services: virtual-machines-windows
documentationcenter: ''
author: eshaparmar
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: esparmar
ms.openlocfilehash: b34314951980f7dbe2269119883dec52a90a0587
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944813"
---
# <a name="powershell-dsc-extension"></a>Estensione DSC di PowerShell

## <a name="overview"></a>Panoramica

L'estensione DSC di PowerShell per Windows è pubblicata e supportata da Microsoft. L'estensione carica e applica una configurazione DSC di PowerShell in una macchina virtuale di Azure. L'estensione DSC esegue una chiamata in PowerShell DSC per applicare la configurazione DSC ricevuta nella VM. Questo documento descrive in dettaglio le piattaforme, le configurazioni e le opzioni di distribuzione supportate per l'estensione macchina virtuale DSC per Windows.

## <a name="prerequisites"></a>prerequisiti

### <a name="operating-system"></a>Sistema operativo

L'estensione DSC supporta i seguenti sistemi operativi

Windows Server 2016, Windows Server 2012R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1

### <a name="internet-connectivity"></a>Connettività Internet

Per distribuire l'estensione DSC per Windows, è necessario che la macchina virtuale di destinazione sia connessa a Internet. 

## <a name="extension-schema"></a>Schema dell'estensione

Il JSON seguente illustra lo schema per la sezione delle impostazioni dell'estensione DSC in un modello di Azure Resource Manager. 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.73",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "wmfVersion": "latest",
        "configuration": {
            "url": "http://validURLToConfigLocation",
            "script": "ConfigurationScript.ps1",
            "function": "ConfigurationFunction"
        },
        "configurationArguments": {
            "argument1": "Value1",
            "argument2": "Value2"
        },
        "configurationData": {
            "url": "https://foo.psd1"
        },
        "privacy": {
            "dataCollection": "enable"
        },
        "advancedOptions": {
            "forcePullAndApply": false
            "downloadMappings": {
                "specificDependencyKey": "https://myCustomDependencyLocation"
            }
        } 
    },
    "protectedSettings": {
        "configurationArguments": {
            "parameterOfTypePSCredential1": {
                "userName": "UsernameValue1",
                "password": "PasswordValue1"
            },
            "parameterOfTypePSCredential2": {
                "userName": "UsernameValue2",
                "password": "PasswordValue2"
            }
        },
        "configurationUrlSasToken": "?g!bber1sht0k3n",
        "configurationDataUrlSasToken": "?dataAcC355T0k3N"
    }
  }
}
```

### <a name="property-values"></a>Valori delle proprietà

| NOME | Valore/Esempio | Tipo di dati |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Powershell.DSC | stringa |
| type | DSC | stringa |
| typeHandlerVersion | 2.73 | int |

### <a name="settings-property-values"></a>Valori delle proprietà delle impostazioni

| NOME | Tipo di dati | DESCRIZIONE
| ---- | ---- | ---- |
| settings.wmfVersion | stringa | Specifica la versione di Windows Management Framework da installare nella macchina virtuale. Se si imposta questa proprietà su "più recente", verrà installata la versione più recente di WMF. Gli unici valori attuali possibili per questa proprietà sono "4.0", "5.0" e "più recente". Questi valori possibili sono soggetti ad aggiornamenti. Il valore predefinito è "più recente". |
| settings.configuration.url | stringa | Specifica il percorso URL da cui scaricare il file ZIP della configurazione DSC. Se l'URL specificato richiede un token di firma di accesso condiviso per accedere, sarà necessario impostare la proprietà protectedSettings.configurationUrlSasToken sul valore del token di firma di accesso condiviso. Questa proprietà è obbligatoria se settings.configuration.script e/o settings.configuration.function sono definiti.
| settings.configuration.script | stringa | Specifica il nome del file di script che contiene la definizione della configurazione DSC. Questo script deve trovarsi nella cartella radice del file ZIP scaricato dall'URL specificato dalla proprietà configuration.url. Questa proprietà è obbligatoria se settings.configuration.url e/o settings.configuration.script sono definiti.
| settings.configuration.function | stringa | Specifica il nome della configurazione DSC. La configurazione indicata deve essere contenuta nello script definito da configuration.script. Questa proprietà è obbligatoria se settings.configuration.url e/o settings.configuration.function sono definiti.
| settings.configurationArguments | Raccolta | Definisce i parametri da passare alla configurazione DSC. Questa proprietà non verrà crittografata.
| settings.configurationData.url | stringa | Specifica l'URL da cui scaricare il file di dati di configurazione con estensione pds1 da usare come input per la configurazione DSC. Se l'URL specificato richiede un token di firma di accesso condiviso per accedere, sarà necessario impostare la proprietà protectedSettings.configurationDataUrlSasToken sul valore del token di firma di accesso condiviso.
| settings.privacy.dataEnabled | stringa | Abilita o disabilita la raccolta di dati di telemetria. Gli unici valori possibili per questa proprietà sono "Abilita", "Disabilita", '' o $null. Lasciando questa proprietà vuota o con valore null verrà abilitata la raccolta di dati di telemetria.
| settings.advancedOptions.forcePullAndApply | Booleano | Abilita l'estensione DSC per aggiornare e applicare le configurazioni DSC quando la modalità di aggiornamento è su Pull.
| settings.advancedOptions.downloadMappings | Raccolta | Definisce posizioni alternative per scaricare le dipendenze, ad esempio WMF e .NET

### <a name="protected-settings-property-values"></a>Valori di proprietà delle impostazioni protette

| NOME | Tipo di dati | DESCRIZIONE
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | stringa | Definisce i parametri da passare alla configurazione DSC. Questa proprietà verrà crittografata. |
| protectedSettings.configurationUrlSasToken | stringa | Specifica il token di firma di accesso condiviso per accedere all'URL definito da configuration.url. Questa proprietà verrà crittografata. |
| protectedSettings.configurationDataUrlSasToken | stringa | Specifica il token di firma di accesso condiviso per accedere all'URL definito da configurationData.url. Questa proprietà verrà crittografata. |


## <a name="template-deployment"></a>Distribuzione del modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. I modelli rappresentano la scelta migliore quando si distribuiscono una o più macchine virtuali per cui è necessaria una configurazione post-distribuzione. Un esempio di modello di Resource Manager che include l'estensione macchina virtuale Agente OMS è disponibile nella [raccolta di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/tree/052db5feeba11f85d57f170d8202123511f72044/dsc-extension-iis-server-windows-vm). 

La configurazione JSON per un'estensione macchina virtuale può essere annidata nella risorsa della macchina virtuale o posizionata nel livello radice o nel livello superiore di un modello JSON di Gestione risorse. Il posizionamento della configurazione JSON influisce sul valore del nome e del tipo di risorsa. 

Quando la risorsa di estensione viene nidificata, JSON viene inserito nell'oggetto `"resources": []` della macchina virtuale. Quando si posiziona l'estensione JSON nella radice del modello, il nome della risorsa include un riferimento alla macchina virtuale padre e il tipo riflette la configurazione annidata.  


## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure può essere usata per distribuire l'estensione macchina virtuale Agente OMS in una macchina virtuale esistente. Sostituire la chiave OMS e l'ID OMS con i dati presenti nello spazio di lavoro OMS. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.73 --protected-settings '{}' \
  --settings '{}'
```

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite l'interfaccia della riga di comando di Azure. Per visualizzare lo stato di distribuzione delle estensioni per una determinata VM, eseguire il comando seguente nell'interfaccia della riga di comando di Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Il pacchetto di estensione viene scaricato e distribuito in questa posizione nella macchina virtuale di Azure
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

Il file di stato dell'estensione contiene lo stato secondario e i codici di esito positivo/di errore dello stato insieme all'errore dettagliato e alla descrizione per ogni esecuzione dell'estensione.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

I log di output dell'estensione vengono registrati nella directory seguente:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Codici di errore e relativi significati

| Codice di errore | Significato | Azione possibile |
| :---: | --- | --- |
| 1000 | Errore generico | Il messaggio per questo errore viene fornito dall'eccezione specifica nei registri di estensione |
| 52 | Errore di installazione dell'estensione | Il messaggio per questo errore viene fornito dall'eccezione specifica |
| 1002 | Errore di installazione WMF | Si è verificato un errore durante l'installazione di WMF. |
| 1004 | Pacchetto Zip non valido | Zip non valido; errore durante la decompressione del file zip |
| 1100 | Errore argomento | Indica un problema nell'input fornito dall'utente. Il messaggio per l'errore viene fornito dall'eccezione specifica|


### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).