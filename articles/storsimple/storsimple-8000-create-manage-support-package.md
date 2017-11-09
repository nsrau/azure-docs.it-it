---
title: Creare un pacchetto per il supporto StorSimple serie 8000 | Microsoft Docs
description: Informazioni su come creare, decrittografare e modificare un pacchetto per il supporto del dispositivo StorSimple serie 8000.
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 92abbb96b2117e10800de61b5c405a784453265b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Creare e gestire un pacchetto di supporto StorSimple serie 8000

## <a name="overview"></a>Panoramica

Un pacchetto per il supporto StorSimple è un meccanismo semplice da usare che raccoglie tutti i log pertinenti per aiutare il supporto tecnico Microsoft a risolvere i problemi relativi ai dispositivi StorSimple. I log raccolti vengono crittografati e compressi.

Questa esercitazione include istruzioni dettagliate per creare e gestire il pacchetto per il supporto per il dispositivo StorSimple serie 8000. Se si lavora con un array virtuale StorSimple, passare a [generare un pacchetto di log](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Creare un pacchetto di supporto

In alcuni casi, è necessario creare manualmente il pacchetto per il supporto tramite Windows PowerShell per StorSimple. Ad esempio:

* Se è necessario rimuovere informazioni riservate dai file di log prima di condividerli con il supporto tecnico Microsoft.
* In caso di difficoltà nel caricare il pacchetto a causa di problemi di connettività.

È possibile condividere il pacchetto per il supporto generato manualmente con il supporto tecnico Microsoft tramite posta elettronica. Per creare un pacchetto per il supporto in Windows PowerShell per StorSimple, eseguire i passaggi seguenti.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Per creare un pacchetto per il supporto in Windows PowerShell per StorSimple

1. Immettere il comando seguente per avviare una sessione di Windows PowerShell come amministratore nel computer remoto usato per connettersi al dispositivo StorSimple:
   
    `Start PowerShell`
2. Nella sessione di Windows PowerShell connettersi alla console SSAdmin del dispositivo:
   
   1. Al prompt dei comandi immettere:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. Nella finestra di dialogo visualizzata immettere la password dell'amministratore del dispositivo. La password predefinita è _Password1_.
     
      ![Finestra di dialogo Credenziali PowerShell](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Selezionare **OK**.
   4. Al prompt dei comandi immettere:
     
      `Enter-PSSession $MS`
3. Nella sessione avviata immettere il comando appropriato.
   
   * Per le condivisioni di rete protette da password, immettere:
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`
     
       Verrà richiesta la password, il percorso della cartella di rete condivisa e la passphrase di crittografia (perché il pacchetto per il supporto è crittografato). Viene quindi creato un pacchetto per il supporto nella cartella specificata.
   * Per le condivisioni non protette da password, il parametro `-Credential` non è necessario. Immettere le informazioni seguenti:
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`
     
       Il pacchetto per il supporto viene creato per entrambi i controller nella cartella di rete condivisa specificata. Si tratta di un file compresso e crittografato che può essere inviato al supporto tecnico Microsoft per la risoluzione dei problemi. Per ulteriori informazioni, vedere [Contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Parametri del cmdlet Export-HcsSupportPackage

Con il cmdlet Export-HcsSupportPackage è possibile usare i parametri seguenti.

| Parametro | Obbligatorio/Facoltativo | Description |
| --- | --- | --- |
| `-Path` |Obbligatorio |Consente di specificare il percorso della cartella di rete condivisa in cui verrà inserito il pacchetto per il supporto. |
| `-EncryptionPassphrase` |Obbligatorio |Consente di fornire una passphrase per crittografare il pacchetto per il supporto. |
| `-Credential` |Facoltativo |Consente di specificare le credenziali di accesso per la cartella di rete condivisa. |
| `-Force` |Facoltativo |Consente di ignorare il passaggio di conferma della passphrase di crittografia. |
| `-PackageTag` |Facoltativo |Consente di specificare una directory in cui inserire il pacchetto per il supporto in *Percorso* . Il valore predefinito è [nome dispositivo]-[data e ora correnti:aaaa-MM-gg-HH-mm-ss]. |
| `-Scope` |Facoltativo |Specificare come **Cluster** (impostazione predefinita) per creare un pacchetto per il supporto per entrambi i controller. Per creare un pacchetto solo per il controller corrente, specificare **Controller**. |

## <a name="edit-a-support-package"></a>Modificare un pacchetto per il supporto

Dopo aver generato un pacchetto per il supporto, potrebbe essere necessario modificarlo per rimuovere le informazioni riservate. Queste informazioni possono includere i nomi dei volumi, gli indirizzi IP dei dispositivi e i nomi dei backup dai file di log.

> [!IMPORTANT]
> È possibile solo modificare un pacchetto per il supporto che è stato generato tramite Windows PowerShell per StorSimple. Non è possibile modificare un pacchetto creato nel portale di Azure con il servizio Gestione dispositivi StorSimple.

Per modificare un pacchetto per il supporto prima di caricarlo nel sito del supporto tecnico Microsoft, è necessario decrittografarlo, modificare i file e quindi crittografarlo di nuovo. Eseguire i passaggi seguenti.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Per modificare un pacchetto per il supporto in Windows PowerShell per StorSimple

1. Generare un pacchetto per il supporto come descritto prima in [Per creare un pacchetto per il supporto in Windows PowerShell per StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Scaricare lo script](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente nel client.
3. Importare il modulo Windows PowerShell. Specificare il percorso della cartella locale in cui è stato scaricato lo script. Per importare il modulo, immettere:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Tutti i file hanno l'estensione *aes* e sono compressi e crittografati. Per decomprimere e decrittografare i file, immettere:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Notare che per tutti i file sono ora visualizzate le estensioni effettive.
   
    ![Modificare il pacchetto per il supporto](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Quando viene chiesta la passphrase di crittografia, immettere quella usata quando è stato creato il pacchetto per il supporto.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Passare alla cartella che contiene i file di log. Poiché i file di log sono ora decompressi e decrittografati, avranno le estensioni di file originali. Modificare questi file per rimuovere eventuali informazioni specifiche del cliente, ad esempio i nomi dei volumi e gli indirizzi IP dei dispositivi, e salvare i file.
7. Chiudere i file per comprimerli con GZIP e crittografarli con AES-256. Queste procedure assicurano velocità e sicurezza durante il trasferimento in rete del pacchetto per il supporto. Per comprimere e crittografare i file, immettere le informazioni seguenti:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Modificare il pacchetto per il supporto](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Quando richiesto, specificare una passphrase di crittografia del pacchetto per il supporto modificato.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Annotare la nuova passphrase in modo che sia possibile condividerla con il supporto tecnico Microsoft quando richiesto.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Esempio: modifica dei file in un pacchetto per il supporto in una condivisione protetta da password

L'esempio seguente mostra come decrittografare, modificare e crittografare di nuovo un pacchetto per il supporto.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni raccolte nel pacchetto per il supporto](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Informazioni su come [utilizzare i pacchetti per il supporto e i registri del dispositivo per risolvere i problemi di distribuzione del dispositivo](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Informazioni su come [usare il servizio Gestione dispositivi StorSimple per gestire il dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

