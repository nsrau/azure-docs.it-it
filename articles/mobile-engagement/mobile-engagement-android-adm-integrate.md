---
title: Integrazione di Android SDK per Azure Mobile Engagement
description: Ultimi aggiornamenti e procedure relativi ad Azure Mobile Engagement SDK per Android
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: dwrede
editor: 
ms.assetid: a7d719ec-67b3-4be3-9d7f-0b61a57fe978
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 928c3d85f98f0bd073a5aaadf0ae4495840c5da6


---
# <a name="how-to-integrate-adm-with-engagement"></a>Come integrare ADM con Mobile Engagement
> [!IMPORTANT]
> Prima di usare questa guida, è necessario eseguire la procedura di integrazione descritta nel documento relativo all'integrazione di Engagement in Android.
> 
> Il documento è utile solo se è già stato integrato il modulo di copertura e si intende eseguire il push dei dispositivi Amazon. Per integrare le campagne Reach nell'applicazione, leggere prima l'articolo relativo all'integrazione del servizio Reach di Engagement in Android.
> 
> 

## <a name="introduction"></a>Introduzione
L'integrazione di ADM consente il push dell'applicazione quando è destinata a dispositivi Android Amazon.

I payload ADM di cui viene eseguito il push sull’SDK contengono sempre la chiave `azme` nell'oggetto dati. Pertanto se si usa ADM per uno scopo diverso nell'applicazione, è possibile filtrare le notifiche push in base a tale chiave.

> [!IMPORTANT]
> Solo i dispositivi Amazon Kindle che eseguono Android 4.0.3 o versioni successive sono supportati da Amazon Device Messaging. È comunque possibile integrare questo codice in modo sicuro in altri dispositivi.
> 
> 

## <a name="sign-up-to-adm"></a>Iscriversi ad ADM
Se questa operazione non è già stata eseguita, è necessario abilitare ADM nel proprio account Amazon.

La procedura è descritta in dettaglio all'indirizzo: [<https://developer.amazon.com/sdk/adm/credentials.html>].

Dopo aver completato la procedura, si ottiene quanto segue:

* Le credenziali OAuth (ID e segreto client) che consentono a Engagement di eseguire il push dei dispositivi.
* Una chiave API che deve essere integrata nell'applicazione.

## <a name="sdk-integration"></a>Integrazione dell'SDK
### <a name="managing-device-registrations"></a>Gestione delle registrazioni dei dispositivi
Ogni dispositivo deve inviare un comando di registrazione ai server ADM; in caso contrario non saranno raggiungibili.

Se si usa già la [libreria client ADM] e si è già [integrato ADM], è possibile passare direttamente ad android-sdk-adm-receive.

Se ADM non è stato ancora integrato, Engagement offre un modo più semplice per abilitarlo nell'applicazione:

Modificare il file `AndroidManifest.xml`:

* Aggiungere lo spazio dei nomi Amazon. Il file deve iniziare nel modo seguente:
  
      <?xml version="1.0" encoding="utf-8"?>
      <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:amazon="http://schemas.amazon.com/apk/res/android"
* All'interno del tag `<application/>` aggiungere questa sezione:
  
      <amazon:enable-feature
         android:name="com.amazon.device.messaging"
         android:required="false"/>
  
      <meta-data android:name="engagement:adm:register" android:value="true" />
* Dopo aver aggiunto il tag di Amazon, può verificarsi un errore di compilazione se come destinazione di compilazione del progetto è impostata una versione precedente Android 2.1. È necessario usare una destinazione di compilazione **Android 2.1** o versione successiva, anche se è ancora possibile avere una proprietà `minSdkVersion` impostata su 4.
* Integrare la chiave API di ADM come asset seguendo [questa procedura].

Seguire quindi le istruzioni riportate nelle sezioni successive.

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Comunicare l'ID di registrazione al servizio push di Engagement e ricevere le notifiche
Per comunicare l'ID di registrazione del dispositivo al servizio push di Engagement e ricevere le notifiche, aggiungere quanto segue al file `AndroidManifest.xml`, all'interno del tag `<application/>` (anche se si usa ADM senza Engagement):

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

Assicurarsi di avere le seguenti autorizzazioni nel file `AndroidManifest.xml` (prima del tag `</application>`).

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

## <a name="grant-engagement-oauth-credentials"></a>Concedere le credenziali OAuth di Engagement
Inviare le credenziali OAuth (ID client e Segreto client) al portale di Engagement.

[<https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[libreria client ADM]:https://developer.amazon.com/sdk/adm/setup.html
[integrato ADM]:https://developer.amazon.com/sdk/adm/integrating-app.html
[questa procedura]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset



<!--HONumber=Nov16_HO3-->


