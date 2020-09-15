---
title: Installare i Language Pack in VM Windows 10 in desktop virtuale di Windows-Azure
description: Come installare i Language Pack per le macchine virtuali con più sessioni di Windows 10 nel desktop virtuale di Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 08/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fbc2aba21212a83bd73d5664f4fe288017954c0d
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90084210"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Aggiungere i Language Pack a un'immagine con più sessioni di Windows 10

Desktop virtuale di Windows è un servizio che gli utenti possono distribuire in qualsiasi momento, ovunque. Per questo motivo è importante che gli utenti siano in grado di personalizzare la lingua visualizzata per l'immagine in più sessioni di Windows 10 Enterprise.

Esistono due modi per soddisfare le esigenze di lingua degli utenti:

- Crea pool host dedicati con un'immagine personalizzata per ogni lingua.
- È necessario che gli utenti dispongano di requisiti diversi per la lingua e la localizzazione nello stesso pool di host, ma personalizzare le immagini per assicurarsi che possano selezionare qualsiasi lingua di cui hanno bisogno.

Il secondo metodo è molto più efficiente ed economicamente conveniente. Tuttavia, spetta a te decidere quale metodo è più adatto alle tue esigenze. In questo articolo viene illustrato come personalizzare le lingue per le immagini.

## <a name="prerequisites"></a>Prerequisiti

Per personalizzare le immagini multisessione di Windows 10 Enterprise per aggiungere più lingue, sono necessari gli elementi seguenti:

- Una macchina virtuale (VM) di Azure con Windows 10 Enterprise multisessione, versione 1903 o successiva

- Il linguaggio ISO, la funzionalità su richiesta (Dom) disco 1 e le app della posta in arrivo ISO della versione del sistema operativo utilizzata dall'immagine. Puoi scaricarle da qui: 
     
     - ISO lingua:
        - [Windows 10, versione 1903 o 1909 Language Pack ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10, versione 2004 Language Pack ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - ISO disco 1 ISO:
        - [Windows 10, versione 1903 o 1909 Dom disco 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10, versione 2004 Dom disco 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - ISO app della posta in arrivo:
        - [Windows 10, versione 1903 o 1909 app della posta in arrivo ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10, versione 2004 app della posta in arrivo ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)

- Una condivisione File di Azure o una condivisione file in una macchina virtuale file server Windows

>[!NOTE]
>La condivisione file (repository) deve essere accessibile dalla macchina virtuale di Azure che si prevede di usare per creare l'immagine personalizzata.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>Creare un repository di contenuti per i pacchetti e le funzionalità della lingua su richiesta

Per creare il repository di contenuto per i pacchetti della lingua e FODs e un repository per i pacchetti delle app della posta in arrivo:

1. In una macchina virtuale di Azure, scaricare le app ISO, FODs e posta in arrivo di Windows 10 multilingua per Windows 10 Enterprise multisessione, versione 1903/1909 e 2004 dai collegamenti nei [prerequisiti](#prerequisites).

2. Aprire e montare i file ISO nella macchina virtuale.

3. Passare al Language Pack ISO e copiare il contenuto dalle cartelle **LocalExperiencePacks** e **x64 \\ Langpacks** , quindi incollare il contenuto nella condivisione file.

4. Passare al **file ISO Dom**, copiare tutto il contenuto e incollarlo nella condivisione file.
5. Passare alla cartella **amd64fre** nelle app della posta in arrivo ISO e copiare il contenuto nel repository per le app in arrivo preparate.

     >[!NOTE]
     > Se si usa una risorsa di archiviazione limitata, copiare solo i file per le lingue necessarie per gli utenti. È possibile distinguere i file esaminando i codici di lingua nei rispettivi nomi di file. Il nome del file francese, ad esempio, è "fr-FR". Per un elenco completo dei codici di lingua per tutte le lingue disponibili, vedere [Language Pack disponibili per Windows](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

     >[!IMPORTANT]
     > Alcuni linguaggi richiedono tipi di carattere aggiuntivi inclusi nei pacchetti satellite che seguono diverse convenzioni di denominazione. Ad esempio, i nomi dei file del tipo di carattere giapponese includono "jpan".
     >
     > [!div class="mx-imgBorder"]
     > ![Un esempio dei Language Pack in giapponese con il tag di lingua "jpan" nei nomi dei file.](media/language-pack-example.png)

6. Impostare le autorizzazioni per la condivisione del repository del contenuto della lingua in modo da avere accesso in lettura alla macchina virtuale che verrà usata per compilare l'immagine personalizzata.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>Creare un'immagine personalizzata di Windows 10 Enterprise in più sessioni manualmente

Per creare manualmente un'immagine di Windows 10 Enterprise multisessione personalizzata:

1. Distribuire una macchina virtuale di Azure, quindi passare alla raccolta di Azure e selezionare la versione corrente di Windows 10 Enterprise multisessione in uso.
2. Dopo aver distribuito la macchina virtuale, connetterla usando RDP come amministratore locale.
3. Assicurarsi che la macchina virtuale disponga di tutti gli aggiornamenti di Windows più recenti. Scaricare gli aggiornamenti e riavviare la macchina virtuale, se necessario.
4. Connettersi al repository di condivisione file delle app di Language Package, Dom e Inbox e montarlo in un'unità lettera (ad esempio, unità E).

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>Creazione automatica di un'immagine di Windows 10 Enterprise multisessione personalizzata

Se si preferisce installare le lingue tramite un processo automatizzato, è possibile configurare uno script in PowerShell. È possibile utilizzare il seguente script di esempio per installare i Language Pack di spagnolo (Spagna), francese (Francia) e cinese (PRC) e i pacchetti satellite per Windows 10 Enterprise multisessione, versione 2004. Lo script integra il Language Interface Pack e tutti i pacchetti satellite necessari nell'immagine. Tuttavia, è anche possibile modificare lo script per installare altre lingue. È sufficiente assicurarsi di eseguire lo script da una sessione di PowerShell con privilegi elevati, altrimenti non funzionerà.

```powershell
########################################################
## Add Languages to running Windows Image for Capture##
########################################################

##Disable Language Pack Cleanup##
Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"

##Set Language Pack Content Stores##
[string]$LIPContent = "E:"

##Spanish##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\es-es\LanguageExperiencePack.es-es.Neutral.appx -LicensePath $LIPContent\es-es\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_es-es.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
Set-WinUserLanguageList $LanguageList -force

##French##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\fr-fr\LanguageExperiencePack.fr-fr.Neutral.appx -LicensePath $LIPContent\fr-fr\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_fr-fr.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~fr-fr~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("fr-fr")
Set-WinUserLanguageList $LanguageList -force

##Chinese(PRC)##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\zh-cn\LanguageExperiencePack.zh-cn.Neutral.appx -LicensePath $LIPContent\zh-cn\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_zh-cn.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

>[!IMPORTANT]
>Windows 10 Enterprise versioni 1903 e 1909 non richiedono il `Microsoft-Windows-Client-Language-Pack_x64_<language-code>.cab` file del pacchetto.

Lo script potrebbe richiedere un po' di tempo a seconda del numero di lingue che è necessario installare.

Al termine dell'esecuzione dello script, verificare che i Language Pack siano installati correttamente **passando a**  >  **Impostazioni**  >  **ora &**  >  **lingua**della lingua. Se i file della lingua sono presenti, l'utente è tutto pronto.

Dopo aver aggiunto altre lingue all'immagine di Windows, è necessario aggiornare anche le app della posta in arrivo per supportare le lingue aggiunte. Questa operazione può essere eseguita aggiornando le app preinstallate con il contenuto delle app della posta in arrivo ISO. Per eseguire l'aggiornamento in un ambiente disconnesso (nessun accesso a Internet dalla VM possibile), è possibile usare l'esempio di script di PowerShell seguente per automatizzare il processo.

```powershell
#########################################
## Update Inbox Apps for Multi Language##
#########################################
##Set Inbox App Package Content Stores##
[string]$InboxApps = "F:\"
##Update Inbox Store Apps##
$AllAppx = Get-Item $inboxapps\*.appx | Select-Object name
$AllAppxBundles = Get-Item $inboxapps\*.appxbundle | Select-Object name
$allAppxXML = Get-Item $inboxapps\*.xml | Select-Object name
foreach ($Appx in $AllAppx) {
    $appname = $appx.name.substring(0,$Appx.name.length-5)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    
    Write-Host "Handeling with xml $appname"  
  
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
      
      Write-Host "Handeling without xml $appname"
      
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
foreach ($Appx in $AllAppxBundles) {
    $appname = $appx.name.substring(0,$Appx.name.length-11)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    Write-Host "Handeling with xml $appname"
    
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
       Write-Host "Handeling without xml $appname"
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
```

>[!IMPORTANT]
>Le app della posta in arrivo incluse nell'ISO non sono le versioni più recenti delle app di Windows pre-installate. Per ottenere la versione più recente di tutte le app, è necessario aggiornare le app usando l'app di Windows Store ed eseguire una ricerca manuale degli aggiornamenti dopo aver installato le lingue aggiuntive.

Al termine, assicurarsi di disconnettere la condivisione.

## <a name="finish-customizing-your-image"></a>Completare la personalizzazione dell'immagine

Dopo aver installato i Language Pack, è possibile installare qualsiasi altro software che si desidera aggiungere all'immagine personalizzata.

Al termine della personalizzazione dell'immagine, sarà necessario eseguire l'utilità preparazione sistema (Sysprep).

Per eseguire Sysprep:

1. Aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente per generalizzare l'immagine:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Arrestare la macchina virtuale, quindi acquisirla in un'immagine gestita seguendo le istruzioni riportate in [creare un'immagine gestita di una macchina virtuale generalizzata in Azure](../virtual-machines/windows/capture-image-resource.md).

3. È ora possibile usare l'immagine personalizzata per distribuire un pool host di desktop virtuali Windows. Per informazioni su come distribuire un pool host, vedere [esercitazione: creare un pool host con il portale di Azure](create-host-pools-azure-marketplace.md).

## <a name="enable-languages-in-windows-settings-app"></a>Abilitare le lingue nell'app impostazioni di Windows

Infine, dopo aver distribuito il pool host, sarà necessario aggiungere la lingua all'elenco di lingue di ogni utente in modo da poter selezionare la lingua preferita nel menu impostazioni.

Per assicurarsi che gli utenti possano selezionare le lingue installate, accedere come utente, quindi eseguire il cmdlet di PowerShell seguente per aggiungere i Language Pack installati nel menu lingue. È anche possibile configurare questo script come attività automatizzata o script di accesso che viene attivato quando l'utente accede alla propria sessione.

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Dopo che un utente ha modificato le impostazioni della lingua, sarà necessario disconnettersi dalla sessione desktop virtuale di Windows e accedere di nuovo per rendere effettive le modifiche. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui problemi noti relativi ai Language Pack, vedere [aggiunta di Language Pack in Windows 10, versione 1803 e versioni successive: problemi noti](/windows-hardware/manufacture/desktop/language-packs-known-issue).

Per altre domande su Windows 10 Enterprise Multisession, vedere le domande [frequenti](windows-10-multisession-faq.md).
