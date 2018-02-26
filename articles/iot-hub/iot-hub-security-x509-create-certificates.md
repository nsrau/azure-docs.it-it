---
title: Come usare PowerShell per creare certificati X.509 | Microsoft Docs
description: Come usare PowerShell per creare certificati X.509 localmente e abilitare la sicurezza basata sullo standard X.509 nell'hub IoT di Azure in un ambiente simulato.
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2017
ms.author: dkshir
ms.openlocfilehash: b2f78e8debd367f86ee9bb06bf7de50590c61ad7
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="powershell-scripts-to-manage-ca-signed-x509-certificates"></a>Script PowerShell per la gestione di certificati X.509 firmati da CA

La sicurezza basata su certificati X.509 nell'hub IoT richiede prima di tutto una [catena di certificati X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), che include il certificato radice e i certificati intermedi fino al certificato foglia. La presente guida fornisce *procedure* e istruzioni dettagliate che usano script PowerShell di esempio basati su [OpenSSL](https://www.openssl.org/) per creare e firmare certificati X.509. È consigliabile usare questa guida solo come riferimento, dal momento che molte delle procedure descritte vengono in realtà eseguite durante il processo di produzione. È possibile usare questi certificati per simulare la sicurezza nell'hub IoT di Azure mediante l'*autenticazione dei certificati X.509*. I passaggi descritti in questa guida consentono di creare certificati in locale nel computer Windows in uso. 

## <a name="prerequisites"></a>prerequisiti
In questa esercitazione si presuppone l'acquisto di file binari OpenSSL. È possibile
    - scaricare il codice sorgente OpenSSL e generare i file binari nel computer in uso oppure 
    - scaricare e installare qualsiasi [file binario OpenSSL di terze parti](https://wiki.openssl.org/index.php/Binaries), ad esempio da [questo progetto in SourceForge](https://sourceforge.net/projects/openssl/).

<a id="createcerts"></a>

## <a name="create-x509-certificates"></a>Creare certificati X.509
Nella procedura seguente viene illustrato un esempio di come creare i certificati radice X.509 in locale. 

1. Aprire una finestra di PowerShell come *amministratore*. 
2. Passare alla directory di lavoro. Eseguire lo script seguente per impostare le variabili globali. 
    ```PowerShell
    $openSSLBinSource = "<full_path_to_the_binaries>\OpenSSL\bin"
    $errorActionPreference    = "stop"

    # Note that these values are for test purpose only
    $_rootCertSubject         = "CN=Azure IoT Root CA"
    $_intermediateCertSubject = "CN=Azure IoT Intermediate {0} CA"
    $_privateKeyPassword      = "123"

    $rootCACerFileName          = "./RootCA.cer"
    $rootCAPemFileName          = "./RootCA.pem"
    $intermediate1CAPemFileName = "./Intermediate1.pem"
    $intermediate2CAPemFileName = "./Intermediate2.pem"
    $intermediate3CAPemFileName = "./Intermediate3.pem"

    $openSSLBinDir              = Join-Path $ENV:TEMP "openssl-bin"

    # Whether to use ECC or RSA.
    $useEcc                     = $true
    ```
3. Eseguire lo script seguente per copiare i file binari OpenSSL nella directory di lavoro e impostare le variabili di ambiente:

    ```PowerShell
    function Initialize-CAOpenSSL()
    {
        Write-Host ("Beginning copy of openssl binaries to {0} (and setting up env variables...)" -f $openSSLBinDir)
        if (-not (Test-Path $openSSLBinDir))
        {
            mkdir $openSSLBinDir | Out-Null
        }

        robocopy $openSSLBinSource $openSSLBinDir * /s 
        robocopy $openSSLBinSource . * /s 

        Write-Host "Setting up PATH and other environment variables."
        $ENV:PATH += "; $openSSLBinDir"
        $ENV:OPENSSL_CONF = Join-Path $openSSLBinDir "openssl.cnf"

        Write-Host "Success"
    }
    Initialize-CAOpenSSL
    ```
4. Eseguire quindi lo script seguente per verificare se un certificato con oggetto *Nome oggetto* è già installato e se OpenSSL è configurato correttamente nel computer in uso:
    ```PowerShell
    function Get-CACertBySubjectName([string]$subjectName)
    {
        $certificates = gci -Recurse Cert:\LocalMachine\ |? { $_.gettype().name -eq "X509Certificate2" }
        $cert = $certificates |? { $_.subject -eq $subjectName -and $_.PSParentPath -eq "Microsoft.PowerShell.Security\Certificate::LocalMachine\My" }
        if ($NULL -eq $cert)
        {
            throw ("Unable to find certificate with subjectName {0}" -f $subjectName)
        }
    
        write $cert
    }
    function Test-CAPrerequisites()
    {
        $certInstalled = $null
        try
        {
            $certInstalled = Get-CACertBySubjectName $_rootCertSubject
        }
        catch {}

        if ($NULL -ne $certInstalled)
        {
            throw ("Certificate {0} already installed.  Cleanup CA certs 1st" -f $_rootCertSubject)
        }

        if ($NULL -eq $ENV:OPENSSL_CONF)
        {
            throw ("OpenSSL not configured on this system.  Run 'Initialize-CAOpenSSL' (even if you've already done so) to set everything up.")
        }
        Write-Host "Success"
    }
    Test-CAPrerequisites
    ```
    Se la configurazione è corretta, verrà visualizzato il messaggio "Success". 

<a id="createcertchain"></a>

## <a name="create-x509-certificate-chain"></a>Creare una catena di certificati X.509
Creare una catena di certificati con un certificato della CA radice, ad esempio "CN=Azure IoT Root CA" in questo esempio, eseguendo lo script PowerShell seguente. Questo script aggiorna anche l'archivio certificati del sistema operativo Windows e crea i file di certificato nella directory di lavoro. 
    1. Lo script seguente crea una funzione di PowerShell che consente di creare un certificato autofirmato con oggetto *Nome oggetto* e un'autorità di firma specificati. 
    ```PowerShell
    function New-CASelfsignedCertificate([string]$subjectName, [object]$signingCert, [bool]$isASigner=$true)
    {
        # Build up argument list
        $selfSignedArgs =@{"-DnsName"=$subjectName; 
                           "-CertStoreLocation"="cert:\LocalMachine\My";
                           "-NotAfter"=(get-date).AddDays(30); 
                          }

        if ($isASigner -eq $true)
        {
            $selfSignedArgs += @{"-KeyUsage"="CertSign"; }
            $selfSignedArgs += @{"-TextExtension"= @(("2.5.29.19={text}ca=TRUE&pathlength=12")); }
        }
        else
        {
            $selfSignedArgs += @{"-TextExtension"= @("2.5.29.37={text}1.3.6.1.5.5.7.3.2,1.3.6.1.5.5.7.3.1", "2.5.29.19={text}ca=FALSE&pathlength=0")  }
        }

        if ($signingCert -ne $null)
        {
            $selfSignedArgs += @{"-Signer"=$signingCert }
        }

        if ($useEcc -eq $true)
        {
            $selfSignedArgs += @{"-KeyAlgorithm"="ECDSA_nistP256";
                             "-CurveExport"="CurveName" }
        }

        # Now use splatting to process this
        Write-Host ("Generating certificate {0} which is for prototyping, NOT PRODUCTION.  It will expire in 30 days." -f $subjectName)
        write (New-SelfSignedCertificate @selfSignedArgs)
    }
    ``` 
    2. La funzione di PowerShell seguente crea certificati X.509 intermedi usando la funzione precedente e i file binari OpenSSL. 
    ```PowerShell
    function New-CAIntermediateCert([string]$subjectName, [Microsoft.CertificateServices.Commands.Certificate]$signingCert, [string]$pemFileName)
    {
        $certFileName = ($subjectName + ".cer")
        $newCert = New-CASelfsignedCertificate $subjectName $signingCert
        Export-Certificate -Cert $newCert -FilePath $certFileName -Type CERT | Out-Null
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\CA" -FilePath $certFileName | Out-Null

        # Store public PEM for later chaining
        openssl x509 -inform deer -in $certFileName -out $pemFileName

        del $certFileName
   
        write $newCert
    }  
    ```
    3. La funzione di PowerShell seguente crea la catena di certificati X.509. Per altre informazioni, vedere [Catene di certificati](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification).
    ```PowerShell
    function New-CACertChain()
    {
        Write-Host "Beginning to install certificate chain to your LocalMachine\My store"
        $rootCACert =  New-CASelfsignedCertificate $_rootCertSubject $null
    
        Export-Certificate -Cert $rootCACert -FilePath $rootCACerFileName  -Type CERT
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\Root" -FilePath $rootCACerFileName

        openssl x509 -inform der -in $rootCACerFileName -out $rootCAPemFileName

        $intermediateCert1 = New-CAIntermediateCert ($_intermediateCertSubject -f "1") $rootCACert $intermediate1CAPemFileName
        $intermediateCert2 = New-CAIntermediateCert ($_intermediateCertSubject -f "2") $intermediateCert1 $intermediate2CAPemFileName
        $intermediateCert3 = New-CAIntermediateCert ($_intermediateCertSubject -f "3") $intermediateCert2 $intermediate3CAPemFileName
        Write-Host "Success"
    }    
    ```
    Questo script crea un file denominato *RootCA.cer* nella directory di lavoro. 
    4. Usare infine le funzioni di PowerShell precedentemente descritte per creare la catena di certificati X.509 eseguendo il comando `New-CACertChain` nella finestra di PowerShell. 


<a id="signverificationcode"></a>

## <a name="proof-of-possession-of-your-x509-ca-certificate"></a>Verifica del possesso del certificato della CA X.509

Questo script esegue il flusso di *verifica del possesso*  per il certificato X.509 in uso. 

Nella finestra di PowerShell disponibile sul desktop eseguire il codice seguente:
   
   ```PowerShell
   function New-CAVerificationCert([string]$requestedSubjectName)
   {
       $cnRequestedSubjectName = ("CN={0}" -f $requestedSubjectName)
       $verifyRequestedFileName = ".\verifyCert4.cer"
       $rootCACert = Get-CACertBySubjectName $_rootCertSubject
       Write-Host "Using Signing Cert:::" 
       Write-Host $rootCACert
   
       $verifyCert = New-CASelfsignedCertificate $cnRequestedSubjectName $rootCACert $false

       Export-Certificate -cert $verifyCert -filePath $verifyRequestedFileName -Type Cert
       if (-not (Test-Path $verifyRequestedFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $verifyRequestedFileName)
       }
   
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnRequestedSubjectName, (Join-Path (get-location).path $verifyRequestedFileName)) 
   }
   New-CAVerificationCert "<your verification code>"
   ```

Questo codice crea un certificato con il nome oggetto specificato, firmato dalla CA, in un file denominato *VerifyCert4.cer* nella directory di lavoro. Questo file di certificato consente di convalidare nell'hub IoT che l'utente dispone dell'autorizzazione di firma, vale a dire della chiave privata, di questa CA.


<a id="createx509device"></a>

## <a name="create-leaf-x509-certificate-for-your-device"></a>Creare il certificato X.509 foglia per il dispositivo

In questa sezione viene illustrato come è possibile usare uno script PowerShell per creare un certificato foglia per il dispositivo e la catena di certificati corrispondente. 

Nella finestra di PowerShell disponibile nel computer locale eseguire lo script seguente per creare un certificato X.509 firmato da CA per il dispositivo in uso:

   ```PowerShell
   function New-CADevice([string]$deviceName, [string]$signingCertSubject=$_rootCertSubject)
   {
       $cnNewDeviceSubjectName = ("CN={0}" -f $deviceName)
       $newDevicePfxFileName = ("./{0}.pfx" -f $deviceName)
       $newDevicePemAllFileName      = ("./{0}-all.pem" -f $deviceName)
       $newDevicePemPrivateFileName  = ("./{0}-private.pem" -f $deviceName)
       $newDevicePemPublicFileName   = ("./{0}-public.pem" -f $deviceName)
   
       $signingCert = Get-CACertBySubjectName $signingCertSubject ## "CN=Azure IoT CA Intermediate 1 CA"

       $newDeviceCertPfx = New-CASelfSignedCertificate $cnNewDeviceSubjectName $signingCert $false
   
       $certSecureStringPwd = ConvertTo-SecureString -String $_privateKeyPassword -Force -AsPlainText

       # Export the PFX of the cert we've just created.  The PFX is a format that contains both public and private keys.
       Export-PFXCertificate -cert $newDeviceCertPfx -filePath $newDevicePfxFileName -password $certSecureStringPwd
       if (-not (Test-Path $newDevicePfxFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $newDevicePfxFileName)
       }

       # Begin the massaging.  First, turn the PFX into a PEM file which contains public key, private key, and other attributes.
       Write-Host ("When prompted for password by openssl, enter the password as {0}" -f $_privateKeyPassword)
       openssl pkcs12 -in $newDevicePfxFileName -out $newDevicePemAllFileName -nodes

       # Convert the PEM to get formats we can process
       if ($useEcc -eq $true)
       {
           openssl ec -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       else
       {
           openssl rsa -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       openssl x509 -in $newDevicePemAllFileName -out $newDevicePemPublicFileName
 
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnNewDeviceSubjectName, (Join-Path (get-location).path $newDevicePemPublicFileName)) 
   }
   ```

Eseguire quindi `New-CADevice "<yourTestDevice>"` nella finestra di PowerShell specificando il nome descrittivo usato per creare il dispositivo. Quando viene richiesta la password per la chiave privata della CA, immettere "123". Verrà creato un file con estensione _<yourTestDevice>pfx_ nella directory di lavoro.

## <a name="clean-up-certificates"></a>Pulire i certificati

Nella barra di avvio o in **Impostazioni** cercare e selezionare **Gestisci i certificati computer**. Rimuovere eventuali certificati rilasciati da **Azure IoT CA TestOnly***. Questi certificati devono essere presenti nelle tre posizioni seguenti: 

* Certificati - Computer locale > Personale > Certificati
* Certificati - Computer locale > Autorità di certificazione radice attendibili > Certificati
* Certificati - Computer locale > Autorità di certificazione intermedia > Certificati

   ![Rimuovere i certificati Azure IoT CA TestOnly](./media/iot-hub-security-x509-create-certificates/cleanup.png)