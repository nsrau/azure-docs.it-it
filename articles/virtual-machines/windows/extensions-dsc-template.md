---
title: Estensione Desired State Configuration (DSC) con modelli di Azure Resource Manager | Microsoft Docs
description: Definizione del modello di Resource Manager per l'estensione Desired State Configuration in Azure
services: virtual-machines-windows
documentationcenter: 
author: mgreenegit
manager: timlt
editor: 
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: f638d1530541526316f6e409f1efd44f136992a5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Estensione Desired State Configuration (DSC) con modelli di Azure Resource Manager

Questo articolo descrive il modello di Azure Resource Manager per il [gestore estensione Desired State Configuration (DSC)](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

*Nota: si potrebbero notare esempi di schema leggermente diversi. * 
 *La modifica dello schema è avvenuta nella versione di ottobre 2016.* 
 *I dettagli sono disponibili nella sezione di questa pagina intitolata*
*[Aggiornamento dal formato precedente](##Updating-from-the-Previous-Format)*.

## <a name="template-example-for-a-windows-vm"></a>Esempio di modello per una VM Windows

Il frammento seguente illustra la sezione del modello relativa alle risorse.
L'estensione DSC eredita le proprietà di estensione predefinite, come documentato in [Classe VirtualMachineExtension](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet.)

```json
            "name": "Microsoft.Powershell.DSC",
            "type": "extensions",
             "location": "[resourceGroup().location]",
             "apiVersion": "2015-06-15",
             "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.72",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                    "configurationArguments": {
                        {
                            "Name": "RegistrationKey",
                            "Value": {
                                "UserName": "PLACEHOLDER_DONOTUSE",
                                "Password": "PrivateSettingsRef:registrationKeyPrivate"
                            },
                        },
                        "RegistrationUrl" : "[parameters('registrationUrl1')]",
                        "NodeConfigurationName" : "nodeConfigurationNameValue1"
                        }
                        },
                        "protectedSettings": {
                            "Items": {
                                        "registrationKeyPrivate": "[parameters('registrationKey1']"
                                    }
                        }
                    }
```

## <a name="template-example-for-windows-vmss"></a>Esempio di modello per set di scalabilità di macchine virtuali (VMSS) Windows

Il nodo VMSS ha una sezione "properties" con gli attributi "VirtualMachineProfile", "extensionProfile". DSC viene aggiunto sotto "extensions".

L'estensione DSC eredita le proprietà di estensione predefinite, come documentato in [Classe VirtualMachineScaleSetExtension](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.72",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configurationArguments": {
                                {
                                    "Name": "RegistrationKey",
                                    "Value": {
                                        "UserName": "PLACEHOLDER_DONOTUSE",
                                        "Password": "PrivateSettingsRef:registrationKeyPrivate"
                                    },
                                },
                                "RegistrationUrl" : "[parameters('registrationUrl1')]",
                                "NodeConfigurationName" : "nodeConfigurationNameValue1"
                        }
                        },
                        "protectedSettings": {
                            "Items": {
                                        "registrationKeyPrivate": "[parameters('registrationKey1']"
                                    }
                        }
                    }
                ]
            }
        }
```

## <a name="detailed-settings-information"></a>Informazioni dettagliate sulle impostazioni

Di seguito è illustrato lo schema per la sezione delle impostazioni dell'estensione DSC di Azure in un modello di Azure Resource Manager.

*Per un elenco degli argomenti disponibili per lo script di configurazione predefinito,*
*vedere la sezione seguente denominata*
*[Script di configurazione predefinito](##Default-Configuration-Script) *.

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

| Nome proprietà | type | DESCRIZIONE |
| --- | --- | --- |
| settings.wmfVersion |stringa |Specifica la versione di Windows Management Framework da installare nella macchina virtuale. Impostando questa proprietà su 'latest' verrà installata la versione più recente di WMF. Gli unici valori attualmente possibili per questa proprietà sono **'4.0', '5.0', '5.0PP' e 'latest'**. Questi valori possibili sono soggetti ad aggiornamenti. Il valore predefinito è 'latest'. |
| settings.configuration.url |stringa |Specifica il percorso URL da cui scaricare il file ZIP della configurazione DSC. Se l'URL specificato richiede un token di firma di accesso condiviso per l'accesso, è necessario impostare la proprietà protectedSettings.configurationUrlSasToken sul valore del token di firma di accesso condiviso. Questa proprietà è obbligatoria se settings.configuration.script e/o settings.configuration.function sono definiti. Se non viene specificato alcun valore per queste proprietà, l'estensione chiamerà lo script di configurazione predefinito per impostare i metadati di Gestione configurazione locale e devono essere forniti gli argomenti. |
| settings.configuration.script |stringa |Specifica il nome del file di script che contiene la definizione della configurazione DSC. Questo script deve trovarsi nella cartella radice del file ZIP scaricato dall'URL specificato dalla proprietà configuration.url. Questa proprietà è obbligatoria se settings.configuration.url e/o settings.configuration.script sono definiti. Se non viene specificato alcun valore per queste proprietà, l'estensione chiamerà lo script di configurazione predefinito per impostare i metadati di Gestione configurazione locale e devono essere applicati gli argomenti. |
| settings.configuration.function |stringa |Specifica il nome della configurazione DSC. La configurazione indicata deve essere contenuta nello script definito da configuration.script. Questa proprietà è obbligatoria se settings.configuration.url e/o settings.configuration.function sono definiti. Se non viene specificato alcun valore per queste proprietà, l'estensione chiamerà lo script di configurazione predefinito per impostare i metadati di Gestione configurazione locale e devono essere forniti gli argomenti. |
| settings.configurationArguments |Raccolta |Definisce i parametri da passare alla configurazione DSC. Questa proprietà non è crittografata. |
| settings.configurationData.url |stringa |Specifica l'URL da cui scaricare il file di dati di configurazione con estensione psd1 da usare come input per la configurazione DSC. Se l'URL specificato richiede un token di firma di accesso condiviso per l'accesso, è necessario impostare la proprietà protectedSettings.configurationDataUrlSasToken sul valore del token di firma di accesso condiviso. |
| settings.privacy.dataEnabled |stringa |Abilita o disabilita la raccolta di dati di telemetria. Gli unici valori attualmente possibili per questa proprietà sono **'Enable', 'Disable', '', o $null**. Lasciando questa proprietà vuota o con valore null verrà abilitata la raccolta di dati di telemetria. Il valore predefinito è ''. [Altre informazioni](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings |Raccolta |Definisce i percorsi alternativi da cui scaricare WMF. [Altre informazioni](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments |Raccolta |Definisce i parametri da passare alla configurazione DSC. Questa proprietà è crittografata. |
| protectedSettings.configurationUrlSasToken |stringa |Specifica il token di firma di accesso condiviso per accedere all'URL definito da configuration.url. Questa proprietà è crittografata. |
| protectedSettings.configurationDataUrlSasToken |stringa |Specifica il token di firma di accesso condiviso per accedere all'URL definito da configurationData.url. Questa proprietà è crittografata. |

## <a name="default-configuration-script"></a>Script di configurazione predefinito

Per altre informazioni su questi valori, vedere la pagina della documentazione [Configurazione di Gestione configurazione locale - Impostazioni di base](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig#basic-settings).
Solo le proprietà di Gestione configurazione locale nella tabella seguente sono configurabili tramite lo script di configurazione predefinito dell'estensione DSC.

| Nome proprietà | type | DESCRIZIONE |
| --- | --- | --- |
| settings.configurationArguments.RegistrationKey |securestring |Proprietà obbligatoria. Specifica la chiave usata per la registrazione di un nodo nel servizio Automazione di Azure come password di un oggetto credenziale di PowerShell. Questo valore può essere individuato automaticamente tramite il metodo listkeys per l'account di Automazione e deve essere protetto come un'impostazione protetta. |
| settings.configurationArguments.RegistrationUrl |stringa |Proprietà obbligatoria. Specifica l'URL dell'endpoint di Automazione di Azure in cui il nodo tenterà di registrarsi. Questo valore può essere individuato automaticamente usando il metodo reference per l'account di Automazione. |
| settings.configurationArguments.NodeConfigurationName |stringa |Proprietà obbligatoria. Specifica la configurazione del nodo nell'account di Automazione di Azure da assegnare al nodo. |
| settings.configurationArguments.ConfigurationMode |stringa |Specifica la modalità di Gestione configurazione locale. Le opzioni valide sono "ApplyOnly", "ApplyandMonitor" e "ApplyandAutoCorrect".  Il valore predefinito è "ApplyandMonitor". |
| settings.configurationArguments.RefreshFrequencyMins | Valore UInt32 | Specifica la frequenza con cui Gestione configurazione locale tenterà di verificare la disponibilità di aggiornamenti con l'account di Automazione.  Il valore predefinito è 30.  Il valore minimo è 15. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | Valore UInt32 | Specifica la frequenza con cui Gestione configurazione locale convaliderà la configurazione corrente.  Il valore predefinito è 15.  Il valore minimo è 15. |
| settings.configurationArguments.RebootNodeIfNeeded | boolean | Specifica se un nodo può essere riavviato automaticamente se richiesto da un'operazione DSC.  Il valore predefinito è False. |
| settings.configurationArguments.ActionAfterReboot | stringa | Specifica che cosa avviene dopo un riavvio durante l'applicazione di una configurazione. Le opzioni valide sono "ContinueConfiguration" e "StopConfiguration". Il valore predefinito è "ContinueConfiguration". |
| settings.configurationArguments.AllowModuleOverwrite | boolean | Specifica se Gestione configurazione locale sovrascriverà i moduli esistenti nel nodo.  Il valore predefinito è False. |

## <a name="settings-vs-protectedsettings"></a>Differenze tra settings e protectedSettings

Tutte le impostazioni vengono salvate in un file di testo delle impostazioni nella macchina virtuale.
Le proprietà indicate in 'settings' sono proprietà pubbliche perché non sono crittografate nel file di testo delle impostazioni.
Le proprietà indicate in 'protectedSettings' vengono crittografate con un certificato e non vengono visualizzate in testo normale in questo file nella macchina virtuale.

Se la configurazione richiede credenziali, queste possono essere indicate in protectedSettings:

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

## <a name="example"></a>Esempio

L'esempio seguente corrisponde al comportamento predefinito per l'estensione DSC, ovvero fornire le impostazioni dei metadati a Gestione configurazione locale ed effettuare la registrazione per il servizio Automation DSC di Azure.
Gli argomenti di configurazione sono necessari e verranno passati allo script di configurazione predefinito per impostare i metadati di Gestione configurazione locale.

```json
"settings": {
    "configurationArguments": {
        {
            "Name": "RegistrationKey",
            "Value": {
                "UserName": "PLACEHOLDER_DONOTUSE",
                "Password": "PrivateSettingsRef:registrationKeyPrivate"
            },
        },
        "RegistrationUrl" : "[parameters('registrationUrl1')]",
        "NodeConfigurationName" : "nodeConfigurationNameValue1"
  }
},
"protectedSettings": {
    "Items": {
                "registrationKeyPrivate": "[parameters('registrationKey1']"
            }
}
```

## <a name="example-using-configuration-script-in-azure-storage"></a>Esempio di uso dello script di configurazione in Archiviazione di Azure

L'esempio seguente deriva dalla sezione "Introduzione" di [Introduzione al gestore dell'estensione DSC (Desired State Configuration) di Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
Questo esempio usa modelli di Resource Manager anziché i cmdlet per distribuire l'estensione.
Salvare la configurazione di "IisInstall.ps1", inserirla in un file ZIP e caricare il file in un URL accessibile.
Questo esempio usa l'archiviazione BLOB di Azure, ma è possibile scaricare file ZIP da qualsiasi percorso.

Nel modello di Azure Resource Manager il codice seguente indica alla VM di scaricare il file corretto ed eseguire la funzione PowerShell appropriata:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="updating-from-the-previous-format"></a>Aggiornamento dal formato precedente

Tutte le impostazioni presenti nel formato precedente, contenente le proprietà pubbliche ModulesUrl, ConfigurationFunction, SasToken o Properties, verranno automaticamente adattate al formato corrente ed eseguite come in precedenza.

Lo schema seguente è simile allo schema delle impostazioni precedente:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties":  {
        "ParameterToConfigurationFunction1":  "Value1",
        "ParameterToConfigurationFunction2":  "Value2",
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

| Nome proprietà | Equivalente nello schema precedente |
| --- | --- |
| settings.wmfVersion |settings.wmfVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |Prima parte di settings.ConfigurationFunction (prima di "\\\\") |
| settings.configuration.function |Seconda parte di settings.ConfigurationFunction (dopo "\\\\") |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri (senza token di firma di accesso condiviso) |
| settings.privacy.dataEnabled |settings.privacy.dataEnabled |
| settings.advancedOptions.downloadMappings |settings.advancedOptions.downloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |Token di firma di accesso condiviso di protectedSettings.DataBlobUri |

## <a name="troubleshooting---error-code-1100"></a>Risoluzione dei problemi - Codice errore 1100

Il codice errore 1100 indica che si è verificato un problema con l'input dell'utente per l'estensione DSC.
Il testo di questi errori può variare.
Di seguito sono riportati alcuni degli errori che possono verificarsi e la possibile soluzione.

### <a name="invalid-values"></a>Valori non validi

"Privacy.dataCollection è '{0}'.
Gli unici valori possibili sono '', 'Enable' e 'Disable'".
"WmfVersion è '{0}'.
Gli unici valori possibili sono: ... e "latest".

Problema: un valore specificato non è consentito.

Soluzione: sostituire il valore non valido con un valore valido.
Vedere la tabella nella sezione dei dettagli.

### <a name="invalid-url"></a>URL non valido

"ConfigurationData.url è '{0}'. URL non valido" "DataBlobUri è '{0}'. URL non valido" "Configuration.url è '{0}'. URL non valido"

Problema: un URL specificato non è valido.

Soluzione: verificare tutti gli URL specificati.
Verificare che tutti gli URL si risolvano in percorsi validi cui l'estensione può accedere nel computer remoto.

### <a name="invalid-configurationargument-type"></a>Tipo ConfigurationArgument non valido

"Tipo configurationArguments {0} non valido"

Problema: la proprietà ConfigurationArguments non si risolve in un oggetto Hashtable.

Soluzione: rendere la proprietà ConfigurationArguments un oggetto Hashtable.
Seguire il formato indicato nell'esempio precedente.
Prestare attenzione alle virgolette, alle virgole e alle parentesi graffe.

### <a name="duplicate-configurationarguments"></a>ConfigurationArguments duplicato

"Trovati argomenti duplicati '{0}' in configurationArguments pubblici e protetti"

Problema: ConfigurationArguments nelle impostazioni pubbliche e ConfigurationArguments nelle impostazioni protette contengono proprietà con lo stesso nome.

Soluzione: rimuovere una delle proprietà duplicate.

### <a name="missing-properties"></a>Proprietà mancanti
"Configuration.function richiede che venga specificato configuration.url o configuration.module"

"Configuration.url richiede che venga specificato configuration.script"

"Configuration.script richiede che venga specificato configuration.url"

"Configuration.url richiede che venga specificato configuration.function"

"ConfigurationUrlSasToken richiede che venga specificato configuration.url"

"ConfigurationDataUrlSasToken richiede che venga specificato configurationData.url"

Problema: una proprietà definita richiede un'altra proprietà mancante.

Soluzioni:

- Specificare la proprietà mancante.
- Rimuovere la proprietà che richiede la proprietà mancante.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su DSC e sui set di scalabilità di macchine virtuali, vedere [Uso dei set di scalabilità di macchine virtuali con l'estensione DSC di Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).

Per altre informazioni dettagliate, leggere l'articolo sulla [gestione delle credenziali protette di DSC](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Per altre informazioni sul gestore dell'estensione DSC, vedere [Introduzione al gestore dell'estensione DSC (Desired State Configuration) di Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Per altre informazioni su PowerShell DSC, [vedere il centro di documentazione di PowerShell](https://msdn.microsoft.com/powershell/dsc/overview).
