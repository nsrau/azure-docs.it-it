<properties urlDisplayName="Add a Cert to the CA Store" pageTitle="Aggiungere un certificato all'archivio CA Java - Azure" metaKeywords="Azure Twilio Java, Twilio Java Certificate, Azure Service Bus Certificate" description="Informazioni su come aggiungere un certificato dell'autorità di certificazione (CA) all'archivio certificati CA Java (cacerts) per il servizio Twilio o Bus di servizio di Azure." metaCanonical="" services="" documentationCenter="Java" title="Adding a Certificate to the Java CA Certificates Store" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Aggiunta di un certificato all'archivio certificati CA Java
Nella procedura seguente viene illustrato come aggiungere un certificato dell'Autorità di certificazione (CA) all'archivio certificati CA Java (cacerts). L'esempio usato è relativo al certificato CA necessario per il servizio Twilio. Più avanti in questo argomento vengono riportate le informazioni per installare il certificato CA per il bus di servizio di Azure. 

È possibile usare lo strumento Keytool per aggiungere il certificato CA prima di comprimere il JDK e di aggiungerlo alla cartella **approot** del progetto Azure. In alternativa, è possibile eseguire un'attività di avvio in Azure che usa lo strumento Keytool per aggiungere il certificato. In questo esempio si presuppone che il certificato CA venga aggiunto prima della compressione del JDK. Verrà inoltre usato un certificato CA specifico, ma i passaggi per ottenere un certificato CA diverso e per importarlo nell'archivio cacerts sono simili.

## Per aggiungere un certificato all'archivio cacerts

1. Al prompt dei comandi impostato sulla cartella **jdk\jre\lib\security** del JDK eseguire il comando seguente per verificare quali certificati sono installati:

	`keytool -list -keystore cacerts`

	Verrà richiesto di immettere la password dell'archivio. La password predefinita è **changeit**. Se si desidera cambiarla, vedere la documentazione dello strumento Keytool all'indirizzo <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>. In questo esempio si presuppone che il certificato con ID digitale MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 non sia presente nell'elenco e che si desideri importarlo. Questo specifico certificato è necessario per il servizio API Twilio.
2. Ottenere il certificato dall'elenco di [certificati radice GeoTrust](http://www.geotrust.com/resources/root-certificates/). Fare clic con il pulsante destro del mouse sul collegamento relativo al certificato con numero di serie 35:DE:F4:CF e salvarlo nella cartella **jdk\jre\lib\security**. Ai fini di questo esempio, è stato salvato in un file denominato **Equifax\_Secure\_Certificate\_Authority.cer**.
3. Importare il certificato tramite il comando seguente:

	`keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`

	Quando viene richiesto se considerare attendibile il certificato, verificare che l'ID digitale MD5 sia 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 e rispondere digitando **y**.
4. Eseguire il comando seguente per verificare se il certificato CA è stato importato correttamente:

	`keytool -list -keystore cacerts`

5. Comprimere il JDK e aggiungerlo alla cartella **approot** del progetto Azure.

Per informazioni sullo strumento Keytool, vedere <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

# Certificati radice di Azure

Le applicazioni che usano i servizi di Azure, ad esempio il bus di servizio di Azure, devono considerare attendibile il certificato Baltimore CyberTrust Root. A partire dal 15 aprile 2013 è iniziata la migrazione di Azure da GTE CyberTrust Global Root a Baltimore CyberTrust Root. Questa migrazione ha richiesto diverse settimane di tempo.

È possibile che il certificato Baltimore sia già installato nell'archivio cacerts, quindi eseguire prima il comando **keytool -list** per verificare se esiste già.

Se è necessario aggiungere Baltimore CyberTrust Root, il numero di serie è 02:00:00:b9 e l'ID digitale SHA1 è d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74. È possibile scaricarlo da <https://cacert.omniroot.com/bc2025.crt>, salvarlo in locale con estensione **.cer** e quindi importarlo con lo strumento **keytool** come descritto in precedenza.

Per altre informazioni sui certificati radice usati da Azure, vedere l'articolo relativo alla [migrazione dei certificati radice di Azure](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx).

