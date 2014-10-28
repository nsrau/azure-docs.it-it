<properties linkid="manage-linux-common-tasks-manage-certs" urlDisplayName="Manage certificates" pageTitle="Manage certificates for Linux virtual machines in Azure" metaKeywords="Azure management certs, uploading management certs, Azure Service Management API" description="Learn how to create and upload management certificates for Linux in Azure. The certificate is required if you use the Service Management API." metaCanonical="" services="virtual-machines" documentationCenter="" title="Create management certificates for Linux in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Creazione di certificati di gestione per Linux in Azure

Un certificato di gestione è necessario quando si desidera usare l'API di gestione del servizio per interagire con la piattaforma di immagini di Azure.

La documentazione su come creare e gestire questi certificati è già disponibile all'indirizzo [][][http://msdn.microsoft.com/it-it/library/azure/gg981929.aspx][http://msdn.microsoft.com/it-it/library/azure/gg981929.aspx]</a>. Per creare il certificato di gestione, è possibile usare anche OpenSSL. Per altre informazioni, vedere [OpenSSL][OpenSSL]. Questa documentazione, tuttavia, è dedicata principalmente all'utilizzo del portale Silverlight che potrebbe non essere accessibile per tutti gli utenti Linux. Viene descritto come ottenere l'accesso a questi certificati, come integrarli con i nostri diversi strumenti e partner e come utilizzarli autonomamente fino a quando questa funzionalità non verrà aggiunta nel portale di gestione di Azure.

## Sommario

-   [Acquisizione di un certificato di gestione dal file publishsettings][Acquisizione di un certificato di gestione dal file publishsettings]
-   [Installazione di un certificato di gestione mediante il portale di gestione di Azure][Installazione di un certificato di gestione mediante il portale di gestione di Azure]

## <span id="publishsettings"></span></a>Procedura: Creare e caricare un certificato di gestione

Per creare un certificato di gestione per Azure, è possibile usare il metodo semplificato disponibile all'indirizzo: [][1]<https://windows.azure.com/download/publishprofile.aspx></a>

In questo sito Web verrà chiesto di effettuare l'accesso usando le credenziali del portale e quindi verrà generato un certificato di gestione incluso in un pacchetto insieme all'ID sottoscrizione in un file publishsettings che verrà chiesto di scaricare.

![linuxcredentials][linuxcredentials]

Salvare questo file in un luogo sicuro poiché non potrà essere recuperato e sarà necessario generare un nuovo certificato di gestione (il numero totale di certificati che è possibile usare nel sistema è limitato; vedere la sezione appropriata di questo sito Web per verificarlo). Questo certificato può essere usato in diversi modi:

### In Visual Studio

![VSpublish][VSpublish]

### Nella riga di comando di Azure per Linux

Per importare il certificato in modo da poterlo usare è possibile eseguire il comando di importazione account Azure:

![cmdlinepublish][cmdlinepublish]

Con qualunque altro partner o software in cui sia necessario lo strumento, occorrerà estrarre il certificato di gestione dal file stesso e decodificarlo in Base 64. Alcun partner come ScaleXtreme e SUSE Studio utilizzeranno il file direttamente nella forma corrente.

Per estrarre il certificato di gestione, eseguire la procedura seguente.

Da tale file sarà necessario estrarre il contenuto con codifica in base 64 tra virgolette dopo ManagementCertificate.

    ?xml version="1.0" encoding="utf-8"?>
    <PublishData>
      <PublishProfile
        PublishMethod="AzureServiceManagementAPI"
        Url="https://management.core.windows.net/"
        ManagementCertificate="xxxxx">
        <Subscription
          Id="8a4a0a51-728e-482e-8daa-c477f03c541d"
          Name="hjereztest" />
      </PublishProfile>
    </PublishData>

È necessario copiare quanto descritto in un file (ad esempio, encodedCert.txt), quindi decodificarlo usando un decodificatore Base 64:

    base64 -d [encodedCert.txt] > [decodedCert.pfx]

In questo modo si otterrà un file con estensione pfx file che può essere convertito in altri formati mediante openssl per estrarre la chiave privata, se necessario:

    openssl pkcs12 -in [decodedCert.pfx] -out [cert.pem] -nodes

In Windows è possibile decodificare ed estrarre il file PFX usando PowerShell o un decodificatore base 64 gratuito di Windows quale [][2]<http://www.fourmilab.ch/webtools/base64/base64.zip></a> eseguendo il comando

    base64 -d key.txt ->key.pfx

#### Limite di generazione di certificati

Con questo strumento è possibile creare un massimo di 10 certificati nella piattaforma.
Dopo averle create, non esiste un modo per recuperare le chiavi generate poiché queste chiavi private non vengono salvate nel sistema.
Se si raggiunge il limite di certificati nel sistema sarà necessario contattare il supporto tramite i forum di Azure per richiedere la cancellazione dei certificati oppure usare un browser in grado di eseguire il rendering del vecchio portale Silverlight per eseguire queste operazioni.

#### Se la chiave privata è compromessa

Se la chiave privata risulta compromessa sarà necessario usare un browser che supporta Silverlight per accedere al vecchio portale ed eliminare i certificati di gestione corrispondenti archiviati oppure contattare il supporto tramite i forum.
Non è sufficiente creare nuovi certificati poiché tutti i 10 certificati sono validi e la chiave compromessa precedente potrà comunque accedere al sito Web.

## <span id="management"></span></a>Installazione di un certificato di gestione mediante il portale di gestione di Azure

Per creare un certificato di gestione è possibile procedere in diversi modi. Per altre informazioni sulla creazione di certificati, vedere [Creare e caricare un certificato di gestione per Azure][Creare e caricare un certificato di gestione per Azure]. Dopo avere creare il certificato, aggiungerlo alla propria sottoscrizione in Azure.

1.  Accedere al portale di gestione di Azure.

2.  Nel pannello di navigazione fare clic su **Settings**.

3.  In **Management Certificates** fare clic su **Upload a management certificate**.

4.  In **Upload a management certificate** individuare il file del certificato e fare clic su **OK**.

### Ottenere un'identificazione digitale del certificato e l'ID sottoscrizione

L'identificazione digitale del certificato di gestione aggiunto e l'ID sottoscrizione sono necessari per caricare il file con estensione vhd in Azure.

1.  Nel portale di gestione di fare clic su **Settings**.

2.  In **Management Certificates** fare clic sul proprio certificato, quindi registrare l'identificazione digitale dal riquadro **Properties** copiandola e incollandola in una posizione da cui possa essere recuperata in seguito.

L'ID della sottoscrizione è necessario anche per caricare il file con estensione vhd.

1.  Nel portale di gestione fare clic su **All Items**.

2.  Nel riquadro centrale, in **Subscription**, copiare la sottoscrizione e incollarla in una posizione da cui possa essere recuperata in seguito.

### Fornitura di queste informazioni agli strumenti se è stata generata una propria chiave

#### Per CSUPLOAD

1.  Aprire una finestra del prompt dei comandi di Azure SDK come amministratore.
2.  Impostare a stringa di connessione usando il comando seguente e sostituendo **Subscriptionid** e **CertThumbprint** con il valore ottenuto in precedenza:

        csupload Set-Connection "SubscriptionID=<Subscriptionid>;CertificateThumbprint=<Thumbprint>;ServiceManagementEndpoint=https://management.core.windows.net"

#### Per gli strumenti da riga di comando di Azure per Linux

È necessario codificare in base 64 il file PFX creato usando openssl con il comando seguente:

        Base64 [file] > [econded file]

In seguito sarà necessario unire l'ID sottoscrizione e il file pfx con codifica Base 64 in un unico file con la struttura seguente:

        ?xml version="1.0" encoding="utf-8"?>
        <PublishData>
          <PublishProfile
            PublishMethod="AzureServiceManagementAPI"
            Url="https://management.core.windows.net/"
            ManagementCertificate="xxxxx">
            <Subscription
              Id="8a4a0a51-728e-482e-8daa-c477f03c541d"
              Name="hjereztest" />
          </PublishProfile>
        </PublishData>
        

Dove xxxxx è il contenuto del [file codificato] che verrà usato per fornire i dettagli agli strumenti da riga di comando di Azure per Linux con i comandi:
Azure account import (File)

  []: http://msdn.microsoft.com/it-it/library/azure/gg981929.aspx
  [OpenSSL]: http://openssl.org/
  [Acquisizione di un certificato di gestione dal file publishsettings]: #createcert
  [Installazione di un certificato di gestione mediante il portale di gestione di Azure]: #management
  [1]: https://windows.azure.com/download/publishprofile.aspx
  [linuxcredentials]: ./media/virtual-machines-linux-create-management-cert/linuxcredentials.png
  [VSpublish]: ./media/virtual-machines-linux-create-management-cert/VSpublish.png
  [cmdlinepublish]: ./media/virtual-machines-linux-create-management-cert/cmdlinepublish.png

  [Creare e caricare un certificato di gestione per Azure]: http://msdn.microsoft.com/library/azure/gg551722.aspx
