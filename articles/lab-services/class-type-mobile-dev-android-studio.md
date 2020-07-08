---
title: Configurare un Lab per insegnare lo sviluppo di applicazioni per dispositivi mobili con Android Studio
titleSuffix: Azure Lab Services
description: Informazioni su come configurare un Lab per insegnare ai dati la classe di sviluppo di applicazioni per dispositivi mobili che usa Android Studio.  Verranno inoltre illustrate le regolazioni da effettuare quando si usa Android Studio in una macchina virtuale in Azure.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: ff996a096cddbb85f1e1c84cd051c18a7ab4ad79
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85444982"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Configurare un Lab per insegnare lo sviluppo di applicazioni per dispositivi mobili con Android Studio

Questo articolo illustra come configurare una classe introduttiva per lo sviluppo di applicazioni per dispositivi mobili.  Questa lezione è incentrata sulle applicazioni per dispositivi mobili Android che possono essere pubblicate nel [Google Play Store](https://play.google.com/store/apps).  Gli studenti imparano a usare [Android Studio](https://developer.android.com/studio) per creare applicazioni.  Per testare l'applicazione a livello locale viene usato l'[Emulatore di Visual Studio per Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/).

## <a name="lab-configuration"></a>Configurazione del lab

Per configurare questo Lab, è necessario disporre di una sottoscrizione di Azure e di un account Lab per iniziare. Se non si ha una sottoscrizione di Azure, prima di iniziare creare un [account gratuito](https://azure.microsoft.com/free/). Una volta ottenuto una sottoscrizione di Azure, è possibile creare un nuovo account Lab in Azure Lab Services. Per ulteriori informazioni sulla creazione di un nuovo account Lab, vedere [esercitazione per la configurazione di un account Lab](tutorial-setup-lab-account.md).  È anche possibile usare un account lab esistente.

Seguire l' [esercitazione](tutorial-setup-classroom-lab.md) per la configurazione del Lab per creare un nuovo Lab e quindi applicare le impostazioni seguenti:

| Dimensioni della macchina virtuale | Image |
| -------------------- | ----- |
| Media (virtualizzazione annidata) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>Configurazione computer modello

Al termine della creazione del computer modello, [avviare il computer e connettersi](how-to-create-manage-template.md#update-a-template-vm) per completare le attività seguenti:

1. Aggiungi ruolo Hyper-V
2. Scaricare e installare Java.  
3. Scaricare e installare l'emulatore di Visual Studio per Android.
4. Scaricare e installare Android Studio.
5. Configurare l'emulatore di Visual Studio per Android Studio.

## <a name="add-hyper-v-role"></a>Aggiungi ruolo Hyper-V

Hyper-V deve essere abilitato per la corretta installazione di Visual Studio Emulator per Android.  Seguire le istruzioni riportate nell'articolo [come abilitare la virtualizzazione annidata in un modello di macchina virtuale](how-to-enable-nested-virtualization-template-vm.md) .

## <a name="install-java"></a>Installare Java

Android Studio richiede Java.  Per scaricare la versione più recente di Java, attenersi alla procedura riportata di seguito.

1. Passare alla [pagina di download Java](https://www.java.com/download/). Fare clic sul pulsante **Download Java** .
2. Nella pagina Web di Windows per Java a 64 bit fare clic sul pulsante **Accetto e avviare il download gratuito**.
3. Quando viene visualizzato programma di installazione di **Java** , fare clic su **Installa**.
4. Attendere fino a quando il titolo del programma di installazione non viene modificato in **installazione Java: completato**.  Fare clic sul pulsante **Chiudi** .

## <a name="install-visual-studio-emulator-for-android"></a>Installare l'emulatore di Visual Studio per Android

Per testare un'applicazione Android localmente, è necessario usare una versione virtualizzata di un dispositivo Android.  Sono disponibili alcuni emulatori Android che consentono a uno sviluppatore di testare la propria applicazione dal computer.  Si usa Visual Studio Emulator for Android perché è un emulatore che supporta la virtualizzazione nidificata.  Poiché la VM del servizio lab è già una macchina virtuale, è necessario un emulatore che supporti la virtualizzazione nidificata.  L'emulatore incorporato per Android Studio non supporta la virtualizzazione nidificata.  Per verificare quali emulatori supportano la virtualizzazione annidata, vedere [accelerazione hardware per le prestazioni dell'emulatore (Hyper-V & HAXM)](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration).

Usare le istruzioni seguenti per scaricare e installare l'emulatore di Visual Studio per Android.

1. Passare a [Visual Studio Emulator for Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) Home page.
2. Fare clic sul pulsante **Scarica l'emulatore** .
3. Quando vs_emulatorsetup.exe viene scaricato, eseguire il file eseguibile.
4. Quando viene visualizzata la finestra di dialogo di installazione di Visual Studio, fare clic sul pulsante **Installa** .
5. Attendere il completamento del programma di installazione.  Fare clic sul pulsante **Riavvia ora** per riavviare il computer e completare l'installazione.

Avviare l'emulatore prima di distribuire l'applicazione usando Android Studio.  Per altre informazioni su Visual Studio Emulator for Android, vedere la [documentazione di Visual Studio Emulator for Android](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android).

## <a name="install-android-studio"></a>Installa Android Studio

Seguire le istruzioni seguenti per scaricare e installare [Android Studio](https://developer.android.com/studio).

1. Passare alla [pagina di download Android Studio](https://developer.android.com/studio#downloads).  
    > [!NOTE]
    > Internet Explorer non è supportato da questo sito.
2. Fare clic sul pacchetto di Android Studio eseguibile di Windows (64 bit).
3. Leggere le note legali scritte nella finestra popup.  Quando si è pronti per continuare, selezionare la casella di controllo **ho letto e accetto i termini e le condizioni indicati sopra** e fare clic sul pulsante **Scarica Android Studio per Windows** .
4. Dopo aver scaricato il file eseguibile di installazione Android Studio, eseguire il file eseguibile.
5. Nella pagina **Android Studio installazione** del programma di installazione del programma di installazione di **Android Studio** , fare clic su **Avanti**.
6. Nella pagina **impostazioni di configurazione** fare clic su **Avanti**.
7. Nella pagina **scegliere la cartella del menu Start** fare clic su **Installa**.
8. Attendere il completamento dell'installazione.
9. Nella pagina **installazione completata** fare clic su **Avanti**.
10. Nella pagina **completamento Android Studio installazione** .  Fare clic su **Fine**.
11. Android Studio verrà avviato automaticamente al termine dell'installazione.
12. Nella finestra **di dialogo Importa impostazioni Android da...** selezionare non **importare le impostazioni**. Fare clic su **OK**.
13. Nella pagina **iniziale** dell' **installazione guidata di Android Studio**fare clic su **Avanti**.
14. Nella pagina **tipo di installazione** scegliere **standard**. Fare clic su **Avanti**.
15. Nella pagina **Selezione tema dell'interfaccia utente** selezionare il tema desiderato. Fare clic su **Avanti**.
16. Nella pagina **Verifica impostazioni** fare clic su **Avanti**.
17. Nella pagina **download dei componenti** attendere che tutti i componenti vengano scaricati.  Fare clic su **Fine**.

    > [!IMPORTANT]
    > Si prevede che l'installazione di HAXM non riesca.  HAXM non supporta la virtualizzazione nidificata, motivo per cui è stato installato Visual Studio Emulator per Android in precedenza in questo articolo.

18. Al termine dell'installazione guidata verrà visualizzata la finestra **di dialogo Introduzione a Android Studio** .

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>Configurare Android Studio e l'emulatore di Visual Studio per Android

Android Studio è quasi pronto per l'uso.  È comunque necessario indicare a Visual Studio Emulator for Android dove è installata la Android SDK.  In questo modo tutti gli emulatori in esecuzione in Visual Studio per Android vengono mostrati come destinazioni di distribuzione per il debug Android Studio.

È necessario impostare una chiave del registro di sistema specifica per indicare a Visual Studio Emulator for Android dove si trova Android SDK.  Per impostare la chiave del registro di sistema necessaria, eseguire lo script seguente.  Lo script di PowerShell riportato di seguito presuppone il percorso di installazione predefinito per Android SDK.  Se Android SDK è stato installato in un altro percorso, modificare il valore di `$androidSdkPath` prima di eseguire lo script.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Riavviare l'emulatore di Visual Studio per Android e Android Studio in modo da usare la nuova impostazione.

Avviare la versione necessaria nell'emulatore di Visual Studio.  Verrà visualizzato come destinazione di distribuzione per l'app Android in Android Studio.  La versione minima per il progetto Android Studio deve supportare la versione in esecuzione in Visual Studio Emulator for Android.  A questo punto si è pronti per creare ed eseguire il debug di progetti usando Android Studio e l'emulatore di Visual Studio per Android.  In caso di problemi, vedere Risoluzione dei problemi dell'emulatore Android.

## <a name="cost"></a>Costi

Se si desidera stimare il costo di questo Lab, è possibile seguire l'esempio riportato di seguito.
Per una classe di 25 studenti con 20 ore di tempo di classe pianificata e 10 ore di quota per il lavoro o le assegnazioni, il prezzo per il Lab sarà  

25 studenti \* (20 pianificati + 10 quote) ore * 55 unità Lab * 0,01 USD all'ora = 412,5 USD

Per altre informazioni sui prezzi, vedere [Prezzi di Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Passaggi successivi

I passaggi successivi sono comuni per la configurazione di qualsiasi Lab.

- [Creare e gestire un modello](how-to-create-manage-template.md)
- [Aggiungere utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Impostare la quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Collegamenti per la registrazione tramite posta elettronica agli studenti](how-to-configure-student-usage.md#send-invitations-to-users)
