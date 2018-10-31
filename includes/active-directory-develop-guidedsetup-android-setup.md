---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 3b1f705b14bdae43fb079f2039ca8c288dd6609e
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988361"
---
## <a name="set-up-your-project"></a>Configurare il progetto

Se si preferisce scaricare il progetto Android Studio di questo esempio, [scaricare un progetto](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip) e andare direttamente al [passaggio della configurazione](#register-your-application) per configurare il codice di esempio prima di eseguirlo.

### <a name="create-a-new-project"></a>Creare un nuovo progetto

1. Aprire Android Studio e quindi selezionare **File** > **New** (Nuovo) > **New Project** (Nuovo progetto).
2. Assegnare un nome all'applicazione e quindi selezionare **Next** (Avanti).
3. Selezionare **API 21 or newer (Android 5.0)** (API 21 o più recente - Android 5.0) e fare clic su **Next** (Avanti).
4. Lasciare invariata l'opzione **Empty Activity** (Attività vuota) e selezionare **Next** (Avanti) e quindi **Finish** (Fine).

### <a name="add-msal-to-your-project"></a>Aggiungere MSAL al progetto

1. In Android Studio selezionare **Gradle Scripts** (Script Gradle) > **build.gradle (Module: app)**.
2. In **Dependencies** incollare il codice seguente:

    ```gradle  
    compile ('com.microsoft.identity.client:msal:0.1.+') {
        exclude group: 'com.android.support', module: 'appcompat-v7'
    }
    compile 'com.android.volley:volley:1.0.0'
    ```

<!--start-collapse-->
### <a name="about-this-package"></a>Informazioni sul pacchetto

Il pacchetto nel codice precedente installa Microsoft Authentication Library (MSAL), che gestisce tutte le operazioni relative ai token, tra cui l'acquisizione, la memorizzazione nella cache, l'aggiornamento e l'eliminazione. I token sono necessarie per accedere alle API protette dalla piattaforma Microsoft Identity.
<!--end-collapse-->

## <a name="create-the-apps-ui"></a>Creare l'interfaccia utente dell'app

1. Passare a **res** > **layout** e quindi aprire **activity_main.xml**.
2. Modificare il layout di attività da `android.support.constraint.ConstraintLayout` o altro a `LinearLayout`.
3. Aggiungere la proprietà `android:orientation="vertical"` al nodo `LinearLayout`.
4. Incollare il codice seguente nel nodo `LinearLayout`, sostituendo il contenuto corrente:

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```
