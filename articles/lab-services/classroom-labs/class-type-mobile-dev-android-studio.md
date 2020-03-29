---
title: Configurare un lab per insegnare lo sviluppo di applicazioni per dispositivi mobili con Android StudioSet up a lab to teach mobile application development with Android Studio
titleSuffix: Azure Lab Services
description: Informazioni su come configurare un lab per insegnare la classe di sviluppo di applicazioni per dispositivi mobili dati che usa Android Studio.Learn how to set up a lab to teach data mobile application development class that uses Android Studio.  Articolo discuterà anche le modifiche da apportare quando si usa Android Studio in una macchina virtuale in Azure.Article will also discuss adjustments to make when using Android Studio on a virtual machine in Azure.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849786"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Configurare un lab per insegnare lo sviluppo di applicazioni mobili dati con Android StudioSet up a lab to teach data mobile application development with Android Studio

Questo articolo illustra come configurare una classe introduttiva di sviluppo di applicazioni per dispositivi mobili.  Questa lezione è incentrata sulle applicazioni per dispositivi mobili Android che possono essere pubblicate nel [Google Play Store](https://play.google.com/store/apps).  Gli studenti imparano a usare [Android Studio](https://developer.android.com/studio) per creare applicazioni.  Per testare l'applicazione a livello locale viene usato l'[Emulatore di Visual Studio per Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/).

## <a name="lab-configuration"></a>Configurazione del lab

Per configurare questo lab, sono necessari una sottoscrizione di Azure e un account lab per iniziare. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Dopo aver ottenuto una sottoscrizione di Azure, è possibile creare un nuovo account lab in Azure Lab Services.Once you get an Azure subscription, you can create a new lab account in Azure Lab Services. Per ulteriori informazioni sulla creazione di un nuovo account lab, vedere [esercitazione per configurare un account lab.](tutorial-setup-lab-account.md)  È inoltre possibile utilizzare un account lab esistente.

Seguire [l'esercitazione](tutorial-setup-classroom-lab.md) del laboratorio di configurazione della classe per creare un nuovo lab e quindi applicare le impostazioni seguenti:

| Dimensioni della macchina virtuale | Immagine |
| -------------------- | ----- |
| Medio (virtualizzazione annidata) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>Configurazione macchina modello

Al termine della creazione della macchina modello, [avviare la macchina e connettersi ad essa](how-to-create-manage-template.md#update-a-template-vm) per completare le attività seguenti:

1. Aggiungi ruolo Hyper-V
2. Scaricare e installare Java.  
3. Scaricare e installare l'emulatore di Visual Studio per Android.Download and install Visual Studio Emulator for Android.
4. Scaricare e installare Android Studio.
5. Configurare l'emulatore di Visual Studio per Android Studio.Configure Visual Studio Emulator for Android Studio.

## <a name="add-hyper-v-role"></a>Aggiungi ruolo Hyper-V

Hyper-V deve essere abilitato per la corretta installazione dell'emulatore di Visual Studio per Android.  Seguire le istruzioni nell'articolo Come abilitare la [virtualizzazione annidata in una macchina virtuale modello.](how-to-enable-nested-virtualization-template-vm.md)

## <a name="install-java"></a>Installare Java

Android Studio richiede Java.  Attenersi alla seguente procedura per scaricare la versione più recente di Java.

1. Passare alla pagina di [download Java](https://www.java.com/download/). Fare clic sul pulsante **Download Java.**
2. Nella pagina Web di Windows per Java a 64 bit, fare clic sul pulsante Con l'etichetta **Accetto e Avvia download gratuito**.
3. Quando viene visualizzato il programma di **installazione del programma di installazione Java,** fare clic su **Installa**.
4. Attendere che il titolo del programma di installazione venga modificato in **Installazione Java – Completata**.  Fare clic sul pulsante **Chiudi.**

## <a name="install-visual-studio-emulator-for-android"></a>Installare l'emulatore di Visual Studio per AndroidInstall Visual Studio Emulator for Android

Per testare un'applicazione Android in locale, è necessario usare una versione virtualizzata di un dispositivo Android.To test an Android application locally, it must use a virtualized version of an Android device.  Ci sono alcuni emulatori Android disponibili che permetteranno a uno sviluppatore di testare la propria applicazione dal proprio computer.  Stiamo usando l'emulatore di Visual Studio per Android perché è un emulatore che supporta la virtualizzazione annidata.  Poiché la macchina virtuale del servizio lab è già una macchina virtuale, è necessario un emulatore che supporti la virtualizzazione annidata.  L'emulatore incorporato per Android Studio non supporta la virtualizzazione annidata.  Per vedere quali emulatori supportano la virtualizzazione annidata, vedere [accelerazione hardware per le prestazioni dell'emulatore (Hyper-V & HAXM)](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration).

Usare le istruzioni seguenti per scaricare e installare l'emulatore di Visual Studio per Android.Use the following instructions to download and install Visual Studio Emulator for Android.

1. Passare alla home page [dell'emulatore di Visual Studio per Android.Navigate](https://visualstudio.microsoft.com/vs/msft-android-emulator/) to Visual Studio Emulator for Android home page.
2. Fare clic sul pulsante **Scarica l'emulatore.**
3. Quando vs_emulatorsetup.exe viene scaricato, eseguire l'eseguibile.
4. Quando viene visualizzata la finestra di dialogo di installazione di Visual Studio, fare clic sul pulsante **Installa.When** the Visual Studio setup dialog appears, click the Install button.
5. Attendere il completamento del programma di installazione.  Fare clic sul pulsante **Riavvia ora** per riavviare il computer e completare l'installazione.

Avviare l'emulatore prima di distribuire l'applicazione con Android Studio.Start the emulator first before deploying your application using Android Studio.  Per ulteriori informazioni sull'emulatore di Visual Studio per Android, vedere [Documentazione relativa all'emulatore di Visual Studio per Android.](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android)

## <a name="install-android-studio"></a>Installare Android Studio

Segui le istruzioni riportate di seguito per scaricare e installare [Android Studio.](https://developer.android.com/studio)

1. Passare alla pagina di download di [Android Studio](https://developer.android.com/studio#downloads).  
    > [!NOTE]
    > Internet Explorer non è supportato da questo sito.
2. Fare clic sul pacchetto eseguibile di Windows (64 bit) di Android Studio.
3. Leggi i termini legali scritti nel pop-up.  Quando sei pronto a continuare, controlla **che ho letto e accetto i termini e le condizioni di cui sopra** e fai clic sul pulsante Scarica Android Studio per **Windows.**
4. Una volta scaricato il file eseguibile di installazione di Android Studio, eseguire l'eseguibile.
5. Nella pagina **di installazione** di Android Studio del programma di installazione di Android **Studio** fare clic su **Avanti**.
6. Nella pagina **Impostazioni di configurazione** fare clic su **Avanti**.
7. Nella pagina **Scegli cartella menu Start** fare clic su **Installa**.
8. Attendere il completamento della configurazione.
9. Nella pagina **Installazione completata** fare clic su **Avanti**.
10. Nella pagina **Completamento della configurazione di Android Studio.**  Fare clic su **Fine**.
11. Android Studio si avvierà automaticamente al termine della configurazione.
12. Nella finestra di dialogo **Importa impostazioni Android da...,** selezionare **Non importare le impostazioni**. Fare clic su **OK**.
13. Nella pagina **iniziale** **dell'Installazione guidata**di Android Studio fare clic su **Avanti**.
14. Nella pagina **Tipo di installazione** scegliere **Standard**. Fare clic su **Avanti**.
15. Nella pagina **Seleziona tema dell'interfaccia utente** selezionare il tema desiderato. Fare clic su **Avanti**.
16. Nella pagina **Verifica impostazioni** fare clic su **Avanti**.
17. Nella pagina **Download dei componenti,** attendere il download di tutti i componenti.  Fare clic su **Fine**.

    > [!IMPORTANT]
    > Si prevede che l'installazione HAXM non riesce.  HAXM non supporta la virtualizzazione annidata, motivo per cui è stato installato l'emulatore di Visual Studio per Android in precedenza in questo articolo.

18. Al termine dell'installazione guidata, verrà visualizzata la finestra di dialogo **Benvenuto in Android Studio.**

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>Configurare Android Studio e l'emulatore di Visual Studio per AndroidConfigure Android Studio and Visual Studio Emulator for Android

Android Studio è quasi pronto per l'uso.  Abbiamo ancora bisogno di comunicare Visual Studio Emulator per Android dove è installato l'SDK di Android.We still need to tell Visual Studio Emulator for Android where the Android SDK is installed.  In questo modo tutti gli emulatori in esecuzione in Visual Studio per Android verranno visualizzati come destinazioni di distribuzione per il debug di Android Studio.This will make any emulators running in Visual Studio for Android show as deployment targets for Android Studio debugging.

È necessario impostare una chiave del Registro di sistema specifico per indicare Visual Studio Emulator per Android in cui si trova Android Sdk.We need to set a specific registry key to tell Visual Studio Emulator for Android where the Android Sdk is located.  Per impostare la chiave del Registro di sistema necessaria, eseguire lo script riportato di seguito.  The PowerShell script below assumes the default install location for the Android Sdk.  Se Android Sdk è stato installato in `$androidSdkPath` un'altra posizione, modificare il valore di prima di eseguire lo script.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Riavviare l'emulatore di Visual Studio per Android e Android Studio in modo che venga usata la nuova impostazione.

Avviare la versione necessaria nell'emulatore di Visual Studio.Start the version you need in the Visual Studio Emulator.  Verrà visualizzato come destinazione di distribuzione per l'app Android in Android Studio.  The minimum version for the Android Studio project must support the version running in the Visual Studio Emulator for Android.  A questo punto è possibile creare ed eseguire il debug di progetti usando Android Studio e l'emulatore di Visual Studio per Android.Now you are ready to create and debug projects using Android Studio and Visual Studio Emulator for Android.  In caso di problemi, vedere Risoluzione dei problemi relativi all'emulatore Android.If you have issues, see Android emulator troubleshooting.

## <a name="cost"></a>Costi

Se si desidera stimare il costo di questo lab, è possibile seguire l'esempio riportato di seguito.
Per una classe di 25 studenti con 20 ore di tempo programmato e 10 ore di quota per compiti a casa o compiti, il prezzo per il laboratorio sarebbe  

25 \* studenti (20 ore programmate e 10) - 55 unità di laboratorio - 0,01 USD all'ora - 412,5 USD

Ulteriori ulteriori dettagli sui prezzi, vedere Prezzi di [Servizi di laboratorio di Azure](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Passaggi successivi

I passaggi successivi sono comuni alla configurazione di qualsiasi lab.

- [Creare e gestire un modello](how-to-create-manage-template.md)
- [Aggiungi utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Imposta quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Link di registrazione via e-mail agli studenti](how-to-configure-student-usage.md#send-invitations-to-users)
