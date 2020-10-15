---
title: Usare la cronologia delle versioni dell'estensione DSC (Desired State Configuration) di Azure
description: Questo articolo descrive come usare la cronologia delle versioni per l'estensione DSC (Desired State Configuration) in Azure.
ms.date: 07/22/2020
keywords: dsc, powershell, azure, estensione
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.openlocfilehash: b45512faf09cfe745023d29d32f89a4432cc3b2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87079758"
---
# <a name="work-with-azure-desired-state-configuration-extension-version-history"></a>Usare la cronologia delle versioni dell'estensione DSC (Desired State Configuration) di Azure

L'estensione macchina virtuale DSC (Desired State Configuration) di Azure viene aggiornata quando necessario per supportare i miglioramenti e le nuove funzionalità offerti da Azure, Windows Server e Windows Management Framework (WMF), che include Windows PowerShell.

Questo articolo contiene informazioni su ogni versione dell'estensione VM DSC di Azure con i relativi ambienti supportati e commenti e note sulle nuove funzionalità o modifiche.

## <a name="latest-version"></a>Versione più recente

### <a name="version-280"></a>Versione 2,80

- **Data di rilascio:**
  - 26 settembre, Sep-2019 (Azure) | 6 luglio 2020 (Azure Cina Viant 21) | 20 luglio 2020 (Azure per enti pubblici)
- **Supporto sistema operativo**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Client Windows 7/8.1/10
  - Nano Server
- **Supporto WMF:**
  - WMF 5.1
  - WMF 5.0 RTM
  - Aggiornamento di WMF 4.0
  - WMF 4.0
- **Environment:**
  - Azure
  - Azure China (21Vianet)
  - Azure Government
- **Osservazioni:** Non sono incluse nuove funzionalità in questa versione.

### <a name="version-276"></a>Versione 2.76

- **Data di rilascio:**
  - 9 maggio 2018 (Azure) | 21 giugno 2018 (Azure China 21Vianet, Azure per enti pubblici)
- **Supporto sistema operativo**
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Client Windows 7/8.1/10
  - Nano Server
- **Supporto WMF:**
  - WMF 5.1
  - WMF 5.0 RTM
  - Aggiornamento di WMF 4.0
  - WMF 4.0
- **Environment:**
  - Azure
  - Azure China (21Vianet)
  - Azure Government
- **Note:** questa versione usa DSC nella versione inclusa in Windows Server 2016, per gli altri sistemi operativi Windows installa [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (l'installazione di WMF richiede il riavvio). Per Nano Server, il ruolo DSC viene installato nella macchina virtuale.
- **Nuove funzionalità:**
  - Miglioramento dei metadati di estensione per lo stato secondario e altre correzioni di bug secondari.

## <a name="supported-versions"></a>Versioni supportate

> [!WARNING]
> Le versioni da 2.4 a 2.13 usano l'anteprima pubblica di WMF 5.0 i cui certificati di firma sono scaduti ad agosto
> 2016. Per altre informazioni su questo problema, vedere il [post di blog](https://devblogs.microsoft.com/powershell/azure-dsc-extension-versions-2-4-up-to-2-13-will-retire-in-august/).

### <a name="version-275"></a>Versione 2.75

- **Data di rilascio:** March 5, 2018
- **Supporto sistema operativo** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Supporto WMF:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment:** Azure
- **Note:** questa versione usa DSC nella versione inclusa in Windows Server 2016, per gli altri sistemi operativi Windows installa [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (l'installazione di WMF richiede il riavvio). Per Nano Server, il ruolo DSC viene installato nella macchina virtuale.
- **Nuove funzionalità:**
  - Dopo il recente passaggio di GitHub a TLS 1.2, non è possibile caricare una macchina virtuale in Automation DSC per Azure usando i modelli DIY di Resource Manager disponibili in Azure Marketplace o usare l'estensione DSC per ottenere configurazioni ospitate in GitHub. Verrà visualizzato un errore simile al seguente durante la distribuzione dell'estensione:

    ```json
    {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
        "details": [{
            "code": "Conflict",
            "message": "{
                \"status\": \"Failed\",
                \"error\": {
                    \"code\": \"ResourceDeploymentFailure\",
                    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",
                    \"details\": [ {
                        \"code\": \"VMExtensionProvisioningError\",
                        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'.
                        Error message: \\\"The DSC Extension failed to execute: Error downloading
                        https://github.com/Azure/azure-quickstart-templates/raw/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip
                        after 29 attempts: The request was aborted: Could not create SSL/TLS secure channel..\\nMore information about the failure can
                        be found in the logs located under 'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.74.0.0' on the VM.\\\".\"
                    } ]
                }
            }"
        }]
    }
    ```

  - Nella nuova versione dell'estensione, ora viene applicato TLS 1.2. Durante la distribuzione dell'estensione, se è già presente AutoUpgradeMinorVersion = true nel modello di Resource Manager, l'estensione viene automaticamente aggiornata alla versione 2.75. Per gli aggiornamenti manuali, specificare `TypeHandlerVersion = 2.75` nel modello di Resource Manager.

### <a name="version-270---272"></a>Versioni da 2.70 a 2.72

- **Data di rilascio:** 13 novembre 2017
- **Supporto sistema operativo** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Supporto WMF:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment:** Azure
- **Note:** questa versione usa DSC nella versione inclusa in Windows Server 2016, per gli altri sistemi operativi Windows installa [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (l'installazione di WMF richiede il riavvio). Per Nano Server, il ruolo DSC viene installato nella macchina virtuale.
- **Nuove funzionalità:**
  - Correzioni di bug e miglioramenti che semplificano l'uso di Automation DSC per Azure nell'interfaccia utente del portale nonché del modello di Resource Manager. Per altre informazioni, vedere l'argomento relativo allo [script di configurazione predefinito](../virtual-machines/extensions/dsc-overview.md) nella documentazione dell'estensione DSC.

### <a name="version-226"></a>Versione 2.26

- **Data di rilascio:** 9 giugno 2017
- **Supporto sistema operativo** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Supporto WMF:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment:** Azure
- **Note:** questa versione usa DSC nella versione inclusa in Windows Server 2016, per gli altri sistemi operativi Windows installa [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (l'installazione di WMF richiede il riavvio). Per Nano Server, il ruolo DSC viene installato nella macchina virtuale.
- **Nuove funzionalità:**
  - Miglioramenti della telemetria.

### <a name="version-225"></a>Versione 2.25

- **Data di rilascio:** 2 giugno 2017
- **Supporto sistema operativo** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Supporto WMF:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment:** Azure
- **Note:** questa versione usa DSC nella versione inclusa in Windows Server 2016, per gli altri sistemi operativi Windows installa [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (l'installazione di WMF richiede il riavvio). Per Nano Server, il ruolo DSC viene installato nella macchina virtuale.
- **Nuove funzionalità:**
  - Aggiunte diverse correzioni di bug oltre a miglioramenti secondari.

### <a name="version-224"></a>Versione 2.24

- **Data di rilascio:** 13 aprile 2017
- **Supporto sistema operativo** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Supporto WMF:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment:** Azure
- **Note:** questa versione usa DSC nella versione inclusa in Windows Server 2016, per gli altri sistemi operativi Windows installa [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (l'installazione di WMF richiede il riavvio). Per Nano Server, il ruolo DSC viene installato nella macchina virtuale.
- **Nuove funzionalità:**
  - Espone UUID della macchina virtuale e ID dell'agente DSC come metadati dell'estensione. Aggiunti altri miglioramenti secondari.

### <a name="version-223"></a>Versione 2.23

- **Data di rilascio:** 15 marzo 2017
- **Supporto sistema operativo** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Supporto WMF:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment:** Azure
- **Note:** questa versione usa DSC nella versione inclusa in Windows Server 2016, per gli altri sistemi operativi Windows installa [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (l'installazione di WMF richiede il riavvio). Per Nano Server, il ruolo DSC viene installato nella macchina virtuale.
- **Nuove funzionalità:**
  - Numerose correzioni di bug e altri miglioramenti.

### <a name="version-222"></a>Versione 2.22

- **Data di rilascio:** 8 febbraio 2017
- **Supporto sistema operativo** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Supporto WMF:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment:** Azure
- **Note:** questa versione usa DSC nella versione inclusa in Windows Server 2016, per gli altri sistemi operativi Windows installa [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (l'installazione di WMF richiede il riavvio). Per Nano Server, il ruolo DSC viene installato nella macchina virtuale.
- **Nuove funzionalità:**
  - L'estensione DSC ha ora il supporto per WMF 5.1.
  - Aggiunti altri miglioramenti secondari.

### <a name="version-221"></a>Versione 2.21

- **Data di rilascio:** 2 dicembre 2016
- **Supporto sistema operativo** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Supporto WMF:** WMF 5.1 Preview, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment:** Azure
- **Note:** questa versione usa DSC nella versione inclusa in Windows Server 2016, per gli altri sistemi operativi Windows installa [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (l'installazione di WMF richiede il riavvio). Per Nano Server, il ruolo DSC viene installato nella macchina virtuale.
- **Nuove funzionalità:**
  - L'estensione DSC è ora disponibile in Nano Server. Questa versione contiene principalmente modifiche del codice per l'esecuzione dell'estensione in Nano Server.
  - Aggiunti altri miglioramenti secondari.

### <a name="version-220"></a>Versione 2.20

- **Data di rilascio:** 2 agosto 2016
- **Supporto sistema operativo** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Supporto WMF:** WMF 5.1 Preview, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment:** Azure
- **Note:** questa versione usa DSC nella versione inclusa in Windows Server 2016 Technical Preview, per gli altri sistemi operativi Windows installa [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (l'installazione di WMF richiede il riavvio).
- **Nuove funzionalità:**
  - Supporto per l'anteprima di WMF 5.1. La prima volta questa versione è stata pubblicata come aggiornamento facoltativo ed era necessario specificare Wmfversion = '5.1PP' nei modelli di Resource Manager per installare l'anteprima di WMF 5.1.
    Wmfversion = 'latest' installa ancora [WMF 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/).
    Per altre informazioni sull'anteprima di WMF 5.1, vedere [questo blog](https://devblogs.microsoft.com/powershell/announcing-windows-management-framework-wmf-5-1-preview/).
  - Aggiunti altre correzioni e miglioramenti secondari.

### <a name="version--219"></a>Versione 2.19

- **Data di rilascio:** 3 giugno 2016
- **Supporto sistema operativo** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Supporto WMF:** WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment:** Azure, Azure China (21Vianet), Azure per enti pubblici
- **Note:** questa versione usa DSC nella versione inclusa in Windows Server 2016 Technical Preview, per gli altri sistemi operativi Windows installa [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (l'installazione di WMF richiede il riavvio).
- **Nuove funzionalità:**
  - L'estensione DSC è ora caricata in Azure China (21Vianet). Questa versione contiene principalmente correzioni per l'esecuzione dell'estensione in Azure China (21Vianet).

### <a name="version-218"></a>Versione 2.18

- **Data di rilascio:** 3 giugno 2016
- **Supporto sistema operativo** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Supporto WMF:** WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment:** Azure
- **Note:** questa versione usa DSC nella versione inclusa in Windows Server 2016 Technical Preview, per gli altri sistemi operativi Windows installa [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (l'installazione di WMF richiede il riavvio).
- **Nuove funzionalità:**
  - Usare la telemetria in modo che non crei blocchi quando si verifica un errore durante il download degli hotfix di telemetria (problema noto del servizio DNS di Azure) o durante l'installazione.
  - Correzione del problema che saltuariamente causa l'arresto dell'elaborazione della configurazione da parte dell'estensione dopo il riavvio.
    Per questo motivo l'estensione DSC rimane in stato di "transizione".
  - Aggiunti altre correzioni e miglioramenti secondari.

### <a name="version-217"></a>Versione 2.17

- **Data di rilascio:** 26 aprile 2016
- **Supporto sistema operativo** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Supporto WMF:** WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Environment:** Azure
- **Note:** questa versione usa DSC nella versione inclusa in Windows Server 2016 Technical Preview, per gli altri sistemi operativi Windows installa [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (l'installazione di WMF richiede il riavvio).
- **Nuove funzionalità:**
  - Supporto per l'aggiornamento di WMF 4.0. Per altre informazioni sull'aggiornamento di WMF 4.0, vedere [questo blog](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-4-0-update-now-available-for-windows-server-2012-windows-server-2008-r2-sp1-and-windows-7-sp1/).
  - Logica di ripetizione dei tentativi in caso di errori che si verificano durante l'installazione dell'estensione DSC o l'applicazione di una configurazione DSC dopo l'installazione dell'estensione. Come parte di questa modifica, l'estensione tenterà di ripetere l'installazione, se un'installazione precedente ha avuto esito negativo, o di applicare di nuovo una configurazione DSC in precedenza non riuscita, per un massimo tre volte finché raggiunge lo stato di completamento (esito positivo o errore) o se viene inviata una nuova richiesta. Se l'estensione ha esito negativo a causa di impostazioni o input utente non validi, non vengono eseguiti tentativi. In questo caso l'estensione deve essere nuovamente richiamata con una nuova richiesta e impostazioni utente corrette. Nota: l'estensione DSC dipende dall'agente di macchine virtuali di Azure per i tentativi. L'agente di macchine virtuali di Azure richiama l'estensione con l'ultima richiesta non riuscita finché raggiunge uno stato di esito positivo o errore.

### <a name="version-216"></a>Versione 2.16

- **Data di rilascio:** 21 aprile 2016
- **Supporto sistema operativo** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Supporto WMF:** WMF 5.0 RTM, WMF 4.0
- **Environment:** Azure
- **Note:** questa versione usa DSC nella versione inclusa in Windows Server 2016 Technical Preview, per gli altri sistemi operativi Windows installa [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (l'installazione di WMF richiede il riavvio).
- **Nuove funzionalità:**
  - Miglioramenti nella gestione degli errori e altre correzioni di bug minori.
  - Nuova proprietà nelle impostazioni dell'estensione DSC. Aggiunta di 'ForcePullAndApply' in AdvancedOptions per consentire all'estensione DSC di applicare le configurazioni DSC quando la modalità di aggiornamento è Pull (in contrapposizione alla modalità predefinita Push). Per altre informazioni, consultare [questo blog](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/) per maggiori dettagli sulle impostazioni dell'estensione DSC.

### <a name="version-215"></a>Versione 2.15

- **Data di rilascio:** 14 marzo 2016
- **Supporto sistema operativo** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Supporto WMF:** WMF 5.0 RTM, WMF 4.0
- **Environment:** Azure
- **Note:** questa versione usa DSC nella versione inclusa in Windows Server 2016 Technical Preview, per gli altri sistemi operativi Windows installa [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (l'installazione di WMF richiede il riavvio).
- **Nuove funzionalità:**
  - Nella versione 2.14 dell'estensione sono state incluse modifiche per l'installazione di WMF RTM. Durante l'aggiornamento dalla versione 2.13.2.0 alla versione 2.14.0.0 dell'estensione si può notare che alcuni cmdlet DSC hanno esito negativo o che la configurazione non riesce generando un errore di istanza non trovata con i valori specificati per le proprietà. Per altre informazioni, consultare le [note sulla versione di DSC](/powershell/scripting/wmf/known-issues/known-issues-dsc). Le soluzioni alternative per questi problemi sono state aggiunte nella versione 2.15.
  - Purtroppo, se è già installata la versione 2.14 e si riscontra uno dei due problemi descritti sopra, è necessario eseguire questi passaggi manualmente. In una sessione di PowerShell con privilegi elevati:
    - `Remove-Item -Path $env:SystemRoot\system32\Configuration\DSCEngineCache.mof`
    - `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`

### <a name="version-214"></a>Versione 2.14

- **Data di rilascio:** 25 febbraio 2016
- **Supporto sistema operativo** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Supporto WMF:** WMF 5.0 RTM, WMF 4.0
- **Environment:** Azure
- **Note:** questa versione usa DSC nella versione inclusa in Windows Server 2016 Technical Preview, per gli altri sistemi operativi Windows installa [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (l'installazione di WMF richiede il riavvio).
- **Nuove funzionalità:**
  - Usa WMF RTM.
  - Consente la raccolta dei dati per migliorare la qualità dell'estensione DSC. Per altre informazioni, vedere [il blog](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/).
  - Mette a disposizione un formato aggiornato delle impostazioni per l'estensione in un modello di Resource Manager. Per altre informazioni, vedere [il blog](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/).
  - Correzioni di bug e altri miglioramenti.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su PowerShell DSC, vedere il [centro della documentazione di PowerShell](/powershell/scripting/dsc/overview/overview).
- Esaminare il [modello di Resource Manager per l'estensione DSC](../virtual-machines/extensions/dsc-template.md).
- Per altre funzionalità e risorse che è possibile gestire con PowerShell DSC, esplorare la [PowerShell Gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Per altre informazioni sul passaggio di parametri sensibili nelle configurazioni, vedere [Gestire credenziali in modo sicuro con il gestore dell'estensione DSC](../virtual-machines/extensions/dsc-credentials.md).
