---
title: "Avvio rapido: Aggiungere chiamate VoIP a un'app Android con Servizi di comunicazione di Azure"
description: Questa esercitazione illustra come usare la libreria client Chiamate di Servizi di comunicazione di Azure per Android
author: matthewrobertson
ms.author: marobert
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 93037d1e1c56e280458a91ae5723502bff27995b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376825"
---
Questo argomento di avvio rapido illustra come avviare una chiamata usando la libreria client Chiamate di Servizi di comunicazione di Azure per Android.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio) per creare l'applicazione Android.
- Una risorsa di Servizi di comunicazione distribuita. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un [token di accesso utente](../../access-tokens.md) per il servizio di comunicazione di Azure.

## <a name="setting-up"></a>Configurazione

### <a name="create-an-android-app-with-an-empty-activity"></a>Creare un'app Android con un'attività vuota

In Android Studio avviare un nuovo progetto.

:::image type="content" source="../media/android/studio-new-project.png" alt-text="Screenshot che mostra il pulsante per l'avvio di un nuovo progetto di Android Studio selezionato in Android Studio.&quot;:::

Selezionare il modello di progetto &quot;Empty Activity&quot; (Attività vuota) in &quot;Phone and Tablet" (Telefono e tablet).

:::image type="content" source="../media/android/studio-blank-activity.png" alt-text="Screenshot che mostra il pulsante per l'avvio di un nuovo progetto di Android Studio selezionato in Android Studio.&quot;:::

Selezionare il modello di progetto &quot;Empty Activity&quot; (Attività vuota) in &quot;Phone and Tablet" o versione successiva.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="Screenshot che mostra il pulsante per l'avvio di un nuovo progetto di Android Studio selezionato in Android Studio.&quot;:::

Selezionare il modello di progetto &quot;Empty Activity&quot; (Attività vuota) in &quot;Phone and Tablet":::


### <a name="install-the-package"></a>Installare il pacchetto

<!-- TODO: update with instructions on how to download, install and add package to project -->
Individuare build.gradle a livello di progetto e assicurarsi di aggiungere `mavenCentral()` all'elenco dei repository in `buildscript` e `allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
In build.gradle a livello di modulo aggiungere quindi le righe seguenti alle sezioni dependencies e android

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.1'
    ...
}
```

### <a name="add-permissions-to-application-manifest"></a>Aggiungere autorizzazioni al manifesto dell'applicazione

Per richiedere le autorizzazioni necessarie per effettuare una chiamata, bisogna prima di tutto dichiararle nel manifesto dell'applicazione (`app/src/main/AndroidManifest.xml`). Sostituire il contenuto del file con quanto segue:

```xml
    <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.contoso.acsquickstart">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <!--Our calling client library depends on the Apache HTTP client library.
When targeting Android client library 28+, this library needs to be explicitly referenced.
See https://developer.android.com/about/versions/pie/android-9.0-changes-28#apache-p-->
        <uses-library android:name="org.apache.http.legacy" android:required="false"/>
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
    
```

### <a name="set-up-the-layout-for-the-app"></a>Configurare il layout per l'app

Sono necessari due input: un input di testo per l'ID del destinatario e un pulsante per effettuare la chiamata. Questi valori possono essere aggiunti tramite la finestra di progettazione o modificando il codice XML del layout. Creare un pulsante con un ID `call_button` e un input di testo `callee_id`. Passare a `app/src/main/res/layout/activity_main.xml` e sostituire il contenuto del file con quanto segue:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/call_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        android:text="Call"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <EditText
        android:id="@+id/callee_id"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="Callee Id"
        android:inputType="textPersonName"
        app:layout_constraintBottom_toTopOf="@+id/call_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>Creare lo scaffolding e i binding per l'attività principale

Con il layout creato è possibile aggiungere i binding e lo scaffolding di base dell'attività. L'attività gestirà la richiesta delle autorizzazioni in fase di esecuzione, la creazione dell'agente di chiamata e l'effettuazione della chiamata quando viene premuto il pulsante. Ogni procedura verrà descritta in una sezione specifica. Viene eseguito l'override del metodo `onCreate` per richiamare `getAllPermissions` e `createAgent`, oltre che per aggiungere i binding per il pulsante di chiamata. Questa operazione verrà eseguita dopo la creazione dell'attività. Per altre informazioni su `onCreate`, vedere la guida sul [ciclo di vita dell'attività](https://developer.android.com/guide/components/activities/activity-lifecycle).

Passare a **MainActivity.java** e sostituire il contenuto con il codice seguente:

```java
package com.contoso.acsquickstart;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationUser;
import com.azure.android.communication.common.CommunicationUserCredential;
import com.azure.communication.calling.CallAgent;
import com.azure.communication.calling.CallClient;
import com.azure.communication.calling.StartCallOptions;


import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {
    
    private CallAgent callAgent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();
        
        // Bind call button to call `startCall`
        Button callButton = findViewById(R.id.call_button);
        callButton.setOnClickListener(l -> startCall());
    }

    /**
     * Request each required permission if the app doesn't already have it.
     */
    private void getAllPermissions() {
        // See section on requesting permissions
    }

    /**
      * Create the call agent for placing calls
      */
    private void createAgent() {
        // See section on creating the call agent
    }

    /**
     * Place a call to the callee id provided in `callee_id` text input.
     */
    private void startCall() {
        // See section on starting the call
    }
}

```

### <a name="request-permissions-at-runtime"></a>Richiedere autorizzazioni in fase di esecuzione

Per Android 6.0 e versioni successive (livello API 23) e `targetSdkVersion` 23 o versione successiva, le autorizzazioni vengono concesse in fase di esecuzione invece che durante l'installazione dell'app. Per supportare questo requisito, è possibile implementare `getAllPermissions` per chiamare `ActivityCompat.checkSelfPermission` e `ActivityCompat.requestPermissions` per ogni autorizzazione richiesta.

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO, Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_PHONE_STATE};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 1);
    }
}
```

> [!NOTE]
> Quando si progetta l'app, valutare quando dovranno essere richieste queste autorizzazioni. È consigliabile che vengano richieste quando è necessario, non prima. Per altre informazioni, vedere la [guida alle autorizzazioni di Android.](https://developer.android.com/training/permissions/requesting)

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client Chiamate di Servizi di comunicazione di Azure:

| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient è il principale punto di ingresso alla libreria client Chiamate.|
| CallAgent | CallAgent si usa per avviare e gestire le chiamate. |
| CommunicationUserCredential | CommunicationUserCredential si usa come credenziale del token per creare un'istanza di CallAgent.|

## <a name="create-an-agent-from-the-user-access-token"></a>Creare un agente dal token di accesso utente

Con il token utente è possibile creare un'istanza di un agente di chiamata autenticato. In genere, questo token verrà generato da un servizio con l'autenticazione specifica per l'applicazione. Per altre informazioni sui token di accesso utente, vedere la guida ai [token di accesso utente](../../access-tokens.md). Per la guida di avvio rapido, sostituire `<User_Access_Token>` con un token di accesso utente generato per la risorsa del servizio di comunicazione di Azure.

```java

/**
 * Create the call agent for placing calls
 */
private void createAgent() {
    String userToken = "<User_Access_Token>";

    try {
        CommunicationUserCredential credential = new CommunicationUserCredential(userToken);
        callAgent = new CallClient().createCallAgent(getApplicationContext(), credential).get();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create call agent.", Toast.LENGTH_SHORT).show();
    }
}

```

## <a name="start-a-call-using-the-call-agent"></a>Avviare una chiamata con l'agente di chiamata

La chiamata può essere effettuata tramite l'agente di chiamata: è sufficiente fornire un elenco di ID dei destinatari e le opzioni di chiamata. Per l'argomento di avvio rapido, si useranno le opzioni di chiamata predefinite senza video e un singolo ID destinatario dall'input di testo.

```java
/**
 * Place a call to the callee id provided in `callee_id` text input.
 */
private void startCall() {
    EditText calleeIdView = findViewById(R.id.callee_id);
    String calleeId = calleeIdView.getText().toString();
    
    StartCallOptions options = new StartCallOptions();

    callAgent.call(
        getApplicationContext(),
        new CommunicationUser[] {new CommunicationUser(calleeId)},
        options);
}
```


## <a name="launch-the-app-and-call-the-echo-bot"></a>Avviare l'app e chiamare l'Echo Bot

È ora possibile avviare l'app usando il pulsante "Run App" (Esegui app) sulla barra degli strumenti (MAIUSC+F10). Verificare che sia possibile effettuare chiamate chiamando `8:echo123`. Verrà riprodotto un messaggio preregistrato che ripeterà il messaggio inviato.

:::image type="content" source="../media/android/quickstart-android-call-echobot.png" alt-text="Screenshot che mostra il pulsante per l'avvio di un nuovo progetto di Android Studio selezionato in Android Studio.&quot;:::

Selezionare il modello di progetto &quot;Empty Activity&quot; (Attività vuota) in &quot;Phone and Tablet":::

## <a name="sample-code"></a>Codice di esempio

È possibile scaricare l'app di esempio da [GitHub](https://github.com/Azure/Communication/tree/master/samples/Add%20Voice%20Calling/Android/Java)
