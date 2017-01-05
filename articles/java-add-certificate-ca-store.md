---
title: Aggiungere un certificato all&quot;archivio CA Java | Microsoft Docs
description: "Informazioni su come aggiungere un certificato dell&quot;autorità di certificazione (CA) all&quot;archivio certificati CA Java (cacerts) per il servizio Twilio o il bus di servizio di Azure."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: d3699b0a-835c-43fb-844d-9c25344e5cda
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a967a522ebbdce57fa5b4f938369ddd88253701c


---
# <a name="adding-a-certificate-to-the-java-ca-certificates-store"></a>Aggiunta di un certificato all'archivio certificati CA Java
La procedura seguente illustra come aggiungere un certificato dell'Autorità di certificazione (CA) all'archivio certificati CA Java (cacerts). L'esempio usa il certificato CA necessario per il servizio Twilio. Più avanti in questo argomento vengono riportate le informazioni per installare il certificato CA per il bus di servizio di Azure. 

È possibile usare lo strumento Keytool per aggiungere il certificato CA prima di comprimere il JDK e di aggiungerlo alla cartella **approot** del progetto Azure. In alternativa, è possibile eseguire un'attività di avvio in Azure che usa lo strumento Keytool per aggiungere il certificato. Questo esempio presuppone che il certificato CA venga aggiunto prima della compressione del JDK. Verrà inoltre usato un certificato CA specifico, ma i passaggi per ottenere un certificato CA diverso e per importarlo nell'archivio cacerts sono simili.

## <a name="to-add-a-certificate-to-the-cacerts-store"></a>Per aggiungere un certificato all'archivio cacerts
1. Al prompt dei comandi impostato sulla cartella **jdk\jre\lib\security** del JDK eseguire il comando seguente per verificare quali certificati sono installati:
   
    `keytool -list -keystore cacerts`
   
    Verrà richiesto di immettere la password dell'archivio. La password predefinita è **changeit**. Se si desidera cambiarla, vedere la documentazione dello strumento Keytool all'indirizzo <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>. Questo esempio presuppone che il certificato con ID digitale MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 non sia presente nell'elenco e che si desideri importarlo. Questo specifico certificato è necessario per il servizio API Twilio.
2. Ottenere il certificato dall'elenco di [certificati radice GeoTrust](http://www.geotrust.com/resources/root-certificates/). Fare clic con il pulsante destro del mouse sul collegamento relativo al certificato con numero di serie 35:DE:F4:CF e salvarlo nella cartella **jdk\jre\lib\security**. Ai fini di questo esempio, è stato salvato in un file denominato **Equifax\_Secure\_Certificate\_Authority.cer**.
3. Importare il certificato tramite il comando seguente:
   
    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`
   
    Quando viene richiesto se considerare attendibile il certificato, verificare che l'ID digitale MD5 sia 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 e rispondere digitando **y**.
4. Eseguire il comando seguente per verificare se il certificato CA è stato importato correttamente:
   
    `keytool -list -keystore cacerts`
5. Comprimere il JDK e aggiungerlo alla cartella **approot** del progetto Azure.

Per informazioni sullo strumento Keytool, vedere <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

## <a name="azure-root-certificates"></a>Certificati radice di Azure
Le applicazioni che usano i servizi di Azure, ad esempio il bus di servizio di Azure, devono considerare attendibile il certificato Baltimore CyberTrust Root. A partire dal 15 aprile 2013 è iniziata la migrazione di Azure da GTE CyberTrust Global Root a Baltimore CyberTrust Root. Questa migrazione ha richiesto diverse settimane di tempo.

È possibile che il certificato Baltimore sia già installato nell'archivio cacerts, quindi eseguire prima il comando **keytool -list** per verificare se esiste già.

Se è necessario aggiungere Baltimore CyberTrust Root, il numero di serie è 02:00:00:b9 e l'impronta digitale SHA1 è d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74. Può essere scaricato da <https://cacert.omniroot.com/bc2025.crt>, salvato in un file locale con estensione **.cer** e quindi importato con **keytool** come illustrato in precedenza.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui certificati radice usati da Azure, vedere l'articolo relativo alla [migrazione dei certificati radice di Azure](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx).

Per ulteriori informazioni su Java, vedere il [Centro per sviluppatori di Java](/develop/java/).




<!--HONumber=Nov16_HO3-->


