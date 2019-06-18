---
title: Estensione Desired State Configuration (DSC) con modelli di Azure Resource Manager
description: Informazioni sulla definizione del modello di Resource Manager per l'estensione Desired State Configuration (DSC) in Azure.
services: virtual-machines-windows
author: bobbytreed
manager: carmonm
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/05/2018
ms.author: robreed
ms.openlocfilehash: 1bcec37e7642ae0cb5bd68de1426c8cc62085d38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61475525"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Estensione Desired State Configuration (DSC) con modelli di Azure Resource Manager

Questo articolo descrive il modello di Azure Resource Manager per il [gestore dell'estensione Desired State Configuration (DSC)](dsc-overview.md). Molti esempi usano i valori **RegistrationURL** (fornito sotto forma di stringa) e **RegistrationKey** (fornito come [PSCredential](/dotnet/api/system.management.automation.pscredential)) per eseguire l'onboarding con Automazione di Azure. Per informazioni dettagliate su come ottenere questi valori, vedere [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure - Registrazione sicura](/azure/automation/automation-dsc-onboarding#secure-registration).

> [!NOTE]
> Si potrebbero notare esempi di schema leggermente diversi. La modifica dello schema è avvenuta nella versione di ottobre 2016. Per maggiori dettagli, vedere [Aggiornamento da un formato precedente](#update-from-a-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Esempio di modello per una VM Windows

Il frammento seguente illustra la sezione del modello relativa alle **risorse**.
L'estensione DSC eredita le proprietà di estensione predefinite.
Per altre informazioni, vedere [VirtualMachineExtension class](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet) (Classe VirtualMachineExtension).

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-06-30",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "protectedSettings": {
      "Items": {
        "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
      }
    },
    "settings": {
      "Properties": [
        {
          "Name": "RegistrationKey",
          "Value": {
            "UserName": "PLACEHOLDER_DONOTUSE",
            "Password": "PrivateSettingsRef:registrationKeyPrivate"
          },
          "TypeName": "System.Management.Automation.PSCredential"
        },
        {
          "Name": "RegistrationUrl",
          "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
          "TypeName": "System.String"
        },
        {
          "Name": "NodeConfigurationName",
          "Value": "[parameters('nodeConfigurationName')]",
          "TypeName": "System.String"
        }
      ]
    }
  }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Esempio di modello per set di scalabilità di macchine virtuali Windows

Il nodo di un set di scalabilità di macchine virtuali ha una sezione **properties** con un attributo **VirtualMachineProfile, extensionProfile**.
In **extensions** aggiungere i dettagli per l'estensione DSC.

L'estensione DSC eredita le proprietà di estensione predefinite.
Per altre informazioni, vedere [VirtualMachineScaleSetExtension class](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet) (Classe VirtualMachineScaleSetExtension).

```json
"extensionProfile": {
    "extensions": [
      {
        "name": "Microsoft.Powershell.DSC",
        "properties": {
          "publisher": "Microsoft.Powershell",
          "type": "DSC",
          "typeHandlerVersion": "2.77",
          "autoUpgradeMinorVersion": true,
          "protectedSettings": {
            "Items": {
              "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
            }
          },
          "settings": {
            "Properties": [
              {
                "Name": "RegistrationKey",
                "Value": {
                  "UserName": "PLACEHOLDER_DONOTUSE",
                  "Password": "PrivateSettingsRef:registrationKeyPrivate"
                },
                "TypeName": "System.Management.Automation.PSCredential"
              },
              {
                "Name": "RegistrationUrl",
                "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
                "TypeName": "System.String"
              },
              {
                "Name": "NodeConfigurationName",
                "Value": "[parameters('nodeConfigurationName')]",
                "TypeName": "System.String"
              }
            ]
          }
        }
      }
    ]
  }
```

## <a name="detailed-settings-information"></a>Informazioni dettagliate sulle impostazioni

Usare lo schema seguente nella sezione delle **impostazioni** dell'estensione DSC di Azure in un modello di Resource Manager.

Per un elenco degli argomenti disponibili per lo script di configurazione predefinito, vedere [Script di configurazione predefinito](#default-configuration-script).

```json
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
        "downloadMappings": {
            "customWmfLocation": "http://myWMFlocation"
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
```

## <a name="details"></a>Dettagli

| Nome proprietà | Type | Descrizione |
| --- | --- | --- |
| settings.wmfVersion |string |Specifica la versione di Windows Management Framework (WMF) da installare nella macchina virtuale. Impostando questa proprietà su **latest** verrà installata la versione più recente di WMF. Attualmente, gli unici valori possibili per questa proprietà sono **4.0**, **5.0**, **5.1** e **latest**. Questi valori possibili sono soggetti ad aggiornamenti. Il valore predefinito è **latest**. |
| settings.configuration.url |string |Specifica il percorso URL da cui scaricare il file ZIP della configurazione DSC. Se l'URL specificato richiede un token di firma di accesso condiviso per l'accesso, impostare la proprietà **protectedSettings.configurationUrlSasToken** sul valore del token di firma di accesso condiviso. Questa proprietà è obbligatoria se **settings.configuration.script** o **settings.configuration.function** sono definiti. Se non viene specificato alcun valore per queste proprietà, l'estensione chiama lo script di configurazione predefinito per impostare i metadati di Gestione configurazione locale e devono essere forniti gli argomenti. |
| settings.configuration.script |string |Specifica il nome del file di script che contiene la definizione della configurazione DSC. Questo script deve trovarsi nella cartella radice del file con estensione zip che viene scaricato dall'URL specificato dalla proprietà **settings.configuration.url**. Questa proprietà è obbligatoria se **settings.configuration.url** o **settings.configuration.script** sono definiti. Se non viene specificato alcun valore per queste proprietà, l'estensione chiama lo script di configurazione predefinito per impostare i metadati di Gestione configurazione locale e devono essere forniti gli argomenti. |
| settings.configuration.function |string |Specifica il nome della configurazione DSC. La configurazione indicata deve essere inclusa nello script definito da **settings.configuration.script**. Questa proprietà è obbligatoria se **settings.configuration.url** o **settings.configuration.function** sono definiti. Se non viene specificato alcun valore per queste proprietà, l'estensione chiama lo script di configurazione predefinito per impostare i metadati di Gestione configurazione locale e devono essere forniti gli argomenti. |
| settings.configurationArguments |Collection |Definisce i parametri da passare alla configurazione DSC. Questa proprietà non è crittografata. |
| settings.configurationData.url |string |Specifica l'URL da cui scaricare il file di dati di configurazione con estensione psd1 da usare come input per la configurazione DSC. Se l'URL specificato richiede un token di firma di accesso condiviso per l'accesso, impostare la proprietà **protectedSettings.configurationDataUrlSasToken** sul valore del token di firma di accesso condiviso. |
| settings.privacy.dataCollection |string |Abilita o disabilita la raccolta di dati di telemetria. Gli unici valori possibili per questa proprietà sono **Enable**, **Disable**, **''** o **$null**. Lasciando questa proprietà vuota o con valore null verrà abilitata la raccolta di dati di telemetria. Il valore predefinito è **''** . Per altre informazioni, vedere [Azure DSC extension data collection](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) (Raccolta di dati dell'estensione DSC di Azure). |
| settings.advancedOptions.downloadMappings |Collection |Definisce i percorsi alternativi da cui scaricare WMF. Per altre informazioni, vedere [Azure DSC extension 2.8 and how to map downloads of the extension dependencies to your own location](https://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) (Estensione DSC di Azure 2.8 e come eseguire il mapping dei download delle dipendenze dell'estensione al percorso). |
| protectedSettings.configurationArguments |Collection |Definisce i parametri da passare alla configurazione DSC. Questa proprietà è crittografata. |
| protectedSettings.configurationUrlSasToken |string |Specifica il token di firma di accesso condiviso da usare per accedere all'URL definito da **settings.configuration.url**. Questa proprietà è crittografata. |
| protectedSettings.configurationDataUrlSasToken |string |Specifica il token di firma di accesso condiviso da usare per accedere all'URL definito da **settings.configurationData.url**. Questa proprietà è crittografata. |

## <a name="default-configuration-script"></a>Script di configurazione predefinito

Per altre informazioni sui valori seguenti, vedere [Configurazione di Gestione configurazione locale - Impostazioni di base](/powershell/dsc/metaconfig#basic-settings).
È possibile usare lo script di configurazione predefinito dell'estensione DSC per configurare solo le proprietà di Gestione configurazione locale elencate nella tabella seguente.

| Nome proprietà | Type | Descrizione |
| --- | --- | --- |
| protectedSettings.configurationArguments.RegistrationKey |PSCredential |Proprietà obbligatoria. Specifica la chiave che viene usata per la registrazione di un nodo nel servizio Automazione di Azure come password di un oggetto credenziale di PowerShell. Questo valore può essere individuato automaticamente usando il metodo **listkeys** per l'account di Automazione.  Vedere l'[esempio](#example-using-referenced-azure-automation-registration-values). |
| settings.configurationArguments.RegistrationUrl |string |Proprietà obbligatoria. Specifica l'URL dell'endpoint di Automazione in cui il nodo tenta di registrarsi. Questo valore può essere individuato automaticamente usando il metodo **reference** per l'account di Automazione. |
| settings.configurationArguments.NodeConfigurationName |string |Proprietà obbligatoria. Specifica la configurazione del nodo nell'account di Automazione da assegnare al nodo. |
| settings.configurationArguments.ConfigurationMode |string |Specifica la modalità di Gestione configurazione locale. Le opzioni valide sono **ApplyOnly**, **ApplyandMonitor** e **ApplyandAutoCorrect**.  Il valore predefinito è **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | Valore UInt32 | Specifica la frequenza con cui Gestione configurazione locale tenta di verificare la disponibilità di aggiornamenti con l'account di Automazione.  Il valore predefinito è **30**.  Il valore minimo è **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | Valore UInt32 | Specifica la frequenza con cui Gestione configurazione locale convalida la configurazione corrente. Il valore predefinito è **15**. Il valore minimo è **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | boolean | Specifica se un nodo può essere riavviato automaticamente se richiesto da un'operazione DSC. Il valore predefinito è **false**. |
| settings.configurationArguments.ActionAfterReboot | string | Specifica che cosa avviene dopo un riavvio durante l'applicazione di una configurazione. Le opzioni valide sono **ContinueConfiguration** e **StopConfiguration**. Il valore predefinito è **ContinueConfiguration**. |
| settings.configurationArguments.AllowModuleOverwrite | boolean | Specifica se Gestione configurazione locale sovrascrive i moduli esistenti nel nodo. Il valore predefinito è **false**. |

## <a name="settings-vs-protectedsettings"></a>Differenze tra settings e protectedSettings

Tutte le impostazioni vengono salvate in un file di testo delle impostazioni nella macchina virtuale.
Le proprietà indicate in **settings** sono proprietà pubbliche.
Le proprietà pubbliche non sono crittografate nel file di testo delle impostazioni.
Le proprietà indicate in **protectedSettings** vengono crittografate con un certificato e non vengono visualizzate in testo normale nel file delle impostazioni nella macchina virtuale.

Se la configurazione richiede credenziali, è possibile includerle in **protectedSettings**:

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
               "userName": "UsernameValue1",
               "password": "PasswordValue1"
        }
    }
}
```

## <a name="example-configuration-script"></a>Script di configurazione di esempio

L'esempio seguente illustra il comportamento predefinito per l'estensione DSC, ovvero fornire le impostazioni dei metadati a Gestione configurazione locale ed effettuare la registrazione per il servizio Automation DSC.
Gli argomenti di configurazione sono necessari
e vengono passati allo script di configurazione predefinito per impostare i metadati di Gestione configurazione locale.

```json
"settings": {
    "RegistrationUrl" : "[parameters('registrationUrl1')]",
    "NodeConfigurationName" : "nodeConfigurationNameValue1"
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "registrationKey"
        }
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Esempio di uso dello script di configurazione in Archiviazione di Azure

L'esempio seguente è tratto da [Introduzione al gestore dell'estensione DSC (Desired State Configuration) di Azure](dsc-overview.md).
Questo esempio usa modelli di Resource Manager anziché i cmdlet per distribuire l'estensione.
Salvare la configurazione di IisInstall.ps1, inserirla in un file con estensione zip (esempio: `iisinstall.zip`) e quindi caricare il file in un URL accessibile.
Questo esempio usa l'archiviazione BLOB di Azure, ma è possibile scaricare file ZIP da qualsiasi percorso.

Nel modello di Resource Manager il codice seguente indica alla VM di scaricare il file corretto e quindi eseguire la funzione PowerShell appropriata:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/iisinstall.zip",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="example-using-referenced-azure-automation-registration-values"></a>Esempio d'uso dei valori di registrazione di Automazione di Azure di riferimento

L'esempio seguente ottiene i valori **RegistrationUrl** e **RegistrationKey** facendo riferimento alle proprietà dell'account di Automazione di Azure e usando il metodo **listkeys** per recuperare la chiave primaria (0).  In questo esempio, i parametri **automationAccountName** e **NodeConfigName** sono stati forniti al modello.

```json
"settings": {
    "RegistrationUrl" : "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
    "NodeConfigurationName" : "[parameters('NodeConfigName')]"
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-01-15').Keys[0].value]"
        }
    }
}
```

## <a name="update-from-a-previous-format"></a>Aggiornamento da un formato precedente

Eventuali impostazioni presenti in un formato precedente dell'estensione (e che hanno le proprietà pubbliche **ModulesUrl**, **ModuleSource**, **ModuleVersion**, **ConfigurationFunction**, **SasToken** o **Properties**) vengono automaticamente adattate al formato corrente dell'estensione
ed eseguite come in precedenza.

Lo schema seguente mostra l'aspetto dello schema delle impostazioni precedente:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties": {
        "ParameterToConfigurationFunction1": "Value1",
        "ParameterToConfigurationFunction2": "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1"
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": {
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Il formato precedente si adatta al formato corrente come segue:

| Nome proprietà corrente | Equivalente nello schema precedente |
| --- | --- |
| settings.wmfVersion |settings.wmfVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |Prima parte di settings.ConfigurationFunction (prima di \\\\) |
| settings.configuration.function |Seconda parte di settings.ConfigurationFunction (dopo \\\\) |
| settings.configuration.module.name | settings.ModuleSource |
| settings.configuration.module.version | settings.ModuleVersion |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri (senza token di firma di accesso condiviso) |
| settings.privacy.dataCollection |settings.Privacy.dataCollection |
| settings.advancedOptions.downloadMappings |settings.advancedOptions.downloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |Token di firma di accesso condiviso di protectedSettings.DataBlobUri |

## <a name="troubleshooting"></a>risoluzione dei problemi

Di seguito sono riportati alcuni degli errori che possono verificarsi e la relativa soluzione.

### <a name="invalid-values"></a>Valori non validi

"Privacy.dataCollection è '{0}'.
Gli unici valori possibili sono '', 'Enable' e 'Disable'".
"WmfVersion è '{0}'.
Gli unici valori possibili sono: ... e "latest".

**Problema**: Un valore specificato non è consentito.

**Soluzione**: Modificare il valore non valido su un valore valido.
Per altre informazioni, vedere la tabella in [Dettagli](#details).

### <a name="invalid-url"></a>URL non valido

"ConfigurationData.url è '{0}'. URL non valido" "DataBlobUri è '{0}'. URL non valido" "Configuration.url è '{0}'. URL non valido"

**Problema**: Un URL specificato non è valido.

**Soluzione**: Controllare tutti gli URL specificati.
Assicurarsi che tutti gli URL si risolvano in percorsi validi a cui l'estensione può accedere nel computer remoto.

### <a name="invalid-registrationkey-type"></a>Tipo RegistrationKey non valido

"Tipo non valido per il parametro RegistrationKey di tipo PSCredential"

**Problema**: Il *RegistrationKey* valore protectedSettings.configurationArguments non può essere fornito per qualsiasi tipo diverso da PSCredential.

**Soluzione**: Modificare la voce protectedSettings.configurationArguments per RegistrationKey a un tipo PSCredential usando il formato seguente:

```json
"configurationArguments": {
    "RegistrationKey": {
        "userName": "NOT_USED",
        "Password": "RegistrationKey"
    }
}
```

### <a name="invalid-configurationargument-type"></a>Tipo ConfigurationArgument non valido

"Tipo configurationArguments {0} non valido"

**Problema**: Il *ConfigurationArguments* proprietà non si risolve in un **tabella Hash** oggetto.

**Soluzione**: Verificare i *ConfigurationArguments* proprietà una **tabella Hash**.
Seguire il formato indicato negli esempi precedenti. Prestare attenzione alle virgolette, alle virgole e alle parentesi graffe.

### <a name="duplicate-configurationarguments"></a>ConfigurationArguments duplicato

"Trovati argomenti duplicati '{0}' in configurationArguments pubblici e protetti"

**Problema**: Il *ConfigurationArguments* nelle impostazioni pubbliche e il *ConfigurationArguments* nelle impostazioni protette hanno proprietà con lo stesso nome.

**Soluzione**: Rimuovere una delle proprietà duplicate.

### <a name="missing-properties"></a>Proprietà mancanti

"settings.Configuration.function richiede che venga specificato settings.configuration.url o settings.configuration.module"

"settings.Configuration.url richiede che venga specificato settings.configuration.script"

"settings.Configuration.script richiede che venga specificato settings.configuration.url"

"settings.Configuration.url richiede che venga specificato settings.configuration.function"

"protectedSettings.ConfigurationUrlSasToken richiede che venga specificato settings.configuration.url"

"protectedSettings.ConfigurationDataUrlSasToken richiede che venga specificato settings.configurationData.url"

**Problema**: Una proprietà definita richiede un'altra proprietà mancante.

**Soluzioni**:

- Specificare la proprietà mancante.
- Rimuovere la proprietà che richiede la proprietà mancante.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull'[uso dei set di scalabilità di macchine virtuali con l'estensione DSC di Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- Maggiori dettagli sulla [gestione delle credenziali protette di DSC](dsc-credentials.md).
- [Introduzione al gestore dell'estensione DSC (Desired State Configuration) di Azure](dsc-overview.md).
- Per altre informazioni su PowerShell DSC, passare al [centro di documentazione di PowerShell](/powershell/dsc/overview).
